# docker

## concepts

A **container** is an emulation of a complete machine in that all necessary components for the running container are within the container itself. This solves the 'it works on my machine' problem.

An **image** can be thought of as a snapshot of a container, but I prefer to think of it as the starting point of a container.

The relationship between a container and image is that a container simply adds a writable layer on top of the image. Moreover, the container is actually running, whereas the image is static.

A **Dockerfile** specifies the steps to make an image. In particular, it specifies a set of layers where docker only tracks the differences between successive layers (copy-on-write).

The docker daemon is the backend that runs as a background process and handles the complex interactions.
The docker client is the cli that acts as the frontend and is what the user interacts with.


## Common commands

run a command on an arbitrary image
```
# alpine is the image
docker [container] run alpine echo 'Command'
```

run a command in the background (daemon)
```
docker [container] run -d alpine echo 'Command'
# keep container alive 
docker [container] run -di alpine 
```

run a command on a running container
```
# -it is only needed for bash or tty 
docker exec -it <container id or name> <command>
```

attach to a running container
```
docker attach <container id or name>
```

stop a container
```
docker stop <container_id or name>
```

list containers
```
# list running
docker container ls
docker ps
# list all (including non running)
docker container ls -a
docker ps -a
```

delete a container
```
docker rm <container id or name>
```

list out locally available images
```
docker images
```

delete an image
```
docker rmi <image name>
```

## Dockerfile
Common commands
```
FROM - specify the starting image
ADD - Add local or remote files and directories
- COPY is similar
CMD - specify default command
RUN - Run a command (in the container)
WORKDIR -  equivalent to cd
ENV - set environment variable
EXPOSE - document a port be visible outside the container
- it does not actually publish anything; use the p flag when running to do this
USER - set the user
```

## Example
```Dockerfile
FROM python:3.12
WORKDIR /usr/local/app

# Install the application dependencies
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

# Copy in the source code
COPY src ./src
EXPOSE 5000

# Setup an app user so the container doesn't run as the root user
RUN useradd app
USER app

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8080"]
```

## Docker Compose
a tool for managing applications with multiple containers

### docker-compose.yml
This file specifies how to launch the several applications

Example:
```
services:
  app:
    image: app:latest
    build:
      context: .
    ports:
      # note we have environment varialble and default syntax
      - ${APP_PORT:-80}:80
  redis:
    image: redis:6
```

### other concepts

profiles are ways are configuration variables where we selectively enable different containers


### commands
note that commands are in relation to the docker-compose.yml file. In particular, if it can't find it (and not manually specified with --profile-directory), then it's an error
```
# start
docker compose up [-d]
# finish
docker compose down
# pause
docker compose stop
# resume
docker compose start
# list
docker compose ps
```

### com

## references
- https://github.com/groda/big_data/blob/master/docker_for_beginners.md
- https://spacelift.io/blog/docker-compose
