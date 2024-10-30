# Docker

* [Check is docker working](#check-is-docker-working)
* [Search image](#search-image)
* [Download image](#download-image-from-hubdockercom)
* [Create container](#create-container-from-image)
* [Show list of running containers](#show-list-of-running-containers)
* [Run bash inside container](#run-bash-inside-container)
* [Stop container](#stop-container)
* [Login to docker hub](#login-to-docker-hub)
* [List images](#list-locally-stored-images)
* [Get logs](#get-logs)
* [Stats](#stats)
* [Inspect](#inspect)
* [List processes running in container](#list-processes-running-in-container)
* [Delete container](#delete-container)
* [Delete image](#delete-image)
* [Volume](#volume)
* [Network](#network)
* [Build image from file](#build-image-from-file)
* [See docker image content](#see-docker-image-content)
* [Docker file definition](#docker-file-definition)
* [Docker compose](#docker-compose)
* [Run app with params](#run-app-with-params)
* [Run specific file](#run-specific-file)

### Check is docker working
```
$ docker run hello-world 
```
### Search image
```
$ docker search nginx
```
### Download image from hub.docker.com
```
$ docker pull nginx
```

### Create container from image 
forward port and dir
```
$ docker run -d --name nginx -p 80:80 -v /var/www/html:/usr/share/nginx/html nginx 
```
if container stopped, then use
```
$ docker start nginx
```

### Show list of running containers
```
$ docker ps
```
OR
```
$ docker container ls
```
Show list of running and exited containers
```
$ docker ps -a
```

### Run bash inside container
```
$ docker exec -it nginx bash
```

### Stop container
```
$ docker stop <container id>
```
kill immediately
```
$ docker kill <container id> 
```

### Login to docker hub
```
$ docker login
```

### List locally stored images
```
$ docker images
```

### Get logs
```
$ docker logs [NAME] -f
```

### List processes running in container
```
$ docker top [NAME]
```

### Stats
Describe container memory usage
```
$ docker stats [NAME]
```

### Inspect
provides detailed information on constructs controlled by Docker
```
$ docker inspect image-name
$ docker inspect 
```
For the most part, you can pick out any field from the JSON in a fairly straightforward manner.
```
docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' $INSTANCE_ID
```

### Delete container
```
$ docker rm <container id>
```

### Delete image
```
$ docker rmi <image-id>
```

### Volume
```
docker volume create my-vol
docker volume ls
docker volume inspect my-vol
docker volume rm my-vol
```
If you start a container with a volume that doesn't yet exist, Docker creates the volume for you.

Docker volume and Bind mount are the docker components. 
Using bind mounts, you may mount a file or directory from your host computer onto your container and access it using its absolute path.
Conversely, when a volume is used, Docker makes a new directory in the host machine’s storage directory and keeps it updated.

**Difference between Docker volume and Bind mount**

| Docker volume  | Bind mount  |
|---|---|
|  Docker volume is the recommended method for storing data created and utilized by Docker containers is to use volumes. | Bind mount has existed from Docker’s early versions. Comparatively speaking, bind mounts are less useful than volumes. |
| All you need is the volume name to mount it.  | When using bind mounts for mounting, a route to the host computer must be supplied. |
| In /var/lib/docker/volumes, the volumes are stored.  | On the host computer, a bind mount can be located anywhere. |

Docker volume is the recommended method for storing data created and utilized by Docker containers is to use volumes.

usage
```
docker run -d \                                                                                                
         -e POSTGRES_USER=postgres \
         -e POSTGRES_PASSWORD=postgres \
         -e POSTGRES_DB=postgres \
         -p 5432:5432 \
         --volume postgres-data:/var/lib/postgresql/data \
         postgres:13
```

### Network
You can create custom, user-defined networks, and connect multiple containers to the same network.
Once connected to a user-defined network, containers can communicate with each other using container IP addresses or container names.

```
docker network create -d bridge my-net
docker run --network=my-net -itd --name=container3 busybox
```
By default, when you create or run a container using `docker create` or `docker run`, 
containers on bridge networks don't expose any ports to the outside world. Use the `--publish` or `-p` flag 
to make a port available to services outside the bridge network. 
This creates a firewall rule in the host, mapping a container port to a port on the Docker host to the outside world.

`-p 8080:80`	- Map port 8080 on the Docker host to TCP port 80 in the container.

If you want to make a container accessible to other containers, it isn't necessary to publish the container's ports. 
You can enable inter-container communication by connecting the containers to the same network, usually a bridge network.


### Build image from file
```shell
docker build -t soapui-base:latest -f base.Dockerfile .
```

### See docker image content
```shell
docker run -it image_name sh
```

## Docker file definition

### FROM
Base image
```shell
FROM image_name:tag
#Example
FROM ubuntu:20.04
```

### WORKDIR
Set base workdir. Execution will be relative to this dir
```shell
WORKDIR /path/to/directory
#Example
 WORKDIR /app
```

### COPY
```shell
COPY host_source_path container_destination_path
#Example
 COPY ..
```

### ADD
Same as copy but additional options
```shell
ADD source_path destination_path
#Example
ADD ./app.tar.gz /app
```

### RUN
Run command in container
```shell
RUN command1 && command2
#Example
RUN apt-get update && apt-get install -y curl
```

### ENV
Set environment variables inside image
```shell
ENV KEY=VALUE
#Example
ENV NODE_VERSION=14
```

### EXPOSE
```shell
EXPOSE port
#Example
EXPOSE 8080
```

### CMD
Console commands to run app inside container.
The `CMD` specifies arguments that will be fed to the `ENTRYPOINT`
```shell
CMD ["executable","param1","param2"]
#Example
CMD ["npm", "start"]
```

### ENTRYPOINT
The `ENTRYPOINT` specifies a command that will always be executed when the container starts.
```shell
ENTRYPOINT ["executable", "param1", "param2"]
#Example
ENTRYPOINT ["node", "app.js"]
```

### VOLUME

```shell
VOLUME /path/to/volume
#Example
VOLUME /data
```

### LABEL
```shell
LABEL key="value"
#Example
LABEL version="1.0" maintainer="Adrian"
```

### USER
By default, Docker runs containers with a root user, which can create a security risk and cause permission issues 
when accessing files and directories. Hence, the container user should be a non-root user with appropriate permissions.

```shell
USER spring:spring
```

```shell
FROM alpine:latest
ARG DOCKER_USER=default_user
RUN addgroup -S $DOCKER_USER && adduser -S $DOCKER_USER -G $DOCKER_USER
USER $DOCKER_USER
CMD ["whoami"]
```

### ARG
Variables can be passed while build docker image with command `docker build`
```shell
ARG VARIABLE_NAME=default_value
#Example
ARG VERSION=latest
```

```shell
$ docker build --build-arg DOCKER_USER=baeldung -t dynamicuser .
```

You can use an ARG or an ENV instruction to specify variables that are available to the RUN instruction. 
Environment variables defined using the ENV instruction always override an ARG instruction of the same name.


### Example Docker file
```shell
FROM adoptopenjdk/openjdk11:x86_64-alpine-jdk-11.0.17_8
ENV SERVICE_NAME=my-service
ENV LOG_PATH=./docker/logs
RUN mkdir -p ${LOG_PATH}/${SERVICE_NAME}/
RUN addgroup -S spring && adduser -S spring -G spring
RUN chown -R spring:spring ${LOG_PATH}/${SERVICE_NAME}/
USER spring:spring
ARG JAR_FILE=build/libs/${SERVICE_NAME}*.jar
COPY ${JAR_FILE} ${SERVICE_NAME}.jar

ENV JAVA_OPTS="-Xss1m -Xms128m -Xmx256m"
ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar /$SERVICE_NAME.jar"]
```

## Docker compose

You can control the order of service startup and shutdown with the `depends_on` attribute.
`depends_on` doesn't wait for other container starts, it's just a order of startup.
You can use `restart: on_failure` to restart a dependent container.

Run a specific container
```
docker compose up postgres
```

```shell
version: "3.7"

services:
  zookeeper:
    image: wurstmeister/zookeeper:3.4.6
    container_name: zookeeper
    restart: always
    ports:
      - 2181:2181
    healthcheck:
      test: nc -z localhost 2181 || exit -1
      interval: 10s
      timeout: 5s
      retries: 10
      start_period: 5s

  kafka:
    build: ./kafka
    container_name: kafka
    hostname: kafka
    restart: always
    ports:
      - 9092:9092
      - 9093:9093
    env_file:
      - kafka/kafka-params.env
      
  postgres:
    build: ./postgres
    container_name: postgres
    restart: always
    ports:
      - 5432:5432
    environment:
      POSTGRES_PASSWORD: mysecretpassword
    command: postgres -c shared_buffers=80MB -c max_connections=300 -c config_file=/etc/postgresql/postgresql.conf      
```

### Ports exposing
```
[HOST:]CONTAINER[/PROTOCOL]
```
examples
```
services:
  myapp1:
    ...
    ports:
    - "3000"                             # container port (3000), assigned to random host port
    - "3001-3005"                        # container port range (3001-3005), assigned to random host ports
    - "8000:8000"                        # container port (8000), assigned to given host port (8000)
    - "9090-9091:8080-8081"              # container port range (8080-8081), assigned to given host port range (9090-9091)
    - "127.0.0.1:8002:8002"              # container port (8002), assigned to given host port (8002) and bind to 127.0.0.1
    - "6060:6060/udp"                    # container port (6060) restricted to UDP protocol, assigned to given host (6060)
```

### Run app with params
use command
```
mongodb:
  container_name: mongo
  image: latest
  command: mongod --httpinterface --rest
```

### Run specific file
`-d` - detached
```shell
docker-compose -f /data/......./docker-compose.yml up -d 
```

