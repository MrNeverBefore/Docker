

# Docker Projects Documentation[1]

## Table of Contents[1]
- [1. Simple Java Application](#1-simple-java-application)[2]
- [2. Simple Flask Application (Python)](#2-simple-flask-application-python)[2]
- [3. Two-Tier Flask Application with MySQL](#3-two-tier-flask-application-with-mysql)[3]
- [4. Three-Tier Django Notes App with Nginx and MySQL (Docker Compose)](#4-three-tier-django-notes-app-with-nginx-and-mysql-docker-compose)[4]
- [5. Three-Tier Java Expense Tracker with MySQL (Multi-Stage Build)](#5-three-tier-java-expense-tracker-with-mysql-multi-stage-build)[5]
- [6. Docker Init (Project Generation)](#6-docker-init-project-generation)[1]

## 1. Simple Java Application[1]
**Purpose:**  
This project demonstrates how to dockerize a basic Java application that prints a "Hello" message along with the current date. It serves as an introduction to creating a Dockerfile, building an image, and running a container for a Java application.[1]

**Technology Stack:** Java[6]

### Step-by-Step Explanation[6]
- Project Setup: Simple Java project, typically containing Main.java.[6]
- Dockerfile Creation: Defines steps for building the Docker image.[1]
- Base Image Selection: FROM specifies a JDK environment.[1]
- Working Directory: WORKDIR sets the default working directory.[1]
- Code Copying: Main.java is copied into the container.[1]
- Code Compilation: RUN javac compiles the Java file.[1]
- CMD Execution: Defines how the app should run.[1]
- Image Building: docker build -t java-app .[1]
- Run Container: docker run java-app[1]
- Code Changes and Rebuilds: Rebuild required when source changes.[1]

### Dockerfile (Conceptual)[1]
```dockerfile
FROM openjdk:17-jdk-alpine
WORKDIR /app
COPY src/Main.java /app/Main.java
RUN javac /app/Main.java
CMD ["java", "Main"]
```
Explanation: Each instruction forms a layer: FROM, WORKDIR, COPY, RUN, and CMD.[1]

Versions:  
Java Base Image: openjdk:17-jdk-alpine[1]

## 2. Simple Flask Application (Python)[1]
**Purpose:**  
Dockerizing a Python Flask web application, introducing dependencies, port mapping, logging, and detached mode.[1]

**Technology Stack:** Python, Flask[6]

### Step-by-Step Explanation[6]
- Project Setup: Files app.py, run.py, requirements.txt.[6]
- Base Image: python:3.7[6]
- Dockerfile Instructions: Install dependencies, copy code, run python run.py.[1]
- Container Run: Use detached mode and port mapping.[1]
- Access & Debugging: Use docker logs and docker attach.[1]

### Dockerfile (Conceptual)[1]
```dockerfile
FROM python:3.7
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD ["python", "run.py"]
```
### Commands[1]
```sh
docker build -t flask-app .
docker run -d -p 80:80 flask-app
docker logs <container_id>
docker attach <container_id>
```
Versions:  
Python Base Image: python:3.7[6]

## 3. Two-Tier Flask Application with MySQL[3]
**Purpose:**  
Multi-container connectivity (Flask + MySQL) using Docker networks.[3]

**Technology Stack:** Python Flask, MySQL[3]

### Key Concepts[3]
- Problem: Independent containers cannot connect.[3]
- Solution: Custom Docker bridge network.[3]
- Networking: Containers communicate using service names.[1]

### Commands[1]
```sh
docker network create two-tier -d bridge
docker run -d --name mysql --network two-tier -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=devops mysql
docker run -d -p 5000:5000 --network two-tier \
  -e MYSQL_HOST=mysql -e MYSQL_USER=root -e MYSQL_PASSWORD=root -e MYSQL_DATABASE=devops two-tier-backend:latest
docker network inspect two-tier
```
Versions:  
Python Base Image: python:3.8-slim[6]

## 4. Three-Tier Django Notes App with Nginx and MySQL (Docker Compose)[4]
**Purpose:**  
Deploy Django App using Docker Compose (Backend, Nginx, MySQL). Demonstrates networks, healthcheck, volumes.[4]

**Technology Stack:** Django, Nginx, MySQL[4]

### docker-compose.yaml (Conceptual)[4]
```yaml
version: '3.8'

services:
  nginx:
    build: ./nginx
    container_name: nginx_container
    ports:
      - "80:80"
    restart: always
    depends_on:
      django_container:
        condition: service_healthy
    networks:
      - notes_app_network

  django_container:
    build: .
    container_name: django_container
    env_file:
      - .env
    ports:
      - "8000:8000"
    restart: always
    depends_on:
      mysql_db:
        condition: service_healthy
    networks:
      - notes_app_network
    command: >
      sh -c "python manage.py migrate --noinput &&
             gunicorn notes_app.wsgi:application --bind 0.0.0.0:8000"
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/admin"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 30s

  mysql_db:
    image: mysql
    container_name: mysql_db
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: test_db
    volumes:
      - mysql_data:/var/lib/mysql
    restart: always
    networks:
      - notes_app_network
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost", "-u", "root", "-p$$MYSQL_ROOT_PASSWORD"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 60s

volumes:
  mysql_data: {}

networks:
  notes_app_network:
    driver: bridge
```
Versions:  
Python Base Image: python:3.7; Docker Compose File: 3.8[4]

## 5. Three-Tier Java Expense Tracker with MySQL (Multi-Stage Build)[1]
**Purpose:**  
Showcases multi-stage build with Java Spring Boot + MySQL using Docker Compose.[1]

**Technology Stack:** Java (Spring Boot, Thymeleaf), MySQL, Maven[6]

### Multi-Stage Dockerfile[1]
```dockerfile
# Stage 1: Build
FROM maven:3.8.3-openjdk-17 AS builder
WORKDIR /app
COPY . .
RUN mvn clean install -DskipTests=true

# Stage 2: Runtime
FROM openjdk:17-alpine
WORKDIR /app
COPY --from=builder /app/target/expenses-app.jar /app/expenses-app.jar
CMD ["java", "-jar", "expenses-app.jar"]
```
### docker-compose.yaml (Conceptual)[4]
```yaml
version: '3.8'

services:
  java_app:
    build: .
    container_name: expenses_app
    ports:
      - "8080:8080"
    environment:
      SPRING_DATASOURCE_URL: "jdbc:mysql://mysql_db:3306/expenses_tracker?allowPublicKeyRetrieval=true&useSSL=false"
      SPRING_DATASOURCE_USERNAME: root
      SPRING_DATASOURCE_PASSWORD: test@123
      SPRING_APPLICATION_NAME: expenses-app
    depends_on:
      mysql_db:
        condition: service_healthy
    restart: always
    networks:
      - expenses_app_network
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 30s

  mysql_db:
    image: mysql
    container_name: mysql_db
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: test@123
      MYSQL_DATABASE: expenses_tracker
    volumes:
      - mysql_data:/var/lib/mysql
    restart: always
    networks:
      - expenses_app_network
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost", "-u", "root", "-p$$MYSQL_ROOT_PASSWORD"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 60s

volumes:
  mysql_data: {}

networks:
  expenses_app_network:
    driver: bridge
```
Versions:  
Maven: 3.8.3-openjdk-17; Java: openjdk:17-alpine; Docker Compose: 3.8[4]

## 6. Docker Init (Project Generation)[1]
**Purpose:**  
docker init scaffolds a new Docker project automatically.[1]

**Technology Stack:** Docker CLI[1]

### Steps[1]
- Navigate to empty project directory.[1]
- Run docker init.[1]
- Answer prompts (platform, version, app port).[1]
- Docker generates: Dockerfile, docker-compose.yaml, .dockerignore, README.md.[1]

### Example (Go)[5]
- Dockerfile: multi-stage build.[5]
- docker-compose.yaml: app + optional DB.[5]
- .dockerignore: excludes .env, logs, node_modules/.[5]
- README.md: usage instructions.[5]

Versions:  
Varies based on selection (for example, Go 1.13)[5]

[1](https://docs.github.com/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)
[2](https://gist.github.com/asabaylus/3071099)
[3](https://www.docstomarkdown.pro/create-a-table-of-contents-in-markdown/)
[4](https://learn.microsoft.com/en-us/azure/devops/project/wiki/markdown-guidance?view=azure-devops)
[5](https://docs.gitlab.com/user/markdown/)
[6](https://www.markdownguide.org/basic-syntax/)
[7](https://stackoverflow.com/questions/11948245/markdown-to-create-pages-and-table-of-contents)
[8](https://www.markdownguide.org/extended-syntax/)
[9](https://stackoverflow.com/questions/53877184/how-to-link-to-heading-containing-dot-or-comma-in-markdown-file-table-o)
[10](https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/organizing-information-with-tables)