.. _client_api_label:

Client API
==========

*eProsima Micro XRCE-DDS* provides the user with a C API to create *eProsima Micro XRCE-DDS Clients* applications.
**All** functions needed to set up the *Client* can be found in the :code:`client.h` header.
That is the only header the user needs to include.

In this section, we provide the full API for the *Micro XRCE-DDS Client*.
As a nomenclature, this API uses the :code:`uxr_` prefix in all of its public functions and the :code:`uxr`
prefix in the types. In constants values, the :code:`UXR_` prefix is used.
The functions belonging to the public interface of the library are only those with the tag :code:`UXRDDLAPI`
in their declarations.

The functions are grouped as follows:

* :ref:`session_api`
* :ref:`entities_xml_api`
* :ref:`entities_ref_api`
* :ref:`entities_common_api`
* :ref:`read_access_api`
* :ref:`write_access_api`
* :ref:`discovery_api`
* :ref:`topic_serialization_api`
* :ref:`general_utilities_api`
* :ref:`transport_api`

.. _session_api:

Session
^^^^^^^

These functions are available even if no profile has been enabled.
The declaration of these functions can be found in ``uxr/client/core/session/session.h``.

------

.. code-block:: c

    void uxr_init_session(uxrSession* session, uxrCommunication* comm, uint32_t key);

Initializes a session structure.
Once this function is called, a ``create_session`` call can be performed.


:session: Session structure where to manage the session data.
:comm: Communication used for connecting to the *Agent*.
          All different transports have a common attribute ``uxrCommunication``.
          This parameter can not be shared between active sessions.
:key: The key identifier of the *Client*.
         All *Clients* connected to an *Agent* must have a different key.

------

.. _status_callback:

.. code-block:: c

    void uxr_set_status_callback(uxrSession* session, uxrOnStatusFunc on_status_func, void* args);

Assigns the callback for the *Agent* status messages.

:session: Session structure previously initialized.
:on_status_func: Function callback that is called when a valid status message comes from the *Agent*.
:args: User pointer data.
       The args are provided to the ``on_status_func`` function.

The function signature for the ``on_status_func`` callback is:

.. code-block:: c

    typedef void (*uxrOnStatusFunc) (struct uxrSession* session, uxrObjectId object_id, uint16_t request_id,
                                     uint8_t status, void* args);

:session: Session structure related to the status.
:object_id: The identifier of the entity related to the status.
:request_id: Status request id.
:status: Status value.
:args: User pointer data.

------

.. _topic_callback:

.. code-block:: c

    void uxr_set_topic_callback(uxrSession* session, uxrOnTopicFunc on_topic_func, void* args);

Assigns the callback for topics.
The topics are received only if a ``request_data`` function has been called.

:session: Session structure previously initialized.
:on_status_func: Function callback that is called when a valid data message comes from the *Agent*.
:args: User pointer data.
       The args are provided to the ``on_topic_func`` function.

The function signature for the ``on_topic_func`` callback is:

.. code-block:: c

    typedef void (*uxrOnTopicFunc) (struct uxrSession* session, uxrObjectId object_id, uint16_t request_id, uxrStreamId stream_id,
                                    struct ucdrBuffer* ub, uint16_t length, void* args);

:session: Session structure related to the topic.
:object_id: The identifier of the entity related to the topic.
:request_id: Request id of the``request_data`` transaction.
:stream_id: XRCE stream used.
:ub: Serialized topic data.
:length: Length of the serialized data.
:args: User pointer data.

------

.. _time_callback:

.. code-block:: c

    void uxr_set_time_callback(uxrSession* session, uxrOnTimeFunc on_time_func, void* args);

Assigns the time callback, to let the user perform custom time calculations based on client and agent timestamps.

:session: Session structure previously initialized.
:on_time_func: Function callback that is called .. ?
:args: User pointer data.
       The args are provided to the ``on_time_func`` function.

The function signature for the ``on_time_func`` callback is:

.. code-block:: c

    typedef void (*uxrOnTimeFunc) (struct uxrSession* session, int64_t current_timestamp, int64_t transmit_timestamp,
                                   int64_t received_timestamp, int64_t originate_timestamp, void* args);

:session: Session structure related to the topic.
:current_timestamp: Client's timestamp of the response packet reception.
:transmit_timestamp: Client's timestamp of the request packet transmission.
:received_timestamp: Agent's timestamp of the request packet reception.
:originate_timestamp: Agent's timestamp of the response packet transmission.
:args: User pointer data.

------

.. _request_callback:

.. code-block:: c

    void uxr_set_request_callback(uxrSession* session, uxrOnRequestFunc on_request_func, void* args);

Sets the request callback, which is called when the *Agent* sends a ``READ_DATA`` submessage associated with a ``Requester``.

:session: Session structure previously initialized.
:on_request_func: Function callback that is called when the *Agent* sends a ``READ_DATA`` submessage associated with a ``Requester``.
:args: User pointer data.
       The args are provided to the ``on_request_func`` function.

The function signature for the ``on_request_func`` callback is:

.. code-block:: c

    typedef void (*uxrOnRequestFunc) (struct uxrSession* session, uxrObjectId object_id, uint16_t request_id,
                                      SampleIdentity* sample_id, struct ucdrBuffer* ub, uint16_t length, void* args);

:session: Session structure related to the topic.
:object_id: The identifier of the entity related to the request.
:request_id: Request id of the``request_data`` transaction.
:sample_id: Identifier of the request.
:ub: Serialized request data.
:length: Lenght of the serialized data.
:args: User pointer data.

------

.. _reply_callback:

.. code-block:: c

    void uxr_set_reply_callback(uxrSession* session, uxrOnReplyFunc on_reply_func, void* args);

Sets the reply callback, which is called when the *Agent* sends a ``READ_DATA`` submessage associated with a ``Replier``.

:session: Session structure previously initialized.
:on_reply_func: Function callback that is called when the *Agent* sends a ``READ_DATA`` submessage associated with a ``Replier``
:args: User pointer data.
       The args are provided to ``on_reply_func`` function.

.. code-block:: c

    typedef void (*uxrOnReplyFunc) (struct uxrSession* session, uxrObjectId object_id, uint16_t request_id, uint16_t reply_id,
                                    struct ucdrBuffer* ub, uint16_t length, void* args);

:session: Session structure related to the topic.
:object_id: The identifier of the entity related to the request.
:request_id: Request id of the``request_data`` transaction.
:reply_id: Identifier of the reply.
:ub: Serialized request data.
:length: Lenght of the serialized data.
:args: User pointer data.

------

.. code-block:: c

    bool uxr_create_session(uxrSession* session);

Creates a new session on the *Agent*.
This function logs in a session, enabling any other `XRCE` communication with the *Agent*.

:session: Session structure previously initialized.

------

.. code-block:: c

    void uxr_create_session_retries(uxrSession* session, size_t retries);

Attempts to establish a new session on the *Agent* :code:`retries` times.
This function logs in a session, enabling any other `XRCE` communication with the *Agent*.

:session: Session structure previously initialized.
:retries: Number of attempts for creating a session.

------

.. code-block:: c

    bool uxr_delete_session(uxrSession* session);

Deletes a session previously created.
All `XRCE` entities created with the session are removed.
This function logs out a session, disabling any other `XRCE` communication with the *Agent*.

:session: Session structure previously initialized and created.

------

.. code-block:: c

    bool uxr_delete_session_retries(uxrSession* session, size_t retries);

Attempts to delete a previously created session :code:`retries` times.
All `XRCE` entities created with the session are removed.
This function logs out a session, disabling any other `XRCE` communication with the *Agent*.

:session: Session structure previously initialized and created.
:retries: Number of attempts for deleting a session.

------

.. code-block:: c

    uxrStreamId uxr_create_output_best_effort_stream(uxrSession* session, uint8_t* buffer, size_t size);

Creates and initializes an output best-effort stream for writing.
The ``uxrStreamId`` returned represents the new stream and can be used to manage it.
The number of available calls to this function must be less or equal than ``CONFIG_MAX_OUTPUT_BEST_EFFORT_STREAMS`` CMake argument.

:session: Session structure previously initialized and created.
:buffer: Memory block where the messages are written.
:size: Buffer size.

------

.. code-block:: c

    uxrStreamId uxr_create_output_reliable_stream(uxrSession* session, uint8_t* buffer, size_t size, size_t history);

Creates and initializes an output reliable stream for writing.
The ``uxrStreamId`` returned represents the new stream and can be used to manage it.
The number of available calls to this function must be less or equal than ``CONFIG_MAX_OUTPUT_RELIABLE_STREAMS`` CMake argument.

:session: Session structure previously initialized and created.
:buffer: Memory block where the messages are written.
:size: Buffer size.
:history: History used for reliable connection.
          The buffer size is split into a ``history`` number of smaller buffers.
          The history must be a divisor of the buffer size.

------

.. code-block:: c

    uxrStreamId uxr_create_input_best_effort_stream(uxrSession* session);

Creates and initializes an input best-effort stream for receiving messages.
The ``uxrStreamId`` returned represents the new stream and can be used to manage it.
The number of available calls to this function must be less or equal than ``CONFIG_MAX_INPUT_BEST_EFFORT_STREAMS`` CMake argument.

:session: Session structure previously initialized and created.

------

.. code-block:: c

    uxrStreamId uxr_create_input_reliable_stream(uxrSession* session, uint8_t* buffer, size_t size, size_t history);

Creates and initializes an input reliable stream for receiving messages.
The returned ``uxrStreamId`` represents the new stream and can be used to manage it.
The number of available calls to this function must be less or equal than ``CONFIG_MAX_INPUT_RELIABLE_STREAMS`` CMake argument.

:session: Session structure previously initialized and created.
:buffer: Memory block where the messages are stored.
:size: Buffer size.
:history: History used for reliable connection.
          The buffer size is split into a ``history`` number of smaller buffers.
          The history must be a divisor of the buffer size.

------

.. code-block:: c

    void uxr_flash_output_streams(uxrSession* session);

Flashes all output streams sending the data through the transport.

:session: Session structure previously initialized and created.

------

.. code-block:: c

    void uxr_run_session_time(uxrSession* session, int timeout_ms);

This function processes the internal functionality of a session.
It implies:

1. Flushing all output streams sending the data through the transport.
2. If there is any reliable stream, it performs the associated reliable behaviour to ensure communication.
3. Listening to messages from the *Agent* and calling the associated callback if it exists (which can be a
   :ref:`status <status_callback>`/:ref:`topic <topic_callback>`/:ref:`time <time_callback>`/:ref:`request <request_callback>`
   or :ref:`reply <reply_callback>` callback)


The ``time`` suffix function version perform these actions and listens to messages for a ``timeout_ms`` duration,
which is refreshed each time a new message is received, that is, the counter restarts for another ``timeout_ms`` period.
Only when the wait time for a message overcomes the ``timeout_ms`` duration, the function finishes.
The function returns ``true`` if the sending data have been confirmed, ``false`` otherwise.

:session: Session structure previously initialized and created.
:timeout_ms: Time for waiting for each new message, in milliseconds.
          For waiting without timeout, set the value to ``UXR_TIMEOUT_INF``

------

.. code-block:: c

    void uxr_run_session_timeout(uxrSession* session, int timeout_ms);

This function processes the internal functionality of a session.
It implies:

1. Flushing all output streams sending the data through the transport.
2. If there is any reliable stream, it performs the associated reliable behaviour to ensure communication.
3. Listening to messages from the *Agent* and calling the associated callback if it exists (which can be a
   :ref:`status <status_callback>`/:ref:`topic <topic_callback>`/:ref:`time <time_callback>`/:ref:`request <request_callback>`
   or :ref:`reply <reply_callback>` callback)

The ``timeout`` suffix function version performs these actions and listens to messages for a *total* ``timeout_ms`` duration.
Each time a new message is received, the counter retakes from where it left, that is, for a period equal to
``timeout_ms`` minus the time spent waiting for the previous message(s).
When the *total* wait time overcomes the ``timeout_ms`` duration, the function finishes.
The function returns ``true`` if the sending data have been confirmed, ``false`` otherwise.

:session: Session structure previously initialized and created.
:timeout_ms: Total time for waiting for a new message, in milliseconds.
          For waiting without timeout, set the value to ``UXR_TIMEOUT_INF``

------

.. code-block:: c

    void uxr_run_session_until_timeout(uxrSession* session, int timeout_ms);

This function processes the internal functionality of a session.
It implies:

1. Flushing all output streams sending the data through the transport.
2. If there is any reliable stream, it performs the associated reliable behaviour to ensure communication.
3. Listening to messages from the *Agent* and calling the associated callback if it exists (which can be a
   :ref:`status <status_callback>`/:ref:`topic <topic_callback>`/:ref:`time <time_callback>`/:ref:`request <request_callback>`
   or :ref:`reply <reply_callback>` callback)

The ``until_timeout`` suffix function version performs these actions until receiving one message,
for a ``timeout_ms`` time duration.
Once a message has been received or the timeout has been reached, the function finishes.
The function returns ``true`` if it has received a message, ``false`` if the timeout has been reached.

:session: Session structure previously initialized and created.
:timeout_ms: Maximum time for waiting for a new message, in milliseconds.
          For waiting without timeout, set the value to ``UXR_TIMEOUT_INF``

------

.. code-block:: c

    bool uxr_run_session_until_confirm_delivery(uxrSession* session, int timeout_ms);

This function processes the internal functionality of a session.
It implies:

1. Flushing all output streams sending the data through the transport.
2. If there is any reliable stream, it performs the associated reliable behaviour to ensure communication.
3. Listening to messages from the *Agent* and calling the associated callback if it exists (which can be a
   :ref:`status <status_callback>`/:ref:`topic <topic_callback>`/:ref:`time <time_callback>`/:ref:`request <request_callback>`
   or :ref:`reply <reply_callback>` callback)

The ``until_confirm_delivery`` suffix function version performs these actions during ``timeout_ms``
or until the output reliable streams confirm that the sent messages have been received by the *Agent*.
The function returns ``true`` if the sent data have been confirmed, ``false`` otherwise.

:session: Session structure previously initialized and created.
:timeout_ms: Maximum waiting time for a new message, in milliseconds.
          For waiting without timeout, set the value to ``UXR_TIMEOUT_INF``

------

.. code-block:: c

    bool uxr_run_session_until_all_status(uxrSession* session, int timeout_ms, const uint16_t* request_list,
                                          uint8_t* status_list, size_t list_size);

This function processes the internal functionality of a session.
It implies:

1. Flushing all output streams sending the data through the transport.
2. If there is any reliable stream, it performs the associated reliable behaviour to ensure communication.
3. Listening to messages from the *Agent* and calling the associated callback if it exists (which can be a
   :ref:`status <status_callback>`/:ref:`topic <topic_callback>`/:ref:`time <time_callback>`/:ref:`request <request_callback>`
   or :ref:`reply <reply_callback>` callback)

The ``until_all_status`` suffix function version performs these actions during a ``timeout_ms`` duration
or until all requested statuses have been received.
The function returns ``true`` if all statuses have been received and all of them have the value ``UXR_STATUS_OK``
or ``UXR_STATUS_OK_MATCHED``, ``false`` otherwise.

:session: Session structure previously initialized and created.
:timeout_ms: Maximum waiting time for a new message, in milliseconds.
          For waiting without timeout, set the value to ``UXR_TIMEOUT_INF``
:request_list: An array of requests to confirm with a status.
:status_list: An uninitialized array with the same size as ``request_list`` where the status values are written.
              The position of each status in the `status_list` matches the corresponding request position in the ``request_list``.
:list_size: The size of the ``request_list`` and ``status_list`` arrays.

------

.. code-block:: c

    bool uxr_run_session_until_one_status(uxrSession* session, int timeout_ms, const uint16_t* request_list,
                                          uint8_t* status_list, size_t list_size);

This function processes the internal functionality of a session.
It implies:

1. Flushing all output streams sending the data through the transport.
2. If there is any reliable stream, it performs the associated reliable behaviour to ensure communication.
3. Listening to messages from the *Agent* and calling the associated callback if it exists (which can be a
   :ref:`status <status_callback>`/:ref:`topic <topic_callback>`/:ref:`time <time_callback>`/:ref:`request <request_callback>`
   or :ref:`reply <reply_callback>` callback)

The ``until_one_status`` suffix function version performs these actions during a ``timeout_ms`` duration
or until one requested status has been received.
The function returns ``true`` if one status has been received and has the value ``UXR_STATUS_OK`` or ``UXR_STATUS_OK_MATCHED``,
``false`` otherwise.

:session: Session structure previously initialized and created.
:timeout_ms: Maximum waiting time for a new message, in milliseconds.
          For waiting without timeout, set the value to ``UXR_TIMEOUT_INF``
:request_list: An array of requests to confirm with a status.
:status_list: An uninitialized array with the same size as ``request_list`` where the status values are written.
              The position of each status in the `status_list` matches the corresponding request position in the ``request_list``.
:list_size: The size of the ``request_list`` and ``status_list`` arrays.

------

.. code-block:: c

    bool uxr_run_session_until_data(uxrSession* session, int timeout_ms);

This function processes the internal functionality of a session.
It implies:

1. Flushing all output streams sending the data through the transport.
2. If there is any reliable stream, it operates according to the associated reliable behaviour to ensure communication.
3. Listening to messages from the *Agent* and calling the associated callback if it exists (which can be a
   :ref:`status <status_callback>`/:ref:`topic <topic_callback>`/:ref:`time <time_callback>`/:ref:`request <request_callback>`
   or :ref:`reply <reply_callback>` callback)

The ``until_data`` suffix function version performs these actions during a ``timeout_ms`` duration
or until a subscription data, request or reply is received.
The function returns ``true`` if a subscription data, request or reply is received, and ``false`` otherwise.

:session: Session structure previously initialized and created.
:timeout_ms: Maximum waiting time for a new message, in milliseconds.
          For waiting without timeout, set the value to ``UXR_TIMEOUT_INF``

------

.. code-block:: c

    bool uxr_sync_session(uxrSession* session, int time);

This function synchronizes the session time with the *Agent* using the NTP protocol by default.

:session: Session structure previously initialized and created.
:time: The waiting time in milliseconds.

------

.. code-block:: c

    int64_t uxr_epoch_millis(uxrSession* session);

This function returns the epoch time in milliseconds, taking into account the offset computed during the time synchronization.

:session: Session structure previously initialized.

------

.. code-block:: c

    int64_t uxr_epoch_nanos(uxrSession* session);

This function returns the epoch time in nanoseconds taking into account the offset computed during the time synchronization.

:session: Session structure previously initialized and created.

------

.. _entities_xml_api:

Create entities by XML
^^^^^^^^^^^^^^^^^^^^^^

These functions are enabled when the ``PROFILE_CREATE_ENTITIES_XML`` is activated as a CMake argument.
The declaration of these functions can be found in ``uxr/client/profile/session/create_entities_xml.h``.

------

.. code-block:: c

    uint16_t uxr_buffer_create_participant_xml(uxrSession* session, uxrStreamId stream_id, uxrObjectId object_id,
                                               uint16_t domain, const char* xml, uint8_t mode);

Creates a *participant* entity in the *Agent*.
The message is written into the stream buffer.
To send the message, it is necessary to call either the ``uxr_flash_output_streams`` or the ``uxr_run_session`` function.

:session: Session structure previously initialized and created.
:stream_id: The output stream ID where the messages are written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``UXR_PARTICIPANT_ID``.
:xml: An XML representation of the new entity.
:mode: Determines the creation entity mode.
        The Creation Mode Table describes the entities' creation behaviour according to the ``UXR_REUSE`` and ``UXR_REPLACE`` flags
        (see :ref:`creation_mode_table`).

------

.. code-block:: c

    uint16_t uxr_buffer_create_topic_xml(uxrSession* session, uxrStreamId stream_id, uxrObjectId object_id,
                                         uxrObjectId participant_id, const char* xml, uint8_t mode);

Creates a *topic* entity in the *Agent*.
The message is written into the stream buffer.
To send the message, it is necessary to call either the ``uxr_flash_output_streams`` or the ``uxr_run_session`` function.

:session: Session structure previously initialized and created.
:stream_id: The output stream ID where the messages are written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``UXR_TOPIC_ID``.
:participant_id: The identifier of the associated participant.
            The type must be ``UXR_PARTICIPANT_ID``.
:xml: An XML representation of the new entity.
:mode: Determines the creation entity mode.
        The Creation Mode Table describes the entities' creation behaviour according to the ``UXR_REUSE`` and ``UXR_REPLACE`` flags
        (see :ref:`creation_mode_table`).

------

.. code-block:: c

    uint16_t uxr_buffer_create_publisher_xml(uxrSession* session, uxrStreamId stream_id, uxrObjectId object_id,
                                             uxrObjectId participant_id, const char* xml, uint8_t mode);

Creates a *publisher* entity in the *Agent*.
The message is written into the stream buffer.
To send the message, it is necessary to call either the ``uxr_flash_output_streams`` or the ``uxr_run_session`` function.

:session: Session structure previously initialized and created.
:stream_id: The output stream ID where the messages are written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``UXR_PUBLISHER_ID``.
:participant_id: The identifier of the associated participant.
            The type must be ``UXR_PARTICIPANT_ID``.
:xml: An XML representation of the new entity.
:mode: Determines the creation entity mode.
        The Creation Mode Table describes the entities' creation behaviour according to the ``UXR_REUSE`` and ``UXR_REPLACE`` flags
        (see :ref:`creation_mode_table`).

------

.. code-block:: c

    uint16_t uxr_buffer_create_subscriber_xml(uxrSession* session, uxrStreamId stream_id, uxrObjectId object_id,
                                              uxrObjectId participant_id, const char* xml, uint8_t mode);

Creates a *subscriber* entity in the *Agent*.
The message is written into the stream buffer.
To send the message, it is necessary to call either the ``uxr_flash_output_streams`` or the ``uxr_run_session`` function.

:session: Session structure previously initialized and created.
:stream_id: The output stream ID where the messages are written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``UXR_SUBSCRIBER_ID``.
:participant_id: The identifier of the associated participant.
            The type must be ``UXR_PARTICIPANT_ID``.
:xml: An XML representation of the new entity.
:mode: Determines the creation entity mode.
        The Creation Mode Table describes the entities' creation behaviour according to the ``UXR_REUSE`` and ``UXR_REPLACE`` flags
        (see :ref:`creation_mode_table`).

------

.. code-block:: c

    uint16_t uxr_buffer_create_datawriter_xml(uxrSession* session, uxrStreamId stream_id, uxrObjectId object_id,
                                              uxrObjectId publisher_id, const char* xml, uint8_t mode);

Creates a *datawriter* entity in the *Agent*.
The message is written into the stream buffer.
To send the message, it is necessary to call either the ``uxr_flash_output_streams`` or the ``uxr_run_session`` function.

:session: Session structure previously initialized and created.
:stream_id: The output stream ID where the messages are written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``UXR_DATAWRITER_ID``.
:publisher_id: The identifier of the associated publisher.
            The type must be ``UXR_PUBLISHER_ID``.
:xml: An XML representation of the new entity.
:mode: Determines the creation entity mode.
        The Creation Mode Table describes the entities' creation behaviour according to the ``UXR_REUSE`` and ``UXR_REPLACE`` flags
        (see :ref:`creation_mode_table`).

------

.. code-block:: c

    uint16_t uxr_buffer_create_datareader_xml(uxrSession* session, uxrStreamId stream_id, uxrObjectId object_id,
                                              uxrObjectId subscriber_id, const char* xml, uint8_t mode);

Creates a *datareader* entity in the *Agent*.
The message is written into the stream buffer.
To send the message, it is necessary to call either the ``uxr_flash_output_streams`` or the ``uxr_run_session`` function.

:session: Session structure previously initialized and created.
:stream_id: The output stream ID where the messages are written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``UXR_DATAREADER_ID``.
:subscriber_id: The identifier of the associated subscriber.
            The type must be ``UXR_SUBSCRIBER_ID``.
:xml: An XML representation of the new entity.
:mode: Determines the creation entity mode.
        The Creation Mode Table describes the entities' creation behaviour according to the ``UXR_REUSE`` and ``UXR_REPLACE`` flags
        (see :ref:`creation_mode_table`).

------

.. code-block:: c

    uint16_t uxr_buffer_create_requester_xml(uxrSession* session, uxrStreamId stream_id, uxrObjectId object_id,
                                             uxrObjectId participant_id, const char* xml, uint8_t mode);

Creates a *requester* entity in the *Agent*.
The message is written into the stream buffer.
To send the message, it is necessary to call either the ``uxr_flash_output_streams`` or the ``uxr_run_session`` function.

:session: Session structure previously initialized and created.
:stream_id: The output stream ID where the messages are written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``UXR_REQUESTER_ID``.
:participant_id: The identifier of the associated participant.
            The type must be ``UXR_PARTICIPANT_ID``.
:xml: An XML representation of the new entity.
:mode: Determines the creation entity mode.
        The Creation Mode Table describes the entities' creation behaviour according to the ``UXR_REUSE`` and ``UXR_REPLACE`` flags
        (see :ref:`creation_mode_table`).

------

.. code-block:: c

    uint16_t uxr_buffer_create_replier_xml(uxrSession* session, uxrStreamId stream_id, uxrObjectId object_id,
                                           uxrObjectId participant_id, const char* xml, uint8_t mode);

Creates a *replier* entity in the *Agent*.
The message is written into the stream buffer.
To send the message, it is necessary to call either the ``uxr_flash_output_streams`` or the ``uxr_run_session`` function.

:session: Session structure previously initialized and created.
:stream_id: The output stream ID where the messages are written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``UXR_REPLIER_ID``.
:participant_id: The identifier of the associated participant.
            The type must be ``UXR_PARTICIPANT_ID``.
:xml: An XML representation of the new entity.
:mode: Determines the creation entity mode.
        The Creation Mode Table describes the entities' creation behaviour according to the ``UXR_REUSE`` and ``UXR_REPLACE`` flags
        (see :ref:`creation_mode_table`).

------

.. _entities_ref_api:

Create entities by reference
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

These functions are enabled when ``PROFILE_CREATE_ENTITIES_REF`` is activated as a CMake argument.
The declaration of these functions can be found in ``uxr/client/profile/session/create_entities_ref.h``.

------

.. code-block:: c

    uint16_t uxr_buffer_create_participant_ref(uxrSession* session, uxrStreamId stream_id, uxrObjectId object_id,
                                               const char* ref, uint8_t mode);

Creates a *participant* entity in the *Agent*.
The message is written into the stream buffer.
To send the message, it is necessary to call either the ``uxr_flash_output_streams`` or the ``uxr_run_session`` function.

:session: SSession structure previously initialized and created.
:stream_id: The output stream ID where the messages are written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``UXR_PARTICIPANT_ID``
:ref: A reference to the new entity.
:mode: Determines the creation entity mode.
        The Creation Mode Table describes the entities' creation behaviour according to the ``UXR_REUSE`` and ``UXR_REPLACE`` flags
        (see :ref:`creation_mode_table`).

------

.. code-block:: c

    uint16_t uxr_buffer_create_topic_ref(uxrSession* session, uxrStreamId stream_id, uxrObjectId object_id,
                                         uxrObjectId participant_id, const char* ref, uint8_t mode);

Creates a *topic* entity in the *Agent*.
The message is written into the stream buffer.
To send the message, it is necessary to call either the ``uxr_flash_output_streams`` or the ``uxr_run_session`` function.

:session: Session structure previously initialized and created.
:stream_id: The output stream ID where the messages are written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``UXR_TOPIC_ID``
:participant_id: The identifier of the associated participant.
            The type must be ``UXR_PARTICIPANT_ID``
:ref: A reference to the new entity.
:mode: Determines the creation entity mode.
        The Creation Mode Table describes the entities' creation behaviour according to the ``UXR_REUSE`` and ``UXR_REPLACE`` flags
        (see :ref:`creation_mode_table`).

------

.. code-block:: c

    uint16_t uxr_buffer_create_datawriter_ref(uxrSession* session, uxrStreamId stream_id, uxrObjectId object_id,
                                              uxrObjectId publisher_id, const char* ref, uint8_t mode);

Creates a *datawriter* entity in the *Agent*.
The message is written into the stream buffer.
To send the message, it is necessary to call either the ``uxr_flash_output_streams`` or the ``uxr_run_session`` function.

:session: Session structure previously initialized and created.
:stream_id: The output stream ID where the messages are written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``UXR_DATAWRITER_ID``
:publisher_id: The identifier of the associated publisher.
            The type must be ``UXR_PUBLISHER_ID``
:ref: A reference to the new entity.
:mode: Determines the creation entity mode.
        The Creation Mode Table describes the entities' creation behaviour according to the ``UXR_REUSE`` and ``UXR_REPLACE`` flags
        (see :ref:`creation_mode_table`).

------

.. code-block:: c

    uint16_t uxr_buffer_create_datareader_ref(uxrSession* session, uxrStreamId stream_id, uxrObjectId object_id,
                                              uxrObjectId subscriber_id, const char* ref, uint8_t mode);

Creates a *datareader* entity in the *Agent*.
The message is written into the stream buffer.
To send the message, it is necessary to call either the ``uxr_flash_output_streams`` or the ``uxr_run_session`` function.

:session: Session structure previously initialized and created.
:stream_id: The output stream ID where the messages are written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``UXR_DATAREADER_ID``.
:subscriber_id: The identifier of the associated subscriber.
            The type must be ``UXR_SUBSCRIBER_ID``.
:ref: A reference to the new entity.
:mode: Determines the creation entity mode.
        The Creation Mode Table describes the entities' creation behaviour according to the ``UXR_REUSE`` and ``UXR_REPLACE`` flags
        (see :ref:`creation_mode_table`).

------

.. code-block:: c

    uint16_t uxr_buffer_create_requester_ref(uxrSession* session, uxrStreamId stream_id, uxrObjectId object_id,
                                             uxrObjectId participant_id, const char* ref, uint8_t mode);

Creates a *requester* entity in the *Agent*.
The message is written into the stream buffer.
To send the message, it is necessary to call either the ``uxr_flash_output_streams`` or the ``uxr_run_session`` function.

:session: Session structure previously initialized and created.
:stream_id: The output stream ID where the messages are written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``UXR_REQUESTER_ID``.
:participant_id: The identifier of the associated participant.
            The type must be ``UXR_PARTICIPANT_ID``.
:ref: A reference to the new entity.
:mode: Determines the creation entity mode.
        The Creation Mode Table describes the entities' creation behaviour according to the ``UXR_REUSE`` and ``UXR_REPLACE`` flags
        (see :ref:`creation_mode_table`).

------

.. code-block:: c

    uint16_t uxr_buffer_create_replier_ref(uxrSession* session, uxrStreamId stream_id, uxrObjectId object_id,
                                           uxrObjectId participant_id, const char* ref, uint8_t mode);

Creates a *replier* entity in the *Agent*.
The message is written into the stream buffer.
To send the message, it is necessary to call either the ``uxr_flash_output_streams`` or the ``uxr_run_session`` function.

:session: Session structure previously initialized and created.
:stream_id: The output stream ID where the messages are written.
:object_id: The identifier of the new entity.
            Later, the entity can be referenced with this id.
            The type must be ``UXR_REPLIER_ID``.
:participant_id: The identifier of the associated participant.
            The type must be ``UXR_PARTICIPANT_ID``.
:ref: A reference to the new entity.
:mode: Determines the creation entity mode.
        The Creation Mode Table describes the entities' creation behaviour according to the ``UXR_REUSE`` and ``UXR_REPLACE`` flags
        (see :ref:`creation_mode_table`).

------

.. _entities_common_api:

Create entities common profile
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

These functions are enabled when either ``PROFILE_CREATE_ENTITIES_XML`` or ``PROFILE_CREATE_ENTITIES_REF`` are activated as CMake arguments.
The declaration of these functions can be found in ``uxr/client/profile/session/common_create_entities.h``.

------

.. code-block:: c

    uint16_t uxr_buffer_delete_entity(uxrSession* session, uxrStreamId stream_id, uxrObjectId object_id);

Removes an entity.
The message is written into the stream buffer.
To send the message, it is necessary to call either the ``uxr_flash_output_streams`` or the ``uxr_run_session`` function.

:session: Session structure previously initialized and created.
:stream_id: The output stream ID where the messages are written.
:object_id: The identifier of the object which is deleted.

------

.. _read_access_api:

Read access
^^^^^^^^^^^

The *Read Access* is used by the *Client* to handle the read operation on the *Agent*.
The declaration of these functions can be found in ``uxr/client/profile/session/read_access.h``.

------

.. code-block:: c

    uint16_t uxr_buffer_request_data(uxrSession* session, uxrStreamId stream_id, uxrObjectId datareader_id,
                                     uxrStreamId data_stream_id, const uxrDeliveryControl* const control);

This function requests a *datareader* previously created on the *Agent* to perform a read operation
that fetches data from the middleware.
The returned value is an identifier of the request.
All received topics have the same request identifier.
The topics are received on the callback topic through the ``run_session`` function.
If there is no error with the request data, a status callback with the value ``UXR_STATUS_OK`` is generated along with 
the topics retrieval.
If there is an error, a status error is sent by the *Agent*.
The message is written into the stream buffer.
To send the message, it is necessary to call either the ``uxr_flash_output_streams`` or the ``uxr_run_session`` function.

:session: Session structure previously initialized and created.
:stream_id: The output stream ID used to send messages to the *Agent*.
:datareader_id: The ID of the *datareader* that reads the topics from the middleware.
:data_stream_id: The input stream ID where the data is received.
:control: Optional information allowing the *Client* to configure the data delivery from the *Agent*.
          Details on the configurable parameters can be found in the :ref:`read_access` of the :ref:`micro_xrce_dds_client_label` page.
          A ``NULL`` value is accepted, in which case only one topic is received.

------

.. code-block:: c

    uint16_t uxr_buffer_cancel_data(uxrSession* session, uxrStreamId stream_id, uxrObjectId datareader_id);

This function requests a *datareader*, *requester* or *replier* previously created on the *Agent* to cancel the data received from the middleware.
It does so by resetting the ``delivery_control`` parameters and the input stream ID used to receive the data.
The returned value is an identifier of the request.

:session: Session structure previously initialized and created.
:stream_id: The output stream ID used to send messages to the *Agent*.
:datareader_id: The ID of the *datareader* that reads the topics from the middleware.

------

.. _write_access_api:

Write access profile
^^^^^^^^^^^^^^^^^^^^

The *Write Access* is used by the *Client* to handle the write operation on the *Agent*.
The declaration of these functions can be found in ``uxr/client/profile/session/write_access.h``.

------

.. code-block:: c

    bool uxr_prepare_output_stream(uxrSession* session, uxrStreamId stream_id, uxrObjectId datawriter_id,
                                   ucdrBuffer* ub, uint32_t topic_size);

This function requests a *datawriter* previously created on the *Agent* to perform a write operation
into a specific output stream.
It initializes a ``ucdrBuffer`` struct where a topic of ``topic_size`` size must be serialized.
If there is sufficient space for writing ``topic_size`` bytes into the stream, the returned value is ``true``, otherwise it is ``false``.
The topic is sent in the following ``run_session`` function.

.. note::
    All ``topic_size`` bytes requested are sent to the *Agent* after a ``run_session`` call,
    no matter if the ``ucdrBuffer`` has been used or not.

:session: Session structure previously initialized and created.
:stream_id: The output stream ID where the messages are written.
:datawriter_id: The ID of the *datawriter* that writes topics into the middleware.
:ub: The ``ucdrBuffer`` struct used to serialize the topic.
           This struct points to the requested memory slot in the stream.
:topic_size: The slot, in bytes, that is reserved in the stream.

------

.. code-block:: c

    bool uxr_buffer_request(uxrSession* session, uxrStreamId stream_id, uxrObjectId requester_id, uint8_t* buffer, size_t len);

This function buffers a request into a specific output stream.
The request is sent in the following ``run_session`` function call.

:session: Session structure previously initialized and created.
:stream_id: The output stream ID where the messages are written.
:requester_id: The ID of the *requester* that forwards the request to the middleware.
:buffer: The raw buffer that contains the serialized request.
:len: The size of the serialized request.

------

.. code-block:: c

    bool uxr_buffer_reply(uxrSession* session, uxrStreamId stream_id, uxrObjectId replier_id, uint8_t* buffer, size_t len);

This function buffers a reply into a specific output stream.
The request is sent in the following ``run_session`` function call.

:session: Session structure previously initialized and created.
:stream_id: The output stream ID where the messages are written.
:replier_id: The ID of the *replier* that writes the reply to the middleware.
:buffer: The raw buffer that contains the serialized reply.
:len: The size of the serialized reply.

------

.. code-block:: c

    bool uxr_prepare_output_stream_fragmented(uxrSession* session, uxrStreamId stream_id, uxrObjectId datawriter_id,
                                              struct ucdrBuffer* ub, size_t topic_size, uxrOnBuffersFull flush_callback);

This function requests a *datawriter* previously created on the *Agent* to allocate an output stream of ``topic_size`` bytes
for a write operation.
This function initializes an ``ucdrBuffer`` struct where a topic of ``topic_size`` size is serialized.
If there is sufficient space for writing ``topic_size`` bytes into the reliable stream, the returned value is ``true``, otherwise it is ``false``.
The topic is sent in the following ``run_session`` function. If, during the serialization process, the buffer gets overfilled, the
``flush_callback`` function is called and the user has to run a session for flushing the stream.

.. note::
    This approach is not valid for best-effort streams.

:session: Session structure previously initialized and created.
:stream_id: The output stream ID where the messages are written.
:datawriter_id: The ID of the *datawriter* that writes the topic to the middleware.
:ub: The ``ucdrBuffer`` struct used to serialize the topic.
           This struct points to the requested memory slot in the stream.
:topic_size: The slot, in bytes, that is reserved in the stream.
:flush_callback: Callback for flushing the output buffers.

The function signature for the ``flush_callback`` callback is:

.. code-block:: c

    typedef bool (*uxrOnBuffersFull) (struct uxrSession* session);

:session: Session structure related to the buffer to be flushed.

------

.. _discovery_api:

Discovery profile
^^^^^^^^^^^^^^^^^

The discovery profile allows discovering *Agents* in the network by UDP.
The reachable *Agents* respond to the discovery call by sending information about themselves, as their IP and port.
There are two modes: unicast and multicast.
The discovery phase precedes the call to the ``uxr_create_session`` function, as it serves to determine the
*Agent* to connect with.
These functions are enabled when ``PROFILE_DISCOVERY`` is activated as a CMake argument.
The declaration of these functions can be found in ``uxr/client/profile/discovery/discovery.h``.

.. note::
    This feature is only available on Linux.

------

.. code-block:: c

    bool uxr_discovery_agents_default(uint32_t attempts, int period, uxrOnAgentFound on_agent_func, void* args);

This function looks for *Agents* in the network using UDP/IP multicast with address "239.255.0.2" and port 7400
(which is the default used by the *Agent*).

:attempts: The times a discovery message is sent across the network.
:period: The wait time between two successive discovery calls.
:on_agent_func: The callback function that is called when an *Agent* is discovered.
                It returns a boolean value. A ``true`` means that the discovery routine has finished.
                A ``false`` implies that the discovery routine must continue searching *Agents*.
:args: User arguments passed to the callback function.

The function signature for the ``on_agent_func`` callback is:

.. code-block:: c

    typedef bool (*uxrOnAgentFound) (const TransportLocator* locator, void* args);

:locator: Transport locator of a discovered agent.
:args: User pointer data.

------

.. code-block:: c

    bool uxr_discovery_agents(uint32_t attempts, int period, uxrOnAgentFound on_agent_func, void* args,
                              const TransportLocator* agent_list, size_t agent_list_size);

This function looks for *Agents* in the network using UDP/IP unicast,
using a list of unicast directions with the addresses and ports set by the user.

:attempts: The times a discovery message is sent across the network.
:period: The wait time between two successive discovery calls.
:on_agent_func: The callback function that is called when an *Agent* is discovered.
                It returns a boolean value. A ``true`` means that the discovery routine has finished.
                A ``false`` implies that the discovery routine must continue searching *Agents*.
:args: User arguments passed to the callback function.
:agent_list: The list of addresses used for discovering *Agents*.
:agent_list_size: The size of the ``agent_list``.

The function signature for the ``on_time_func`` callback is the same as above.

------

.. _topic_serialization_api:

Topic serialization
^^^^^^^^^^^^^^^^^^^

Functions to serialize and deserialize topics.
These functions are generated automatically by the *eProsima Micro XRCE-DDS Gen* utility fed with an IDL file with a topic ``TOPICTYPE``.
The declaration of these functions can be found in the generated file ``TOPICTYPE.h``.

------

.. code-block:: c

    bool TOPICTYPE_serialize_topic(struct ucdrBuffer* writer, const TOPICTYPE* topic);

This function serializes a topic into an ``ucdrBuffer``.
The returned value indicates if the serialization was successful.

:writer: The ``ucdrBuffer`` representing the buffer for the serialization.
:topic: Struct to serialize.

------

.. code-block:: c

    bool TOPICTYPE_deserialize_topic(struct ucdrBuffer* reader, TOPICTYPE* topic);

This function deserializes a topic from an ``ucdrBuffer``.
The returned value indicates if the deserialization was successful.

:reader: An ``ucdrBuffer`` representing the buffer for the deserialization.
:topic: Struct to deserialize.

------

.. code-block:: c

    uint32_t TOPICTYPE_size_of_topic(const TOPICTYPE* topic, uint32_t size);

This function counts the number of bytes that the topic needs in an `ucdrBuffer`.

:topic: Struct to count the size.
:size: Number of bytes already written into the `ucdrBuffer`.
       Typically, its value is `0` if the purpose is to use in ``uxr_prepare_output_stream`` function.

------

.. _general_utilities_api:

General utilities
^^^^^^^^^^^^^^^^^

Utility functions.
The declaration of these functions can be found in ``uxr/client/core/session/stream_id.h`` and ``uxr/client/core/session/object_id.h``.

------

.. code-block:: c

    uxrStreamId uxr_stream_id(uint8_t index, uxrStreamType type, uxrStreamDirection direction);

This function creates a stream identifier.
This function does not create a new stream, it only creates its identifier to be used in the *Client* API.

:index: Identifier of the stream. Its value corresponds to the creation order of the stream, different for each ``type``.
:type: The type of the stream, it can be ``UXR_BEST_EFFORT_STREAM`` or ``UXR_RELIABLE_STREAM``.
:direction: Represents the direction (input or output) of the stream. It can be ``UXR_INPUT_STREAM`` or ``UXR_OUTPUT_STREAM``.

------

.. code-block:: c

    uxrStreamId uxr_stream_id_from_raw(uint8_t stream_id_raw, uxrStreamDirection direction);

This function creates a stream identifier.
This function does not create a new stream, it only creates its identifier to be used in the *Client* API.

:stream_id_raw: Identifier of the stream. It goes from **0** to **255**. **0** is for internal library use. **1** to **127** are for best effort.
                **128** to **255** are for reliable.
:direction: Represents the direction (input or output) of the stream. It can be ``UXR_INPUT_STREAM`` or ``MT_OUTPUT_STREAM``.

------

.. code-block:: c

    uxrObjectId uxr_object_id(uint16_t id, uint8_t type);

This function creates an identifier to reference an entity.

:id: Identifier of the object, different for each ``type``. There can be several objects with the same ID,
     provided they have different types.
:type: The type of the entity.
       It can be: ``UXR_PARTICIPANT_ID``, ``UXR_TOPIC_ID``, ``UXR_PUBLISHER_ID``,
       ``UXR_SUBSCRIBER_ID``, ``UXR_DATAWRITER_ID``, ``UXR_DATAREADER_ID``,
       ``UXR_REQUESTER_ID``, or ``UXR_REPLIER_ID``.

------

.. code-block:: c

    bool uxr_ping_agent(const uxrCommunication* comm, const int timeout);

[TODO]

:comm: [TODO]
:timeout: [TODO]

------

.. code-block:: c

    bool uxr_ping_agent_attempts(const uxrCommunication* comm, const int timeout, const uint8_t attempts);

[TODO]

:comm: [TODO]
:timeout: [TODO]
:attempts: [TODO]

------

.. _transport_api:

Transport
^^^^^^^^^

These functions are platform-dependent.
The declaration of these functions can be found in the ``uxr/client/profile/transport/`` folder.
The common init transport functions follow the nomenclature below.

------

.. code-block:: c

    bool uxr_init_udp_transport(uxrUDPTransport* transport, uxrIpProtocol ip_protocol, const char* ip, const char* port);

This function initializes a UDP connection.

:transport: The uninitialized structure used for managing the transport.
            This structure must be accessible during the connection.
:ip_protocol: IPv4 or IPv6.
:ip: *Agent* IP.
:port: *Agent* port.

------

.. code-block:: c

    bool uxr_init_tcp_transport(uxrTCPTransport* transport, uxrIpProtocol ip_protocol, const char* ip, const char* port);

This function initializes a TCP connection.
In the case of TCP, the behaviour of best-effort streams is similar to that of reliable streams in UDP.

:transport: The uninitialized structure used for managing the transport.
            This structure must be accessible during the connection.
:ip_protocol: IPv4 or IPv6.
:ip: *Agent* IP.
:port: *Agent* port.

------

.. code-block:: c

    bool uxr_init_serial_transport(uxrSerialTransport* transport, const int fd, uint8_t remote_addr, uint8_t local_addr);

This function initializes a Serial connection using a file descriptor.

:transport: The uninitialized structure used for managing the transport.
            This structure must be accessible during the connection.
:fd: File descriptor of the serial connection. Usually, the ``fd`` comes from the ``open`` OS function.
:remote_addr: Identifier of the *Agent* in the connection.
              By default, the *Agent* identifier in a seria connection is **0**.
:local_addr: Identifier of the *Client* in the serial connection.

------

.. code-block:: c

    bool uxr_init_custom_transport(uxrCustomTransport* transport, void * args);

This function initializes a Custom connection using user-defined arguments.

:transport: The uninitialized structure used for managing the transport.
            This structure must be accessible during the connection.
            ``args`` is accesible as ``transport->args``.

------

.. code-block:: c

    bool uxr_close_PROTOCOL_transport(PROTOCOLTransport* transport);

This function closes a transport previously opened. ``PROTOCOL`` can be ``udp``, ``tcp``, ``serial`` or ``custom``.

:transport: The structure used for managing the transport that must be closed.

------

.. _custom_transport_callbacks:

.. code-block:: c

    void uxr_set_custom_transport_callbacks(uxrCustomTransport* transport, bool framing, open_custom_func open,
                               close_custom_func close, write_custom_func write, read_custom_func read);

This function assigns the callback for custom transport.

:transport: The uninitialized structure used for managing the transport.
            This structure must be accessible during the connection.
:framing: Enables or disables Stream Framing Protocol for a custom transport.
:open: Callback for opening a custom transport.
:close: Callback for closing a custom transport.
:write: Callback for writing to a custom transport.
:read: Callback for reading from a custom transport.

The function signatures for the above callbacks are:

.. code-block:: c

    typedef bool (*open_custom_func) (struct uxrCustomTransport* transport);

:transport: Custom transport structure. Has the ``args`` passed through ``bool uxr_init_custom_transport(uxrCustomTransport* transport, void * args);``.

.. code-block:: c

    typedef bool (*close_custom_func) (struct uxrCustomTransport* transport);

:transport: Custom transport structure. Has the ``args`` passed through ``bool uxr_init_custom_transport(uxrCustomTransport* transport, void * args);``.

.. code-block:: c

    typedef size_t (*write_custom_func) (struct uxrCustomTransport* transport, const uint8_t* buffer, size_t length, uint8_t* error_code);

:transport: Custom transport structure. Has the ``args`` passed through ``bool uxr_init_custom_transport(uxrCustomTransport* transport, void * args);``.
:buffer: Buffer to be sent.
:length: Length of buffer.
:error_code: Error code that should be set in case the write process experiences some error.

This function should return the number of successfully sent bytes.

.. code-block:: c

    typedef size_t (*read_custom_func) (struct uxrCustomTransport* transport, uint8_t* buffer, size_t length, int timeout, uint8_t* error_code);

:transport: Custom transport structure. Has the ``args`` passed through ``bool uxr_init_custom_transport(uxrCustomTransport* transport, void * args);``.
:buffer: Buffer to write.
:length: Maximum length of buffer.
:timeout: Maximum timeout of the read operation.
:error_code: Error code that should be set in case the write process experiences some error.

This function should return the number of successfully received bytes.
