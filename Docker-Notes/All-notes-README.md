

# Docker Study Guide: From Basics to Advanced Deployment[1]

This study guide covers the fundamental and advanced concepts of Docker, including its architecture, core components, networking, storage, and orchestration with Docker Compose, as well as an introduction to Kubernetes and practical project deployments.[1]

## I. Introduction to Docker[1]
### 1. What is Docker?[1]
- A tool that creates isolated environments (containers) for applications.[1]
- Resolves the "works on my machine, but not on yours" problem by packaging applications with all necessary libraries and dependencies.[1]
- An open-source tool, originating from DotCloud in 2013 and becoming publicly available at PyCon. It moved to the Cloud Native Computing Foundation (CNCF) in 2017.[1]

### 2. Why Use Docker?[1]
- Portability: Applications run consistently across different environments (development, testing, production).[1]
- Isolation: Each application runs in its own container, preventing conflicts between dependencies.[1]
- Efficiency: Containers are lightweight, sharing the host OS kernel, unlike virtual machines.[1]
- Scalability: Easily scale applications by deploying multiple containers.[1]
- Automation: Docker Compose allows for the automation of multi-container application deployment.[3]

### 3. Virtualization vs. Containerization[3]
- Virtualization (VMs): Uses a hypervisor to create multiple guest operating systems (OS) on a single physical machine. Each VM has its own OS, leading to higher resource consumption.[3]
- Containerization (Docker): Containers share the host OS kernel, making them much lighter and faster to start than VMs. Docker Engine manages containers on top of the host OS.[1]
- Resource Usage: VMs consume more RAM (e.g., 1-2 VMs on 8GB RAM), while containers are lightweight, allowing many to run on the same resources.[3]

## II. Docker Architecture[1]



### 1. Key Components[1]
- Docker Engine: The core component that runs and manages Docker containers. Also known as Docker Application Container Engine.[1]
- Docker Daemon (dockerd): A background service that manages Docker objects like images, containers, networks, and volumes. It listens for Docker API requests.[1]
- Docker CLI (Command Line Interface): The primary way users interact with Docker. Commands like docker run, docker ps, docker build are executed via the CLI, which then communicates with the Docker Daemon.[1]
- Containerd: A core container runtime that Docker Daemon uses in the background for managing container lifecycle (start, stop, pause, delete). It’s written in GoLang and is a CNCF project.[1]
- Docker Client/Docker Desktop: A user interface (like Docker Desktop) or command-line client that connects to the Docker Engine via its API to manage containers and images.[1]

## III. Installation and Setup[1]
### 1. Local Installation (Docker Desktop)[1]
- Download Docker Desktop from the official Docker website.[1]
- Create a Docker Hub account.[1]
- Docker Desktop provides a GUI to monitor running containers, images, volumes, and more.[1]

### 2. AWS EC2 Instance Installation[1]
- Launch an EC2 instance (e.g., Ubuntu).[1]
- Use t2.micro (free tier eligible) for practice or t2.medium for more resources (4GB RAM).[1]
- Connect to the instance via SSH using a .pem key.[1]
- Update the system: sudo apt-get update.[1]
- Install Docker: sudo apt-get install docker.io. This installs Docker Engine, Daemon, CLI, and Containerd.[1]
- Verify Docker status: sudo systemctl status docker.[1]
- Add the current user to the Docker group to avoid permission issues: sudo usermod -aG docker $USER.[1]
- Refresh the Docker group: newgrp docker.[1]
- Verify Docker CLI functionality: docker ps.[1]

## IV. Docker Images and Containers[1]
### 1. Docker Images[1]
- Blueprint: An image is a read-only template that contains the application code, libraries, dependencies, and environment required to run an application.[1]
- Layers: Images are built in layers, where each instruction in a Dockerfile creates a new layer. This allows for efficient caching and smaller updates.[1]
- Creation: Images are built from a Dockerfile.[1]
- Pulling Images: Download pre-built images from Docker Hub using docker pull <image_name>.[1]
- Pushing Images: Upload custom-built images to Docker Hub (or other registries) using docker push <repository_name>/<image_name>:<tag>. Requires tagging the image first: docker tag <local_image_name> <repository_name>/<image_name>:<tag>.[1]

### 2. Docker Containers[1]
- Instance of an Image: A container is a runnable instance of a Docker image.[1]
- Lifecycle: Containers can be started, stopped, restarted, and removed.[1]
- Running Containers: docker run <image_name> creates and starts a container.[1]
- Detached Mode: docker run -d <image_name> runs a container in the background.[1]
- Port Mapping: docker run -p <host_port>:<container_port> <image_name> maps a port on the host to a port in the container, allowing external access to the application.[1]
- Environment Variables: docker run -e <KEY>=<VALUE> <image_name> passes environment variables to the container.[1]
- Listing Containers: docker ps shows running containers; docker ps -a shows all containers (running, stopped, exited).[1]
- Stopping/Starting/Removing Containers:
  - docker stop <container_id/name>[1]
  - docker start <container_id/name>[1]
  - docker rm <container_id/name>[1]
- Executing Commands in Containers: docker exec -it <container_id/name> <command> runs a command inside a running container (e.g., docker exec -it <container_id> bash for a shell).[1]
- Viewing Container Logs: docker logs <container_id/name>. Use docker attach <container_id/name> for real-time logs (note: this blocks the terminal).[1]

### 3. Dockerfile[4]
- Instructions for Image Building: A text file containing a series of instructions that Docker uses to build an image.[4]
- Common Instructions:
```
  - FROM: Specifies the base image (e.g., FROM python:3.8-slim).[5]
  - WORKDIR: Sets the working directory inside the container.[5]
  - COPY: Copies files from the host to the container.[5]
  - RUN: Executes commands during the image build process (e.g., RUN pip install -r requirements.txt).[5]
  - EXPOSE: Informs Docker that the container listens on the specified network ports at runtime (e.g., EXPOSE 8000).[5]
  - CMD: Provides default commands for an executing container. Can be overridden when running the container.[5]
  - ENTRYPOINT: Configures a container that will run as an executable. Cannot be easily overridden.[5]
- Building Images: docker build -t <image_name>:<tag> . (the dot specifies the build context, usually the current directory).[4]
```
### 4. Multi-Stage Docker Builds[6]
- Optimizing Image Size: A technique to create smaller, more secure images by dividing the build process into multiple stages.[6]
- Process:
  - Builder Stage: Use a larger base image (e.g., with build tools like Maven or a full SDK) to compile code and install dependencies.[6]
  - Runtime Stage: Use a minimal base image (e.g., Alpine or slim versions) and copy only the necessary compiled application artifacts and runtime dependencies from the builder stage.[6]
- Benefits: Reduces the final image size, attack surface, and build/push/pull times.[6]

## V. Docker Networking[1]
### 1. Purpose[1]
- Enables communication between Docker containers and between containers and the outside world.[1]
- Containers by default are isolated. Networking explicitly connects them.[1]

### 2. Types of Docker Networks[1]
- Host Network: Containers share the host's network stack directly. No port mapping is needed, but it reduces isolation.[1]
- Bridge Network (Default): The default network for containers. Docker creates a virtual bridge that containers connect to, allowing them to communicate with each other and the host via NAT.[1]
- User-Defined Bridge Networks: Custom bridge networks created by the user. Provide better isolation and easier service discovery (containers can be referenced by name).[3]
- docker network create -d bridge <network_name>[1]
- None Network: The container is completely isolated, with no network interfaces. Useful for containers that don't require network access.[1]
- Macvlan/IPVlan/Overlay Networks: Advanced networks typically used with Docker Swarm for clustering multiple Docker hosts.[1]

### 3. Network Management[1]
- List Networks: docker network ls[1]
- Inspect Network: docker network inspect <network_name> (shows connected containers).[1]
- Connecting Containers: Use the --network <network_name> flag when running containers.[1]

## VI. Docker Volumes and Storage[1]
### 1. Persistence[1]
- Containers are ephemeral; data inside them is lost when the container is removed.[1]
- Volumes provide a way to persist data generated by or used by Docker containers.[1]

### 2. Types of Volumes[1]
- Named Volumes: Docker manages the creation and location of these volumes on the host system. They are easy to back up and manage.[1]
  - docker volume create <volume_name>[1]
  - Attach to container: docker run -v <volume_name>:<container_path> <image_name>[1]
- Bind Mounts: The absolute path on the host is controlled and mapped into the container.[1]
  - Attach to container: docker run -v <host_path>:<container_path> <image_name>[1]
- tmpfs Mounts: Store data in the host's memory, ideal for temporary, non-persistent data.[1]

### 3. Volume Management[1]
- List Volumes: docker volume ls[1]
- Inspect Volume: docker volume inspect <volume_name>[1]
- Remove Volume: docker volume rm <volume_name> (ensure no containers are using it).[1]

## VII. Docker Compose[1]
### 1. Orchestration for Multi-Container Apps[1]
- A tool for defining and running multi-container Docker applications.[1]
- Uses a YAML file (docker-compose.yml) to configure application services, networks, and volumes.[1]
- Automates the manual process of building images, running containers, configuring networks, and managing volumes.[3]

### 2. docker-compose.yml Structure[1]
- version: Specifies the Compose file format version (e.g., 3.8).[1]
- services: Defines the containers (services) that make up your application.[1]
- build: Specifies the Dockerfile context for building an image.[1]
- image: Specifies a pre-existing image from Docker Hub.[1]
- container_name: Assigns a custom name to the container.[1]
- ports: Maps host ports to container ports.[1]
- environment: Sets environment variables.[1]
- volumes: Mounts volumes.[1]
- networks: Attaches to specified networks.[1]
- depends_on: Defines service dependencies (ensures services start in a specific order).[1]
- restart: Configures container restart policy (e.g., always).[1]
- healthcheck: Defines commands to check the health of a container, ensuring it's fully ready before dependent services start.[1]
- volumes: Defines named volumes.[1]
- networks: Defines user-defined networks.[1]

### 3. Docker Compose Commands[1]
- Install: sudo apt-get install docker-compose-v2 (for version 2).[1]
- Start Application: docker compose up (foreground) or docker compose up -d (detached mode). Use docker compose up --build to force rebuilding images.[1]
- Stop and Remove: docker compose down (stops and removes containers, networks, and volumes specified in the Compose file).[1]

## VIII. Docker Registry[1]
### 1. Central Storage for Images[1]
- A repository for Docker images. Docker Hub is the most popular public registry.[1]
- Push: docker push <repository_name>/<image_name>:<tag> uploads a local image to the registry.[1]
- Pull: docker pull <repository_name>/<image_name>:<tag> downloads an image from the registry.[1]
- Tagging: docker tag <local_image_name> <repository_name>/<image_name>:<tag> associates a local image with a registry name and tag.[1]

## IX. Introduction to Kubernetes[1]
### 1. Container Orchestration[1]
- A powerful open-source system for automating deployment, scaling, and management of containerized applications.[1]
- Manages Docker containers in a cluster environment, providing auto-healing, auto-scaling, and load balancing.[1]
- Problem it Solves: Prevents individual Docker containers from crashing in production by providing robust management and recovery.[1]

### 2. Key Concepts (Kubernetes)[1]
- Pods: The smallest deployable units in Kubernetes, containing one or more Docker containers that share network and storage.[1]
- Deployments: Manages a set of identical pods, ensuring a desired number of replicas are running and facilitating rolling updates.[1]
- Services: An abstraction that defines a logical set of pods and a policy by which to access them (e.g., load balancing across pods).[1]
- Ingress: Manages external access to services in a cluster, typically HTTP/S, by providing routing rules.[1]

## X. Docker Monitoring and Logging[1]
### 1. Basic Logging[1]
- docker logs <container_id/name>: Retrieves logs from a container.[1]
- Redirect logs to a file: Use tools like nohup for background execution and output redirection.[1]

### 2. Docker Scout[1]
- Image Scanning: A tool for advanced image analysis to identify security vulnerabilities (CVEs) in Docker images, including those in base images and installed packages.[1]
- Integration: Available within Docker Desktop.[1]
- Commands:
  - docker scout quickview <image_name>: Provides a quick overview of vulnerabilities.[1]
  - docker scout cves <image_name>: Lists detailed CVEs found in the image.[1]

## XI. Docker Init[1]
### 1. Automated Dockerfile and Compose File Generation[1]
- A command-line tool that generates a basic Dockerfile, docker-compose.yml, and .dockerignore file based on your project type (e.g., Go, Python, Node.js).[1]
- Benefits: Speeds up the initial setup of Docker for new projects.[1]
- Command: docker init (then follow the interactive prompts).[1]

## Quiz: Docker Fundamentals[2]
### Instructions: Answer each question in 2-3 sentences.[2]
1. What core problem does Docker aim to solve, and how does it achieve this?[2]
2. Explain the primary difference between a Docker image and a Docker container.[2]
3. Describe the role of the Docker Daemon (dockerd) in the Docker architecture.[2]
4. Why is it often recommended to use user-defined bridge networks instead of the default bridge network for multi-container applications?[2]
5. What is the purpose of Docker Volumes, and why are they important for database containers?[2]
6. How does docker compose up -d differ from docker compose up?[2]
7. Explain the concept of Multi-Stage Docker Builds and their main advantage.[6]
8. When would you use docker pull versus docker push?[1]
9. Briefly describe what CMD and ENTRYPOINT instructions do in a Dockerfile and how they differ.[5]
10. In the context of Docker, what problem does Kubernetes primarily address?[1]

### Quiz Answer Key[1]
- What core problem does Docker aim to solve, and how does it achieve this? Docker addresses the "it works on my machine, but not on yours" problem. It achieves this by packaging an application and all its dependencies (libraries, tools, configurations) into a single, isolated unit called a container, ensuring consistent execution across different environments.[1]
- Explain the primary difference between a Docker image and a Docker container. A Docker image is a read-only template that contains the application and its environment, acting as a blueprint. A Docker container, on the other hand, is a runnable instance of a Docker image, an active process that brings the application to life.[1]
- Describe the role of the Docker Daemon (dockerd) in the Docker architecture. The Docker Daemon is a persistent background process that manages Docker objects such as images, containers, networks, and volumes. It listens for Docker API requests sent from the Docker CLI or other clients and executes the commands to build, run, and manage containers.[1]
- Why is it often recommended to use user-defined bridge networks instead of the default bridge network for multi-container applications? User-defined bridge networks offer better service discovery and isolation. Containers within a user-defined network can communicate with each other by their service names, simplifying configuration, whereas the default bridge requires IP addresses or manual linking.[3]
- What is the purpose of Docker Volumes, and why are they important for database containers? Docker Volumes are used to persist data generated by or used by Docker containers, ensuring data is not lost when a container is removed or restarted. For database containers, this is crucial as it guarantees the integrity and availability of your data, even if the database container itself is replaced.[1]
- How does docker compose up -d differ from docker compose up? docker compose up starts the services defined in docker-compose.yml and runs them in the foreground, showing their logs directly in the terminal. In contrast, docker compose up -d (detached mode) starts the services in the background, allowing the terminal to remain free for other commands.[1]
- Explain the concept of Multi-Stage Docker Builds and their main advantage. Multi-Stage Docker Builds involve using multiple FROM instructions in a Dockerfile, where each FROM represents a new build stage. The main advantage is significantly reducing the final image size by only copying necessary artifacts from earlier build stages into a minimal runtime image, thus improving security and deployment speed.[6]
- When would you use docker pull versus docker push? You would use docker pull <image_name> to download an image from a Docker Registry (like Docker Hub) to a local machine. Conversely, docker push <image_name> uploads a local Docker image to a registry, making it available for others or for deployment across environments.[1]
- Briefly describe what CMD and ENTRYPOINT instructions do in a Dockerfile and how they differ. Both CMD and ENTRYPOINT define the command that runs when a container starts. CMD provides default arguments or a command that can be easily overridden by docker run; ENTRYPOINT sets the main executable and is harder to override, used for the container’s primary process.[5]
- In the context of Docker, what problem does Kubernetes primarily address? Kubernetes primarily addresses orchestrating, managing, and scaling containers in production, offering auto-healing, auto-scaling, and load balancing beyond individual container capabilities.[1]

## Essay Format Questions[7]
- Compare and contrast Docker's containerization with traditional virtualization technologies (Virtual Machines). Discuss the underlying architectural differences, resource efficiency, and typical use cases where one might be preferred over the other.[7]
- Explain the complete lifecycle of a Docker image, starting from writing a Dockerfile, through building the image, and finally running it as a container. Include a discussion on how multi-stage builds contribute to image optimization.[6]
- Design a multi-container application (e.g., a web application with a database) and detail how you would use Docker Compose to define, deploy, and manage it. Your answer should cover service definitions, networking, volume persistence, and dependencies.[1]
- Discuss the various Docker networking options available and explain how you would choose the appropriate network type for different scenarios. Provide specific examples of when you might use a bridge network, a host network, or a user-defined network.[1]
- Explain the importance of Docker Volumes in maintaining data integrity for stateful applications in containerized environments. Describe different types of Docker Volumes and provide a step-by-step example of how to implement data persistence for a MySQL database using a named volume.[1]

## Glossary of Key Terms[1]
- Docker: An open-source platform that enables developers to package applications into containers.[1]
- Container: A lightweight, standalone, executable package of software that includes everything needed to run an application. It's an instance of an image.[1]
- Docker Image: A read-only template with instructions for creating a Docker container.[1]
- Dockerfile: A text file that contains all the commands a user could call on the command line to assemble an image.[4]
- Docker Engine: The client-server application that consists of the Docker daemon, a REST API, and a CLI.[1]
- Docker Daemon (dockerd): The background service running on the host that manages Docker objects (images, containers, networks, volumes).[1]
- Docker CLI: The command-line interface for interacting with the Docker daemon.[1]
- Containerd: A core container runtime that manages the complete container lifecycle.[1]
- Docker Desktop: An application for macOS, Windows, and Linux that provides a simple graphical interface for managing Docker.[1]
- Docker Hub: A cloud-based registry service provided by Docker for finding and sharing container images.[1]
- Docker Compose: A tool for defining and running multi-container Docker applications. It uses YAML files to configure application services.[1]
- Docker Network: A virtual network that allows containers to communicate with each other and with the host machine.[1]
- Bridge Network: The default network for containers that allows them to communicate with each other and the outside world.[1]
- User-Defined Bridge Network: A custom bridge network created by the user for better isolation and service discovery.[3]
- Host Network: A network type where containers share the network namespace of the host machine.[1]
- None Network: A network type where a container is completely isolated from other containers and the host network.[1]
- Docker Volume: A mechanism for persisting data generated by and used by Docker containers.[1]
- Named Volume: A type of Docker volume managed by Docker, providing easy data persistence and backup.[1]
- Bind Mount: A type of Docker volume that maps a specific path on the host filesystem directly into a container.[1]
- Multi-Stage Build: A Dockerfile technique that uses multiple FROM statements to create smaller and more efficient images.[6]
- Dockerfile FROM: Specifies the base image for building.[5]
- Dockerfile WORKDIR: Sets the working directory inside the container.[5]
- Dockerfile COPY: Copies files from the host to the container.[5]
- Dockerfile RUN: Executes commands during the image build process.[5]
- Dockerfile CMD: Provides default command and/or arguments for an executing container.[5]
- Dockerfile ENTRYPOINT: Configures a container that will run as an executable.[5]
- docker pull: Command to download an image from a Docker registry.[1]
- docker push: Command to upload an image to a Docker registry.[1]
- docker tag: Command to create a tag (alias) that refers to an image.[1]
- docker ps: Command to list running containers.[1]
- docker ps -a: Command to list all containers (running and stopped).[1]
- docker exec -it: Command to execute a command inside a running container with an interactive terminal.[1]
- docker logs: Command to fetch the logs of a container.[1]
- Kubernetes: An open-source container orchestration platform for automating deployment, scaling, and management of containerized applications.[1]
- Pod (Kubernetes): The smallest and simplest unit in the Kubernetes object model that you create or deploy. It represents a single instance of a running process in a cluster.[1]
- Deployment (Kubernetes): An object that manages a set of identical Pods, ensuring that a specified number of replicas are running and facilitating rolling updates.[1]
- Service (Kubernetes): An abstraction that defines a logical set of Pods and a policy by which to access them.[1]
- Ingress (Kubernetes): An API object that manages external access to the services in a cluster, typically HTTP/S.[1]
- Docker Scout: A Docker tool for advanced image analysis and vulnerability scanning.[1]
- CVE (Common Vulnerabilities and Exposures): A list of publicly disclosed cybersecurity vulnerabilities.[1]
- Docker Init: A Docker CLI command that helps to create Dockerfiles and Docker Compose files for a project.[1]
- .dockerignore: A file that specifies patterns for files and directories that should be excluded when building a Docker image.[4]
- NotebookLM can be inaccurate; please double-check its responses.[2]

[1](https://docs.github.com/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)
[2](https://stackoverflow.com/questions/11948245/markdown-to-create-pages-and-table-of-contents)
[3](https://www.docstomarkdown.pro/create-a-table-of-contents-in-markdown/)
[4](https://docs.docker.com/contribute/style/formatting/)
[5](https://docs.docker.com/build/building/best-practices/)
[6](https://docs.docker.com/get-started/docker-concepts/building-images/multi-stage-builds/)
[7](https://google.github.io/styleguide/docguide/style.html)
[8](https://gist.github.com/asabaylus/3071099)
[9](https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/organizing-information-with-tables)
[10](https://forums.docker.com/t/how-to-format-your-forum-posts/127295)
[11](https://learn.microsoft.com/en-us/azure/devops/project/wiki/markdown-guidance?view=azure-devops)
[12](https://docs.docker.com/contribute/guides/)
[13](https://www.markdownguide.org/extended-syntax/)
[14](https://www.markdownguide.org/basic-syntax/)
[15](https://github.com/adam-p/markdown-here/wiki/markdown-cheatsheet)
[16](https://handbook.gitlab.com/docs/markdown-guide/)
[17](https://vitepress.dev/guide/markdown)
[18](https://www.markdownguide.org)
[19](https://docs.docker.com/engine/cli/formatting/)