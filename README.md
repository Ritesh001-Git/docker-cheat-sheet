# Docker Cheat Sheet

## Why Docker

"Docker enables developers to build portable apps in any language, using any toolchain, that run consistently across diverse environments, from local workstations to cloud and data center servers.

Docker Hub offers over 13,000 apps, enabling rapid development. Docker simplifies application management for sysadmins by tracking changes and dependencies. Docker Hub also allows developers to automate build pipelines and share artifacts via public or private repositories.

Docker helps developers build and ship higher-quality applications, faster." -- [What is Docker](https://www.docker.com/what-docker#copy1)

## Installation

### Linux

Run this quick and easy install script provided by Docker:

```sh
curl -sSL https://get.docker.com/ | sh
```

If you're not willing to run a random shell script, please see the [installation](https://docs.docker.com/engine/installation/linux/) instructions for your distribution.

If you are a complete Docker newbie, you should follow the [series of tutorials](https://docs.docker.com/engine/getstarted/) now.

#### Mac

1. Download docker desktop for mac from https://docs.docker.com/docker-for-mac/install/
2. Double-click `Docker.dmg` to open the installer and drag it to the Applications folder.
3. Double-click `Docker.app` in the Applications folder to start Docker.
