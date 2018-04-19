.. _operations_label:

Operations
==========
Operations are the possible actions your *Micro RTPS Client* can ask to the *Micro RTPS Agent*.
Operations revolve around *Micro RTPS Entities*.
*Micro RTPS Agent* will respond to all the requests with the status of the Operation.
The *Micro RTPS Client* implementation will treat this operations synchronously.

Types
-----
Create Client
    Create Client asks the *Micro RTPS Agent* to register your *Client* on the *Agent*.
    This is the first Operation that you must request.
    If this Operation fails or you don't request it, any of the following Operations will work.
    Operations are executed per *Client* basis, so the *Agent* needs your *Client* registered before continue processing request from it.
    This operations will create the Session object corresponding to the *Client-Agent* connection.

Create Entities
    Your *Client* can create all the *Micro RTPS Entities* it needs.
    There is a Create Entity Operation for each Entities your *Client* can handle.
    All the Create Entities operations are related to a ID for its management.

Delete Entities
    Analogous to create entities your *Client* needs to drop the entities on the *Agent*.
    To drop an Entity you need to request a deletion of an Entity to the *Agent* using the entity ID.
    As a *Client* is an entitiy itself, you always can delete a client as another entity, by its ID.

Read Data
    This operation configures how do you want to receive data, and the Agent will deliver it from the DDS to your *Client*.
    This data will be receive asynchronously, in accordance with the data delivery control setted in this Operation.
    Reading data is asynchronous.
    Reading data is done using a DataReader Entity.

About XML Representation
------------------------
Creating a Topic, a DataWriter or a DataReader needs to be done using DDS XML configuration of the object to create.
That XML configuration follows the same rules as in *Fast RTPS*.

Creating a Participant is done using the Default Participant profile found already preconfigured in the *Agent*.
Yo can change the Participant configuration changing the "DEFAULT_FASTRTPS_PROFILES.xml" file found along with de *Micro RTPS Agent* installation.
