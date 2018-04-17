.. _micro_rtps_client_label:

Micro RTPS Client
=================

In *Micro RTPS* a *Client* can communicate with DDS Network as any other DDS actor could do.
Clients can publish and subscribe to data topics in DDS Global Data Space.
*Micro RTPS* provides you with a C API to create *Micro RTPS Clients*.
All functions needed to set up the client can be found into ``xrce_client.h`` header.
This is the only header that you must to include.

Within your *Micro RTPS Client* you can choose between several transport layers.
Communication with the Agent is done through the transport you choose.
The API functions to select transport follows the next syntax, where X correspond to the transport layer protocol.

.. code-block:: c

    bool new_X_session(Session* session, SessionId id, ClientKey key,
                       const uint8_t* const agent_ip, uint16_t agent_port,
                       OnTopic on_topic_callback, void* on_topic_args);
    //Currently, only 'udp' is entirelly supported.

This function initialize a Session object that you should keep during a communication session with a *Micro RTPS Agent*.

To log in with the agent you must call:

.. code-block:: c

    bool init_session_sync(Session* session);

If you do not need that communication you can close and free it using:

.. code-block:: c

    bool close_session_sync(Session* session); //to destroy the client instance in the agent. Equivalent to logging out.
    void free_X_session(Session* session); //to remove the local data used in this session.
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

This API functions prepare requests for the *Micro RTPS Agent*. The client wait the status about of the state of these entities in the agent side.

Write and read topics are asynchronous messages.
For writing data, *micrortpsgen* creates a function for writing specific topics easily with the next form:

.. code-block:: c

    bool write_TopicName(Session* session, ObjectId datawriter_id, StreamId stream_id, TopicName* topic);

This functions only serialize the ``TopicName`` topic into the ``stream_id`` selected, do not send it to the agent.

To send the serialized data to the agent you must call to the main library function:

.. code-block:: c

    void run_communication(Session* session);

This function performs all the activities required related to the session.
This activities include:
- Send written topics to the agent.
- Receive topics from the agent.
- In reliable connection, send and received heartbeats and acknacks.
- Resend lost messages.

All data from DDS Global Data Space that the client has been subscribed, will call the callback setted at the Session creation.
This callback must have the next form:

.. code-block:: c

    void on_topic(ObjectId id, MicroBuffer *message, void* args);

The id correspond to the subscriber id, for distinguishing the topic received.
The topic is serialized into message.
For deserializen the topic, *micrortpsgen* generates a deserialize function of your topic.

.. code-block:: c

    bool deserialize_TopicName_topic(MicroBuffer* reader, TopicName* topic);
