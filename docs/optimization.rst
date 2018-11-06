.. _optimization_label:

Memory optimization
===================

Executable code size
--------------------
In order to manage the executable code size, the library can be compiled enabling or disabling several profiles.
To add or remove a profile from the library, edit the ``client.config`` file.
More information can be found at: :ref:`micro_xrce_dds_client_label`.

NOTE: If you are compiling your app with *gcc*, its highly recommended compile it with the linker flag: ``-Wl,--gc-sections``.
This will remove the code that your app does not use from the final executable.

Runtime size
------------
The *Client* is dynamic memory free and static memory free.
This implies that all memory charge depends only on how the stack grows during the execution.
To control the stack, there are some values that can be modified and that impact to a large degree:

Stream buffers
~~~~~~~~~~~~~~
1. Make sure you define only the streams that will be used.
   You can define a maximum `127` best-effort streams and `128` reliable streams,
   but for the majority of purposes, only one stream of either best effort or reliable can be used.

2. The history of a reliable streams is used for recovering from lost messages and speeding up the communication.
   For output streams, more history value will allow to write and send more messages without wait confirmation.
   If a history of an output stream is full (no messages were still confirmed by the *Agent*), no more messages can be storaged into the stream.
   For input streams, the history is used for recovering faster when the messages are lost using less extra band-width.
   If your connection is highly reliable and to save memory is a priority, a reduced history can be used.

3. The size of the stream corresponds to the total reserved memory for the stream.
   The size must be according to the next value: ``MAX_MESSAGE_SIZE * HISTORY``.
   The ``MAX_MESSAGE_SIZE`` must be less or equal than the transport MTU used.

Transport MTUs
~~~~~~~~~~~~~~
Each transport have a different MTU.
The MTU value can be defined into the ``client.config`` file.
The MTU will represent the ``MAX_MESSAGE_SIZE`` that can be send or received.
The transport uses the MTU value to create an internal buffer.
