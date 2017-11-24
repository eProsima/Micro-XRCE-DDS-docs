Micro RTPS Agent
================

*Micro RTPS Agent* acts as a Server for the *Micro RTPS Clients* and allow those Clients to interact with DDS Global Data Space.
The *Micro RTPS Agent* manages the DDS Objects on behalf of the client. It keeps track off the Clients and the Entities they had created to interact with DDS world.

To run the *Micro RTPS Agent* you should build it as indicated in :ref:`source_label`

*Micro RTPS Agent* supports two communication transports: UDP or SerialPort.

Once it is build successfully you just need to launch it executing one of the following options:

For running it on UDP.  ::

    $ ./micrortps_agent udp <outUPDport> <inUPDport>

For running it on SerialPort.  ::

    $ ./micrortps_agent serial <serialDevice>


While running *Micro RTPS Agent* will attend any received request from your *Micro RTPS Clients". Once a request is attended *Micro RTPS Agent* answers back with the result of that request.