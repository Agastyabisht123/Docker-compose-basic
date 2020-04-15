
# Docker

## Image & Containers
•	Container is nothing but a running process, with some added encapsulation features applied to it in order to keep it isolated from the host and from other containers. One of the most important aspects of container isolation is that each container interacts with its own private filesystem; this filesystem is provided by a Docker image. An image includes everything needed to run an application - the code or binary, runtimes, dependencies, and any other filesystem objects required.

•	To build your own image, you create a Dockerfile with a simple syntax for defining the steps needed to create the image and run it. Each instruction in a Dockerfile creates a layer in the image. When we change the Dockerfile and rebuild the image, only those layers which have changed are rebuilt. This is part of what makes images so lightweight, small, and fast.

## Containers and virtual machines
  + A container runs natively on Linux and shares the kernel of the host machine with other containers. It runs a discrete process, taking no more memory than any other executable, making it lightweight.
  + By contrast, a virtual machine (VM) runs a full-blown “guest” operating system with virtual access to host resources through a hypervisor. In general, VMs incur a lot of overhead beyond what is being consumed by your application logic.
 

# Setup Steps

  + Create a folder to hold the project.

	`mkdir hello_docker`

  Navigate to that directory with `cd` .

	`cd hello_docker`

  + Make sure you have docker installed
	`$ docker -v`  

   Now to check if their any running containers.

	`$ docker ps`  

  + To kill a running container

	`$ docker kill <CONTAINER ID>`

  + Create code/application file, Dockerfile & requirements.txt file.

## Writing a Dockerfile
##### Writing a Dockerfile is the first step to containerizing an application. You can think of these Dockerfile commands as a step-by-step recipe on how to build up your image.
Dockerfile:
+  ADD
+  COPY
+  ENV
+  EXPOSE
+  FROM
+  LABEL
+  STOPSIGNAL
+  USER
+  VOLUME
+  WORKDIR


###### Example:

```python
FROM python:3
WORKDIR /usr/src/app
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["python","./pythonfile.py"] 
```

+ FROM – The FROM instruction specifies the Parent Image from which you are building.

 	`FROM [--platform=<platform>] <image> [AS <name>]
	-ARG is the only instruction that may precede FROM in the Dockerfile.
	ARG CODE_VERSION=latest`

+ WORKDIR - specify that all subsequent actions should be taken from the directory /path/of/file in your image filesystem (never the host’s filesystem).

+ COPY – Copy the source file from host to current working directory.

+ RUN – Use RUN in case we want to run any command, for ex to install pkg. RUN <command> (shell form, the command is run in a shell, 
which by default is /bin/sh -c on Linux or cmd /S /C on Windows).

	`Run pip install -r requirements.txt`

-In the shell form you can use a \ (backslash) to continue a single RUN instruction onto the next line. -For example, consider these two lines:
	`$RUN /bin/bash -c 'source $HOME/.bashrc; \
	echo $HOME`

+ EXPOSE: Informs Docker that the container is listening on port XXXX at runtime

Syntex -
	`EXPOSE <port> [<port>/<protocol>...]`
-By default, EXPOSE assumes TCP. You can also specify UDP:
	`EXPOSE 80/udp`

-To expose on both TCP and UDP, include two lines:
	`EXPOSE 80/tcp
	EXPOSE 80/udp`

+ ENTRYPOINT -- Specify the command which we want to execute when we create a container out of our Image.

+ CMD: The argument/file we want to pass to ENTRYPOINT.
	There can only be one CMD instruction in a Dockerfile. If you list more than one CMD then only the last CMD will take effect.

	` CMD ['python','/.pythonfile.py]`
	This will be executed as `python /.pythonfile.py`

+ ENV: The ENV instruction sets the environment variable <key> to the value <value>.
	   `ENV <key> <value>
	    ENV <key>=<value>`


## To build an image from dockerfile:

	`$ docker build .`
+  The build is run by the Docker daemon, not by the CLI. The first thing a build process does is send the entire context (recursively) to the daemon.
+  Traditionally, the Dockerfile is called Dockerfile and located in the root of the context. You use the -f flag with docker build to point to a Dockerfile anywhere in your file system.

	`$ docker build -f /path/to/a/Dockerfile .`

 Look for it at the end with the following confirmation

	`Successfully built ddc23d92067e
	Successfully tagged my_docker_flask:latest`

   It build an image with the tag ( --tag , -t ) `my_docker:latest` that includes everything in the current directory, .
   It run each step in Dockerfile & we can check the status on consol window.

## Run your image as a container:

	`$ docker run --publish 8000:8080 --detach --name bb bulletinboard:1.0`

  +  publish: asks Docker to forward traffic incoming on the host’s port 8000, to the container’s port 8080(specified in dockerfile). Containers have their own private set of ports, so if you want to reach one from the network, you have to forward traffic to it in this way.
  +  detach: asks Docker to run this container in the background.
  +  name specifies a name with which you can refer to your container in subsequent commands, in this case bb.

##### Container can be delete using: `$docker rm --force bb`


## Push Your Image To Docker Hub

Now you can push your image to docker hub so you can pull it down later and use it, thats why docker is awesome.

Create an account over at docker hub.

https://hub.docker.com/

Then from your terminal window,

`$ docker login -u 'username'

Password:Login Succeeded`

Replace 'username' with your username

Now we re-tag the image with your username prefix, <username>/.

`$ docker tag my_docker agastya/my_docker_flask`
And then you can push it to your docker hub.

`$ docker push agastya/my_docker`



# --------------------------------------------------------------
# Compose file

Compose is a tool for defining and running multi-container Docker applications.

The Compose file is a YAML file defining services, networks and volumes. The default path for a Compose file is ./docker-compose.yml.

`Tip: You can use either a ‘.yml’ or ‘.yaml’ extension for this file. They both works.`


###### Using Compose is basically a three-step process:

- Define your app’s environment with a Dockerfile so it can be reproduced anywhere.

- Define the services that make up your app in docker-compose.yml so they can be run together in an isolated environment.

- Run docker-compose up and Compose starts and runs your entire app.


Example:

	```
	version: "3.7"
	services:

  	redis:
		image: redis:alpine
		ports:
		- "6379"
		networks:
		- frontend
		deploy:
		replicas: 2
		update_config:
			parallelism: 2
			delay: 10s
		restart_policy:
			condition: on-failure

  	db:
    		image: postgres:9.4
    		volumes:
		      - db-data:/var/lib/postgresql/data
    		networks:
      			- backend
    		deploy:
      		placement:
        	constraints:
          		- "node.role==manager"```

##### Features of Compose

The features of Compose that make it effective are:

+ Multiple isolated environments on a single host
+ Preserve volume data when containers are created
+ Only recreate containers that have changed
+ Variables and moving a composition between environments
