.. _quickstart_label:

Quick start
===========
`Micro XRCE-DDS` provides a C API which allows the creation of `Micro XRCE-DDS Clients` that publish and/or subscribe to topics from DDS Global Data Space.
The following example shows how to create a simple `Micro XRCE-DDS Client` and `Micro XRCE-DDS Agent` for publishing and subscribing to the DDS world, using this HelloWorld.idl: ::

    struct HelloWorld
    {
        unsigned long index;
        string message;
    };

First of all, we launch the `Agent`. For this example, the `Client` - `Agent` communication will be done through UDP: ::

    $ cd /usr/local/bin && MicroXRCE-DDSAgent udp 2018

Along with the `Agent`, the `PublishHelloWorldClient` example provided in the source code is launched.
This `Client` example will publish in the DDS World the HelloWorld topic. ::

    $ examples/PublishHelloWorld/PublishHelloWorldClient

The code of the *PublishHelloWorldClient* is the following:

.. code-block:: C

    #include "HelloWorldWriter.h"

    #include <uxr/client/client.h>
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
        if(!mr_run_session_until_all_status(&session, 1000, requests, status, 4))
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

            mcBuffer mb;
            uint32_t topic_size = HelloWorld_size_of_topic(&topic, 0);
            mr_prepare_output_stream(&session, reliable_out, datawriter_id, &mb, topic_size);
            HelloWorld_serialize_topic(&mb, &topic);

            connected = mr_run_session_until_time(&session, 1000);
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

        #include "HelloWorld.h"

        #include <uxr/client/client.h>
        #include <string.h> //strcmp
        #include <stdlib.h> //atoi
        #include <stdio.h>

        #define STREAM_HISTORY  8
        #define BUFFER_SIZE     MR_CONFIG_UDP_TRANSPORT_MTU * STREAM_HISTORY

        void on_topic(mrSession* session, mrObjectId object_id, uint16_t request_id, mrStreamId stream_id, struct mcBuffer* mb, void* args)
        {
            (void) session; (void) object_id; (void) request_id; (void) stream_id;

            HelloWorld topic;
            HelloWorld_deserialize_topic(mb, &topic);

            printf("Received topic: %s, id: %i\n", topic.message, topic.index);

            uint32_t* count_ptr = (uint32_t*) args;
            (*count_ptr)++;
        }

        int main(int args, char** argv)
        {
            if(args >= 2 && (0 == strcmp("-h", argv[1]) || 0 == strcmp("--help", argv[1]) || 0 == atoi(argv[1])))
            {
                printf("usage: program [-h | --help | <topics>]\n");
                return 0;
            }

            uint32_t count = 0;
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
            mr_init_session(&session, &transport.comm, 0xCCCCDDDD);
            mr_set_topic_callback(&session, on_topic, &count);
            if(!mr_create_session(&session))
            {
                printf("Error at create session.\n");
                return 1;
            }

            // Streams
            uint8_t output_reliable_stream_buffer[BUFFER_SIZE];
            mrStreamId reliable_out = mr_create_output_reliable_stream(&session, output_reliable_stream_buffer, BUFFER_SIZE, STREAM_HISTORY);

            uint8_t input_reliable_stream_buffer[BUFFER_SIZE];
            mrStreamId reliable_in = mr_create_input_reliable_stream(&session, input_reliable_stream_buffer, BUFFER_SIZE, STREAM_HISTORY);

            // Create entities
            mrObjectId participant_id = mr_object_id(0x01, MR_PARTICIPANT_ID);
            const char* participant_ref = "default participant";
            uint16_t participant_req = mr_write_create_participant_ref(&session, reliable_out, participant_id, 0, participant_ref, MR_REPLACE);

            mrObjectId topic_id = mr_object_id(0x01, MR_TOPIC_ID);
            const char* topic_xml = "<dds><topic><name>HelloWorldTopic</name><dataType>HelloWorld</dataType></topic></dds>";
            uint16_t topic_req = mr_write_configure_topic_xml(&session, reliable_out, topic_id, participant_id, topic_xml, MR_REPLACE);

            mrObjectId subscriber_id = mr_object_id(0x01, MR_SUBSCRIBER_ID);
            const char* subscriber_xml = "<subscriber name=\"MySubscriber\">";
            uint16_t subscriber_req = mr_write_configure_subscriber_xml(&session, reliable_out, subscriber_id, participant_id, subscriber_xml, MR_REPLACE);

            mrObjectId datareader_id = mr_object_id(0x01, MR_DATAREADER_ID);
            const char* datareader_xml = "<profiles><subscriber profile_name=\"default_xrce_subscriber_profile\"><topic><kind>NO_KEY</kind><name>HelloWorldTopic</name><dataType>HelloWorld</dataType><historyQos><kind>KEEP_LAST</kind><depth>5</depth></historyQos><durability><kind>TRANSIENT_LOCAL</kind></durability></topic></subscriber></profiles>";
            uint16_t datareader_req = mr_write_configure_datareader_xml(&session, reliable_out, datareader_id, subscriber_id, datareader_xml, MR_REPLACE);

            // Send create entities message and wait its status
            uint8_t status[4];
            uint16_t requests[4] = {participant_req, topic_req, subscriber_req, datareader_req};
            if(!mr_run_session_until_all_status(&session, 1000, requests, status, 4))
            {
                printf("Error at create entities: participant: %i topic: %i subscriber: %i datareader: %i\n", status[0], status[1], status[2], status[3]);
                return 1;
            }

            // Request topics
            mrDeliveryControl delivery_control = {0};
            delivery_control.max_samples = MR_MAX_SAMPLES_UNLIMITED;
            uint16_t read_data_req = mr_write_request_data(&session, reliable_out, datareader_id, reliable_in, &delivery_control);

            // Read topics
            bool connected = true;
            while(connected && count < max_topics)
            {
                uint8_t read_data_status;
                connected = mr_run_session_until_all_status(&session, MR_TIMEOUT_INF, &read_data_req, &read_data_status, 1);
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

To learn more about DDS and Fast RTPS: `eProsima Fast RTPS <http://eprosima-fast-rtps.readthedocs.io>`_

To learn how to install *Micro XRCE-DDS* read: :ref:`installation_label`

To learn more about *Micro XRCE-DDS* read :ref:`user`

To learn more about *Micro XRCE-DDS Gen* read: :ref:`microxrceddsgen_label`

