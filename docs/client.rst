Micro RTPS Client
=================

*Micro RTPS* provides you with a C API to create *Micro RTPS Clients*.

Within your *Micro RTPS Client* you can choose between two transport layers: UDP or SerialPort. Communication with the Agent is done through the transport you choose. The API functions to select transport are:

.. code-block:: c

    ClientState* new_serial_client_state(uint32_t buffer_size, const char* device);
    ClientState* new_udp_client_state(uint32_t buffer_size, uint16_t recv_port, uint16_t send_port);

Those two functions creates a ClientState object that you should keep during a communication session with a *Micro RTPS Agent*. You can free it using:

.. code-block:: c

    void free_client_state(ClientState* state);


Your *Micro RTPS Client* can request operations to the *Micro RTPS Agent* using the API provided:

.. code-block:: c

    XRCEInfo create_client(ClientState* state, OnStatusReceived on_status, void* on_status_args);
    XRCEInfo create_participant(ClientState* state);
    XRCEInfo create_topic(ClientState* state, uint16_t participant_id, String xml);
    XRCEInfo create_publisher(ClientState* state, uint16_t participant_id);
    XRCEInfo create_subscriber(ClientState* state, uint16_t participant_id);
    XRCEInfo create_data_writer(ClientState* state, uint16_t participant_id, uint16_t publisher_id, String xml);
    XRCEInfo create_data_reader(ClientState* state, uint16_t participant_id, uint16_t subscriber_id, String xml);

    XRCEInfo delete_resource(ClientState* state, uint16_t resource_id);

    XRCEInfo write_data(ClientState* state, uint16_t data_writer_id, SerializeTopic serialization, void* topic);
    XRCEInfo read_data(ClientState* state, uint16_t data_reader_id, DeserializeTopic deserialization,
                       OnTopicReceived on_topic, void* on_topic_args);

All this functions returns information about the entities and request identifiers for further reference.
This API functions just prepare requests for the *Micro RTPS Agent* but they dont send them. For pushing those request through the transport you need to call:

.. code-block:: c

    bool send_to_agent(ClientState* state);


In the other way of the communication (Agent to client) you need to provide two callbacks to get the responses from the Agent as well as receiving your data from DDS Global Data Space:

.. code-block:: c

    typedef void (*OnTopicReceived)(XRCEInfo info, const void* topic, void* args);
    typedef void (*OnStatusReceived)(XRCEInfo info, uint8_t operation, uint8_t status, void* args);

And make a call to receive so your transport feeds your client with new data coming from the Agent.

.. code-block:: c

    bool receive_from_agent(ClientState* state);