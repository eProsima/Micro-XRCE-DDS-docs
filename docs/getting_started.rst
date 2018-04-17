Getting started
===============

This page shows you how to get started with the *Micro RTPS Client* development.

First, you need to have on your system:

 - :ref:`micro_rtps_client_label`.
 - :ref:`micro_rtps_agent_label`.
 - :ref:`micrortpsgen_label`.
 - `FastRTPS. <https://github.com/eProsima/Fast-RTPS>`_

Generate from your IDL
^^^^^^^^^^^^^^^^^^^^^^

Using ShapesDemo as our Topic. The ShapeType IDL on FastRTPS looks like: ::

    struct ShapeType {
        @Key string color;
        long x;
        long y;
        long shapesize;
    };

In your Client you need to create and equivalent type and serialization/deserialization code. This is done automatically for you by :ref:`micrortpsgen_label`: ::

    $ micrortpsgen Shape.idl

Creating a Client
^^^^^^^^^^^^^^^^^

You need to create a ClientState using the transport you want to use (UDP or Serial). This time we are using UDP over the ports 2019 (in) and 2020 (out). We also specify the buffer size of 4096 for the messages.

Include your generated type code.

.. code-block:: C

    #include "Shape.h"

In your code you can create a Client State like:

.. code-block:: C

    ClientState* state = new_udp_client_state(4096, "127.0.0.1", 2019, 2020);

If the Client state creation finished successfully you will get a valid pointer otherwise the pointer will be NULL.

First Agent Operation
^^^^^^^^^^^^^^^^^^^^^

For starting a communication with the Agent you need to let it know about your new Client. You can do this using Create Client Operation.

All the Operations you send to the Agent will be replied over a callback you must implement:

.. code-block:: C

    void on_status_received(XRCEInfo info, uint8_t operation, uint8_t status, void* args)
    {
        printf("Status response received\n");
    }

You pass that callback to the create Operation so all the status responses for that Client are received on that callback. For creating a Client you need the callback and the Client State you create before.

.. code-block:: C

    XRCEInfo create_client_info = create_client(state, on_status_received, NULL);

The XRCEInfo you get from this function provides you with the request ID generated as well as the Entity ID the object created will have in the Agent.

Setup a Participant
^^^^^^^^^^^^^^^^^^^

For establishing DDS communication you need to create a :class:`Participant` for your Client in the Agent. You do this calling create Participant:

.. code-block:: C

    XRCEInfo participant_info = create_participant(state);

This Participant configuration is taken from the XML file DEFAULT_FASTRTPS_PROFILES.xml that you can find within the Agent installation. You can modify this predefined configuration.

What is the response
^^^^^^^^^^^^^^^^^^^^

If you provide the Client with a status callback all the subsequent Operations will generate a response message. This messages are received upon a call to receive_from_agent:

.. code-block:: C

    receive_from_agent(state);

This call will check the transport for new incoming messages. On the callback you will receive the XRCEInfo corresponding to the last Operation as well as the last Operation ID and the status of this Operation. This are the possible Status and last Operation IDs:

.. code-block:: C

    // Operation Status
    #define STATUS_OK 0x00
    #define STATUS_OK_MATCHED 0x01
    #define STATUS_ERR_DDS_ERROR 0x80
    #define STATUS_ERR_MISMATCH 0x81
    #define STATUS_ERR_ALREADY_EXISTS 0x82
    #define STATUS_ERR_DENIED 0x83
    #define STATUS_ERR_UNKNOWN_REFERENCE 0x84
    #define STATUS_ERR_INVALID_DATA 0x85
    #define STATUS_ERR_INCOMPATIBLE 0x86
    #define STATUS_ERR_RESOURCES 0x87

    // Last Operation ID
    #define STATUS_LAST_OP_NONE 0x00
    #define STATUS_LAST_OP_CREATE 0x01
    #define STATUS_LAST_OP_UPDATE 0x02
    #define STATUS_LAST_OP_DELETE 0x03
    #define STATUS_LAST_OP_LOOKUP 0x04
    #define STATUS_LAST_OP_READ 0x05
    #define STATUS_LAST_OP_WRITE 0x06

Creating  topics
^^^^^^^^^^^^^^^^

Once you have created a Participant you can use create Topic Operation for register your Topic within the Participant.

You need to provide the Participant ID to create the Topic with it:

.. code-block:: C

    String topic_profile = {"<dds><topic><kind>WITH_KEY</kind><name>Square</name><dataType>ShapeType</dataType></topic></dds>", 96+1};
    create_topic(state, participant_info.object_id, topic_profile);

For this Operation You must provide a XML defining your topic. That definition consists on a name and a type.

Publishers & Subscribers
^^^^^^^^^^^^^^^^^^^^^^^^

Similar to Topic registration you can create publishers and subscribers. You create a publisher or subscriber on a Participant, so you need to provide the ID of the Participant that will hold those publishers or subscribers.

.. code-block:: C

    XRCEInfo publisher_info = create_publisher(state, participant_info.object_id);

    XRCEInfo subscriber_info = create_subscriber(state, participant_info.object_id);

Write data
^^^^^^^^^^

For writing data you need two essential elements, the data you want to write on a DDS topic and the DataWriter you want to use to write.

You need to specify in which Participant and in which Publisher you want the new DataWriter to be created by the Agent. Also you need to pass a XML representation of it. We support the same XML profiles as in FastRTPS implementation.

.. code-block:: C

    String data_writer_profile = {"<profiles><publisher profile_name=\"default_xrce_publisher_profile\"><topic><kind>NO_KEY</kind><name>Square</name><dataType>ShapeType</dataType><historyQos><kind>KEEP_LAST</kind><depth>5</depth></historyQos><durability><kind>TRANSIENT_LOCAL</kind></durability></topic></publisher></profiles>",
    289+1};

    XRCEInfo data_writer_info = create_data_writer(state, participant_info.object_id, publisher_info.object_id, data_writer_profile);

Once you have created a valid DataWriter and with its ID you can write data into DDS Global Data Space using the write Operation:

.. code-block:: C

    ShapeTopic shape_topic = {strlen("GREEN") + 1, "GREEN", 100 , 100, 50};
    XRCEInfo write_info = write_data(state, data_writer_info.object_id, serialize_shape_topic, &ShapeTopic);

You need to provide the serialization function to be used with your type. This serialization function is automatically generated by :ref:`micrortpsgen_label` from your IDL.

Read Data
^^^^^^^^^

For receiving data you need to create a DataReader in an already existing Subscriber.

.. code-block:: C

    String data_reader_profile = {"<profiles><subscriber profile_name=\"default_xrce_subscriber_profile\"><topic><kind>NO_KEY</kind>  <name>Square</name><dataType>ShapeType</dataType><historyQos><kind>KEEP_LAST</kind><depth>5</depth></historyQos><durability>   <kind>TRANSIENT_LOCAL</kind></durability></topic></subscriber></profiles>", 297+1}

    XRCEInfo data_reader_info = create_data_reader(state, participant_info.object_id, publisher_info.object_id, data_reader_profile);


You receive data in a callback you must provide. This callback is called after the topic data deserialization function. Here you can free up all the resources the library have reserved deserializing the data.

.. code-block:: C

    void on_shape_topic(XRCEInfo info, const void* vtopic, void* args)
    {
        ShapeTopic* topic = (ShapeTopic*) vtopic;
        free(topic->color);
        free(topic);
    }

Once you have the callback for receiving data you can ask your DataReader to read data.

.. code-block:: C

    XRCEInfo read_info = read_data(state, id, deserialize_shape_topic, on_shape_topic, NULL);

From this point you will receive the data read from DDS Global Data Space within the callback you provide.

Communication with Agent
^^^^^^^^^^^^^^^^^^^^^^^^

All the previous Operations calls are not sent to the Agent till you ask so. You must call send to Agent explicitly.

.. code-block:: C

    send_to_agent(state);

This call will send all the accumulated Operations to the Agent.

For receiving data there is an analogous Operation you must call:

.. code-block:: C

    receive_from_agent(state);

to receive read data from the Agent.


Closing my Client
^^^^^^^^^^^^^^^^^

You need to free all the Client State resources with a call to free Client state.

.. code-block:: C

    free_client_state(state);


