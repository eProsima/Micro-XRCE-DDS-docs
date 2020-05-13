.. _client_service_label:

Client-service application
==========================

This section shows an example of a client-service application using the `Requester` and `Replier` entities.
This applicatión has two ends, the client (*RequestAdder*) and the service (*ReplyAdder*).
On the one hand, the client is in charge of sending requests which contain two integers, as well as receiving the responses from the service.
On the other hand, the service is in charge of receiving the requests from the client, summing the two integers, and finally sending the response to the client.

Before considering the client and service examples, it is first necessary to briefly summarize how the `Requester` and `Replier` entities work,
as well as to list the functions related to both entities.

Requester
^^^^^^^^^

The `Requester` entity is composed of a `Publisher` and a `Subscriber` associated with a `RequestTopic` and a `ReplyTopic` respectively.
The `Publisher` is in charge of sending the request, while the `Susbscriber` receives the replies.

To create a `Requester` entity, the ``uxr_buffer_create_requester_xml`` or ``uxr_buffer_create_requester_ref`` shall be used.
Once created, requests can be sent through ``uxr_buffer_request``, and replies can be received, making a data request to the *Agent* using the ``uxr_buffer_request_data``.
Replies are received through the `on_reply` callback which shall be set by the ``uxr_set_reply_callback``.
This callback has a parameter `reply_id` which corresponds to the identifier returned by the ``uxr_buffer_request`` call.

Replier
^^^^^^^

The `Reply` entity is a mirror of the `Requester`, that is, it contains a `Publisher` and a `Subscriber` as well, but the topic association is reversed,
as the `Publisher` is associated with the `ReplyTopic` and the `Subscriber` to the `RequestTopic`.
In this case, the `Subscriber` is in charge of receiving the request from the `Requester`, while the `Publisher` sends the replies.

To create a `Replier` entity, the ``uxr_buffer_create_replier_xml`` or ``uxr_buffer_create_replier_ref`` shall be used.
Once created, replies can be sent through ``uxr_buffer_reply``, and requests can be received, making a data request to the *Agent* using the ``uxr_buffer_request_data``. 
Requests are received through the `on_request` callback which shall be set by the ``uxr_set_request_callback``.
This callback has a parameter `sample_id` which identifies the request and should be used in the ``uxr_buffer_reply``.

Client example
^^^^^^^^^^^^^^

The code below shows the *RequestAdder* example available at `eProsima Micro XRCE-DDS Client <https://github.com/eProsima/Micro-XRCE-DDS-Client/tree/master/examples/RequesterAdder>`__.
This example represents a client application that sends two integers as requests, and receives the sum of both integers as response.

.. code-block:: C

    // Copyright 2019 Proyectos y Sistemas de Mantenimiento SL (eProsima).
    //
    // Licensed under the Apache License, Version 2.0 (the "License");
    // you may not use this file except in compliance with the License.
    // You may obtain a copy of the License at
    //
    //     http://www.apache.org/licenses/LICENSE-2.0
    //
    // Unless required by applicable law or agreed to in writing, software
    // distributed under the License is distributed on an "AS IS" BASIS,
    // WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    // See the License for the specific language governing permissions and
    // limitations under the License.

    #include <uxr/client/client.h>

    #include <stdio.h>
    #include <string.h>
    #include <stdlib.h>
    #include <inttypes.h>

    #define STREAM_HISTORY  8
    #define BUFFER_SIZE     UXR_CONFIG_UDP_TRANSPORT_MTU * STREAM_HISTORY

    void on_reply(uxrSession* session, uxrObjectId object_id, uint16_t request_id, uint16_t reply_id, uint8_t* buffer, size_t len, void* args)
    {
        (void) object_id;
        (void) request_id;

        ucdrBuffer ub;
        ucdr_init_buffer(&ub, buffer, len);

        uint64_t result;
        ucdr_deserialize_uint64_t(&ub, &result);

    #ifdef WIN32
        printf("Reply received: %I64u [id: %d]\n", result, reply_id);
    #else
        printf("Reply received: %" PRIu64 " [id: %d]\n", result, reply_id);
    #endif
    }

    int main(int args, char** argv)
    {
        if(3 > args || 0 == atoi(argv[2]))
        {
            printf("usage: program [-h | --help] | ip port [key]\n");
            return 0;
        }

        char* ip = argv[1];
        char* port = argv[2];
        uint32_t key = (args == 4) ? (uint32_t)atoi(argv[3]) : 0xAAAABBBB;

        // Transport
        uxrUDPTransport transport;
        uxrUDPPlatform udp_platform;
        if (!uxr_init_udp_transport(&transport, &udp_platform, UXR_IPv4, ip, port))
        {
            printf("Error at init transport.\n");
            return 1;
        }

        // Session
        uxrSession session;
        uxr_init_session(&session, &transport.comm, key);
        uxr_set_reply_callback(&session, on_reply, false);
        if (!uxr_create_session(&session))
        {
            printf("Error at init session.\n");
            return 1;
        }

        // Streams
        uint8_t output_reliable_stream_buffer[BUFFER_SIZE];
        uxrStreamId reliable_out = uxr_create_output_reliable_stream(&session, output_reliable_stream_buffer, BUFFER_SIZE, STREAM_HISTORY);

        uint8_t input_reliable_stream_buffer[BUFFER_SIZE];
        uxrStreamId reliable_in = uxr_create_input_reliable_stream(&session, input_reliable_stream_buffer, BUFFER_SIZE, STREAM_HISTORY);

        // Create entities
        uxrObjectId participant_id = uxr_object_id(0x01, UXR_PARTICIPANT_ID);
        const char* participant_xml = "<dds>"
                                          "<participant>"
                                              "<rtps>"
                                                  "<name>default_xrce_participant</name>"
                                              "</rtps>"
                                          "</participant>"
                                      "</dds>";
        uint16_t participant_req = uxr_buffer_create_participant_xml(&session, reliable_out, participant_id, 0, participant_xml, UXR_REPLACE);

        uxrObjectId requester_id = uxr_object_id(0x01, UXR_REQUESTER_ID);
        const char* requester_xml = "<dds>"
                                        "<requester profile_name=\"my_requester\""
                                                   "service_name=\"service_name\""
                                                   "request_type=\"request_type\""
                                                   "reply_type=\"reply_type\">"
                                        "</requester>"
                                    "</dds>";
        uint16_t requester_req = uxr_buffer_create_requester_xml(&session, reliable_out, requester_id, participant_id, requester_xml, UXR_REPLACE);

        // Send create entities message and wait its status
        uint8_t status[2];
        uint16_t requests[2] = {participant_req, requester_req};
        if(!uxr_run_session_until_all_status(&session, 1000, requests, status, 2))
        {
            printf("Error at create entities: participant: %i requester: %i\n", status[0], status[1]);
            return 1;
        }

        // Request replies
        uxrDeliveryControl delivery_control = {0};
        delivery_control.max_samples = UXR_MAX_SAMPLES_UNLIMITED;
        uint16_t read_data_req = uxr_buffer_request_data(&session, reliable_out, requester_id, reliable_in, &delivery_control);

        // Write requests
        bool connected = true;
        uint32_t count = 0;
        while (connected)
        {
            uint8_t request[2 * 4] = {0};
            ucdrBuffer ub;

            ucdr_init_buffer(&ub, request, sizeof(request));
            ucdr_serialize_uint32_t(&ub, count);
            ucdr_serialize_uint32_t(&ub, count);

            uint16_t request_id = uxr_buffer_request(&session, reliable_out, requester_id, request, sizeof(request));
            printf("Request sent: (%d + %d) [id: %d]\n", count, count, request_id);
            connected = uxr_run_session_time(&session, 1000);

            ++count;
        }

        return 0;
    }

Service example
^^^^^^^^^^^^^^^

The code below shows the *ReplyAdder* example available at `eProsima Micro XRCE-DDS Client <https://github.com/eProsima/Micro-XRCE-DDS-Client/tree/master/examples/ReplyAdder>`__.
This example represents a service application that receives requests composed by two integers, sums both numbers, and finally sends the response.

.. code-block:: C

    // Copyright 2019 Proyectos y Sistemas de Mantenimiento SL (eProsima).
    //
    // Licensed under the Apache License, Version 2.0 (the "License");
    // you may not use this file except in compliance with the License.
    // You may obtain a copy of the License at
    //
    //     http://www.apache.org/licenses/LICENSE-2.0
    //
    // Unless required by applicable law or agreed to in writing, software
    // distributed under the License is distributed on an "AS IS" BASIS,
    // WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    // See the License for the specific language governing permissions and
    // limitations under the License.

    #include <uxr/client/client.h>

    #include <stdio.h>
    #include <string.h>
    #include <stdlib.h>
    #include <inttypes.h>

    #define STREAM_HISTORY  8
    #define BUFFER_SIZE     UXR_CONFIG_UDP_TRANSPORT_MTU * STREAM_HISTORY

    static uxrStreamId reliable_out;
    static uxrStreamId reliable_in;

    static uxrObjectId participant_id;
    static uxrObjectId replier_id;

    void on_request(uxrSession* session, uxrObjectId object_id, uint16_t request_id, SampleIdentity* sample_id, uint8_t* request_buffer, size_t request_len, void* args)
    {
        (void) object_id;
        (void) request_id;

        uint32_t rhs;
        uint32_t lhs;
        ucdrBuffer request_ub;
        ucdr_init_buffer(&request_ub, request_buffer, request_len);
        ucdr_deserialize_uint32_t(&request_ub, &rhs);
        ucdr_deserialize_uint32_t(&request_ub, &lhs);

        printf("Request received: (%d + %d)\n", rhs, lhs);

        uint8_t reply_buffer[8] = {0};
        ucdrBuffer reply_ub;
        ucdr_init_buffer(&reply_ub, reply_buffer, sizeof(reply_buffer));
        ucdr_serialize_uint64_t(&reply_ub, rhs + lhs);

        uxr_buffer_reply(session, reliable_out, replier_id, sample_id, reply_buffer, sizeof(reply_buffer));

    #ifdef WIN32
        printf("Reply send: %I64u\n", (uint64_t)(rhs + lhs));
    #else
        printf("Reply send: %" PRIu64 "\n", (uint64_t)(rhs + lhs));
    #endif
    }

    int main(int args, char** argv)
    {
        if(3 > args || 0 == atoi(argv[2]))
        {
            printf("usage: program [-h | --help] | ip port [key]\n");
            return 0;
        }

        char* ip = argv[1];
        char* port = argv[2];
        uint32_t key = (args == 4) ? (uint32_t)atoi(argv[3]) : 0xCCCCDDDD;

        // Transport
        uxrUDPTransport transport;
        uxrUDPPlatform udp_platform;
        if (!uxr_init_udp_transport(&transport, &udp_platform, UXR_IPv4, ip, port))
        {
            printf("Error at init transport.\n");
            return 1;
        }

        // Session
        uxrSession session;
        uxr_init_session(&session, &transport.comm, key);
        uxr_set_request_callback(&session, on_request, 0);
        if (!uxr_create_session(&session))
        {
            printf("Error at init session.\n");
            return 1;
        }

        // Streams
        uint8_t output_reliable_stream_buffer[BUFFER_SIZE];
        reliable_out = uxr_create_output_reliable_stream(&session, output_reliable_stream_buffer, BUFFER_SIZE, STREAM_HISTORY);

        uint8_t input_reliable_stream_buffer[BUFFER_SIZE];
        reliable_in = uxr_create_input_reliable_stream(&session, input_reliable_stream_buffer, BUFFER_SIZE, STREAM_HISTORY);

        // Create entities
        participant_id = uxr_object_id(0x01, UXR_PARTICIPANT_ID);
        const char* participant_xml = "<dds>"
                                          "<participant>"
                                              "<rtps>"
                                                  "<name>default_xrce_participant</name>"
                                              "</rtps>"
                                          "</participant>"
                                      "</dds>";
        uint16_t participant_req = uxr_buffer_create_participant_xml(&session, reliable_out, participant_id, 0, participant_xml, UXR_REPLACE);

        replier_id = uxr_object_id(0x01, UXR_REPLIER_ID);
        const char* replier_xml = "<dds>"
                                    "<replier profile_name=\"my_requester\""
                                             "service_name=\"service_name\""
                                             "request_type=\"request_type\""
                                             "reply_type=\"reply_type\">"
                                    "</replier>"
                                    "</dds>";
        uint16_t replier_req = uxr_buffer_create_replier_xml(&session, reliable_out, replier_id, participant_id, replier_xml, UXR_REPLACE);

        // Send create entities message and wait its status
        uint8_t status[2];
        uint16_t requests[2] = {participant_req, replier_req};
        if(!uxr_run_session_until_all_status(&session, 1000, requests, status, 2))
        {
            printf("Error at create entities: participant: %i requester: %i\n", status[0], status[1]);
            return 1;
        }

        // Request  requests
        uxrDeliveryControl delivery_control = {0};
        delivery_control.max_samples = UXR_MAX_SAMPLES_UNLIMITED;
        uint16_t read_data_req = uxr_buffer_request_data(&session, reliable_out, replier_id, reliable_in, &delivery_control);

        // Read request
        bool connected = true;
        while (connected)
        {
            uint8_t read_data_status;
            connected = uxr_run_session_until_all_status(&session, UXR_TIMEOUT_INF, &read_data_req, &read_data_status, 1);
        }

        return 0;
    }
