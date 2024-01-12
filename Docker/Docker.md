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
* [Ports exposing](#ports-exposing)

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
$ docker container logs [NAME]
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