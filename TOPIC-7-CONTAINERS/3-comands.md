docker build — Turns Dockerfile into Image

Basic Syntax

bashdocker build [OPTIONS] PATH

Most Used Build Commands
Basic build (. means current directory)
docker build .

# Build with a name/tag
docker build -t myapp .

# Build with name and version
docker build -t myapp:v1 .

# Build with Docker Hub username
docker build -t myusername/myapp:v1 .

# Build from different directory
docker build -t myapp /path/to/project

# Build from specific Dockerfile
docker build -t myapp -f Dockerfile.prod .

# Build without using cache
docker build --no-cache -t myapp .



docker run — Creates and Starts a Container

Basic Syntax

docker run [OPTIONS] IMAGE [COMMAND]
Most Used Run Commands

Basic run
docker run nginx

# Run in background (detached mode)
docker run -d nginx

# Run with a name
docker run -d --name mycontainer nginx

# Run and auto remove when stopped
docker run --rm nginx

# Run with interactive terminal
docker run -it ubuntu bash

Port Mapping (-p)
Map host port to container port


# -p HOST_PORT:CONTAINER_PORT
docker run -d -p 8080:80 nginx


Your Browser                    Container
localhost:8080  →  Docker  →   port 80 (nginx)
               port mapping


#  — no tag, hard to manage
docker build .

# Right — always tag your builds
docker build -t myapp:v1 .

#  — port not mapped, can't access app
docker run myapp

#  — map the port
docker run -p 3000:3000 myapp

# ❌  no -d, terminal gets stuck
docker run myapp

#  — run in background
docker run -d myapp



docker build -t myapp:v1 .

docker run -d -p 3000:3000 --name myapp  myapp:v1