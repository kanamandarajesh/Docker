## docker-compose installation commands
1. sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
2. sudo chmod +x /usr/local/bin/docker-compose
3. docker-compose --version
4. sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

## docker service/stack commands

### below command is for deploying service

  docker stack deploy -c docker-compose.yml my-stack

### below command is for scaling the services

 docker service scale my-stack_web=5

### below command is for deleting network permanantly

docker network prune -f

### below command is for creating network

docker network create subversion

### below command is for updating or changing image version

docker service update --image redis:3 myredis 

### below command is for rollback the image version

docker service update --rollback myredis 

### below command is for node availability

docker node update --availability active node-01 

### below command is for drain the node

docker node update --availability drain node-02

## Updating the Service

To update the service with a new image or configuration, you can modify the docker-compose.yml file and then redeploy the stack:

docker stack deploy -c docker-compose.yml my-stack

docker stack rm <stack-name>


docker swarm init --advertise-addr <MANAGER-IP>

docker swarm join --token <TOKEN> <MANAGER-IP>:<PORT>

docker service create --name my-web-app -p 8080:80 my-web-image:latest

docker service scale my-web-app=5

docker service update --image my-new-image:latest my-web-app

### add volume manually 

docker container update --mount source=my_volume,target=/path/in/container container_id_or_name

## sharable docker volume

these volumes are used to sharable between the multiple containers

## simple docker volume

these volumes are used to preserve data on the hostmachine even if the containers are deleted.

## Docker volume containers

these volumes or bidirectional we made change in host machine it will reflect in containers,
we made changes on containers it will reflect on host machine.

## NETWORKS

In Docker, networking options allow containers to communicate with each other and with the external world in different ways. Here's an overview of the different network types—bridge, host, null, and overlay—and their key differences:

 1. Bridge Network

- Description: The default network driver for Docker containers. It creates a private internal network on your host system and connects containers to this network.
- Use Case: Suitable for when you want containers to communicate with each other on the same host but isolate them from the external network.
- Key Features:
  - Isolation: Containers connected to a bridge network can communicate with each other, but not with the host network or other networks unless specifically configured.
  - Port Mapping: To expose container ports to the host, you need to use port mapping (e.g., `-p 8080:80`).

```
  docker network create bridge
```  

  - Example Configuration in `docker-compose.yml`:

```
version: '3.8'
services:
      web:
        image: nginx
        networks:
          - my_bridge_network
networks:
  my_bridge_network:
    driver: bridge
```       
    

 2. Host Network

- Description: Removes network isolation between the container and the Docker host. The container shares the host’s network stack.
- Use Case: Ideal for performance-sensitive applications where you need the container to have direct access to the host network. Often used for low-latency applications.
- Key Features:
  - No Network Isolation: Containers share the host's IP address and network interfaces.
  - Port Conflicts: Since the container and host share the same network, port conflicts can occur if the same port is used on both.

```
  docker run --network host nginx
 ``` 

  - Example Configuration in `docker-compose.yml`:

```
version: '3.8'
services:
      web:
        image: nginx
        network_mode: host
```

 3. Null Network

- Description: A special network driver that essentially disables networking for containers.
- Use Case: Useful for containers that do not require network access and should not communicate with other containers or the outside world.
- Key Features:
  - No Network Access: Containers using the null network are isolated from all network traffic.
  
```
  docker network create -d null my_null_network
``` 

  - Example Configuration in `docker-compose.yml`:

 ```   
    version: '3.8'
    services:
      web:
        image: nginx
        networks:
          - my_null_network
    networks:
      my_null_network:
        driver: null
  ```   

 4. Overlay Network

- Description: A network driver used to connect Docker containers across multiple Docker hosts, using a virtual network layer over the host’s network.
- Use Case: Ideal for multi-host deployments, such as in Docker Swarm or Kubernetes environments, where services need to communicate across different hosts.
- Key Features:
  - Multi-Host Networking: Allows containers on different Docker hosts to communicate as if they were on the same network.
  - Requires a Key-Value Store: Typically requires a key-value store like etcd, Consul, or Zookeeper for managing network state.

```
  docker network create --driver overlay my_overlay_network
```


Example Configuration in `docker-compose.yml`:


```   
 version: '3.8'
 services:
      web:
        image: nginx
        networks:
          - my_overlay_network
networks:
  my_overlay_network:
    driver: overlay
 ```
        

 Summary of Differences:

- Bridge: Default, isolated network suitable for single-host communication.
- Host: Shares the host’s network stack, removing isolation and potentially improving performance.
- Null: Disables networking entirely for containers, useful for non-networked services.
- Overlay: Extends network capabilities across multiple Docker hosts, useful for clustering and orchestration setups.

Choosing the right network driver depends on your specific use case, including considerations for performance, isolation, and whether your containers need to communicate across different hosts.


========================================
========================================


### Here are some Docker scenario-based interview questions and their detailed answers that can help assess your understanding of Docker and containerization.

### 1. Scenario: Deploying a multi-container application
Question:
You are tasked with deploying a multi-container application using Docker. The application consists of a frontend (React) and a backend (Node.js). How would you approach this task, and what files would you need?

Answer:
In this scenario, you need to create a Dockerized environment for both the frontend and backend services, using Docker Compose to manage the multi-container setup. Here's how you would approach it:

Create a Dockerfile for the Backend (Node.js) Service:

backend/Dockerfile:
Dockerfile

```
FROM node:14
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```


Create a Dockerfile for the Frontend (React) Service:

frontend/Dockerfile:
Dockerfile

```
FROM node:14 AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

Create a docker-compose.yml file to define the services:

docker-compose.yml:

```
version: '3'
services:
  frontend:
    build:
      context: ./frontend
    ports:
      - "80:80"
    depends_on:
      - backend

  backend:
    build:
      context: ./backend
    ports:
      - "3000:3000"
```

Steps to run the application:

In the project directory, run the following command to start both containers:

```
docker-compose up --build
```

This will create and start the containers, linking them as defined in docker-compose.yml.

### 2. Scenario: Networking between containers
Question:
You have two containers running in the same Docker network: a web application and a database. The web application needs to communicate with the database. How would you ensure that the containers can communicate with each other?

Answer:
To allow communication between containers, you need to make sure that both containers are part of the same Docker network. Docker Compose makes this easy by default, as services defined in the same Compose file are automatically placed in the same network.

Here’s how you would ensure the containers can communicate:

Docker Compose Setup: In your docker-compose.yml file, define both the web application and database services, ensuring they are in the same network.

```
version: '3'
services:
  web:
    image: mysql
    environment:
      - DATABASE_URL=mysql://db:3306
    depends_on:
      - db
    networks:
      - app_network

  db:
    image: mysql:5.7
    environment:
      - MYSQL_ROOT_PASSWORD=rootpassword
    networks:
      - app_network

networks:
  app_network:
    driver: bridge
```

Access the database from the web application: The web service can access the database using the service name (db) as the hostname, e.g., mysql://db:3306. Docker’s internal DNS resolution allows containers to communicate by service name.

Running the containers: Run the following command to start both containers and allow them to communicate:

```
docker-compose up
```

By using Docker Compose and defining a custom network, containers in the same network can easily access each other using their service names.

### 3. Scenario: Optimizing Docker image build
Question:
You have a Dockerfile for a Python application. However, your image build times are taking too long. How would you optimize the Dockerfile for faster builds?

Answer:
To optimize Docker image builds, you can focus on improving the build context, minimizing the number of layers, and leveraging Docker’s cache effectively. Here are some strategies:

Use a smaller base image: If you're using a large base image (e.g., python:3.x), consider using a smaller one like python:3.x-slim to reduce the image size.

Order commands to maximize caching: Docker caches layers from the top down. Therefore, frequently changing parts of your Dockerfile should be placed towards the bottom. For example:

```
FROM python:3.9

# Set the working directory inside the container
WORKDIR /app

# Copy the rest of the application code into the container
COPY . .

# Command to run the application
CMD ["python", "app.py"]
```

By copying requirements.txt and running pip install early, Docker will cache the results of the pip install command and reuse them unless the requirements.txt changes.

Use multi-stage builds: Multi-stage builds allow you to separate the build environment from the final runtime environment. This reduces the size of the final image by excluding unnecessary build dependencies.

```
# Build stage
FROM python:3.9 AS build

# Set the working directory inside the container
WORKDIR /app

# Copy the rest of the application code into the container
COPY . .

# Final stage
FROM python:3.9-slim

# Set the working directory inside the container
WORKDIR /app

# Copy the installed dependencies from the build stage
COPY --from=build /app /app

# Command to run the application
CMD ["python", "app.py"]
```

Leverage .dockerignore: Ensure you are not copying unnecessary files into the Docker image, which will increase the build context size and build time. Use a .dockerignore file to exclude files like .git, .vscode, and local development dependencies.

Example .dockerignore:

```
.git
.vscode
__pycache__
*.pyc
```

### 4. Scenario: Managing persistent data
Question:
You need to persist data in a Docker container (e.g., a MySQL database) so that data is not lost when the container is stopped or removed. How would you configure this?

Answer:
To persist data across container restarts or removal, you need to use Docker volumes or bind mounts. Volumes are the recommended way to manage persistent data because they are managed by Docker, are portable, and provide better performance.

Using Docker Volumes: You can define a volume for the database container in your docker-compose.yml file.

```
version: '3'
services:
  db:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - app_network

volumes:
  db_data:
```

In this example, the db_data volume will persist the MySQL database data at /var/lib/mysql. Even if the MySQL container is stopped or removed, the volume data will remain intact and can be reused when the container is recreated.

Using Bind Mounts: Bind mounts allow you to map a host directory to a container directory. For example:

```
docker run -v /host/path/to/data:/container/path my-container
```

This would map the host directory /host/path/to/data to /container/path in the container, making the data persist between container restarts.

### 5. Scenario: Debugging a Docker container
Question:
You have a running Docker container, but you suspect there is an issue with the application inside the container. How would you troubleshoot and debug the container?

Answer:
To troubleshoot and debug a running Docker container, you can use several Docker commands:

Check container logs: The first step is to inspect the logs of the running container to see if there are any obvious errors.

```
docker logs <container_id>
```

Attach to a running container: You can attach to the container’s stdout and stderr to see real-time logs.

```
docker attach <container_id>
```

Exec into the container: You can execute a shell inside the container to investigate the environment and application state.

```
docker exec -it <container_id> /bin/bash
```

This will give you a shell prompt inside the container, allowing you to check files, processes, and logs directly.

Inspect the container: To view detailed information about the container (e.g., environment variables, network settings), use:

```
docker inspect <container_id>
```

Check resource usage: If you suspect resource issues, use the docker stats command to monitor resource usage in real time.

```
docker stats
```

By combining these tools, you can gather sufficient information to diagnose and resolve issues within a Docker container.
