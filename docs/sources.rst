.. _source_label:

Installation from Sources
=========================

Installing the Agent
--------------------

Clone the project from Github: ::

    $ git clone --recursive https://github.com/eProsima/Micro-RTPS
    $ mkdir Micro-RTPS/build && cd Micro-RTPS/build

On Linux, execute: ::

    $ cmake -DTHIRDPARTY=ON ..
    $ make
    $ sudo make install

Now you have micrortps_agent installed in your system. Before running it, you need to add /usr/local/lib to the dynamic loader-linker directories. ::

    sudo ldconfig /usr/local/lib/

Installing the Client
---------------------

Clone the project from Github: ::

    $ git clone --recursive  https://github.com/eProsima/Micro-RTPS
    $ mkdir Micro-RTPS/build && cd Micro-RTPS/build

On Linux, execute: ::

    $ cmake -DTHIRDPARTY=ON ..
    $ make
    $ sudo make install

If you want to install our *Micro RTPS Client* examples you can add "-DEPROSIMA_BUILD_EXAMPLES=ON" to the cmake command line options.