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
* [List processes running in container](#list-processes-running-in-container)
* [Delete container](#delete-container)
* [Delete image](#delete-image)
* [Build image from file](#build-image-from-file)
* [See docker image content](#see-docker-image-content)
* [Docker file definition](#docker-file-definition)
* [Docker compose](#docker-compose)
* [Run app with params](#run-app-with-params)
* [Run specific file](#run-specific-file)

### Check is docker working
```
$ sudo docker run hello-world 
```
### Search image
```
$ sudo docker search nginx
```
### Download image from hub.docker.com
```
$ sudo docker pull nginx
```

### Create container from image 
forward port and dir
```
$ sudo docker run -d --name nginx -p 80:80 -v /var/www/html:/usr/share/nginx/html nginx 
```

### Show list of running containers
```
$ sudo docker ps
```
OR
```
$ docker container ls
```
Show list of running and exited containers
```
$ sudo docker ps -a
```

### Run bash inside container
```
$ sudo docker exec -it nginx bash
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
$ docker container top [NAME]
```

### Stats
```
$ docker container stats [NAME]
```

### Delete container
```
$ docker rm <container id>
```

### Delete image
```
$ docker rmi <image-id>
```

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

