
Docker Engine
What is Docker Engine?
Docker Engine is the core of Docker. It is the underlying technology that actually creates, runs, and manages containers.



Everything you do with Docker — building images, running containers, networking — goes through Docker Engine.




Docker Engine Architecture
┌─────────────────────────────────────────────┐
│              Docker Engine                  │
│                                             │
│  ┌─────────────┐                            │
│  │  Docker CLI │  ← You type commands here  │
│  └──────┬──────┘                            │
│         │ REST API                          │
│  ┌──────▼──────┐                            │
│  │  Docker     │  ← Brain of Docker         │
│  │  Daemon     │    (dockerd)               │
│  │  (dockerd)  │                            │
│  └──────┬──────┘                            │
│                              │
└─────────────────────────────────────────────┘

1.  Docker CLI

docker run nginx
docker build .
docker ps
What you interact with
Just a client that sends commands
Talks to Docker Daemon via REST API



2.  Docker Daemon (dockerd)

The main background process
Always running on your machine
Receives commands from CLI
Manages images, containers, networks, volumes
The real "brain" of Docker


In short: Docker Desktop = Docker Engine + graphical tools + developer features. Docker Engine = the core container runtime used on servers.




What is a Registry?

A registry is a storage and distribution system for Docker images.
Think of it as a server where images live — you push images to it and pull images from it.

Registry  =  GitHub        (stores and shares code)
Image     =  Repository    (a specific project)
Tag       =  Branch/Release (version of that project)

Just like GitHub stores code,
Registry stores Docker Images



You write         You build          You push
Dockerfile   →    Image locally  →   to Registry
                                          ↓
                                   Anyone can pull
                                   and run it anywhere


Types of Registries
┌─────────────────────────────────────────────────────┐
│                   Registries                        │
│                                                     │
│  Public              Private          Local         │
│  ┌───────────────┐  ┌─────────────┐  ┌──────────┐  │
│  │ Docker Hub    │  │ AWS ECR     │  │ Your own │  │
│  │ (default)     │  │ Google GCR  │  │ registry │  │
│  │               │  │ GitHub GHCR │  │ on a     │  │
│  │ Free & public │  │ Azure ACR   │  │ server   │  │
│  │ Anyone access │  │ GitLab      │  │          │  │
│  └───────────────┘  └─────────────┘  └──────────┘  │
│                      Company images                 │
│                      Private & secure               │
└─────────────────────────────────────────────────────┘



What is Docker Hub?

Docker Hub is the official public registry made by Docker.
It is the default registry — when you do docker pull nginx, it pulls from Docker Hub automatically.


Docker Hub Structure
┌─────────────────────────────────────────────┐
│               Docker Hub                    │
│                                             │
│  Official Images      Community Images      │
│  ┌──────────────┐    ┌──────────────────┐   │
│  │ nginx        │    │ username/myapp   │   │
│  │ postgres     │    │ username/api     │   │
│  │ redis        │    │ company/backend  │   │
│  │ node         │    │ myteam/frontend  │   │
│  │ python       │    └──────────────────┘   │
│  │ ubuntu       │                           │
│  └──────────────┘                           │
└─────────────────────────────────────────────┘



# Build
docker build -t myapp .

# Login
docker login

# Tag
docker tag <local-image> <dockerhub-username>/<repository>:<tag>

docker tag myapp kiwoly/myapp:v1

# Push
docker push kiwoly/myapp:v1

# Pull (on another machine)
docker pull kiwoly/myapp:v1


What is systemd?

It is the first process that starts after the Linux kernel boots and has Process ID (PID) 1. Its job is to initialize the system and manage services and processes.


systemd starts essential services such as:

Network services
SSH server
Docker
Databases
Web servers


systemctl is a command-line tool used to manage system services and the system state on Linux systems that use systemd as their init system.

Think of systemctl as the command you use to:

Start services
Stop services
Restart services
Check service status
Enable services to start automatically at boot
Disable services


Example: Managing Docker

After installing Docker Engine on Ubuntu:

sudo systemctl start docker
sudo systemctl enable docker
sudo systemctl status docker