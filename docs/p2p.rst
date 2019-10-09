.. _p2p_communication_label:

P2P Communication
=================

The peer-to-peer (P2P) mode allows direct communication between applications without DDS,
understanding by application a set of an *Agent* and one o more *Clients* running in the same device.

In this communication mode, the *Agent* uses the :ref:`CedMiddleware <ced_middleware_label>`.
The *Agent* is in charge of discovering other *External Agents* and create an *Internal Client* for each one of them.
Each *Internal Client* connects to an *External Agent* and subscribes to the set of Topics managed by its own *Agent*.
Thus, a cloud of *Agents* interconnected is create.

.. image:: images/p2p_overview.svg

How to use P2P
--------------

Some consideration shall be taken into account in order to use the P2P communication:

#. Only the `create_<entity>_by_ref()` functions shall be used.
#. The Topic's reference represents the name of the Topic.
#. The DataWriter's and DataReader's references shall match the Topic's reference.
#. Publishers and Subscriber have no roles.
#. Agents shall use CedMiddleware and Discovery server.

Publish/Subscribe Example
^^^^^^^^^^^^^^^^^^^^^^^^^

The following code shows a Publisher and Subscribe P2P application which take into account the aforementioned considerations.
These *Client* application can be tested launching the following *eProsima Micro XRCE-DDS Agent* application:

    $ ./MicroXRCEAgent udp -p <port> -m ced -d

.. code-block:: C

    /*
     * Publisher example
     */

    #include "HelloWorld.h"
    
    #include <uxr/client/client.h>
    #include <ucdr/microcdr.h>
    
    #include <stdio.h> //printf
    #include <string.h> //strcmp
    #include <stdlib.h> //atoi
    
    #define STREAM_HISTORY  8
    #define BUFFER_SIZE     UXR_CONFIG_UDP_TRANSPORT_MTU * STREAM_HISTORY
    
    int main(int args, char** argv)
    {
        // CLI
        if(3 > args || 0 == atoi(argv[2]))
        {
            printf("usage: program [-h | --help] | ip port [<max_topics>]\n");
            return 0;
        }
    
        char* ip = argv[1];
        uint16_t port = (uint16_t)atoi(argv[2]);
        uint32_t max_topics = (args == 4) ? (uint32_t)atoi(argv[3]) : UINT32_MAX;
    
        // Transport
        uxrUDPTransport transport;
        uxrUDPPlatform udp_platform;
        if(!uxr_init_udp_transport(&transport, &udp_platform, ip, port))
        {
            printf("Error at create transport.\n");
            return 1;
        }
    
        // Session
        uxrSession session;
        uxr_init_session(&session, &transport.comm, 0xAAAABBBB);
        if(!uxr_create_session(&session))
        {
            printf("Error at create session.\n");
            return 1;
        }
    
        // Streams
        uint8_t output_reliable_stream_buffer[BUFFER_SIZE];
        uxrStreamId reliable_out = 
            uxr_create_output_reliable_stream(
                &session,
                output_reliable_stream_buffer,
                BUFFER_SIZE,
                STREAM_HISTORY);
    
        uint8_t input_reliable_stream_buffer[BUFFER_SIZE];
        uxr_create_input_reliable_stream(
            &session,
            input_reliable_stream_buffer,
            BUFFER_SIZE,
            STREAM_HISTORY);
    
        // Create entities
        uxrObjectId participant_id = uxr_object_id(0x01, UXR_PARTICIPANT_ID);
        const char* participant_ref = "participant_name";
        uint16_t participant_req = 
            uxr_buffer_create_participant_ref(
                &session,
                reliable_out,
                participant_id,
                0,
                participant_ref,
                UXR_REPLACE);
    
        uxrObjectId topic_id = uxr_object_id(0x01, UXR_TOPIC_ID);
        const char* topic_ref = "topic_name";
        uint16_t topic_req = 
            uxr_buffer_create_topic_ref(
                &session,
                reliable_out,
                topic_id,
                participant_id,
                topic_ref,
                UXR_REPLACE);
    
        uxrObjectId publisher_id = uxr_object_id(0x01, UXR_PUBLISHER_ID);
        const char* publisher_xml = "";
        uint16_t publisher_req = 
            uxr_buffer_create_publisher_xml(
                &session,
                reliable_out,
                publisher_id,
                participant_id,
                publisher_xml,
                UXR_REPLACE);
    
        uxrObjectId datawriter_id = uxr_object_id(0x01, UXR_DATAWRITER_ID);
        const char* datawriter_ref = topic_ref; // It shall match the topic_ref;
        uint16_t datawriter_req = 
            uxr_buffer_create_datawriter_xml(
                &session,
                reliable_out,
                datawriter_id,
                publisher_id,
                datawriter_ref,
                UXR_REPLACE);
    
        // Send create entities message and wait its status
        uint8_t status[4];
        uint16_t requests[4] = {participant_req, topic_req, publisher_req, datawriter_req};
        if(!uxr_run_session_until_all_status(&session, 1000, requests, status, 4))
        {
            printf(
                "Error at create entities: participant: %i topic: %i publisher: %i darawriter: %i\n",
                status[0], status[1], status[2], status[3]);
            return 1;
        }
    
        // Write topics
        bool connected = true;
        uint32_t count = 0;
        while(connected && count < max_topics)
        {
            HelloWorld topic = {++count, "Hello DDS world!"};
    
            ucdrBuffer ub;
            uint32_t topic_size = HelloWorld_size_of_topic(&topic, 0);
            uxr_prepare_output_stream(&session, reliable_out, datawriter_id, &ub, topic_size);
            HelloWorld_serialize_topic(&ub, &topic);
    
            printf("Send topic: %s, id: %i\n", topic.message, topic.index);
            connected = uxr_run_session_time(&session, 1000);
        }
    
        // Delete resources
        uxr_delete_session(&session);
        uxr_close_udp_transport(&transport);
    
        return 0;
    }

.. code-block:: C

    /*
     * Subscriber example
     */

    #include "HelloWorld.h"
    
    #include <uxr/client/client.h>
    
    #include <stdio.h> //printf
    #include <string.h> //strcmp
    #include <stdlib.h> //atoi
    
    #define STREAM_HISTORY  8
    #define BUFFER_SIZE     UXR_CONFIG_UDP_TRANSPORT_MTU * STREAM_HISTORY
    
    void on_topic(uxrSession* session, uxrObjectId object_id, uint16_t request_id, uxrStreamId stream_id, struct ucdrBuffer* ub, void* args)
    {
        (void) session; (void) object_id; (void) request_id; (void) stream_id;
    
        HelloWorld topic;
        HelloWorld_deserialize_topic(ub, &topic);
    
        printf("Received topic: %s, id: %i\n", topic.message, topic.index);
    
        uint32_t* count_ptr = (uint32_t*) args;
        (*count_ptr)++;
    }
    
    int main(int args, char** argv)
    {
        // CLI
        if(3 > args || 0 == atoi(argv[2]))
        {
            printf("usage: program [-h | --help] | ip port [<max_topics>]\n");
            return 0;
        }
    
        char* ip = argv[1];
        uint16_t port = (uint16_t)atoi(argv[2]);
        uint32_t max_topics = (args == 4) ? (uint32_t)atoi(argv[3]) : UINT32_MAX;
    
        // State
        uint32_t count = 0;
    
        // Transport
        uxrUDPTransport transport;
        uxrUDPPlatform udp_platform;
        if(!uxr_init_udp_transport(&transport, &udp_platform, ip, port))
        {
            printf("Error at create transport.\n");
            return 1;
        }
    
        // Session
        uxrSession session;
        uxr_init_session(&session, &transport.comm, 0xCCCCDDDD);
        uxr_set_topic_callback(&session, on_topic, &count);
        if(!uxr_create_session(&session))
        {
            printf("Error at create session.\n");
            return 1;
        }
    
        // Streams
        uint8_t output_reliable_stream_buffer[BUFFER_SIZE];
        uxrStreamId reliable_out = uxr_create_output_reliable_stream(&session, output_reliable_stream_buffer, BUFFER_SIZE, STREAM_HISTORY);
    
        uint8_t input_reliable_stream_buffer[BUFFER_SIZE];
        uxrStreamId reliable_in = uxr_create_input_reliable_stream(&session, input_reliable_stream_buffer, BUFFER_SIZE, STREAM_HISTORY);
    
        // Create entities
        uxrObjectId participant_id = uxr_object_id(0x01, UXR_PARTICIPANT_ID);
        const char* participant_ref = "participant_name";
        uint16_t participant_req = uxr_buffer_create_participant_ref(&session, reliable_out, participant_id, 0, participant_ref, UXR_REPLACE);
    
        uxrObjectId topic_id = uxr_object_id(0x01, UXR_TOPIC_ID);
        const char* topic_ref = "topic_name";
        uint16_t topic_req = uxr_buffer_create_topic_ref(&session, reliable_out, topic_id, participant_id, topic_ref, UXR_REPLACE);
    
        uxrObjectId subscriber_id = uxr_object_id(0x01, UXR_SUBSCRIBER_ID);
        const char* subscriber_xml = "";
        uint16_t subscriber_req = uxr_buffer_create_subscriber_xml(&session, reliable_out, subscriber_id, participant_id, subscriber_xml, UXR_REPLACE);
    
        uxrObjectId datareader_id = uxr_object_id(0x01, UXR_DATAREADER_ID);
        const char* datareader_ref = topic_ref;
        uint16_t datareader_req = uxr_buffer_create_datareader_ref(&session, reliable_out, datareader_id, subscriber_id, datareader_ref, UXR_REPLACE);
    
        // Send create entities message and wait its status
        uint8_t status[4];
        uint16_t requests[4] = {participant_req, topic_req, subscriber_req, datareader_req};
        if(!uxr_run_session_until_all_status(&session, 1000, requests, status, 4))
        {
            printf("Error at create entities: participant: %i topic: %i subscriber: %i datareader: %i\n", status[0], status[1], status[2], status[3]);
            return 1;
        }
    
        // Request topics
        uxrDeliveryControl delivery_control = {0};
        delivery_control.max_samples = UXR_MAX_SAMPLES_UNLIMITED;
        uint16_t read_data_req = uxr_buffer_request_data(&session, reliable_out, datareader_id, reliable_in, &delivery_control);
    
        // Read topics
        bool connected = true;
        while(connected && count < max_topics)
        {
            uint8_t read_data_status;
            connected = uxr_run_session_until_all_status(&session, UXR_TIMEOUT_INF, &read_data_req, &read_data_status, 1);
        }
    
        // Delete resources
        uxr_delete_session(&session);
        uxr_close_udp_transport(&transport);
    
        return 0;
    }
