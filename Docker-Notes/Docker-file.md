# [Home](README.md)
# DockerFile


A Dockerfile acts as a blueprint, containing a collection of instructions that tell Docker how to configure the environment to run your application. 

It is used to build a Docker image, which then runs as an isolated container.

## Key Dockerfile Instructions
*Instructions in a Dockerfile are conventionally written in all caps.*
### 1. FROM:
- This is usually the first instruction in a Dockerfile.
- It specifies the base image to start from, often a Linux distribution (e.g., Ubuntu latest, Node:20).
- An optional image tag, separated by a colon, specifies the version of the OS.
- A multi-stage build will have multiple ***FROM*** statements, splitting the Dockerfile into different stages (e.g., one for building, one for running).

### 2. WORKDIR:
- Creates a source directory and changes into it. All subsequent commands will be executed from this working directory.
- It helps standardise where your source code is placed within the container.


### 3. RUN:
- Allows you to execute any command, just as you would from the command line.
- Commonly used to install dependencies using a Linux package manager (e.g., apt update, apt install python).
### 4. USER:
- Used to create a non-root user for better security, as running as the root user by default can be a drawback.
### 5. COPY:
- Copies code from your local machine to the image.
- In multi-stage builds, it can copy artefacts from one build stage to another using COPY --from <stage_name>.
- It's a best practice to copy dependency files (like requirements.txt) before the main source code to leverage Docker's layering for caching efficiency.
### 6. ENV:
- Sets environment variables within the container. Useful for things like API keys or configuration settings.
### 7. EXPOSE:
- Declares the port that the web server or application inside the container will listen on for external traffic. Note that this only documents the port; actual port mapping is done with the `docker run -p` command.
### 8. CMD:
- The command you want to run when starting a container.
- There can only be one CMD instruction per container.
- CMD is configurable/overrideable by arguments passed during docker run. It's suitable for arguments or default commands that users might want to change.
### 9. ENTRYPOINT:
- Allows you to pass arguments to the CMD when the container starts.
- ENTRYPOINT is not overrideable by default when running the container; it defines the main executable. It's best for the core executable of your application (e.g., python3, java).
### 10. LABEL:
- Adds extra metadata to the image.
### 11. HEALTHCHECK:
- Used to ensure the container is running properly.
### 12. VOLUME:
- Used if the container needs to store data persistently or share data among multiple containers. Volumes ensure data is preserved even if the container is shut down or deleted. 


#


## Dockerfile Workflow and Best Practices

 #### Dockerfile to Image to Container: 
 - The process involves writing a Dockerfile, using docker build to create an image, and then docker run to launch a container from that image.
#### Tagging Images: 
- Always use the -t flag with docker build to tag your image with a recognizable name and version (e.g., username/repository:tag). This makes it easier to manage and share images.
#### Docker Ignore: 
- Create a .dockerignore file to exclude unnecessary files from being copied into the Docker image, preventing them from ending up in the final image.
#### Layer Caching:
-  Docker builds images in layers. If you modify your Dockerfile, only the changed layers are rebuilt, making the workflow more efficient due to caching.
#### Multi-stage Builds:
- This concept involves splitting your Dockerfile into multiple stages to reduce the final image size and improve security.
- One stage focuses on building the application (using a "rich" base image with all necessary build tools), and a final stage focuses on running the application (using a minimal base image, only copying the essential binary or artefact from the build stage).
- This significantly reduces the size of the final image (e.g., from 861MB to 1.83MB for a Go application).
#### Distroless Images:
- These are extremely minimalistic Docker images that contain only the necessary runtime environments and very few, if any, packages or shell commands.
- They offer the highest security by drastically reducing the attack surface, as they are "99 percent of times not exposed to vulnerabilities".
- Examples include scratch (for statically typed languages like Go that don't need a runtime) or specific language distroless images (e.g., Python, Java)



## Examples

```
# Use an official Ubuntu image as the base image for this stage.
FROM Ubuntu:latest [1]

# Set the working directory inside the container to /app.
# All subsequent commands will execute from this directory.
WORKDIR /app [2]

# Copy the requirements.txt file first to leverage Docker's build cache.
# This ensures that if only the application code changes, the dependencies
# don't need to be reinstalled every time.
COPY requirements.txt . [2]

# Update the package list and install Python 3 and pip.
RUN apt update && apt install -y python3 python3-pip [3]

# Copy all the application source code from the current directory
# on the host to the working directory (/app) in the image.
COPY . . [3]

# Install the Python dependencies listed in requirements.txt.
RUN pip install -r requirements.txt [4]

# Expose port 8000, indicating that the web server inside the container
# will listen on this port for incoming connections.
EXPOSE 8000 [5]

# Define the entry point for the container. This sets the primary executable
# that will run when the container starts. It is generally not meant to be overridden.
ENTRYPOINT ["python3"] [4, 6]

# Define the default command to execute when the container starts.
# This command acts as arguments to the ENTRYPOINT and can be easily overridden
# when running the container.
CMD ["manage.py", "runserver", "0.0.0.0:8000"] [4, 7]

```

### Explanation

**`FROM Ubuntu:latest:`** This instruction specifies the base image for the build. It starts with the latest Ubuntu operating system image, which is a rich base image that makes installing packages easy.\
\
**`WORKDIR /app:`** This command creates a directory named /app inside the image and sets it as the current working directory. All subsequent instructions (like COPY, RUN, CMD) will be executed relative to this directory. This standardises where the source code is placed within the container.\
\
**`COPY requirements.txt .:`** This copies the requirements.txt file from your local machine (the build context) into the /app directory within the image. It's a best practice to copy dependency files early to utilise Docker's layer caching. If requirements.txt doesn't change, this layer is cached and doesn't need to be rebuilt.\
\
**`RUN apt update && apt install -y python3 python3-pip:`** The RUN instruction executes commands in a new layer on top of the current image. Here, it updates the package list for Ubuntu and then installs python3 and python3-pip, which are necessary for a Python application.\
\
**`COPY . .`  :** This copies all files and folders from the current directory on your local machine (where the Dockerfile is located) into the /app directory inside the image. This includes your Django application's source code.

**`RUN pip install -r requirements.txt:`** This command uses pip to install all the Python libraries specified in the requirements.txt file. The requirements.txt would typically list dependencies like Django and TCdata.

**`EXPOSE 8000:`** This instruction informs Docker that the container will listen on port 8000 at runtime. It acts as documentation, and actual port mapping to the host system is done when running the container (e.g., docker run -p 8000:8000).


**`ENTRYPOINT ["python3"]`** The ENTRYPOINT configures a container that will run as an executable. It specifies the command that will always be executed when the container starts. It is not easily overridden by arguments given during docker run. In this case, python3 is set as the main executable.

**`CMD ["manage.py", "runserver", "0.0.0.0:8000"]:`** The CMD instruction provides default arguments for the ENTRYPOINT. If the user specifies arguments to docker run, the CMD arguments are overridden. This allows for flexibility; for instance, a user could change the server binding or port. The 0.0.0.0:8000 ensures the Django development server listens on all available network interfaces on port 8000 within the container.
Accompanying Files
For this Dockerfile to function correctly, the following files and structure are expected in the same directory as the Dockerfile:

**`requirements.txt:`** This file lists the Python packages required by the Django application. An example content would be:

**`Django Application Source Code:`** The COPY . . instruction assumes your Django project structure is in the current directory, which typically includes:
- manage.py: Django's command-line utility for administrative tasks.
- Your main project directory (e.g., devops/), containing settings.py (for project configuration), urls.py, etc..
- Your application directories (e.g., demo/), containing views.py (where your Python functions that process web requests are written).
- A templates/ folder (for HTML files rendered by Django).


***This Dockerfile is a basic example that can be further optimised, for instance, by using multi-stage builds or distroless images to reduce the final image size and enhance security***

# [Home](README.md)
