Quickstart
==========

This page shows you how to get started with the *Micro RTPS Client* development.

We will go step by step on one of our Client examples using `ShapesDemo. <https://github.com/eProsima/ShapesDemo>`_

If you want to jump straight to the code go to :ref:`quick_start_code`.

First, you need to have on your system:

 - FastRTPS
 - ShapesDemo.
 - :ref:`micro_rtps_client_label`.
 - :ref:`micro_rtps_agent_label`.
 - :ref:`micrortpsgen_label`.

Generate from your IDL
^^^^^^^^^^^^^^^^^^^^^^

Using ShapesDemo as our Topic. The ShapeType IDL on FastRTPS looks like:

::

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


.. _quick_start_code:

Full Code
^^^^^^^^^

This is an example code of an interactive shapesDemo Client.

This interactive client waits for user input indicating commands to execute.

For publishing a topic data you need to run the following commands in order:

* create_client
* create_participant
* create_topic 1
* create_publisher 1
* create_data_writer 1 3
* write_data 4

Participant will be assigned ID 1.
Topic will be assigned ID 2.
Publisher will be assigned ID 3.
And DaWriter will be assigned ID 4.

And for reading data:

* create_client
* create_participant
* create_topic 1
* create_subscriber 1
* create_data_reader 1 3
* read_data 4

The previous IDs are valid for a fresh run of Agent and Client pair. Other wise you need to get IDs from status messages responses sent from Agent.

Generated Type code:

.. code-block:: C

    /*!
    * @file Shape.h
    * This header file contains the declaration of the described types in the IDL file.
    *
    * This file was generated by the tool gen.
    */

    #ifndef _Shape_H_
    #define _Shape_H_

    #include <string.h>
    #include <stdlib.h>

    #include <micrortps/client/client.h>
    #include <microcdr/microcdr.h>

    /*!
    * @brief This class represents the structure ShapeType defined by the user in the IDL file.
    * @ingroup SHAPE
    */
    typedef struct ShapeType
    {
        char* m_color;
        int32_t m_x;
        int32_t m_y;
        int32_t m_shapesize;
    }ShapeType;

    bool serialize_Shape_topic(MicroBuffer* writer, const AbstractTopic* topic_structure)
    {
        ShapeType* topic = (ShapeType*) topic_structure->topic;
        serialize_uint32_t(writer, strlen(topic->m_color) + 1);
        serialize_array_char(writer, topic->m_color, strlen(topic->m_color) + 1);
        serialize_int32_t(writer, topic->m_x);
        serialize_int32_t(writer, topic->m_y);
        serialize_int32_t(writer, topic->m_shapesize);

        return true;
    }

    bool deserialize_Shape_topic(MicroBuffer* reader, AbstractTopic* topic_structure)
    {
        ShapeType* topic = malloc(sizeof(ShapeType));
        uint32_t size = 0;
        deserialize_uint32_t(reader, &size);
        topic->m_color = malloc(size);
        deserialize_array_char(reader, topic->m_color, size);
        deserialize_int32_t(reader, &topic->m_x);
        deserialize_int32_t(reader, &topic->m_y);
        deserialize_int32_t(reader, &topic->m_shapesize);

        topic_structure->topic = topic;

        return true;
    }


*Micro RTPS Client* code:

.. code-block:: C

    #include <stdio.h>
    #include <string.h>
    #include <stdlib.h>
    #include <pthread.h>
    #include <unistd.h>

    #include "Shape.h"

    #define BUFFER_SIZE 4096

    // ----------------------------------------------------
    //    App client
    // ----------------------------------------------------
    void on_shape_topic(XRCEInfo info, const void* topic, void* args);
    void on_status_received(XRCEInfo info, uint8_t operation, uint8_t status, void* args);

    void printl_shape_topic(const ShapeTopic* shape_topic);
    void* listen_agent(void* args);
    bool compute_command(const char* command, ClientState* state);
    void list_commands();
    void help();

    String read_file(char* file_name);

    bool stop_listening = false;

    int main(int args, char** argv)
    {
        printf("<< SHAPES DEMO XRCE CLIENT >>\n");

        ClientState* state = NULL;
        if(args > 3)
        {
            if(strcmp(argv[1], "serial") == 0)
            {
                state = new_serial_client_state(BUFFER_SIZE, argv[2]);
                printf("<< Serial mode => dev: %s >>\n", argv[2]);
            }
            else if(strcmp(argv[1], "udp") == 0 && args == 5)
            {
                uint16_t received_port = atoi(argv[3]);
                uint16_t send_port = atoi(argv[4]);
                state = new_udp_client_state(BUFFER_SIZE, argv[2], received_port, send_port);
                printf("<< UDP mode => recv port: %u, send port: %u >>\n", received_port, send_port);
            }
        }
        if(!state)
        {
            printf("Help: program [serial | udp dest_ip recv_port send_port]\n");
            return 1;
        }


        // Listening agent
        pthread_t listening_thread;
        if(pthread_create(&listening_thread, NULL, listen_agent, state))
        {
            printf("ERROR: Error creating thread\n");
            return 2;
        }

        // Waiting user commands
        printf(":>");
        char command_stdin_line[256];
        while(fgets(command_stdin_line, 256, stdin))
        {
            if(!compute_command(command_stdin_line, state))
            {
                stop_listening = true;
                break;
            }
            printf(":>");
        }

        pthread_join(listening_thread, NULL);

        free_client_state(state);

        return 0;
    }

    void* listen_agent(void* args)
    {
        while(!stop_listening)
        {
            receive_from_agent((ClientState*) args);
        }

        return NULL;
    }

    bool compute_command(const char* command, ClientState* state)
    {
        char name[128];
        static unsigned int hello_world_id = 0;
        int id = 0;
        int extra = 0;
        int length = sscanf(command, "%s %u %u", name, &id, &extra);


        if(strcmp(name, "create_client") == 0)
        {
            create_client(state, on_status_received, NULL);
        }
        else if(strcmp(name, "create_participant") == 0)
        {
            create_participant(state);
        }
        else if(strcmp(name, "create_topic") == 0 && length == 2)
        {
            String xml = read_file("shape_topic.xml");
            if (xml.length > 0)
            {
                create_topic(state, id, xml);
            }
        }
        else if(strcmp(name, "create_publisher") == 0 && length == 2)
        {
            create_publisher(state, id);
        }
        else if(strcmp(name, "create_subscriber") == 0 && length == 2)
        {
            create_subscriber(state, id);
        }
        else if(strcmp(name, "create_data_writer") == 0 && length == 3)
        {
            String xml = read_file("data_writer_profile.xml");
            if (xml.length > 0)
            {
                create_data_writer(state, id, extra, xml);
            }
        }
        else if(strcmp(name, "create_data_reader") == 0 && length == 3)
        {
            String xml = read_file("data_reader_profile.xml");
            if (xml.length > 0)
            {
                create_data_reader(state, id, extra, xml);
            }
        }
        else if(strcmp(name, "write_data") == 0 && length == 2)
        {
            ShapeTopic shape_topic = {strlen("GREEN") + 1, "GREEN", 100 , 100, 50};
            write_data(state, id, serialize_shape_topic, &shape_topic);
            printl_shape_topic(&shape_topic);
        }
        else if(strcmp(name, "read_data") == 0 && length == 2)
        {
            read_data(state, id, deserialize_shape_topic, on_shape_topic, NULL);
        }
        else if(strcmp(name, "delete") == 0 && length == 2)
        {
            delete_resource(state, id);
        }
        else if(strcmp(name, "h") == 0 || strcmp(name, "help") == 0)
        {
            list_commands();
        }
        else
        {
            help();
        }

        // only send data if there is.
        send_to_agent(state);

        // close client
        if(strcmp(name, "exit") == 0)
            return false;

        return true;
    }

    void on_shape_topic(XRCEInfo info, const void* vtopic, void* args)
    {
        ShapeTopic* topic = (ShapeTopic*) vtopic;
        printl_shape_topic(topic);

        free(topic->color);
        free(topic);
    }

    void on_status_received(XRCEInfo info, uint8_t operation, uint8_t status, void* args)
    {
        printf("User status callback\n");
    }

    void printl_shape_topic(const ShapeTopic* shape_topic)
    {
        printf("        %s[%s | x: %u | y: %u | size: %u]%s\n",
                "\e[1;34m",
                shape_topic->color,
                shape_topic->x,
                shape_topic->y,
                strlen(shape_topic->color) + 1,
                "\e[0m");
    }

    String read_file(char* file_name)
    {
        printf("READ FILE\n");
        const size_t MAXBUFLEN = 4096;
        char data[MAXBUFLEN];
        String xml = {data, 0};
        FILE *fp = fopen(file_name, "r");
        if (fp != NULL)
        {
            xml.length = fread(xml.data, sizeof(char), MAXBUFLEN, fp);
            if (xml.length == 0)
            {
                printf("Error reading %s\n", file_name);
            }
            fclose(fp);
        }
        else
        {
            printf("Error opening %s\n", file_name);
        }

        return xml;
    }

    void help()
    {
        printf("usage: <command> [<args>]\n");
        printf("    h, help: for command list\n");
    }

    void list_commands()
    {
        printf("usage: <command> [<args>]\n");
        printf("    create_client:                                       Creates a Client\n");
        printf("    create_participant:                                  Creates a new Participant on the current Client\n");
        printf("    create_topic <participant id>:                       Register new Topic using <participant id> participant\n");
        printf("    create_publisher <participant id>:                   Creates a Publisher on <participant id> participant\n");
        printf("    create_subscriber <participant id>:                  Creates a Subscriber on <participant id> participant\n");
        printf("    create_data_writer <participant id> <publisher id>:  Creates a DataWriter on the publisher <publisher id> of the <participant id> participant\n");
        printf("    create_data_reader <participant id> <subscriber id>: Creates a DataReader on the subscriber <subscriber id> of the <participant id> participant\n");
        printf("    write_data <data writer id>:                         Write data using <data writer id> DataWriter\n");
        printf("    read_data <data reader id>:                          Read data using <data reader id> DataReader\n");
        printf("    delete <id>:                                         Removes object with <id> identifier\n");
        printf("    h, help:                                             Shows this message\n");
    }