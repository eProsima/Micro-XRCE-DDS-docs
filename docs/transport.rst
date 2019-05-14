.. _transport_label:

Transport
=========
This section shows how the transport layer is implemented in both *eProsima Micro XRCE-DDS Agent* and *eProsima Micro-DDS Client*.
Furthermore, this section describes how to add your own custom transport in *eProsima Micro XRCE-DDS*.

Introduction
------------
In contrast to other IoT middleware such as MQTT and CoaP which work over a particular transport protocol, the XRCE protocol is designed to support multiple transport protocols natively.
This feature of XRCE is enhanced by *eProsima Micro XRCE-DDS* in two ways.
On the one hand, both *Agent* and *Client* have the logic of the protocol complete separate from the transport protocol through a set of interfaces, which will be explained in the following sections.
On the other hand, taking advantage of the transport interface flexibility, one custom Serial Transport has been implemented in order to provide support to serial communication.

Agent Transport Architecture
----------------------------

The *Agent* transport architecture is composed by 3 different layers:

.. image:: images/agent_transport_stack.svg

* **Server Layer**: is an interface from which each transport specific server inherits.
  This interface implements 4 different threads: 

  * *Sender thread*: in charge of sending the messages to the *Clients*.
  * *Receiver thread*: in charge of receiving the messages from the *Clients*.
  * *Processing thread*: in charge of processing the messages received from the *Clients*. 
  * *Heartbeat thread* in charge of handling reliability with the *Clients*.

* **Transport Layer**: is a transport specific class which manages the sessions established between the *Agent* and the *Clients*. This class inherits from the *Server* interface.
* **Platform Layer**: is a platform specific class which implements the sending and receiving functions for a given transport in a given platform. It should be noted that it is the only class which has platform dependencies.

UDP Server Example
^^^^^^^^^^^^^^^^^^

As an example, this subsection describes how the UDP server is implemented in *eProsima Micro XRCE-DDS Agent*.
The figure below shows the *Agent* transport architecture for the UDP servers.

.. image:: images/agent_transport_architecture.svg

At the top of this architecture, there is a ``Server`` interface (Server Layer).
This ``Server`` interface has the following pure virtual functions:

.. code-block:: cpp

    /* Transport Layer */
    virtual void on_create_client(EndPoint* source, const dds::xrce::ClientKey& client_key) = 0;
    virtual void on_delete_client(EndPoint* source) = 0;
    virtual const dds::xrce::ClientKey get_client_key(EndPoint* source) = 0;
    virtual std::unique_ptr<EndPoint> get_source(const dds::xrce::ClientKey& client_key) = 0;

    /* Platform Layer */
    virtual bool init() = 0;
    virtual bool close() = 0;
    virtual bool recv_message(InputPacket& input_packet, int timeout) = 0;
    virtual bool send_message(OutputPacket output_packet) = 0;
    virtual int get_error() = 0;

The first 4 virtual function are transport specific (Transport Layer).
These functions are override by the ``UDPServerBase`` class which is in charge of managing the sessions between *Clients* and the *Agent*.

On the other hand, the last 5 virtual function are platform specific (Platform Layer).
These functions are override by the ``UDPServerLinux`` and ``UDPServerWindows`` for Linux and Windows systems respectively.

Client Transport Architecture
-----------------------------

The *Client* transport architecture is analogous to the *Agent* architecture.
There are also 3 different layers but instead of the Server Layer, there is a Session Layer.

.. image:: images/client_transport_stack.svg

* **Session Layer**: implements the XRCE protocol logic and it only knowns about sending and receiving messages.
* **Transport Layer**: implements the sending and receiving **message functions** for each transport protocol, calling to the Platform Layer functions.
  This layer only knows about sending and receiving messages through a given transport protocol.
* **Platform Layer**: implements the sending and receiving **data functions** for each platform.
  This layer only knows about sending and receiving raw data through a given transport in a given platform.

UDP Transport Example
^^^^^^^^^^^^^^^^^^^^^

As an example, this subsection describes how the UDP transport is implemented in *eProsima Micro XRCE-DDS Client*.
The figure below shows the *Client* transport architecture for the UDP transport.

.. image:: images/client_transport_architecture.svg

Similar to the *Agent* architecture, there is also an interface, ``uxrCommunication``, whose functions are call from the Session Layer.
That is, each time a ``run_session`` is called, the Session Layer calls to ``send_msg_func`` and ``recv_msg_func`` without worrying about the transport protocol or the platform in use.
This struct has the following virtual functions:

.. code-block:: c

    bool send_msg_func(void* instance, const uint8_t* buf, size_t len);
    bool recv_msg_func(void* instance, uint8_t** buf, size_t* len, int timeout);
    uint8_t comm_error_func(void);

These functions are override by the ``uxrUDPTransport``, which is in charge of two main tasks:

1. Override the communication interface functions. 
   For example, in the case of the UDP protocol these functions are the followings:

.. code-block:: c
   
    bool send_udp_msg(void* instance, const uint8_t* buf, size_t len);
    bool recv_udp_msg(void* instance, uint8_t** buf, size_t* len, int timeout);
    uint8_t get_udp_error(void);

2. Offer to the user the initialization and close functions related with the transport protocol.
   For example, in the case of the UDP protocol these functions are the followings:

.. code-block:: c

    bool uxr_init_udp_transport(uxrUDPTransport* transport, uxrUDPPlatform* platform, const char* ip, uint8_t port);
    bool uxr_close_udp_transport(uxrUDPTransport* transport);

For each platform there is an implementation of the virtual functions defined in the Transport Layer interface.
For example, in the case of Linux under UDP transport protocol, the ``uxrUDPPlatform`` implement the following functions:

.. code-block:: c

    bool uxr_init_udp_platform(uxrUDPPlatform* platform, const char* ip, uint16_t port);
    bool uxr_close_udp_platform(uxrUDPPlatform* platform);
    size_t uxr_write_udp_data_platform(uxrUDPPlatform* platform, const uint8_t* buf, size_t len, uint8_t* errcode);
    size_t uxr_read_udp_data_platform(uxrUDPPlatform* platform, uint8_t* buf, size_t len, int timeout, uint8_t* errcode);

Custom Serial Transport
-----------------------
*eProsima Micro XRCE-DDS* has a **Custom Serial Transport** with the following features:

* **HDLC Framing**: each serial framing begins with a ``begin_frame`` octet ``(0x7E)`` and the rest of the frame is byte stuffing using the ``space`` octet ``(0x7D)`` following by the original octet exclusive-or with ``0x20``.
  For example, if the frame contains the octet `0x7E` it is encoded as `0x7D, 0x5E`; and the same for the octet `0x7E` which is encoded as `0x7D, 0x5D`.
* **CRC Calculation**: serial frames end with a CRC-16 for detecting frame corruption. The CRC-16 is computed using the polynomial ``x^16 + x^12 + x^5 + 1`` after the frame stuffing for each octet of the frame and including the ``begin_frame``.
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

Shapes Topic Example
^^^^^^^^^^^^^^^^^^^^

This subsection shows how a **Shapes Topic**, defined by the IDL below, is packed in the Serial Transport.

::

    typedef struct ShapeType
    {
        char color[128];
        int32_t x;
        int32_t y;
        int32_t shapesize;
    } ShapeType;

    ShapeType topic = {"red", 11, 11, 89};

In the Serial Transport, the topic packaging could be divided in 2 steps:

    1. The Session Layer adds the XRCE header and subheader.
       It adds an overhead of 12 bytes to the topic. 
    2. The Serial Transport adds the serial header, CRC and stuffing the payload.
       In the best case, it adds an overhead of 7 bytes to the topic.

.. image:: images/serial_transport_stack.svg

The figure above shows the overhead added by the Serial Transport.
In the best case, it is **only 19 bytes**, but it should be noted that in this example the message stuffing has been neglected.
