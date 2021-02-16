.. _quickstart_label:

Quick start
===========

*eProsima Micro XRCE-DDS* provides a C API which allows the creation of *eProsima Micro XRCE-DDS Clients* that publish
and/or subscribe to topics from the DDS Global Data Space.

In this section, we will guide the user through the creation of a simple *eProsima Micro XRCE-DDS Client* and a simple
*eProsima Micro XRCE-DDS Agent* for publishing and subscribing to the DDS world.

All the files and the code used in this example can be found in the
`Micro-XRCE-DDS-Client/examples/PublishHelloWorld <https://github.com/eProsima/Micro-XRCE-DDS-Client/tree/master/examples/PublishHelloWorld`_
and
`Micro-XRCE-DDS-Client/examples/SubscribeHelloWorld <https://github.com/eProsima/Micro-XRCE-DDS-Client/tree/master/examples/SubscribeHelloWorld`_
folders.

Running an Agent
^^^^^^^^^^^^^^^^

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

.. TODO: write a comment tagging guys and asking if it was ok to remove the "-r <references-file>" part

Running a Publisher Client application
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Let's now install the *Client* locally, and with the :code:`-DUCLIENT_BUILD_EXAMPLES=ON` flag enable, so as
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

Running a Subscriber Client application
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

After having executed the publisher app, we can launch the :code:`SubscribeHelloWorldClient` excutable,
which is located in the folder :code:`Micro-XRCE-DDS-Client/build/examples/SubscribeHelloWorld`, which'll make
this *Client* subscribe to the same :code:`HelloWorld` topic from the DDS World. ::

    $ Micro-XRCE-DDS-Client/build/examples/SubscriberHelloWorld/SubscribeHelloWorldClient 127.0.0.1 2019

The source code of the :code:`SubscribeHelloWorldClient` can be found in
:code:`Micro-XRCE-DDS-Client/examples/SubscribeHelloWorld/main.c`.

At this point, the subscriber will receive the topics that are being sent by the publisher.

In order to see the messages from the DDS Global Data Space point of view, you can use the *eProsima Fast DDS* HelloWorld example
running a subscriber. Find more information on how to do so at
`Fast DDS HelloWorld <https://fast-dds.docs.eprosima.com/en/latest/fastdds/getting_started/simple_app/simple_app.html#writing-a-simple-publisher-and-subscriber-application>`_.

Learn More
^^^^^^^^^^

Find a detailed explanation of the code used to write and run these applications in the
:ref:<_getting_started_label> section.

To learn more about DDS and *eProsima Fast DDS*: `eProsima Fast DDS <https://fast-dds.docs.eprosima.com/en/latest/>`_

To learn how to install *eProsima Micro XRCE-DDS* read: :ref:`installation_label`

To learn more about *eProsima Micro XRCE-DDS* read: :ref:`user`

.. TODO: Decide what to do w/ this:
To learn more about *eProsima Micro XRCE-DDS Gen* read: :ref:`microxrceddsgen_label`
