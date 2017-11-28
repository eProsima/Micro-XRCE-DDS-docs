Introduction
============

DDS-XRCE protocol
-----------------

*Micro RTPS* implements DDS-XRCE protocol specified in the "Extremely Resource Constrained Environments DDS (DDS-XRCE)" proposal submitted to the Object Management Group (OMG) consortium. DDS-XRCE protocol allows to communicate resource constrained clients with a larger DDS network. This communication is done using a client-server architecture, where the server(Agent) acts as an intermediary between clients and DDS Global Data Space.

DDS-XRCE protocol defines a messaging protocol between those Agents and Clients. This message exchange revolves around operations and their responses. Clients ask the Agents to run operations and the Agents reply with the result of those requested operations. Making use of those operations, Clients are able to create the DDS objects hierarchy necessary to publish and/or receive data from DDS. DDS objects are created and stored on the Agent side so the Clients can reuse them at will.

*Micro RTPS* implements the DDS-XRCE protocol using a *Micro RTPS Agent* as server and providing a C API for developing your own *Micro RTPS Clients*. *Micro RTPS Agent* uses *Fast RTPS* to reach DDS Global Data Space.

FastRTPS
^^^^^^^^

FastRTPS is a C++ implementation of the RTPS (Real Time Publish Subscribe) protocol, which provides publisher-subscriber communications over unreliable transports such as UDP, as defined and maintained by the Object Management Group (OMG) consortium. RTPS is also the wire interoperability protocol defined for the Data Distribution Service (DDS) standard, again by the OMG.

For deeper information please refer to the official documentation: `eProsima Fast RTPS <http://eprosima-fast-rtps.readthedocs.io>`_

Operations
----------

*Micro RTPS* communication between Client and Agent is based upon operations and responses. Clients request Operations to the Agent. The Agent will process the received operations and generate responses with the result of these operations. Clients can process the responses to know how an operation request has ended in the Agent.

In your *Micro RTPS Clients* you cant request a variety of operations to the *Micro RTPS Agent*:

* Create Client.
* Create Entities.
* Delete Entities.
* Write Data.
* Read Data.

First of all the Agent must know about any *Micro RTPS Client*. This is done sending a Create Client operation from the Client to the Agent, which will register the *Micro RTPS Client*. Once registered, you can request operations from your *Micro RTPS Client*. If you don't register the *Micro RTPS Client*, all the operations coming from that *Micro RTPS Client* will be refused by the *Micro RTPS Agent*.

The communication with DDS is handled by *Micro RPS Agent* using *Micro RTPS Entities*. The Client can create and query Entities using operations.

Create Entities operation is the operation you use to create *Micro RTPS entities* on the *Micro RTPS Agent*. Each one of these *Micro RTPS Entities* corresponds with a *Fast RTPS* object. The *Micro RTPS Entities* you can create are:

* Participants.
* Publisher.
* Subscriber.
* Topic.
* DataWriter.
* DataReader.

For sending and receiving data from/to DDS, *Micro RTPS Client* has access to two operations: Write Data and Read Data. An already created DataWriter or DataReader on the Agent handle these write/read operations.

If you want to remove any *Micro RTPS Entities* from the *Micro RTPS Agent* you use Delete operation.

For further information:
    * :ref:`operations_label`.
    * :ref:`entities_label`.

Topic Type
----------

The data sent from the Client to the DDS Global data Space uses the same principles as in *Fast RTPS*.
On the DDS side you use Interface Definition Language (IDL) to define your own type. *Micro RTPS Client* must know that type.
You need to add that type implementation on your *Micro RTPS Client* providing serialization and deserialization code. The type sent or received by *Micro RTPS Client* should match that one used on the DDS Side. For sending and receiving any topic from your *Micro RTPS Client* you should use Write Data and Read Data operations.
