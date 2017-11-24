.. _operations_label:

Operations
==========

Operations are the possible actions your *Micro RTPS Client* can request to the *Micro RTPS Agent*. Operations revolve around *Micro RTPS Entities*. *Micro RTPS Agent* will respond to all the requests with the status of the Operation.

Types
-----
Create client
    Create client asks the *Micro RTPS Agent* to register your Client on the Agent. This is the firs operation that you should request. If this operation fails or you dont request it any of the following Operations will work. Operations are made per Client basis, so the Agent needs your Client registered before continue processing request from it.
Create entities
    Your Client can create all the *Micro RTPS Entities* it needs. There is a create entity operation for each Entities your Client can handle. All the create entities operation returns the new Entity ID.
Delete entities
    Analogous to create entities your Client need to delete the entities on the Agent. This is done requesting a deletion of an Entity to the Agent using the entity ID.
Write data
    Using the data reader entity ID you can ask the Agent to write your data into DDS Global Data Space.
Read data
    Reading data is done in asynchronous way. You configure how do you want to receive data and the Agent will deliver it from DDS to your Client following the read configuration you request. Reading data is done using a Data Reader Entity.


About XML Representation
------------------------

Creating a topic, a data writer or a data reader needs to be done using DDS XML configuration of the object to create. That XML configuration follows the same rules as in *Fast RTPS*.

Creating a participant is done using the Default Participant profile found already preconfigured in the Agent. Yo can change the participant configuration changing the "DEFAULT_FASTRTPS_PROFILES.xml" file found along with de *Micro RTPS Agent* installation.