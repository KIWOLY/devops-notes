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



## Most Used Docker Commands



##  Image Commands


# Pull image from registry
docker pull nginx
docker pull node:18

# List all local images
docker images

# Build image from Dockerfile
docker build -t myapp:v1 .

# Tag an image
docker tag myapp:v1 myusername/myapp:v1

# Push image to registry
docker push myusername/myapp:v1

# Remove an image
docker rmi myapp:v1

# Remove all unused images
docker image prune -a

# Search image on Docker Hub
docker search nginx

# See image history/layers
docker history myapp:v1

# Inspect image details
docker image inspect nginx


##  Container Commands


# Run a container
docker run nginx

# Run in background
docker run -d nginx

# Run with name
docker run -d --name myapp nginx

# Run with port mapping
docker run -d -p 8080:80 nginx

# Run with env variable
docker run -d -e DB_HOST=localhost myapp

# Run with volume
docker run -d -v $(pwd):/app myapp

# Run interactively
docker run -it ubuntu bash

# Run and auto remove when stopped
docker run --rm nginx

# List running containers
docker ps

# List ALL containers (including stopped)
docker ps -a

# Stop a container
docker stop myapp

# Start a stopped container
docker start myapp

# Restart a container
docker restart myapp

# Remove a container
docker rm myapp

# Force remove running container
docker rm -f myapp

# Remove all stopped containers
docker container prune




## Inspect & Debug Commands

```bash
# See container logs
docker logs myapp

# Follow logs live
docker logs -f myapp

# See last 50 lines of logs
docker logs --tail 50 myapp

# Go inside running container
docker exec -it myapp bash

# Go inside with sh (alpine containers)
docker exec -it myapp sh

# Run single command inside container
docker exec myapp ls /app

# See container resource usage live
docker stats

# See stats of specific container
docker stats myapp

# Inspect full container details
docker inspect myapp

# See running processes inside container
docker top myapp

# See port mappings
docker port myapp


##  Volume Commands


# List all volumes
docker volume ls

# Create a volume
docker volume create myvolume

# Inspect a volume
docker volume inspect myvolume

# Remove a volume
docker volume rm myvolume

# Remove all unused volumes
docker volume prune


## Network Commands


# List all networks
docker network ls

# Create a network
docker network create mynetwork

# Connect container to network
docker network connect mynetwork myapp

# Disconnect container from network
docker network disconnect mynetwork myapp

# Inspect a network
docker network inspect mynetwork

# Remove a network
docker network rm mynetwork

# Remove all unused networks
docker network prune


## Cleanup Commands


# Remove all stopped containers
docker container prune

# Remove all unused images
docker image prune -a

# Remove all unused volumes
docker volume prune

# Remove all unused networks
docker network prune

# Remove EVERYTHING unused at once
docker system prune

# Remove EVERYTHING including volumes
docker system prune -a --volumes

# See disk usage
docker system df


## Docker Compose Commands

```bash
# Start all services
docker compose up

# Start in background
docker compose up -d

# Build and start
docker compose up --build

# Stop all services
docker compose down

# Stop and remove volumes
docker compose down -v

# See logs
docker compose logs

# Follow logs
docker compose logs -f

# See logs of specific service
docker compose logs app

# List running services
docker compose ps

# Go inside a service container
docker compose exec app bash

# Run one off command
docker compose run app node migrate.js

# Restart a service
docker compose restart app

# Build images only
docker compose build

# Pull latest images
docker compose pull


## System Commands

```bash
# Check Docker version
docker version

# Check Docker info
docker info

# See disk usage
docker system df

# See all events
docker events

# Login to Docker Hub
docker login

# Logout
docker logout


##  Most Used Day to Day

```bash
# These are the ones you use EVERY DAY:

docker ps                    # whats running?
docker ps -a                 # all containers
docker logs -f myapp         # watch logs
docker exec -it myapp bash   # go inside
docker stop myapp            # stop it
docker rm myapp              # delete it
docker images                # list images
docker rmi myapp             # delete image
docker system prune          # clean up
docker compose up -d         # start project
docker compose down          # stop project
docker compose logs -f       # watch logs
