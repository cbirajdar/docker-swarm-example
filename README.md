# Docker swarm example

Swarm is a native docker module to provision clustering and management of docker containers. Using docker swarm, you can communicate with different docker hosts to run and manage different containers.

**In this example, swarm master and agent VMs are running on the same host OS but you configure it to run across different hosts - https://docs.docker.com/swarm/install-manual/**

## Steps

Docker installation guide - https://docs.docker.com/engine/installation/

### Generate discovery token for swarm
I used md5 utility to generate a discovery token which will be used by swarm master and agents

```TOKEN=$(md5 -q -s SOME_STRING)```

### Creating docker swarm master

```docker-machine create -d virtualbox --swarm --swarm-master --swarm-discovery token://$TOKEN swarm-master```

This will
- Create a new docker machine
- Configure it to be a swarm master
- Install and run swarm discovery service with swarm daemon

### Creating docker swarm agents / nodes

```docker-machine create -d virtualbox --swarm --swarm-discovery token://$TOKEN swarm-agent1```

```docker-machine create -d virtualbox --swarm --swarm-discovery token://$TOKEN swarm-agent2```

Creates two docker machines which will register with master and run as swarm agents

### Verify and configure shell as swarm master

Run ```docker-machine ls``` to verify the docker machines for swarm master and agents are running

Run ```eval $(docker-machine env --swarm swarm-master)``` to configure your shell as swarm master

### Download and run docker images

For this example, we will download the official docker image for redis and try to run it in a container

```docker run -itd --name redis redis```

It may take few minutes to download and run the image.

Once it returns, on running ```docker ps```, you will notice that this image is running on one of the swarm agents (something similar as below).

```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
62626fd746bd        redis               "docker-entrypoint.sh"   20 hours ago        Up 20 hours         6379/tcp            swarm-agent1/redis
```

Basically, swarm master instructed one of the registered agent machines (based on the scheduling strategy) to pull the image and run it in a docker container.

### References
- https://docs.docker.com/swarm/install-w-machine/
- https://www.youtube.com/watch?v=zTKGfPfhg78
