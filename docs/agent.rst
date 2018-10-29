.. _micro_xrce_dds_agent_label:

Micro XRCE-DDS Agent
====================

*Micro XRCE-DDS Agent* acts as a server between the DDS Network and *Micro XRCE-DDS Clients* applications.
*Agents* receive messages containing operations from *Clients*.
Also *Agents* keep track of the *Clients* and the entities they create.
The *Agent* uses the entities to interact with the DDS Global Data Space on behalf of the *Clients*.

The communication between a *Client* and an *Agent* currently supports UDP, TCP and Serial (dependent on the platform).
While it is running, the *Agent* will attend any received request from the *Clients* and answers back with the result of that request.

Configuration
-------------

There are several configuration parameters which can be set at **compile time** in order to configure the *Micro RTPS Agent*.
These parameters can be selected as CMake flags (``-D<parameter>=<value>``) before the compilation.
The following is a list of the aforementioned parameters:

``CONFIG_RELIABLE_STREAM_DEPTH``
    Specify the history of the reliable streams (default 16).

``CONFIG_BEST_EFFORT_STREAM_DEPTH``
    Specify the history of the best-effort streams (default 16).

``CONFIG_HEARTBEAT_PERIOD``
    Specify the ``HEARTBEAT`` message period in millisecond (default 200).

``CONFIG_SERIAL_TRANSPORT_MTU``
    Specify the `Maximum Transmission Unit` able to send and receive by Serial (default 512).

``CONFIG_UDP_TRANSPORT_MTU``
    Specify the `Maximum Transmission Unit` able to send and receive by UDP (default 512).

``CONFIG_TCP_TRANSPORT_MTU``
    Specify the `Maximum Transmission Unit` able to send and receive by TCP (default 512).

``CONFIG_TCP_MAX_CONNECTIONS``
    Specify the maximum number of connections, the *Agent* is able to manage (default 100).

``CONFIG_TCP_MAX_BACKLOG_CONNECTIONS``
    Specify the maximum number of incoming connections (peding to establish), the *Agent* is able to manage (default 100).



Run an Agent
------------

To run the *Agent* you should build it as indicated in :ref:`installation_label`.
Once it is built successfully you just need to launch it executing the following command.

For Serial communication: ::

    $ ./MicroXRCEAgent serial <device>

For UDP communication: ::

    $ ./MicroXRCEAgent udp <port> [<discovery-port>]

For TCP communication: ::

    $ ./MicroXRCEAgent tcp <port> [<discovery-port>]

If the transport used is a reliable transport, the use of a best-effort stream over it is equivalent to use a reliable stream over a not reliable transport.

For running the *Agent* is necessary that the file ``DEFAULT_FASTRTPS_PROFILES.xml`` is located in the runtime folder.
This file contains XML profiles of `Participants`, `Topic`, `DataWriters` and `DataReaders` referenced by a ``profile_name``.
The *Client* can use the aforementioned ``profile_name`` in order to create these entities using the reference representation.
Let's see an example.

The ``DEFAULT_FASTRTPS_PROFILES.xml`` contains the following participant profile:

.. code-block:: xml

    <profiles>
      <participant profile_name="default_xrce_participant_profile">
        <rtps>
          <name>default_participant</name>
        </rtps>
      </participant>
    </profile>

therefore, the *Client* can use `default_xrce_participant_profile` as ``ref`` in the ``mr_buffer_create_participant_ref`` function.

Default installation place this file along with the *Agent* executable into ``/usr/local/bin`` for Linux or ``C:/Program Files/microxrcedds_agent/bin`` for Windows.


