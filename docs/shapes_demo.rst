.. _shapes_demo_label:

Shapes Demo
===========

`ShapesDemo <https://github.com/eProsima/ShapesDemo>`_ is an interactive example for testing how *eProsima Fast RTPS* working in the `DDS Global Data Space`.
Because *eProsima Micro XRCE-DDS* aims to connect an `XRCE Client` to the `DDS World`, in this example, we will create a *Client* which will interact with the `Shapes Demo`.
It can be found at `examples/uxr/client/ShapeDemoClient` inside of the installation directory.
This interactive *Client* waits for user input indicating commands to execute.

The available commands are the following:

create_session
    Creates a Session, if exists, reuse it.
create_participant <participant id>:
    Creates a Participant on the current session.
create_topic       <topic id> <participant id>:
    Registers a Topic using <participant id> participant.
create_publisher   <publisher id> <participant id>:
    Creates a Publisher on <participant id> participant.
create_subscriber  <subscriber id> <participant id>:
    Creates a Subscriber on <participant id> participant.
create_datawriter  <datawriter id> <publisher id>:
    Creates a DataWriter on the publisher <publisher id>.
create_datareader  <datareader id> <subscriber id>:
    Creates a DataReader on the subscriber <subscriber id>.
write_data <datawriter id> <stream id> [<x> <y> <size> <color>]:
    Writes data into a <stream id> using <data writer id> DataWriter.
request_data       <datareader id> <stream id> <samples>:
    Reads <sample> topics from a <stream id> using <datareader id> DataReader,
cancel_data        <datareader id>:
    Cancels any previous request data of <datareader id> DataReader.
delete             <id_prefix> <type>:
    Removes object with <id prefix> and <type>.
stream, default_output_stream <stream_id>:
    Changes the default output stream for all messages except of write data.
    <stream_id> can be 1-127 for best effort and 128-255 for reliable.
    The streams must be initially configured.
exit:
    Closes session and exit.
tree, entity_tree            <id>:
    Creates the necessary entities for a complete publisher and subscriber.
    All entities will have the same <id> as id.
h, help:
    Shows this message.

For example, to create a publisher *Client* that sends a square Topic in reliable mode, you need to run the following commands: ::

    > create_session
    > create_participant 1
    > create_topic 1 1
    > create_publisher 1 1
    > create_datawriter 1 1
    > write_data 1 128 200 200 40 BLUE

This *Client* will publish a topic in the reliable mode that will have color BLUE, x coordinate 200, y coordinate 200, and size 40.

In case of a subscriber *Client* that receives square topics in a reliable mode, run the following: ::

    > create_session
    > create_participant 1
    > create_topic 1 1
    > create_subscriber 1 1
    > create_datareader 1 1
    > request_data 1 128 5

This *Client* will receive 5 topics in reliable mode.

To create the entities tree easily, you can run the command ``entity_tree <id>``.
For example, the following command creates the necessary entities for publishing and subscribing data with id `3`: ::

    > entity_tree 3
    create_participant 3
    create_topic 3 3
    create_publisher 3 3
    create_subscriber 3 3
    create_datawriter 3 3
    create_datareader 3 3

To modify the output default stream, you can change it with `stream <id>`.

The maximum available streams correspond to the ``CONFIG_MAX_OUTPUT_BEST_EFFORT_STREAMS`` and
``CONFIG_MAX_OUTPUT_RELIABLE_STREAMS`` properties as CMake arguments.

    > stream 1

Now the messages will be sent in best-effort mode.
