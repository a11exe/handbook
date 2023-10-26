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