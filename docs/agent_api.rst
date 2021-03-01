.. _agent_api_label:

Agent API
=========

The *Micro XRCE-DDS Agent* is developed using a fully compliant C++11 API.
This allowed to focus its development on modularity and usability,
while keeping it simple for the final user.

Keeping up with this philosophy, the API provided to the user attempts to be as much intuitive as possible,
while allowing to configure all the different aspects related to the *Agent*'s behaviour.

That being said, most user will find out that, with te provided *MicroXRCEAgent* standalone application,
it is more than enough to launch an *Agent* and start the communication process with *Micro XRCE-DDS Client* applications.
This is possible thanks to the intuitive built-in :ref:`agent_cli` and its multiple configuration parameters.

Alternatively, users can access the underneath *Agent* implementation and fine-tune all of its parameters, options, and behaviour in their final application.
This is specially useful when dealing with a :ref:`custom_transport` implementation.

The *Agent* API section is organized as follows:

* :ref:`agent_instance_class_api`
* :ref:`agent_class_api`

.. _agent_instance_class_api:

eprosima::uxr::AgentInstance
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The CLI manager, along with all the built-in *Agents* available for the supported transports
(UDP, TCP, Serial) are encapsulated in the ``eprosima::uxr::AgentInstance`` class.

------

.. code-block:: cpp

    AgentInstance& getInstance();

Gets a reference to the singleton ``AgentInstance`` wrapper class,
which allows launching a *Micro XRCE-DDS Agent* with user-given parameters.

------

.. code-block:: cpp

    bool create(int argc, char** argv);

Creates a UDP/TCP/Serial *Micro XRCE-DDS Agent*, based on the given arguments.

Returns ``true`` if the arguments were valid and an *Agent* was successfully created, ``false`` otherwise.

:argc: Number of arguments provided by the user via the CLI.
       This is usually inherited from the ``main`` loop.
:argv: List of arguments to be parsed by the CLI engine.

------

.. code-block:: cpp

    bool run();

Starts the execution of the previously created *Agent*, until it's ended by a user's interrupt or a process error.

------

.. code-block:: cpp

    template <typename ... Args>
    void add_middleware_callback(const Middleware::Kind& middleware_kind, const middleware::CallbackKind& callback_kind, std::function<void (Args ...)>&& callback_function);

Sets a user-defined callback function for a specific create/delete middleware entity operation.

:middleware_kind: Enum defining all the supported middlewares (see :ref:`middleware_abstraction_layer`).
:callback_kind: Enum holding all the possible create/delete operations:

.. code-block:: cpp

    enum class CallbackKind : uint8_t
    {
        CREATE_PARTICIPANT,
        CREATE_DATAWRITER,
        CREATE_DATAREADER,
        CREATE_REQUESTER,
        CREATE_REPLIER,
        DELETE_PARTICIPANT,
        DELETE_DATAWRITER,
        DELETE_DATAREADER,
        DELETE_REQUESTER,
        DELETE_REPLIER
    };

:callback_function: Callback to be defined by the user. It must follow a certain signature, depending on the middleware used.

------

.. _agent_class_api:

eprosima::uxr::Agent
^^^^^^^^^^^^^^^^^^^^

However, it is also possible for users to create and instantiate their own *Agent* instance, for example, to implement a :ref:`custom_transport_agent`.
Also, in some scenarios, it could be useful to have all the necessary ``ProxyClient``s and their associated DDS entities created by the *Agent* even before
*Clients* are started, so that *Clients* applications can avoid the process of creating the session and the DDS entities, and can focus on the communication.

This would allow a Micro XRCE-DDS Client application to be as tiny as it can be in terms of memory consumption.

The following API is provided to fulfill these requirements:

------

.. code-block:: cpp

    bool create_client(uint32_t key, uint8_t session, uint16_t mtu, Middleware::Kind middleware_kind, OpResult& op_result);

This function allows to create a ``ProxyClient`` entity, which can act on behalf of an external *Client* to request the creation/deletion of DDS entities.

It returns ``true`` if the creation was successful, ``false`` otherwise.

:key: The ``ProxyClient``'s identifier.
:session: The session ID to which the created ``ProxyClient`` is attached to.
:mtu: The *Maximum Transmission Unit* size.
:middleware_kind: The middleware used by the ``ProxyClient``, to be chosen among the ones presented in the :ref:`middleware_abstraction_layer`.
:op_result: The result status of this operation.

------

.. code-block:: cpp

    bool delete_client(uint32_t key, OpResult& op_result);

This function deletes a given ``ProxyClient`` from the client proxy database, given its ID.

Returns ``true`` if the operation was completed successfully, ``false`` otherwise (for example, if the provided ID was not registered to any ``ProxyClient``).

:key: The identifier of the ``ProxyClient`` to be removed.
:op_result: The result status of the operation.

------

.. code-block:: cpp

    bool create_participant_by_xml(uint32_t client_key, uint16_t participant_id, int16_t domain_id, const char* xml, uint8_t flag, OpResult& op_result);

This function creates a DDS participant for a given *Client*, given its self-contained description in an XML file.

The participant will act as an entry point for the rest of the DDS entities to be created.

It returns ``true`` if the creation was successful, ``false`` otherwise.

:client_key: The identifier of the ``ProxyClient`` to which the resulting participant will be attached to.
:participant_id: The identifier of the participant to be created.
:domain_id: The DDS domain ID associated to the participant.
:xml: The XML describing the participant properties.
:flag: It determines the creation mode of the new participant (see :ref:`creation_mode_table`).
:op_result: The result status of this operation.

------

.. code-block:: cpp

    bool create_participant_by_ref(uint32_t client_key, uint16_t participant_id, int16_t domain_id, const char* ref, uint8_t flag, OpResult& op_result);

This function creates a DDS participant for a given *Client*, given a reference to its description hosted in a certain XML descriptor file.

This reference file must have been previously loaded to the *Agent*.

The participant will act as an entry point for the rest of the DDS entities to be created.

Returns ``true`` if the creation was successful, ``false`` otherwise.

:client_key: The identifier of the ``ProxyClient`` to which the resulting participant will be attached to.
:participant_id: The identifier of the participant to be created.
:domain_id: The DDS domain ID associated to the participant.
:ref: The reference tag which will retrieve the participant description from the references file, previously loaded by the agent.
:flag: It determines the creation mode of the new participant (see :ref:`creation_mode_table`).
:op_result: The result status of this operation.

..
    TODO: Add reference to creation mode section (xml/ref).

------

.. code-block:: cpp

    bool delete_participant(uint32_t client_key, uint16_t participant_id, OpResult& op_result);

Removes a DDS participant from a certain client proxy.
Returns `true` if the participant was deleted, or `false` otherwise.

:client_key: The identifier of the `ProxyClient` from which the participant must be deleted.
:participant_id: The ID of the participant to be deleted.
:op_result: The result status of the operation.

------

.. code-block:: cpp

    bool create_<entity>_by_xml(uint32_t client_key, uint16_t <entity>_id, uint16_t <associated_entity>_id, const char* xml, uint8_t flag, OpResult& op_result);

Creates a certain DDS entity attached to an existing `ProxyClient`, given its client key.

An XML must be provided, containing the DDS description of the entity to be created.

There are as many methods available as existing DDS entities, replacing the parameters *<entity>* and *<associated_entity>* as follows:

.. _existing_entities_and_associated_entities:

*Agent's API available DDS entities and their associated entities*
******************************************************************

=============== =========================
**<entity>**    **<associated_entity>**
=============== =========================
topic           participant
publisher       participant
subscriber      participant
datawriter      publisher
datareader      subscriber
requester       participant
replier         participant
=============== =========================

This operation returns `true` if the entity is successfully created and linked to its associated entity (which must previously exist in the given `ProxyClient`), or false otherwise.

:client_key: The identifier of the `ProxyClient` to which the resulting entity will be attached to.
:<entity>_id: The ID of the DDS entity to be created.
:<associated_entity>_id: The identifier of the DDS entity to which this one will be assocciated.
:xml: The XML describing the entity properties.
:flag: It determines the creation mode of the new entity (see :ref:`creation_mode_table`).
:op_result: The result status of this operation.

------

.. code-block:: cpp

    bool create_<entity>_by_ref(uint32_t client_key, uint16_t <entity>_id, uint16_t <associated_entity>_id, const char* ref, uint8_t flag, OpResult& op_result);

Creates a certain DDS entity attached to an existing `ProxyClient`, given its client key.

The description of the entity to be created is hosted in a certain references file and must be tagged with the same tag name, provided as the `ref` parameter to this method.

There are as many methods available as existing DDS entities, replacing the parameters *<entity>* and *<associated_entity>* as shown above in the previous method description (see :ref:`existing_entities_and_associated_entities`).

This operation returns `true` if the entity is successfully created and linked to its associated entity (which must previously exist in the given `ProxyClient`), or false otherwise.

:client_key: The identifier of the `ProxyClient` to which the resulting entity will be attached to.
:<entity>_id: The ID of the DDS entity to be created.
:<associated_entity>_id: The identifier of the DDS entity to which this one will be assocciated.
:ref: The reference tag which will retrieve the DDS entity description from the references file.
:flag: It determines the creation mode of the new entity (see :ref:`creation_mode_table`).
:op_result: The result status of this operation.

..
    TODO: Add reference to creation mode section (xml/ref).

------

.. code-block:: cpp

    bool delete_<entity>(uint32_t client_key, uint16_t <entity>_id, OpResult& op_result);

Deletes a certain entity from a `ProxyClient`. Its associated entities will also be deleted, if applicable.

There exist as many method signatures of this type in the agent's API as available entities. See the :ref:`existing_entities_and_associated_entities` table for further information.

Returns `true` if the entity is correctly removed, or `false` otherwise.

:client_key: The identifier of the `ProxyClient` from which the entity must be deleted.
:<entity>_id: The ID of the DDS entity to be deleted.
:op_result: The result status of the operation.

------

.. code-block:: cpp

    bool load_config_file(const std::string& file_path);

Loads a configuration file which provides with the tagged XML definitions of the desired XRCE entities that can be created using the reference creation mode.

The used syntax must match the one defined for [FastDDS XML profile syntax](https://fast-dds.docs.eprosima.com/en/latest/fastdds/xml_configuration/xml_configuration.html),
where the *profile name* attributes represent the reference's names.

Returns `true` if the file was correctly loaded or `false` otherwise.

..
    TODO: Add reference to creation mode section (xml/ref).

:file_path: Relative path to the references file.

------

.. code-block:: cpp

    void reset();

Deletes all the `ProxyClient` instances and their associated DDS entities.

------

.. code-block:: cpp

    void set_verbose_level(uint8_t verbose_level);

Sets the verbose level of the logger, from `0` (logger is off) to `6` (critical, error, warning, info, debug and trace messages displayed).

:verbose_level: The level to be set.

------

.. code-block:: cpp

    template <typename ... Args>
    void add_middleware_callback(const Middleware::Kind& middleware_kind, const middleware::CallbackKind& callback_kind, std::function<void (Args ...)>&& callback_function);

Same functionality as the one described in :code:`add_middleware_callback`, for :ref:`agent_instance_class_api`.
