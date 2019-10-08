.. eProsima Micro XRCE-DDS documentation master file.

.. _microxrcedds_doc:


eProsima Micro XRCE-DDS
=======================

*eProsima Micro XRCE-DDS* is a software solution which allows communicating eXtremely Resource Constrained Environments (XRCEs) with an existing DDS network.
This implementation complies with the specification proposal, "DDS for eXtremely Resource Constrained Environments" submitted to the Object Management Group (OMG) consortium.

*eProsima Micro XRCE-DDS* implements a client-server protocol to enable resource-constrained devices (clients) to take part in DDS communications.
*eProsima Micro XRCE-DDS Agent* (server) makes possible this communication acting on behalf of the *eProsima Micro XRCE-DDS Clients*
and enables them to take part as DDS publishers and/or subscribers in the DDS Global Data Space.

*eProsima Micro XRCE-DDS* provides both, a plug and play *eProsima Micro XRCE-DDS Agent* and an API layer which allows you to implement your *eProsima Micro XRCE-DDS Clients*.

.. image:: images/xrcedds_architecture.png

Main Features
-------------

High performance.
    Uses a static low-level serialization library `(eProsima Micro CDR) <https://github.com/eProsima/Micro-CDR>`_ that serialize in `XCDR <https://www.omg.org/spec/DDS-XTypes/1.2/PDF>`_.

Low resouces.
    The client library is dynamic memory free and static memory free.
    This means that the only memory charge is due to the stack growth.
    It can manage a simple publisher/subscriber with less of 2KB of RAM.
    Besides, the client is built with a *profiles* concept, that allows adding or removing functionality to the library changing the binary size.

Multi-platform.
    The OS dependencies are treated as pluggable modules.
    The user can easily implement his platform modules to *eProsima Micro XRCE-DDS Client* library in his specific platform.
    By default, the project can run over `Linux`, `Windows` and `Nuttx`.

Compiler dependencies free.
    The client library uses pure c99 standard.
    No `C` compiler extensions are used.

Free and Open Source.
   The client library, the agent executable, the generator tool and internal dependencies as *eProsima Micro CDR* or *eProsima Fast RTPS* are all of them free and open source.

Easy to use.
    The project comes with examples of a publisher and a subscriber,
    an example of how *eProsima Micro XRCE-DDS* is deployed into a stage and
    an interactive demo that can be used with the `ShapesDemo <https://github.com/eProsima/ShapesDemo>`_ with the purpose of understanding the DDS-XRCE protocol and making tests.
    The client API is thoroughly explained, and a guided example of how to create your client application is distributed as part of the documentation.

Implementation of the DDS-XRCE standard.
    `DDS-XRCE <https://www.omg.org/spec/DDS-XRCE/1.0/Beta1/PDF>`_ is a standard communication protocol of OMG consortium
    focused on communicating eXtremely Resource Constrained Environments with the DDS world.

Best effort and reliable communication.
    *eProsima Micro XRCE-DDS* supports both, *best effort* for fast and light communication and *reliable* when the communication reliability is needed.

Pluggable tansport layer.
    *eProsima Micro XRCE-DDS* is not built over a specific transport protocol as *Serial* or *UDP*.
    It is agnostic about the transport used, and give the user the possibility of implementing easily his tailored transport.
    By default, *UPD*, *TCP*, and *Serial* transports are provided.

Commercial support
    Available at `support@eprosima.com`

Installation
------------

To install *eProsima Micro XRCE-DDS*, follow the instructions of :ref:`installation_label` page.

User manual
-----------

To test *eProsima Micro XRCE-DDS* in your computer, you can follow the :ref:`quickstart_label` instructions.
This page shows how to create a simple publisher as a XRCE client that send topics into the DDS World.

Additionally, there is an interactive example called :ref:`shapes_demo_label` that allow you to create entities and
to send/receive topics by instructions given by command line.
This example is useful to understand how XRCE protocol works along to the DDS World.

To create your own client, you can follow the instructions of :ref:`getting_started_label` page.
This is a tutorial that describe briefly how *eProsima Micro XRCE-DDS* API is and how it works.

To know more about *eProsima Micro XRCE-DDS*, you can read the corresponding parts to the :ref:`micro_xrce_dds_client_label` or the :ref:`micro_xrce_dds_agent_label`.
If you are interested in how XRCE works, read :ref:`entities_label` and :ref:`operations_label` pages.

eProsima Micro XRCE-DDS Gen
---------------------------

To create a serialization/deserialization topic code for *eProsima Micro XRCE-DDS Client* and make easy the built of examples using thoses topics, there is a tool called `microxrceddsgen`.
Information about this tool can be found in :ref:`microxrceddsgen_label` page.

Installation manual
===================

.. toctree::
   :caption: Installation manual

   dependencies
   installation

User manual
===========

.. toctree::
   :caption: User manual 

   introduction
   quickstart
   getting_started
   shapes_demo
   client
   gen
   agent
   entities
   operations
   deployment
   optimization
   transport
   p2p

Release notes
=============

.. toctree::
   :caption: Release notes

   notes
