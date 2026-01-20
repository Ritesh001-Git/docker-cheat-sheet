# Docker Cheat Sheet

## What is Docker?

Docker is a tool that lets you run applications inside lightweight, portable containers.  
A container includes everything the application needs to run (code, libraries, dependencies, and runtime), so it works the same everywhere — on your laptop, server, or in the cloud.

In short:
- No “works on my machine” issues
- Fast setup and consistent environments
- Easy packaging and deployment

Containers are isolated like virtual machines, but much lighter and faster because they share the host system’s kernel instead of emulating an entire operating system.

## Why Docker

"Docker enables developers to build portable apps in any language, using any toolchain, that run consistently across diverse environments, from local workstations to cloud and data center servers.

Docker Hub offers over 13,000 apps, enabling rapid development. Docker simplifies application management for sysadmins by tracking changes and dependencies. Docker Hub also allows developers to automate build pipelines and share artifacts via public or private repositories.

Docker helps developers build and ship higher-quality applications, faster." -- [What is Docker](https://www.docker.com/what-docker#copy1)

## Installation

#### Linux

You can install from a package easily
1. Go to https://download.docker.com/linux/ubuntu/dists/, choose your Ubuntu version and then go to pool/stable/ to get .deb file
2. Install Docker Engine by referring the downloaded location of the Docker package.
```cmd
$ sudo dpkg -i /path/to/package.deb
```
3. Verify the Docker Engine by running the `hello-world` image to check correct installation.
```cmd
$ sudo docker run hello-world
```

#### Mac

1. Download docker desktop for mac from https://docs.docker.com/docker-for-mac/install/
2. Double-click `Docker.dmg` to open the installer and drag it to the Applications folder.
3. Double-click `Docker.app` in the Applications folder to start Docker.

#### Windows
It supports for Windows 10 64-bit: Home, Pro, Enterprise, or Education, version 1903 (Build 18362 or higher). You need to follow the below steps for installation.

1. Download docker desktop for windows from https://docs.docker.com/docker-for-windows/install/
2. Double-click `Docker Desktop Installer.exe` to run the installer.
3. Make sure `Enable Hyper-V Windows Features` option is selected

## Key Docker Concepts (Glossary)

1. **Container** — A lightweight, isolated environment that runs an application and its dependencies.
2. **Image** — A snapshot or template used to create containers. Images contain the app code, libraries, and configuration.
3. **Containerization** — The process of packaging an application and its dependencies into containers.
4. **Registry** — A storage and distribution system for Docker images (e.g., Docker Hub, AWS ECR, GitHub Container Registry).
5. **Repository** — A collection of related Docker images with different versions/tags.
6. **Docker Engine** — The core component that builds and runs Docker images and containers.
7. **Volume** — A method to persist data outside the container’s lifecycle.
8. **Network** — Allows containers to communicate with each other or with external systems.
9. **Tag** — A label for an image version (e.g., `latest`, `1.2.0`, etc.).
10. **Dockerfile** — A script containing instructions on how to build a Docker image.

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
`docker container ls --filter "status=exited" --filter "ancestor=ubuntu"` | show all container instances of the ubuntu image that have exited | `docker container ls --filter "status=exited" --filter "ancestor=ubuntu"`
`docker container inspect [container-name]` | display detailed information about specified container | `docker container inspect web-container`
`docker container inspect --format '{{.NetworkSettings.IPAddress}}' [container-name]` | display container IP address using specified format | `docker container inspect --format '{{.NetworkSettings.IPAddress}}' web-container`
`docker container inspect --format '{{json .NetworkSettings}}' [container-name]` | display container network settings in JSON format | `docker container inspect --format '{{json .NetworkSettings}}' web-container`

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
`docker container rm $(docker container ls --filter "status=exited" --filter "ancestor=ubuntu" -q)` | remove all containers whose ids are returned from *'$(...)'* list | `docker container rm $(docker container ls --filter "status=exited" --filter "ancestor=ubuntu" -q)`

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
`docker container update [options] [container-name]` | update container resource limits | `docker container update --memory 512m web-container`
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
