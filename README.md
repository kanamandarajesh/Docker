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

