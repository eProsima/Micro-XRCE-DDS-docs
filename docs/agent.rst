.. _micro_rtps_agent_label:

Micro RTPS Agent
================

*Micro RTPS Agent* acts as a server between DDS Network and *Micro RTPS Clients*. *Micro RTPS Agents* receive messages containing Operations from Clients. Agents keep track of the Clients and the *Micro RTPS Entities* they create. The Agent uses the Entities to interact with DDS Global Data Space on behalf of the Client.

The communication between a *Micro RTPS Client* and a *Micro RTPS Agent* supports two kind transports: UDP or SerialPort. While running *Micro RTPS Agent* will attend any received request from your *Micro RTPS Clients*. *Micro RTPS Agent* answers back with the result of a request each time a request is attended.

Run an Agent
------------

To run the *Micro RTPS Agent* you should build it as indicated in :ref:`source_label`

Once it is built successfully you just need to launch it executing one of the following options:

For running it on UDP.  ::

    $ ./micrortps_agent udp <local_ip> <out_UDP_port> <in_UDP_port>

For running it on SerialPort.  ::

    $ ./micrortps_agent serial <serial_Device>
