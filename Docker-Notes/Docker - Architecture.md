# [Home](READEME.md)

# Docker Architecture

![App Screenshot](https://docs.docker.com/get-started/images/docker-architecture.webp)

Image Source - https://docs.docker.com




## Components of Docker

### Docker Daemon

-  It runs on Host OS. 
- It is resposible for running Containers. Background service managing containers/images.
- It can communicate with others Daemon.
- Docker Engine lies in Daemon.

### Docker Client

- Communicates with Docker Daemon via REST API.
- When a client run any server command on docker client terminal, the client terminal send the docker command to docker Daemon.
- It can communicate with more than one Daemon.

### Docker Host
- It is used to providing an enviroment to execute and run app.
- It conatines the Docker Daemon, Image, Containers, network and Stroge.


### Docker Hub/registory

Manage and stored the Docker Images.
Example DockerHub, ECR etc.


### Docker Images
It is read-only Binary templates used to create docker Containers.
#### Diffrent ways to Create Images
- Download from Docker Hub or registory.
- Create from Docker file
- Create from existing docker containers.

### Docker Containers

Image becore container when they run on docker engine.


 










#

![App Screenshot](https://miro.medium.com/v2/resize:fit:1400/0*SPCr5zXp8jw9Mfk8.png)










