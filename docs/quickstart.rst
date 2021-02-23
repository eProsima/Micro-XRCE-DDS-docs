.. _quickstart_label:

Quick start
===========


*eProsima Micro XRCE-DDS* provides a C API which allows the creation of *eProsima Micro XRCE-DDS Clients* that can either
publish/subscribe to topics from the DDS Global Data Space, or act as client-service applications following
a request/reply pattern.

In this section, we will guide the user through the deployment of two out-of-the-box examples.
In the first one, an *eProsima Micro XRCE-DDS Agent*
bridges two *eProsima Micro XRCE-DDS Clients* publishing and subscribing to the DDS world.
The second example shows instead the deployment of a client-service application using the
Requester and Replier entities, also put into communication via an *eProsima Micro XRCE-DDS Agent*.

The section is organized as follows:

- :ref:`run_agent`
- :ref:`run_pubsub_example`
- :ref:`run_reqrep_example`

.. _run_agent:

Running an Agent
----------------

First of all, install the *Agent* as explained in the :ref:`install_agent` section.
On Linux, this would be: ::

    $ git clone https://github.com/eProsima/Micro-XRCE-DDS-Agent.git
    $ cd Micro-XRCE-DDS-Agent
    $ mkdir build && cd build
    $ cmake ..
    $ make
    $ sudo make install

After having installed the *Agent* system-wide, you can launch it.

For this example, the *Client*-*Agent* communication will be done through UDP, using the port :code:`2019`
and with the XML creation mode, which is the default mode for creating entities: ::

    $ cd /usr/local/bin && MicroXRCEAgent udp -p 2019


.. _run_pubsub_example:

Running a Publisher-Subscriber example
--------------------------------------

In this section, we guide the user through the configuration and deployment of
a simple publish/subscribe example where the communication is mediated by the *Agent* created above.

Before considering the publisher and subscriber examples, it is useful to briefly summarize how the
`Publisher` and `Subscriber` entities work, as well as to list the functions related to both entities.

Publisher
    The `Publisher` will be associated with a `Topic` and will handle a DDS publisher that publish topics.

    To create a `Publisher` entity, the ``uxr_buffer_create_publisher_xml`` or ``uxr_buffer_create_publisher_ref`` shall be used.
    Once created, topics can be published through ``uxr_prepare_output_stream``.

Subscriber
    The `Subscriber` will be associated with a `Topic` and will handle a DDS subscriber that receives topics.

    To create a `Subscriber` entity, the ``uxr_buffer_create_subscriber_xml`` or ``uxr_buffer_create_subscriber_ref`` shall be used.
    Topics can be received by sending a data request to the *Agent* with ``uxr_buffer_request_data``, 
    and through the `on_topic` callback which shall be set by the ``uxr_set_topic_callback``.
    This callback has a parameter `request_id` which identifies the data request.

All the files and the code used in this example can be found in the
`Micro-XRCE-DDS-Client/examples/PublishHelloWorld <https://github.com/eProsima/Micro-XRCE-DDS-Client/tree/master/examples/PublishHelloWorld>`_
and
`Micro-XRCE-DDS-Client/examples/SubscribeHelloWorld <https://github.com/eProsima/Micro-XRCE-DDS-Client/tree/master/examples/SubscribeHelloWorld>`_
folders.

Publisher application
^^^^^^^^^^^^^^^^^^^^^

Let's now install the *Client* locally, and with the :code:`-DUCLIENT_BUILD_EXAMPLES=ON` flag enabled, so as
to activate the compilation of the examples. On Linux, this implies running the following: ::

    $ git clone https://github.com/eProsima/Micro-XRCE-DDS-Client.git
    $ cd Micro-XRCE-DDS-Client
    $ mkdir build && cd build
    $ cmake .. -DUCLIENT_BUILD_EXAMPLES=ON
    $ make

At this point, it's possible to launch the :code:`PublishHelloWorldClient` executable
located in the folder :code:`Micro-XRCE-DDS-Client/build/examples/PublishHelloWorld`, which'll make
the *Client* publish in the DDS World the :code:`HelloWorld` topic
(take a look at the IDL defining this topic in the file
:code:`Micro-XRCE-DDS-Client/examples/PublishHelloWorld/HelloWorld.idl`). ::

    $ Micro-XRCE-DDS-Client/build/examples/PublishHelloWorld/PublishHelloWorldClient 127.0.0.1 2019

.. TODO: decide which path to give to the user.

The source code of the :code:`PublishHelloWorldClient` can be found in
:code:`Micro-XRCE-DDS-Client/examples/PublishHelloWorld/main.c`.

Subscriber application
^^^^^^^^^^^^^^^^^^^^^^

After having executed the publisher app, we can launch the :code:`SubscribeHelloWorldClient` executable,
which is located in the folder :code:`Micro-XRCE-DDS-Client/build/examples/SubscribeHelloWorld`, which'll make
this *Client* subscribe to the same :code:`HelloWorld` topic from the DDS World. ::

    $ Micro-XRCE-DDS-Client/build/examples/SubscriberHelloWorld/SubscribeHelloWorldClient 127.0.0.1 2019

.. TODO: decide which path to give to the user.

The source code of the :code:`SubscribeHelloWorldClient` can be found in
:code:`Micro-XRCE-DDS-Client/examples/SubscribeHelloWorld/main.c`.

At this point, the subscriber will receive the topics that are being sent by the publisher.

In order to see the messages from the DDS Global Data Space point of view, you can use the *eProsima Fast DDS* HelloWorld example
running a subscriber. Find more information on how to do so at
`Fast DDS HelloWorld <https://fast-dds.docs.eprosima.com/en/latest/fastdds/getting_started/simple_app/simple_app.html#writing-a-simple-publisher-and-subscriber-application>`_.

.. _run_reqrep_example:

Running a Requester/Replier example
-----------------------------------

This section shows an example of a client-service application using the `Requester` and `Replier` entities.
This application has two ends, the client (*RequestAdder*) and the service (*ReplyAdder*).
On the one hand, the client is in charge of sending requests which contain two integers, as well as receiving
the responses from the service.
On the other hand, the service is in charge of receiving the requests from the client,
summing the two integers, and finally of sending the response to the client.

Before considering the client and service examples, it is useful to briefly summarize how the
`Requester` and `Replier` entities work, as well as to list the functions related to both entities.

Requester
    The `Requester` entity is composed of a `Publisher` and a `Subscriber` associated with a `RequestTopic` and a `ReplyTopic` respectively.
    The `Publisher` is in charge of sending the request, while the `Susbscriber` receives the replies.

    To create a `Requester` entity, the ``uxr_buffer_create_requester_xml`` or ``uxr_buffer_create_requester_ref`` shall be used.
    Once created, requests can be sent through ``uxr_buffer_request``.
    Replies can be received by sending a data request to the *Agent* with ``uxr_buffer_request_data``,
    and through the `on_reply` callback which shall be set by the ``uxr_set_reply_callback``.
    This callback has a parameter :code:`reply_id` which corresponds to the identifier returned by the ``uxr_buffer_request`` call.

Replier
    The `Reply` entity is a mirror of the `Requester`, that is, it contains a `Publisher` and a `Subscriber` as well,
    but the topic association is reversed, 
    as the `Publisher` is associated with the `ReplyTopic` and the `Subscriber` to the `RequestTopic`.
    In this case, the `Subscriber` is in charge of receiving the request from the `Requester`, while the `Publisher` sends the replies.

    To create a `Replier` entity, the ``uxr_buffer_create_replier_xml`` or ``uxr_buffer_create_replier_ref`` shall be used.
    Once created, replies can be sent through ``uxr_buffer_reply``.
    Requests can be received by sending a data request to the *Agent* with ``uxr_buffer_request_data``, 
    and through the `on_request` callback which shall be set by the ``uxr_set_request_callback``.
    This callback has a parameter `sample_id` which identifies the request and should be used in the ``uxr_buffer_reply``.

All the files and the code used in this example can be found in the
`Micro-XRCE-DDS-Client/examples/RequestAdder <https://github.com/eProsima/Micro-XRCE-DDS-Client/tree/master/examples/RequestAdder>`_
and
`Micro-XRCE-DDS-Client/examples/ReplyAdder <https://github.com/eProsima/Micro-XRCE-DDS-Client/tree/master/examples/ReplyAdder>`_
folders.

Requester application
^^^^^^^^^^^^^^^^^^^^^

Let's now install the *Client* locally, and with the :code:`-DUCLIENT_BUILD_EXAMPLES=ON` flag enabled, so as
to activate the compilation of the examples. On Linux, this implies running the following: ::

    $ git clone https://github.com/eProsima/Micro-XRCE-DDS-Client.git
    $ cd Micro-XRCE-DDS-Client
    $ mkdir build && cd build
    $ cmake .. -DUCLIENT_BUILD_EXAMPLES=ON
    $ make

At this point, it's possible to launch the :code:`RequestAdder` executable
located in the folder :code:`Micro-XRCE-DDS-Client/build/examples/RequestAdder`, which'll make
the *Client* send two integers as a request, and receive the sum of both integers as a response. ::

    $ Micro-XRCE-DDS-Client/build/examples/RequestAdder/RequestAdder 127.0.0.1 2019

.. TODO: decide which path to give to the user.
.. TODO: check the command here.

The source code of the :code:`RequestAdder` can be found in
:code:`Micro-XRCE-DDS-Client/examples/RequestAdder/main.c`.

Replier application
^^^^^^^^^^^^^^^^^^^

After having executed the Requester app, we can launch the :code:`ReplyAdder` executable,
which is located in the folder :code:`Micro-XRCE-DDS-Client/build/examples/ReplyAdder`, which'll make
this *Client* receive requests composed by two integers, sum both numbers, and finally send the response. ::

    $ Micro-XRCE-DDS-Client/build/examples/ReplyAdder/ReplyAdder 127.0.0.1 2019

.. TODO: decide which path to give to the user.
.. TODO: check the command here.

The source code of the :code:`ReplyAdder` can be found in
:code:`Micro-XRCE-DDS-Client/examples/ReplyAdder/main.c`.

At this point, the Requester and the Replier will start communicating.

Learn More
----------

Find a detailed explanation of the code used to write and run these applications in the
:ref:`getting_started_label` section.

Find other relevant material:

- *eProsima Fast DDS*: `eProsima Fast DDS <https://fast-dds.docs.eprosima.com/en/latest/>`_
- To learn how to install *eProsima Micro XRCE-DDS* read: :ref:`installation_label`
- To learn more about *eProsima Micro XRCE-DDS* read: :ref:`user`
- To learn more about *eProsima Micro XRCE-DDS Gen* read: :ref:`microxrceddsgen_label`
