# eProsima Micro XRCE-DDS

<a href="http://www.eprosima.com"><img src="https://encrypted-tbn3.gstatic.com/images?q=tbn:ANd9GcSd0PDlVz1U_7MgdTe0FRIWD0Jc9_YH-gGi0ZpLkr-qgCI6ZEoJZ5GBqQ" align="left" hspace="8" vspace="2" width="100" height="100" ></a>

*eProsima Micro XRCE-DDS* is a software solution which allows to communicate eXtremely Resource Constrained Environments (XRCEs) with an existing DDS network.
This implementation complies with the specification of the [eXtremely Resource Constrained Environments DDS (DDS-XRCE)](https://www.omg.org/spec/DDS-XRCE/) protocol as defined and maintained by the Object Management Group (OMG) consortium.

The *eProsima Micro XRCE-DDS* library implements a client-server protocol that enables resource-constrained devices (clients) to take part in DDS communications.
The *eProsima Micro XRCE-DDS Agent* (server) acts as a bridge to make this communication possible.
It acts on behalf of the *Micro XRCE-DDS Clients* to enable them to take part to the DDS Global Data Space
as DDS publishers and/or subscribers.

*eProsima Micro XRCE-DDS* provides both a plug and play *eProsima Micro XRCE-DDS Agent* and an API layer which allows the user to implement the *eProsima Micro XRCE-DDS Clients*.

![Architecture](docs/images/xrcedds_architecture.png)

## Commercial support

Looking for commercial support? Write us to info@eprosima.com

Find more about us at [eProsima’s webpage](https://eprosima.com/).

## Documentation

The online documentation is hosted by Read the Docs.

* [Start Page](http://micro-xrce-dds.readthedocs.io)
* [Installation manual](http://micro-xrce-dds.readthedocs.io/en/latest/installation.html)
* [User manual](http://micro-xrce-dds.readthedocs.io/en/latest/introduction.html)

## Build local documentation

On Ubuntu, run:

```bash
git clone https://github.com/eProsima/Micro-XRCE-DDS-docs
cd Micro-XRCE-DDS-docs
sudo apt -y install librsvg2-bin
python3 -m venv python_deps
source python_deps/bin/activate
pip3 install -U -r rtd_requirements.txt
make html
xdg-open build/html/index.html
```
