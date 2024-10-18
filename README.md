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
 
