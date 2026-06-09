# Docker Engine

---

## What is Docker Engine?

Docker Engine is the **core of Docker**. It is the underlying technology that actually creates, runs, and manages containers.

> Everything you do with Docker — building images, running containers, networking — goes through Docker Engine.

---

## Docker Engine Architecture

```
┌─────────────────────────────────────────────┐
│              Docker Engine                  │
│                                             │
│  ┌─────────────┐                            │
│  │  Docker CLI │  ← You type commands here  │
│  └──────┬──────┘                            │
│         │  REST API                         │
│  ┌──────▼──────┐                            │
│  │   Docker    │  ← Brain of Docker         │
│  │   Daemon    │    (dockerd)               │
│  │  (dockerd)  │                            │
│  └─────────────┘                            │
└─────────────────────────────────────────────┘
```

### 1. Docker CLI

What you interact with directly:

```bash
docker run nginx
docker build .
docker ps
```

- Just a **client** that sends commands
- Talks to Docker Daemon via REST API

### 2. Docker Daemon (`dockerd`)

- The main background process — always running on your machine
- Receives commands from the CLI
- Manages images, containers, networks, and volumes
- The real **"brain"** of Docker

> **Docker Desktop** = Docker Engine + graphical tools + developer features  
> **Docker Engine** = the core container runtime used on servers

---

## What is a Registry?

A registry is a **storage and distribution system for Docker images**. Think of it as a server where images live — you push images to it and pull images from it.

```
Registry  =  GitHub         (stores and shares code)
Image     =  Repository     (a specific project)
Tag       =  Branch/Release (version of that project)
```

### The Workflow

```
You write         You build            You push
Dockerfile   →   Image locally   →    to Registry
                                            ↓
                                     Anyone can pull
                                     and run it anywhere
```

### Types of Registries

```
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
```

---

## What is Docker Hub?

Docker Hub is the **official public registry** made by Docker. It is the default registry — when you run `docker pull nginx`, it pulls from Docker Hub automatically.

### Docker Hub Structure

```
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
```

### Pushing an Image to Docker Hub

```bash
# Build
docker build -t myapp .

# Login
docker login

# Tag
docker tag myapp kiwoly/myapp:v1

# Push
docker push kiwoly/myapp:v1

# Pull (on another machine)
docker pull kiwoly/myapp:v1
```

---

## `systemd` — The Init System

`systemd` is the **first process that starts after the Linux kernel boots** — it has Process ID (PID) 1. Its job is to initialize the system and manage services and processes.

### Services `systemd` Starts

- Network services
- SSH server
- Docker
- Databases
- Web servers

---

## `systemctl` — Managing Services

`systemctl` is the command-line tool used to **manage system services** on Linux systems that use `systemd`.

```bash
# Start a service
sudo systemctl start docker

# Stop a service
sudo systemctl stop docker

# Restart a service
sudo systemctl restart docker

# Check service status
sudo systemctl status docker

# Enable service to start automatically at boot
sudo systemctl enable docker

# Disable service from starting at boot
sudo systemctl disable docker
```

### Example: Managing Docker After Install

```bash
sudo systemctl start docker    # start Docker now
sudo systemctl enable docker   # start Docker on every boot
sudo systemctl status docker   # verify it's running
```
