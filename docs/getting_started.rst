.. _getting_started_label:

Getting started
===============
This page shows how to get started with the *Micro RTPS Client* development.
We will create a *Client* that can publish and subscribe a topic.
This tutorial has been extract from the examples found into ``examples/PublisherHelloWorld`` and ``examples/SubscriberHelloWorld``.

First, we need to have on the system:

 - :ref:`micro_rtps_client_label`.
 - :ref:`micro_rtps_agent_label`.
 - :ref:`micrortpsgen_label`.

Generate from the IDL
^^^^^^^^^^^^^^^^^^^^^^
We will use HelloWorld as our Topic whose IDL is the following: ::

    struct HelloWorld
    {
        unsigned long index;
        string message;
    };

In the *Client* we need to create an equivalent c type with its serialization/deserialization code.
This is done automatically by :ref:`micrortpsgen_label`: ::

    $ micrortpsgen -write-access-profile HelloWorld.idl

Because we want to write this topic into the DDS world, we have added the ``-write-access-option`` that will creates another file with functionality for writing topics.

Initialize a Session
^^^^^^^^^^^^^^^^^^^^
In the source example file, we include the generated type code, in order to have access to its serialization/deserialization functions along to the write function.
Also, we will specify the max buffer for the streams and its historical associated for the reliable streams.

.. code-block:: C

    #include "HelloWorldWriter.h"

    #define STREAM_HISTORY  8
    #define BUFFER_SIZE     MR_CONFIG_UDP_TRANSPORT_MTU * STREAM_HISTORY

First, we need to create a Session indicating the transport to use.
This time we are using UDP in localhost over the port 2018.
The agent must be configured for listening from udp at port 2018.

.. code-block:: C

    mrUDPTransport transport;
    if(!mr_init_udp_transport(&transport, "127.0.0.1", 2018))
    {
        printf("Error at create transport.\n");
        return 1;
    }

Next, we will a session that allow us interact with the agent:

.. code-block:: C

    mrSession session;
    mr_init_session(&session, &transport.comm, 0xABCDABCD);
    mr_set_topic_callback(&session, on_topic, NULL);
    if(!mr_create_session(&session))
    {
        printf("Error at create session.\n");
        return 1;
    }

The first function ``mr_init_session`` initializes the ``Session`` structure with the transport and the key (the session identifier for an agent).
The ``mr_set_topic_callback`` function is for registering the function ``on_topic`` which has been called when the client receive a topic.
Once the session has been initialized, we can send the first message for login the client in agent side: ``mr_create_session``.
This function will try to connect with the agent by ``CONFIG_MAX_SESSION_CONNECTION_ATTEMPTS`` attempts (the value can be configurable at ``client.config``).

Optionally, we also could add a status callback with the function ``mr_set_status_callback``, but for the purpose of this example we do not need it.

Once we have login the session successful, we can create the streams that we will use.
In this case, we will use two, both reliables, for input and output.

.. code-block:: C

    uint8_t output_reliable_stream_buffer[BUFFER_SIZE];
    mrStreamId reliable_out = mr_create_output_reliable_stream(&session, output_reliable_stream_buffer, BUFFER_SIZE, STREAM_HISTORY);

    uint8_t input_reliable_stream_buffer[BUFFER_SIZE];
    mrStreamId reliable_in = mr_create_input_reliable_stream(&session, input_reliable_stream_buffer, BUFFER_SIZE, STREAM_HISTORY);

In order to publish and/or subscribe a topic, we need to create a hierarchy of XRCE entities in the agent side.
These entities will be created from the client.

.. image:: images/entities_hierarchy.svg

Setup a Participant
^^^^^^^^^^^^^^^^^^^
For establishing DDS communication we need to create a participant entity for our *Client* in the *Agent*.
We can do this calling *Create participant* operation:

.. code-block:: C

    mrObjectId participant_id = mr_object_id(0x01, MR_PARTICIPANT_ID);
    const char* participant_ref = "default participant";
    uint16_t participant_req = mr_write_create_participant_ref(&session, reliable_out, participant_id, participant_ref, MR_REPLACE);

In any XRCE Operation that creates an entity, an ObjectId is necessary.
It is used to represent and manage the object in the *Client* side.
The reference is the identifier of a DDS entity in the *Agent* side.
Each operation, return a `request id`.
This identifier of the operation can be used later for assoaciated the status with the request.
In this case, we have only write the operation into the stream ``reliable_out``.
Later, in ``run_session`` function the stream will send the written data to the agent.

Creating  topics
^^^^^^^^^^^^^^^^
Once the Participant has been created, we can use *Create Topic* operation for register a topic entity within the participant.

.. code-block:: C

    mrObjectId topic_id = mr_object_id(0x01, MR_TOPIC_ID);
    const char* topic_xml = "<dds><topic><name>HelloWorldTopic</name><dataType>HelloWorld</dataType></topic></dds>";
    uint16_t topic_req = mr_write_configure_topic_xml(&session, reliable_out, topic_id, participant_id, topic_xml, MR_REPLACE);

As any other XRCE Operation used to create an entity, an ObjectId must be specify to represent the object.
The ``participant_id`` is the participant where the Topic will be registered.
In order to determine which topic will be used, an XML is sent to the agent for creating and defining the Topic in the DDS Global Data Space.
That definition consists of a name and a type.

Publishers & Subscribers
^^^^^^^^^^^^^^^^^^^^^^^^
Similar to Topic registration we can create publishers and subscribers entities.
We create a publisher or subscriber on a participant entity, so it is necessary to provide the ID of the Participant which will hold those publishers or subscribers.

.. code-block:: C

    mrObjectId publisher_id = mr_object_id(0x01, MR_PUBLISHER_ID);
    const char* publisher_xml = "<publisher name=\"MyPublisher\">";
    uint16_t publisher_req = mr_write_configure_publisher_xml(&session, reliable_out, publisher_id, participant_id, publisher_xml, MR_REPLACE);

    mrObjectId subscriber_id = mr_object_id(0x01, MR_SUBSCRIBER_ID);
    const char* subscriber_xml = "<subscriber name=\"MySubscriber\">";
    uint16_t subscriber_req = mr_write_configure_subscriber_xml(&session, reliable_out, subscriber_id, participant_id, subscriber_xml, MR_REPLACE);

DataWriters & DataReaders
^^^^^^^^^^^^^^^^^^^^^^^^^
Analogous to publishers and subscribers entities, we create the data writers and data readers entities.
These entities are responsible to send and receive the data.
Data writers are referred to a publisher, and data readers are referred to a subscriber.
The configuration about how these data readers and data writers works is contained in the xml.

.. code-block:: C

    mrObjectId datawriter_id = mr_object_id(0x01, MR_DATAWRITER_ID);
    const char* datawriter_xml = "<profiles><publisher profile_name=\"default_xrce_publisher_profile\"><topic><kind>NO_KEY</kind><name>HelloWorldTopic</name><dataType>HelloWorld</dataType><historyQos><kind>KEEP_LAST</kind><depth>5</depth></historyQos><durability><kind>TRANSIENT_LOCAL</kind></durability></topic></publisher></profiles>";
    uint16_t datawriter_req = mr_write_configure_datawriter_xml(&session, reliable_out, datawriter_id, publisher_id, datawriter_xml, MR_REPLACE);

    mrObjectId datareader_id = mr_object_id(0x01, MR_DATAREADER_ID);
    const char* datareader_xml = "<profiles><subscriber profile_name=\"default_xrce_subscriber_profile\"><topic><kind>NO_KEY</kind><name>HelloWorldTopic</name><dataType>HelloWorld</dataType><historyQos><kind>KEEP_LAST</kind><depth>5</depth></historyQos><durability><kind>TRANSIENT_LOCAL</kind></durability></topic></subscriber></profiles>";
    uint16_t datareader_req = mr_write_configure_datareader_xml(&session, reliable_out, datareader_id, subscriber_id, datareader_xml, MR_REPLACE);

Agent response
^^^^^^^^^^^^^^
In operations such as create a session, create an entity or request data from the *Agent*,
an status state is sent from the *Agent* to the *Client* indicating what happened.

For `Create session` or `Detele session` operations the status value is storage into the ``session.info.last_request_status``.
For the rest of the operations, the status are send to the input reliable stream ``0x80``, that is, the first input reliable stream created, with index 0.

The different status values that the agent can send to the client are the following:

.. code-block:: C

    MR_STATUS_OK
    MR_STATUS_OK_MATCHED
    MR_STATUS_ERR_DDS_ERROR
    MR_STATUS_ERR_MISMATCH
    MR_STATUS_ERR_ALREADY_EXISTS
    MR_STATUS_ERR_DENIED
    MR_STATUS_ERR_UNKNOWN_REFERENCE
    MR_STATUS_ERR_INVALID_DATA
    MR_STATUS_ERR_INCOMPATIBLE
    MR_STATUS_ERR_RESOURCES

These status, can be trated with the callback destined for it or by the ``run_session_until_status`` as we will see.

.. code-block:: C

    uint8_t status[6]; // we have 6 request to check.
    uint16_t requests[6] = {participant_req, topic_req, publisher_req, subscriber_req, datawriter_req, datareader_req};
    if(!mr_run_session_until_status(&session, 1000, requests, status, 6))
    {
        printf("Error at create entities\n");
        return 1;
    }

The ``run_session`` functions are the main functions of the `Micro RTP Client` library.
They performs serveral things: send the stream data to the agent, listen data from the agent, call callbacks, and manage the reliable connection.
There are three variations of ``run_session`` function.
Here we use the ``until_status`` variation that will performs these actions until all status have been confirmed or the timeout has been reached.
The function ``mr_run_session_until_status`` will return ``true`` in case all status were `OK`.
After call this function, the status can be read from the ``status`` array previously declared.

Write Data
^^^^^^^^^^
Once we have created a valid data writer entity, we can write data into the DDS Global Data Space using the write operation.
For creating a message with data, first we must to decide which stream we want to use, and write that topic in this stream.

.. code-block:: C

    HelloWorld topic = {count++, "Hello DDS world!"};
    (void) mr_write_HelloWorld_topic(&session, reliable_out, datawriter_id, &topic);

    mr_run_session_until_confirmed_delivery(&session, 1000);

``mr_write_HelloWorld_topic`` function is automatically generated by :ref:`micrortpsgen_label` from the IDL.
This function serializes the topic into stream.
If the stream is available and the topic fix into it, a valid *request id* is returned.
``datawriter_id`` correspond to the data writer entity used for sending the data.

After the write function, as happend with the creation of entities, the topic has been serialized into the buffer but it has not been sent yet.
To send the topic is necessary call to a ``run_session`` function.
In this case, we call to ``mr_run_session_until_confirmed_delivery`` that will wait until the message was confirmed or until the timeout has been reached.

Read Data
^^^^^^^^^
Once we have created a valid data reader entity, we can read data from the DDS Global Data Space using the read operation.
This operation configures how the agent will send the data to the client.
current implementation sends one topic to the client for each read data operation of the client.

.. code-block:: C

    mrDeliveryControl delivery_control = {0};
    delivery_control.max_samples = MR_MAX_SAMPLES_UNLIMITED;

    uint16_t read_data_req = mr_write_request_data(&session, reliable_out, datareader_id, reliable_in, &delivery_control);

In order to configure how the agent will send the topic, we must set the input stream. In this case, we use the input reliable stream previously defined.
``datareader_id`` corresponds to the data deader entity used for receiving the data.
The ``delivery_control`` parameter is option, and allow to specify how the data will be deliverd to the client.
For the example purpose, we set it as `unlimited`, so any number HelloWorld topic will be delivered to the client.

The function ``run_session`` will call the callback when a topic will be received from the agent.
Once the topic has been received we can read it in the callback:

.. code-block:: C

    void on_topic(mrSession* session, mrObjectId object_id, uint16_t request_id, mrStreamId stream_id, struct MicroBuffer* mb, void* args)
    {
        (void) session; (void) object_id; (void) request_id; (void) stream_id; (void) args;

        HelloWorld topic;
        HelloWorld_deserialize_topic(mb, &topic);
    }

To know which kind of Topic has been received, we can use the ``object_id`` parameter or the ``request_id``.
This id of the ``object_id`` corresponds to the DataReader that has read the Topic.
The ``args`` argument correspond to user free data.

Closing my Client
^^^^^^^^^^^^^^^^^
To close a *Client*, we must perform two steps.
First, we need to tell the agent that the session is no longer available.
This is done sending the next message:

.. code-block:: C

    mr_delete_session(&session);

After this, we can close the transport used by the session.

.. code-block:: C

    mr_close_udp_transport(&transport);

