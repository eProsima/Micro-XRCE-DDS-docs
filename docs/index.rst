.. eProsima Micro RTPS documentation master file.

.. _micrortpsdoc:

eProsima Micro RTPS Documentation
=================================

.. image:: logo.png
   :height: 100px
   :width: 100px
   :align: left
   :alt: eProsima
   :target: http://www.eprosima.com/

*eprosima Micro RTPS* is a software solution which allows to communicate extremely resource constrained environments(XRCEs) with an existing DDS network. This implementation comply with the specification proposal, "eXtremely Resource Constrained Environments DDS (DDS-XRCE)" submitted to the Object Management Group (OMG) consortium.
TODO: Cambiar orden de las frases

*Micro RTPS* implements a client-server protocol in order to enable resource-constrained devices(clients) to participate in DDS communications, This communication is done through an *Micro RTPS Agent* (server). The *Micro RTPS Agent* acts on behalf of the *Micro RTPS Clients* and enable them to participate as DDS publishers and/or subscribers in the DDS Global Data Space.

*eProsima Micro RTPS* provides both, a plug and play *Micro RTPS Agent* and an API layer for the implementation of your own *Micro RTPS Clients*.
TODO: Meter mas chicha a esta frase

TODO: grafico de la arquitectura

Quick start
=============

*Micro RTPS* provides a C API which allows you to create your own *Micro RTPS Clients* publishing and/or listening to topics from DDS Glocal Data Space. The following example is a simple *Micro RTPS Clients* (Using that C API) and a *Micro RTPS Agent* publishing a "Hello DDS world!" messages to DDS world.

.. code-block:: c++

    // User type declaration
    typedef struct HelloWorld
    {
        uint32_t index;
        uint32_t message_length;
        char* message;
    } HelloTopic;

    // Serialization implementation provided by the user. Uses Eprosima MicroCDR.
    bool serialize_hello_topic(MicroBuffer* writer, const AbstractTopic* topic_structure)
    {
        HelloTopic* topic = (HelloTopic*) topic_structure->topic;
        serialize_uint32_t(writer, topic->index);
        serialize_array_char(writer, topic->message, topic->message_length);
        return true;
    }

    // User callback for receiving status messages from the Micro RTPS Agent.
    void on_status(XRCEInfo info, uint8_t operation, uint8_t status, void* args)
    {
        // Process status message.
    };

    // Data writer profile configuration as XML data.
    String data_writer_profile = {"<profiles><publisher profile_name=\"default_xrce_publisher_profile\"><topic><kind>NO_KEY</kind><name>HelloWorldTopic</name><dataType>HelloWorld</dataType><historyQos><kind>KEEP_LAST</kind><depth>5</depth></historyQos><durability><kind>TRANSIENT_LOCAL</kind></durability></topic></publisher></profiles>",
                                  300+1};

    int main(int args, char** argv)
        {
        // Creates a client state.
        ClientState* state = new_udp_client_state(4096, 2019, 2020);

        // Creates a client on the Micro RTPS Micro RTPS Agent.
        create_client(state, on_status, NULL);

        // Creates a participant on the Micro RTPS Agent.
        XRCEInfo participant_info = create_participant(state);

        // Register a topic on the given participant. Uses a topic configuration written in xml format
        String topic_profile = {"<dds><topic><name>HelloWorldTopic</name><dataType>HelloWorld</dataType></topic></dds>", 86};
        create_topic(state, participant_info.object_id, topic_profile);

        // Creates a publisher on the given participant
        XRCEInfo publisher_info = create_publisher(state, participant_info.object_id);

        // Creates a data writer using the participant and publisher recently created. This data writer is configured through a XML profile.
        String data_writer_profile = {"<xml>", 6};
        XRCEInfo data_writer_info = create_data_writer(state, participant_info.object_id, publisher_info.object_id, data_writer_profile);

        // Prepare and write the user data to be sent.
        char message[] = "Hello DDS world!";
        uint32_t length = strlen(message) + 1;
        HelloTopic hello_topic = {10, length, message};
        // Write user type data.
        write_data(state, data_writer_info.object_id, serialize_hello_topic, &hello_topic);

        // Send the data through the UDP transport.
        send_to_agent(state);

        // Free all the ClientState resources.
        free_client_state(state);
    }

Along with this *Micro RTPS Clients* you need to already have started a *Micro RTPS Agent* listening on the same UDP ports:
::
    $ ./micrortps_agent udp 2020 2019

and for seeing what is being received on the DDS side, you can use *Fast RTPS* HelloWorld example running a subscriber:
::
    $ ./HelloWorldExample subscriber

TODO: Link a FastRtps y como compilar el HelloWorld

This example shows how a *Micro RTPS Client* publish message on a DDS Global Data Space. For achieve that, you need to create different kind of entities on a *Micro RTPS Agent* using operations requests sent by *Micro RTPS Client*.

In the following figure is represented the hierarchy of objects you need to instantiate on the *Micro RTPS Agent* to publish on a topic:

TODO: Grafico de objetos ejemplo anterior

Learn More
----------

To learn more about DDS and FastRTPS: `eProsima Fast RTPS <http://eprosima-fast-rtps.readthedocs.io>`_

To learn how to install *Micro RTPS* read :ref:`installation`

To learn more about *Micro RTPS* read :ref:`user`


Index
-----
.. _installation:

.. toctree::
   :caption: Installation manual

   dependencies
   sources

.. _user:

.. toctree::
   :caption: User Manual

   introduction
   quickstart
   agent
   client
   entities
   operations
   more

.. _notes:

.. toctree::
   :caption: Release Notes

   notes
