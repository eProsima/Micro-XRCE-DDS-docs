.. _entities_label:

Micro RTPS Entities
===================

*Micro RTPS Entities* manage the communication between  *Micro RTPS Client* and the DDS Global Data Space. *Micro RTPS Entities* are part of the *Micro RTPS Agent*. *Micro RTPS Clients* can create, use and destroy these *Micro RTPS Entities*, but they are stored in the *Micro RTPS Agent* per Client basis. *Micro RTPS Entities* have a direct correspondence with their analogous actor on *Fast RTPS*.

*Micro RTPS Entities* are uniquely identified by an ID. Entities ID is the way a Client refers to them inside an Agent. In most of the Client request Operations you need to specify an ID referring to one of the Client Entities stored in the Agent.

Type of Entities
----------------

These are the *Micro RTPS Entities* that you can interact in your Client.

Participants
    Micro RTPS Participants can hold any number of Publishers and/or Subscribers

Publisher
    Publishers can hold any number of data readers.

Subscriber
    Subscribers can hold any number of data readers.

Topic
    Topic data is the base of the communication. A Topic is composed of a name and a data type.

DataWriter
    This is the endpoint able to write Topic data.

DataReader
    This is the endpoint able to read Topic data.

This figure shows the Entities hierarchy

.. image:: micrortps_entities_hierarchy.svg
