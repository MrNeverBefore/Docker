# [Home](README.md)
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

#


![App Screenshot](https://k21academy.com/wp-content/uploads/2020/11/Docker-and-Vm-blog-image_result-1.webp)


#
## Containers

 It is a bundle of app and libraries requires to run your app.
- Containers share the host OS kernel, are lightweight, fast to start, and use fewer resources.



## Virtual Machine (Vs)

A virtual machine (VM) is a software-based, virtual version of a physical computer that allows you to run an operating system and applications independently from the host computer's actual hardware
- VMs include entire OS, use hypervisors, are heavy on resources.

## Containers Vs VMs

| Feature         | Virtual Machines (VMs)                                | Docker Containers                                    |
|-----------------|------------------------------------------------------|-----------------------------------------------------|
| Architecture    | Hypervisor virtualizes hardware; each VM has a full guest OS | Docker Engine uses host OS kernel; containers are isolated processes |
| OS Footprint    | Full operating system for each VM, including kernel  | Share host OS kernel; contain only minimal OS components |
| Size            | Heavy (GBs)                                          | Lightweight (MBs)                                    |
| Startup Time    | Slower (boots an entire OS)                          | Faster (just starts the application process)        |
| Portability     | Less portable due to OS overhead                     | Highly portable; runs consistently across environments |
| Resource Usage  | Fixed allocation of CPU/memory; less efficient       | Dynamic use of host resources; more efficient        |


# [Home](README.md)
