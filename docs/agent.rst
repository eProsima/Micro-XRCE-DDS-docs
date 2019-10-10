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
      -d,--discovery                          Activate the Discovery server
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
      -d,--discovery                          Activate the Discovery server
      --disport UINT=7400 Needs: --discovery  Select the port for the Discovery server
      --p2p UINT                              Activate the P2P profile using the given port

**Serial server** (only Linux) ::

    $ ./MicroXRCEAgent serial [OPTIONS]

    Options:
      -h,--help                               Print this help message and exit
      --dev FILE REQUIRED                     Select the serial device
      -b,--baudrate TEXT=115200               Select the baudrate
      -m,--middleware TEXT in {ced,dds}=dds   Select the kind of Middleware
      -r,--refs FILE                          Load a reference file
      -v,--verbose UINT in {0,1,2,3,4,5,6}=4  Select log level from less to more verbose
      -d,--discovery                          Activate the Discovery server
      --disport UINT=7400 Needs: --discovery  Select the port for the Discovery server
      --p2p UINT                              Activate the P2P profile using the given port

**Pseudo-Serial server** (only Linux) ::

    $ ./MicroXRCEAgent pseudo-serial [OPTIONS]

    Options:
      -h,--help                               Print this help message and exit
      --dev FILE REQUIRED                     Select the serial device
      -b,--baudrate TEXT=115200               Select the baudrate
      -m,--middleware TEXT in {ced,dds}=dds   Select the kind of Middleware
      -r,--refs FILE                          Load a reference file
      -v,--verbose UINT in {0,1,2,3,4,5,6}=4  Select log level from less to more verbose
      -d,--discovery                          Activate the Discovery server
      --disport UINT=7400 Needs: --discovery  Select the port for the Discovery server
      --p2p UINT                              Activate the P2P profile using the given port

* The reference file shall be composed by a set of Fast RTPS profiles following the `XML syntax <https://eprosima-fast-rtps.readthedocs.io/en/latest/xmlprofiles.html>`_ described in Fast RTPS.
  The ``profile_name`` attribute of each profile represents a reference to an XRCE-Entity so that it could be used by the *Clients* to create entities by reference.
* The ``-b,--baudrate <baudrate>`` options sets the baud rate of the communication. It can take the following values:
  0, 50, 75, 110, 134, 150, 200, 300, 600, 1200, 1800, 240, 4800, 9600, 19200, 38400, 57600, 115200 (default), 230400, 460800, 500000, 576000, 921600, 1000000, 1152000, 1500000, 2000000, 2500000, 3000000, 3500000 or 4000000 Bd.
* The ``-v,--verbose <level[0-6]>`` option sets log level from less to more verbose, in  level 0 the logger is off.
* ``-m,--middleware <middleware-impl>``: set the middleware implementation to use. There are two: DDS (specified by the XRCE standard) and Centralized (topic are managed by the Agent similarly MQTT).
* The ``--p2p <port>`` option enables P2P communication. Centralized middleware is necessary for this option.

Middleware Abstraction Layer
----------------------------

The Middleware Abstraction Layer is an interface whose purpose is to isolated the XRCE core from the middleware, as well as, to allow providing multiple middleware implementations.
The interface has a set of pure virtual functions, which are called by the `ProxyClient` each time a *Client* requests for creating/deleting an entity or write/read data.

.. image:: images/middleware_abstraction_layer.svg

For the moment, the *Agent* counts with two middleware implementations: *FastMiddleware* and *CedMiddleware*.

FastMiddleware
^^^^^^^^^^^^^^

The *FastMiddleware* uses *eProsima Fast RTPS*, a C++ implementation of the RTPS (Real Time Publish Subscribe) protocol.
This middleware allows *Client* to produce and consume data in the DDS Global Data Space, and consequently in the ROS 2 system.
In that case, the *Agent* has the default behaviour as described in the DDS-XRCE standard, that is, for each DDS-XRCE entity a DDS proxy entity is created, and the writing/reading action produces a publishing/subscribing operation in the DDS world.

.. _ced_middleware_label:

CedMiddleware
^^^^^^^^^^^^^

The *CedMiddleware* (Centralized Middleware) works similar to MQTT, that is, the *Agent* acts as a broker:

* accepting connection from *Clients*,
* accepting topics messages published by *Client*,
* processing subscribe and unsubscribe requests from *Client*,
* forwarding topics messages that match *Client* subscriptions,
* and closing the connection from the *Client*.
 
By default, this middleware does not allow communication between *Client* connected to different *Agent*, but the :ref:`P2P communication <p2p_communication_label>` enable this feature.

How to add a middleware
^^^^^^^^^^^^^^^^^^^^^^^

Adding a new middleware implementation is quite simple, just the following steps must be taken:

#. Create a class that implement the `Middleware` class (see *inclue/uxr/agent/middleware/fast/FastMiddleware.hpp* and *src/cpp/middleware/fast.cpp* as examples).
#. Add a `enum` member protected by defines in `Middleware::Kind` at *include/uxr/agent/middleware/Middleware.hpp*.
#. Add a case in the switch of the `ProxyClient` constructor at *src/cpp/client/ProxyClient.cpp*.
#. In *CMakeLists.txt* add an option similar to `UAGENT_FAST_PROFILE` and add the source to `SRCS` variable.
#. In *include/uxr/agent/config.hpp.in* add a `#cmakedefine` with the name of the CMake option.
#. Finally, add the CLI middleware option in `MiddlewareOpt` constructor at *include/uxr/agent/utils/CLI.hpp*.

