.. eProsima Micro XRCE-DDS documentation master file.

.. _microxrcedds_doc:

eProsima Micro XRCE-DDS
=======================

*eProsima Micro XRCE-DDS* is a software solution that allows communicating eXtremely Resource Constrained Environments (XRCEs) with an existing DDS network.
This implementation complies with the specification of the [eXtremely Resource Constrained Environments DDS (DDS-XRCE)](https://www.omg.org/spec/DDS-XRCE/)
protocol as defined and maintained by the Object Management Group (OMG) consortium.

The *eprosima Micro XRCE-DDS* library implements a client-server protocol that enables resource-constrained devices (clients) to take part in DDS communications.
The *eProsima Micro XRCE-DDS Agent* (server) acts as a bridge to make this communication possible.
It acts on behalf of the *Micro XRCE-DDS Clients* by enabling them to take part to the DDS Global Data Space
as DDS publishers and/or subscribers. It also allows for Remote Procedure Calls, as defined by the `DDS-RPC standard <https://www.omg.org/spec/DDS-RPC/About-DDS-RPC/>`_, which implement a request/reply communication pattern.

*eProsima Micro XRCE-DDS* provides both a plug and play *eProsima Micro XRCE-DDS Agent* and an API layer which allows the user to implement the *eProsimaMicro XRCE-DDS Clients*.

.. image:: images/xrcedds_architecture.png

Main Features
-------------

High performance.
    The *eProsima Micro XRCE-DDS Client* uses a static low-level serialization library `(eProsima Micro CDR) <https://github.com/eProsima/Micro-CDR>`_ that serializes in `XCDR <https://www.omg.org/spec/DDS-XTypes/1.2/PDF>`_.

Low resources.
    The *Client* library is dynamic and static memory free, as the only memory footprint is due to the stack growth.
    It can manage a simple publisher/subscriber with less of 2 kB of RAM.
    Besides, the *Client* is built according to a *profiles* concept, allowing to add or remove functionalities to/from the library at the same time as changing the binary size.

Multi-platform.
    The OS dependencies are treated as pluggable modules, so that users can easily implement their platform-specific modules for the *eProsima Micro XRCE-DDS Client* library.
    By default, the project can run both over the standard Operating Systems `Linux` and `Windows`,
    and on the Real-Time Operating Systems `Nuttx`, `FreeRTOS` and `Zephyr`.

Compiler dependencies free.
    The *Client* library uses pure C99 standard.
    No `C` compiler extensions are used.

Free and Open Source.
   The *Client* library, the *Agent* executable, the generator tool and other internal dependencies as *eProsima Micro CDR* or *eProsima Fast DDS* are all free and open-source.

Easy to use.
    The project provides several `examples <https://github.com/eProsima/Micro-XRCE-DDS-Client/tree/master/examples>`_.
    This documentation guides the user step-by-step through some of them, namely how to create a *publisher/subscriber*, a *requester/replier* and a *Peer-to-Peer publiser/subscriber* *Client* example. An interactive demo can also be found to interact with the `ShapesDemo <https://github.com/eProsima/ShapesDemo>`_ application, useful to understand the DDS-XRCE protocol and to make tests.
    The *Client API* is thoroughly explained.

Implementation of the DDS-XRCE standard.
    `DDS-XRCE <https://www.omg.org/spec/DDS-XRCE/1.0/Beta1/PDF>`_ is a standard communication protocol by the OMG consortium
    focused on communicating eXtremely Resource Constrained Environments with the DDS world.

Best effort and reliable communication.
    *eProsima Micro XRCE-DDS* supports both *best-effort* and *reliable* communication modes. The first implements a fast and light communication, while the second ensures reliability independently of the transport layer used underneath.

Pluggable transport layer.
    *eProsima Micro XRCE-DDS* is not built over a specific transport protocol as *Serial* or *UDP*.
    It is agnostic about the transport used, and give the user the possibility of implementing the needed transport easily.
    By default, *UPD*, *TCP*, and *Serial* transports are provided. Also, an easy way to implement *Custom* transport is offered.

Commercial support
    Available at `support@eprosima.com`

Installation
------------

To install *eProsima Micro XRCE-DDS*, follow the instructions provided in the :ref:`installation_label` page.

User manual
-----------

To test *eProsima Micro XRCE-DDS*, follow the :ref:`quickstart_label` instructions.
This page shows how to create simple *publisher/subscriber* and *requester/replier* applications.

Additionally, there is an interactive example called :ref:`shapes_demo_label` allowing users to create entities and
to send/receive topics by instructions given by the command line.
This example is useful to understand how the DDS-XRCE protocol interfaces with the DDS World.

To learn how to handle all the ingredients needed to create a *Client*, carefully read the :ref:`getting_started_label` page.
This page describes how to use the *eProsima Micro XRCE-DDS* API in order to set up and run a *Client* application.

A generic introduction to the library can be found in the :ref:`user` page.
To know more about *Clients* and *Agents*, find detailed information in the :ref:`micro_xrce_dds_client_label` and :ref:`client_api_label` pages, and in the :ref:`micro_xrce_dds_agent_label` and :ref:`agent_api_label` pages respectively.

eProsima Micro XRCE-DDS Gen
---------------------------

To create a serialization/deserialization topic code for the *eProsima Micro XRCE-DDS Client*,
there is a tool called :code:`microxrceddsgen`.
Information about this tool can be found in the :ref:`microxrceddsgen_label` page.

Structure of the documentation
------------------------------

.. toctree::
   :caption: Installation manual
   :maxdepth: 1

   dependencies
   installation

.. toctree::
   :caption: User Manual
   :maxdepth: 1

   introduction
   quickstart
   getting_started
   shapes_demo
   client
   agent
   api
   gen
   optimization
   transport
   p2p
   time_sync
   docker

.. toctree::
   :caption: Release Notes
   :maxdepth: 0

   notes
