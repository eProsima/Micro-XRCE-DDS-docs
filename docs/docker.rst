eProsima Docker Image
=====================

eProsima provides the eProsima XRCE-DDS Suite Docker image for those who want a quick demonstration of XRCE-DDS running on an Ubuntu
platform. It can be downloaded from `eProsima's downloads page <https://eprosima.com/index.php/downloads-all>`_.

This Docker image was built for Ubuntu 20.04 (Focal Fossa).

To run this container you need Docker installed. From a terminal run

.. code-block:: bash

    $ sudo apt install docker.io

.. _xrce_dds_suite:

XRCE-DDS Suite
--------------

This Docker image contains the complete XRCE-DDS suite. This includes:

- :ref:`eProsima XRCE-DDS libraries and examples <xrcedds_suite_examples>`: XRCE-DDS libraries bundled with several
  examples that showcase a variety of capabilities of eProsima's Fast DDS implementation.

- :ref:`eProsima XRCE-DDS Agent <xrcedds_suite_agent>`: eProsima XRCE-DDS Agent is a ready to use implementation of
  DDS-XRCE Agent and it is available for using along with provided examples.

To load this image into your Docker repository, from a terminal run

.. code-block:: bash

 $ docker load -i xrcedds-suite:<XRCE-DDS-Version>.tar

You can run this Docker container as follows

.. code-block:: bash

 $ docker run -it xrcedds-suite:<XRCE-DDS-Version>

From the resulting Bash Shell you can run each feature.

.. xrcedds_suite_examples:

Client Hello World Example
^^^^^^^^^^^^^^^^^^^^^^^^^^

Included in this Docker container is a set of binary examples that showcase some functionalities of the
XRCE-DDS libraries.

This is a minimal example that will perform a Publisher/Subscriber match and start sending samples.

This example is not constrained to the current instance. It's possible to run several instances of this
container to check the communication between them by running the following from each container.

.. code-block:: bash

 $ docker run -it xrcedds-suite:<XRCE-DDS-Version> helloworld_pub

or

.. code-block:: bash

 $ docker run -it xrcedds-suite:<XRCE-DDS-Version> helloworld_sub

.. xrcedds_suite_agent:

XRCE-DDS Agent
^^^^^^^^^^^^^^

This command creates an instance of the eProsima XRCE-DDS Agent, required for communicating XRCE-DDS Client.
In order to use it run:

.. code-block:: bash

 $ docker run -it xrcedds-suite:<XRCE-DDS-Version> xrce_agent [ARGUMENTS]

More information about the eProsima XRCE-DDS Agent CLI can be found :ref:`here <_agent_cli>`