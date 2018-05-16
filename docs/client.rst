.. _micro_rtps_client_label:

Micro RTPS Client
=================

In *Micro RTPS* a *Client* can communicate with DDS Network as any other DDS actor could do.
*Clients* can publish and subscribe to data Topics in the DDS Global Data Space.
*Micro RTPS* provides you with a C API to create *Micro RTPS Clients*.
All functions needed to set up the *Client* can be found into ``xrce_client.h`` header.
This is the only header that you must to include.

Within your *Micro RTPS Client* you can choose between several transport layers.
Communication with the Agent is done through the transport you choose.
The API functions to select transport follows the next syntax, where X correspond to the transport layer protocol.

.. code-block:: c

    bool new_X_session(Session* session, SessionId id, ClientKey key,
                       const uint8_t* const agent_ip, uint16_t agent_port,
                       OnTopic on_topic_callback, void* on_topic_args);
    //Currently, only 'UDP' is entirelly supported.

This function initialize a Session object that you should keep during a communication session with a *Micro RTPS Agent*.

To log in with the *Agent* you must call:

.. code-block:: c

    bool init_session_sync(Session* session);

If you do not need that communication you can close and free it using:

.. code-block:: c

    bool close_session_sync(Session* session); //to destroy the client instance in the agent. Equivalent to logging out.
    void free_session(Session* session); //to remove the local data used in this session.
    //Currently, only 'udp' is entirelly supported.

Your *Micro RTPS Client* can send request Operations to the *Micro RTPS Agent* using the API provided.
All operations except to send and to receive data are synchronous:

.. code-block:: c

    bool create_participant_sync_by_ref(Session* session, const ObjectId object_id, const char* ref,
                                        bool reuse, bool replace);

    bool create_topic_sync_by_xml(Session* session, const ObjectId object_id, const char* xml, const ObjectId participant_id,
                                  bool reuse, bool replace);

    bool create_publisher_sync_by_xml(Session* session, const ObjectId object_id, const char* xml, const ObjectId participant_id,
                                      bool reuse, bool replace);

    bool create_subscriber_sync_by_xml(Session* session, const ObjectId object_id, const char* xml, const ObjectId participant_id,
                                       bool reuse, bool replace);

    bool create_datawriter_sync_by_xml(Session* session, const ObjectId object_id, const char* xml, const ObjectId publisher_id,
                                       bool reuse, bool replace);

    bool create_datareader_sync_by_xml(Session* session, const ObjectId object_id, const char* xml, const ObjectId subscriber_id,
                                       bool reuse, bool replace);

    bool delete_object_sync(Session* session, ObjectId object_id);

    bool read_data_sync(Session* session, ObjectId object_id, StreamId id);

This API functions prepare requests for the *Micro RTPS Agent*. The *Client* waits the status about of the state of these XRCE entities in the *Agent* side.

Write and read Topics are asynchronous messages.
For writing data, *Micro RTPS Gent* creates a function for writing specific Topics easily with the next form:

.. code-block:: c

    bool write_TopicName(Session* session, ObjectId datawriter_id, StreamId stream_id, TopicName* topic);

This functions only serialize the ``TopicName`` Topic into the ``stream_id`` selected, it do not send the Topic to the *Agent*.

To send the serialized data to the *Agent* you must call to the main library function:

.. code-block:: c

    void run_communication(Session* session);

This function performs all the activities required related to the session.
This activities include:

- Send written Topics to the *Agent*.
- Receive Topics from the *Agent*.
- In reliable connection, send and received heartbeats and acknacks.
- Resend lost messages.

All data from the DDS Global Data Space that the *Client* has been subscribed, will call the callback setted at the Session creation.
This callback must have the next form:

.. code-block:: c

    void on_topic(ObjectId id, MicroBuffer *message, void* args);

The id correspond to the Subscriber id, for distinguishing the Topic received.
The Topic is serialized into message.
For deserializen the Topic, *Micro RTPS Gen* generates a deserialize function of your Topic.

.. code-block:: c

    bool deserialize_TopicName_topic(MicroBuffer* reader, TopicName* topic);

Build configuration
-------------------
There are several cmake definitions for configuring the build of the client library at compile time.
These allow you to create a version of the library according to your requirements.

:``_MICRORTPS_MTU_SIZE_=<number>``:
    This value set the size of the internal buffers.
    Both, input and output streams, will use this value for creating their buffers.
    Knowing that any message will overcome a certain size, you can reduce this value for save considerably the memory that the library will use.
    By default, this value is 512 (bytes)

:``_MICRORTPS_RELIABLE_HISTORY_=<number>``:
    This value set the number of buffers reserved for a reliable stream.
    By default is 16.
    Each buffer will hold a `_MICRORTPS_MTU_SIZE_` bytes.

:``_MICRORTPS_TIMEOUT_MS_=<number>``:
    This value is the max milliseconds that the client will wait for receiving a message.
    By default is 1 ms.

:``_MICRORTPS_MAX_ATTEMPTS_=<number>``:
    This value indicates the number of reading tries that an sync petition (as create or delete operations) will perform until receiving an status message.
    It is only used in reliable streams. By default is attempts

:``_MICRORTPS_CONNECTION_MAX_ATTEMPTS_=<number>``:
    Similar to `_MICRORTPS_MAX_ATTEMPTS_` but used when there is not a reliable stream and a status message is required, as in *Create Session* operation.
    By default is 20 attemps.

:``_MICRORTPS_HEARTBEAT_MIN_PERIOD_MS_=<number>``:
    This time indicates the period of the hearbeat.
    A large period will saturate less the network, saving the client bandwidth, but if a message is lost, their recovery will take more time.

:``-D_CONFIG_MAX_TRANSMISSIONS_UNIT_SIZE_=<number>``:
    This value is used to create an internal buffer. By default is 512 (bytes).
    If a message is higher than this value, the message will be sengmented at transport level.

:``-D_CONFIG_MAX_NUM_LOCATORS_=<number>``:
    This value refers to an array of locators managed internally.
    The user must be defined as maximun this number of locators.
    By default is 16.

:``-D_CONFIG_MAX_STRING_SIZE_=<number>``:
    Internally, it is used to save the name of the device in serial communication.
    By default is 255.

:``-D__BIG_ENDIAN__=<number>``:
    This value must be correspond to the memory endianness of the device in which the client is running.
    `0` implies that the machine is little endian and `1` big endian.
    By default, when the client is compiled, the build system will get this value from the machine that is compiling the library.
    For cross compiling, you must set this value manually with the endianness of the device that run the client.

