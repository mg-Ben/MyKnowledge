---
tags:
  - Application_Development
---
# Core principles
## Definition
Docker is an Open Source platform that allows users to deploy applications as a standarized package called **container**. The point in using docker is the ability to **package the application with all the configuration files (i.e. dependencies)** to be ready to run on any machine.

Docker is a kind of **virtualization**, but with Docker we virtualize applications instead of Operating Systems.
- The **Guest system** is the container
- We **Host System** is your Operative System itself
## Image example
In the following image example, we have created two containers inside the Host OS: one is the server container (which might be python-based) and the other one is the DataBase container (e.g., MongoDB).
- The server receives requests, process them and communicate with Database container. Therefore, every network packet that is received in Host OS must be forwarded to the internal Server container. There are several ways to get this (see [[#Docker network]]): in this example, we have bound 5566 port of Host OS machine to 1228 container machine. Thus, so as to connect to server, a remote machine must communicate to port 5566 and the Host Machine OS would redirect the network packets to Server container:1228.
- The Database container has not direct reachability from Host OS. We are forced to communicate with Server container as an intermediary. To enable the inter-container communication, we must define a Docker network too (see [[#Docker network]]).
![[docker-containers.png]]
## Advantages
- **Portability**: you can run your app on any Operating System with Docker installed.
- **Scalability**: you can deploy _N_ instances of your application just with one click, so It is easily scalable.
# Docker layer
A Docker image is composed by layers, each one with an identifier.
# Docker image
It is the environment, with all the dependencies (configuration files). Consists of several [[#Docker layer|docker layers]]. To create a Docker image, a ```Dockerfile``` is needed, where you define which image to use (Python, Java, C, NodeJS...), the packages or modules your application needs (pandas, matplotlib, numpy...) or the command(s) that must be run when building the container to deploy your application (```python3 main.py```).
You can download Docker images on [DockerHub](https://hub.docker.com/).
## Dockerfile Example
Dockerfile example:
```yml
#Choose the image to download:
FROM python:3.12.0

#Commands to run when deploying the future container based on this image:

#Inside the container, we create a directory where to store appdata:
RUN mkdir -p /home/app

#We copy the current directory (where we are writing this Dockerfile) to /home/app inside the container:
COPY . /home/app

#Here we define which modules to download when deploying the container:
RUN pip install --upgrade pip
RUN pip3 install --no-cache-dir -r /home/app/requirements.txt

#In case we want to bind some Host OS port to some port in the future container to be created, we must explictly set that this image must expose the port of the future container to host:
EXPOSE 1228

#The command that runs the main code of the app: as the image is python, the command python is recognized inside the container:
CMD ["python", "/home/app/logic/main.py"]
```

Where ``requirements.txt`` is the file with the python modules and versions to use. Example:
```
pandas==2.1.4
openpyxl==3.1.2
matplotlib==3.8.2
fuzzywuzzy==0.18.0
```
The Operating System image (```.iso``` file) in terms of Operating System virtualization would be the analogous to [[#Docker image]] in terms of Application virtualization, because the image is the flavour we want (Ubuntu version 18.04 or Windows version 10, for example; in this case, Python, NodeJS, Prometheus...).
# Docker container
It is the application itself. Consists of several [[# Docker image|docker images]]. One of those images is the **base image**.
# Docker network
Allows the communication in several ways, called ```drivers```.
## Bridge driver
Communication inter-container: Docker container-to-docker container inside the same Host OS.
Supposing you have two containers inside the same bridged net, they communicate each other using as "IP addresses" the **container names**, because a DNS is created inside the network to resolve them.
## Host driver
Docker container-to-Host OS communication.
## IPVLAN driver
Docker container-to-external Internet directly. It assigns an [[IP#IP address|IP address]] to your docker container, so it is directly reachable from outside Internet as if it was a network interface on your machine.
## MACVLAN driver
Docker container-to-external Internet directly, but as though it was a separated physical machine. It assigns a MAC Address to your docker container.
## None driver
Docker container is no accessible from anywhere.
# Docker volume
Whenever a container is removed, the internal data is removed too. Supposing you have deployed a DataBase as a docker container, all your data would get lost. The solution is Docker volumes. The main idea is to link the internal container file system to Host OS file system.
## Docker volume approach
Docker allows you to create a volume managed by Docker itself. For example, you can create or delete them from Docker Desktop:
![[docker-desktop-volumes.png]]
You just need to attach that volume to some directory inside your container.
## Bind-mount approach
Docker allows you to link some path inside your [[Virtualization#Host Operating System|Host OS]] to some path inside the [[Docker#Docker container|Docker container]], and they are real-time synchronised by bind-mounting.
For this purpose, you will have to use Docker volume settings:
### If you are deploying with docker create
Then use the ```-v``` [[#Create your container|flag]].
### If you are deploying with docker-compose
Then use the ```volumes:``` [[#Template example|section]].

# Use case example
For example, you can run docker with a fixed version of Python libraries, NodeJS, Java, Ruby... And every machine that would execute your application would run it with that version, so Docker avoids conflicts with versioning and conflicts with the underlaying Operating System.
# Hands on
## Interesting commands
### For Docker images
#### List Docker images
```shell
docker images
```
Example output:
```
REPOSITORY             TAG           IMAGE ID       CREATED        SIZE
image1                 w64           d4e0ddc8e979   4 weeks ago    3.36GB
image2                 w32           86f87179d13f   4 weeks ago    3.32GB
image3                 0.1.0         d0041d84b67f   7 weeks ago    1.1GB
```
#### Remove Docker image
##### By image name:version
```shell
docker image rm <image_name>:<image_tag>
```
##### By image ID
```shell
docker image rm <image_id>
```
#### Get docker image
##### From Dockerfile
Once you have your [[#Docker image#Dockerfile Example|Dockerfile]], you can build your image like:
```
docker build <Dockerfile_path>
```
Where ```<Dockerfile_path>``` is the path where your Dockerfile lies (e.g., ```/nowhere/land/```).
Interesting flags:
- ```-t <image_name>:<image_tag>```: specify image name and image tag
##### From DockerHub
```shell
docker pull <image_name>:<image_tag>
```
### For Docker containers
#### Create your container
##### From a base image
```shell
docker create <image_name>
```
Interesting flags:
- ```--name <container_name>```: specify the container name. Don't use the ```_``` character
- ```-p A:B```: bind Host OS port ```A``` to container port ```B```
- ```-e HELLO_GOODBYE=X -e HERE_THERE_EVERYWHERE=Y```: pass environment variables: some containers, such as MongoDB, requires some environment variables (e.g., passwords or usernames to log into database)
- ```--network <network_ID_or_name>```: network where this container will join to communicate to other containers. Ensure it is [[#Interesting commands#For Docker networks|created]] before.
- ```-v <volume_name>:<path_inside_container>```: attach a docker volume (either existing or not; if not, docker automatically creates it) to some path inside container ([[#Docker volume#Docker volume approach]])
- ```-v <path_inside_host>:<path_inside_container>```: attach a Host path to some path inside container ([[#Docker volume#Bind-mount approach]])
#### List current containers
```shell
docker ps -a
```
Example output:
```
CONTAINER ID       IMAGE     COMMAND           CREATED       STATUS     PORTS     NAMES
8a7ffeed7881       python    "python main.py"  5 weeks ago   Exited               myapp
```
#### Start your container
```shell
docker start <container_ID_or_name>
```
Interesting flags:
- ```-d```: don't show logs (deploy in silent mode)
#### Stop your container
```shell
docker stop <container_ID_or_name>
```
#### Remove your container
```shell
docker rm <container_ID_or_name>
```
If you cannot remove it, try to [[#Stop your container]] before.
### For both Docker images and Docker containers
#### Download a docker image and start container at once
```shell
docker run <image_name>
```
Interesting flags: the same as [[#Interesting commands#For Docker containers#Create your container]] and [[#Interesting commands#For Docker containers#Start your container]].
### For Docker networks
#### List networks
```shell
docker network ls
```
Example output:
```
NETWORK ID     NAME                     DRIVER    SCOPE
6862988c3761   bridge                   bridge    local
95f97f73ce3d   host                     host      local
6163518f361f   none                     null      local
```
#### Create network
Creates a network for inter-container communication (see [[#Docker network#Bridge driver]]):
```shell
docker network create <network_name>
```
#### Remove network
```shell
docker network rm <network_name>
```
### For Docker volumes
#### List docker volumes
```shell
docker volume ls
```
Example output:
```
DRIVER    VOLUME NAME
local     stack_elk_certs
local     stack_elk_esdata01
local     stack_elk_esdata02
```
#### Remove docker volume
```shell
docker volume rm <volume_name>
```
If this error triggers: ```Error response from daemon: remove <volume_name> is in use - [...]``` then you would have to [[#Remove your container|remove the containers that are using that volume]].
#### Access to docker volume data
```
docker inspect <volume_name>
```
### For general purpose
#### Run commands inside docker container
Once your docker container is running, you can execute commands inside:
```shell
docker exec <container_ID_or_name> <command>
```
However, this would invoke the internal container **default** shell (not the internal container shell), which may have a very restricted support for some complex commands such as ```ll```, ```grep```, ```find```... To run these types of commands, we have to explictly set that the command would run inside the not default shell, but in the shell inside the container:
```
docker exec -it <container_ID_or_name> sh -c <command>
```
TIP: If ```<command> = bash```, you can get into the command line inside the container.
## Deploy your app
### 0. Download Docker Desktop
Download Docker Desktop to enable the [[#Interesting commands|Docker commands]].
### 1. Get an image
Either create it or download it from DockerHub (see [[#Docker image]]).
### 2. Build a container
See [[#Hands on#Interesting commands#For Docker containers]]: create and start your container.
### 3. The reality: docker-compose.yml
In practice, we don't get an image and deploy a container through docker commands: that is not feasible for lots and lots of containers. We use ```docker-compose.yml``` instead, where we define  which images to download and the containers to build, commonly called ```services```, and then deploy every container at once with ```docker compose up``` on the same directory as ```docker-compose.yml```.
#### Deploy docker-compose.yml
```shell
docker compose up
```
Interesting flags:
- ```--build```: Rebuild images before deploy, but not rebuild containers. If you make some changes on your code (e.g., in your python main code), maybe they are not reflected after ```docker compose up``` because of docker cache. Therefore, use this flag for testing purposes to rebuild everything for each test. However, it only rebuilds images, not containers!
- ```--force-recreate```: Recreate containers before deploy. If you find an error like ```error response from daemon: network <...> not found``` you will need to recreate the network when deploying by ```docker compose up``` with ```--force-recreate``` flag. This would stop and recreate the docker containers, but won't remove the docker volumes!
_Note: use the two flags ```--build``` and ```--force-recreate``` in conjunction to rebuild images and recreate containers_
#### Remove images and deployed by docker-compose.yml
```
docker compose down
```
#### Template example
Here you have the main settings to deploy your containers:
```yaml
version: "0.1" #Your project version
services: #Containers
	mongo_db:
		container_name: my-mongo-database #Container name
		image: mongo #Either dockerHub image name to pull or:
		build: . #Path to Dockerfile to build image
		ports:
			- "hostPort:containerPort" #Bind ports from Host to container
		environment: #Environment variables passed to container
			- HELLO_GOODBYE=X
			- HERE_THERE_EVERYWHERE=Y
		networks:
			- my-stack-network #Network name
		volumes:
			- mongoDB-volume:/data/db #VolumeName:path (docker volume)
			- /home/ubuntu/appdata:/data/db #HostOSPath:ContainerPath (bind-mount)
		limits: #Only if you want to limit the CPU and RAM resources
			cpus: '3' #CPU cores to use. 0.0 = Use the same cores as hostOS
			memory: 4G #RAM to use. '0' = Use the same RAM as hostOS
		restart: always #This means that this container will try to automatic restart if it stops working (for service continuity and high-availability of the service)
		user: root #The process inside the container will run as root user
	server: #The same
		depends_on:
			- mongo_db #This container will be created after mongo_db if mongo_db is created successfully
		container_name: python-server
		build: .
		ports:
			- "hostPort:containerPort"
		environment:
			...
		networks:
			- my-stack-network
		volumes:
			python-server-volume:/home/app/data
	
	volumes: #Here are the settings for the volumes defined above
		mongoDB-volume:
			driver: local
		python-server-volume:
			driver: local
	
	networks: #Here are the settings for the networks defined above
		my-stack-network:
			driver: bridge
```
A good and common practice is to have a ```.env``` file on the same directory of ```docker-compose.yml``` where you can define all the versions, port numbers and environment variables to use (e.g., the Python image version to use and the username/password for database containers):
```
PYTHON_VERSION=9.8.7
SERVER_PORT=9090
MONGODBVERSION=1.2.3
MONGODB_PORT=3000
HELLO_GOODBYE_VALUE=123
...
```
Then, reference those variables inside ```docker-compose.yml``` such as:
```
...
	mongo-db:
		...
		environment:
			- HELLO_GOODBYE=${HELLO_GOODBYE_VALUE}
		...
```
This makes easier the deployment: user just needs to edit the ```.env``` file and ```docker compose up```.