.. eProsima Micro RTPS documentation master file.

.. _micrortps_doc:

eProsima Micro RTPS Documentation
=================================

.. image:: logo.png
   :height: 100px
   :width: 100px
   :align: left
   :alt: eProsima
   :target: http://www.eprosima.com/

*eProsima Micro RTPS* is a software solution which allows to communicate eXtremely Resource Constrained Environments (XRCEs) with an existing DDS network.
This implementation complies with the specification proposal, "eXtremely Resource Constrained Environments DDS (DDS-XRCE)" submitted to the Object Management Group (OMG) consortium.

*Micro RTPS* implements a client-server protocol to enable resource-constrained devices (clients) to take part in DDS communications. *Micro RTPS Agent* (server) makes possible this communication. The *Micro RTPS Agent* acts on behalf of the *Micro RTPS Clients* and enables them to take part as DDS publishers and/or subscribers in the DDS Global Data Space.

*Micro RTPS* provides both, a plug and play *Micro RTPS Agent* and an API layer which allows you to implement your *Micro RTPS Clients*.

.. image:: architecture.svg


Index
-----

.. toctree::
   :caption: Installation manual

   dependencies
   sources

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
