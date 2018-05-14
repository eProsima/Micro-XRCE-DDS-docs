.. _shapes_demo_label:

Shapes Demo
===========

`ShapesDemo <https://github.com/eProsima/ShapesDemo>`_ is an interactive example for testing how *Fast RTPS* working in the DDS Global Data Space.
Because the aim of *Micro RTPS* is connect a *Client* to the DDS World, in this example we will create a *Client* which will interact with the Shapes Demo.
This interactive *Client* waits for user input indicating commands to execute.

The available commands are the following:

create_session
    Creates a session
create_participant <participant id>
    Creates a new participant on the current session
create_topic       <topic id> <participant id>
    Registers new topic using <participant id> Participant
create_publisher   <publisher id> <participant id>
    Creates a publisher on <participant id> Participant
create_subscriber  <subscriber id> <participant id>
    Creates a subscriber on <participant id> Participant
create_datawriter  <datawriter id> <publisher id>
    Creates a dataWriter on the publisher <publisher id>
create_datareader  <datareader id> <subscriber id>
    Creates a dataReader on the subscriber <subscriber id>
write_data <datawriter id> <stream id> [<color> <x> <y> <size>]
    Write data into a <stream id> using <data writer id> DataWriter
read_data <datareader id> <stream id>
    Read data from a <stream id> using <data reader id> DataReader
delete <id>
    Removes a object (the entity created with the `create_*` function)  with <id> identifier
delete_session
    Deletes a session
exit
    Close program.


For example, to create a Publisher *Client* that sends an square Topic in reliable mode, you need to run the following commands in order: ::

> create_session
> create_participant 1
> create_topic 1 1
> create_publisher 1 1
> create_datawriter 1 1
> write_data 1 128 BLUE 200 200 40

The Topic will have color BLUE, x coordinate 200, y coordinate 200, size 40.

In case of a Subscriber *Client* that receives square Topics in a reliable mode, run the following: ::

> create_session
> create_participant 1
> create_topic 1 1
> create_subscriber 1 1
> create_datareader 1 1
> read_1 128

