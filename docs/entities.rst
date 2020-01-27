.. _entities_label:

Entities
========

The protocol under *eProsima Micro XRCE-DDS* (XRCE), defines entities that have a direct correspondence with their analogous actors on *eProsima Fast RTPS* (DDS).
The entities manage the communication between *eProsima Micro XRCE-DDS Client* and the DDS Global Data Space.
Entities are stored in the *eProsima Micro XRCE-DDS Agent* and the *eProsima Micro XRCE-DDS Client* can create, use and destroy these entities.

The entities are uniquely identified by an ID called `Object ID`. The `Object ID` is the way a *Client* refers to them inside an *Agent*.
In most of the *Client* request operations is necessary to specify an ID referring to one of the *Client* entities stored in the *Agent*.

Type of Entities
----------------
These are the entities that the *Client* can interact with.

Participants
    Participants can hold any number of Publishers and/or Subscribers

Publisher
    Publishers can hold any number of data writers.

Subscriber
    Subscribers can hold any number of data readers.

Topic
    Topic data is the base of the communication. A Topic is composed of a name and a data type.

DataWriter
    This is the endpoint able to write Topic data.

DataReader
    This is the endpoint able to read Topic data.

Requester
    This is the endpoint able to write Request data and to read Reply data.

Replier
    This is the endpoint able to read Request data and to write Reply data.

This figure shows the entities hierarchy

.. image:: images/entities_hierarchy.svg
