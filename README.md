# Docker Cheat Sheet

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
