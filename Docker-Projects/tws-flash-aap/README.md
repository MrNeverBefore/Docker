# Flask Python Application
### Description:
 Deploying a Flask web application within a Docker container, including port mapping and managing container logs.
### Technologies Used: 
Python, Flask (microframework), pip for dependency management.
### Commands & Output:
- To clone the project:
```
 git clone <repository_url_for_flask_app>.
 ```

- To build the Docker image:
```
 docker build -t flask-app .
```

- To run the application in detached mode, mapping host port 80 to container port 80:
```
 docker run -d -p 80:80 flask-app
```

- To check logs of a running container: 
```
docker logs <container_id>
```
- To attach to a running container's output:

```
 docker attach <container_id>
```


### Output:
 The web application displays `"Hey hey welcome to Dev 0"` on the browser at the specified IP address. 
 
 Accessing the /health endpoint shows `"Server is up and running"`. Logs within the container show HTTP request details.


### Dockerfile Details:
```
 FROM python:3.7-slim
 # Uses a lightweight Python 3.7 base image.
 WORKDIR /app
 # Sets the working directory.
 COPY . /app
 # Copies all project files to the container.
 RUN pip install -r requirements.txt
 # Installs Python dependencies.
 CMD ["python", "run.py"] 
# or similar: Specifies the command to run the Flask app.

```
### Details
 This project covers crucial Docker concepts like port mapping (-p), running containers in detached mode (-d), and monitoring container activity through logs and attaching to the container's standard output. It also highlights the importance of security group configuration (firewall rules) on cloud instances like AWS EC2 to allow external access to mapped ports.
