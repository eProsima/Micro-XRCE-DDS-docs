.. eProsima Micro XRCE-DDS documentation master file.

.. _microxrcedds_doc:


eProsima Micro XRCE-DDS
-----------------------

*eProsima Micro XRCE-DDS* is a software solution which allows to communicate eXtremely Resource Constrained Environments (XRCEs) with an existing DDS network.
This implementation complies with the specification proposal, "DDS for eXtremely Resource Constrained Environments" submitted to the Object Management Group (OMG) consortium.

*Micro XRCE-DDS* implements a client-server protocol to enable resource-constrained devices (clients) to take part in DDS communications.
*Micro XRCE-DDS Agent* (server) makes possible this communication.
The *Micro XRCE-DDS Agent* acts on behalf of the *Micro XRCE-DDS Clients* and enables them to take part as DDS publishers and/or subscribers in the DDS Global Data Space.

*Micro XRCE-DDS* provides both, a plug and play *Micro XRCE-DDS Agent* and an API layer which allows you to implement your *Micro XRCE-DDS Clients*.

.. image:: images/xrcedds_architecture.png

Installation
~~~~~~~~~~~~
To install *Micro XRCE-DDS*, follow the instructions of :ref:`installation_label` page.

User manual
~~~~~~~~~~~
To test *Micro XRCE-DDS* in your computer, you can follow the :ref:`quickstart_label` instructions.
This page shows how to create a simple publisher as a XRCE client that send topics into the DDS World.

Additionally, there is an interactive example called :ref:`shapes_demo_label` that allow you to create entities and
to send/receive topics by instructions given by command line.
This example is useful to understand how XRCE protocol works along to the DDS World.

To create your own client, you can follow the instructions of :ref:`getting_started_label` page.
This is a tutorial that describe briefly how *Micro XRCE-DDS* API is and how it works.

To know more about *Micro XRCE-DDS*, you can read the corresponding parts to the :ref:`micro_xrce_dds_client_label` or the :ref:`micro_xrce_dds_agent_label`.
If you are interesting in how XRCE works, read :ref:`entities_label` and :ref:`operations_label` pages.

Micro XRCE-DDS Gen
~~~~~~~~~~~~~~~~~~
To create a serialization/deserialization topic code for *Micro XRCE-DDS Client* and make easy the built of examples using thoses topics, there is a tool called `microxrceddsgen`.
Information about this tool can be found in :ref:`microxrceddsgen_label` page.

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
   client
   gen
   agent
   entities
   operations
   deployment
   optimization

.. toctree::
   :caption: Release Notes

   notes
