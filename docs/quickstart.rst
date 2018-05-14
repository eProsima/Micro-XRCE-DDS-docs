.. _quickstart_label:

Quick start
===========

*Micro RTPS* provides a C API which allows you to create your own *Micro RTPS Clients* publishing and/or listening to topics from DDS Global Data Space.
The following example shows how create a simple *Micro RTPS Client* and a *Micro RTPS Agent* for publishing and subscribing to the DDS world, using this HelloWorld.idl: ::

    struct HelloWorld
    {
        unsigned long index;
        string message;
    };

First of all, we will start a *Micro RTPS Agent*. For this example, the client - agent communication will be done through UDP
(currently, there is two modes: UDP and serial): ::

    $ cd /usr/local/bin && MicroRTPSAgent udp 127.0.0.1 2018

Along with the *Agent*, the *PublishHelloWorldClient* example provided in the source code is launched.
This *Client* example will publish in the DDS World the HelloWorld topic. ::

    $ examples/PublishHelloWorld/PublishHelloWorldClient udp 127.0.0.1 2018

The following shows the source code of the *PublishHelloWorldClient* example.

.. code-block:: C

    #include "HelloWorld.h"

    #include <stdio.h>

    void check_and_print_error(Session* session, const char* where)
    {
        if(session->last_status_received)
        {
            if(session->last_status.status == STATUS_OK)
            {
                return; //All things go well
            }
            else
            {
                printf("%sStatus error (%i)%s", "\x1B[1;31m", session->last_status.status, "\x1B[0m");
            }
        }
        else
        {
            printf("%sConnection error", "\x1B[1;31m");
        }

        printf(" at %s%s\n", where, "\x1B[0m");
        exit(1);
    }

    int main(int args, char** argv)
    {
        Session my_session;
        ClientKey key = {{0xAA, 0xAA, 0xAA, 0xAA}};
        if(args == 3 && strcmp(argv[1], "serial") == 0)
        {
            const char* device = argv[2];
            if(!new_serial_session(&my_session, 0x01, key, device, NULL, NULL))
            {
                printf("%sCan not create serial connection%s\n", "\x1B[1;31m", "\x1B[0m");
                return 1;
            }
            printf("<< Serial mode => dev: %s >>\n", device);
        }
        else if(args == 4 && strcmp(argv[1], "udp") == 0)
        {
            uint8_t ip[4];
            int length = sscanf(argv[2], "%d.%d.%d.%d", (int*)&ip[0], (int*)&ip[1], (int*)&ip[2], (int*)&ip[3]);
            if(length != 4)
            {
                printf("%sIP must have th format a.b.c.d%s\n", "\x1B[1;31m", "\x1B[0m");
                return 1;
            }

            uint16_t port = (uint16_t)atoi(argv[3]);
            if(!new_udp_session(&my_session, 0x01, key, ip, port, NULL, NULL))
            {
                printf("%sCan not create a socket%s\n", "\x1B[1;31m", "\x1B[0m");
                return 1;
            }
            printf("<< UDP mode => ip: %s - port: %hu >>\n", argv[2], port);
        }
        else
        {
            printf("Usage: program <command>\n");
            printf("List of commands:\n");
            printf("    serial device\n");
            printf("    udp agent_ip agent_port\n");
            printf("    help\n");
            return 1;
        }

        /* Init session. */
        init_session_sync(&my_session);
        check_and_print_error(&my_session, "init session");

        /* Init XRCE objects. */
        ObjectId participant_id = {{0x00, OBJK_PARTICIPANT}};
        create_participant_sync_by_ref(&my_session, participant_id, "default_participant", false, false);
        check_and_print_error(&my_session, "create participant");

        const char* topic_xml = {"<dds><topic><name>HelloWorldTopic</name><dataType>HelloWorld</dataType></topic></dds>"};
        ObjectId topic_id = {{0x00, OBJK_TOPIC}};
        create_topic_sync_by_xml(&my_session, topic_id, topic_xml, participant_id, false, false);
        check_and_print_error(&my_session, "create topic");

        const char* publisher_xml = {"<publisher name=\"MyPublisher\""};
        ObjectId publisher_id = {{0x00, OBJK_PUBLISHER}};
        create_publisher_sync_by_xml(&my_session, publisher_id, publisher_xml, participant_id, false, false);
        check_and_print_error(&my_session, "create publisher");

        const char* datawriter_xml = {"<profiles><publisher profile_name=\"default_xrce_publisher_profile\"><topic><kind>NO_KEY</kind><name>HelloWorldTopic</name><dataType>HelloWorld</dataType><historyQos><kind>KEEP_LAST</kind><depth>5</depth></historyQos><durability><kind>TRANSIENT_LOCAL</kind></durability></topic></publisher></profiles>"};
        ObjectId datawriter_id = {{0x00, OBJK_DATAWRITER}};
        create_datawriter_sync_by_xml(&my_session, datawriter_id, datawriter_xml, publisher_id, false, false);
        check_and_print_error(&my_session, "create datawriter");

        /* Main loop */
        int32_t count = 0;
        while(true)
        {
            /* Write HelloWorld topic */
            HelloWorld topic;
        topic.index = ++count;
        topic.message = "Hello DDS world!";
            write_HelloWorld(&my_session, datawriter_id, STREAMID_BUILTIN_RELIABLE, &topic);
            printf("Send topic: %s, count: %i\n", topic.message, topic.index);

            run_communication(&my_session);

            ms_sleep(1000);
        }

        close_session_sync(&my_session);
        check_and_print_error(&my_session, "close session");

        free_session(&my_session);

        return 0;
    }

In order to see the messages from the DDS Global Data Space point of view, you can use *Fast RTPS* HelloWorld example running a subscriber
(`Fast RTPS HelloWorld <http://eprosima-fast-rtps.readthedocs.io/en/latest/introduction.html#building-your-first-application>`_): ::

    $ cd /usr/local/examples/C++/HelloWorldExample
    $ sudo make && cd bin
    $ ./HelloWorldExample subscriber

This example shows how a *Micro RTPS Client* publishes messages on a DDS Global Data Space.
*Micro RTPS Client* can also subscribe to messages from a DDS Global Data Space.
To run a subscriber HelloWorld topic from the client, execute the example: ::

    $ examples/PublishHelloWorld/PublishHelloWorldClient 127.0.0.1 2018

Learn More
----------

To learn more about DDS and FastRTPS: `eProsima Fast RTPS <http://eprosima-fast-rtps.readthedocs.io>`_

To learn how to install *Micro RTPS* read: :ref:`installation_label`

To learn more about *Micro RTPS* read :ref:`user`

To learn more about *Micro RTPS Gen* read: :ref:`micrortpsgen_label`

