.. _micro_xrce_dds_client_label:

Micro XRCE-DDS Client
=====================
In *Micro XRCE-DDS*, a *Client* can communicate with DDS Network as any other DDS actor could do.
*Clients* can publish and subscribe to data Topics in the DDS Global Data Space.

*Micro XRCE-DDS* provides you with a C API to create *Micro XRCE-DDS Clients*.
All functions needed to set up the *Client* can be found into ``client.h`` header.
This is the only header you need to include.

Profiles
--------

The client library follows a profile concept that enables to choose add or remove some features in configuration time.
This allows to reduce the client library size, if there are features that are not used.
The profiles can be chosen in ``client.config`` and start with the prefix ``PROFILE``.
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

The addition of a new transport or an existant transport for a new platform can be easily implemented setting the callbacks of a ``uxrCommunication`` structure.
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

``PROFILE_WRITE_ACCESS=<bool>``
    Enables or disables the functions related to write topics.

``PROFILE_DISCOVERY=<bool>``
    Enables or disables the functions the discovery feature.

``PROFILE_UDP_TRANSPORT=<bool>``
    Enables or disables the posibility to connect with the agent by UDP.

``PROFILE_TCP_TRANSPORT=<bool>``
    Enables or disables the posibility to connect with the agent by TCP.

``PROFILE_SERIAL_TRANSPORT=<bool>``
    Enables or disables the posibility to connect with the agent by Serial.

``CONFIG_MAX_OUTPUT_BEST_EFFORT_STREAMS=<number>``
    Configures the maximun output best effort streams that a session could have.
    The calls to ``uxr_create_output_best_effort_stream`` function for a session must be less or equal that this value.

``CONFIG_MAX_OUTPUT_RELIABLE_STREAMS=<number>``
    Configures the maximun output reliable streams that a session could have.
    The calls to ``uxr_create_output_reliable_stream`` function for a session must be less or equal that this value.

``CONFIG_MAX_INPUT_BEST_EFFORT_STREAMS=<number>``
    Configure the maximun input best effort streams that a session could have.
    The calls to ``uxr_create_input_best_effort_stream`` function for a session must be less or equal that this value.

``CONFIG_MAX_INPUT_RELIABLE_STREAMS=<number>``
    Configures the maximun input reliable streams that a session could have.
    The calls to ``uxr_create_input_reliable_stream`` function for a session must be less or equal that this value.

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

Streams
-------
The client communication is performed by streams.
The streams can be seen as communication channels.
There are two types of streams: best effort and reliable streams and you can create several of them.

- Best effort streams will send and receive the data leaving the reliability to the transport layer.
  As a result, the best effort streams consume fewer resources than a reliable stream.

- Reliable streams perform the communication without lost regardless of the transport layer.
  To avoid message losses, the reliable streams use additional messages to confirm the delivery, along to a history of the messages sent and received.
  The history is used to save the messages that have not been confirmed yet.
  A high history will reduce the data traffic of confirmation messages in transports with a high loss rate.
  This implies that a reliable stream is more expensive than best effort streams, in both, memory and bandwidth.

The streams are maybe the highest memory load part of the application.
For that, the choice of a right configuration for the application purpose is highly recommendable, especially when the target is a limited resources device.
The :ref:`optimization_label` page explain more about how to archive this.

API
---
As a nomenclature, `Micro XRCE-DDS Client` API uses a ``uxr_`` prefix in all of their public API functions and ``uxr`` prefix in the types.
In constants values an ``UXR_`` prefix is used.
The functions belonging to the public interface of the library are only those with the tag ``UXRDDLAPI`` in their declarations.

Session
```````
These functions are available even if no profile has been enabled in ``client.config`` file.
The declaration of these function can be found in ``uxr/client/core/session/session.h``.

------

.. code-block:: c

    void uxr_init_session(uxrSession* session, uxrCommunication* comm, uint32_t key);

Initializes a session structure.
Once this function is called, a ``create_session`` call can be performed.

:session: Session structure where manage the session data.
:key: The identifying key of the client.
      All clients connected to an agent must have different key.
:comm: Communication used for connecting to the agent.
       All different transports have a common attribute uxrCommunication.
       This parameter can not be shared between active sessions.

------

.. code-block:: c

    void uxr_set_status_callback(uxrSession* session, uxrOnStatusFunc on_status_func, void* args);

Assigns the callback for the agent status messages.

:session: Session structure previously initialized.
:on_status_func: Function callback that will be called when a valid status message comes from the agent.
:args: User pointer data.
       The args will be provided to ``on_status_func`` function.

------

.. code-block:: c

    void uxr_set_topic_callback(uxrSession* session, uxrOnTopicFunc on_topic_func, void* args);

Assigns the callback for topics.
The topics will be received only if a ``request_data`` function has been called.

:session: Session structure previously initialized.
:on_status_func: Function callback that will be called when a valid data message comes from the agent.
:args: User pointer data.
       The args will be provided to ``on_topic_func`` function.

------

.. code-block:: c

    bool uxr_create_session(uxrSession* session);

Creates a new session with the agent.
This function logs in a session, enabling any other XRCE communication with the agent.

:session: Session structure previously initialized.

------

.. code-block:: c

    bool uxr_delete_session(uxrSession* session);

Deletes a session previously created.
All `XRCE` entities created with the session will be removed.
This function logs out a session, disabling any other `XRCE` communication with the agent.

:session: Session structure previously initialized.

------

.. code-block:: c

    uxrStreamId uxr_create_output_best_effort_stream(uxrSession* session, uint8_t* buffer, size_t size);

Creates and initializes an output best effort stream for writing.
The ``uxrStreamId`` returned represents the new stream and can be used to manage it.
The number of available calls to this function must be less or equal than ``CONFIG_MAX_OUTPUT_BEST_EFFORT_STREAMS`` value of the ``client.config`` file.

:session: Session structure previously initialized.
:buffer: Memory block where the messages will be written.
:size: Buffer size.

------

.. code-block:: c

    uxrStreamId uxr_create_output_reliable_stream(uxrSession* session, uint8_t* buffer, size_t size, size_t history);

Creates and initializes an output reliable stream for writing.
The ``uxrStreamId`` returned represents the new stream and can be used to manage it.
The number of available calls to this function must be less or equal than ``CONFIG_MAX_OUTPUT_RELIABLE_STREAMS`` value of the ``client.config`` file.

:session: Session structure previously initialized.
:buffer: Memory block where the messages will be written.
:size: Buffer size.
:history: History used for the reliable connection.
          The buffer size will be splited into smaller buffers using this value.
          The history must be a power of two.

------

.. code-block:: c

    uxrStreamId uxr_create_input_best_effort_stream(uxrSession* session);

Creates and initializes an input best effort stream for receiving messages.
The ``uxrStreamId`` returned represents the new stream and can be used to manage it.
The number of available calls to this function must be less or equal than ``CONFIG_MAX_INPUT_BEST_EFFORT_STREAMS`` value of the ``client.config`` file.

:session: Session structure previously initialized.

------

.. code-block:: c

    uxrStreamId uxr_create_input_reliable_stream(uxrSession* session, uint8_t* buffer, size_t size, size_t history);

Creates and initializes an input reliable stream for receiving messages.
The returned ``uxrStreamId`` represents the new stream and can be used to manage it.
The number of available calls to this function must be less or equal than ``CONFIG_MAX_INPUT_RELIABLE_STREAMS`` value of the ``client.config`` file.

:session: Session structure previously initialized.
:buffer: Memory block where the messages will be storaged.
:size: Buffer size.
:history: History used for the reliable connection.
          The buffer will be splited into smaller buffers using this value.
          The history must be a power of two.

------

.. code-block:: c

    void uxr_flash_output_streams(uxrSession* session);

Flashes all output streams sending the data through the transport.

:session: Session structure previously initialized.

------

.. code-block:: c

    void uxr_run_session_time(uxrSession* session, int time);

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
          For waiting without timeout, set the value to ``UXR_TIMEOUT_INF``

------

.. code-block:: c

    void uxr_run_session_until_timeout(uxrSession* session, int timeout);

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
          For waiting without timeout, set the value to ``UXR_TIMEOUT_INF``

------

.. code-block:: c

    bool uxr_run_session_until_confirm_delivery(uxrSession* session, int timeout);

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
          For waiting without timeout, set the value to ``UXR_TIMEOUT_INF``

------

.. code-block:: c

    bool uxr_run_session_until_all_status(uxrSession* session, int timeout, const uint16_t* request_list, uint8_t* status_list, size_t list_size);

The main library function.
This function processes the internal functionality of a session.
This implies:

1. Flashes all output streams sending the data through the transport.
2. If there is any reliable stream, it will perform the asociated reliable behaviour to ensure the communication.
3. Listenes messages from the agent and call the associated callback if exists (a topic callback or a status callback).

The ``_until_all_status`` suffix function version will perform these actions during ``timeout`` duration
or until all requested status had been received.
The function will return ``true`` if all status have been received and all of them have the value ``UXR_STATUS_OK`` or ``UXR_STATUS_OK_MATCHED``, ``false`` otherwise.

:session: Session structure previously initialized.
:timeout: Maximun time for waiting to a new message, in milliseconds.
          For waiting without timeout, set the value to ``UXR_TIMEOUT_INF``
:request_list: An array of request to confirm with a status.
:status_list: An uninitialized array with the same size as ``request_list`` where the status values will be written.
              The position of a status in the list corresponds to the request at the same position in ``request_list``.
:list_size: The size of ``request_list`` and ``status_list`` arrays.

------

.. code-block:: c

    bool uxr_run_session_until_one_status(uxrSession* session, int timeout, const uint16_t* request_list, uint8_t* status_list, size_t list_size);

The main library function.
This function processes the internal functionality of a session.
This implies:

1. Flashes all output streams sending the data through the transport.
2. If there is any reliable stream, it will perform the asociated reliable behaviour to ensure the communication.
3. Listenes messages from the agent and call the associated callback if exists (a topic callback or a status callback).

The ``_until_one_status`` suffix function version will perform these actions during ``timeout`` duration
or until one requested status had been received.
The function will return ``true`` if one status have been received and has the value ``UXR_STATUS_OK`` or ``UXR_STATUS_OK_MATCHED``, ``false`` otherwise.

:session: Session structure previously initialized.
:timeout: Maximun time for waiting to a new message, in milliseconds.
          For waiting without timeout, set the value to ``UXR_TIMEOUT_INF``
:request_list: An array of request that can be confirmed.
:status_list: An uninitialized array with the same size as ``request_list`` where the statu value will be written.
              The position of the status in the list corresponds to the request at the same position in ``request_list``.
:list_size: The size of ``request_list`` and ``status_list`` arrays.

------

Create entities by XML profile
``````````````````````````````
These functions are enabled when ``PROFILE_CREATE_ENTITIES_XML`` is enabled in the ``client.config`` file.
The declaration of these functions can be found in ``uxr/client/profile/session/create_entities_xml.h``.

------

.. code-block:: c

    uint16_t uxr_buffer_create_participant_xml(uxrSession* session, uxrStreamId stream_id, uxrObjectId object_id, uint16_t domain, const char* xml, uint8_t mode);

Creates a `participant` entity in the agent.
The message is only written into the stream buffer.
To send the message is necessary call to ``uxr_flash_output_streams`` or to ``uxr_run_session`` function.

:session: Session structure previously initialized.
:stream_id: The output stream ID where the message will be written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``UXR_PARTICIPANT_ID``
:xml: A xml representation of the new entity.
:mode: Determines the creation entity mode.
        Currently, only soported ``UXR_REPLACE``.
        It will delete the entity previously in the agent if exists.
        A ``0`` value, implies that only creates the entity if it does not exists.

------

.. code-block:: c

    uint16_t uxr_buffer_create_topic_xml(uxrSession* session, uxrStreamId stream_id, uxrObjectId object_id, uxrObjectId participant_id, const char* xml, uint8_t mode);

Creates a `topic` entity in the agent.
The message is only written into the stream buffer.
To send the message is necessary call to ``uxr_flash_output_streams`` or to ``uxr_run_session`` function.

:session: Session structure previously initialized.
:stream_id: The output stream ID where the message will be written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``UXR_TOPIC_ID``
:xml: A xml representation of the new entity.
:mode: Determines the creation entity mode.
        Currently, only soported ``UXR_REPLACE``.
        It will delete the entity previously in the agent if exists.
        A ``0`` value, implies that only creates the entity if it does not exists.

------

.. code-block:: c

    uint16_t uxr_buffer_create_publisher_xml(uxrSession* session, uxrStreamId stream_id, uxrObjectId object_id, uxrObjectId participant_id, const char* xml, uint8_t mode);

Creates a `publisher` entity in the agent.
The message is only written into the stream buffer.
To send the message is necessary call to ``uxr_flash_output_streams`` or to ``uxr_run_session`` function.

:session: Session structure previously initialized.
:stream_id: The output stream ID where the message will be written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``UXR_PUBLISHER_ID``
:xml: A xml representation of the new entity.
:mode: Determines the creation entity mode.
        Currently, only soported ``UXR_REPLACE``.
        It will delete the entity previously in the agent if exists.
        A ``0`` value, implies that only creates the entity if it does not exists.

------

.. code-block:: c

    uint16_t uxr_buffer_create_subscriber_xml(uxrSession* session, uxrStreamId stream_id, uxrObjectId object_id, uxrObjectId participant_id, const char* xml, uint8_t mode);

Creates a `publisher` entity in the agent.
The message is only written into the stream buffer.
To send the message is necessary call to ``uxr_flash_output_streams`` or to ``uxr_run_session`` function.

:session: Session structure previously initialized.
:stream_id: The output stream ID where the message will be written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``UXR_SUBSCRIBER_ID``
:xml: A xml representation of the new entity.
:mode: Determines the creation entity mode.
        Currently, only soported ``UXR_REPLACE``.
        It will delete the entity previously in the agent if exists.
        A ``0`` value, implies that only creates the entity if it does not exists.

------

.. code-block:: c

    uint16_t uxr_buffer_create_datawriter_xml(uxrSession* session, uxrStreamId stream_id, uxrObjectId object_id, uxrObjectId publisher_id, const char* xml, uint8_t mode);

Creates a `datawriter_id` entity in the agent.
The message is only written into the stream buffer.
To send the message is necessary call to ``uxr_flash_output_streams`` or to ``uxr_run_session`` function.

:session: Session structure previously initialized.
:stream_id: The output stream ID where the message will be written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``UXR_DATAWRITER_ID``
:xml: A xml representation of the new entity.
:mode: Determines the creation entity mode.
        Currently, only soported ``UXR_REPLACE``.
        It will delete the entity previously in the agent if exists.
        A ``0`` value, implies that only creates the entity if it does not exists.

------

.. code-block:: c

    uint16_t uxr_buffer_create_datareader_xml(uxrSession* session, uxrStreamId stream_id, uxrObjectId object_id, uxrObjectId subscriber_id, const char* xml, uint8_t mode);

Creates a `datareader` entity in the agent.
The message is only written into the stream buffer.
To send the message is necessary call to ``uxr_flash_output_streams`` or to ``uxr_run_session`` function.

:session: Session structure previously initialized.
:stream_id: The output stream ID where the message will be written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``UXR_DATAREADER_ID``
:xml: A xml representation of the new entity.
:mode: Determines the creation entity mode.
        Currently, only soported ``UXR_REPLACE``.
        It will delete the entity previously in the agent if exists.
        A ``0`` value, implies that only creates the entity if it does not exists.

------

Create entities by reference profile
````````````````````````````````````
These functions are enabled when ``PROFILE_CREATE_ENTITIES_REF`` is enabled in the ``client.config`` file.
The declaration of these functions can be found in ``uxr/client/profile/session/create_entities_ref.h``.

------

.. code-block:: c

    uint16_t uxr_buffer_create_participant_ref(uxrSession* session, uxrStreamId stream_id, uxrObjectId object_id, const char* ref, uint8_t mode);

Creates a `datareader` entity in the agent.
The message is only written into the stream buffer.
To send the message is necessary call to ``uxr_flash_output_streams`` or to ``uxr_run_session`` function.

:session: Session structure previously initialized.
:stream_id: The output stream ID where the message will be written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``UXR_DATAREADER_ID``
:xml: A xml representation of the new entity.
:mode: Determines the creation entity mode.
        Currently, only soported ``UXR_REPLACE``.
        It will delete the entity previously in the agent if exists.
        A ``0`` value, implies that only creates the entity if it does not exists.

------

Create entities common profile
``````````````````````````````
These functions are enabled when ``PROFILE_CREATE_ENTITIES_XML`` or ``PROFILE_CREATE_ENTITIES_REF`` are enabled in the ``client.config`` file.
The declaration of these functions can be found in ``uxr/client/profile/session/common_create_entities.h``.

------

.. code-block:: c

    uint16_t uxr_buffer_delete_entity(uxrSession* session, uxrStreamId stream_id, uxrObjectId object_id);

Removes a entity.
The message is only written into the stream buffer.
To send the message is necessary call to ``uxr_flash_output_streams`` or to ``uxr_run_session`` function.

:session: Session structure previously initialized.
:stream_id: The output stream ID where the message will be written.
:object_id: The identifier that will be deleted.

------

Read access profile
```````````````````
These functions are enabled when PROFILE_READ_ACCESS is enabled in the ``client.config`` file.
The declaration of these functions can be found in ``uxr/client/profile/session/read_access.h``.

------

.. code-block:: c

    uint16_t uxr_buffer_request_data(uxrSession* session, uxrStreamId stream_id, uxrObjectId datareader_id, uxrStreamId data_stream_id, uxrDeliveryControl* delivery_control);

This function requests a read from a datareader of the agent.
The returned value is an identifier of the request.
All received topic will have the same request identifier.
The topics will be received at the callback topic through the ``run_session`` function.
If there is no error with the request data, the topics will be received generating a status callback with the value ``UXR_STATUS_OK``.
If there is an error, a status error will be sent by the agent.
The message is only written into the stream buffer.
To send the message is necessary call to ``uxr_flash_output_streams`` or to ``uxr_run_session`` function.

:session: Session structure previously initialized.
:stream_id: The output stream ID where the message will be written.
:object_id: The Data Reader ID that will read the topic from the DDS World.
:data_stream_id: The input stream ID where the data will be received.
:delivery_control: Optional information about how the delivery must be.
                   A ``NULL`` value is accepted, in this case, only one topic will be received.

------

Write access profile
````````````````````
These functions are enabled when PROFILE_WRITE_ACCESS is enabled in the ``client.config`` file.
The declaration of these functions can be found in ``uxr/client/profile/session/write_access.h``.

------

.. code-block:: c

    bool uxr_prepare_output_stream(uxrSession* session, uxrStreamId stream_id, uxrObjectId datawriter_id,
                                  struct ucdrBuffer* mb_topic, uint32_t topic_size);

Requests a writing into a specific output stream.
For that this function will initialize a ``ucdrBuffer`` struct where a topic of ``topic_size`` size must be serialized.
If the returned value is ``true``, exists the necessary gap for writing a ``topic_size`` bytes into the stream.
If the returned value is ``false``, the topic can no be serialized into the stream.
The topic will be sent in the next ``run_session`` function.

NOTE: All `topic_size` bytes requested will be sent to the agent after a ``run_session`` call, no matter if the ``ucdrBuffer`` has been used or not.

:session: Session structure previously initialized.
:stream_id: The output stream ID where the message will be written.
:datawriter_id: The DataWriter ID that will write the topic to the DDS World.
:mb_topic: A ``ucdrBuffer`` struct used to serialize the topic.
           This struct points to a requested gap into the stream.
:topic_size: The bytes that will be reserved in the stream.

------

Discovery profile
```````````````````
The discovery profile allows to discover agents in the network by UDP.
The reachable agents will respond to the discovery call sending information about them, as their ip and port.
There is two modes: multicast and unicast.
The discovery phase can be performed before the `uxr_create_session` call in order to determine the agent to connect with.
These functions are enabled when PROFILE_DISCOVERY is enabled in the ``client.config`` file.
The declaration of these functions can be found in ``uxr/client/profile/discovery/discovery.h``.

bool uxr_discovery_agents_multicast(uint32_t attemps, int period, uxrOnAgentFound on_agent_func, void* args, uxrAgentAddress* chosen);

------

.. code-block:: c

    bool uxr_discovery_agents_multicast(uint32_t attempts, int period,
                                        uxrOnAgentFound on_agent_func, void* args, uxrAgentAddress* chosen);

Searches into the network using multicast ip "239.255.0.2" and port 7400 (default used by the agent) in order to discover agents.

:attempts: The number of attempts to send the discovery message to the network.
:period: How often will be sent the discovery message to the network.
:on_agent_func: The callback function that will be called when an agent was discovered.
                The callback returns a boolean value.
                A `true` means that the discovery rutine will be end and exit.
                The current agent will be selected as *chosen*.
                A `false` implies that the discovery rutine must to continue searching agents.
:args: User arguments passed to the callback function.
:chosen: If the callback function was returned `true`, this value will contains the agent value of the callback.

------

.. code-block:: c

    bool uxr_discovery_agents_unicast(uint32_t attempts, int period,
                                      uxrOnAgentFound on_agent_func, void* args, uxrAgentAddress* chosen,
                                      const uxrAgentAddress* agent_list, size_t agent_list_size);

Searches into the network using a list of unicast directions in order to discover agents.

:attempts: The number of attempts to send the discovery message to the network.
:period: How often will be sent the discovery message to the network.
:on_agent_func: The callback function that will be called when an agent was discovered.
                The callback returns a boolean value.
                A ``true`` means that the discovery rutine will be end and exit.
                The current agent will be selected as *chosen*.
                A ``false`` implies that the discovery rutine must to continue searching agents.
:args: User arguments passed to the callback function.
:chosen: If the callback function was returned ``true``, this value will contains the agent value of the callback.
:agent_list: The list of address where discover agent.
             By default the agents will be listen at **port 7400** the discovery messages..
:agent_list_size: The size of the ``agent_list``.

------

Topic serialization
```````````````````
Functions to serialize and deserialize topics.
These functions are generated automatically by `Micro XRCE-DDS Gen` utility over an idl file with a topic `TOPICTYPE`.
The declaration of these function can be found in the generated file ``TOPICTYPE.h``.

------

.. code-block:: c

    bool TOPICTYPE_serialize_topic(struct ucdrBuffer* writer, const TOPICTYPE* topic);

It serializes a topic into a ucdrBuffer.
The returned value indicates if the serialization was successful.

:writer: A ucdrBuffer representing the buffer for the serialization.
:topic: Struct to serialize.

------

.. code-block:: c

    bool TOPICTYPE_deserialize_topic(struct ucdrBuffer* reader, TOPICTYPE* topic);

It deserializes a topic from a ucdrBuffer.
The returned value indicates if the serialization was successful.

:reader: A ucdrBuffer representing the buffer for the deserialization.
:topic: Struct where deserialize.

------

.. code-block:: c

    uint32_t TOPICTYPE_size_of_topic(const TOPICTYPE* topic, uint32_t size);

It counts the number of bytes that the topic will need in a `ucdrBuffer`.

:topic: Struct to count the size.
:size: Number of bytes already written into the `ucdrBuffer`.
       Typically its value is `0` if the purpose is to use in ``uxr_prepare_output_stream`` function.

------

General utilities
`````````````````
Utility functions.
The declaration of these functions can be found in ``uxr/client/core/session/stream_id.h`` and ``uxr/client/core/session/object_id.h``.

------

.. code-block:: c

    uxrStreamId uxr_stream_id(uint8_t index, uxrStreamType type, uxrStreamDirection direction);

Creates an stream identifier.
This function does not create a new stream, only creates its identifier to be used in the `Client` API.

:index: Identifier of the stream, its value correspond to the creation order of the stream, different for each `type`.
:type: The type of the stream, it can be UXR_BEST_EFFORT_STREAM or UXR_RELIABLE_STREAM.
:direction: Represents the direccion of the stream, it can be UXR_INPUT_STREAM or MT_OUTPUT_STREAM.

------

.. code-block:: c

    uxrStreamId uxr_stream_id_from_raw(uint8_t stream_id_raw, uxrStreamDirection direction);

Creates an stream identifier.
This function does not create a new stream, only creates its identifier to be used in the `Client` API.

:raw: identifier of the stream.
      It goes from 0 to 255.
      0 is for internal library use.
      1 to 127, for best effort.
      128 to 255, for reliable.
:direction: Represents the direccion of the stream, it can be UXR_INPUT_STREAM or MT_OUTPUT_STREAM.

------

.. code-block:: c

    uxrObjectId uxr_object_id(uint16_t id, uint8_t type);

Creates a identifier for reference an entity.

:id: identifier of the object, different for each `type`
     (Can be several ids with the same id if they have different types)
:type: The type of the entity.
       It can be:
       * UXR_PARTICIPANT_ID
       * UXR_TOPIC_ID
       * UXR_PUBLISHER_ID
       * UXR_SUBSCRIBER_ID
       * UXR_DATAWRITER_ID
       * UXR_DATAREADER_ID

------

Transport
`````````
These functions are platform dependent.
The values ``PROFILE_XXX_TRANSPORT`` found into ``client.config`` allow to enable some of them.
The declaration of these function can be found in ``uxr/client/profile/transport/`` folder.
The common init transport functions follow the next nomenclature.

------

.. code-block:: c

    bool uxr_init_udp_transport(UDPTransport* transport, const char* ip, uint16_t port);

Initializes an UDP connection.

:transport: The uninitialized structure used for managing the transport.
            This structure must to be accesible during the connection.
:ip: Agent ip.
:port: Agent port.

------

.. code-block:: c

    bool uxr_init_tcp_transport(TCPTransport* transport, const char* ip, uint16_t port);

Initializes a TCP connection.
If the TCP is used, the behaviour of best effort streams will be similiar to reliable streams in UDP.

:transport: The uninitialized structure used for managing the transport.
            This structure must to be accesible during the connection.
:ip: Agent ip.
:port: Agent port.

------

.. code-block:: c

    bool uxr_init_serial_transport(SerialTransport* transport, const char* device, uint8_t remote_addr, uint8_t local_addr);

Initializes a Serial connection using a device.

:transport: The uninitialized structure used for managing the transport.
            This structure must to be accesible during the connection.
:device: Device used for the serial connection.
:remote_addr: Identifier of the agent in the serial connection.
              By default, the agent identifier in a serial is 0.
:local_addr: Identifier of the client in the serial connection.

------

.. code-block:: c

    bool uxr_init_serial_transport_fd(SerialTransport* transport, const int fd, uint8_t remote_addr, uint8_t local_addr);

Initializes a Serial connection using a file descriptor

:transport: The uninitialized structure used for managing the transport.
            This structure must to be accesible during the connection.
:fd: File descriptor of the serial connection. Usually, the fd comes from the ``open`` OS function.
:remote_addr: Identifier of the agent in the serial connection.
              By default, the agent identifier in a serial is 0.
:local_addr: Identifier of the client in the serial connection.

------

.. code-block:: c

    bool uxr_close_PROTOCOL_transport(PROTOCOLTransport* transport);

Closes a transport previously opened. `PROTOCOL` can be ``udp``, ``tcp`` or ``serial``.

:transport: The transport to close.

