.. _installation_label:

Installation
=========================
To compile and install the Client and the Agent modules, we use CMake.

Installing Agent and Client
---------------------------

Clone the project from GitHub: ::

    $ git clone --recursive https://github.com/eProsima/micro-XRCE-DDS.git
    $ cd micro-XRCE-DDS
    $ mkdir build && cd build

On Linux, inside of ``build`` folder, execute the following commands: ::

    $ cmake -DTHIRDPARTY=ON ..
    $ make
    $ sudo make install

On Windows choose the Visual Studio version using the CMake option *-G*, for example: ::

    $ cmake -G "Visual Studio 14 2015 Win64" -DTHIRDPARTY=ON ..
    $ cmake --build . --target install

Now you have *Micro XRCE-DDS Agent* and *Micro XRCE-DDS Client* installed in your system.

Installing the Agent stand-alone
--------------------------------

Clone the project from GitHub: ::

    $ git clone https://github.com/eProsima/micro-XRCE-DDS-agent.git
    $ cd micro-XRCE-DDS-agent
    $ mkdir build && cd build

On Linux, inside of ``build`` folder, execute the following commands: ::

    $ cmake -DTHIRDPARTY=ON ..
    $ make
    $ sudo make install

On Windows first select the Visual Studio version: ::

    $ cmake -G "Visual Studio 14 2015 Win64" -DTHIRDPARTY=ON ..
    $ cmake --build . --target install

Now you have the executable `Micro XRCE-DDS Agent` installed in your system. Before running it, you need to add ``/usr/local/lib`` to the dynamic loader-linker directories. ::

    sudo ldconfig /usr/local/lib/

Installing the Client stand-alone
---------------------------------

Clone the project from GitHub: ::

    $ git clone https://github.com/eProsima/micro-XRCE-DDS-client.git
    $ cd micro-XRCE-DDS-client
    $ mkdir build && cd build

On Linux, inside of ``build`` folder, execute the following commands: ::

    $ cmake -DTHIRDPARTY=ON ..
    $ make
    $ sudo make install

On Windows first select the Visual Studio version: ::

    $ cmake -G "Visual Studio 14 2015 Win64" -DTHIRDPARTY=ON ..
    $ cmake --build . --target install

If you want to install the *Micro XRCE-DDS Client* examples you can add ``-DEPROSIMA_BUILD_EXAMPLES=ON`` to the CMake command line options.
This flag will enable the compilation of the examples when the project it is compiled.
There are several CMake definitions for configuring the build of the client library at compile time.
You can found them in :ref:`micro_rtps_client_label` page under `Configuration` section.

Compiling an app with the Client library
----------------------------------------
For building your client program you need to build against the following libs: ::

    gcc <your_main.c> -lmicrocdr -lmicrortps_client

