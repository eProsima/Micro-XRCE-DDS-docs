.. _notes_label:

Version 3.0.0
=============

**Agent 3.0.0 | Client 3.0.0 | Micro-CDR 2.0.1 | Gen 2.0.2**

* `Agent 3.0.0 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/releases/tag/v3.0.0>`_:
    * This release includes the following major changes:
        * Bump Fast DDS to v3.1.0

* `Client 3.0.0 <https://github.com/eProsima/Micro-XRCE-DDS-Client/releases/tag/v3.0.0>`_:
    * This is a convenience release to match Agent major version bump.

* `Micro-CDR 2.0.1 <https://github.com/eProsima/Micro-CDR/releases/tag/v2.0.1>`_:
    * This release is not modified

* `Gen 2.0.2 <https://github.com/eProsima/Micro-XRCE-DDS-Gen/releases/tag/v2.0.2>`_:
    * This release is not modified



Version 2.4.3
=============

**Agent 2.4.3 | Client 2.4.3 | Micro-CDR 2.0.1 | Gen 2.0.2**

* `Agent 2.4.3 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/releases/tag/v2.4.3>`_:
    * This release includes the following minor changes:
        * Bump Fast DDS to v2.14.x.
        * Bump Fast CDR to v2.2.x.

* `Client 2.4.3 <https://github.com/eProsima/Micro-XRCE-DDS-Client/releases/tag/v2.4.3>`_:
    * This release includes the following minor changes:
        * Add BigHelloWorld example (`#379 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/379>`__)
    * This release includes the following bugfixes:
        * Fixed removal of z_impl_clock_gettime in zephyr 3.5.99 (`#378 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/378>`__)

* `Micro-CDR 2.0.1 <https://github.com/eProsima/Micro-CDR/releases/tag/v2.0.1>`_:
    * This release is not modified

* `Gen 2.0.2 <https://github.com/eProsima/Micro-XRCE-DDS-Gen/releases/tag/v2.0.2>`_:
    * This release is not modified


Version 2.4.2
=============

**Agent 2.4.2 | Client 2.4.2 | Micro-CDR 2.0.1 | Gen 2.0.2**

* `Agent 2.4.2 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/releases/tag/v2.4.2>`_:
    * This release includes the following minor changes:
        * Enable termios baudrate configuration for macOS (`#342 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/342>`__).
        * Enable Domain Override on Reference and XML Participant (`#351 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/351>`__).
        * Bump internal Micro XRCE-DDS Client to v2.4.2
        * Bump Fast DDS to v2.12.1.
        * Bump Fast CDR to v1.1.1.
    * This release includes the following bugfixes:
        * Fix deserialization endianness (`#336 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/336>`__).

* `Client 2.4.2 <https://github.com/eProsima/Micro-XRCE-DDS-Client/releases/tag/v2.4.2>`_:
    * This release includes the following bugfixes:
        * Rename UXR_CONFIG_CAN_TRANSPORT_MTU (`#372 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/372>`__)

* `Micro-CDR 2.0.1 <https://github.com/eProsima/Micro-CDR/releases/tag/v2.0.1>`_:
    * This release is not modified

* `Gen 2.0.2 <https://github.com/eProsima/Micro-XRCE-DDS-Gen/releases/tag/v2.0.2>`_:
    * This release includes the following bugfixes:
        * Force 4 Bytes to enum types (`#69 <https://github.com/eProsima/Micro-XRCE-DDS-Gen/pull/69>`__)


Version 2.4.1
=============

**Agent 2.4.1 | Client 2.4.1 | Micro-CDR 2.0.1 | Gen 2.0.1**

* `Agent 2.4.1 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/releases/tag/v2.4.1>`_:
    * This release includes the following minor changes:
        * Allow overriding the client's domain ID through reference (`#331 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/331>`__).
        * Override domain_id with ROS_DOMAIN_ID provided by the agent (`#333 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/333>`__).
        * Bump internal Micro XRCE-DDS Client to v2.4.1
        * Bump Fast DDS to v2.11.0.
        * Bump Fast CDR to v1.1.0.

* `Client 2.4.1 <https://github.com/eProsima/Micro-XRCE-DDS-Client/releases/tag/v2.4.1>`_:
    * This release includes the following bugfixes:
        * Fix freeaddrinfo call on UDP transport (`#359 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/359>`__)
        * Fix fragmentation check on uxr_prepare_reliable_buffer_to_write (`#360 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/360>`__)

* `Micro-CDR 2.0.1 <https://github.com/eProsima/Micro-CDR/releases/tag/v2.0.1>`_:
    * This release is not modified

* `Gen 2.0.1 <https://github.com/eProsima/Micro-XRCE-DDS-Gen/releases/tag/v2.0.1>`_:
    * This release includes the following minor changes:
        * Use size_t in for loops to fix integer comparisons (`#64 <https://github.com/eProsima/Micro-XRCE-DDS-Gen/pull/64>`__)
        * Update IDL-Parser submodule to latest v1.6.0 version.

Version 2.4.0
=============

**Agent 2.4.0 | Client 2.4.0 | Micro-CDR 2.0.1 | Gen 2.0.0**

* `Agent 2.4.0 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/releases/tag/v2.4.0>`_:
    * This release includes the following minor changes:
        * Bump internal Micro XRCE-DDS Client to v2.4.0
        * Bump Fast DDS to v2.10.0
        * Bump Fast CDR to v1.0.27

* `Client 2.4.0 <https://github.com/eProsima/Micro-XRCE-DDS-Client/releases/tag/v2.4.0>`_:
    * This release includes the following bugfixes:
        * Fix build for macOS (`#346 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/346>`__)
        * Fix newline-eof compiler warning (`#347 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/347>`__)
        * Fix doxygen warning (`#352 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/352>`__)
        * Add missing doxygen parameter (`#353 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/353>`__)
        * Fix domain id argument in create_participant API (`#348 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/348>`__)

    * This release includes the following **minor changes**:
        * Add CMAKE configuration for C standard version (`#340 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/340>`__)

* `Micro-CDR 2.0.1 <https://github.com/eProsima/Micro-CDR/releases/tag/v2.0.1>`_:
    * This release is not modified

* `Gen 2.0.0 <https://github.com/eProsima/Micro-XRCE-DDS-Gen/releases/tag/v2.0.0>`_:
    * Adapted generation to Micro XRCE-DDS Client v2.4.0 API.
    * This release includes the following minor changes:
        * Update gradlew scrip to use Gradle 7 and fix compatibility with Client 2.3.0 (`#47 <https://github.com/eProsima/Micro-XRCE-DDS-Gen/pull/47>`__)
        * Enumerations support (`#43 <https://github.com/eProsima/Micro-XRCE-DDS-Gen/pull/43>`__)
        * Update IDL-Parser dependency to latest master (`#59 <https://github.com/eProsima/Micro-XRCE-DDS-Gen/pull/59>`__)
        * Add case sensitive argument -cs (`#61 <https://github.com/eProsima/Micro-XRCE-DDS-Gen/pull/61>`__)
        * Support of include directories argument -I (`#60 <https://github.com/eProsima/Micro-XRCE-DDS-Gen/pull/60>`__)
        * Add support for modules in examples (`#48 <https://github.com/eProsima/Micro-XRCE-DDS-Gen/pull/48>`__)
    * This release includes the following bugfixes:
        * Avoid typedefs from IDL source files (`#58 <https://github.com/eProsima/Micro-XRCE-DDS-Gen/pull/58>`__)
        * Fix C namespaced module includes (`#53 <https://github.com/eProsima/Micro-XRCE-DDS-Gen/pull/53>`__)
        * Consume the bool return type and propagate it outward (`#46 <https://github.com/eProsima/Micro-XRCE-DDS-Gen/pull/46>`__, `#49 <https://github.com/eProsima/Micro-XRCE-DDS-Gen/pull/49>`__)

Version 2.3.0
=============

**Agent 2.3.0 | Client 2.3.0 | Micro-CDR 2.0.1**

* `Agent 2.3.0 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/releases/tag/v2.3.0>`_:
    * This release includes the following bugfixes:
        * Fix requester and replier reuse behavior (`#318 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/318>`__)
        * Increase cmake minimum required according to fastdds (`#323 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/323>`__)

    * This release includes the following minor changes:
        * Bump internal Micro XRCE-DDS Client to v2.3.0
        * Bump Fast DDS to v2.9.0
        * Bump Fast CDR to v1.0.26

* `Client 2.3.0 <https://github.com/eProsima/Micro-XRCE-DDS-Client/releases/tag/v2.3.0>`_:
    * This release includes the following bugfixes:
        * Increase cmake minimum required (`#335 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/335>`__)

    * This release includes the following minor changes:
        * Bump Micro CDR to v2.0.1

* `Micro-CDR 2.0.1 <https://github.com/eProsima/Micro-CDR/releases/tag/v2.0.1>`_:
    * This release includes the following bugfixes:
        * Remove COMPILE_LANGUAGE:CXX from set_common_compile_options (`#71 <https://github.com/eProsima/Micro-CDR/pull/71>`__)
        * Increase cmake minimum required (`#72 <https://github.com/eProsima/Micro-CDR/pull/72>`__)


Version 2.2.1
=============

**Agent 2.2.1 | Client 2.2.1 | Micro-CDR 2.0.0**

* `Agent 2.2.1 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/releases/tag/v2.2.1>`_:
    * This release includes the following bugfixes:
        * Fix exception on Heartbeat filter (`#314 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/314>`__)
        * Fix default QoS in Requester and Replier (`#313 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/313>`__)

    * This release includes the following minor changes:
        * Bump Fast DDS to v2.8 and Fast CDR to v1.0.24 (`#315 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/315>`__)

* `Client 2.2.1 <https://github.com/eProsima/Micro-XRCE-DDS-Client/releases/tag/v2.2.1>`_:
    * This release includes the following bugfixes:
        * Check setsockopt return (`#325 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/325>`__)

* `Micro-CDR 2.0.0 <https://github.com/eProsima/Micro-CDR/releases/tag/v2.0.0>`_:
    * This release is not modified


Version 2.2.0
=============

**Agent 2.2.0 | Client 2.2.0 | Micro-CDR 2.0.0**

* `Agent 2.2.0 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/releases/tag/v2.2.0>`_:
    * This release includes the following bugfixes:
        * Fix select timeout format (`#311 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/311>`__)
        * Default services to preallocated with realloc (`#310 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/310>`__)

    * This release includes the following minor changes:
        * Implement hard liveliness check (`#308 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/308>`__)

* `Client 2.2.0 <https://github.com/eProsima/Micro-XRCE-DDS-Client/releases/tag/v2.2.0>`_:
    * This release includes the following bugfixes:
        * SuperBuild.cmake: pass C, CXX and LINKER flags too (`#315 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/315>`__)
        * Add a nopoll version of the POSIX TCP transport profile (`#318 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/318>`__)
        * Fix wait_session_status listen timeout (`#322 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/322>`__)

    * This release includes the following minor changes:
        * Implement hard liveliness check (`#316 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/316>`__)

* `Micro-CDR 2.0.0 <https://github.com/eProsima/Micro-CDR/releases/tag/v2.0.0>`_:
    * This release is not modified

Version 2.1.1
=============

**Agent 2.1.1 | Client 2.1.1 | Micro-CDR 2.0.0**

* `Agent 2.1.1 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/releases/tag/v2.1.1>`_:
    * This release includes the following bugfixes:
        * Fix write destination id (`#292 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/292>`__)
        * Add sub entities destruction on FastDDS entities (`#295 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/295>`__)
        * Add reuse socket to TCP agent (`#301 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/301>`__)
        * Fix linux compile (`#297 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/297>`__)

    * This release includes the following minor changes:
        * Add CAN payload len on first frame byte (`#293 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/293>`__)
        * Add CAN transport flag to cmake / Upgrade splog version (`#296 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/296>`__)
        * Add Twitter and Readthedocs shields (backport #298) (`#299 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/299>`__)
        * Add use system spdlog flag (`#303 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/303>`__)
        * Implement GET_STATUS implementation result (`#304 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/304>`__)

* `Client 2.1.1 <https://github.com/eProsima/Micro-XRCE-DDS-Client/releases/tag/v2.1.1>`_:
    * This release includes the following bugfixes:
        * Fix fragment capacity overflow (`#296 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/296>`__)
        * Fix fragmentation header alignment (`#300 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/300>`__)
        * Fix run session timeouts (`#299 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/299>`__)
        * Fix code scanning alert (`#302 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/302>`__)
        * Fix exit run session condition (`#305 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/305>`__)
        * Fix multithread interlock (`#303 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/303>`__)
        * Reset stream on created session (`#304 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/304>`__)
        * Fix subscriber example (`#309 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/309>`__)
        * Fix Req Res example (`#314 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/314>`__)

    * This release includes the following minor changes:
        * RTEMS Serial Transport support (`#297 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/297>`__)
        * Add payload lenght on CAN messages (`#298 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/298>`__)
        * Add Twitter and Readthedocs shields (`#307 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/307>`__)
        * Implement GET_STATUS implementation result (`#312 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/312>`__)

* `Micro-CDR 2.0.0 <https://github.com/eProsima/Micro-CDR/releases/tag/v2.0.0>`_:
    * This release is not modified

Version 2.1.0
=============

**Agent 2.1.0 | Client 2.1.0 | Micro-CDR 2.0.0**

* `Agent 2.1.0 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/releases/tag/v2.1.0>`_:
    * This release includes the following bugfixes:
        * Style corrections (`#238 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/238>`__)
        * Fix packaging test (`#241 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/241>`__)
        * Fix serial error detection (`#251 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/251>`__)
        * Server: Add wait for error_handle (`#252 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/252>`_)
        * Fix use FastDDS profiles (`#260 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/260>`__)
        * Fix session key log (`#265 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/265>`_)
        * Fix custom transport bug (`#259 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/259>`__)
        * Add missing define if logger is disabled (`#267 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/267>`__)
        * Fix warning when CED disabled (`#272 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/272>`__)
        * FramingIO optimizations (`#278 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/278>`__)
        * Fix stream type on entities creation/destruction (`#284 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/284>`__)

    * This release includes the following minor changes:
        * Add wait for a serial port connection (`#246 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/246>`__)
        * Set runtime check for discovery and p2p protocols (`#254 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/254>`_)
        * Add flag for using system Fast-CDR (`#255 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/255>`_, `#256 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/256>`_)
        * Add LOG_INFO traces when entities are created (`#257 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/257>`_)
        * Add stop functionality (`#268 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/268>`_)

    * This release includes the following major changes:
        * Client shared memory support (`#236 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/236>`__)
        * Binary entity creation mode (`#239 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/239>`__, `#245 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/245>`__, `#248 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/248>`__, `#250 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/250>`_, `#273 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/273>`_)
        * Off-standard 64 kB write limit tweak (`#249 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/249>`_)
        * Multiserial agent functionality (`#253 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/253>`_, `#262 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/262>`__)
        * Build agent with Android NDK (`#280 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/280>`__, `#282 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/282>`__, `#283 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/283>`__)
        * Incoming heartbeats filter (`#277 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/277>`_)
        * Support for CAN/FD (`#285 <https://github.com/eProsima/Micro-XRCE-DDS-Agent/pull/285>`_)
        * Updated Fast-DDS to v2.4.1 and Fast-CDR to v1.0.22

* `Client 2.1.0 <https://github.com/eProsima/Micro-XRCE-DDS-Client/releases/tag/v2.1.0>`_:
    * This release includes the following bugfixes:
        * Minor fixes in FreeRTOS (`#236 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/236>`__, `#239 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/239>`__, `#270 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/270>`_)
        * Style corrections (`#222 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/222>`_, `#223 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/223>`_, `#231 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/231>`_, `#237 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/237>`_, `#247 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/247>`_, `#248 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/248>`__)
        * Fix missing declarations of inet_to family for POSIX_NOPOLL (`#272 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/272>`__)
        * Modified heartbeat calculations (`#251 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/251>`__)
        * FramingIO performance improvements (`#259 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/259>`__, `#267 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/267>`__)
        * Fix conditional compilation Shapes Demo Windows (`#262 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/262>`__)
        * Fix uxr_run_session_until_all_status (`#279 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/279>`_)
        * Add check to stream type on fragmented output (`#293 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/293>`_)

    * This release includes the following minor changes:
        * Doxygen updates (`#226 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/226>`_, `#229 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/229>`_, `#292 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/292>`_)
        * XRCE-DDS sessions runs at least once when timeout is 0 ms (`#212 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/212>`_)
        * Add argument to continuous fragment mode callback (`#260 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/260>`__)
        * Add flag to force micro-CDR build (`#264 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/264>`_)
        * Support building for Android with NDK. (`#269 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/269>`_)
        * Allow for pinging once and and return (`#282 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/282>`__)
        * Allow wait session with no timeout (`#280 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/280>`__)

    * This release includes the following major changes:
        * Binary entity creation mode (`#224 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/224>`_, `#232 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/232>`_, `#241 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/241>`__, `#246 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/246>`__, `#266 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/266>`_)
        * Multithread support and shared memory transport (`#216 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/216>`_, `#234 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/234>`_, `#240 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/240>`_, `#243 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/243>`_, `#245 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/245>`__, `#238 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/238>`__, `#263 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/263>`_, `#274 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/274>`_, `#289 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/289>`_, `#290 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/290>`_, `#291 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/291>`_, `#294 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/294>`_)
        * Off-standard 64 kB write limit tweak (`#244 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/244>`_)
        * Support for CAN/FD (`#278 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/278>`__, `#284 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/284>`__)
        * Support for RTEMS RTOS (`#283 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/283>`__, `#287 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/287>`_)

* `Micro-CDR 2.0.0 <https://github.com/eProsima/Micro-CDR/releases/tag/v2.0.0>`_:
    * This release includes the following bugfixes:
        * Fixed buffer handling in fragmentation for compatibility with FastDDS (`#69 <https://github.com/eProsima/Micro-CDR/pull/69>`_).

    * This release includes the following minor changes:
        * Only add -wsign-conversion if supported (`#68 <https://github.com/eProsima/Micro-CDR/pull/68>`_)
        * Avoid enabling CXX language (`#67 <https://github.com/eProsima/Micro-CDR/pull/67>`_)
        * Fix memcmp in tests (`#66 <https://github.com/eProsima/Micro-CDR/pull/66>`_)
        * Only add -wdouble-promotion if supported (`#65 <https://github.com/eProsima/Micro-CDR/pull/65>`_)
        * Update ABI Stability section (`#64 <https://github.com/eProsima/Micro-CDR/pull/64>`_)

Version 2.0.0
=============

**Agent 2.0.0 | Client 2.0.0 | Micro-CDR 1.2.1**

This version includes the following changes in both Agent and Client:

* Agent 2.0.0:
    * Add
        * `Micro XRCE-DDS Agent Snap package <https://snapcraft.io/micro-xrce-dds-agent>`_
        * Middleware callbacks API
        * Client to Agent ping feature without a session
        * Custom transports API
    * Fix / Modify
        * Simplified CLI and removed dependency with CLI11 library.
        * Optional disable of executable build.
        * CLI help console output.
        * Removed platform handling in user API.
* Client 2.0.0:
    * Add
        * POSIX transport with based on timeout instad of polling.
        * Client to Agent ping feature without a session
        * Continuos fragment mode
        * FreeRTOS+TCP transport support
        * Zephyr RTOS time functions support
        * Custom transports API
        * DDS-XRCE best effort examples
        * :code:`uxr_run_session_until_data` functionality
        * :code:`uxr_create_session_retries` functionality
        * :code:`uxr_buffer_topic` functionality
    * Fix / Modify
        * `Update <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/192>`_ session creating timing to linear approach
        * Modified :code:`uxr_prepare_output_stream` API return code
        * Removed :code:`client.config` file in favor of CMake arguments.
        * Removed platform handling in user API.
        * `Bugfix #156 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/156>`_ request/reply lenght management.
        * `Bugfix #167 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/167>`_ reliable fragment slots management.
        * `Bugfix #175 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/175>`_ reliable fragment size management.
        * `Bugfix #176 <https://github.com/eProsima/Micro-XRCE-DDS-Client/pull/176>`_ discovery message deserialization.
* Micro-CDR 1.2.1:
    * Fix / Modify
        * `Bugfix #53 <https://github.com/eProsima/Micro-CDR/pull/53>`_ fix in ucdr_reset_buffer function
        * `Bugfix #54 <https://github.com/eProsima/Micro-CDR/pull/54>`_ fix alignment zero-length sequence bug
        * `Bugfix #55 <https://github.com/eProsima/Micro-CDR/pull/55>`_ fix asymmetric fragmentation buffers

Version 1.3.0
=============

**Agent 1.4.0 | Client 1.2.3**

This version includes the following changes in both Agent and Client:

* Agent 1.4.0:
    * Add
        * FastDDS middleware (compatible with ROS 2 Foxy).
    * Fix
        * TermiosAgent's baudrate setting.
* Client 1.2.3:
    * Modify
        * Examples installation.
    * Fix
        * Minor Windows visibility function fixes.

Previous Versions
=================

Version 1.2.0
-------------

**Agent 1.3.0 | Client 1.2.1**

This version includes the following changes in both Agent and Client:

* Agent 1.3.0
    * Add
        * IPv6 support.
        * Requester/Replier support.
        * Packaging compatibility with colcon.
        * Isolated installation option.
        * Raspberry Pi support.
    * Change
        * Serial transport.

* Client 1.2.1
    * Add
        * IPv6 support.
        * Requester/Replier support.
        * Packaging compatibility with colcon.
        * Isolated installation option.

Version 1.1.0
-------------

**Agent 1.1.0 | Client 1.1.1**

This version includes the following changes in both Agent and Client:

* Agent 1.1.0:
    * Add
        * Message fragmentation.
        * P2P communication.
        * API.
        * Time synchronization.
        * Windows discovery support.
        * New unitary tests.
        * API documentation.
        * Logger.
        * Command Line Interface.
        * Centralized middleware.
        * Remove Asio dependency.
    * Change
        * CMake approach.
        * Server's thread pattern.
        * Fast RTPS version upgraded to 1.8.0.
    * Fix
        * Serial transport.

* Client 1.1.1:
    * Add
        * Message fragmentation.
        * Time synchronization.
        * Windows discovery support.
        * New unitary tests.
        * API documentation.
        * Raspberry Pi support.
    * Change
        * Memory usage improvement.
        * CMake approach.
        * Discovery API.
        * Examples usage.
    * Fix
        * Acknack reading.
        * User data bad alignment.

Version 1.0.3
-------------

**Agent 1.0.3 | Client 1.0.2**

This version includes the following changes in both Agent and Client:

* Agent 1.0.3:
    * Fast RTPS version upgraded to 1.7.2.
    * Baud rate support improvements.
    * Bugfixes.

* Client 1.0.2:
    * Uses new Fast RTPS 1.7.2 XML format.
    * Add Raspberry Pi toolchain.
    * Fix bugs.

Version 1.0.2
-------------

**Agent 1.0.2 | Client 1.0.1**

This version includes the following changes in the Agent:

* Agent 1.0.2:
    * Fast RTPS version upgraded to 1.7.0.
    * Added dockerfile.
    * Documentation fixes.

Version 1.0.1
-------------

**Agent 1.0.1 | Client 1.0.1**

This release includes the following changes in both Agent and Client:

* Agent 1.0.1:
    * Fixed Windows installation.
    * Fast CDR version upgraded.
    * Simplified CMake code.
    * Bug fixes.

* Client 1.0.1:
    * Fixed Windows configuration.
    * MicroCDR version upgraded.
    * Cleaned unused code.
    * Fixed documentation.
    * Bug fixes.

Version 1.0.0
-------------

This release includes the following features:

* Extended C topic code generation tool (strings, sequences, and n-dimensional arrays).
* Discovery profile.
* Native write access profile (without using *eProsima Micro XRCE-DDS Gen*)
* Creation and configuration by XML.
* Creation by reference.
* Added `REUSE` flag at entities creation.
* Added prefix to functions.
* Transport stack modification.
* More tests.
* Reorganized project.
* Bug fixes.
* API changes.

Version 1.0.0Beta2
------------------

This release includes the following features:

* Reliability.
* Stream concept (best-effort, reliable).
* Multiples streams of the same type.
* Configurable data delivery control.
* C Topic example code generation tool.
