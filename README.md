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
