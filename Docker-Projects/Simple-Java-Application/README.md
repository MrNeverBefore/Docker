## 1. Simple Java Application

**Purpose:**  
This project demonstrates how to dockerize a basic Java application that prints a "Hello" message along with the current date. It serves as an introduction to creating a Dockerfile, building an image, and running a container for a Java application.

**Technology Stack:** Java

### Step-by-Step Explanation
- **Project Setup:** Simple Java project, typically containing `Main.java`.  
- **Dockerfile Creation:** Defines steps for building the Docker image.  
- **Base Image Selection:** `FROM` specifies a JDK environment.  
- **Working Directory:** `WORKDIR` sets the default working directory.  
- **Code Copying:** `Main.java` is copied into the container.  
- **Code Compilation:** `RUN javac` compiles the Java file.  
- **CMD Execution:** Defines how the app should run.  
- **Image Building:** `docker build -t java-app .`  
- **Run Container:** `docker run java-app`  
- **Code Changes and Rebuilds:** Rebuild required when source changes.  

### Dockerfile (Conceptual)
```dockerfile
FROM openjdk:17-jdk-alpine
WORKDIR /app
COPY src/Main.java /app/Main.java
RUN javac /app/Main.java
CMD ["java", "Main"]
```

**Explanation:**  
Each instruction forms a layer: `FROM`, `WORKDIR`, `COPY`, `RUN`, and `CMD`.

**Versions:**  
Java Base Image: `openjdk:17-jdk-alpine`

***
