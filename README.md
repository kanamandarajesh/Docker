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

