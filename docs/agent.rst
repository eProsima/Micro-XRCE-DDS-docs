.. _micro_rtps_agent_label:

Micro RTPS Agent
================

*Micro RTPS Agent* acts as a server between the DDS Network and *Micro RTPS Clients*.
*Micro RTPS Agents* receive messages containing Operations from Clients.
Agents keep track of the *Clients* and the *Micro RTPS Entities* they create.
The Agent uses the Entities to interact with the DDS Global Data Space on behalf of the *Client*.

The communication between a *Micro RTPS Client* and a *Micro RTPS Agent* currently supports UDP and serial.
While running *Micro RTPS Agent* will attend any received request from your *Micro RTPS Clients*.
*Micro RTPS Agent* answers back with the result of a request each time a request is attended.

Run an Agent
------------

To run the *Micro RTPS Agent* you should build it as indicated in :ref:`installation_label`.
Once it is built successfully you just need to launch it executing the following command.

For serial communication: ::

    $ ./micrortps_agent serial <device>

For udp communication: ::

    $ ./micrortps_agent udp <port>

For running the agent is necessary that the file ``DEFAULT_FASTRTPS_PROFILES.xml`` be located in the folder where the
agent is located. Default installation place this file along with the agent executable into ``/usr/local/bin``.
