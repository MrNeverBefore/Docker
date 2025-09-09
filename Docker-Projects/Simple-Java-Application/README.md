# Simple Java Application
### Description:
 Building a Docker image and running a container for a basic Java application, highlighting the process of creating a Dockerfile from scratch.
### Technologies Used: 
Java.
### GitHub Repository: 
simple-java-docker.

**Github Repo Url** : https://github.com/LondheShubham153/simple-java-docker.git

### Commands & Output:
- To clone the project: 
```
git clone <repository_url_for_simple_java_docker>.
```
- To build the Docker image with a tag java-app:
```
 docker build -t java-app .
 ```
- To run the Java application in a container:
```
 docker run java-app.
```

### Output:
- Initially, "Hello Docker Current Date is Wednesday November 6th...".
- After modifying the code and rebuilding the image, the output changes to, "Hello dosto, subscribe kar lo. Current Date is 6 November...".

- #### Dockerfile Details: ####
```
FROM openjdk:17-alpine
WORKDIR /app
COPY src/Main.java /app/Main.java
RUN javac /app/Main.java
CMD ["java", "Main"]
 ```

 ### Explanation of Docker File
- `FROM openjdk:17-alpine` Uses a lightweight Alpine-based OpenJDK 17 image as the base.
- `WORKDIR /app: Sets /app` as the working directory inside the container.
- `COPY src/Main.java /app/Main.java`: Copies the Main.java source file from the host's src directory to /app/Main.java in the container.
- `RUN javac /app/Main.java`: Compiles the Java source code.
- `CMD ["java", "Main"]`: Specifies the command to run the compiled Java application when the container starts.

### Details
 This project explains how to write a Dockerfile, compile and run Java code within a container, and demonstrates that changes in the source code require rebuilding the Docker image for the changes to take effect.
