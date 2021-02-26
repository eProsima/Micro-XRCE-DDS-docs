.. _dependencies_label:

External dependencies
=====================

In this section we list the dependencies of the libraries composing the
*eProsima Micro XRCE-DDS* suite.

eProsima Micro XRCE-DDS Client
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The *eProsima Micro XRCE-DDS Client* has no external dependencies.

eProsima Micro XRCE-DDS Agent
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

eProsima Fast DDS
    The *eProsima Micro XRCE-DDS Agent* requires *eProsima Fast DDS* to work.
    If *eProsima Fast DDS* is already installed in the system, the *Agent* will
    look for it and use it. Otherwise, it will be automatically downloaded together
    with the *Agent* application.

    If willing to install *eProsima Fast DDS*, follow the instructions provided in the
    installation guide of the `eProsima Fast DDS documentation <https://fast-dds.docs.eprosima.com/en/latest/>`_.

eProsima Micro XRCE-DDS Gen
^^^^^^^^^^^^^^^^^^^^^^^^^^^

In order to compile the code generation tool *Micro XRCE-DDS Gen*,
the following packages need to be installed in the system.

Java JDK
    The JDK is a development environment for building applications and components using the Java language.
    Download and install it following the steps provided in the
    `Oracle website <https://www.oracle.com/java/technologies/javase-downloads.html>`_.

Gradle
    `Gradle <https://gradle.org/install/>`_ is an open-source build automation tool.

Windows
^^^^^^^

Microsoft Visual C++ 2015 or 2017
    *eProsima Micro XRCE-DDS* is supported on Windows over the Microsoft Visual C++ 2015 and 2017 frameworks.

