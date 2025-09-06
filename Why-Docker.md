
# Why Docker ?

Before understang the docker we need to uderstnd how the software industry worked before Docker and how it changed after Docker:

 


## Before Docker

- Applications installed directly on physical servers or full - virtual machines (VMs). 
- Developers faced "works on my machine" issues due to environment - inconsistencies.
- Manual setup of dependencies, libraries, and runtime environments.
- VM-based deployment was resource-heavy and slow to start (minutes)- .
- Configuration management tools helped but added complexity.

- Deployment was error-prone, time-consuming, and lacked - portability.

- Scaling and environment replication were challenging and inefficient.




## After Docker

- Applications run inside lightweight containers sharing the host OS kernel.

- Containers package app + all dependencies, ensuring consistent environments.

- Containers start quickly (seconds), saving resources compared to VMs.

- Simplifies deployment with portable, reproducible container images.

- Enables microservices and DevOps practices with CI/CD pipeline integration.

- Makes scaling and environment replication fast and easy across platforms.

- Large ecosystem with Docker Hub, Docker Compose, and orchestration tools.



## Containers

 It is a bundle of app and libraries requires to run your app.
- Containers share the host OS kernel, are lightweight, fast to start, and use fewer resources.



## Virtual Machine (Vs)

A virtual machine (VM) is a software-based, virtual version of a physical computer that allows you to run an operating system and applications independently from the host computer's actual hardware
- VMs include entire OS, use hypervisors, are heavy on resources.

## Containers Vs VMs

![App Screenshot](https://www.netapp.com/media/Screen-Shot-2018-03-20-at-9.24.09-AM_tcm19-56643.png)









