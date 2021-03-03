.. _getting_started_label:

Getting started
===============
This page shows how to get started with the *eProsima Micro XRCE-DDS Client*.
We will create a *Client* that can publish and subscribe to a topic,
or engage in a request-reply kind of communication.
Also, we illustrate how to create C code consumable by the client from a IDL file with
*eProsima Micro XRCE-DDS Gen*.
Finally, we provide a deployment example.

The section is organized as follows:

- :ref:`prerequisites`
- :ref:`idl_example`
- :ref:`init_session`
- :ref:`setup_participant`
- :ref:`create_topics`
- :ref:`pubs_and_subs`
- :ref:`dws_and_drs`
- :ref:`reqs_and_reps`
- :ref:`agent_response`
- :ref:`write_data`
- :ref:`read_data`
- :ref:`close_client`
- :ref:`deployment_example`

.. hint:: 
    The code shown here can be found in the examples below:

    * :code:`examples/PublisherHelloWorld`
    * :code:`examples/SubscriberHelloWorld`
    * :code:`examples/ReplyAdder`
    * :code:`examples/RequestAdder`
    * :code:`examples/Deployment`

.. note::
    This example makes use of the creation mode by XML, which is one of the two possible representation formats for creating DDS entities:
    by XML or by reference (see the :ref:`creation_mode_client` and :ref:`creation_mode_agent` sections).

.. _prerequisites:

Prerequisites
^^^^^^^^^^^^^

First, make sure to have correctly installed the following:

- :ref:`install_agent`.
- :ref:`install_client`.
- :ref:`install_gen`.

.. _idl_example:

Generate code from an IDL
^^^^^^^^^^^^^^^^^^^^^^^^^
We will use HelloWorld as our Topic whose IDL is the following: ::

    struct HelloWorld
    {
        unsigned long index;
        string message;
    };

In the *Client* we need to create an equivalent C type with its serialization/deserialization code.
This is done automatically by :ref:`microxrceddsgen_label`: ::

    $ microxrceddsgen HelloWorld.idl

.. _init_session:

Initialize a Session
^^^^^^^^^^^^^^^^^^^^

In the source example file, we include the generated type code, to have access to its serialization/deserialization functions along to the writing function.
Also, we will specify the max buffer for the streams and its historical associated for the reliable streams.

.. code-block:: C

    #include "HelloWorldWriter.h"

    #define STREAM_HISTORY  8
    #define BUFFER_SIZE     UXR_CONFIG_UDP_TRANSPORT_MTU * STREAM_HISTORY

Before create a Session we need to indicate the transport to use (the *Agent* must be configured for listening from UDP at port 2018).

.. code-block:: C

    uxrUDPTransport transport;
    if (!uxr_init_udp_transport(&transport, UXR_IPv4, "127.0.0.1", "2018"))
    {
        printf("Error at create transport.\n");
        return 1;
    }

Next, we will create a session that allows us interacting with the *Agent*:

.. code-block:: C

    uxrSession session;
    uxr_init_session(&session, &transport.comm, 0xABCDABCD);
    uxr_set_topic_callback(&session, on_topic, NULL);
    if(!uxr_create_session(&session))
    {
        printf("Error at create session.\n");
        return 1;
    }

The first function ``uxr_init_session`` initializes the ``session`` structure with the transport and the `Client Key` (the session identifier for an *Agent*).
The ``uxr_set_topic_callback`` function is for registering the function ``on_topic`` which will be called when the `Client` receives a topic.
Once the session has been initialized, we can send the first message for logging the `Client` in the *Agent* side: ``uxr_create_session``.
This function will try to connect with the *Agent* by ``CONFIG_MAX_SESSION_CONNECTION_ATTEMPTS`` attempts (configurable as a CMake argument).

Optionally, we also could add a status callback with the function ``uxr_set_status_callback``, but for this example, we do not need it.

Once we have logged in the session successfully, we can create the streams that we will use.
In this case, we will use two, both reliables, for input and output.

.. code-block:: C

    uint8_t output_reliable_stream_buffer[BUFFER_SIZE];
    uxrStreamId reliable_out = uxr_create_output_reliable_stream(&session, output_reliable_stream_buffer, BUFFER_SIZE, STREAM_HISTORY);

    uint8_t input_reliable_stream_buffer[BUFFER_SIZE];
    uxrStreamId reliable_in = uxr_create_input_reliable_stream(&session, input_reliable_stream_buffer, BUFFER_SIZE, STREAM_HISTORY);

To publish and/or subscribes to a topic, we need to create a hierarchy of XRCE entities in the *Agent* side.
These entities will be created from the *Client*.

.. image:: images/entities_hierarchy.svg

.. _setup_participant:

Setup a Participant
^^^^^^^^^^^^^^^^^^^

For establishing DDS communication, we need to create a `Participant` entity for the `Client` in the *Agent*.
We can do this calling *Create participant* operation:

.. code-block:: C

    uxrObjectId participant_id = uxr_object_id(0x01, UXR_PARTICIPANT_ID);
    const char* participant_xml = "<dds>"
                                      "<participant>"
                                          "<rtps>"
                                              "<name>default_xrce_participant</name>"
                                          "</rtps>"
                                      "</participant>"
                                  "</dds>";
    uint16_t participant_req = uxr_buffer_create_participant_ref(&session, reliable_out, participant_id, participant_xml, UXR_REPLACE);

In any `XRCE Operation` that creates an entity, an `Object ID` is necessary.
It is used to represent and manage the entity in the *Client* side.
In this case, we will create the entity by its XML description, but also could be done by a reference of the entity in the *Agent*.
Each operation returns a `Request ID`.
This identifier of the operation can be used later for associating the status with the operation.
In this case, the operation has been written into the stream ``reliable_out``.
Later, in the ``run_session`` function, the data written in the stream will be sent to the *Agent*.

.. _create_topics:

Create  topics
^^^^^^^^^^^^^^

Once the `Participant` has been created, we can use `Create topic` operation to register a `Topic` entity within the `Participant`.

.. code-block:: C

    uxrObjectId topic_id = uxr_object_id(0x01, UXR_TOPIC_ID);
    const char* topic_xml = "<dds>"
                                "<topic>"
                                    "<name>HelloWorldTopic</name>"
                                    "<dataType>HelloWorld</dataType>"
                                "</topic>"
                            "</dds>";
    uint16_t topic_req = uxr_buffer_create_topic_xml(&session, reliable_out, topic_id, participant_id, topic_xml, UXR_REPLACE);

As any other XRCE Operation used to create an entity, an Object ID must be specified to represent the entity.
The ``participant_id`` is the participant where the Topic will be registered.
To determine which topic will be used, an XML is sent to the *Agent* for creating and defining the Topic in the DDS Global Data Space.
That definition consists of a name and a type.

.. _pubs_and_subs:

Publishers & Subscribers
^^^^^^^^^^^^^^^^^^^^^^^^

Similar to Topic registration, we can create `Publishers` and `Subscribers` entities.
We create a publisher or subscriber on a participant entity, so it is necessary to provide the ID of the `Participant` which will hold those `Publishers` or `Subscribers`.

.. code-block:: C

    uxrObjectId publisher_id = uxr_object_id(0x01, UXR_PUBLISHER_ID);
    const char* publisher_xml = "";
    uint16_t publisher_req = uxr_buffer_create_publisher_xml(&session, reliable_out, publisher_id, participant_id, publisher_xml, UXR_REPLACE);

    uxrObjectId subscriber_id = uxr_object_id(0x01, UXR_SUBSCRIBER_ID);
    const char* subscriber_xml = "";
    uint16_t subscriber_req = uxr_buffer_create_subscriber_xml(&session, reliable_out, subscriber_id, participant_id, subscriber_xml, UXR_REPLACE);

The `Publisher` and `Subscriber` XML information is given when the `DataWriter` and `DataReader` are created.

.. _dws_and_drs:

DataWriters & DataReaders
^^^^^^^^^^^^^^^^^^^^^^^^^

Analogously to publishers and subscribers entities, we create the `DataWriters` and `DataReaders` entities.
These entities are in charge of sending and receiving the data.
`DataWriters` are referred to as publishers, and `DataReaders` are referred to as subscribers.
The configuration of these `DataReaders` and `DataWriters` are contained in the XML.

.. code-block:: C

    uxrObjectId datawriter_id = uxr_object_id(0x01, UXR_DATAWRITER_ID);
    const char* datawriter_xml = "<dds>"
                                     "<data_writer>"
                                         "<topic>"
                                             "<kind>NO_KEY</kind>"
                                             "<name>HelloWorldTopic</name>"
                                             "<dataType>HelloWorld</dataType>"
                                         "</topic>"
                                     "</data_writer>"
                                 "</dds>";
    uint16_t datawriter_req = uxr_buffer_create_datawriter_xml(&session, reliable_out, datawriter_id, publisher_id, datawriter_xml, UXR_REPLACE);

    uxrObjectId datareader_id = uxr_object_id(0x01, UXR_DATAREADER_ID);
    const char* datareader_xml = "<dds>"
                                     "<data_reader>"
                                         "<topic>"
                                             "<kind>NO_KEY</kind>"
                                             "<name>HelloWorldTopic</name>"
                                             "<dataType>HelloWorld</dataType>"
                                         "</topic>"
                                     "</data_reader>"
                                 "</dds>";
    uint16_t datareader_req = uxr_buffer_create_datareader_xml(&session, reliable_out, datareader_id, subscriber_id, datareader_xml, UXR_REPLACE);

.. _reqs_and_reps:

Requester & Replier
^^^^^^^^^^^^^^^^^^^

There is another pair of coupled entities, the Requester and the Replier.
These entities provide request-reply functionality using the underlining publish-subscribe pattern.
It is achieved through a mirror configuration between a Requester and a Replier, that is,
both entities contain a `Publisher` and a `Subscriber`,
the `Publisher` of the `Requester` and the `Subscriber` of the `Replier` are associated with the same `Topic` and vice versa.
In that way, each time a `Requester` publishes a request it will be received by the `Replier`,
then the latter will generate a reply and publish it, and finally, this reply will be received by the `Requester`.

The following code shows how to create a `Requester` and a `Replier` using the XML representation.

.. code-block:: C

    uxrObjectId requester_id = uxr_object_id(0x01, UXR_REQUESTER_ID);
    const char* requester_xml = "<dds>"
                                    "<requester profile_name=\"my_requester\""
                                               "service_name=\"service_name\""
                                               "request_type=\"request_type\""
                                               "reply_type=\"reply_type\">"
                                    "</requester>"
                                "</dds>";
    uint16_t requester_req = uxr_buffer_create_requester_xml(&session, reliable_out, requester_id, participant_id, requester_xml, UXR_REPLACE);

    replier_id = uxr_object_id(0x01, UXR_REPLIER_ID);
    const char* replier_xml = "<dds>"
                                  "<replier profile_name=\"my_replier\""
                                           "service_name=\"service_name\""
                                           "request_type=\"request_type\""
                                           "reply_type=\"reply_type\">"
                                  "</replier>"
                             "</dds>";
    uint16_t replier_req = uxr_buffer_create_replier_xml(&session, reliable_out, replier_id, participant_id, replier_xml, UXR_REPLACE);

.. _agent_response:

Agent response
^^^^^^^^^^^^^^

In operations such as create a session, create entity or request data from the *Agent*,
a status is sent from the *Agent* to the *Client* indicating what happened.

For `Create session` or `Delete session` operations, the status value is stored into the ``session.info.last_request_status``.
For the rest of the operations, the statuses are sent to the input reliable stream ``0x80``, that is,
the first input reliable stream created, with index 0.

The different status values that the *Agent* can send to the *Client* are the following (defined in ``uxr/client/core/session/session_info.h``):

.. code-block:: C

    UXR_STATUS_OK
    UXR_STATUS_OK_MATCHED
    UXR_STATUS_ERR_DDS_ERROR
    UXR_STATUS_ERR_MISMATCH
    UXR_STATUS_ERR_ALREADY_EXISTS
    UXR_STATUS_ERR_DENIED
    UXR_STATUS_ERR_UNKNOWN_REFERENCE
    UXR_STATUS_ERR_INVALID_DATA
    UXR_STATUS_ERR_INCOMPATIBLE
    UXR_STATUS_ERR_RESOURCES
    UXR_STATUS_NONE (never send, only used when the status is known)

The status can be handled by the ``on_status_callback`` callback (configured in ``uxr_set_status_callback`` function) or by the ``run_session_until_all_status`` as we will see.

.. code-block:: C

    uint8_t status[6]; // we have 6 request to check.
    uint16_t requests[6] = {participant_req, topic_req, publisher_req, subscriber_req, datawriter_req, datareader_req};
    if(!uxr_run_session_until_all_status(&session, 1000, requests, status, 6))
    {
        printf("Error at create entities\n");
        return 1;
    }

The ``run_session`` functions are the main functions of the *eProsima Micro XRCE-DDS Client* library.
They perform several tasks: send the stream data to the *Agent*, listen to data from the *Agent*, call callbacks, and manage the reliable connection.
There are five variations of ``run_session`` function:
- ``uxr_run_session_time``
- ``uxr_run_session_until_timeout``
- ``uxr_run_session_until_confirmed_delivery``
- ``uxr_run_session_until_all_status``
- ``uxr_run_session_until_one_status``

Here we use the ``uxr_run_session_until_all_status`` variation that will perform these actions until all statuses have been confirmed or the timeout has been reached.
This function will return ``true`` in case all statuses were `OK`.
After calling this function, the status can be read from the ``status`` array previously declared.

.. _write_data:

Write Data
^^^^^^^^^^

Once we have created a valid data writer entity, we can write data into the DDS Global Data Space using the writing operation.
For creating a message with data, first, we must decide which stream we want to use, and write that topic in this stream.

.. code-block:: C

    HelloWorld topic = {count++, "Hello DDS world!"};

    ucdrBuffer ub;
    uint32_t topic_size = HelloWorld_size_of_topic(&topic, 0);
    (void) uxr_prepare_output_stream(&session, reliable_out, datawriter_id, &ub, topic_size);
    (void) HelloWorld_serialize_topic(&ub, &topic);

    uxr_run_session_until_confirmed_delivery(&session, 1000);

``HelloWorld_size_of_topic`` and ``HelloWorld_serialize_topic`` functions are automatically generated by :ref:`microxrceddsgen_label` from the IDL.
The function ``uxr_prepare_output_stream`` requests a writing for a topic of ``topic_size`` size into the reliable stream represented by ``reliable_out``,
with a ``datawriter_id`` (correspond to the data writer entity used for sending the data in the `DDS World`).
If the stream is available and the topic fits in it, the function will initialize the ``ucdrBuffer`` structure ``ub``.
Once the ``ucdrBuffer`` is prepared, the topic can be serialized into it.
We are careless about ``uxr_prepare_output_stream`` return value because the serialization only will occur if the ``ucdrBuffer`` is valid.

After calling the writing function, the topic has been serialized into the buffer, but it has not been sent yet.
To send the topic, it is necessary to call a ``run_session`` function.
In this case, the function ``uxr_run_session_until_confirmed_delivery`` is called, which will wait until the message was confirmed or until the timeout has been reached.

.. _read_data:

Read Data
^^^^^^^^^

Once we have created a valid `DataReader` entity, we can read data from the DDS Global Data Space using the read operation.
This operation configures how the *Agent* will send the data to the *Client*.
The current implementation sends unlimited topics to the *Client*.

.. code-block:: C

    uxrDeliveryControl delivery_control = {0};
    delivery_control.max_samples = UXR_MAX_SAMPLES_UNLIMITED;

    uint16_t read_data_req = uxr_buffer_request_data(&session, reliable_out, datareader_id, reliable_in, &delivery_control);

To configure how the *Agent* will send the topic, we must set the input stream. In this case, we use the input reliable stream previously defined.
``datareader_id`` corresponds with the `DataDeader` entity used for receiving the data.
The ``delivery_control`` parameter is optional, and allows specifying how the data will be delivered to the *Client*.
For the example purpose, we set it as `unlimited`, so any number HelloWorld topic will be delivered to the *Client*.

The ``run_session`` function will call the topic callback each time a topic will be received from the *Agent*.

.. code-block:: C

    void on_topic(uxrSession* session, uxrObjectId object_id, uint16_t request_id, uxrStreamId stream_id, struct ucdrBuffer* ub, uint16_t length, void* args)
    {
        (void) session; (void) object_id; (void) request_id; (void) stream_id; (void) length; (void) args;

        HelloWorld topic;
        HelloWorld_deserialize_topic(ub, &topic);
    }

To know which kind of Topic has been received, we can use the ``object_id`` parameter or the ``request_id``.
The ``id`` of the ``object_id`` corresponds to the `DataReader` that has read the Topic, so it can be useful to discretize among different topics.
The ``args`` argument corresponds to user-free-data, that has been given at `uxr_set_status_callback` function.

.. _close_client:

Close the Client
^^^^^^^^^^^^^^^^

To close a `Client`, we must perform two steps.
First, we need to tell the *Agent* that the session is no longer available.
This is done sending the next message:

.. code-block:: C

    uxr_delete_session(&session);

After this, we can close the transport used by the session.

.. code-block:: C

    uxr_close_udp_transport(&transport);

.. _deployment_example:

Deployment example
^^^^^^^^^^^^^^^^^^

This section is devoted to illustrate how to deploy a system using *eProsima Micro XRCE-DDS* in a real environment.
An example of this can be found in the :code:`examples/Deployment` folder.

The tutorials above are based on *all in one* examples, that is, examples that create entities, publish or subscribe and then delete the resources.
One possible real purpose of this consists in differentiating the logic of `creating entities` and the actions of `publishing and subscribing`.
It can be done by creating two differents *Clients*, one in charge of configuring the entities in the *Agent*, which runs once,
only for creating the entities at **compile-time**, and other/s that log(s) in the same session as the first *Client*
(sharing the entities) and only publish(es) or subscrib(es) to data.

This allows creating *Clients* in a real scenario with the only purpose of sending and receiving data.
The concept of `profiles` allows building the *Client* library only with the chosen behavior
(only to publish or to subscribe, for example).
See :ref:`micro_xrce_dds_client_label` for more information.

The diagram below shows an example of how to configure the environment using a `configurator client`.

Initial state
-------------

    .. image:: images/deployment_0.svg
        :width: 600 px
        :align: center

The environment contains two *Agents* (it's perfectly possible to use only one *Agent* too), and two *Clients*,
one for publishing and another for subscribing.


Publisher configuration
-----------------------

    .. image:: images/deployment_1.svg
        :width: 600 px
        :align: center

In this state a `configurator client` is connected to the *Agent* `A` with the `client key` that will be used by the future `publisher client` (0xAABBCCDD).
Once a session is logged in, the `configurator client` creates all the necessary entities for the `publisher client`.
This implies the creation of `participant`, `topic`, `publisher`, and `datawriter` entities.
These entities have a representation as DDS entities, and can be reached from the DDS world, that is,
any `subscriber DDS entity` could already be listening to topics if it matches with such `publisher DDS entity` through the `DDS` world.

Publisher
---------
    .. image:: images/deployment_2.svg
        :width: 600 px
        :align: center

Then, the `publisher client` is connected to the *Agent* `A`.
This *Client* logs in a session with its *Client* key (0xAABBCCDD).

At that moment, it can use all entities created related to this `client key`.
Because all entities that it uses were successfully created by the `configurator client`, the `publisher client` can immediately publish to `DDS`.

Subscriber configuration
------------------------

    .. image:: images/deployment_3.svg
        :width: 600 px
        :align: center

Again, the `configurator client` connects and logs in, this time to *Agent* `B`, now with the subscriber's key (0x11223344).
In this case, the entities that the `configurator client` creates are a `participant`, a `topic`, a `subscriber`, and a `datareader`.
The entities created by the `configuraton client` will be available until the session is deleted.

Subscriber
----------

    .. image:: images/deployment_4.svg
        :width: 600 px
        :align: center

Once the subscriber is configured, the `subscriber client` logs in the *Agent* `B`.
Since all of its entities have been created previously, it only needs to configure the read after the login.
Once the data request message has been sent, the subscriber will receive the topics from the publisher through the `DDS` world.
