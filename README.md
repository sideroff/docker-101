# docker-101
A repo for reference on the things most commonly used in docker. Tutorial that was followed can be found at: [https://www.youtube.com/watch?v=fqMOX6JJhGo](https://www.youtube.com/watch?v=fqMOX6JJhGo)


# docker

## why
```Matrix from hell or compatibility matrix``` - it happens when your application has a lot of different dependencies that each need to be compatible between themselves and also be supported by the OS. It is difficult to maintain the same version of each of those dependencies and the os on each environment and team member. Thus a more robust method is required.

You can avoid that problem by running each of your troubled dependencies in a separate container within a known context of os & installed prerequisites. 


## docker hub/store
[https://hub.docker.com/](https://hub.docker.com/)
A registry of all public images. You can publish your own image.


## image
A template for the context of the container: os type, prerequisites to be installed, script to be ran, etc..

## image versions
```<image name>:<version name>```
default is latest

## docker cli
Well documented using --help anywhere.

### management cmds
1. ```builder```     Manage builds
1. ```config```      Manage Docker configs
1. ```container```   Manage containers
1. ```context```     Manage contexts
1. ```image```       Manage images
1. ```network```     Manage networks
1. ```node```        Manage Swarm nodes
1. ```plugin```      Manage plugins
1. ```secret```      Manage Docker secrets
1. ```service```     Manage services
1. ```stack```       Manage Docker stacks
1. ```swarm```       Manage Swarm
1. ```system```      Manage Docker
1. ```trust```       Manage trust on Docker images
1. ```volume```      Manage volumes

Each of these supports commands of its own. To view the supported commands and flags for any management command use —-help (eg. ```docker —help```; ```docker container —help```, etc...)

## stdin & stdout
by default containers are **NOT** run in interactive mode. To allow the docker container to read stdin, supply ```--interactive``` or ```-i``` to run
for stdout supply ```--tty``` or ```-t```

### viewing the stdout & stderr of a detached component
```docker logs <container id>```

## port mapping
Ports inside a container are not accessible outside. For that to work you need to map a host port to the corresponding port in the container. Supply ```-p``` or ```--publish``` <host port>:<container port> to the run command to publish the port.


## volume mapping
Data inside the docker container dies when the container is removed. To save data, you can map a independant volume to a volume inside the container. This way, all the data that would otherwise live inside the container gets written to the  volume. Supply ```-v``` or ```--volume <volume path>:<container path>``` to the run command to map the volume.


## detailed container data
```docker inspect <container id>``` => json

## environment variables
Supply ```-e``` or ```—env <env_name>=<env_value>``` to the run command.
Environment variables that a container has set can be inspected by running the inspect command for a specific container 


## building an image
```docker build <path to Dockerfile> -t <tag>```

## publish an image
```docker push <tag>```

## Dockerfile
```<instruction> <argument>```
Instructions are all caps.
1. ```FROM``` - first line (can be preceeded by variables using ARG), specifies the base docker image
1. ```RUN``` - runs argument in the bash
1. ```COPY <foo> <bar>``` - copies everything from host foo directory to container bar directory
1. ```ENTRYPOINT``` - commands to run when image is run as conntainer

### Dockerfile layers
instructions are “layers” as in they are hashed and cached and provide an incremental history of the ran instructions 

### CMD vs ENTRYPOINT
CMD is replaced by parameters supplied to run. 
ENTRYPOINT prepended to parameters supplied to run.


## Network
By default, Docker creates 3 networks when installed: Bridge ( default ), Host & None. Usefull info on which exact network a container is under can be seen using the inspect command for that container.

### Bridge
A VPC created by docker that every container lives in by default, to access outside net, use port mapping. You can create custom bridge networks using the ```docker network create``` command. 

### Host
The container is ran inside the host network so no port mapping is needed.

### None
No attachments to any network.

### Specifyingn a network on container creation

```--network=<host|none>```


## Storage
Docker stores all data in ```/var/lib/docker/```

## Resources
Docker has a sort of alias for every process that it starts. This alias pid is linked to an actual process id on the host machine. Thus it's clear that docker containers use the underlying os kernel.


## Container orchestration
## why 
Since the trend to switch from Monolith applications to microservices there has been an increasing demand for tools like docker since containers are the perfect place for a microservice. These applications usualy require multiple containers that need to be able to communicate between each other and scale based on load. Setting these up manually and servicing them upon crashes or other problems is not really feasable. This is where orchestrators come in. An orchestrator is a tool that takes care of setting up containers, scales them up when needed, makes it easy to ensure high availability and fault tolerance.

## Required features
1. High availability - strive to 100% up time
1. Scalability - scale based on load for high performannce
1. Disaster recovery - backup and restore, you can rerun a node with the latest date before the crash

## Others - not going to go into detail
### MESOS
### Swarm
Can manage multiple docker hosts, load balance, etc.

#### Structure
You need multiple docker hosts. You designate one of them as the Master (swarm manager) and all others as Slaves (workers)

1. ```docker swarm init``` - creates a docker swarm manager, returns a token used for attaching workers
1. ```docker swarm join --token <token>``` - creates a worker and links it to the manager.




## Kubernetes
Open source container orchestration framework by Google.

### Structure
1. Pod - an abstraction above containers to allow different implementations ( docker or other ). Usually only one container runs inside a Pod but running multiple is possible. Each Pod gets an IP address from the virtual network.
1. Node - simple phyisical or virtual machine. Pods run in a Node. Can be master or worker.
1. Cluster - set of Nodes

### Components
1. API server - the frontend of kubernetes
1. etcd server - distributed reliable key value store - keeps the state
1. Scheduler - distributes containers across nodes
1. Controllers - responsible for noticing and responding to load / crashes
1. container runtime - underlying software that runs containers ( could be docker )
1. kubelet - kubernetes agent that runs on each node


### kubectl
Kubernetes cli.

```kubectl run <name>``` - runs a cluster
```kubectl cluster-info <name>``` - gets detailed info about cluster
```kubectl get nodes <name>``` - gets info on nodes on the cluster

### Virtual Network
This allows the nodes to talk to each other.


## Continious integration
Basically make sure that each new addition to the code is:
1. buildable
2. testable ( and passes tests )

This way no new broken code gets accepted in the repo.
