.. _installation_label:

Installation
=========================
To compile and install the Client and the Agent modules, we use CMake.

Installing the Agent stand-alone
--------------------------------

Clone the project from GitHub: ::

    $ git clone https://github.com/eProsima/Micro-XRCE-DDS-Agent.git
    $ cd Micro-XRCE-DDS-Agent
    $ mkdir build && cd build

On Linux, inside of ``build`` folder, execute the following commands: ::

    $ cmake ..
    $ make
    $ sudo make install

On Windows first select the Visual Studio version: ::

    $ cmake -G "Visual Studio 14 2015 Win64" ..
    $ cmake --build .
    $ cmake --build . --target install

Now you have the executable *eProsima Micro XRCE-DDS Agent* installed in your system. Before running it, you need to add ``/usr/local/lib`` to the dynamic loader-linker directories. ::

    sudo ldconfig /usr/local/lib/

Installing the Client stand-alone
---------------------------------

Clone the project from GitHub: ::

    $ git clone https://github.com/eProsima/Micro-XRCE-DDS-Client.git
    $ cd Micro-XRCE-DDS-Client
    $ mkdir build && cd build

On Linux, inside of ``build`` folder, execute the following commands: ::

    $ cmake ..
    $ make
    $ sudo make install

Now you have the executable *eProsima Micro XRCE-DDS Client* installed in your system. Before running it, you need to add ``/usr/local/lib`` to the dynamic loader-linker directories. ::

    sudo ldconfig /usr/local/lib/

On Windows first select the Visual Studio version: ::

    $ cmake -G "Visual Studio 14 2015 Win64" ..
    $ cmake --build .
    $ cmake --build . --target install

If you want to install the *eProsima Micro XRCE-DDS Client* examples, you can add ``-DUCLIENT_BUILD_EXAMPLES=ON`` to the CMake's command-line options.
This flag will enable the compilation of the examples when the project is compiled.
There are several CMake definitions for configuring the build of the client library at compile time.
You can found them in :ref:`micro_xrce_dds_client_label` page under `Configuration` section.

For building your Client app, you need to build against the following libs: ::

    gcc <your_main.c> -lmicrocdr -lmicroxrcedds_client

Installing Agent and Client
---------------------------

Clone the project from GitHub: ::

    $ git clone https://github.com/eProsima/Micro-XRCE-DDS.git
    $ cd Micro-XRCE-DDS
    $ mkdir build && cd build

On Linux, inside of ``build`` folder, execute the following commands: ::

    $ cmake ..
    $ make
    $ sudo make install

On Windows choose the Visual Studio version using the CMake option *-G*, for example: ::

    $ cmake -G "Visual Studio 14 2015 Win64" ..
    $ cmake --build . --target install

Now you have *eProsima Micro XRCE-DDS Agent* and *eProsima Micro XRCE-DDS Client* installed in your system.

Usually is useful to install examples along with the XRCE-DDS suite, for doing so, just use `cmake .. -DUXRCE_BUILD_EXAMPLES=ON`.

