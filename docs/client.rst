.. _micro_rtps_client_label:

Micro RTPS Client
=================
In *Micro RTPS*, a *Client* can communicate with DDS Network as any other DDS actor could do.
*Clients* can publish and subscribe to data Topics in the DDS Global Data Space.

*Micro RTPS* provides you with a C API to create *Micro RTPS Clients*.
All functions needed to set up the *Client* can be found into ``client.h`` header.
This is the only header you need to include.

Profiles
--------

The client library follows a profile concept that enables to choose add or remove some features in configuration time.
This allows to reduce the client library size, if there are features that are not used.
The profiles can be choosen in ``client.config`` and start with the prefix ``PROFILE``.
As part of these profiles, you can choose between several transport layers.
Communication with the agent is done through the transport you choose.

The implementation of the transport depends of the platform.
The next tables show the current implementation.

============ ========== ========= =========
Transport     Linux      Windows   NuttX
============ ========== ========= =========
UDP           X           X        X
TCP           X           X        X
Serial        X                    X
============ ========== ========= =========

The addition of a new transport or an existant transport for a new platform can be easily implemented setting the callbacks of a ``mrCommunication`` structure.
See the current transport implementations as an example for a new custom transport.

Configuration
-------------
There are several definitions for configuring and building of the client library at **compile time**.
These allow you to create a version of the library according to your requirements.
These definitions can be modified at ``client.config`` file.
For incorporating the changes to your project, is necessary to run the ``cmake`` command every time the definitions change.

``PROFILE_CREATE_ENTITIES_REF=<bool>``
    Enables or disables the functions related to create entities by reference.

``PROFILE_CREATE_ENTITIES_XML=<bool>``
    Enables or disables the functions related to create entities by xml.

``PROFILE_READ_ACCESS=<bool>``
    Enables or disables the functions related to read topics.

``PROFILE_UDP_TRANSPORT=<bool>``
    Enables or disables the posibility to connect with the agent by UDP.

``PROFILE_TCP_TRANSPORT=<bool>``
    Enables or disables the posibility to connect with the agent by TCP.

``PROFILE_SERIAL_TRANSPORT=<bool>``
    Enables or disables the posibility to connect with the agent by Serial.

``CONFIG_MAX_OUTPUT_BEST_EFFORT_STREAMS=<number>``
    Configures the maximun output best effort streams that a session could have.
    The calls to ``mr_create_output_best_effort_stream`` function for a session must be less or equal that this value.

``CONFIG_MAX_OUTPUT_RELIABLE_STREAMS=<number>``
    Configures the maximun output reliable streams that a session could have.
    The calls to ``mr_create_output_reliable_stream`` function for a session must be less or equal that this value.

``CONFIG_MAX_INPUT_BEST_EFFORT_STREAMS=<number>``
    Configure the maximun input best effort streams that a session could have.
    The calls to ``mr_create_input_best_effort_stream`` function for a session must be less or equal that this value.

``CONFIG_MAX_INPUT_RELIABLE_STREAMS=<number>``
    Configures the maximun input reliable streams that a session could have.
    The calls to ``mr_create_input_reliable_stream`` function for a session must be less or equal that this value.

``CONFIG_MAX_SESSION_CONNECTION_ATTEMPTS=<number>``
    This value indicates the number of attempts that ``create_session`` and ``delete_session`` will perform until receiving an status message.
    After a failed attempt, the wait time for the next attempt will be double.

``CONFIG_MIN_SESSION_CONNECTION_INTERVAL=<number>``
    This value represents how long it will take to send a new ``create_session`` or ``delete_session`` if the first sent has not answer.
    The wait time for the each attempt will be double until reach ``CONFIG_MAX_SESSION_CONNECTION_ATTEMPTS``.
    It is measured in milliseconds.

``CONFIG_MIN_HEARTBEAT_TIME_INTERVAL=<number>``
    Into a reliable communication, this value represents how long it will take the first heartbeat to be sent.
    The wait time for the next heartbeat will be double.
    It is measured in milliseconds.

``CONFIG_MACHINE_ENDIANNESS=<number>``
    This value must be correspond to the memory endianness of the device in which the client is running.
    `0` implies that the machine is little endian and `1` implies big endian.
    It this entry is not in the ``client.config`` the build system will get this value from the machine that is compiling the library.
    For cross compiling, you must set this value manually with the endianness of the device that run the client.

``CONFIG_UDP_TRANSPORT_MTU=<number>``
    This value corresponds to the `Maximun Transmission Unit` able to send and receive by UDP.
    Internally a buffer is created with this size.

``CONFIG_TCP_TRANSPORT_MTU=<number>``
    This value corresponds to the `Maximun Transmission Unit` able to send and receive by TCP.
    Internally a buffer is created with this size.

``CONFIG_SERIAL_TRANSPORT_MTU=<number>``
    This value corresponds to the `Maximun Transmission Unit` able to send and receive by Serial.
    Internally a buffer is created proportional to this size.

API
---
As a nomenclature, `Micro RTPS Client` API uses a ``mr_`` prefix in all of their public API functions and ``mr`` prefix in the types.
In constants values an ``MR_`` prefix is used.
Functions without these rules `should not` be used.
They are only for internal use.

Session
```````
These functions are available even if no profile has been enabled in ``client.config`` file.
The declaration of these function can be found in ``micrortps/client/core/session/session.h``.

------

.. code-block:: c

    void mr_init_session(mrSession* session, mrCommunication* comm, uint32_t key);

Initializes a session structure.
Once this function is called, a ``create_session`` call can be performed.

:session: Session structure where manage the session data.
:key: The identifying key of the client.
      All clients connected to an agent must have different key.
:comm: Communication used for connecting to the agent.
       All different transports have a common attribute mrCommunication.
       This parameter can not be shared between active sessions.

------

.. code-block:: c

    void mr_set_status_callback(mrSession* session, mrOnStatusFunc on_status_func, void* args);

Assigns the callback for the agent status messages.

:session: Session structure previously initialized.
:on_status_func: Function callback that will be called when a valid status message comes from the agent.
:args: User pointer data.
       The args will be provided to ``on_status_func`` function.

------

.. code-block:: c

    void mr_set_topic_callback(mrSession* session, mrOnTopicFunc on_topic_func, void* args);

Assigns the callback for topics.
The topics will be received only if a ``request_data`` function has been called.

:session: Session structure previously initialized.
:on_status_func: Function callback that will be called when a valid data message comes from the agent.
:args: User pointer data.
       The args will be provided to ``on_topic_func`` function.

------

.. code-block:: c

    bool mr_create_session(mrSession* session);

Creates a new session with the agent.
This function logs in a session, enabling any other XRCE communication with the agent.

:session: Session structure previously initialized.

------

.. code-block:: c

    bool mr_delete_session(mrSession* session);

Deletes session previously created.
All `XRCE` entities created with the session will be removed.
This function logs out a session, disabling any other `XRCE` communication with the agent.

:session: Session structure previously initialized.

------

.. code-block:: c

    mrStreamId mr_create_output_best_effort_stream(mrSession* session, uint8_t* buffer, size_t size);

Creates and initializes an output best effort stream for writing.
The ``mrStreamId`` returned represents the new stream and can be used to manage it.
The number of available calls to this function must be less or equal than ``CONFIG_MAX_OUTPUT_BEST_EFFORT_STREAMS`` value of the ``client.config`` file.

:session: Session structure previously initialized.
:buffer: Memory block where the messages will be written.
:size: Buffer size.

------

.. code-block:: c

    mrStreamId mr_create_output_reliable_stream(mrSession* session, uint8_t* buffer, size_t size, size_t history);

Creates and initializes an output reliable stream for writing.
The ``mrStreamId`` returned represents the new stream and can be used to manage it.
The number of available calls to this function must be less or equal than ``CONFIG_MAX_OUTPUT_RELIABLE_STREAMS`` value of the ``client.config`` file.

:session: Session structure previously initialized.
:buffer: Memory block where the messages will be written.
:size: Buffer size.
:history: History used for the reliable connection.
          The buffer size will be splited into smaller buffers using this value.
          The history must be a power of two.

------

.. code-block:: c

    mrStreamId mr_create_input_best_effort_stream(mrSession* session);

Creates and initializes an input best effort stream for receiving messages.
The ``mrStreamId`` returned represents the new stream and can be used to manage it.
The number of available calls to this function must be less or equal than ``CONFIG_MAX_INPUT_BEST_EFFORT_STREAMS`` value of the ``client.config`` file.

:session: Session structure previously initialized.

------

.. code-block:: c

    mrStreamId mr_create_input_reliable_stream(mrSession* session, uint8_t* buffer, size_t size, size_t history);

Creates and initializes an input reliable stream for receiving messages.
The returned ``mrStreamId`` represents the new stream and can be used to manage it.
The number of available calls to this function must be less or equal than ``CONFIG_MAX_INPUT_RELIABLE_STREAMS`` value of the ``client.config`` file.

:session: Session structure previously initialized.
:buffer: Memory block where the messages will be storaged.
:size: Buffer size.
:history: History used for the reliable connection.
          The buffer will be splited into smaller buffers using this value.
          The history must be a power of two.

------

.. code-block:: c

    void mr_flash_output_streams(mrSession* session);

Flashes all output streams sending the data through the transport.

:session: Session structure previously initialized.

------

.. code-block:: c

    void mr_run_session_time(mrSession* session, int time);

The main library function.
This function processes the internal functionality of a session.
This implies:

1. Flashes all output streams sending the data through the transport.
2. If there is any reliable stream, it will perform the asociated reliable behaviour to ensure the communication.
3. Listens messages from the agent and call the associated callback if exists (a topic callback or a status callback).

The ``time`` suffix function version will perform these actions and will listen messages for a ``time`` duration.
Only when the time waiting for a message overcome the ``time`` duration, the function finishes.
The function will return ``true`` if the sent data have been confirmed, ``false`` otherwise.

:session: Session structure previously initialized.
:time: Time for waiting, in milliseconds.
          For waiting without timeout, set the value to ``MR_TIMEOUT_INF``

------

.. code-block:: c

    void mr_run_session_until_timeout(mrSession* session, int timeout);

The main library function.
This function processes the internal functionality of a session.
This implies:

1. Flashes all output streams sending the data through the transport.
2. If there is any reliable stream, it will perform the asociated reliable behaviour to ensure the communication.
3. Listens messages from the agent and call the associated callback if exists (a topic callback or a status callback).

The ``_until_timeout`` suffix function version will perform these actions until receiving one message.
Once the message has been received or the timeout has been reached, the function finishes.
Only when the time waiting for a message overcome the ``timeout`` duration, the function finishes.
The function will return ``true`` if has received a message, ``false`` if the timeout has been reached.

:session: Session structure previously initialized.
:timeout: Time for waiting a new message, in milliseconds.
          For waiting without timeout, set the value to ``MR_TIMEOUT_INF``

------

.. code-block:: c

    bool mr_run_session_until_confirm_delivery(mrSession* session, int timeout);

The main library function.
This function processes the internal functionality of a session.
This implies:

1. Flashes all output streams sending the data through the transport.
2. If there is any reliable stream, it will perform the asociated reliable behaviour to ensure the communication.
3. Listenes messages from the agent and call the associated callback if exists (a topic callback or a status callback).

The ``_until_confirm_delivery`` suffix function version will perform these actions during ``timeout`` duration
or until the output reliable streams confirm that the sent messages have been received by the agent.
The function will return ``true`` if the sent data have been confirmed, ``false`` otherwise.

:session: Session structure previously initialized.
:timeout: Maximun time for waiting to a new message, in milliseconds.
          For waiting without timeout, set the value to ``MR_TIMEOUT_INF``

------

.. code-block:: c

    bool mr_run_session_until_status(mrSession* session, int timeout, const uint16_t* request_list, uint8_t* status_list, size_t list_size);

The main library function.
This function processes the internal functionality of a session.
This implies:

1. Flashes all output streams sending the data through the transport.
2. If there is any reliable stream, it will perform the asociated reliable behaviour to ensure the communication.
3. Listenes messages from the agent and call the associated callback if exists (a topic callback or a status callback).

The ``_until_status`` suffix function version will perform these actions during ``timeout`` duration
or until the requested status had been received.
The function will return ``true`` if all status have been received and all of them have the value ``MR_STATUS_OK`` or ``MR_STATUS_OK_MATCHED``, ``false`` otherwise.

:session: Session structure previously initialized.
:timeout: Maximun time for waiting to a new message, in milliseconds.
          For waiting without timeout, set the value to ``MR_TIMEOUT_INF``
:request_list: An array of request to confirm with a status.
:status_list: An uninitialized array with the same size as ``request_list`` where the status values will be written.
              The position of a status in the list corresponds to the request at the same position in ``request_list``.
:list_size: The size of ``request_list`` and ``status_list`` arrays.

------

Create entities by XML profile
``````````````````````````````
These functions are enabled when ``PROFILE_CREATE_ENTITIES_XML`` is enabled into ``client.config`` file.
The declaration of these function can be found in ``micrortps/client/profile/session/create_entities_xml.h``.

------

.. code-block:: c

    uint16_t mr_write_configure_participant_xml(mrSession* session, mrStreamId stream_id, mrObjectId object_id, uint16_t domain, const char* xml, uint8_t mode);

Create a `participant` entity in the agent.

:session: Session structure previously initialized.
:stream_id: The output stream ID where the message will be written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``MR_PARTICIPANT_ID``
:xml: A xml representation of the new entity.
:mode: Determines the creation entity mode.
        Currently, only soported ``MR_REPLACE``.
        It will delete the entity previously in the agent if exists.
        A ``0`` value, implies that only creates the entity if it does not exists.

------

.. code-block:: c

    uint16_t mr_write_configure_topic_xml(mrSession* session, mrStreamId stream_id, mrObjectId object_id, mrObjectId participant_id, const char* xml, uint8_t mode);

Create a `topic` entity in the agent.

:session: Session structure previously initialized.
:stream_id: The output stream ID where the message will be written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``MR_TOPIC_ID``
:xml: A xml representation of the new entity.
:mode: Determines the creation entity mode.
        Currently, only soported ``MR_REPLACE``.
        It will delete the entity previously in the agent if exists.
        A ``0`` value, implies that only creates the entity if it does not exists.

------

.. code-block:: c

    uint16_t mr_write_configure_publisher_xml(mrSession* session, mrStreamId stream_id, mrObjectId object_id, mrObjectId participant_id, const char* xml, uint8_t mode);

Create a `publisher` entity in the agent.

:session: Session structure previously initialized.
:stream_id: The output stream ID where the message will be written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``MR_PUBLISHER_ID``
:xml: A xml representation of the new entity.
:mode: Determines the creation entity mode.
        Currently, only soported ``MR_REPLACE``.
        It will delete the entity previously in the agent if exists.
        A ``0`` value, implies that only creates the entity if it does not exists.

------

.. code-block:: c

    uint16_t mr_write_configure_subscriber_xml(mrSession* session, mrStreamId stream_id, mrObjectId object_id, mrObjectId participant_id, const char* xml, uint8_t mode);

Create a `publisher` entity in the agent.

:session: Session structure previously initialized.
:stream_id: The output stream ID where the message will be written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``MR_SUBSCRIBER_ID``
:xml: A xml representation of the new entity.
:mode: Determines the creation entity mode.
        Currently, only soported ``MR_REPLACE``.
        It will delete the entity previously in the agent if exists.
        A ``0`` value, implies that only creates the entity if it does not exists.

------

.. code-block:: c

    uint16_t mr_write_configure_datawriter_xml(mrSession* session, mrStreamId stream_id, mrObjectId object_id, mrObjectId publisher_id, const char* xml, uint8_t mode);

Create a `datawriter_id` entity in the agent.

:session: Session structure previously initialized.
:stream_id: The output stream ID where the message will be written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``MR_DATAWRITER_ID``
:xml: A xml representation of the new entity.
:mode: Determines the creation entity mode.
        Currently, only soported ``MR_REPLACE``.
        It will delete the entity previously in the agent if exists.
        A ``0`` value, implies that only creates the entity if it does not exists.

------

.. code-block:: c

    uint16_t mr_write_configure_datareader_xml(mrSession* session, mrStreamId stream_id, mrObjectId object_id, mrObjectId subscriber_id, const char* xml, uint8_t mode);

Create a `datareader` entity in the agent.

:session: Session structure previously initialized.
:stream_id: The output stream ID where the message will be written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``MR_DATAREADER_ID``
:xml: A xml representation of the new entity.
:mode: Determines the creation entity mode.
        Currently, only soported ``MR_REPLACE``.
        It will delete the entity previously in the agent if exists.
        A ``0`` value, implies that only creates the entity if it does not exists.

------

Create entities by reference profile
````````````````````````````````````
These functions are enabled when ``PROFILE_CREATE_ENTITIES_REF`` is enabled into ``client.config`` file.
The declaration of these function can be found in ``micrortps/client/profile/session/create_entities_ref.h``.

------

.. code-block:: c

    uint16_t mr_write_create_participant_ref(mrSession* session, mrStreamId stream_id, mrObjectId object_id, const char* ref, uint8_t mode);

Create a `datareader` entity in the agent.

:session: Session structure previously initialized.
:stream_id: The output stream ID where the message will be written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``MR_DATAREADER_ID``
:xml: A xml representation of the new entity.
:mode: Determines the creation entity mode.
        Currently, only soported ``MR_REPLACE``.
        It will delete the entity previously in the agent if exists.
        A ``0`` value, implies that only creates the entity if it does not exists.

------

Create entities common profile
``````````````````````````````
These functions are enabled when ``PROFILE_CREATE_ENTITIES_XML`` or ``PROFILE_CREATE_ENTITIES_REF`` are enabled into ``client.config`` file.
The declaration of these function can be found in ``micrortps/client/profile/session/common_create_entities.h``.

------

.. code-block:: c

    uint16_t mr_write_delete_entity(mrSession* session, mrStreamId stream_id, mrObjectId object_id);

Removes a entity.

:session: Session structure previously initialized.
:stream_id: The output stream ID where the message will be written.
:object_id: The identifier that will be deleted.

------

Read access profile
```````````````````
These functions are enabled when PROFILE_READ_ACCESS is enabled into ``client.config`` file.
The declaration of these function can be found in ``micrortps/client/profile/session/read_access.h``.

------

.. code-block:: c

    uint16_t mr_write_request_data(mrSession* session, mrStreamId stream_id, mrObjectId datareader_id, mrStreamId data_stream_id, mrDeliveryControl* delivery_control);

This function requests a read from a datareader of the agent.
The returned value is an identifier of the request.
All received topic will have the same request identifier.
The topics will be received at the callback topic through the ``run_session`` function.
If there is no error with the request data, the topics will be received generating a status callback with the value ``MR_STATUS_OK``.
If there is an error, a status error will be sent by the agent.

:session: Session structure previously initialized.
:stream_id: The output stream ID where the message will be written.
:object_id: The Data Reader ID that will read the topic from the DDS World.
:data_stream_id: The input stream ID where the data will be received.
:delivery_control: Optional information about how the delivery must be.
                   A ``NULL`` value is accepted, in this case, only one topic will be received.

------

Write access profile
````````````````````
These functions are generated automatically by `MicroRTPSGen` utility with the ``-write-access-profile`` option enabled over an idl file with a topic `TOPICTYPE`.
The declaration of these function can be found in the generated file ``TOPICTYPEWriter.h``.

------

.. code-block:: c

    bool mr_write_TOPICTYPE_topic(mrSession* session, mrStreamId stream_id, mrObjectId datawriter_id, const TOPICTYPE* topic);

This function writes a topic into a stream.
If the returned value is ``true``, the topic has been serialized.
The topic will be sent in the next ``run_session`` function.

:session: Session structure previously initialized.
:stream_id: The output stream ID where the message will be written.
:object_id: The DataWriter ID that will write the topic to the DDS World.
:topic: The topic that will be sent to the agent.

------

Topic serialization
```````````````````
Functions to serialize and deserialize topics.
These functions are generated automatically by `MicroRTPSGen` utility over an idl file with a topic `TOPICTYPE`.
The declaration of these function can be found in the generated file ``TOPICTYPE.h``.

------

.. code-block:: c

    bool TOPICTYPE_serialize_topic(struct MicroBuffer* writer, const TOPICTYPE* topic);

It serializes a topic into a MicroBuffer.
The returned value indicates if the serialization was successful.

:writer: A MicroBuffer representing the buffer for the serialization.
:topic: Struct to serialize.

------

.. code-block:: c

    bool TOPICTYPE_deserialize_topic(struct MicroBuffer* reader, TOPICTYPE* topic);

It deserializes a topic from a MicroBuffer.
The returned value indicates if the serialization was successful.

:reader: A MicroBuffer representing the buffer for the deserialization.
:topic: Struct where deserialize.

------

.. code-block:: c

    uint32_t TOPICTYPE_size_of_topic(const TOPICTYPE* topic, uint32_t size);

It counts the number of bytes that the topic will need in a `MicroBuffer`.

:topic: Struct to count the size.
:size: Number of bytes already written into the `MicroBuffer`.

------

General utilities
`````````````````
Utility functions.
The declaration of these functions can be found in ``micrortps/client/core/session/stream_id.h`` and ``micrortps/client/core/session/object_id.h``.

------

.. code-block:: c

    mrStreamId mr_stream_id(uint8_t index, mrStreamType type, mrStreamDirection direction);

Creates an stream identifier.
This function does not create a new stream, only creates its identifier to be used in the `Client` API.

:index: Identifier of the stream, its value correspond to the creation order of the stream, different for each `type`.
:type: The type of the stream, it can be MR_BEST_EFFORT_STREAM or MR_RELIABLE_STREAM.
:direction: Represents the direccion of the stream, it can be MR_INPUT_STREAM or MT_OUTPUT_STREAM.

------

.. code-block:: c

    mrStreamId mr_stream_id_from_raw(uint8_t stream_id_raw, mrStreamDirection direction);

Creates an stream identifier.
This function does not create a new stream, only creates its identifier to be used in the `Client` API.

:raw: identifier of the stream.
      It goes from 0 to 255.
      0 is for internal library use.
      1 to 127, for best effort.
      128 to 255, for reliable.
:direction: Represents the direccion of the stream, it can be MR_INPUT_STREAM or MT_OUTPUT_STREAM.

------

.. code-block:: c

    mrObjectId mr_object_id(uint16_t id, uint8_t type);

Creates a identifier for reference an entity.

:id: identifier of the object, different for each `type`
     (Can be several ids with the same id if they have different types)
:type: The type of the entity.
       It can be:
       * MR_PARTICIPANT_ID
       * MR_TOPIC_ID
       * MR_PUBLISHER_ID
       * MR_SUBSCRIBER_ID
       * MR_DATAWRITER_ID
       * MR_DATAREADER_ID

------

Transport
`````````
These functions are platform dependent.
The values ``PROFILE_XXX_TRANSPORT`` found into ``client.config`` allow to enable some of them.
The declaration of these function can be found in ``micrortps/client/profile/transport/`` folder.
The common init transport functions follow the next nomenclature.

------

.. code-block:: c

    bool mr_init_udp_transport(UDPTransport* transport, const char* ip, uint16_t port);

Initializes an UDP connection.

:transport: The uninitialized structure used for managing the transport.
            This structure must to be accesible during the connection.
:ip: Agent ip.
:port: Agent port.

------

.. code-block:: c

    bool mr_init_tcp_transport(TCPTransport* transport, const char* ip, uint16_t port);

Initializes a TCP connection.
If the TCP is used, the behaviour of best effort streams will be similiar to reliable streams in UDP.

:transport: The uninitialized structure used for managing the transport.
            This structure must to be accesible during the connection.
:ip: Agent ip.
:port: Agent port.

------

.. code-block:: c

    bool mr_init_serial_transport(SerialTransport* transport, const char* device, uint8_t remote_addr, uint8_t local_addr);

Initializes a Serial connection using a device.

:transport: The uninitialized structure used for managing the transport.
            This structure must to be accesible during the connection.
:device: Device used for the serial connection.
:remote_addr: Identifier of the agent in the serial connection.
              By default, the agent identifier in a serial is 0.
:local_addr: Identifier of the client in the serial connection.

------

.. code-block:: c

    bool mr_init_serial_transport_fd(SerialTransport* transport, const int fd, uint8_t remote_addr, uint8_t local_addr);

Initializes a Serial connection using a file descriptor

:transport: The uninitialized structure used for managing the transport.
            This structure must to be accesible during the connection.
:fd: File descriptor of the serial connection. Usually, the fd comes from the ``open`` OS function.
:remote_addr: Identifier of the agent in the serial connection.
              By default, the agent identifier in a serial is 0.
:local_addr: Identifier of the client in the serial connection.

------

.. code-block:: c

    bool mr_close_PROTOCOL_transport(PROTOCOLTransport* transport);

Closes a transport previously opened. `PROTOCOL` can be ``udp``, ``tcp`` or ``serial``.

:transport: The transport to close.

