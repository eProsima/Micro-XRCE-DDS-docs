# eProsima Micro RTPS

<a href="http://www.eprosima.com"><img src="https://encrypted-tbn3.gstatic.com/images?q=tbn:ANd9GcSd0PDlVz1U_7MgdTe0FRIWD0Jc9_YH-gGi0ZpLkr-qgCI6ZEoJZ5GBqQ" align="left" hspace="8" vspace="2" width="100" height="100" ></a>

*eprosima Micro RTPS* is a software solution which allows to communicate extremely resource constrained environments(XRCEs) with an existing DDS network. This implementation comply with the specification proposal, "eXtremely Resource Constrained Environments DDS (DDS-XRCE)" submitted to the Object Management Group (OMG) consortium.

*Micro RTPS* implements a client-server protocol in order to enable resource-constrained devices(clients) to participate in DDS communications, This communication is done through an Agent(server). The Agent acts on behalf of the clients and enable them to participate as DDS publishers and/or subscribers in the DDS Global Data Space.

*eProsima Micro RTPS* provides both, a plug and play Agent and an API layer for the implementation of your own clients.


## Quick example

*Micro RTPS* provides a C API which allows you to create your own clients publishing and/or listening to topics from DDS Glocal Data Space. The following example are a simple Client (Using that C API) and an Agent publishing and receiving "Hello World" messages to DDS world.


`
ClientState* state = new_udp_client_state(BUFFER_SIZE, received_port, send_port);
free_client_state(state);

 `



----------------------------------------














Start client

















`<blink>`



You can get either a binary distribution of *eprosima Fast RTPS* or compile the library yourself from source.

### Installation from binaries
The latest, up to date binary release of *eprosima Fast RTPS* can be obtained from the <a href='http://www.eprosima.com'>company website</a>.

### Installation from Source
To compile *eprosima Fast RTPS* from source, at least Cmake version 2.8.12 and Boost 1.61 are needed.
Clone the project from GitHub:

    $ git clone https://github.com/eProsima/Fast-RTPS

If you are on Linux, execute:

    $ cmake ../ -DEPROSIMA_BUILD=ON -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=install
    $ make
    $ make install

If you are on Windows, choose your version of Visual Studio:

    > cmake ../  -G"Visual Studio 14 2015 Win64" -DEPROSIMA_BUILD=ON -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=installationpath
    > cmake --build . --target install

If you want to compile the performance tests, you will need to add the argument `-DPERFORMANCE_TESTS=ON` when calling Cmake.

## Documentation

You can access the documentation online, which is hosted on [Read the Docs](http://eprosima-fast-rtps.readthedocs.io).

* [Start Page](http://eprosima-fast-rtps.readthedocs.io)
* [Installation manual](http://eprosima-fast-rtps.readthedocs.io/en/latest/requirements.html)
* [User manual](http://eprosima-fast-rtps.readthedocs.io/en/latest/introduction.html)
* [FastRTPSGen manual](http://eprosima-fast-rtps.readthedocs.io/en/latest/geninfo.html)
* [Release notes](http://eprosima-fast-rtps.readthedocs.io/notes.html)

## Getting Help

If you need support you can reach us by mail at `support@eProsima.com` or by phone at `+34 91 804 34 48`.
