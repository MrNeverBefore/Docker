# [Home](READEME.md)
# Docker Networks

- Docker networking allows containers to communicate with each other and with the host system.
- This is crucial because containers, being lightweight and not having a complete operating system, need to interact with the host and potentially other containers to function effectively.

## Here are key aspects of Docker Networking:
### Purpose and Problems Solved:
- **Container Communication:** Networking enables one container to communicate with another, which is essential for multi-service applications *(e.g., a front-end container talking to a back-end container)*.
- **Isolation:** Docker networking provides solutions for creating logical isolation between containers, preventing unauthorised access between different application components *(e.g., a login container being isolated from a payments container holding sensitive information).*
- **External Access:** It allows applications running inside containers to be reachable from the internet or external clients.
- **Simplified Deployment:** Docker networking simplifies the deployment process by managing network configurations for containerised applications.

### How Containers Talk to the Host (Default Bridge Network):
- By default, when you create a container, Docker automatically sets up a Bridge Network.
- This bridge acts as a virtual Ethernet (veth), often called docker0, which connects the container's network interface (e.g., eth0) to the host's network.
- This virtual bridge allows containers to communicate with the host, even if they are on different subnets.
- Without this default bridge network, applications inside containers would not be reachable from the internet, making container usage pointless.


## Types of Docker Networks:

![App Screenshot](https://i.ytimg.com/vi/fBRgw5dyBd4/maxresdefault.jpg)

#### Docker provides several network drivers, each suited for different use cases: ####

#### 1. Bridge Network: ####
- ***Default:*** As mentioned, it's the default network type for containers that are not explicitly assigned to another network.
- ***Communication:*** Allows containers on the same host to communicate with each other and with the host system.
- ***Custom Bridge Networks:*** Docker also allows you to create custom bridge networks. These are useful for:
- ***ogical Isolation:*** By assigning different containers to different custom bridge networks, you can achieve logical network isolation between them. This breaks the common communication path that the default docker0 bridge would otherwise provide.
- ***Service Discovery:*** Containers within the same custom bridge network can communicate using their names, not just IP addresses, simplifying service discovery for multi-container applications [Information not directly in sources but implied by networking capabilities].
#### 2. Host Network: ####
- ***Shared Stack:*** Containers using the host network directly share the network stack of the host machine.
- ***No Isolation:*** This means the container does not get its own IP address but uses the host's IP address and ports directly.
- ***Insecure:*** While it can offer high performance, it is considered the most insecure way to create networking in Docker because it removes the isolation barrier between the container and the host's network.

#### 3. Overlay Network: ####
- ***Multi-Host Communication:*** Overlay networks are designed for distributed networks, allowing containers running on different Docker hosts to communicate seamlessly as if they were on the same host.
- ***Orchestration Tools:*** They are commonly used with container orchestration tools like Kubernetes or Docker Swarm to manage intricate deployments across multiple machines.
- ***Complexity:*** Overlay networking is more complicated to set up and is usually not necessary for single-host Docker deployments.
## Code Example (Practical Demonstration): ##

#### Let's illustrate Docker networking with commands that demonstrate listing networks, inter-container communication on default bridge, creating a custom bridge for isolation, and using host networking. ####

### 1. List Existing Docker Networks: ###

- You can see the default networks Docker provides out-of-the-box: `bridge, host, and none.`
```
docker network ls

```

***Notes: This command lists all Docker networks on your system. You'll typically see bridge, host, and none as default networks. The bridge network is the default for new containers unless otherwise specified.***

#


### 2. Demonstrate Container-to-Container Communication (Default Bridge Network): ###

- First, run two simple Nginx containers without specifying a network. They will automatically be attached to the default bridge network.
```
# Run the first container (login application)
docker run -d --name login nginx:latest

# Run the second container (logout application)
docker run -d --name logout nginx:latest
```
***Notes:*** *`-d` runs the container in detached mode (background). `--name` assigns a recognisable name.
Now, find their IP addresses and attempt to ping from one to another.*

```
# Get the IP address of the 'logout' container
LOGOUT_IP=$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' logout)
echo "Logout container IP: $LOGOUT_IP"

```

```
# Log into the 'login' container and ping the 'logout' container
docker exec -it login bash -c "apt-get update && apt-get install -y iputils-ping && ping -c 3 $LOGOUT_IP"

```
***Notes:*** *`docker inspect` provides detailed information about a container, including its network settings. The -f flag is used to format the output to extract the IP address. 

`docker exec -it` allows you to run a command inside a running container interactively.
-  We install `iputils-ping` because ping is often not available by default in minimal container images.*

***Observation:*** You should see successful pings, indicating that containers on the default bridge network can communicate with each other.


### 3. Demonstrate Network Isolation (Custom Bridge Network): ###
 Create a new custom bridge network and then run a "finance" container on this new network.
 ```
# Create a custom bridge network
docker network create secure-network

```
***Notes:*** 
- `docker network` create is used to create a new network.
- By default, it creates a bridge network.

Now, run a "finance" container and explicitly attach it to secure-network.
```
# Run a 'finance' container on the custom secure-network
docker run -d --name finance --network secure-network nginx:latest

```
***Notes:***
-  --network flag specifies which network the container should attach to.


Now, try to ping the "finance" container from the "login" container (which is on the default bridge network).
```
# Get the IP address of the 'finance' container
FINANCE_IP=$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' finance)
echo "Finance container IP: $FINANCE_IP"
```
```
# Log into the 'login' container and try to ping the 'finance' container
docker exec -it login ping -c 3 $FINANCE_IP
```

#### Observation: ####
The ping attempts should fail because the login container and finance container are on different, isolated bridge networks. This demonstrates logical network isolation.


### 4. Demonstrate Host Networking: ###
 Run a container using the host's network.
 ```
# Run a container using the host network
docker run -d --name host-demo --network host nginx:latest
```
***Notes:***
-  --network host attaches the container directly to the host's network stack.
```
# Inspect this container to see its network configuration.
docker inspect host-demo
```

#### Observation: ####
- You will notice that the host-demo container does not have its own unique IP address within a Docker subnet. 
- Instead, it uses the host's IP address and network interfaces directly. 
- This type of networking is generally less secure due to the lack of isolation.


### Cleanup:  ###
To remove the containers and networks created: 
```
docker stop login logout finance host-demo
docker rm login logout finance host-demo
docker network rm secure-network
```


## Screenshots

![App Screenshot](https://via.placeholder.com/468x300?text=App+Screenshot+Here)

