.. _installation_label:

Installation
============

In this section, you can find the instructions to install the following packages:

- :ref:`install_agent`
- :ref:`install_client`
- :ref:`install_gen`
- :ref:`install_agent_client`

The user can decide wether to install the *Agent* and *Client* as stand-alone packages,
or together. If the first approach is chosen, refer to the :ref:`Release Notes <notes_label>`
section in order to match versions that are compatible.

.. _install_agent:

Installing the Agent stand-alone
--------------------------------

Clone the project from GitHub: ::

    $ git clone https://github.com/eProsima/Micro-XRCE-DDS-Agent.git
    $ cd Micro-XRCE-DDS-Agent
    $ mkdir build && cd build

On Linux, inside of the :code:`build` folder, execute the following commands: ::

    $ cmake ..
    $ make
    $ sudo make install

On Windows first select the Visual Studio version: ::

    $ cmake -G "Visual Studio 14 2015 Win64" ..
    $ cmake --build .
    $ cmake --build . --target install

.. note::
    The *eProsima Micro XRCE-DDS Agent* can be configured at compile-time via several CMake definitions.
    Find them listed in the :ref:`agent_configuration` section of the :ref:`micro_xrce_dds_agent_label` page.

Now you have the executable *eProsima Micro XRCE-DDS Agent* installed in your system. Before running it, you need to add
:code:`/usr/local/lib` to the dynamic loader-linker directories. ::

    sudo ldconfig /usr/local/lib/

.. important::
    The *eProsima Micro XRCE-DDS Agent* executable comes with a rich CLI.
    Find out all the options offered by this CLI when running the *Agent* in the :ref:`agent_cli` section of the
    :ref:`micro_xrce_dds_agent_label` page. 

.. note::
    If you wish, you can also install the Micro XRCE-DDS Agent as a `Snap package. <https://snapcraft.io/micro-xrce-dds-agent>`_ To do so, simply execute ``sudo snap install micro-xrce-dds-agent`` in your Linux console. You can add the ``--edge`` flag.

.. _install_client:

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

Now you have the executable *eProsima Micro XRCE-DDS Client* installed in your system.
Before running it, you need to add :code:`/usr/local/lib` to the dynamic loader-linker directories. ::

    sudo ldconfig /usr/local/lib/

On Windows first select the Visual Studio version: ::

    $ cmake -G "Visual Studio 14 2015 Win64" ..
    $ cmake --build .
    $ cmake --build . --target install

.. note::
    If you want to install the *eProsima Micro XRCE-DDS Client* examples, you can add :code:`-DUCLIENT_BUILD_EXAMPLES=ON`
    to the :code:`cmake ..` command-line options. This flag will enable the compilation of the examples.
    In addition to this flag, there are several other CMake definitions for configuring the building of the client
    library at compile-time.
    Find them in the :ref:`profiles` and :code:`configurations` sections of the :ref:`micro_xrce_dds_client_label` page.

For building your Client app in your host machine, you need to build against the following libs: ::

    gcc <your_main.c> -lmicrocdr -lmicroxrcedds_client

.. _install_gen:

Installing the Micro XRCE-DDS Gen tool
--------------------------------------

Clone the project from GitHub: ::

    $ sudo apt install git openjdk-8-jdk gradle
    $ git clone https://github.com/eProsima/Micro-XRCE-DDS-Gen.git
    $ cd Micro-XRCE-DDS-Gen
    $ git submodules init
    $ git submodules update
    $ gradle build -Dbranch=v1.2.5  

You will have the *Micro XRCE-DDS-Gen* tool available as: ::

    $ ./scripts/microxrceddsgen -help 

.. _install_agent_client:

Installing Agent and Client
---------------------------

Clone the project from GitHub: ::

    $ git clone https://github.com/eProsima/Micro-XRCE-DDS.git
    $ cd Micro-XRCE-DDS
    $ mkdir build && cd build

On Linux, inside of the :code:`build` folder, execute the following commands: ::

    $ cmake ..
    $ make
    $ sudo make install

On Windows choose the Visual Studio version using the CMake option *-G*, for example: ::

    $ cmake -G "Visual Studio 14 2015 Win64" ..
    $ cmake --build . --target install

Now you have both the *eProsima Micro XRCE-DDS Agent* and the *eProsima Micro XRCE-DDS Client* installed in your system.

Usually is useful to install examples along with the XRCE-DDS suite, for doing so, just use `cmake .. -DUXRCE_BUILD_EXAMPLES=ON`.

.. note::
    If you want to install the *eProsima Micro XRCE-DDS* examples, you can add :code:`-DUXRCE_BUILD_EXAMPLES=ON`
    to the :code:`cmake ..` command-line options. This flag will enable the compilation of the examples.