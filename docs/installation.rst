.. _installation_label:

Installation
============

In this section, instructions are provided to install the following packages:

- :ref:`docker_install`
- :ref:`install_agent`
- :ref:`install_client`
- :ref:`install_gen`
- :ref:`install_agent_client`

The user can decide wether to install the *Agent* and *Client* as stand-alone packages,
or together. If the first approach is chosen, refer to the :ref:`Release Notes <notes_label>`
section in order to match versions that are compatible.

.. _docker_install:

Using pre-installed docker images
---------------------------------
Download `here <https://www.eprosima.com/index.php/downloads-all>`__ the Micro XRCE-DDS and Fast-DDS Suite docker image that contains a pre-installed client and agent as well as some compiled examples.
More information about this Docker image can be found :ref:`here <eprosima_docker_image>`.

.. _install_agent:

Installing the Agent standalone
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

    $ cmake -G "Visual Studio 15 2017 Win64" ..
    $ cmake --build .
    $ cmake --build . --target install

.. note::
    The *eProsima Micro XRCE-DDS Agent* can be configured at compile-time via several CMake definitions.
    Find them listed in the :ref:`agent_configuration` section of the :ref:`micro_xrce_dds_agent_label` page.

Now the the executable *eProsima Micro XRCE-DDS Agent* is installed in the system. Before running it, add
:code:`/usr/local/lib` to the dynamic loader-linker directories. ::

    sudo ldconfig /usr/local/lib/

.. important::
    The *eProsima Micro XRCE-DDS Agent* executable comes with a rich CLI.
    Find out all the options offered by this CLI when running the *Agent* in the :ref:`agent_cli` section of the
    :ref:`micro_xrce_dds_agent_label` page.

Installation from Snap package
******************************

The *eProsima Micro XRCE-DDS Agent* can also be installed as a `Snap package. <https://snapcraft.io/micro-xrce-dds-agent>`_.

To this aim, simply execute ``sudo snap install micro-xrce-dds-agent`` in the console.
This will download the Snap package corresponding to the ``stable`` version, that is, the *master* branch on GitHub.

For downloading the Snap image corresponding to the *develop* branch, add the ``--edge`` flag to the installation command.

.. note::
    The Snap package is only available for Linux.

.. _install_agent_docker:

Using the provided Docker image
*******************************

The *eProsima Micro XRCE-DDS Agent* can also be launched directly from its dedicated `Docker image. <https://hub.docker.com/r/eprosima/micro-xrce-dds-agent>`_.

Pull the image by executing ``docker pull eprosima/micro-xrce-dds-agent:<tag> <<args>>``, with *tag* being one of the following options:

* ``stable``: `Micro XRCE-DDS Agent master <https://github.com/eProsima/Micro-XRCE-DDS-Agent/tree/master>`_ branch.
* ``latest``: `Micro XRCE-DDS Agent develop <https://github.com/eProsima/Micro-XRCE-DDS-Agent/tree/develop>`_ branch)
* ``vM.m.p``: `Micro XRCE-DDS Agent tagged <https://github.com/eProsima/Micro-XRCE-DDS-Agent/tags>`_ versions, with the *Major*, *minor*, *patch* structure.

The accepted arguments for ``<<args>>`` are exactly the same which are listed in the :ref:`agent_cli` section.

.. _install_client:

Installing the Client standalone
---------------------------------

Clone the project from GitHub: ::

    $ git clone https://github.com/eProsima/Micro-XRCE-DDS-Client.git
    $ cd Micro-XRCE-DDS-Client
    $ mkdir build && cd build

On Linux, inside of ``build`` folder, execute the following commands: ::

    $ cmake ..
    $ make
    $ sudo make install

Now the the executable *eProsima Micro XRCE-DDS Client* is installed in the system. Before running it, add
:code:`/usr/local/lib` to the dynamic loader-linker directories. ::

    sudo ldconfig /usr/local/lib/

On Windows first select the Visual Studio version: ::

    $ cmake -G "Visual Studio 15 2017 Win64" ..
    $ cmake --build .
    $ cmake --build . --target install

.. note::
    In order to install the *eProsima Micro XRCE-DDS Client* examples, add :code:`-DUCLIENT_BUILD_EXAMPLES=ON`
    to the :code:`cmake ..` command-line options. This flag will enable the compilation of the examples.
    In addition to this flag, there are several other CMake definitions for configuring the building of the client
    library at compile-time.
    Find them in the :ref:`profiles` and :code:`configurations` sections of the :ref:`micro_xrce_dds_client_label` page.

For building a Client app in the host machine, compile against the following libs: ::

    gcc <main.c> -lmicrocdr -lmicroxrcedds_client

.. _install_client_docker:

Using the provided Docker image
*******************************

The *eProsima Micro XRCE-DDS Client* comes with a `Docker image <https://hub.docker.com/r/eprosima/micro-xrce-dds-agent>`_ where the library is installed together with the provided examples, so they can easily be executed by the users.

Pull the image by executing ``docker pull eprosima/micro-xrce-dds-client:<tag> <<args>>``, with *tag* being one of the following options:

* ``stable``: `Micro XRCE-DDS Client master <https://github.com/eProsima/Micro-XRCE-DDS-Client/tree/master>`_ branch.
* ``latest``: `Micro XRCE-DDS Client develop <https://github.com/eProsima/Micro-XRCE-DDS-Client/tree/develop>`_ branch
* ``vM.m.p``: `Micro XRCE-DDS Client tagged <https://github.com/eProsima/Micro-XRCE-DDS-Client/tags>`_ versions, with the *Major*, *minor*, *patch* structure.

The accepted arguments for ``<<args>>`` are the examples' executable names, followed by the arguments required for each example to work.
Find a list of all the available examples `here <https://github.com/eProsima/Micro-XRCE-DDS-Client/tree/master/examples>`_. Note that they may differ between *master* and *develop* branches and the tagged versions.

.. _install_gen:

Installing the Micro XRCE-DDS Gen tool
--------------------------------------

:ref:`Install dependencies<dependencies_label_gen>`, clone the project from GitHub and build: ::

    $ git clone https://github.com/eProsima/Micro-XRCE-DDS-Gen.git
    $ cd Micro-XRCE-DDS-Gen
    $ git submodule init
    $ git submodule update
    $ gradle build -Dbranch=v1.2.5

The *Micro XRCE-DDS-Gen* tool will be available as: ::

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

    $ cmake -G "Visual Studio 15 2017 Win64" ..
    $ cmake --build . --target install

Now both the *eProsima Micro XRCE-DDS Agent* and the *eProsima Micro XRCE-DDS Client* are installed in the system.

.. note::
    In order to install the *eProsima Micro XRCE-DDS Gen* tool as well, add :code:`-DUXRCE_ENABLE_GEN=ON`
    to the :code:`cmake ..` command-line options. This flag will enable the downloading and compilation of the code generating tool.

.. note::
    In order to install the *eProsima Micro XRCE-DDS* examples, add :code:`-DUXRCE_BUILD_EXAMPLES=ON`
    to the :code:`cmake ..` command-line options. This flag will enable the compilation of the examples.

.. _install_agent_client_docker:

Using the provided Docker image
*******************************

*eProsima Micro XRCE-DDS* is also available as a whole package in a `Docker image. <https://hub.docker.com/r/eprosima/micro-xrce-dds-agent>`_

Within this Docker image, the *Micro XRCE-DDS Agent* standalone application and library are installed, as well as the *Micro XRCE-DDS Client* library and built-in examples.

Pull the image by executing ``docker pull eprosima/micro-xrce-dds:<tag> <<args>>``, with *tag* being one of the following options:

* ``stable``: `Micro XRCE-DDS master <https://github.com/eProsima/Micro-XRCE-DDS/tree/master>`_ branch.
* ``latest``: `Micro XRCE-DDS develop <https://github.com/eProsima/Micro-XRCE-DDS/tree/develop>`_ branch
* ``vM.m.p``: `Micro XRCE-DDS tagged <https://github.com/eProsima/Micro-XRCE-DDS/tags>`_ versions, with the *Major*, *minor*, *patch* structure.

The accepted arguments for ``<<args>>`` are:

* To launch the *Micro XRCE-DDS Agent*: ``MicroXRCEAgent <<agent_args``, being ``<<agent_args>>`` the ones described in the :ref:`agent_cli` section.
* The *Micro XRCE-DDS Client* examples' executable names, as explained :ref:`above.<install_client_docker>`
