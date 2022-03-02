.. _eprosima_docker_image:

eProsima Docker Image
=====================

eProsima provides the `eProsima XRCE-DDS Suite Docker image <https://eprosima.com/index.php/downloads-all>`_  for those who want a quick demonstration of XRCE-DDS running on an Ubuntu platform.

This Docker image was built for Ubuntu 20.04 (Focal Fossa).

To run this container you need Docker installed; from a terminal run:

.. code-block:: bash

    $ sudo apt install docker.io

.. _xrce_dds_suite:

XRCE-DDS Suite
--------------

This Docker image contains the complete XRCE-DDS suite, which includes:

- :ref:`eProsima XRCE-DDS libraries and examples <xrcedds_suite_examples>`: XRCE-DDS libraries bundled with several
  examples that showcase a variety of capabilities of eProsima's Fast DDS implementation.

- :ref:`eProsima XRCE-DDS Agent <xrcedds_suite_agent>`: eProsima XRCE-DDS Agent is a ready to use implementation of
  DDS-XRCE Agent and it is available for using along with provided examples.

To load this image into your Docker repository, run:

.. code-block:: bash

 $ docker load -i xrcedds-suite:<XRCE-DDS-Version>.tar

You can run this Docker container as follows:

.. code-block:: bash

 $ docker run -it xrcedds-suite:<XRCE-DDS-Version>

From the resulting Bash Shell you can run each feature.

.. _xrcedds_suite_examples:

Client Hello World Example
^^^^^^^^^^^^^^^^^^^^^^^^^^

Included in this Docker container is a set of binary examples that showcase some of the functionalities of the
XRCE-DDS libraries.

This is a minimal example that will perform a Publisher/Subscriber match and start sending samples.
This example is not constrained to the current instance. It's possible to run several instances of this
container to check the communication between them by running the following from each container:

.. code-block:: bash

 $ docker run -it xrcedds-suite:<XRCE-DDS-Version> helloworld_pub

or

.. code-block:: bash

 $ docker run -it xrcedds-suite:<XRCE-DDS-Version> helloworld_sub

.. _xrcedds_suite_agent:

XRCE-DDS Agent
^^^^^^^^^^^^^^

This command creates an instance of the eProsima XRCE-DDS Agent, required for communicating XRCE-DDS Client.
In order to use it run:

.. code-block:: bash

 $ docker run -it xrcedds-suite:<XRCE-DDS-Version> xrce_agent [ARGUMENTS]

More information about the eProsima XRCE-DDS Agent CLI can be found :ref:`here <agent_cli>`