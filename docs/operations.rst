.. _operations_label:

Operations
==========
Operations are the possible actions your *Micro XRCE-DDS Client* can ask to the *Micro XRCE-DDS Agent*.
Operations revolve around `entities`. :ref:`entities_label`.
*Micro XRCE-DDS Agent* will respond to all the requests with the status of the operation.

Types
-----
`Create session`
    `Create session` asks the agent to register a session.
    This is the first operation that you must request.
    If this operation fails or you don't request it, any of the following operations will not work.
    This operation will create the session corresponding to the *Client-Agent* connection.

`Delete session`
    Delete connection *Client-Agent* and remove all entities associated with this relation.
    After this operation, any other operation except `Create session` will fail.

`Create entity`
    A session can create all the entities it needs.
    There is a `Create entity` operation for each entity your session can handle.
    Each `Create entity` operation are related to an ID for its management.

`Delete entity`
    Analogous to create entities a session can to drop the entities on the *Agent*.
    To drop an entity you need to request a deletion of the entity to the *Agent* using the entity ID.

`Request Data`
    This operation configures how do you want to receive data, and the Agent will deliver it from the DDS to your *Client*.
    This data will be receive asynchronously, in accordance with the data delivery control setted in this operation.
    Reading data is done using a DataReader entity.

About XML Representation
------------------------
Creating a Topic, a DataWriter or a DataReader needs to be done using DDS XML configuration of the object to create.
That XML configuration follows the same rules as in *Fast RTPS*.

Creating a Participant is done using the ``default participant`` profile found already preconfigured in the agent.
Yo can change the Participant configuration changing the ``DEFAULT_FASTRTPS_PROFILES.xml`` file found along with de *Micro XRCE-DDS Agent* installation.
