.. _transport_label:

Transport
=========
This section shows how the transport layer is implemented in both *eProsima Micro XRCE-DDS Agent* and *eProsima Micro-DDS Client*.
Furthermore, this section describes how to add your own custom transport in *eProsima Micro XRCE-DDS*.

Introduction
------------
In contract to other IoT middleware such as MQTT and CoaP which work over a particular transport protocol, the XRCE protocol is design to support multiple transport protocol natively.
This feature of XRCE is enhanced by *eProsima Micro XRCE-DDS* in two ways.
On the one hand, both *Agent* and *Client* have the logic of the protocol complete separate from the transport protocol through a set of interfaces, which will be explained in the following sections.
On the other hand, taking advantage of the transport interface flexibility, one custom Serial Transport has been implemented in order to provide support to serial communication.

Agent Transport Architecture
----------------------------
The above figure shows the *Agent* transport architecture.
There is an interface ``Server`` which each transport protocol shall implement.
For each kind of transport protocol, there is a class which is in charge of managing the sessions with the *Client*.
It class implement the following virtual function from the ``Server`` interface::

    void on_create_client(EndPoint* source, const dds::xrce::CLIENT_Representation& representation);
    void on_delete_client(EndPoint* source);

These functions are call each time a session is established or remove with a *Client*.
As an example of how these functions behave, they update set of ``std::map`` which relate the source address of the *Client* with its ``ClientKey``.

For each kind of platform (Windows, Linux, MacOS, etc.), there is a class which implements the following functions of the ``Server`` interface: ::

    bool init();
    bool close();
    bool recv_message(InputPacket& input_packet, int timeout);
    bool send_message(OutputPacket output_packet);
    int get_error();

.. image:: images/agent_transport_architecture.svg

How to add support for a new platform?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Take into account the aforementioned, the adaptation of an already implemented transport protocol to a new platform is straightforward, only the five functions described above shall be implemented.

How to add a new transport?
^^^^^^^^^^^^^^^^^^^^^^^^^^^
On the other hand, for adding a new kind of transport protocol both set of functions shall be implemented, the session management functions and the transport functions.

Client Transport Architecture
-----------------------------
The above figure shows the *Client* transport architecture.
In these case, there is also an interface, ``uxrCommunication``, which functions are call from the session layer.
That is, each time the session wants to send or receive a message form the *Agent* it is going to call to these functions ::

    bool send_msg_func(void* instance, const uint8_t* buf, size_t len);
    bool recv_msg_func(void* instance, uint8_t** buf, size_t* len, int timeout);
    uint8_t comm_error_func(void);

The transport layer interface, ``uxrUDPTransport``, is in charge of two main tasks: 

1. Override the communication interface functions. 
   For example, in the case of the UDP protocol these functions are the followings: ::
   
    bool send_udp_msg(void* instance, const uint8_t* buf, size_t len);
    bool recv_udp_msg(void* instance, uint8_t** buf, size_t* len, int timeout);
    uint8_t get_udp_error(void);

2. Offer to the user the initialization and close function related with the transport protocol.
   For example, in the case of the UDP protocol these functions are the followings: ::

    bool uxr_init_udp_transport(uxrUDPTransport* transport, uxrUDPPlatform* platform, const char* ip, uint8_t port);
    bool uxr_close_udp_transport(uxrUDPTransport* transport);

For each platform there is an implementation of the virtual functions defined in the transport layer interface.
For example, in the case of Linux under UDP transport protocol, the ``uxrUDPPlatform`` implement the following functions: ::

    bool uxr_init_udp_platform(uxrUDPPlatform* platform, const char* ip, uint16_t port);
    bool uxr_close_udp_platform(uxrUDPPlatform* platform);
    size_t uxr_write_udp_data_platform(uxrUDPPlatform* platform, const uint8_t* buf, size_t len, uint8_t* errcode);
    size_t uxr_read_udp_data_platform(uxrUDPPlatform* platform, uint8_t* buf, size_t len, int timeout, uint8_t* errcode);

.. image:: images/client_transport_architecture.svg

Custom Transport
^^^^^^^^^^^^^^^^

Taking into account the aforementioned, the adaptation of an already implemented transport protocol for a new platform is straightforward. 
It is enough to implement the virtual function of the transport layer.
For example, in the case of UDP there are: ::

    bool uxr_init_udp_platform(uxrUDPPlatform* platform, const char* ip, uint16_t port);
    bool uxr_close_udp_platform(uxrUDPPlatform* platform);
    size_t uxr_write_udp_data_platform(uxrUDPPlatform* platform, const uint8_t* buf, size_t len, uint8_t* errcode);
    size_t uxr_read_udp_data_platform(uxrUDPPlatform* platform, uint8_t* buf, size_t len, int timeout, uint8_t* errcode);
    

Custom Serial Transport
-----------------------
*eProsima Micro XRCE-DDS* has a **Custom Serial Transport** with the following features:

* **HDLC Framing**: each serial framing begins with a ``begin_frame`` octet ``(0x7E)`` and the rest of the frame is byte stuffing using the ``space`` octet ``(0x7D)`` following by the original octet exclusive-or with ``0x20``.
  For example, if the frame contains the octet `0x7E` it is encoded as `0x7D, 0x5E`; and the same for the octet `0x7E` which is encoded as `0x7D, 0x5D`.
* **CRC Calculation**: serial frames end with a CRC-16 for detecting frame corruption. The CRC-16 is computed using the polinomy ``x^16 + x^12 + x^5 + 1`` after the frame stuffing for each octet of the frame and including the ``begin_frame``.
* **Routing header**: the Serial Transport provides ``source`` and ``remote`` address in the framing, which could be used for implement a routing protocol.

All the aforementioned features are addressed using the following frame format: ::

    0        8        16       24                40                 X                X+16
    +--------+--------+--------+--------+--------+--------//--------+--------+--------+
    |  FLAG  |  SADD  |  RADD  |       LEN       |      PAYLOAD     |       CRC       |
    +--------+--------+--------+--------+--------+--------//--------+--------+--------+

* ``FLAG``: is a ``begin_frame`` octet for frame initialization.
* ``SADD``: is the address of the device which sent the message, that is, the ``source`` address.
* ``RADD``: is the address of the device which should receive the message, that is, the ``remote`` address.
* ``LEN``: is the length of the **payload without framing**. It is encoded as a 2-bytes array in little endian.
* ``PAYLOAD``: is the payload of the message.
* ``CRC``: is the CRC of the message **after the stuffing**.

Data Sending
^^^^^^^^^^^^
The figure below shows the workflow of the data sending.
This workflow could be divided in the following steps:

    1. A publisher application calls the *Client* library to send a given topic.
    2. The *Client* library serializes the topic inside a XRCE message using *Micro CDR*.
       As result, the XRCE message with the topic is stored in a **Output Stream Buffer**.
    3. The *Client* library calls the Serial Transport in order to send the serialized message.
    4. The Serial Transport frames the message, that is, add the header, payload and CRC of the frame taking into account the stuffing.
       This step takes place in an auxiliary buffer called **Framing Buffer**.
    5. Each time the Framing Buffer is full, the data is flushed into the **Device Buffer** calling the writing system function.

.. image:: images/serial_transport_sending.svg

This approach has some advantages which should be pointed out:

    1. The HDLD framing and the CRC control provides **integrity** and **security** to the Serial Transport.
    2. The framing technique allows to **reducing memory usage**.
       This is because the Framing Buffer size (42 bytes) bounds the Device Buffer size.
    3. The framing technique also allows sending **large data** over serial.
       This is because the message size is not bounded by the Device Buffer size, since the message is fragmented and stuffing during the framing stage.

Data Receiving
^^^^^^^^^^^^^^
The workflow of the data receiving is analogous to the data sending workflow:

    1. A subscriber application call the *Client* library to receive a given topic.
    2. The *Client* library calls the Serial Transport in order to receive the serial message.
    3. The Serial Transport reads data from the **Device Buffer** and unframes the raw data received from the Device Buffer in the **Unframing Buffer**.
    4. Once the Unframing Buffer is full, the Serial Transport append the fragment into the **Input Stream Buffer**.
       This operation is repeated until a complete message is received.
    5. The *Client* library deserialized the topic from the Input Stream Buffer to the user topic struct.

.. image:: images/serial_transport_receiving.svg

It should point out that this approach has the same advantages that the sending one.

