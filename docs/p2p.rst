.. _p2p_communication_label:

P2P Communication
=================

* What is P2P?
* How does it work?

.. image:: images/p2p_overview.svg

*eProsima Micro XRCE-DDS* offers P2P through the :ref:`CedMiddleware <ced_middleware_label>`.

How to use P2P
--------------

* Some considerations:

  * XML and REF are the same and they are treated as names of entities,
  * Publisher and Subscriber are neccesary?,
  * DataReaders and DataWriters shall has the same REF or XML as Topics.

Publish/Subscribe Example
^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: C

    int main(int args, char** argv)
    {
        // Subscriber example...
    }

.. code-block:: C

    int main(int args, char** argv)
    {
        // Publisher example...
    }
