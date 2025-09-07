# [Home](READEME.md)



# Docker Workflow

The process of using Docker is typically broken down into three easy steps



![App Screenshot](Image/docker-workflow.png)

Image Source -www.researchgate.ne


## 1. Dockerfile: The Blueprint

 A Dockerfile is a text file containing a set of instructions (like a blueprint) that tells Docker how to build an image for your application

#### Common Instructions:
- **FROM:** *Specifies the base image to start from (e.g., Ubuntu latest, python:3). It's usually the first instruction.*
- **WORKDIR:** *Sets the working directory for any subsequent RUN, CMD, ENTRYPOINT, COPY, or ADD instructions.*

- **RUN:** *Executes commands inside the image during the build process. Used for installing dependencies, compiling code, etc..*

- **COPY:** *Copies files or directories from your local machine to the image.*

- **ENV:** *Sets environment variables (e.g., API keys).*

- **EXPOSE:** *Informs Docker that the container listens on the specified network ports at runtime. This doesn't actually publish the port; it's a documentation/declaration.*

- **CMD:** *Provides defaults for an executing container. It's the command run when a container starts from the image. There can only be one CMD per Dockerfile.*

- **ENTRYPOINT:** *Configures a container to run as an executable. It's often used with CMD to provide default arguments to the entrypoint command. The ENTRYPOINT is typically non-overrideable, while CMD provides configurable arguments*


## 2. Docker Image: The Template

- An image is a read-only template containing the operating system, dependencies, and your application code. 
- It's essentially a snapshot of a container at a specific point in time.

**Building an Image:**  You use the `docker build` command to create an image from a Dockerfile.
\
\
**Layers:** Images are built in layers. Each instruction in a Dockerfile creates a new layer. Docker caches these layers, so if you modify your Dockerfile, it only rebuilds the layers that have actually changed, making the workflow efficient.
\
\
**.dockerignore file:**  Similar to *.gitignore*, this file specifies files and directories to exclude from the image, helping to keep image sizes smaller.
- Example Image Build: To build the image:
\
\
***(Note: -t tags the image with a name and optional version. . indicates the Dockerfile is in the current directory)***

• Viewing Images: You can see your built images using 
```
 docker images
```


## 3. Docker Container: The Running Application

A container is a runnable instance of a Docker image. It's the isolated package executing your application code.


**Statelessness & Portability:** Containers are typically stateless, meaning any data written directly inside the container filesystem is lost when it shuts down. This makes them highly portable, able to run on any major cloud platform without vendor lock-in.


**Running a Container:** You use the `docker run` command to start a new container from a specified image.
- *For web applications, you typically need to map ports from the container to the host using the -p flag:*
- *This maps container port 8000 to host port 8000*









