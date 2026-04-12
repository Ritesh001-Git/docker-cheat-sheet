# Docker Network
To understand Docker networking, you have to look at how Docker uses the Linux Kernel to create "virtual wires" between containers.

## 1. The Internal Hardware: veth pairs and docker0
Internally, Docker uses a Bridge Network (the default is named bridge). To make this work on a single host, Docker uses two key Linux networking components:

- The Virtual Ethernet (veth) Pair: Think of this as a virtual network cable with two ends. When a container starts, Docker creates a veth pair. One end is plugged into the container (as eth0), and the other end is plugged into a virtual switch on the host.

- The docker0 Bridge: This is a virtual Layer 2 switch created by Docker on your host. All containers on the default bridge are "plugged into" this switch.

## 2. How Communication Works (The "DNS" Magic)

Containers communicate differently depending on which network they are in:

- Default Bridge: Containers can only talk to each other via IP Addresses. DNS resolution (using the container name) does not work here by default.

- User-Defined Bridge: (Recommended) When you run docker network create my-net, Docker enables an embedded DNS server. Containers can talk to each other simply by using their names (e.g., ping database-service).

### How the packet travels:

- Source: Application in container-A sends a packet to container-B using its IP.
- Inside: The packet leaves the container via eth0.
- The Pipe: It travels through the veth pair to the docker0 bridge on the host.
- The Switch: The bridge looks at the destination IP and "switches" the packet to the veth end belonging to container-B.

## 3. When Communication Fails (Isolation)
Sometimes containers cannot talk to each other, even if they are on the same host. This happens because:
