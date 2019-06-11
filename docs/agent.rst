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

``UAGENT_CONFIG_RELIABLE_STREAM_DEPTH``
    Specify the history of the reliable streams (default 16).

``UAGENT_CONFIG_BEST_EFFORT_STREAM_DEPTH``
    Specify the history of the best-effort streams (default 16).

``UAGENT_CONFIG_HEARTBEAT_PERIOD``
    Specify the ``HEARTBEAT`` message period in millisecond (default 200).

``UAGENT_CONFIG_TCP_MAX_CONNECTIONS``
    Specify the maximum number of connections, the *Agent* is able to manage (default 100).

``UAGENT_CONFIG_TCP_MAX_BACKLOG_CONNECTIONS``
    Specify the maximum number of incoming connections (pending to establish), the *Agent* is able to manage (default 100).


Run an Agent
------------

To run the *Agent* you should build it as indicated in :ref:`installation_label`.
Once it is built successfully, you just need to launch it executing one of the following commands:

**UDP server** ::

    $ ./MicroXRCEAgent udp [OPTIONS]

    Options:
      -h,--help                               Print this help message and exit
      -p,--port UINT REQUIRED                 Select the port
      -m,--middleware TEXT in {ced,dds}=dds   Select the kind of Middleware
      -r,--refs FILE                          Load a reference file
      -v,--verbose UINT in {0,1,2,3,4,5,6}=4  Select log level from less to more verbose
      -d,--discovery                          Active the Discovery server
      --disport UINT=7400 Needs: --discovery  Select the port for the Discovery server
      --p2p UINT                              Activate the P2P profile using the given port

**TCP server** ::

    $ ./MicroXRCEAgent tcp [OPTIONS]

    Options:
      -h,--help                               Print this help message and exit
      -p,--port UINT REQUIRED                 Select the port
      -m,--middleware TEXT in {ced,dds}=dds   Select the kind of Middleware
      -r,--refs FILE                          Load a reference file
      -v,--verbose UINT in {0,1,2,3,4,5,6}=4  Select log level from less to more verbose
      -d,--discovery                          Active the Discovery server
      --disport UINT=7400 Needs: --discovery  Select the port for the Discovery server
      --p2p UINT                              Activate the P2P profile using the given port

**Serial server** (only Linux) ::

    $ ./MicroXRCEAgent serial

    Options:
      -h,--help                               Print this help message and exit
      --dev FILE REQUIRED                     Select the serial device
      -b,--baudrate TEXT=115200               Select the baudrate
      -m,--middleware TEXT in {ced,dds}=dds   Select the kind of Middleware
      -r,--refs FILE                          Load a reference file
      -v,--verbose UINT in {0,1,2,3,4,5,6}=4  Select log level from less to more verbose
      -d,--discovery                          Active the Discovery server
      --disport UINT=7400 Needs: --discovery  Select the port for the Discovery server
      --p2p UINT                              Activate the P2P profile using the given port

**Pseudo-Serial server** (only Linux) ::

    $ ./MicroXRCEAgent pseudo-serial

    Options:
      -h,--help                               Print this help message and exit
      --dev FILE REQUIRED                     Select the serial device
      -b,--baudrate TEXT=115200               Select the baudrate
      -m,--middleware TEXT in {ced,dds}=dds   Select the kind of Middleware
      -r,--refs FILE                          Load a reference file
      -v,--verbose UINT in {0,1,2,3,4,5,6}=4  Select log level from less to more verbose
      -d,--discovery                          Active the Discovery server
      --disport UINT=7400 Needs: --discovery  Select the port for the Discovery server
      --p2p UINT                              Activate the P2P profile using the given port

* ``-d,--discovery <discovery-port>`` select a port for the discovery server which has the port number ``<7400>`` by default.
* ``-r,--refs <refs-file>``: load a reference file using the relative path to the working directory.
  This reference file shall be composed by a set of Fast RTPS profiles following the `XML syntax <https://eprosima-fast-rtps.readthedocs.io/en/latest/xmlprofiles.html>`_ described in Fast RTPS.
  The ``profile_name`` attribute of each profile represents a reference to an XRCE-Entity so that it could be used by the *Clients* to create entities by reference.
* ``-b,--baudrate <baudrate>``: set the baud rate of the communication. It can take the following values:
  0, 50, 75, 110, 134, 150, 200, 300, 600, 1200, 1800, 240, 4800, 9600, 19200, 38400, 57600, 115200 (default), 230400, 460800, 500000, 576000, 921600, 1000000, 1152000, 1500000, 2000000, 2500000, 3000000, 3500000 or 4000000 Bd.
* ``-v,--verbose <level[0-6]>``: set log level from less to more verbose, in  level 0 the logger is off.
* ``-m,--middleware <middleware-impl>``: set the middleware implementation to use. There are two: DDS (specified by the XRCE standard) and Centralized (topic are managed by the Agent similarly MQTT).
* ``--p2p <port>``: enable P2P communication. Centralized middleware is necessary for this option.
