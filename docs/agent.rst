.. _micro_xrce_dds_agent_label:

Micro XRCE-DDS Agent
====================

*Micro XRCE-DDS Agent* acts as a server between the DDS Network and *Micro XRCE-DDS Clients*.
Agents receive messages containing operations from clients.
Also agents keep track of the clients and the entities they create.
The Agent uses the entities to interact with the DDS Global Data Space on behalf of the client.

The communication between a client and an agent currently supports UDP, TCP and serial (dependent on the platform).
While running agent will attend any received request from your clients and answers back with the result of that request.

Run an Agent
------------

To run the agent you should build it as indicated in :ref:`installation_label`.
Once it is built successfully you just need to launch it executing the following command.

For serial communication: ::

    $ ./MicroXRCE-DDSAgent serial <device>

For udp communication: ::

    $ ./MicroXRCE-DDSAgent udp <port>

For tcp communication: ::

    $ ./MicroXRCE-DDSAgent tcp <port>

If the transport used is a reliability transport, the use of a best effort stream over it is equivalent to use a reliable stream over a not reliable transport.

For running the agent is necessary that the file ``DEFAULT_FASTRTPS_PROFILES.xml`` be located in the folder where the
agent is located. Default installation place this file along with the agent executable into ``/usr/local/bin``.

