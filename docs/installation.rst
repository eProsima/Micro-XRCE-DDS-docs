.. _installation_label:

Installation
=========================

Installing Agent and Client
---------------------------

Clone the project from Github: ::

    $ git clone --recursive https://github.com/eProsima/micro-RTPS.git
    $ cd micro-RTPS
    $ mkdir build && cd build

On Linux, execute the following commands: ::

    $ cmake -DTHIRDPARTY=ON ..
    $ make
    $ sudo make install

On Windows choose the Visual Studio version using the CMake option *-G*, for example: ::

    > cmake -G "Visual Studio 14 2015 Win64" -DTHIRDPARTY=ON ..
    > cmake --build . --target install

Now you have *Micro RTPS Agent* and *Client* installed in your system.

Installing the Agent stand-alone
--------------------------------

Clone the project from Github: ::

    $ git clone --recursive https://github.com/eProsima/micro-RTPS-agent.git
    $ cd micro-RTPS-agent
    $ mkdir build && cd build

On Linux, execute the following commands: ::

    $ cmake -DTHIRDPARTY=ON ..
    $ make
    $ sudo make install

On Windows first select the Visual Studio version: ::

    > cmake -G "Visual Studio 14 2015 Win64" -DTHIRDPARTY=ON ..
    > cmake --build . --target install

Now you have the program `agent` installed in your system. Before running it, you need to add ``/usr/local/lib`` to the dynamic loader-linker directories. ::

    sudo ldconfig /usr/local/lib/

Installing the Client stand-alone
---------------------------------

Clone the project from Github: ::

    $ git clone --recursive https://github.com/eProsima/micro-RTPS-client.git
    $ cd micro-RTPS-client
    $ mkdir build && cd build

On Linux, execute the following commands: ::

    $ cmake -DTHIRDPARTY=ON ..
    $ make
    $ sudo make install

On Windows first select the Visual Studio version: ::

    > cmake -G "Visual Studio 14 2015 Win64" -DTHIRDPARTY=ON ..
    > cmake --build . --target install

If you want to install our *Micro RTPS Client* examples you can add ``-DEPROSIMA_BUILD_EXAMPLES=ON`` to the cmake command line options.
