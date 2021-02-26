.. _time_sync_label:

Time synchronization
====================

*eProsima Micro XRCE-DDS* offers a synchronization mechanism based on the NTP protocol.
It allows synchronizing *Client* with *Agents*, something very useful when working in embedded environments that do not provide any time synchronization mechanism.
The use of this feature is quite simple. 
The *Client* library provides the function ``uxr_sync_session`` which is all that is needed.
This function involves an exchange of messages between *Client* and *Agent* that allows the *Client* compute its time offset using the NTP protocol.
Apart from it, the *Client* library also provides a callback that allows users to implement their time synchronization protocol.

Find the code for a *Client* application making use of this time synchronization mechanism in the `TimeSync example app <https://github.com/eProsima/Micro-XRCE-DDS-Client/tree/master/examples/TimeSync>`_.

Another useful example shows how to use the time-synchronization callback. In particular, this example implements the NTP protocol with average computation to increase the time-offset's accuracy. Find the code for this example in the
`TimeSync with callback example app <https://github.com/eProsima/Micro-XRCE-DDS-Client/tree/master/examples/TimeSyncWithCb>`_.
