.. _user:

Introduction
============

DDS-XRCE protocol
-----------------

*Micro RTPS* implements DDS-XRCE protocol specified in the "eXtremely Resource Constrained Environments DDS (DDS-XRCE)" proposal submitted to the Object Management Group (OMG) consortium. DDS-XRCE protocol allows to communicate resource constrained clients with a larger DDS network. This communication is done using a client-server architecture, where the server (Agent) acts as an intermediary between clients and DDS Global Data Space.

DDS-XRCE protocol defines a messaging protocol between those Agents and Clients. This message exchange revolves around Operations and their responses. Clients ask the Agents to run Operations and the Agents reply with the result of those requested Operations. Making use of those Operations, Clients are able to create the DDS objects hierarchy necessary to publish and/or receive data from DDS. DDS objects are created and stored on the Agent side so the Clients can reuse them at will.

*Micro RTPS* implements the DDS-XRCE protocol using a *Micro RTPS Agent* as server and providing a C API for developing your own *Micro RTPS Clients*. *Micro RTPS Agent* uses *Fast RTPS* to reach the DDS Global Data Space.

FastRTPS
^^^^^^^^

FastRTPS is a C++ implementation of the RTPS (Real-Time Publish-Subscribe) protocol, which provides publisher-subscriber communications over unreliable transports such as UDP, as defined and maintained by the Object Management Group (OMG) consortium. RTPS is also the wire interoperability protocol defined for the Data Distribution Service (DDS) standard, again by the OMG.

For deeper information please refer to the official documentation: `eProsima Fast RTPS <http://eprosima-fast-rtps.readthedocs.io>`_

Operations
----------

*Micro RTPS* communication between Client and Agent is based upon Operations and responses. Clients request Operations to the Agent. The Agent will process the received Operations and generate responses with the result of these Operations. Clients can process the responses to know how an Operations request has ended in the Agent.

In your *Micro RTPS Clients* you can't request a variety of Operations to the *Micro RTPS Agent*:

* Create Client.
* Create Entities.
* Delete Entities.
* Write Data.
* Read Data.

First of all the Agent must know about any *Micro RTPS Client*. This is done by sending a Create Client Operations from the Client to the Agent, which will register the *Micro RTPS Client*. Once registered, you can request Operations from your *Micro RTPS Client*. If you don't register the *Micro RTPS Client*, all the Operations coming from that *Micro RTPS Client* will be refused by the *Micro RTPS Agent*.

The communication with DDS is handled by *Micro RPS Agent* using *Micro RTPS Entities*. The Client can create and query Entities using Operations.

Create Entities Operation is the request you use to create *Micro RTPS Entities* on the *Micro RTPS Agent*. Each one of these *Micro RTPS Entities* corresponds with a *Fast RTPS* object. The *Micro RTPS Entities* you can create are:

* Participants.
* Publisher.
* Subscriber.
* Topic.
* DataWriter.
* DataReader.

For sending and receiving data from/to DDS, *Micro RTPS Client* has access to two Operations: Write Data and Read Data. An already created DataWriter or DataReader on the Agent handle these write/read Operations.

If you want to remove any *Micro RTPS Entities* from the *Micro RTPS Agent* you use Delete Operation.

For further information:
    * :ref:`operations_label`.
    * :ref:`entities_label`.

Topic Type
----------

The data sent by the Client to the DDS Global data Space uses the same principles as in *Fast RTPS*.
On the DDS side you use Interface Definition Language (IDL) to define your own type. *Micro RTPS Client* must know that type. Having your type defined as IDL we provide you with (:ref:`micrortpsgen_label`). It is a tool able to generate a compatible Type that your *Micro RTPS Client* can use to send and receive that type of data. The type sent or received by *Micro RTPS Client* should match that one used on the DDS Side. For sending and receiving any Topic from your *Micro RTPS Client* you should use Write Data and Read Data Operations.
