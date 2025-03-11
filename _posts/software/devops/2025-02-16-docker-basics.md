---
title: Docker Basics
date: 2025-02-16 21:16:00 +0800
categories: [Software, DevOps]
tags: [docker]     # TAG names should always be lowercase
pin: false
math: false
mermaid: false
---

Here’s a basic tutorial on Docker, covering the essentials to help you get started with containerization.

---

## Docker Basics Tutorial

Docker is an open-source platform used to automate the deployment, scaling, and management of applications.
Docker uses containers to package applications and their dependencies into a portable and consistent unit.
This tutorial will cover the key concepts, commands, and use cases for Docker.

### 1. **What is Docker?**

Docker is a tool that allows you to run applications inside isolated environments called containers.
Containers are lightweight, fast, and portable. They include everything an application needs to run,
such as the code, runtime, libraries, and dependencies.

### 2. **Docker Architecture**
- **Docker Engine**: The core of Docker, consisting of a client (CLI), a server (Docker Daemon), and an API for communication.
- **Containers**: A lightweight, stand-alone package that contains everything needed to run an application.
- **Images**: Read-only templates used to create containers. Images can be pulled from a Docker registry (like Docker Hub) or built from a Dockerfile.
- **Docker Hub**: A public registry for sharing Docker images.

### 3. **Installing Docker**

Before starting with Docker, you need to install it.

#### On Ubuntu:
```bash
# Update the package list
sudo apt-get update

# Install prerequisites
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common

# Add Docker's official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# Add Docker's repository
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

# Install Docker
sudo apt-get update
sudo apt-get install docker-ce

# Verify installation
sudo docker --version
```

#### On macOS:
Install Docker Desktop from the official website [here](https://www.docker.com/products/docker-desktop/).

### 4. **Basic Docker Commands**

#### Checking Docker Status
To check if Docker is installed and running:
```bash
sudo systemctl status docker
```

#### Running Your First Container

The most basic way to run a Docker container is by using the `docker run` command. This will pull an image from Docker Hub and run a container.

```bash
# Run a container with the official "hello-world" image
docker run hello-world
```

#### Listing Containers
To list all running containers:
```bash
docker ps
```

To list all containers (including stopped ones):
```bash
docker ps -a
```

#### Stopping and Removing Containers

To stop a running container:
```bash
docker stop <container_name_or_id>
```

To remove a container:
```bash
docker rm <container_name_or_id>
```

#### Pulling and Pushing Docker Images

To pull an image from Docker Hub:
```bash
docker pull <image_name>
```

To push an image to Docker Hub (after logging in with `docker login`):
```bash
docker push <username>/<image_name>
```

### 5. **Building Docker Images**

Docker images are the blueprints for creating containers. You can build custom Docker images by writing a **Dockerfile**. A Dockerfile defines the environment and how to set up your application.

#### Example Dockerfile:
Here’s an example Dockerfile that sets up a basic Python environment:

```Dockerfile
# Use the official Python image as a base
FROM python:3.9-slim

# Set the working directory inside the container
WORKDIR /app

# Copy the current directory to the container
COPY . /app

# Install dependencies from requirements.txt
RUN pip install -r requirements.txt

# Set the command to run the application
CMD ["python", "app.py"]
```

#### Build the Image:
```bash
docker build -t my-python-app .
```

#### Running the Built Image:
```bash
docker run -d -p 5000:5000 my-python-app
```

This will run the image in detached mode (`-d`) and map port 5000 on your local machine to port 5000 in the container.

### 6. **Docker Volumes and Persistent Data**

Docker containers are ephemeral, meaning any data created inside a container will be lost when the container stops or is removed. To persist data, use Docker volumes.

To create a volume:
```bash
docker volume create my-volume
```

To run a container with the volume mounted:
```bash
docker run -v my-volume:/app/data my-python-app
```

This mounts the `my-volume` volume to the `/app/data` directory inside the container.

### 7. **Docker Compose**

Docker Compose is a tool for defining and running multi-container Docker applications. It uses a YAML file (`docker-compose.yml`) to configure services, networks, and volumes.

#### Example `docker-compose.yml`:

```yaml
version: '3'
services:
  web:
    image: my-python-app
    ports:
      - "5000:5000"
    volumes:
      - ./app:/app
  redis:
    image: redis
```

To start the services defined in `docker-compose.yml`:
```bash
docker-compose up
```

To stop the services:
```bash
docker-compose down
```

### 8. **Useful Docker Commands**

- **Show image details**: `docker inspect <image_name>`
- **Remove unused images**: `docker image prune`
- **List all Docker images**: `docker images`
- **Show logs for a container**: `docker logs <container_id_or_name>`

### 9. **Docker Networks**

Docker allows containers to communicate with each other over networks.
By default, containers can communicate if they are on the same network.

To create a custom network:
```bash
docker network create my-network
```

To run a container on the custom network:
```bash
docker run --network my-network <image_name>
```

### 10. **Conclusion**

Docker is a powerful tool for packaging, distributing, and running applications in a consistent environment.
By using Docker containers, you can ensure that your application runs the same way across different systems and environments.

### Next Steps:
- Explore Docker Hub for pre-built images.
- Learn how to optimize Dockerfiles for faster builds.
- Experiment with Docker Compose for multi-container applications.
