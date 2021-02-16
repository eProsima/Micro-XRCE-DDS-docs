.. _user:

Introduction
============

DDS-XRCE protocol
-----------------

*eProsima Micro XRCE-DDS* implements the `DDS-XRCE protocol <https://www.omg.org/spec/DDS-XRCE/1.0/Beta1/PDF>`_
specified in the `DDS for eXtremely Resource Constrained Environments` standard as defined and maintained by the
`Object Management Group (OMG)` consortium.

This protocol allows resource-constrained devices to communicate with a larger `DDS (Data Distribution Service)` network.
This communication is based on a client-server architecture,
where the server (XRCE Agent) acts as an intermediary between the clients (XRCE Clients) and the `DDS Global Data Space`.

The *DDS-XRCE protocol* defines the wire protocol between XRCE Agents and XRCE Clients.
The messages exchanged revolve around operations and their responses.
The XRCE Clients request the XRCE Agents to run operations, and the XRCE Agents reply with the result of the requested operations.
Making use of these operations, the XRCE Clients can create the DDS entities' hierarchy necessary to publish and/or receive data from DDS.
The DDS entities are created and stored on the XRCE Agent's side so the XRCE Clients can reuse them at will with subsequent operations requests.

*eProsima Micro XRCE-DDS* implements the DDS-XRCE protocol using an XRCE Agent as a broker and providing a C API for
developing XRCE Clients applications.
The Micro XRCE-DDS Agent uses *eProsima Fast DDS* to reach the DDS Global Data Space.

.. At this point one can already mention something regarding the 3 possible Agent middlewares.

.. image:: images/xrcedds_architecture.png

Fast DDS
`````````
*eProsima Fast DDS* is a C++ implementation of the `DDS standard <https://www.omg.org/spec/DDS/About-DDS/>`_ and makes underneath use of the
`RTPS (Real-Time Publish-Subscribe)` wire protocol, which provides publisher-subscriber communications over unreliable transports such as UDP.
Both the DDS specification and the RTPS protocol are defined and maintained by the `OMG` consortium.

For more extensive information, please refer to the official documentation: `eProsima Fast DDS <http://eprosima-fast-dds.readthedocs.io>`_.

Operations and entities
-----------------------
The communication between XRCE Clients and XRCE Agents is based upon :ref:`operations_label` and responses.
Clients request operations to the Agents, which will generate responses with the result of these operations.
Clients may process the responses to know how an operations request has ended in the Agent.

XRCE Clients can request a variety of operations to an XRCE Agent:

* Create and delete sessions.
* Create and delete entities.
* Write and read data.

The communication starts with a handshake necessary to make the Agent acknowledge that a Client
is present in the network.
This happens via a *Create session* operation forwarded from the Client to the Agent,
with which the latter will consequenlty register the Client.
Without registering a session, all the subsequent operations sent to the Agent will be refused.
Once registered, the Client can request operations to the Agent, through which it can create and query entities.
The communication with DDS is handled by the Agent using these :ref:`entities_label`.

The `Create entity` operation is the request used to create entities in the `Agent`.
Each of these entities corresponds to an *eProsima Fast DDS* object.
The entities you can create are:

* Participants.
* Topics.
* Publisher.
* Subscriber.
* DataWriter.
* DataReader.

For sending and receiving data from/to DDS, the Client has access to the *DataWriter* and *DataReader* entities.
These entities handle the writing/reading operations.
For sending and receiving any topic, the *Write Data* and *Read Data* operations must be used.

If you want to remove any entity from the Agent, you can use the *Delete entity* operation.
Also, to log out a Client session from the Agent, you can use the *Delete session* operation.

Topic Type
----------
The data sent by the Client to the DDS Global Data Space closely resembles that of *eProsima Fast DDS*.
The `Interface Definition Language (IDL) <https://www.omg.org/spec/IDL/4.2/PDF>`_ is used to define the type and must be known by the Client.
Having the type defined as `IDL`, we provide the :ref:`microxrceddsgen_label` tool.
This tool can generate a compatible type that the XRCE Client can use to send and receive.
The type has to match the one used on the DDS Side.
