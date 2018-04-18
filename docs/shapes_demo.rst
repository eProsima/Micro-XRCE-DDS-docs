Shapes Demo
===========

`ShapesDemo. <https://github.com/eProsima/ShapesDemo>`_ is an interactive example for test how *Fast RTPS* working in the DDS Global Data Space.
Because the aim of *Micro RTPS* is connect a Client to the DDS World, in this example we will create a client which will interact with the Shapes Demo.
This interactive client waits for user input indicating commands to execute.

The available commands are the following:

create_session
    Creates a Session
create_participant <participant id>
    Creates a new Participant on the current session
create_topic       <topic id> <participant id>
    Register new Topic using <participant id> participant
create_publisher   <publisher id> <participant id>
    Creates a Publisher on <participant id> participant
create_subscriber  <subscriber id> <participant id>
    Creates a Subscriber on <participant id> participant
create_datawriter  <datawriter id> <publisher id>
    Creates a DataWriter on the publisher <publisher id>
create_datareader  <datareader id> <subscriber id>
    Creates a DataReader on the subscriber <subscriber id>
write_data <datawriter id> <stream id> [<color> <x> <y> <size>]
    Write data into a <stream id> using <data writer id> DataWriter
read_data <datareader id> <stream id>
    Read data from a <stream id> using <data reader id> DataReader
delete <id>
    Removes object with <id> identifier
exit
    Close program previously notifying the agent.


For example, to create a publisher client that send a square topic in reliable mode, you need to run the following commands in order: ::

> create_session
> create_participant 1
> create_topic 1 1
> create_publisher 1 1
> create_datawriter 1 1
> write_data 1 128 BLUE 200 200 40

The topic will have color BLUE, x coordinate 200, y coordinate 200, size 40.

In case of a subscriber client that receive square topics in a reliable mode, run the following: ::

> create_session
> create_participant 1
> create_topic 1 1
> create_subscriber 1 1
> create_datareader 1 1
> read_1 128

