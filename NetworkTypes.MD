# Docker Networks

Docker networking allows containers to communicate with each other, the host machine, and external networks.

---

# Why Do We Need Docker Networks?

Without Docker networking:

- Containers cannot communicate with each other.
- Applications like **Frontend → Backend → Database** won't work.
- Containers cannot access external services (Internet, APIs, etc.).
- Traffic isolation and security become difficult.

Docker provides different network drivers for different use cases.

---

# Docker Network Types

| Network Type | Description | Scope | Best Use Case |
|--------------|-------------|-------|---------------|
| **bridge** | Default private network for containers on a single host. | Single Host | Most standalone container applications |
| **host** | Container shares the host's network stack. | Single Host | High-performance applications |
| **none** | Disables networking completely. | Single Host | Maximum isolation |
| **overlay** | Connects containers across multiple Docker hosts. | Multi Host | Docker Swarm clusters |
| **macvlan** | Assigns a real MAC and IP address to each container. | Physical Network | Legacy applications requiring direct LAN access |
| **ipvlan** | Similar to Macvlan but shares the parent MAC address. | Physical Network | Large-scale deployments with limited MAC addresses |
| **Custom Network** | User-created bridge or overlay network with custom configuration. | Single/Multi Host | Microservices and production applications |

---

# 1. Bridge Network (Default)

## What is it?

A **bridge network** is Docker's default networking mode.

Containers connected to the same bridge network can communicate with each other using container names.

```text
            Bridge Network

+------------+      +------------+
| Frontend   | ---> | Backend    |
+------------+      +------------+
        \              /
         \            /
        +----------------+
        |   Docker Bridge |
        +----------------+
```

---

## Create a Bridge Network

```bash
docker network create my-network
```

Run containers:

```bash
docker run -d --name backend --network my-network backend-image

docker run -d --name frontend --network my-network frontend-image
```

Backend is accessible as:

```text
http://backend:8080
```

---

## Real-Life Use Case

Suppose you're building:

```text
React App
      │
      ▼
Spring Boot API
      │
      ▼
MySQL Database
```

All containers connect to the same bridge network.

Advantages:

- Easy communication
- Container name resolution
- Secure private network

---

# 2. Host Network

## What is it?

The container uses the host machine's network directly.

No separate IP address is assigned.

```text
Host Machine
│
├── Port 80
├── Port 443
└── Container
```

Container shares:

- IP Address
- Network interfaces
- Ports

---

## Create

```bash
docker run --network host nginx
```

---

## Real-Life Use Case

Use Host networking for:

- High-performance applications
- Low latency
- Monitoring tools
- Network analysis

Examples:

- Prometheus Node Exporter
- Packet analyzers
- VPN servers

---

## Advantages

- No NAT
- Better performance
- Lowest latency

---

## Disadvantages

- Port conflicts
- Less isolation
- Linux only

---

# 3. None Network

## What is it?

Container has **no networking**.

```text
Container

❌ Internet
❌ Host
❌ Other Containers
```

---

## Create

```bash
docker run --network none ubuntu
```

---

## Real-Life Use Case

Useful for:

- Security testing
- Offline processing
- Batch jobs
- Cryptographic operations

Example:

```text
Image Converter
```

Input

↓

Processing

↓

Output

No Internet required.

---

# 4. Overlay Network

## What is it?

Overlay connects containers running on **multiple Docker hosts**.

```text
Host A
│
├── Container A

       Overlay Network

Host B
│
├── Container B

Host C
│
├── Container C
```

Containers communicate as if they were on the same machine.

---

## Create

```bash
docker network create \
--driver overlay \
my-overlay
```

---

## Real-Life Use Case

Suppose you have:

```text
Server 1
Frontend

Server 2
Backend

Server 3
Database
```

All communicate using one overlay network.

Perfect for:

- Docker Swarm
- Multi-server deployments
- Production clusters

---

# 5. Macvlan Network

## What is it?

Each container receives its **own MAC Address** and **its own IP Address** on the physical network.

The container behaves like an actual physical machine.

```text
Router

│

├── Laptop
├── Printer
├── NAS
└── Docker Container
```

Router sees the container as another device.

---

## Create

```bash
docker network create -d macvlan \
--subnet=192.168.1.0/24 \
--gateway=192.168.1.1 \
-o parent=eth0 \
mymacvlan
```

---

## Real-Life Use Case

Useful for:

- Legacy applications
- Network appliances
- DHCP servers
- Monitoring devices

Example:

Running Pi-hole inside Docker.

Every device accesses it directly.

---

# 6. IPvlan Network

## What is it?

Similar to Macvlan.

Difference:

- Containers share the parent MAC address.
- Each container gets a unique IP address.

```text
Parent MAC

│

├── Container 1 (IP1)

├── Container 2 (IP2)

└── Container 3 (IP3)
```

---

## Advantages

- Better scalability
- Less switch overhead
- Fewer MAC addresses

---

## Real-Life Use Case

Large data centers

Cloud infrastructure

Enterprise deployments

---

# 7. Custom Network

## What is it?

A user-defined network.

Usually based on:

- Bridge
- Overlay

Provides automatic DNS resolution.

---

## Create

```bash
docker network create my-app-network
```

---

## Example

```bash
docker run -d \
--network my-app-network \
--name mysql \
mysql
```

```bash
docker run -d \
--network my-app-network \
--name springboot \
springboot
```

Spring Boot connects to:

```text
mysql:3306
```

No IP addresses required.

---

# Real-Life Architecture

```text
                    Internet
                        │
                        ▼
                  Nginx Container
                        │
                        ▼
               React Frontend Container
                        │
                        ▼
             Spring Boot Backend Container
                        │
                        ▼
                MySQL Database Container
```

All four containers use the same **custom bridge network**.

---

# Which Network Should I Use?

| Scenario | Recommended Network |
|-----------|---------------------|
| Normal web applications | ✅ Bridge |
| Microservices | ✅ Custom Bridge |
| React + Spring Boot + MySQL | ✅ Custom Bridge |
| Docker Compose projects | ✅ Custom Bridge |
| Docker Swarm | ✅ Overlay |
| Kubernetes nodes | Overlay (managed by Kubernetes CNI plugins) |
| Maximum performance | Host |
| Legacy applications | Macvlan |
| Need a unique LAN IP | Macvlan |
| Enterprise networking | IPvlan |
| Offline processing | None |
| Security testing | None |

---

# Network Comparison

| Feature | Bridge | Host | None | Overlay | Macvlan | IPvlan | Custom |
|---------|--------|------|------|----------|----------|---------|--------|
| Internet Access | ✅ | ✅ | ❌ | ✅ | ✅ | ✅ | ✅ |
| Container-to-Container | ✅ | Via Host | ❌ | ✅ | ✅ | ✅ | ✅ |
| Cross Host | ❌ | ❌ | ❌ | ✅ | Limited | Limited | Depends |
| Isolation | High | Low | Highest | High | Medium | Medium | High |
| Performance | Good | Excellent | N/A | Good | Excellent | Excellent | Good |
| Production Ready | ✅ | Sometimes | Rarely | ✅ | ✅ | ✅ | ✅ |

---

# Common Interview Questions

### Which Docker network is the default?

**Bridge**

---

### Which network gives the best performance?

**Host**

---

### Which network is used in Docker Swarm?

**Overlay**

---

### Which network gives containers their own IP on the LAN?

**Macvlan**

---

### Which network disables all networking?

**None**

---

### Which network is most commonly used for microservices?

**Custom Bridge Network**

---

# Summary

| Network | Best For |
|----------|----------|
| **Bridge** | Default single-host applications |
| **Host** | High-performance networking |
| **None** | Secure isolated workloads |
| **Overlay** | Multi-host Docker Swarm clusters |
| **Macvlan** | Containers requiring direct LAN presence |
| **IPvlan** | Large-scale enterprise networking |
| **Custom Network** | Production microservices and Docker Compose applications |
