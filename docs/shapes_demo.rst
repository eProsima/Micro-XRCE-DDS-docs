Shapes Demo
==========

We will go step by step on one of our Client examples using `ShapesDemo. <https://github.com/eProsima/ShapesDemo>`_
This is an example code of an interactive ShapesDemo Client.

This interactive client waits for user input indicating commands to execute.

For publishing a topic data you need to run the following commands in order:

* create_client
* create_participant
* create_topic 1
* create_publisher 1
* create_data_writer 1 3
* write_data 4

Participant will be assigned ID 1.
Topic will be assigned ID 2.
Publisher will be assigned ID 3.
And DaWriter will be assigned ID 4.

And for reading data:

* create_client
* create_participant
* create_topic 1
* create_subscriber 1
* create_data_reader 1 3
* read_data 4

The previous IDs are valid for a fresh run of Agent and Client pair. Other wise you need to get IDs from status messages responses sent from Agent.

Generated Type code:

.. code-block:: C

    ShapesDemo code here.
