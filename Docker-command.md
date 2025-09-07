# Docker Commands

- Docker provides a wide array of commands, from simple instructions within a Dockerfile to complex commands for managing containers, images, networks, and volumes via the Command Line Interface (CLI). 
- These commands facilitate the entire application development lifecycle, from initial build to deployment and debugging.

Here is a comprehensive list of important Docker commands, categorised for clarity and presented from basic to advanced operations.

### Dockerfile Instructions (Building Images)

These commands are used inside a `Dockerfile` to create a Docker image, acting as a blueprint for your application's environment.

*   **`FROM <image>[:<tag>]`**
    *   **Purpose**: Specifies the **base image** from which your image will be built. It is typically the first instruction in a Dockerfile.
    *   **Note**: This could be an operating system (e.g., `ubuntu:latest`) or a language runtime.
*   **`WORKDIR /path/to/directory`**
    *   **Purpose**: Sets the **working directory** for any subsequent `RUN`, `CMD`, `ENTRYPOINT`, `COPY`, or `ADD` instructions.
    *   **Note**: If the directory does not exist, Docker will create it.
*   **`RUN <command>`**
    *   **Purpose**: Executes commands **during the image build process**.
    *   **Note**: Commonly used for installing dependencies (`apt-get install`, `pip install`), compiling code, or setting up the environment.
*   **`COPY <src> <dest>`**
    *   **Purpose**: **Copies files or directories from your local host machine into the image**.
    *   **Note**: For multi-stage builds, `COPY --from=<stage_name> <src> <dest>` is used to copy artefacts from a previous build stage.
*   **`ENV <key>=<value>`**
    *   **Purpose**: Sets **environment variables** inside the image, which persist when the container runs.
*   **`EXPOSE <port>`**
    *   **Purpose**: Informs Docker that the container will listen on the specified network ports at runtime.
    *   **Note**: This instruction is informational; it does not actually publish the port. Port publishing needs to be done with the `docker run -p` command.
*   **`CMD ["executable", "param1", "param2"]`** (preferred form)
    *   **Purpose**: Provides **default arguments for an `ENTRYPOINT` instruction** or specifies the **command to be executed when a container starts**.
    *   **Note**: There can only be one `CMD` instruction per Dockerfile. If multiple `CMD`s are present, only the last one takes effect. It can be overridden when running a container.
*   **`ENTRYPOINT ["executable", "param1", "param2"]`** (preferred form)
    *   **Purpose**: Configures a container to run as an **executable**.
    *   **Note**: Similar to `CMD`, but the `ENTRYPOINT` command and its parameters are generally **not overridden** when running a container; arguments passed via `docker run` are appended to it.
*   **`USER <username | UID>`**
    *   **Purpose**: Sets the **user name or UID** to use when running the image and for any subsequent `RUN`, `CMD`, and `ENTRYPOINT` instructions.
    *   **Note**: Used for better security practices by running processes as a non-root user.
*   **`LABEL <key>=<value> ...`**
    *   **Purpose**: Adds **metadata** to an image.
*   **`HEALTHCHECK [OPTIONS] CMD command`**
    *   **Purpose**: Tells Docker how to test a container to check if it's still working properly.
*   **`VOLUME ["/data"]`**
    *   **Purpose**: Creates a **mount point** with the specified name and marks it as holding externally mounted volumes from the native host or other containers.
    *   **Note**: Used for persistent data storage.

### Docker Installation and Setup

*   **`sudo apt update`**
    *   **Purpose**: Updates the package lists for upgrades and new package installations on Ubuntu-based systems.
*   **`sudo apt install docker.io -y`**
    *   **Purpose**: Installs the Docker Engine on Ubuntu-based systems. The `-y` flag automatically answers 'yes' to prompts.
*   **`sudo systemctl status docker`**
    *   **Purpose**: Checks the status of the Docker service to verify if it is running and active.
*   **`sudo usermod -aG docker <user>`**
    *   **Purpose**: Adds the specified user to the `docker` group, granting them permissions to run Docker commands without `sudo`.
    *   **Note**: The user needs to log out and log back in for the changes to take effect.
*   **`docker desktop`**
    *   **Purpose**: A desktop application for Windows, macOS, and Linux that includes Docker Engine, Docker CLI, Docker Compose, Kubernetes, and other tools, providing an easy way to develop with Docker on a personal machine. This directly provides a virtual machine and the Docker stack without manual setup.

### Basic Docker CLI Commands (Image & Container Management)

These commands are used to interact with the Docker Engine from your terminal for fundamental operations:

*   **`docker help`**
    *   **Purpose**: Displays a list of all available Docker CLI commands and their descriptions.
*   **`docker build -t <tag_name> .`**
    *   **Purpose**: **Builds a Docker image from a Dockerfile** located in the current directory (`.`).
    *   **Note**: The `-t` flag tags the image with a recognisable name and optional version (e.g., `myrepo/myapp:latest`). Docker builds images in layers, leveraging caching for efficiency.
*   **`docker images`**
    *   **Purpose**: Lists **all Docker images** stored on your local machine.
*   **`docker run [OPTIONS] <image_name> [COMMAND] [ARG...]`**
    *   **Purpose**: **Starts a new container from a specified image**.
    *   **Common Options**:
        *   **`-it`**: Runs the container in **interactive mode** with a pseudo-TTY, allowing you to interact with the container's shell.
        *   **`-p <host_port>:<container_port>`**: **Publishes (maps) a container's port to a port on the host machine**, making the application accessible externally.
        *   **`-d`**: Runs the container in **detached mode** (in the background).
        *   **`--name <container_name>`**: Assigns a **custom name** to the container for easier reference.
*   **`docker ps`**
    *   **Purpose**: Lists **all currently running containers**.
    *   **Note**: Use `docker ps -a` (or `--all`) to list all containers, including stopped ones.
*   **`docker stop <container_name | container_id>`**
    *   **Purpose**: **Gracefully stops** one or more running containers. Docker sends a `SIGTERM` signal, allowing the container to shut down cleanly.
*   **`docker kill <container_name | container_id>`**
    *   **Purpose**: **Forcefully stops** one or more running containers. Docker sends a `SIGKILL` signal, immediately terminating the container.
*   **`docker rm <container_name | container_id>`**
    *   **Purpose**: **Removes** one or more stopped containers.

### Advanced Docker CLI Commands

These commands enable more complex management, inspection, and debugging:

*   **`docker exec -it <container_name | container_id> <command>`**
    *   **Purpose**: **Executes a command inside a running container**.
    *   **Note**: Often used with `-it` to open an interactive shell (e.g., `/bin/bash` or `/bin/sh`) inside the container for debugging or inspection.
*   **`docker inspect <object_name | object_id>`**
    *   **Purpose**: Returns **detailed low-level information** on Docker objects (containers, images, networks, volumes) in JSON format.
    *   **Note**: Extremely useful for debugging, checking IP addresses, mounted volumes, port bindings, and configuration.
*   **`docker start <container_name | container_id>`**
    *   **Purpose**: Starts one or more stopped containers.
*   **`docker restart <container_name | container_id>`**
    *   **Purpose**: Restarts one or more running containers.
*   **`docker pause <container_name | container_id>`**
    *   **Purpose**: Pauses all processes within one or more running containers.
*   **`docker unpause <container_name | container_id>`**
    *   **Purpose**: Unpauses all processes within one or more paused containers.
*   **`docker stats`**
    *   **Purpose**: Displays a live stream of **container resource usage statistics** (CPU, memory, network I/O, disk I/O).
*   **`docker login`**
    *   **Purpose**: Logs you into **Docker Hub** or another Docker registry. This is necessary before pushing images to a remote repository.
*   **`docker push <image_name>:<tag>`**
    *   **Purpose**: **Uploads a local Docker image** to a remote registry like Docker Hub.
*   **`docker pull <image_name>:<tag>`**
    *   **Purpose**: **Downloads a Docker image** from a remote registry to your local machine.

### Docker Compose Commands (Multi-container Applications)

Docker Compose is a tool for defining and running multi-container Docker applications using a `docker-compose.yml` file.

*   **`docker compose up`**
    *   **Purpose**: Builds, (re)creates, starts, and attaches to containers for all services defined in a `docker-compose.yml` file.
*   **`docker compose down`**
    *   **Purpose**: Stops and removes containers, networks, and volumes created by `compose up`.

### Docker Networking Commands

These commands manage how Docker containers communicate with each other and the host system.

*   **`docker network ls`**
    *   **Purpose**: Lists **all Docker networks** on the host.
    *   **Note**: Includes default networks (e.g., `bridge`, `host`, `none`) and any custom networks you've created.
*   **`docker network create <network_name>`**
    *   **Purpose**: **Creates a custom network**, which by default is a `bridge` network.
    *   **Note**: Custom bridge networks provide logical isolation between containers.
*   **`docker network rm <network_name>`**
    *   **Purpose**: **Removes** one or more Docker networks.
*   **`docker run --network <network_name> <image_name>`**
    *   **Purpose**: Attaches a container to a specific network during creation.
    *   **Note**: This allows containers on the same custom network to communicate by name and provides isolation from containers on other networks.
*   **`ping <IP_address_or_container_name>`**
    *   **Purpose**: Used inside a container (via `docker exec`) to test network connectivity to other containers or the host.

### Docker Volume Commands (Persistent Data Storage)

These commands manage persistent data storage for Docker containers, addressing the ephemeral nature of containers.

*   **`docker volume ls`**
    *   **Purpose**: Lists **all Docker volumes**.
*   **`docker volume create <volume_name>`**
    *   **Purpose**: **Creates a Docker volume**.
    *   **Note**: Volumes are Docker-managed logical partitions on the host, offering better lifecycle management than bind mounts for persistent data.
*   **`docker volume inspect <volume_name>`**
    *   **Purpose**: Displays **detailed information** about a specific Docker volume, including its mount point on the host filesystem.
*   **`docker volume rm <volume_name>`**
    *   **Purpose**: **Removes** one or more Docker volumes.
    *   **Note**: A volume cannot be removed if it is currently in use by a container. You must first stop and remove any containers using the volume.
*   **`docker run --mount source=<volume_name>,target=/path/in/container <image_name>`**
    *   **Purpose**: Mounts a named volume to a container at a specified target path. This is the more verbose and recommended way to mount volumes.
*   **`docker run -v <volume_name>:/path/in/container <image_name>`**
    *   **Purpose**: An alternative, shorter syntax to mount a named volume. Both `-v` and `--mount` achieve the same result.

This structured list covers the essential Docker commands for managing images, containers, networks, and persistent data, from basic operations to more advanced scenarios like multi-stage builds and networking isolation.