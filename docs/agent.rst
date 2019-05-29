.. _micro_xrce_dds_agent_label:

eProsima Micro XRCE-DDS Agent
=============================

*eProsima Micro XRCE-DDS Agent* acts as a server between the DDS Network and *eProsima Micro XRCE-DDS Clients* applications.
*Agents* receive messages containing operations from *Clients*.
Also, *Agents* keep track of the *Clients* and the entities they create.
The *Agent* uses the entities to interact with the DDS Global Data Space on behalf of the *Clients*.

The communication between a *Client* and an *Agent* currently supports UDP, TCP and Serial (dependent on the platform).
While it is running, the *Agent* will attend any received request from the *Clients* and answers back with the result of that request.

Configuration
-------------

There are several configuration parameters which can be set at **compile time** in order to configure the *eProsima Micro XRCE-DDS Agent*.
These parameters can be selected as CMake flags (``-D<parameter>=<value>``) before the compilation.
The following is a list of the aforementioned parameters:

``CONFIG_RELIABLE_STREAM_DEPTH``
    Specify the history of the reliable streams (default 16).

``CONFIG_BEST_EFFORT_STREAM_DEPTH``
    Specify the history of the best-effort streams (default 16).

``CONFIG_HEARTBEAT_PERIOD``
    Specify the ``HEARTBEAT`` message period in millisecond (default 200).

``CONFIG_TCP_MAX_CONNECTIONS``
    Specify the maximum number of connections, the *Agent* is able to manage (default 100).

``CONFIG_TCP_MAX_BACKLOG_CONNECTIONS``
    Specify the maximum number of incoming connections (pending to establish), the *Agent* is able to manage (default 100).


Run an Agent
------------

To run the *Agent* you should build it as indicated in :ref:`installation_label`.
Once it is built successfully, you just need to launch it executing the following command.

**UDP communication** ::

    $ ./MicroXRCEAgent --udp <local-port> [--discovery <discovery-port>] [--refs <refs-file>]

Launch a UDP server over port ``<local-port>``. It has the following options:

* ``--discovery <discovery-port>`` (only Linux): select a port for the discovery server which has the port number ``<7400>`` by default.
* ``--refs <refs-file>``: load a reference file using the relative path to the working directory.
  This reference file shall be composed by a set of Fast RTPS profiles following the `XML syntax <https://eprosima-fast-rtps.readthedocs.io/en/latest/xmlprofiles.html>`_ described in Fast RTPS.
  The ``profile_name`` attribute of each profile represents a reference to an XRCE-Entity so that it could be used by the *Clients* to create entities by reference.

**TCP communication** ::

    $ ./MicroXRCEAgent --tcp <local-port> [--discovery <discovery-port>] [--refs <refs-file>]

Launch a TCP server over port ``<local-port>``. It has the following options:

* ``--discovery <discovery-port>`` (only Linux): select a port for the discovery server which has the port number ``<7400>`` by default.
* ``--refs <refs-file>``: load a reference file using the relative path to the working directory.
  This reference file shall be composed by a set of Fast RTPS profiles following the `XML syntax <https://eprosima-fast-rtps.readthedocs.io/en/latest/xmlprofiles.html>`_ described in Fast RTPS.
  The ``profile_name`` attribute of each profile represents a reference to an XRCE-Entity so that it could be used by the *Clients* to create entities by reference.

**Serial communication** (only Linux) ::

    $ ./MicroXRCEAgent --serial <device-name> [--baudrate <baudrate>] [--refs <refs-file>]

Launch a Serial server over device ``<device-name>``. It has the following options:

* ``--baudrate <baudrate>``: set the baud rate of the communication. It can take the following values:
  0, 50, 75, 110, 134, 150, 200, 300, 600, 1200, 1800, 240, 4800, 9600, 19200, 38400, 57600, 115200 (default), 230400, 460800, 500000, 576000, 921600, 1000000, 1152000, 1500000, 2000000, 2500000, 3000000, 3500000 or 4000000 Bd.
* ``--refs <refs-file>``: load a reference file using the relative path to the working directory.
  This reference file shall be composed by a set of Fast RTPS profiles following the `XML syntax <https://eprosima-fast-rtps.readthedocs.io/en/latest/xmlprofiles.html>`_ described in Fast RTPS.
  The ``profile_name`` attribute of each profile represents a reference to an XRCE-Entity so that it could be used by the *Clients* to create entities by reference.

**Pseudo-Serial communication** (only Linux) ::

    $ ./MicroXRCEAgent --pseudo-serial [--baudrate <baudrate>] [--refs <refs-file>]

Launch a pseudo-serial server which provide a device to which *Clients* can connect. It has the following options:

* ``--baudrate <baudrate>``: set the baud rate of the communication. It can take the following values:
  0, 50, 75, 110, 134, 150, 200, 300, 600, 1200, 1800, 240, 4800, 9600, 19200, 38400, 57600, 115200 (default), 230400, 460800, 500000, 576000, 921600, 1000000, 1152000, 1500000, 2000000, 2500000, 3000000, 3500000 or 4000000 Bd.
* ``--refs <refs-file>``: load a reference file using the relative path to the working directory.
  This reference file shall be composed by a set of Fast RTPS profiles following the `XML syntax <https://eprosima-fast-rtps.readthedocs.io/en/latest/xmlprofiles.html>`_ described in Fast RTPS.
  The ``profile_name`` attribute of each profile represents a reference to an XRCE-Entity so that it could be used by the *Clients* to create entities by reference.
