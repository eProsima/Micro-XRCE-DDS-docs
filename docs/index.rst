.. eProsima Micro RTPS documentation master file.

.. _micrortps_doc:


eProsima Micro RTPS
-------------------

*eProsima Micro RTPS* is a software solution which allows to communicate eXtremely Resource Constrained Environments (XRCEs) with an existing DDS network.
This implementation complies with the specification proposal, "eXtremely Resource Constrained Environments DDS (DDS-XRCE)" submitted to the Object Management Group (OMG) consortium.

*Micro RTPS* implements a client-server protocol to enable resource-constrained devices (clients) to take part in DDS communications.
*Micro RTPS Agent* (server) makes possible this communication.
The *Micro RTPS Agent* acts on behalf of the *Micro RTPS Clients* and enables them to take part as DDS publishers and/or subscribers in the DDS Global Data Space.

*Micro RTPS* provides both, a plug and play *Micro RTPS Agent* and an API layer which allows you to implement your *Micro RTPS Clients*.

.. image:: architecture.svg

Installation
~~~~~~~~~~~~
To install *Micro RTPS*, follow the instructions of :ref:`installation_label` page.

User manual
~~~~~~~~~~~
To test *Micro RTPS* in your computer, you can follow the :ref:`quickstart_label` instructions.
This page shows how to create a simple publisher as a XRCE client that send topics into the DDS World.

Additionally, there is an interactive example called :ref:`shapes_demo_label` that allow you to create XRCE objects and
to send/receive topics by the instructions given by command line.
This example is useful to understand how XRCE protocol works along to the DDS World.

To create your own client, you can follow the the instructions of :ref:`getting_started_label` page.
This is a tutorial that describe briefly how *Micro RTPS* API is and how it works.

To know more about *Micro RTPS*, you can read the corresponding parts to the :ref:`micro_rtps_client_label` or the :ref:`micro_rtps_agent_label`.
If you are interesting in how XRCE works, read :ref:`entities_label` and :ref:`operations_label` pages.

Micro RTPS Gen
~~~~~~~~~~~~~~
To create a serialization/deserialization topic code for *Micro RTPS Client* and make easy the built of examples using thoses topics, there is a tool called `micrortpsgen`.
Information about this tool can be found in :ref:`micrortpsgen_label` page.

Index
-----

.. toctree::
   :caption: Installation manual

   dependencies
   installation

.. toctree::
   :caption: User Manual

   introduction
   quickstart
   getting_started
   shapes_demo
   agent
   client
   entities
   operations

.. toctree::
   :caption: Micro RTPS Gen

   micrortpsgen

.. toctree::
   :caption: Release Notes

   notes
