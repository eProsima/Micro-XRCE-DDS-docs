.. _entities_label:

Micro RTPS Entities
===================

*Micro RTPS Entities* manage the communication between  *Micro RTPS Client* and the DDS Global Data Space. *Micro RTPS Entities* are part of the *Micro RTPS Agent*. These *Micro RTPS Entities* are created, used and destroyed by *Micro RTPS Clients* and stored in the *Micro RTPS Agent* per Client. *Micro RTPS Entities* have a direct correspondence with their analogous actor on *Fast RTPS*.

*Micro RTPS Entities* are uniquely identified by an ID. Entities ID is the way a Client refers to them inside an Agent. In most of the Client request operations you need to specify an ID referring to one of the Client Entities stored in the Agent.

Type of Entities
----------------

These are the *Micro RTPS Entities* that you can interact with withing your Client.

Participants
    Micro RTPS Participants can hold any number of Publishers and/or Subscribers

Publisher
    Publishers can hold any number of data readers.

Subscriber
    Subscribers can hold any number of data readers.

Topic
    Communication is based on Topic data. A Topic is a definition of a topic name and a topic data type.

Data Writer
    This is the endpoint able to write topic data.

Data reader
    This is the endpoint able to read topic data.

This is the Entities hierarchy:

TODO: Grafico jerarquia