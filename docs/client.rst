.. |br| raw:: html

  <br/>

.. _micro_xrce_dds_client_label:

eProsima Micro XRCE-DDS Client
==============================

In *eProsima Micro XRCE-DDS*, a *Client* can communicate with the DDS Network as any other DDS actor could do.
*Clients* can either publish and subscribe to data Topics in the DDS Global Data Space, or act as a client/service
application following a request-reply pattern.

This section explains how the *Client-Agent* communication happens through streams that can be either best-effort or reliable.
After this, it is explained how users can configure *Clients* applications and the communication with the *Agent*
via sets of CMake flags (:code:`-D<parameter>=<value>`) that enable/disable profiles and/or allow customize
the size of several parameters.
Finally, a table is presented to explain the creation mode options of the *Clients* on the *Agent*'s side.
The section is organized as follows:

- :ref:`streams`
- :ref:`profiles`
- :ref:`configurations`
- :ref:`read_access`
- :ref:`creation_mode_client`
- :ref:`creation_policy_table`

*eProsima Micro XRCE-DDS* provides the user with a C API to create *eProsima Micro XRCE-DDS Clients* applications.
Find the full Client API in the :ref:`dedicated page <client_api_label>`.

.. _streams:

Streams
-------

The *Client*-*Agent* communication is performed by streams. The streams can be seen as communication channels.
There are two types of streams: best-effort and reliable.
The user can define a maximum of 127 best-effort streams and 128 reliable streams, but for the majority of purposes,
only one stream in either best-effort or reliable mode is used.

Best-effort streams
    Best-effort streams send and receive the data leaving the reliability to the transport layer.
    As a result, they consume fewer resources than reliable streams.
    Also, no history is stored and so the message size sent or received by a best-effort stream must be less or equal
    than the *MTU* defined in the transport layer.

Reliable streams
    Reliable streams perform the communication without loss, regardless of the transport layer used,
    and allow for message fragmentation to send and receive messages longer than the *MTU*.

    To avoid loss of data, reliable streams use additional messages to confirm the delivery.
    Moreover, reliable streams have a history associated, used to store messages that can not be processed
    due to issues such as delivery order or incomplete fragments or messages that can not be confirmed yet.
    The size of the history can be tailored to fit the specific requirements of the application.
    The size of the stream corresponds to the *MTU* defined in the transport layer times the history.

    If the history is full:

    * The messages written to the *Agent* will be discarded until the history is freed and has space to
      store the new messages.
    * The messages received from the agent will be discarded.
      The library will try to recover the discarded messages requesting them to the agent
      (increasing the bandwidth consumption in the process).

    Summarizing:

    * A short history causes more messages to be discarded, increasing the data traffic because they need to be sent again.
      At the same time, it consumes less memory.
    * A long history will reduce the traffic of confirmation messages when the loss rate is high.

    This internal management of the communication implies that a reliable stream is more expensive than a best-effort
    stream, in both memory and bandwidth, but it is possible to play with these values using the history size.

The streams are probably the highest memory load part of the application.
For that, the choice of a right configuration for the application is highly recommendable, especially when the target is a
limited resource device. The :ref:`optimization_label` page explains more in detail how to achieve this.

.. _profiles:

Profiles
--------

The *Client* library follows a profile concept that enables to choose, add or remove some features at **compile-time**,
thus allowing to customize the *Client* library size, if there are features that are not used.

The profiles can be chosen using CMake arguments and start with the prefix :code:`UCLIENT_PROFILE`
(:code:`-D<parameter>=<value>`) before the compilation.

By means of these profiles, the user can choose which transport to use, and whether to enable or not the
discovery and framing functionalities.

.. list-table::
    :header-rows: 1

    *   - Definition
        - Description
        - Values
        - Default
    *   - :code:`UCLIENT_PROFILE_UDP`
        - Enables or disables the possibility to connect with the *Agent* by UDP.
        - :code:`<bool>`
        - :code:`ON`
    *   - :code:`UCLIENT_PROFILE_TCP`
        - Enables or disables the possibility to connect with the *Agent* by TCP.
        - :code:`<bool>`
        - :code:`ON`
    *   - :code:`UCLIENT_PROFILE_SERIAL`
        - Enables or disables the possibility to connect with the *Agent* by Serial.
        - :code:`<bool>`
        - :code:`ON`
    *   - :code:`UCLIENT_PROFILE_CAN`
        - Enables or disables the possibility to connect with the *Agent* by CAN FD.
        - :code:`<bool>`
        - :code:`OFF`
    *   - :code:`UCLIENT_PROFILE_CUSTOM_TRANSPORT`
        - Enables or disables the possibility to connect with the *Agent* by Custom Transport.
        - :code:`<bool>`
        - :code:`ON`
    *   - :code:`UCLIENT_PROFILE_DISCOVERY`
        - Enables or disables the functions of the discovery feature |br|
          (currently, only for POSIX).
        - :code:`<bool>`
        - :code:`ON`
    *   - :code:`UCLIENT_PROFILE_STREAM_FRAMING`
        - Enables or disables the stream framing protocol.
        - :code:`<bool>`
        - :code:`ON`
    *   - :code:`UCLIENT_PROFILE_MULTITHREAD`
        - Enables or disables the multithread locking operation of the library.
        - :code:`<bool>`
        - :code:`ON`
    *   - :code:`UCLIENT_PROFILE_SHARED_MEMORY`
        - Enables or disables a basic local memory transport operation between entities in the same application.
        - :code:`<bool>`
        - :code:`ON`

Transport profiles
^^^^^^^^^^^^^^^^^^

The implementation of the transport depends on the platform.
As mentioned in the :ref:`Introductory page <microxrcedds_doc>`, the *Client* is supported by the following platforms:
Linux, Windows, FreeRTOS, Zephyr and NuttX. Linux and all three RTOSes present a POSIX-compliant API to some degree.
Find below a table summarizing the compatibility of each these Operating Systems, according to their POSIX compliance,
with the transports supported by the *eProsima Micro XRCE-DDS Client*.

The table below shows the current implementation.

============ ========== =========
Transport     POSIX      Windows
============ ========== =========
UDP           X           X
TCP           X           X
Serial        X
CAN FD        X
Custom        X           X
============ ========== =========

Each available transport can be activated or desactivated via the opportune CMake flag:
:code:`UCLIENT_PROFILE_<transport>`, where :code:`<transport> = UDP, TCP, SERIAL, CAN`, or
:code:`UCLIENT_PROFILE_CUSTOM_TRANSPORT` in the case Custom transport is to be used.

*eProsima Micro XRCE-DDS* provides a user API that allows interfacing with the lowest level transport layer at runtime. In this way, a user is enabled to implement its own transports based on one of the two communication approaches: stream-oriented or packet-oriented.
By means of this API, a user can set four callbacks which will be in charge of opening and closing the transport, and writing and reading from it. This custom transport API is enabled by setting the CMake argument ``UCLIENT_PROFILE_CUSTOM_TRANSPORT=<bool>`` to true. In the case that stream-oriented transport is used ``UCLIENT_PROFILE_STREAM_FRAMING=<bool>`` should also be enabled.

Find out more in the :ref:`transport_api` section of the :ref:`client_api_label`.

Discovery profile
^^^^^^^^^^^^^^^^^

The discovery profile allows discovering *Agents* in the network by UDP.
The reachable *Agents* will respond to the discovery call sending information about themselves, as their IP and port.
This can happen in two ways: multicast or unicast.
The discovery phase can be performed before the `uxr_create_session` call to determine the *Agent* to connect with.
The declaration of these functions can be found in ``uxr/client/profile/discovery/discovery.h``.
This profile is enabled when the :code:`UCLIENT_DISCOVERY_PROFILE` is :code:`ON`.

Find out more in the :ref:`dedicated section <discovery_api>` of the API.

.. note::
    This feature is only available on Linux.

Framing profile
^^^^^^^^^^^^^^^

The framing profile enables :ref:`HDLC Framing <stream_framing_label>` for using :ref:`stream-oriented transports <intro_transport>` such as Serial transports or Custom transports that require framing.

Multithread profile
^^^^^^^^^^^^^^^^^^^

The multithread profile enables the thread-safe operation with the Micro XRCE-DDS Client library. It lockguards all the critical sections of the API and allows the usage from concurrent tasks.

Shared memory profile
^^^^^^^^^^^^^^^^^^^^^

The multithread profile enables a simple intraprocess communication. This profile is intended to be used whithin devices without memory protection units where all tasks or processes have access to the whole memory space.


.. _configurations:

Configurations
--------------

There are several definitions for configuring and building the *Client* library at **compile-time**.
These definitions allow users to create a version of the library according to their requirements.
These parameters can be selected as CMake flags (:code:`-D<parameter>=<value>`) before the compilation.
By means of these flags, the user can change the default value of all the parameters listed below.

.. list-table::
    :header-rows: 1

    *   - Definition
        - Description
        - Values
        - Default
    *   - :code:`UCLIENT_MAX_OUTPUT_BEST_EFFORT_STREAMS`
        - Configures the maximum output best-effort streams that a session could |br|
          have. The calls to the :code:`uxr_create_output_best_effort_stream` function |br|
          for a session must be less than or equal to this value.
        - :code:`<number>`
        - :code:`1`
    *   - :code:`UCLIENT_MAX_OUTPUT_RELIABLE_STREAMS`
        - Configures the maximum output reliable streams that a session could have. |br|
          The calls to the :code:`uxr_create_output_realiable_stream` function for a |br|
          session must be less than or equal to this value.
        - :code:`<number>`
        - :code:`1`
    *   - :code:`UCLIENT_MAX_INPUT_BEST_EFFORT_STREAMS`
        - Configures the maximum input best-effort streams that a session could |br|
          have. The calls to the :code:`uxr_create_input_best_effort_stream` function |br|
          for a session must be less than or equal to this value.
        - :code:`<number>`
        - :code:`1`
    *   - :code:`UCLIENT_MAX_INPUT_RELIABLE_STREAMS`
        - Configures the maximum input reliable streams that a session could have. |br|
          The calls to the :code:`uxr_create_input_realiable_stream` function for a |br|
          session must be less than or equal to this value.
        - :code:`<number>`
        - :code:`1`
    *   - :code:`UCLIENT_MAX_SESSION_CONNECTION_ATTEMPTS`
        - This value indicates the number of attempts that :code:`create_session` and |br|
          :code:`delete_session` will perform until receiving a status message.
        - :code:`<number>`
        - :code:`10`
    *   - :code:`UCLIENT_MIN_SESSION_CONNECTION_INTERVAL`
        - This value represents how long it will take to send a new :code:`create_session` |br|
          or :code:`delete_session` if the first attempt was left answered.
        - :code:`<number>`
        - :code:`1000`
    *   - :code:`UCLIENT_MIN_HEARTBEAT_TIME_INTERVAL`
        - In a reliable communication, this value represents how long it will take for |br|
          the first heartbeat to be sent. The wait time for the next heartbeat will be |br|
          double. It is measured in milliseconds.
        - :code:`<number>`
        - :code:`100`
    *   - :code:`UCLIENT_BIG_ENDIANNESS`
        - This value must correspond to the memory endianness of the device in |br|
          which the *Client* is running. :code:`OFF` implies that the machine is little-endian |br|
          and :code:`ON` implies big-endian.
        - :code:`<bool>`
        - :code:`OFF`
    *   - :code:`UCLIENT_UDP_TRANSPORT_MTU`
        - This value corresponds to the *Maximum Transmission Unit (MTU)* that can |br|
          be sent and/or received by UDP. It is measured in bytes and, internally, it |br|
          corresponds to the creation of a buffer this size.
        - :code:`<number>`
        - :code:`512`
    *   - :code:`UCLIENT_TCP_TRANSPORT_MTU`
        - This value corresponds to the *Maximum Transmission Unit (MTU)* that can |br|
          be sent and/or received by TCP. It is measured in bytes and, internally, it |br|
          corresponds to the creation of a buffer this size.
        - :code:`<number>`
        - :code:`512`
    *   - :code:`UCLIENT_SERIAL_TRANSPORT_MTU`
        - This value corresponds to the *Maximum Transmission Unit (MTU)* that can |br|
          be sent and/or received by Serial. It is measured in bytes and, internally, it |br|
          corresponds to the creation of a buffer this size.
        - :code:`<number>`
        - :code:`512`
    *   - :code:`UCLIENT_CUSTOM_TRANSPORT_MTU`
        - This value corresponds to the *Maximum Transmission Unit (MTU)* that can |br|
          be sent and/or received by Custom transport. It is measured in bytes and, |br|
          internally, it corresponds to the creation of a buffer this size.
        - :code:`<number>`
        - :code:`512`
    *   - :code:`UCLIENT_SHARED_MEMORY_MAX_ENTITIES`
        - This value corresponds to the *Max number of entities involved in shared memory*.
        - :code:`<number>`
        - :code:`4`
    *   - :code:`UCLIENT_SHARED_MEMORY_STATIC_MEM_SIZE`
        - This value corresponds to the *Max number data buffers stored in shared memory*.
        - :code:`<number>`
        - :code:`10`
    *   - :code:`UCLIENT_HARD_LIVELINESS_CHECK`
        - Enables Micro XRCE-DDS Client hard liveliness check.
        - :code:`<bool>`
        - :code:`OFF`
    *   - :code:`UCLIENT_HARD_LIVELINESS_CHECK_TIMEOUT`
        - Sets Micro XRCE-DDS Client hard liveliness check timeout in milliseconds. Maximum value is 999999 ms.
        - :code:`<number>`
        - :code:`10000`

.. note::
    The MTU of the CAN transport is fixed to 64 bytes, which is the maximum payload supported by CAN FD frames.
    Take this into account to calculate the size of the streams for the requirements of the application.

.. _read_access:

Read Access Delivery Control
----------------------------

The Read Access Delivery Control handles the read operation from a *datareader* previously created
on the *Agent* to fetch data from the middleware.
It comes with an optional ``control`` argument, that allows the *Client* setting the following parameters:

* ``max_bytes_per_second``: Maximum rate at which data messages may be returned, measured in bytes per secpond.
* ``max_elapsed_time``: Maximum amount of time that can be spent by the Agent in delivering the topic, measured in seconds.
* ``max_samples``: Maximum number of topics that the Agent can send to the Client.
* ``min_pace_period``: Minimum elapsed time between two topics deliveries, measured in milliseconds,.

For more information, consult the :ref:`read_access_api` of the :ref:`client_api_label`.

.. _creation_mode_client:

Creation Mode: Client
---------------------

The creation of :ref:`entities_label` on the *Agent* which can act on behalf of the *Clients*
in the DDS world can be done in three ways: by XML, by reference or by binary. In this section, we explain
these three creation modes and provide guidance on their usage.

XML
    In the XML case, when creating the entities in the *Client* application, the user must provide each :code:`entity`
    with a `const char* <entity>_xml` parameter containing a string of text with XML syntax, matching the DDS rules for creating
    a DDS entity with an XML profile, as explained
    `here <https://fast-dds.docs.eprosima.com/en/latest/fastdds/xml_configuration/xml_configuration.html>`_.

    For instance, when creating a *participant* or a *topic*, the profiles shall look as follows:

    .. code-block:: C

        <!-- PARTICIPANT -->
        const char* participant_xml = "<dds>"
                                          "<participant>"
                                              "<rtps>"
                                                  "<name>[PARTICIPANT NAME]</name>"
                                              "</rtps>"
                                          "</participant>"
                                      "</dds>";

        <!-- TOPIC -->
        const char* topic_xml = "<dds>"
                                    "<topic>"
                                        "<name>[TOPIC NAME]</name>"
                                        "<dataType>[TOPIC TYPE]</dataType>"
                                    "</topic>"
                                "</dds>"

    As detailed in the :ref:`getting_started_label` section, *participants*, *topics*, *datawriters*, *datareaders*, *requesters* and *repliers* work similarly.
    *Publishers* and *subscribers*, instead, inherit their XML fields from their associated *dataWriters* and *dataReaders*.

    Creation by XML has the advantage of being configurable direclty within the *Client* application,
    but comes with the drawback of offering a very limited set of options as regards the QoS with which the DDS entities
    profiles can be configured. Indeed, only best-effort or reliable communication streams can be set with this creation mode.
    In many cases, these QoS configurations alone may not be enough. For these cases, *eProsima Micro XRCE-DDS* allows the users
    to use the creation by references mode.

References
    Creation by references happens by feeding the *Agent* with an XML profile containing a string of text similar to the snippets
    provided above, with a label associated to it. Therefore, when creating an entity, the *Client* will only need to provide a reference
    to this label in spite of the complete XML profile. This creation mode comes with two advantages:

    - It consumes less *Client* memory, making the application more lightweight.
    - It allows the *Clients* to write their own XML QoS and run the *Agent* with a custom configuration which can benefit of the *full set* of QoS available in DDS.

    For instance, when creating a *participant* or a *topic*, the profiles shall look as follows:

    .. code-block:: C

        <!-- PARTICIPANT -->
        const char* participant_ref = "participant_label";

        <!-- TOPIC -->
        const char* topic_ref = "topic_label"

Binary

    Creation by binary provides a comprehensive API in the Micro XRCE-DDS Client library that can be used to generate and send over the
    XRCE-DDS middleware binary representations of the entities that are being created. This creation mode comes with two advantages:

    - It consumes less *Client* memory than XML mode, making the application more lightweight.
    - It provides much more flexibility than the REF mode in the client side.

    For instance, when creating a *participant* or a *topic*, the profiles shall look as follows:

    .. code-block:: C

      uxrQoS_t qos = {
        .reliability = UXR_RELIABILITY_RELIABLE, .durability = UXR_DURABILITY_TRANSIENT_LOCAL,
        .history = UXR_HISTORY_KEEP_LAST, .depth = 0
      };
      uxr_buffer_create_topic_bin(&session, reliable_out, topic_id, participant_id, "ExampleTopic", "ExampleType", UXR_REPLACE);
      uxr_buffer_create_datawriter_bin(&session, reliable_out, datawriter_id, publisher_id, topic_id, qos, UXR_REPLACE);

Find more information in the :ref:`creation_mode_agent` section in the :ref:`micro_xrce_dds_agent_label` page.


.. _creation_policy_table:

Creation Policy Table
---------------------

The following table summarizes the behaviour of the *Agent* under entity creation request.

=========================== ================= ==========
**Creation flags**          **Entity exists** **Result**
=========================== ================= ==========
Don't care                  NO                Entity is created.
``0``                       YES               No action is taken, and ``UXR_STATUS_ERR_ALREADY_EXITS`` is returned.
``UXR_REPLACE``             YES               Existing entity is deleted, requested entity is created and ``UXR_STATUS_OK`` is returned.
``UXR_REUSE``               YES               | If entity matches no action is taken and ``UXR_STATUS_OK_MATCHED`` is returned.
                                              | If entity does not match any action is taken and ``UXR_STATUS_ERR_MISMATCH`` is returned.
``UXR_REUSE | UXR_REPLACE`` YES               | If entity matches no action is taken and ``UXR_STATUS_OK_MATCHED`` is returned.
                                              | If entity does not match, exiting entity is deleted, requested entity is created and ``UXR_STATUS_OK`` |br| is returned.
=========================== ================= ==========
