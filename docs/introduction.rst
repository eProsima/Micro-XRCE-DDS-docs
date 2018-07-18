.. _user:

Introduction
============

DDS-XRCE protocol
-----------------

*Micro RTPS* implements DDS-XRCE protocol specified in the "eXtremely Resource Constrained Environments DDS (DDS-XRCE)" proposal submitted to the Object Management Group (OMG) consortium.
DDS-XRCE protocol allows to communicate resource constrained clients with a larger DDS network.
This communication is done using a client-server architecture, where the server (Agent) acts as an intermediary between clients and DDS Global Data Space.

DDS-XRCE protocol defines a messaging protocol between those Agents and clients.
This message exchanged revolves around operations and their responses.
Clients ask the Agents to run operations and the Agents reply with the result of those requested operations.
Making use of those operations, clients are able to create the DDS entities hierarchy necessary to publish and/or receive data from DDS.
DDS entities are created and stored on the Agent side so the clients can reuse them at will.

*Micro RTPS* implements the DDS-XRCE protocol using a *Micro RTPS Agent* as server and providing a C API for developing your own clients.
*Micro RTPS Agent* uses *Fast RTPS* to reach the DDS Global Data Space.

.. image:: images/architecture.svg

Fast RTPS
`````````

*eProsima Fast RTPS* is a C++ implementation of the RTPS (Real-Time Publish-Subscribe) protocol,
which provides publisher-subscriber communications over unreliable transports such as UDP,
as defined and maintained by the Object Management Group (OMG) consortium.
RTPS is also the wire interoperability protocol defined for the Data Distribution Service (DDS) standard, again by the OMG.

For deeper information please refer to the official documentation: `eProsima Fast RTPS <http://eprosima-fast-rtps.readthedocs.io>`_

Operations
----------

*Micro RTPS* communication between Client and Agent is based upon :ref:`operations_label` and responses.
Clients request operations to the Agent.
The Agent will process the received operations and generate responses with the result of these operations.
Clients may process the responses to know how an operations request has ended in the Agent.

*Micro RTPS Client* can request a variety of operations to the *Micro RTPS Agent*:

* Create Client.
* Delete Client.
* Create Entity.
* Delete Entity.
* Read Data.

First of all the Agent must know about any *Micro RTPS Client*.
This is done by sending a `Create client` operations from the Client to the Agent, which will register the *Micro RTPS Client*.
Once registered, you can request operations from your *Micro RTPS Client*.
If you don't register the *Micro RTPS Client*, all the Operations coming from that *Micro RTPS Client* will be refused by the *Micro RTPS Agent*.

The communication with DDS is handled by *Micro RPS Agent* using :ref:`entities_label`. The Client can create and query entities using operations.
    .

`Create entity` Operation is the request you use to create entities on the *Micro RTPS Agent*.
Each one of these entities corresponds with a *Fast RTPS* object. The entities you can create are:

* Participants.
* Topics.
* Publisher.
* Subscriber.
* DataWriter.
* DataReader.

For sending and receiving data from/to DDS, *Micro RTPS Client* has access to two operations: Write Data and Read Data.
An already created DataWriter or DataReader on the Agent handle these write/read operations.

If you want to remove any *Micro RTPS Entities* from the *Micro RTPS Agent* you use `Delete entity` operations.
Also, if you want to detach an *Micro RTPS Client* from the Agent, you can use the analogous `Delete entity` operations for clients.

Topic Type
----------

The data sent by the Client to the DDS Global data Space uses the same principles as in *Fast RTPS*.
On the DDS side you use Interface Definition Language (IDL) to define your own type.
*Micro RTPS Client* must know that type.
Having your type defined as IDL we provide you with :ref:`micrortpsgen_label`.
It is a tool able to generate a compatible Type that your *Micro RTPS Client* can use to send and receive that type of data.
The type sent or received by *Micro RTPS Client* should match that one used on the DDS Side.
For sending and receiving any Topic from your *Micro RTPS Client* you should use Write Data and Read Data operations.
