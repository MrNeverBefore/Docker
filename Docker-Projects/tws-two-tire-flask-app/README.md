# Two-Tier Flask Application with MySQL (using Docker Network)


### Description
 Building and connecting a two-tier application (Flask backend and MySQL database) using custom Docker networks to enable communication between isolated containers.
### Technologies Used
 Python, Flask, MySQL, Docker Network.
### GitHub Repository
 two-tier-flask-app - https://github.com/LondheShubham153/two-tier-flask-app.git

### Commands & Output:
- To clone the project:
```
 git clone <repository_url_for_two_tier_flask_app>

 ```
- To build the Flask backend image:
```
 docker build -t two-tier-backend .
```

 -To create a custom bridge network named two-tier:
 ```
  docker network create --driver bridge two-tier
```

- To run the MySQL container on the two-tier network:

```
 docker run -d --name mysql --network two-tier -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=devops mysql
```


- To run the Flask backend container, also on the two-tier network, and configure it to connect to the mysql container (using its name as hostname):
```
 docker run -d -p 5000:5000 --network two-tier -e MYSQL_HOST=mysql -e MYSQL_USER=root -e MYSQL_PASSWORD=root -e MYSQL_DB=devops two-tier-backend:latest
```

- To inspect the network to see connected containers: 
```
docker network inspect two-tier
```

### Output

 The Flask application, when accessed on port 5000, displays `"Hello dosto, let's make a two-tier application flask and mysql".` Data submitted via the app (e.g., "Hello", "Like Share Subscribe") is successfully stored and retrieved from the MySQL database.

# Details

 This project is a core demonstration of Docker networking. It explicitly shows how containers, by default, are isolated and require a shared network to communicate. Creating a user-defined bridge network allows containers within that network to resolve each other by their names (e.g., mysql as a hostname for the database).
