.. _sources_label:

Installation from Sources
=========================

Installing Agent and Client
---------------------------

Clone the project from Github: ::

    $ git clone --recursive https://github.com/eProsima/micro-RTPS.git
    $ mkdir micro-RTPS/build && cd micro-RTPS/build

On Linux, execute: ::

    $ cmake ..
    $ make
    $ sudo make install

Now you have Micro-RTPS-Agent and Micro-RTPS-Client installed in your system.

Installing the Agent stand-alone
--------------------------------

Clone the project from Github: ::

    $ git clone --recursive https://github.com/eProsima/micro-RTPS-agent.git
    $ mkdir micro-RTPS-agent/build && cd micro-RTPS-agent/build

On Linux, execute: ::

    $ cmake -DTHIRDPARTY=ON ..
    $ make
    $ sudo make install

Now you have micrortps_agent installed in your system. Before running it, you need to add ``/usr/local/lib`` to the dynamic loader-linker directories. ::

    sudo ldconfig /usr/local/lib/

Installing the Client stand-alone
---------------------------------

Clone the project from Github: ::

    $ git clone --recursive  https://github.com/eProsima/micro-RTPS-client.git
    $ mkdir micro-RTPS-client/build && cd micro-RTPS-client/build

On Linux, execute: ::

    $ cmake -DTHIRDPARTY=ON ..
    $ make
    $ sudo make install

If you want to install our *Micro RTPS Client* examples you can add ``-DEPROSIMA_BUILD_EXAMPLES=ON`` to the cmake command line options.
