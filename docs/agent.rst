Micro RTPS Agent
================

*Micro RTPS Agent* acts as a server between DDS Network and *Micro RTPS Clients". *Micro RTPS Agents* receive messages containing operations from Clients. Agents keep track of the Clients and the *Micro RTPS Entities* they had created. The Agent uses the Entities to interact with DDS Global Data Space on behalf of the Client.

The communication between a *Micro RTPS Client* and a *Micro RTPS Agent* supports two kind transports: UDP or SerialPort. While running *Micro RTPS Agent* will attend any received request from your *Micro RTPS Clients*. Once a request is attended *Micro RTPS Agent* answers back with the result of that request.

Run an Agent
------------

To run the *Micro RTPS Agent* you should build it as indicated in :ref:`source_label`

Once it is build successfully you just need to launch it executing one of the following options:

For running it on UDP.  ::

    $ ./micrortps_agent udp <outUPDport> <inUPDport>

For running it on SerialPort.  ::

    $ ./micrortps_agent serial <serialDevice>
