# Docker Network
To understand Docker networking, you have to look at how Docker uses the Linux Kernel to create "virtual wires" between containers.

## 1. The Internal Hardware: veth pairs and docker0
Internally, Docker uses a Bridge Network (the default is named bridge). To make this work on a single host, Docker uses two key Linux networking components:

- The Virtual Ethernet (veth) Pair: Think of this as a virtual network cable with two ends. When a container starts, Docker creates a veth pair. One end is plugged into the container (as eth0), and the other end is plugged into a virtual switch on the host.

- The docker0 Bridge: This is a virtual Layer 2 switch created by Docker on your host. All containers on the default bridge are "plugged into" this switch.


