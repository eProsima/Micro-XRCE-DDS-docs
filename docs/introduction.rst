Getting Started
===============

The Micro RTPS protocol
-----------------------

XRCE-DDS protocol allows to communicate resource constrained clients with a DDS Global Data Space. This communication is achieve using an Agent as an intermediate.

XRCE-DDS protocol defines the communication between those Agents and Clients. This communication is based on operations and responses,
the Client requests the Agent to run operations and the Agent responds accordingly to the result of those operations.

Combining those operations, Clients are able to create the DDS objects necessary to publish or receive data from DDS. Those DDS objects are
created on the Agent side so the Clients can reuse them at will.

*Micro RTPS* implements the XRCE-DDS protocol using a *Micro RTPS Agent* as server and providing a C API for developing your own *Micro RTPS Clients*. *Fast RTPS* is used by the *Micro RTPS Agent* to reach DDS Global Data Space.

FastRTPS
^^^^^^^^

FastRTPS is a C++ implementation of the RTPS (Real Time Publish Subscribe) protocol, which provides publisher-subscriber communications over unreliable transports such as UDP, as defined and maintained by the Object Management Group (OMG) consortium. RTPS is also the wire interoperability protocol defined for the Data Distribution Service (DDS) standard, again by the OMG.

For deeper information please refer to the official documentation: `eProsima Fast RTPS <http://eprosima-fast-rtps.readthedocs.io>`_

Operations
----------

Your *Micro RTPS Clients* are able to call a variety of *Micro RTPS Agent* operations:

* Create client.
* Create entities.
* Delete entities.
* Write data.
* Read data.

First of all the *Micro RTPS Client* must be known by the Agent. You do this by sending a create client operation to the Agent, which will register the *Micro RTPS Client*. Once registered, you can request operations from your *Micro RTPS Client*. If you dont register the *Micro RTPS Client*, all the operations coming from that *Micro RTPS Client* will be refused by the *Micro RTPS Agent*.

Create entities operation has multiple target possibilities. It is the operation you use to create *Micro RTPS entities* on the *Micro RTPS Agent*. Each one of these *Micro RTPS Entities* has an equivalent on *Fast RTPS* object. The *Micro RTPS Entities* you can create are:

* Participants.
* Publisher.
* Subscriber.
* Topic.
* Data Writer.
* Data reader.

For sending and receiving that from/to DDS, *Micro RTPS Client* has access to two operations: Write and Read. Those operations are handled by an already created Data Writer or a Data Reader respectively.

If you want to remove any *Micro RTPS Entities* from the *Micro RTPS Agent* you use Delete operation.

For further information on this topic please check :ref:`operations_label`.

Topic Type
----------

The data sent from the Client to the DDS Global data Space uses the same principles as in *Fast RTPS*.
On the DDS side you use Interface Definition Language (IDL) to define your own type. That type must be known by the *Micro RTPS Client*.
You need to implement that type on your *Micro RTPS Client* providing serialization and deserialization code. The type sent or received by *Micro RTPS Client* should match that one used on the DDS Side.