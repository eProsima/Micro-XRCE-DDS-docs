Dependencies
============
*eProsima Micro RTPS - Client* does not required external dependencies.

*eProsima Micro RTPS - Agent* requires you to install the following packages on your machine.

:FastRTPS: *Micro RTPS* Agent requires *eProsima Fast RTPS* to be on your machine. (see: `eProsima Fast RTPS installation guide <http://eprosima-fast-rtps.readthedocs.io/en/latest/index.html#installation>`_)

:ASIO: Agents uses Asio library. You can use the one provided as a third party with the *Micro RTPS Agent* as part of the sources or use a different one installed on the system.

:Gtest: Our tests uses Gtest, so it is needed to build our tests from the sources.
