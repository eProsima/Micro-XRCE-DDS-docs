Getting started
===============
This page shows how to get started with the *Micro RTPS Client* development.
We will create a client that can publish and subscribe a topic.
This example can be found into ``examples/PublishSubscribeHelloWorld``.

First, we need to have on the system:

 - :ref:`micro_rtps_client_label`.
 - :ref:`micro_rtps_agent_label`.
 - :ref:`micrortpsgen_label`.
 - `FastRTPS. <https://github.com/eProsima/Fast-RTPS>`_

Generate from the IDL
^^^^^^^^^^^^^^^^^^^^^^
We will use HelloWorld as our Topic, that looks like: ::

    struct HelloWorld
    {
        unsigned long index;
        string message;
    };

In the Client we need to create an equivalent c type with its serialization/deserialization code. This is done automatically by :ref:`micrortpsgen_label`: ::

    $ micrortpsgen HelloWorld.idl

Initialize a Session
^^^^^^^^^^^^^^^^^^^^
We need to create a Session using a transport to use. This time we are using UDP in localhost over the port 2018.

In the source example file, we include the generated type code, in order to have access to its serialization/deserialization functions.
Also, we will specify a define for identifing the topic more readability.

.. code-block:: C

    #include "HelloWorld.h"
    #define HELLO_WORLD_TOPIC  1

The first API call is for creating a Session which will use to manage the client.
A valid Session is created in two steps:

.. code-block:: C

    ClientKey key = {{0xBB, 0xBB, 0xCC, 0xDD}};
    if(new_udp_session(&my_session, 0x01, key, "127.0.0.1", 2018, on_topic, NULL))
    {
        printf("Error: socket is not available.");
        return 1;
    }

    bool success = init_session_sync(&my_session);

The first function ``new_udp_session`` does several things,
first of all, set an id and a key for the Session,
creates a socket for connecting to the agent,
and setted a callback for receiving data from that agent.

Once the udp Session has been created, we can send the first message for initiallizing the Session in the agent ``init_session_sync``.
This call creates an client representation in the agent side.
All functions with the suffix ``sync`` will be wait until the response or until a max number of attempts.

In order to publish and subscribe a topic, we need to create a hierarchy of xrce entities in the Agent side.
This entities will be created from the client.

.. image:: micrortps_entities_hierarchy.svg

Agent response
^^^^^^^^^^^^^^
In XRCE operations like initializing a client, create an entity or request data from the agent,
an status state is send from the agent to the client to specify what happened.

In this operations, if the call returns true, the things go well, as expected.
If not, an status state can be read on ``session->last_status`` attribute to determine what was wrong.
There are several values into a status response:

.. code-block:: C

    typedef enum StatusValue
    {
        STATUS_OK = 0x00,
        STATUS_OK_MATCHED = 0x01,
        STATUS_ERR_DDS_ERROR = 0x80,
        STATUS_ERR_MISMATCH = 0x81,
        STATUS_ERR_ALREADY_EXISTS = 0x82,
        STATUS_ERR_DENIED = 0x83,
        STATUS_ERR_UNKNOWN_REFERENCE = 0x84,
        STATUS_ERR_INVALID_DATA = 0x85,
        STATUS_ERR_INCOMPATIBLE = 0x86,
        STATUS_ERR_RESOURCES = 0x87

    } StatusValue;

Setup a Participant
^^^^^^^^^^^^^^^^^^^
For establishing DDS communication we need to create a Participant for our Client in the Agent.
We can do this calling create Participant operation:

.. code-block:: C

    const char* reference = "default_participant";
    ObjectId participant_id = {{0x00, OBJK_PARTICIPANT}};
    success = create_participant_sync_by_ref(&my_session, participant_id, reference, false, false);

In any XRCE operation that creates an entity, an ObjectId is necessary. It is used to represent and manage the object in the client side.
The reference is the identifier of a DDS entity in the agent side.
if the function returns true, the participant will be able to use from this client.

Creating  topics
^^^^^^^^^^^^^^^^
Once the Participant has been created, we can use create Topic Operation for register a Topic within the Participant.

.. code-block:: C

    const char* topic_xml = {"<dds><topic><name>HelloWorldTopic</name><dataType>HelloWorld</dataType></topic></dds>"};
    ObjectId topic_id = {{0x00, OBJK_TOPIC}};
    success = create_topic_sync_by_xml(&my_session, topic_id, topic_xml, participant_id, false, false);

As any other XRCE operation used to create an entity, an ObjectId must be specify to represent the object.
The ``participant_id`` is the participant where the topic will be register.
In order to determine which topic will be used, an xml is sent to the agent for creating and defining the topic in the DDS Global Data Space.
That definition consists on a name and a type.

Publishers & Subscribers
^^^^^^^^^^^^^^^^^^^^^^^^
Similar to Topic registration we can create publishers and subscribers. We create a publisher or subscriber on a Participant, so its necessary to provide the Id of the Participant that will hold those publishers or subscribers.

.. code-block:: C

    const char* publisher_xml = {"<publisher name=\"MyPublisher\""};
    ObjectId publisher_id = {{HELLO_WORLD_TOPIC, OBJK_PUBLISHER}};
    success = create_publisher_sync_by_xml(&my_session, publisher_id, publisher_xml, participant_id, false, false);

    const char* subscriber_xml = {"<publisher name=\"MySubscriber\""};
    ObjectId subscriber_id = {{HELLO_WORLD_TOPIC, OBJK_SUBSCRIBER}};
    success = create_subscriber_sync_by_xml(&my_session, subscriber_id, subscriber_xml, participant_id, false, false);

DataWriters & DataReaders
^^^^^^^^^^^^^^^^^^^^^^^^^
Analogous to Publishers and Subscribers, we create the DataWriters and DataReaders.
This XRCE entities are responsible to send and receive the data.
DataWriters are referred to a Publisher, and DataReaders are referred to a Subscriber.

.. code-block:: C

    const char* datawriter_xml = {"<profiles><publisher profile_name=\"default_xrce_publisher_profile\"><topic><kind>NO_KEY</kind><name>HelloWorldTopic</name><dataType>HelloWorld</dataType><historyQos><kind>KEEP_LAST</kind><depth>5</depth></historyQos><durability><kind>TRANSIENT_LOCAL</kind></durability></topic></publisher></profiles>"};
    ObjectId datawriter_id = {{HELLO_WORLD_TOPIC, OBJK_DATAWRITER}};
    success = create_datawriter_sync_by_xml(&my_session, datawriter_id, datawriter_xml, publisher_id, false, false);

    const char* datareader_xml = {"<profiles><subscriber profile_name=\"default_xrce_subscriber_profile\"><topic><kind>NO_KEY</kind><name>HelloWorldTopic</name><dataType>HelloWorld</dataType><historyQos><kind>KEEP_LAST</kind><depth>5</depth></historyQos><durability><kind>TRANSIENT_LOCAL</kind></durability></topic></subscriber></profiles>"};
    ObjectId datareader_id = {{HELLO_WORLD_TOPIC, OBJK_DATAREADER}};
    success = create_datareader_sync_by_xml(&my_session, datareader_id, datareader_xml, subscriber_id, false, false);

Write Data
^^^^^^^^^^
Once we have created a valid DataWriter, we can write data into DDS Global Data Space using the write Operation.
For creating a message with data, first we must to decide which stream we want to use, and write that topic in this stream.
In this case, we will use a reliable stream.

.. code-block:: C

    HelloWorld topic = {counter, "Hello DDS World!"};
    bool serialized = write_HelloWorld(&my_session, datawriter_id, STREAMID_BUILTIN_RELIABLE, &topic);
    if(true == serialized)
    {
        printf("Write topic: %s, count: %i\n", topic.message, topic.index);
    }

``write_HelloWorld`` function is automatically generated by :ref:`micrortpsgen_label` from the IDL.
This functions serialize the topic into stream.
If the stream is available and the topic fix into it, a true is returned.
``datawriter_id`` correspond to the DataWriter entity used for sending the data.

At this point, the topic has been serialized in the buffer but not has been sent yet.
The topic will be send in the next call to ``run_communication`` function.

Read Data
^^^^^^^^^
Once we have created a valid DataWriter, we can read data from DDS Global Data Space using the read Operation.
This operation configure how the agent will send the data to the client.
Current implementation send one topic to the client for each read data operation of the client.

.. code-block:: C

        success = read_data_sync(&my_session, datareader_id, STREAMID_BUILTIN_RELIABLE);

In order to configure how the agent will send the topic, we must set the input stream. In this case, we used a reliable stream.
``datareader_id`` correspond to the DataReader entity used for receiving the data.

The function ``run_communication`` will call the callback provided at Session creation stage when a topic will be received from the agent.
Once the topic has been received we can read it in the callback:

.. code-block:: C

    void on_topic(ObjectId id, MicroBuffer* serialized_topic, void* args)
    {
        switch(id.data[0])
        {
            case HELLO_WORLD_TOPIC:
            {
                HelloWorld topic;
                deserialize_HelloWorld_topic(serialized_topic, &topic);
                printf("Read topic: %s, count: %i\n", topic.message, topic.index);
                break;
            }

            default:
                break;
        }
    }

To know which kind of topic has received, we can use the ObjectId parameter. This id correspond to the DataReader that has read the topic.
The args of the third argument correspond to user free data.

Let the Client works
^^^^^^^^^^^^^^^^^^^^
Write Data operation only write a topic into the buffer, and Read Data operation only set how read the topics.
To make it works, we must to call the main function of the library ``run_communication``.
This function is responsible to send topics, receive topics, call the user callback, send and receive heartbeats and acknacks for reliable streams, and send lost messages again.
For a correct work of the *Micro RTPS Client*, this function must be called periodically.

.. code-block:: C

    // main loop
    while(true)
    {
        //write something
        //...

        //configure reads
        //...

        run_communication(&my_session);

        //go to sleep 1 second
        sleep(1);
    }

Closing my Client
^^^^^^^^^^^^^^^^^
To close a client, we must perform two steps.
First, we need to tell the agent that the client is no longer available. This is done sending the next message:

.. code-block:: C

    close_session_sync(&my_session);

After this, we can free the resources held by the client with:

.. code-block:: C

    free_session_udp(&my_session);

