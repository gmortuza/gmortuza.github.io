---
title: 'Get Started with docker'
date: 2020-08-06
permalink: /posts/docker-get-started
mathjax: false
excerpt: Docker provides OS-level virtualization like virtual machine. Unlike virtual machine docker is fast and minimal weight.......

imgDir: '/images/docker-get-started/'
categories:
  - DevOps
tags:
  - docker
  - devops
---

## What is docker?

Let's consider a case where a developer built an web app in some machine. That app depends on bunch of libraries. If at
any point the developer wants to migrate the app in some other machine then he needs to reinstall all the libraries in the 
new machine. Some of the libraries might not be compatilbe at the point because of the different version. It would be really
nice if the developer had some kind of system that handle all these library installation. Docker is a tool that can handle
all these libraries installation. Docker provides OS level virtualization.  

**Docker container:** Docker container is nothing but an isolated running process that run on the host machine. Containers
can have their own process, networks and file system. A single host OS can have multiple container. Where each container
can communicate among themselves.  
**Docker images:** Docker images are the blue print of the the container. Docker image contains all the instruction about
how the container will behave.   

If you are familiar with Object Orientaed Programming(OOP) you can compare docker image with class and docker container 
with the instance of the class(object).


Virtual machine also does this same thing as docker container. So what's the difference between docker container and 
the virtual machine? 

| Docker container | Virtual machine |
| --- | --- |
| Share same OS from host machine | Create it's own OS|
| Have lower CPU utilization | Have higher CPU utilization |
| Have lower memory consumption | Have higher memory consumption |
| Boot up faster | Boot up slower |

<p class="img-with-source">
<img src="/images/docker-get-started/container-vm.png" alt="Docker container vs virtual machine">
Image taken from <a href="https://www.docker.com/sites/default/files/d8/2018-11/docker-containerized-and-vm-transparent-bg.png" >here</a>
</p>
## Creating Docker image
Before creating the docker image we need to first install docker. You can use <a href="https://docs.docker.com/desktop/">this</a>
source to download and install docker on your machine.
As we mentioned earlier docker image is a file which contains the blueprint of the docker container. The name of the docker
image file is **Dockerfile**. Whenever docker try to build the container it search this file in that directory. Docker image
can start up using another docker image. If docker found that image in the local machine it will use that local machine's 
image otherwise docker will search the image in the remote repository. This remote repository is also called <a href="https://hub.docker.com/">
docker hub</a>. You can also upload your created image into docker hub. For example if you want to use tensorflow in your project
you don't need to install the tensorflow in your docker image. You can start building your image by using the prebuild image
of tensorflow in <a href="https://hub.docker.com/r/tensorflow/tensorflow/">docker hub</a>. Let's see an example where we will
create a docker image by using ubuntu prebuild image.


```docker
# Docker will try to find the Ubuntu version 20.04 image in your machine.
# If you never Ubuntu version 20.04 image then docker will grab that from the docker hub.
# It's a good idea to specify the version number in your docker file. 
# So that if infuture your application won't have any problem because of the version change.
# If we don't specify the version name docker will install the latest version by default.
FROM Ubuntu:20.04
# This defines an environmental variable in your docker container.  
ENV version 1.0
# The Ubuntu image that we just install is the minimal functionality that an OS can have.
# We need to install our required apps/package by ourself.
# RUN is a docker image that run the command in the terminal
RUN apt-get update
RUN apt-get install python3.8 git make
# Create workspace directory in docker container
RUN mkdir /workspace
# By default docker use root directory as the workspace directory
# We want to change that to workspace directory
WORKDIR /workspace
# This will copy the current directory's content(Where the Dockerfile located) to the docker container workspace. 
COPY . /workspace
# Or we we can also use git to copy a repository
RUN git clone github.com/example/example.git
# Compile the files
RUN make
# Run the application
# Instead of ENTRYPOINT we could also use CMD command here.
CMD [python3, main.py]
```
#### ENTRYPOINT vs CMD
Both ENTRYPOINT and CMD are the command for docker image. But there is a subtle difference between them. 
The ENTRYPOINT specifies a command that will always be executed whenthe container starts. The CMD specifies arguments that will be 
fed to the ENTRYPOINT. By default docker use `/bin/sh -c` this entrypoint. However we can modify the entrypoint using 
`--entrypoint` parameter. If the docker image is for our general purpose we should leave it be. Otherwise we can modify 
the entrypoint. For example if we want a docker image that will ping another any server we can use the Entrypoint. If
we run any image that looks like this.
```docker
ENTRYPOINT ["/bin/ping"]
CMD ["localhost"]
```
And run it using this command `docker run my_image` it will always ping localhost. If we run it using `docker run my_image https://google.com`, it will always ping google.
So if we pass any parameter it will replace the parameter in the CMD.
