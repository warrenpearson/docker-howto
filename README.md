## Docker How To

### Overview

A starting definition:

<https://phoenixnap.com/kb/docker-image-vs-container>

```
"A Docker image is an immutable (unchangeable) file that contains 
the source code, libraries, dependencies, tools, and other files 
needed for an application to run."

A container is, ultimately, just a running image. Once you create 
a container, it adds a writable layer on top of the immutable image, 
meaning you can now modify it.
```

This guide has the followig limitations:

* Mac only


### Install

Download and install `Docker Desktop` from <https://desktop.docker.com/mac/main/arm64/Docker.dmg>

### Run an example container

```bash
docker run -dp 80:80 docker/getting-started

Unable to find image 'docker/getting-started:latest' locally
latest: Pulling from docker/getting-started
261da4162673: Pull complete 
a60aada4c44a: Pull complete 
2f61404bb4b8: Pull complete 
fa3f58a317be: Pull complete 
476bb2a1cc22: Pull complete
33a28b928e89: Pull complete 
a879581b8e12: Pull complete 
d0193f05f10f: Pull complete 
14f901bbf056: Pull complete 
Digest: sha256:d79336f4812b6547a53e735480dde67f8f8f7071b414fbd9297609ffb989abc1
Status: Downloaded newer image for docker/getting-started:latest

994b97f695416cd2ca16c50cd62762b80fee68e063638a089c74f3820bb9c595
``` 

Open <http://127.0.0.1:80> in your browser

#### What's happening here?

You've just 

* downloaded the latest version of a docker image called `getting-started` from the central docker repository
* built it into a container that can run on your system
* told it to run the container in `detached` mode - that is, in the background
* told it to map the container's port 80 to your local Mac's port 80

#### Introspecting the container

```bash
docker image ls
REPOSITORY               TAG       IMAGE ID       CREATED         SIZE
docker/getting-started   latest    289dc403af49   13 months ago   46.5MB

docker container ls
CONTAINER ID   IMAGE                    COMMAND                  CREATED          STATUS          PORTS                NAMES
59d2ca997087   docker/getting-started   "/docker-entrypoint.…"   23 minutes ago   Up 23 minutes   0.0.0.0:80->80/tcp   optimistic_greider

docker ps -a
CONTAINER ID   IMAGE                    COMMAND                  CREATED          STATUS          PORTS                NAMES
59d2ca997087   docker/getting-started   "/docker-entrypoint.…"   23 minutes ago   Up 23 minutes   0.0.0.0:80->80/tcp   optimistic_greider

docker exec -it 59d2ca997087 sh

	/ # ls
	bin                   docker-entrypoint.sh  lib                   opt                   run                   sys                   var
	dev                   etc                   media                 proc                  sbin                  tmp
	docker-entrypoint.d   home                  mnt                   root                  srv                   usr
	
	/ # ps -a
	PID   USER     TIME  COMMAND
	    1 root      0:00 nginx: master process nginx -g daemon off;
	   30 nginx     0:00 nginx: worker process
	   31 nginx     0:00 nginx: worker process
	   32 nginx     0:00 nginx: worker process
	   33 nginx     0:00 nginx: worker process
	   34 nginx     0:00 nginx: worker process
	   35 nginx     0:00 nginx: worker process
	   36 nginx     0:00 nginx: worker process
	   37 nginx     0:00 nginx: worker process
	   72 root      0:00 sh
	   79 root      0:00 ps -a

```


#### Creating our own container

#### Find a base image

<https://hub.docker.com/_/python/> lists our options. We'll choose `3.12.1-slim-bookworm`, a python image based on Debian linux.

We will use python's built-in http server to create a web server we can connect to for testing. (The container will just stop running if it doesn't have a long-running process.)

Create a Dockerfile

```bash
FROM python:3.12.1-slim-bookworm

RUN python3 -m http.server 8080
```

Build the container

```bash
docker build -t simply-python-app .
```

Run the container

```bash
# get the image id
docker image ls
REPOSITORY          TAG       IMAGE ID       CREATED       SIZE
simply-python-app   latest    ae98223b783a   5 weeks ago   156MB

# run the container
docker run -d -it -p 8080:8080 ae98223b783a

# check the container status
docker ps -a

# view the python process in a browser
open http://127.0.0.1:8080
```