
# Docker Compose

-Docker Compose is a powerful tool designed to simplify the definition and management of multi-container Docker applications.
- Instead of running each container individually, Compose allows you to define an entire application -stack, including multiple services, networks, and volumes, within a single YAML file.

## Key Concepts of Docker Compose ##

 #### 1. Definition of Multi-Container Applications: ####
 Docker Compose enables you to define multiple applications (often broken down into microservices like a frontend, a backend, and a database) and their respective Docker images within a single YAML file. This single file acts as a blueprint for your entire application.

#### 2. Configuration: ####
 The docker-compose.yml file configures all the application's services, networks, and volumes.


#### 3. Simplified Management: ####
 With Docker Compose, you can start, stop, and manage all the services in your application with just a single command.

- The docker compose up command is used to spin up all the containers simultaneously, creating and starting the services defined in your YAML file.
- The docker compose down command will stop and remove the containers, networks, and volumes created by docker compose up.


## Code Example: ##


#### A Multi-Container Web Application ####
Here is an example of a docker-compose.yml file that defines a simple web application consisting of a web server (Nginx), a backend application (Python), and a database (PostgreSQL):

***Note:*** *The 'version' field specifies the Docker Compose file format version. This is a common practice for Docker Compose files, though not explicitly detailed in the provided sources.*
```
version: '3.8'

services:
  web:
    image: nginx:latest # [1, 2]
    ports:
      - "80:80"
    depends_on: # Defines dependency, ensuring 'app' starts before 'web'
      - app
    networks: # [2]
      - app-network

  app:
    build: . # Builds the image from a Dockerfile in the current directory
    ports:
      - "5000:5000"
    environment: # Sets environment variables for the container
      DATABASE_URL: postgres://user:password@db:5432/mydatabase
    depends_on: # Defines dependency, ensuring 'db' starts before 'app'
      - db
    networks: # [2]
      - app-network

  db:
    image: postgres:13 # [1, 2]
    environment: # Sets environment variables for database configuration
      POSTGRES_DB: mydatabase
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    volumes: # [2]
      - db_data:/var/lib/postgresql/data
    networks: # [2]
      - app-network

volumes: # Defines named volumes for data persistence [2]
  db_data:

networks: # Defines custom networks for inter-service communication [2]
  app-network:

  ```


### Bullet Point Notes for the Code Example: ###
 #### 1. version: '3.8': ####
- Specifies the Docker Compose file format version. While not explicitly detailed in the sources, this is a standard and important part of Docker Compose files.

#### 2. services:: ####
 - This top-level key defines the different components or applications that make up your overall multi-container application. Each key under services (e.g., web, app, db) represents a distinct service.

    #### 2.1 **Web:** ####
    - #### image: nginx:latest: ####
        -   Specifies the Docker image to use for this service. In this case, it uses the official Nginx web server image.
   - ####  ports: "80:80": ####
        - Maps port 80 on your host machine to port 80 inside the web container. This makes your web server accessible from outside the Docker environment.
   - #### depends_on: - app: ####
     - Declares a dependency on the app service, meaning Docker Compose will ensure the app service is started before the web service.
   - ####  networks: - app-network: ####
        - Connects this service to a user-defined network named app-network, allowing it to communicate with other services on the same network.

    #### 2.2. **app service:** ####

    - ####  build: .: ####
        -  Instructs Docker Compose to build a Docker image for this service using a Dockerfile located in the current directory (.) where the docker-compose.yml file resides.
   - #### ports: "5000:5000": ####
     - Maps port 5000 on your host to port 5000 inside the app container.
   - #### 2.7.  environment:: ####
        - Allows you to set environment variables that will be available inside the app container, often used for configuration like database connection strings.
    - #### depends_on: - db: ####
        - Declares a dependency on the db service, ensuring the database is running before the application tries to connect to it.
    - #### networks:- app-network: ####
        - Connects this service to the app-network.
     #### 2.3  **db service:** ####
    - #### image: postgres:13: ####
        - Specifies the PostgreSQL database image, version 13.
   - #### environment:: ####
        - Sets environment variables specific to PostgreSQL for database initialization, such as database name, user, and password.
   - #### volumes: - db_data:/var/lib/postgresql/data: ####
        - This is crucial for data persistence. It mounts a named volume called db_data (defined globally below) to the /var/lib/postgresql/data directory inside the PostgreSQL container. This ensures that your database data is preserved even if the db container is removed or restarted.
   - #### networks: - app-network: ####
        - Connects this service to the app-network.


#### 3. volumes:: ####
 - This top-level key defines named volumes, such as db_data. These volumes are managed by Docker and are used to persist data generated by containers, making them independent of the container's lifecycle.

#### 4. networks::  ####

- This top-level key defines custom networks. app-network in this example provides an isolated network for services to communicate securely with each other.


# END