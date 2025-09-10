# MySQL Database Container

### Description: 
 Running a MySQL database instance within a Docker container to show how pre-built images for databases can be quickly deployed.
### Technologies Used:
 MySQL database.

### Commands & Output:

  - To pull the MySQL image: 
```
docker pull mysql
```
-   To run MySQL with a root password (e.g., 'root'):

```
 docker run -e MYSQL_ROOT_PASSWORD=root mysql
 ```
### Output: 
The terminal shows messages indicating the MySQL server starting, such as
```
 "IDDB initialization has ended" and "MySQL Community Server... Ready for startup".
 ```
- To run MySQL in detached mode (background): 
```
docker run -d -e MYSQL_ROOT_PASSWORD=root mysql.
```

- To access the MySQL container's shell (e.g., for troubleshooting or interaction):
```
 docker exec -it <container_id> bash
 ```
- Inside the container, to log in to MySQL:
```
 mysql -u root -p root.
 ```
 
- To view databases:
```
 show databases;
 ```
- To create a database: 
```
create database devops;
```
- To use the database:
```
 use devops;
 ```
### Details:
 This project demonstrates running a database, managing container processes (blocking vs. detached mode), and directly interacting with the service inside the container


 
