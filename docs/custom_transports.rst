.. _custom_transports_label:

TODO: Use this label in other parts of the documentation

XRCE Custom transports
======================

The Custom transport functionality in Micro XRCE-DDS allows users to implement the lowest communication 
layer and provide it easily and at runtime to both *Client* and *Agent* libraries.

This way, Micro XRCE-DDS wire protocol can be transmitted over virtually any protocol, network or communication
mechanism. In order to do so, two general communication modes are provided:

* Stream-oriented mode: the communication mechanism implemented does not have the concept of packet. 
  HDLC framing (:ref:`_stream_framing_label`) will be used.
* Packet-oriented mode: the communication mechanism implemented is able to send a whole packet that include a XRCE message.

These two modes can be selected by means of activating and deactivating parameter :code:`framing` in both the Client and Agent functions.

Micro XRCE-DDS Client
^^^^^^^^^^^^^^^^^^^^^

In order to enable the Micro XRCE-DDS Client profile for custom transports the CMake argument 
``UCLIENT_PROFILE_CUSTOM_TRANSPORT=<bool>`` must be set to true. By doing so, the user will enable the functionality for setting
the transport related callbacks explained :ref:`_custom_transports_client_api_label`.

An example on how to set these external transport callbacks in the Micro XRCE-DDS Client API is:

.. code-block:: c

    uxrCustomTransport transport;
    uxr_set_custom_transport_callbacks(
        &transport,
        true, // Framing enabled here. Using Stream-oriented mode.
        my_custom_transport_open,
        my_custom_transport_close,
        my_custom_transport_write,
        my_custom_transport_read);

    struct custom_args {
        ...
    }
    
    struct custom_args args;

    if(!uxr_init_custom_transport(&transport, (void *) &args))
    {
        printf("Error at create transport.\n");
        return 1;
    }

It important to notice that in :code:`uxr_init_custom_transport` a pointer to custom arguments is set. This reference will be copied to
the :code:`uxrCustomTransport` and will be available along all callbacks calls.

In general, four functions should be implemented. The behavior of these functions sightly differs depending on the selected mode:

.. code-block:: c

    bool my_custom_transport_open(uxrCustomTransport* transport)
    {
        ...
    }

This function should open and init the custom transport.
:code:`transport->args` have the arguments passed through :code:`uxr_init_custom_transport`
This function returns a boolean indicating if opening is successful.

.. code-block:: c

    bool my_custom_transport_close(uxrCustomTransport* transport)
    {
        ...
    }

This function should close the custom transport.
:code:`transport->args` have the arguments passed through :code:`uxr_init_custom_transport`.
This function returns a boolean indicating if closing is successful.

.. code-block:: c

    size_t my_custom_transport_write(
            uxrCustomTransport* transport,
            const uint8_t* buffer,
            size_t length,
            uint8_t* errcode)
    {
        ...
    }

This function should write data to the custom transport.
:code:`transport->args` have the arguments passed through :code:`uxr_init_custom_transport`.

**Stream-oriented mode:**
The function can send up to :code:`length` Bytes from :code:`buffer`

**Packet-oriented mode:**
The function should send :code:`length` Bytes from :code:`buffer`
If less than :code:`length` Bytes are written :code:`errcode` can be set.

This function returns the number of Bytes written.

.. code-block:: c

    size_t my_custom_transport_read(
            uxrCustomTransport* transport,
            uint8_t* buffer,
            size_t length,
            int timeout,
            uint8_t* errcode)
    {
        ...
    }

This function should read data to the custom transport
:code:`transport->args` have the arguments passed through :code:`uxr_init_custom_transport`

**Stream-oriented mode:**
The function should retrieve up to :code:`length` Bytes from transport
and write them into :code:`buffer` in :code:`timeout` milliseconds.

**Packet-oriented mode:**
The function should retrieve :code:`length` Bytes from transport
and write them into :code:`buffer` in :code:`timeout` milliseconds.
If less than :code:`length` Bytes are read :code:`errcode` can be set.

This function returns the number of Bytes read.

Micro XRCE-DDS Agent
^^^^^^^^^^^^^^^^^^^^^

TODO: Jose review this

The Micro XRCE-DDS Agent profile for custom transports is enabled by default. 

An example on how to set these external transport callbacks in the Micro XRCE-DDS Agent API is:

.. code-block:: cpp
    
    eprosima::uxr::Middleware::Kind mw_kind(eprosima::uxr::Middleware::Kind::FASTDDS);
    eprosima::uxr::CustomEndPoint custom_endpoint;

    // Add transport endpoing parameters
    custom_endpoint.add_member<uint32_t>("param1");
    custom_endpoint.add_member<uint16_t>("param2");
    custom_endpoint.add_member<std::string>("param3");

    eprosima::uxr::CustomAgent custom_agent(
        "my_custom_transport",
        &custom_endpoint,
        mw_kind,
        true, // Framing enabled here. Using Stream-oriented mode.
        my_custom_transport_open,
        my_custom_transport_close,
        my_custom_transport_write
        my_custom_transport_read);

    custom_agent.start();

:code:`custom_endpoint` will be the object in charge of handling endpoint parameters. The *Agent*, unlike the *Client*, can receive
messages from multiple *Clients* so it must be able to differentiate between different *Clients*. This :code:`eprosima::uxr::CustomEndPoint` should be filled in the read callback with information about the origin of the message and will be provided in the write callback with information about the destination of the message.

In general, members of an :code:`eprosima::uxr::CustomEndPoint` object can be unsigned integers and strings.

As in the *Client* API four functions should be implemented. The behavior of these functions sightly differs depending on the selected mode:

.. code-block:: c

    eprosima::uxr::CustomAgent::InitFunction my_custom_transport_open = [&]() -> bool
    {
        ...
    }

This function should open and init the custom transport
This function returns a boolean indicating if opening is successful

.. code-block:: c

    eprosima::uxr::CustomAgent::FiniFunction my_custom_transport_close = [&]() -> bool
    {
        ...
    }

This function should close the custom transport
This function returns a boolean indicating if closing is successful

.. code-block:: c

    eprosima::uxr::CustomAgent::SendMsgFunction my_custom_transport_write = [&](
        const eprosima::uxr::CustomEndPoint* destination_endpoint,
        uint8_t* buffer,
        size_t length,
        eprosima::uxr::TransportRc& transport_rc) -> ssize_t
    {
        ...
    }

This function should write data to the custom transport
This function must use destination_endpoint members to set the data destination

**Stream-oriented mode:**
The function can send up to :code:`length` Bytes from :code:`buffer`

**Packet-oriented mode:**
The function should send :code:`length` Bytes from :code:`buffer`
If less than :code:`length` Bytes are written transport_rc can be set.

This function returns the number of Bytes written.

.. code-block:: c

    eprosima::uxr::CustomAgent::RecvMsgFunction my_custom_transport_read = [&](
            eprosima::uxr::CustomEndPoint* source_endpoint,
            uint8_t* buffer,
            size_t length,
            int timeout,
            eprosima::uxr::TransportRc& transport_rc) -> ssize_t
    {
        ...
    }

This function should read data to the custom transport
This function must fill source_endpoint members with data source 

**Stream-oriented mode:**
The function should retrieve up to :code:`length` Bytes from transport
and write them into :code:`buffer` in :code:`timeout` milliseconds.

**Packet-oriented mode:**
The function should retrieve :code:`length` Bytes from transport
and write them into :code:`buffer` in :code:`timeout` milliseconds.
If less than :code:`length` Bytes are read transport_rc can be set.

This function returns the number of Bytes read.

























