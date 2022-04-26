.. _optimization_label:

Memory optimization
===================

This section explains how memory is managed by the *eProsima Micro XRCE-DDS* library and
how it can be configured and customized by the user.
For more information regarding the internal handling of the memory in *eProsima Micro XRCE-DDS*,
refer to the detailed analysis and memory profiling
`here <https://www.eprosima.com/index.php/resources-all/performance/micro-xrce-dds-memory-profiling>`_.

Executable code size
--------------------

To tune the executable code size, the library can be compiled enabling or disabling several profiles.
To add or remove profiles from the library, enable or disable them as CMake arguments.
More information can be found at: :ref:`micro_xrce_dds_client_label`.

.. important::

    When compiling with *gcc*, it is highly recommended to compile it with the linker flag: ``-Wl,--gc-sections``.
    It will remove the code that the app doesn't use from the final executable.

Runtime size
------------

The *Client* is **dynamic** and **static** memory free: the whole memory footprint only depends
on how the **stack** grows during the execution.
Several values can be modified to control the stack growth:

Stream buffers
~~~~~~~~~~~~~~

Streams number
   It's possible to define a maximum of *127* best-effort streams and of *128* reliable streams.
   However, for most purposes, only one stream - either best effort or reliable - is needed.

History of a reliable stream
   The history is used for recovering lost messages and for speeding up the communication.
   For output streams, a bigger history will allow writing and sending more messages without having to wait
   for confirmation.
   However, if the history of an output stream is full (no messages confirmed by the *Agent* yet),
   no more messages can be stored in the stream.
   For input streams, the history is used for recovering lost messages faster while reducing the bandwidth.
   If the connection is highly reliable and saving memory is a priority, a reduced history can be used.

Stream size
   In case of reliable communication, this size is equal to :code:`MAX_MESSAGE_SIZE * HISTORY`.
   In case of a best-effort stream, the size simply equals :code:`MAX_MESSAGE_SIZE`,
   as no history is made available in this case.
   The :code:`MAX_MESSAGE_SIZE` represents the maximum message size that can be sent without
   fragmenting the message, and it must be less or equal than the *MTU* chosen for the selected transport.

MTU
   A different *MTU* can be chosen for each transport available.
   The *MTU* value can be defined as a CMake argument, and fixes the :code:`MAX_MESSAGE_SIZE` that can be
   sent or received. The transport uses the *MTU* value to create an internal buffer.
