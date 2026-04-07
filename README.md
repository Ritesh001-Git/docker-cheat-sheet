# Docker Cheat Sheet

## What is Docker?

Docker is an open-source platform that enables developers to package their applications and dependencies into lightweight containers. These containers encapsulate everything an application needs to run, including code, runtime, system tools, and libraries. Docker containers are portable, scalable, and consistent across different environments, making it easier to develop, deploy, and manage applications.

In short:
- No “works on my machine” issues
- Fast setup and consistent environments
- Easy packaging and deployment

Containers are isolated like virtual machines, but much lighter and faster because they share the host system’s kernel instead of emulating an entire operating system.

## Why Docker

"Docker enables developers to build portable apps in any language, using any toolchain, that run consistently across diverse environments, from local workstations to cloud and data center servers.

Docker Hub offers over 13,000 apps, enabling rapid development. Docker simplifies application management for sysadmins by tracking changes and dependencies. Docker Hub also allows developers to automate build pipelines and share artifacts via public or private repositories.

Docker helps developers build and ship higher-quality applications, faster." -- [What is Docker](https://www.docker.com/what-docker#copy1)

## What is Virtualization?
Virtualization is a technology that creates virtual versions of computer resources such as hardware platforms, operating systems, storage devices, and network resources. It’s like creating a software-based replica of a physical machine, allowing you to run multiple isolated environments on the same hardware or across a distributed system.

- Imagine you have a powerful computer but you only use a small portion of its resources. 
- Virtualization allows you to split that computer into several virtual machines (VMs), each acting like a separate computer with its operating system and applications. 
- Each virtual machine is isolated from the others, meaning issues in one virtual machine won’t affect others. 
= This allows you to optimize resource utilization, run multiple applications on a single machine, and improve scalability by easily adding or removing virtual machines as needed.

## What is Containerization?
Containerization is a lightweight form of virtualization that allows you to run applications and their dependencies in isolated containers. Each container shares the same operating system kernel but is isolated from other containers, providing a portable and consistent runtime environment for applications.

- Containers provide process isolation, ensuring that applications running in one container do not affect applications running in other containers.
- Containers encapsulate all dependencies and configuration required to run an application, making them portable across different environments.
- Containers are lightweight compared to traditional virtual machines (VMs) because they share the host operating system kernel.
- Containers are designed to be scalable, allowing you to quickly scale up or down based on demand.
- Containers enable developers to build, test, and deploy applications more efficiently, leading to faster release cycles and improved collaboration between development and operations teams.

## 🏗️ Virtualization vs. Containerization
| Feature | Virtualization (VMs) | Containerization (Docker) |  
|---|---|---|  
| Architecture | Hardware-level: Virtualizes the physical hardware. | OS-level: Virtualizes the Operating System. |  
| OS Setup | Each VM has its own Full Guest OS (Windows, Linux, etc.). | All containers share the Host OS kernel. |  
| Weight | Heavyweight: Usually GBs in size (due to the full OS). | Lightweight: Usually MBs in size (only app + dependencies). |  
| Performance | Slower (requires booting an entire OS). | Near-native speed (starts in seconds/milliseconds). |  
| Isolation | Strong: Fully isolated at the hardware level. | Process-level: Isolated but shares the same system "heart" (kernel). |  
| Resource Usage | High (CPU/RAM is pre-allocated and often wasted). | Efficient (Uses only what the app needs from the host). |  
| Portability | Hard to move (VM images are huge and hardware-dependent). | Highly portable (Build once, run anywhere with Docker). |

<img width="3840" height="1332" alt="image" src="https://github.com/user-attachments/assets/456f682c-c9f0-49a9-b9fa-efeec11727c8" />

## Docker Architecture (Simple Explanation)

Docker uses a client–server architecture to manage containers. It consists of the Docker Client, Docker Engine, Docker Daemon, containerd, and Docker API. These components work together to create, run, and manage containers.

### Docker Engine
Docker Engine is the core runtime that builds and runs containers. It includes the Docker Daemon, containerd, and other components. All container-related operations happen inside the Docker Engine.

### Docker Daemon (dockerd)
The Docker daemon runs on the host machine and is responsible for managing Docker objects, such as images, containers, networks, and volumes.

Responsibilities:
- Creating and managing containers
- Managing images
- Managing networks and volumes
- Communicating with containerd

### Docker Client (CLI)
The Docker client is a command-line interface (CLI) tool that allows users to interact with the Docker daemon through commands. Users can build, run, stop, and manage Docker containers using the Docker CLI.

### Docker API
The Docker API is the communication layer between the Docker Client and Docker Engine. The client sends commands through this API, and the engine executes them. Communication happens using REST API over Unix socket or network.

Flow:
User → Docker CLI → Docker API → Docker Engine

### containerd
containerd is a low-level container runtime used by the Docker Daemon. It is responsible for actually creating, starting, stopping, and deleting containers.

containerd handles:
- Container execution
- Image pulling and storage
- Container lifecycle management

### runC
runC is a lightweight, portable container runtime. It includes all of the plumbing code used by Docker to interact with system features related to containers.

### How Everything Works Together

Step-by-step flow:

1. User runs command:
   docker run nginx

2. Docker Client sends request to Docker API

3. Docker API delivers request to Docker Daemon (dockerd)

4. Docker Daemon instructs containerd to create and start the container

5. containerd runs the container using OS kernel features

6. Container starts running inside Docker Engine

Summary flow:
User → Docker CLI → Docker API → Docker Daemon → containerd → Container → OS Kernel

This architecture allows Docker to efficiently manage containers using a layered and modular design.

### Registries and Repositories
#### Registry:
Docker Registry is a service that stores your docker images. It could be hosted by a third party, as public or private registry. Some of the examples are,

- Docker Hub,
- Quay,
- Google Container Registry,
- AWS Container Registry

#### Repository:
A Docker Repository is a collection of related images with same name which have different tags. These tags are an alphanumeric identifiers(like 1.0 or latest) attached to images within a repository.

For example, if you want to pull golang image using `docker pull golang:latest` command, it will download the image tagged latest within the `golang` repository from the Docker Hub registry. The tags appeared on dockerhub as below,

#### Login
Login to a registry
```js
> docker login [OPTIONS] [SERVER]

[OPTIONS]:
-u/--username username
-p/--password password

Example:

1. docker login localhost:8080 // Login to a registry on your localhost
2. docker login
```

#### Logout
Logout from a registry
```js
> docker logout [SERVER]

Example:

docker logout localhost:8080 // Logout from a registry on your localhost
```

## Docker Commands

### Important Docker Flags

Flag | Description | Use Case | Example
--- | --- | --- | ---
`-i` | keep STDIN open even if not attached | used for interactive input to container | `docker run -i ubuntu`
`-t` | allocate a pseudo-TTY (terminal) | used to get terminal access inside container | `docker run -t ubuntu`
`-it` | interactive + terminal | run container in interactive mode (most common) | `docker run -it ubuntu /bin/bash`
`-d` | run container in detached mode (background) | run services like web servers in background | `docker run -d nginx`
`-p` | map host port to container port | expose container service to outside world | `docker run -p 8080:80 nginx`
`-P` | map all exposed ports to random host ports | quick testing without specifying ports | `docker run -P nginx`
`--name` | assign custom name to container | easier container management | `docker run --name my-container nginx`
`--rm` | automatically remove container after it stops | useful for temporary or test containers | `docker run --rm ubuntu`
`-v` | mount volume or bind mount | persist data or share files | `docker run -v my-volume:/data ubuntu`
`--network` | connect container to a specific network | control container communication | `docker run --network my-network nginx`
`-e` | set environment variables | configure app settings inside container | `docker run -e ENV=prod nginx`
`--restart` | define restart policy | auto-restart containers on failure | `docker run --restart always nginx`
`--cpus` | limit CPU usage | control resource allocation | `docker run --cpus="1.5" nginx`
`--memory` | limit memory usage | prevent container from overusing RAM | `docker run --memory="512m" nginx`
`--entrypoint` | override default entrypoint | run custom command instead of default | `docker run --entrypoint /bin/bash nginx`

### Get docker info

#### General
Command | Description
--- | ---
`docker version` | provides full description of docker version
`docker -v` | provides a short description of docker version
`docker info` | display system wide information
`docker info --format '{{.DriverStatus}}'` | display 'DriverStatus' fragment from docker information
`docker info --format '{{json .DriverStatus}}'` | display 'DriverStatus' fragment from docker information in JSON format

### Manage Images
Docker images are read-only templates used to create containers. An image contains the application code, runtime, libraries, environment variables, and configuration files. Images ensure consistency and portability across development, testing, and production environments.

Command | Description | Example
--- | --- | ---
`docker image ls` | shows all local images | `docker image ls`
`docker image ls --filter 'reference=ubuntu:16.04'` | show images filtered by name and tag | `docker image ls --filter 'reference=ubuntu:16.04'`
`docker image pull [image-name]` | pull specified image from registry | `docker image pull nginx:latest`
`docker image rm [image-name]` | remove image for specified _image-name_ | `docker image rm nginx`
`docker image rm [image-id]` | remove image for specified _image-id_ | `docker image rm d1a364dc548d`
`docker image prune` | remove unused images | `docker image prune`

### Search Images

Command | Description | Example
--- | --- | ---
`docker search [OPTIONS] TERM` | search for an image in registry | `docker search golang`
`docker search [image-name] --filter "is-official=true"` | find only official images having *[image-name]* | `docker search nginx --filter "is-official=true"`
`docker search [image-name] --filter "stars=1000"` | find only images having specified *[image-name]* and 1000 or more stars | `docker search nginx --filter "stars=1000"`

### Manage Containers
Docker containers are lightweight, isolated runtime instances created from images. A container runs an application in a controlled environment while sharing the host system’s kernel. Containers start quickly, use fewer resources than virtual machines, and are ideal for microservices and scalable deployments.


#### Display Container Information
Command | Description | Example
--- | --- | ---
`docker container ls` | show all running containers | `docker container ls`
`docker container ls -a` | show all containers regardless of state | `docker container ls -a`
`docker container inspect [container-name]` | display detailed information about specified container | `docker container inspect web-container`

#### Run Container
Command | Description | Example
--- | --- | ---
`docker container run [image-name]` | run container based on specified image | `docker container run nginx`
`docker container run --rm [image-name]` | run container based on specified image and immediately remove it once it stops | `docker container run --rm ubuntu`
`docker container run --name fuzzy-box [image-name]` | assign name and run container based on specified image | `docker container run --name fuzzy-box nginx`

#### Remove Container

Command | Description | Example
--- | --- | ---
`docker container rm [container-name]` | remove specified container | `docker container rm web-container`

#### Container Lifecycle Management

Command | Description | Example
--- | --- | ---
`docker container start [container-name]` | start a stopped container | `docker container start web-container`
`docker container stop [container-name]` | stop a running container gracefully | `docker container stop web-container`
`docker container restart [container-name]` | restart a container | `docker container restart web-container`
`docker container pause [container-name]` | pause all processes inside a container | `docker container pause web-container`
`docker container unpause [container-name]` | resume a paused container | `docker container unpause web-container`
`docker container kill [container-name]` | force stop a container immediately | `docker container kill web-container`

#### Container Interaction & Debugging

Command | Description | Example
--- | --- | ---
`docker container logs [container-name]` | fetch logs of a container | `docker container logs web-container`
`docker container logs -f [container-name]` | stream container logs in real time | `docker container logs -f web-container`
`docker container exec -it [container-name] [command]` | run a command inside a running container interactively | `docker container exec -it web-container /bin/bash`
`docker container attach [container-name]` | attach local terminal to a running container | `docker container attach web-container`
`docker container top [container-name]` | display running processes inside container | `docker container top web-container`
`docker container stats` | display live resource usage statistics of containers | `docker container stats`

#### Container Metadata & Maintenance

Command | Description | Example
--- | --- | ---
`docker container rename [old-name] [new-name]` | rename a container | `docker container rename web-container app-container`
`docker container prune` | remove all stopped containers | `docker container prune`

#### Container Creation (Advanced Usage)

Command | Description | Example
--- | --- | ---
`docker container create [image-name]` | create a container without starting it | `docker container create nginx`
`docker container run -d [image-name]` | run container in detached mode | `docker container run -d nginx`
`docker container run -it [image-name]` | run container in interactive terminal mode | `docker container run -it ubuntu /bin/bash`
`docker container run -p [host-port]:[container-port] [image-name]` | map host port to container port | `docker container run -p 8080:80 nginx`
`docker container run -v [volume-name]:[path] [image-name]` | mount volume into container | `docker container run -v my-volume:/data ubuntu`
### Manage Volumes
Docker volumes provide persistent storage for containers. They are used to store data outside the container’s writable layer so that data is not lost when a container is stopped or removed. Volumes are commonly used for databases, logs, and shared data between containers.

#### Display Volume Information

Command | Description | Example
--- | --- | ---
`docker volume ls` | show all volumes | `docker volume ls`
`docker volume ls --filter "dangling=true"` | display all volumes not referenced by any containers | `docker volume ls --filter "dangling=true"`
`docker volume inspect [volume-name]` | display detailed information on *[volume-name]* | `docker volume inspect my-volume`

#### Remove Volumes

Command | Description | Example
--- | --- | ---
`docker volume rm [volume-name]` | remove specified volume | `docker volume rm my-volume`
`docker volume rm $(docker volume ls --filter "dangling=true" -q)` | remove all volumes having an id equal to any of the ids returned from *'$(...)'* list | `docker volume rm $(docker volume ls --filter "dangling=true" -q)`

### Networks
Docker networks enable communication between containers and external systems. They provide isolation, service discovery, and secure connectivity. Different network drivers (bridge, host, overlay, etc.) support various deployment scenarios, from single-host applications to multi-host clusters.
### Docker Network Types

Docker provides different network drivers to control how containers communicate with each other and with external systems. Each network type serves a specific use case.

#### Bridge Network
The bridge network is the default network driver for Docker containers. Containers connected to a bridge network can communicate with each other using container names. It provides isolation from the host network and is commonly used for single-host applications and microservices.

**Use case:** Local development, container-to-container communication on the same host.

#### Host Network
In the host network mode, the container shares the host machine’s network stack directly. This removes network isolation and avoids port mapping, resulting in better network performance.

**Use case:** High-performance networking scenarios where minimal latency is required.

#### None Network
The none network disables all networking for the container. Containers have no external network access and cannot communicate with other containers.

**Use case:** Security-sensitive workloads or batch jobs that do not require network connectivity.

#### Overlay Network
Overlay networks allow containers running on different Docker hosts to communicate with each other. This network type is used with Docker Swarm and supports multi-host container communication.

**Use case:** Distributed applications and clustered environments.

These network drivers allow Docker to support a wide range of application architectures, from standalone containers to distributed microservices.

#### Docker Network Management

Command | Description | Example
--- | --- | ---
`docker network ls` | list all Docker networks | `docker network ls`
`docker network inspect [network-name]` | display detailed information about a network | `docker network inspect bridge`
`docker network create [network-name]` | create a custom Docker network | `docker network create my-network`
`docker network create --driver bridge [network-name]` | create a bridge network explicitly | `docker network create --driver bridge app-network`
`docker network create --subnet [CIDR] [network-name]` | create network with a specific subnet | `docker network create --subnet 172.18.0.0/16 custom-net`
`docker network rm [network-name]` | remove a Docker network | `docker network rm my-network`
`docker network prune` | remove all unused networks | `docker network prune`

#### Container–Network Association

Command | Description | Example
--- | --- | ---
`docker network connect [network-name] [container-name]` | connect a running container to a network | `docker network connect my-network web-container`
`docker network disconnect [network-name] [container-name]` | disconnect a container from a network | `docker network disconnect my-network web-container`

#### Network Usage with Containers

Command | Description | Example
--- | --- | ---
`docker container run --network [network-name] [image-name]` | run container attached to a specific network | `docker container run --network my-network nginx`
`docker container run --network host [image-name]` | run container using host network stack | `docker container run --network host nginx`
`docker container run --network none [image-name]` | run container with no network access | `docker container run --network none ubuntu`

#### Network Drivers

Command | Description | Example
--- | --- | ---
`docker network create --driver bridge [network-name]` | create bridge network (default for single host) | `docker network create --driver bridge bridge-net`
`docker network create --driver host [network-name]` | create host network (Linux only) | `docker network create --driver host host-net`
`docker network create --driver overlay [network-name]` | create overlay network (Swarm mode) | `docker network create --driver overlay overlay-net`
`docker network create --driver macvlan [network-name]` | create macvlan network for direct LAN access | `docker network create --driver macvlan macvlan-net`

### Docker Exec (Work Inside Container)

Command | Description | Use Case | Example
--- | --- | --- | ---
`docker exec -it <container> bash` | open interactive bash shell | most common way to access container | `docker exec -it web-container bash`
`docker exec -it <container> sh` | open shell (for minimal images) | used when bash is not available | `docker exec -it web-container sh`
`docker exec <container> ls -la /tmp` | run command inside container | check files inside container | `docker exec web-container ls -la /tmp`
`docker exec <container> whoami` | check current user inside container | debugging permissions | `docker exec web-container whoami`
`docker exec -d <container> <command>` | run command in background | run scripts without blocking terminal | `docker exec -d web-container python script.py`
`docker exec -u root <container> <command>` | run command as specific user | perform admin/root tasks | `docker exec -u root web-container whoami`
`docker exec -e VAR=value <container> <command>` | pass environment variable | test configs inside container | `docker exec -e DEBUG=true web-container printenv DEBUG`
`docker exec -w <dir> <container> <command>` | set working directory | run command in specific path | `docker exec -w /app web-container ls`
`docker exec -it <container> /bin/bash` | explicit bash path | safer for some images | `docker exec -it web-container /bin/bash`
`docker exec -it <container> env` | list environment variables | debug env configs | `docker exec -it web-container env`
`docker exec -it <container> ps aux` | view running processes | inspect container processes | `docker exec -it web-container ps aux`
`docker exec -it <container> cat <file>` | read file inside container | check configs/logs | `docker exec -it web-container cat /etc/nginx/nginx.conf`

### Cleanup Commands
You may need to clean up unused Docker resources such as containers, images, volumes, and networks to free disk space and keep the Docker environment tidy.

#### Remove all unused resources

```cmd
docker system prune
```

#### Images

```cmd
$ docker images
$ docker rmi $(docker images --filter "dangling=true" -q --no-trunc)

$ docker images | grep "none"
$ docker rmi $(docker images | grep "none" | awk '/ / { print $3 }')
```

#### Containers

```cmd
$ docker ps
$ docker ps -a
$ docker rm $(docker ps -qa --no-trunc --filter "status=exited")
```

#### Volumes

```cmd
$ docker volume rm $(docker volume ls -qf dangling=true)
$ docker volume ls -qf dangling=true | xargs -r docker volume rm
```

#### Networks

```cmd
$ docker network ls
$ docker network ls | grep "bridge"
$ docker network rm $(docker network ls | grep "bridge" | awk '/ / { print $1 }')
```
## Normal Dockerfile (Single-Stage)

A normal Dockerfile uses a single `FROM` statement and builds everything (code, dependencies, build tools) inside one image.  
Unlike multi-stage builds, it does not separate build and runtime environments.

### Characteristics
- Simple and easy to write
- Includes all dependencies and tools in one image
- Larger image size
- Less optimized for production

### Example (Java Application)
```
FROM maven:3.9.6-eclipse-temurin-17

WORKDIR /app
COPY . .

RUN mvn clean package

CMD ["java", "-jar", "target/my-app.jar"]
```

### How it Works

1. Uses Maven image (includes JDK + build tools)
2. Copies project files into container
3. Builds the application using Maven
4. Runs the generated JAR file

### Build and Run

Build image: `docker build -t my-app .`

Run container: `docker run -p 8080:8080 my-app`

### Key Difference from Multi-Stage

- Normal Dockerfile → build tools + app → larger image  
- Multi-stage Dockerfile → only final app → smaller, optimized image

### When to Use

- Learning and development
- Small projects
- When optimization is not critical

## Multi-Stage Dockerfile

A multi-stage Dockerfile is used to reduce the final image size by separating the build environment from the runtime environment.  
It allows you to use multiple `FROM` statements, where each stage performs a specific task.

### Why use Multi-Stage Build?
- Smaller final image size
- No unnecessary build tools in production image
- Better security and performance
- Cleaner and optimized Docker images

### Example (Java Application)
```
### Stage 1: Build Stage
FROM maven:3.9.6-eclipse-temurin-17 AS builder
WORKDIR /app
COPY . .
RUN mvn clean package

### Stage 2: Runtime Stage
FROM eclipse-temurin:17-jdk-alpine
WORKDIR /app
COPY --from=builder /app/target/my-app.jar app.jar
CMD ["java", "-jar", "app.jar"]
```
### How it Works

1. First stage (`builder`):
   - Uses Maven image
   - Compiles the application
   - Generates JAR file

2. Second stage:
   - Uses lightweight Java runtime image
   - Copies only the final JAR from builder stage
   - Runs the application

### Build and Run

Build image: `docker build -t my-app .`

Run container: `docker run -p 8080:8080 my-app`

## Docker Compose

Docker Compose is a tool used to define and run multi-container Docker applications.  
Instead of running multiple `docker run` commands, you define all services (containers), networks, and volumes in a single `docker-compose.yml` file.

### Why Use Docker Compose?
- Manage multiple containers easily
- Define services in one file
- Automatic networking between containers
- Simplifies development and testing

### Example (docker-compose.yml)
```
version: "3.9"

services:
  web:
    image: nginx
    ports:
      - "8080:80"

  app:
    image: node:18
    working_dir: /app
    volumes:
      - .:/app
    command: "node server.js"
```
### How it Works

- Each service = one container
- All services run in the same network by default
- You can start/stop everything with a single command

### Docker Compose Commands

Command | Description | Example
--- | --- | ---
`docker-compose up` | create and start all services | `docker-compose up`
`docker-compose up -d` | start services in detached mode | `docker-compose up -d`
`docker-compose down` | stop and remove containers, networks | `docker-compose down`
`docker-compose build` | build or rebuild services | `docker-compose build`
`docker-compose ps` | list running services | `docker-compose ps`
`docker-compose logs` | view logs of services | `docker-compose logs`
`docker-compose logs -f` | follow logs in real-time | `docker-compose logs -f`
`docker-compose exec [service] [command]` | run command inside a service container | `docker-compose exec web bash`
`docker-compose restart` | restart all services | `docker-compose restart`
`docker-compose stop` | stop services without removing them | `docker-compose stop`
`docker-compose start` | start existing stopped services | `docker-compose start`
