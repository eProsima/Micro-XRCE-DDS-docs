.. _quickstart_label:

Quick start
===========
`Micro RTPS` provides a C API which allows the creation of `Micro RTPS Clients` that publish and/or subscribe to topics from DDS Global Data Space.
The following example shows how to create a simple `Micro RTPS Client` and `Micro RTPS Agent` for publishing and subscribing to the DDS world, using this HelloWorld.idl: ::

    struct HelloWorld
    {
        unsigned long index;
        string message;
    };

First of all, we launch the `Agent`. For this example, the `Client` - `Agent` communication will be done through UDP: ::

    $ cd /usr/local/bin && MicroRTPSAgent udp 2018

Along with the `Agent`, the `PublishHelloWorldClient` example provided in the source code is launched.
This `Client` example will publish in the DDS World the HelloWorld topic. ::

    $ examples/PublishHelloWorld/PublishHelloWorldClient

The code of the *PublishHelloWorldClient* is the following:

.. code-block:: C

    #include "HelloWorldWriter.h"

    #include <micrortps/client/client.h>
    #include <stdio.h>
    #include <string.h> //strcmp
    #include <stdlib.h> //atoi

    #define STREAM_HISTORY  8
    #define BUFFER_SIZE     MR_CONFIG_UDP_TRANSPORT_MTU * STREAM_HISTORY

    int main(int args, char** argv)
    {
        if(args >= 2 && (0 == strcmp("-h", argv[1]) || 0 == strcmp("--help", argv[1]) || 0 == atoi(argv[1])))
        {
            printf("usage: program [-h | --help | <topics>]\n");
            return 0;
        }

        uint32_t max_topics = (args == 2) ? (uint32_t)atoi(argv[1]) : UINT32_MAX;

        // Transport
        mrUDPTransport transport;
        if(!mr_init_udp_transport(&transport, "127.0.0.1", 2018))
        {
            printf("Error at create transport.\n");
            return 1;
        }

        // Session
        mrSession session;
        mr_init_session(&session, &transport.comm, 0xAAAABBBB);
        if(!mr_create_session(&session))
        {
            printf("Error at create session.\n");
            return 1;
        }

        // Streams
        uint8_t output_reliable_stream_buffer[BUFFER_SIZE];
        mrStreamId reliable_out = mr_create_output_reliable_stream(&session, output_reliable_stream_buffer, BUFFER_SIZE, STREAM_HISTORY);

        uint8_t input_reliable_stream_buffer[BUFFER_SIZE];
        mr_create_input_reliable_stream(&session, input_reliable_stream_buffer, BUFFER_SIZE, STREAM_HISTORY);

        // Create entities
        mrObjectId participant_id = mr_object_id(0x01, MR_PARTICIPANT_ID);
        const char* participant_ref = "default participant";
        uint16_t participant_req = mr_write_create_participant_ref(&session, reliable_out, participant_id, participant_ref, MR_REPLACE);

        mrObjectId topic_id = mr_object_id(0x01, MR_TOPIC_ID);
        const char* topic_xml = "<dds><topic><name>HelloWorldTopic</name><dataType>HelloWorld</dataType></topic></dds>";
        uint16_t topic_req = mr_write_configure_topic_xml(&session, reliable_out, topic_id, participant_id, topic_xml, MR_REPLACE);

        mrObjectId publisher_id = mr_object_id(0x01, MR_PUBLISHER_ID);
        const char* publisher_xml = "<publisher name=\"MyPublisher\">";
        uint16_t publisher_req = mr_write_configure_publisher_xml(&session, reliable_out, publisher_id, participant_id, publisher_xml, MR_REPLACE);

        mrObjectId datawriter_id = mr_object_id(0x01, MR_DATAWRITER_ID);
        const char* datawriter_xml = "<profiles><publisher profile_name=\"default_xrce_publisher_profile\"><topic><kind>NO_KEY</kind><name>HelloWorldTopic</name><dataType>HelloWorld</dataType><historyQos><kind>KEEP_LAST</kind><depth>5</depth></historyQos><durability><kind>TRANSIENT_LOCAL</kind></durability></topic></publisher></profiles>";
        uint16_t datawriter_req = mr_write_configure_datawriter_xml(&session, reliable_out, datawriter_id, publisher_id, datawriter_xml, MR_REPLACE);

        // Send create entities message and wait its status
        uint8_t status[4];
        uint16_t requests[4] = {participant_req, topic_req, publisher_req, datawriter_req};
        if(!mr_run_session_until_status(&session, 1000, requests, status, 4))
        {
            printf("Error at create entities: participant: %i topic: %i publisher: %i darawriter: %i\n", status[0], status[1], status[2], status[3]);
            return 1;
        }

        // Write topics
        bool connected = true;
        uint32_t count = 0;
        while(connected && count < max_topics)
        {
            HelloWorld topic = {count++, "Hello DDS world!"};
            (void) mr_write_HelloWorld_topic(&session, reliable_out, datawriter_id, &topic);

            connected = mr_run_session_until_timeout(&session, 1000);
            if(connected)
            {
                printf("Sent topic: %s, id: %i\n", topic.message, topic.index);
            }
        }

        // Delete resources
        mr_delete_session(&session);
        mr_close_udp_transport(&transport);

        return 0;
    }

After it, we will launch the *SubscriberHelloWorldClient*. This `Client` example will subscribe to HelloWorld topic from the DDS World. ::

    $ examples/SubscriberHelloWorld/SubscribeHelloWorldClient

The code of the *SubscriberHelloWorldClient* is the following:

.. code-block:: C

    #include "HelloWorldWriter.h"

    #include <micrortps/client/client.h>
    #include <stdio.h>
    #include <string.h> //strcmp
    #include <stdlib.h> //atoi

    #define STREAM_HISTORY  8
    #define BUFFER_SIZE     MR_CONFIG_UDP_TRANSPORT_MTU * STREAM_HISTORY

    int main(int args, char** argv)
    {
        if(args >= 2 && (0 == strcmp("-h", argv[1]) || 0 == strcmp("--help", argv[1]) || 0 == atoi(argv[1])))
        {
            printf("usage: program [-h | --help | <topics>]\n");
            return 0;
        }

        uint32_t max_topics = (args == 2) ? (uint32_t)atoi(argv[1]) : UINT32_MAX;

        // Transport
        mrUDPTransport transport;
        if(!mr_init_udp_transport(&transport, "127.0.0.1", 2018))
        {
            printf("Error at create transport.\n");
            return 1;
        }

        // Session
        mrSession session;
        mr_init_session(&session, &transport.comm, 0xAAAABBBB);
        if(!mr_create_session(&session))
        {
            printf("Error at create session.\n");
            return 1;
        }

        // Streams
        uint8_t output_reliable_stream_buffer[BUFFER_SIZE];
        mrStreamId reliable_out = mr_create_output_reliable_stream(&session, output_reliable_stream_buffer, BUFFER_SIZE, STREAM_HISTORY);

        uint8_t input_reliable_stream_buffer[BUFFER_SIZE];
        mr_create_input_reliable_stream(&session, input_reliable_stream_buffer, BUFFER_SIZE, STREAM_HISTORY);

        // Create entities
        mrObjectId participant_id = mr_object_id(0x01, MR_PARTICIPANT_ID);
        const char* participant_ref = "default participant";
        uint16_t participant_req = mr_write_create_participant_ref(&session, reliable_out, participant_id, participant_ref, MR_REPLACE);

        mrObjectId topic_id = mr_object_id(0x01, MR_TOPIC_ID);
        const char* topic_xml = "<dds><topic><name>HelloWorldTopic</name><dataType>HelloWorld</dataType></topic></dds>";
        uint16_t topic_req = mr_write_configure_topic_xml(&session, reliable_out, topic_id, participant_id, topic_xml, MR_REPLACE);

        mrObjectId publisher_id = mr_object_id(0x01, MR_PUBLISHER_ID);
        const char* publisher_xml = "<publisher name=\"MyPublisher\">";
        uint16_t publisher_req = mr_write_configure_publisher_xml(&session, reliable_out, publisher_id, participant_id, publisher_xml, MR_REPLACE);

        mrObjectId datawriter_id = mr_object_id(0x01, MR_DATAWRITER_ID);
        const char* datawriter_xml = "<profiles><publisher profile_name=\"default_xrce_publisher_profile\"><topic><kind>NO_KEY</kind><name>HelloWorldTopic</name><dataType>HelloWorld</dataType><historyQos><kind>KEEP_LAST</kind><depth>5</depth></historyQos><durability><kind>TRANSIENT_LOCAL</kind></durability></topic></publisher></profiles>";
        uint16_t datawriter_req = mr_write_configure_datawriter_xml(&session, reliable_out, datawriter_id, publisher_id, datawriter_xml, MR_REPLACE);

        // Send create entities message and wait its status
        uint8_t status[4];
        uint16_t requests[4] = {participant_req, topic_req, publisher_req, datawriter_req};
        if(!mr_run_session_until_status(&session, 1000, requests, status, 4))
        {
            printf("Error at create entities: participant: %i topic: %i publisher: %i darawriter: %i\n", status[0], status[1], status[2], status[3]);
            return 1;
        }

        // Write topics
        bool connected = true;
        uint32_t count = 0;
        while(connected && count < max_topics)
        {
            HelloWorld topic = {count++, "Hello DDS world!"};
            (void) mr_write_HelloWorld_topic(&session, reliable_out, datawriter_id, &topic);

            connected = mr_run_session_until_timeout(&session, 1000);
            if(connected)
            {
                printf("Sent topic: %s, id: %i\n", topic.message, topic.index);
            }
        }

        // Delete resources
        mr_delete_session(&session);
        mr_close_udp_transport(&transport);

        return 0;
    }

At this moment, the subscriber will receive the topics that are sending by the publisher.

In order to see the messages from the DDS Global Data Space point of view, you can use *Fast RTPS* HelloWorld example running a subscriber
(`Fast RTPS HelloWorld <http://eprosima-fast-rtps.readthedocs.io/en/latest/introduction.html#building-your-first-application>`_): ::

    $ cd /usr/local/examples/C++/HelloWorldExample
    $ sudo make && cd bin
    $ ./HelloWorldExample subscriber

Learn More
----------

To learn more about DDS and FastRTPS: `eProsima Fast RTPS <http://eprosima-fast-rtps.readthedocs.io>`_

To learn how to install *Micro RTPS* read: :ref:`installation_label`

To learn more about *Micro RTPS* read :ref:`user`

To learn more about *Micro RTPS Gen* read: :ref:`micrortpsgen_label`

