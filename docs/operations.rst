.. _operations_label:

Operations
==========
Operations are the possible actions your *eProsima Micro XRCE-DDS Client* can ask to the *eProsima Micro XRCE-DDS Agent*.
Operations revolve around :ref:`entities_label`.
*eProsima Micro XRCE-DDS Agent* will respond to all the requests with the status of the operation.

Types
-----
`Create session`
    `Create session` asks the *Agent* to register a session.
    It is the first operation that you must request.
    If this operation fails or you don't request it, any of the following operations will not work.
    This operation will create the session corresponding to the *Client-Agent* connection.

`Delete session`
    Delete connection *Client-Agent* and remove all entities associated with this relation.
    After this operation, any other operation except `Create session` will fail.

`Create entity`
    A session can create all the entities it needs.
    There is a `Create entity` operation for each entity your session can handle.
    Each `Create entity` operation is related to an ID for its management.

`Delete entity`
    Analogous to create entities a session can drop the entities on the *Agent*.
    To drop an entity, you need to request the deletion of the entity to the *Agent* using the entity ID.

`Request Data`
    This operation configures how do you want to receive data, and the *Agent* will deliver it from the DDS to your *Client*.
    This data will be received asynchronously, according to the data delivery control set in this operation.
    Reading data is done using a `DataReader` entity.

About XML Representation
------------------------
`Participats`, `Topics`, `DataWriters` and `DataReaders` creation needs to be done using the DDS XML configuration of the object to create.
That XML configuration follows the same rules as in *eProsima Fast RTPS*.
