Virtualization

Virtualization is the technology that lets you run multiple isolated computing environments on a single physical machine. Instead of needing separate hardware for each system, you simulate it in software.

How it works
Physical Hardware (CPU, RAM, Disk, Network)
        ↓
  Virtualization Layer
        ↓
Multiple isolated environments running simultaneously


VM vs Container
Architecture Diagram
┌─────────────────────────────┐    ┌─────────────────────────────┐
│        VIRTUAL MACHINE      │    │          CONTAINER          │
├───────────┬─────────────────┤    ├───────────┬─────────────────┤
│  App A    │   App B         │    │  App A    │   App B         │
├───────────┴─────────────────┤    ├───────────┴─────────────────┤
│  Guest OS │   Guest OS      │    │  Libs     │   Libs          │
├───────────┴─────────────────┤    ├───────────────────────────  ┤
│        Hypervisor           │    │   Container Runtime(Docker) │
├─────────────────────────────┤    ├─────────────────────────────┤
│        Host OS              │    │          Host OS            │
├─────────────────────────────┤    ├─────────────────────────────┤
│   Physical Hardware         │    │      Physical Hardware      │
└─────────────────────────────┘    └─────────────────────────────┘



Docker Container
A Docker container is a lightweight, isolated, runnable instance of a Docker image. Think of it as a running process that has everything it needs bundled inside it.



You can make many containers from the same image, just like cooking the same recipe multiple times.

How it works
Dockerfile  →  docker build  →  Image  →  docker run  →  Container
(blueprint)                   (template)               (running process)


What's inside a Container?
┌─────────────────────────────────┐
│           CONTAINER             │
│  ┌───────────────────────────┐  │
│  │      Your App / Code      │  │
│  ├───────────────────────────┤  │
│  │  Dependencies / Libraries │  │
│  ├───────────────────────────┤  │
│  │   Config / Env Variables  │  │
│  ├───────────────────────────┤  │
│  │      File System          │  │
│  └───────────────────────────┘  │
│                                 │
│   Shares Host OS Kernel ↓       │
└─────────────────────────────────┘

Key Properties
Property	             Description
Isolated	            Has its own filesystem, network, process space
Lightweight          	Shares OS kernel, no full OS needed
Portable	            Runs the same on any machine with Docker
Ephemeral	            Temporary by default — data lost when removed
Fast	                 Starts in seconds

Side-by-side Comparison
Feature          	Virtual Machine (VM)                        	Container
Includes OS?	     Full Guest OS	                         Shares Host OS kernel
Size	              GBs (heavy)	                         MBs (lightweight)
Startup time	      Minutes	                             Seconds
Isolation	         Very strong (separate kernel)           Good (process-level)
Performance	          Slightly slower (overhead)	         Near-native speed
Portability          	Less portable	                      Highly portable



Docker is famous because it took powerful but complex container technology and made it simple enough for any developer to use — then built a complete ecosystem around it that the entire industry adopted.


The Right Way to Think About It

Docker is NOT just a tool and NOT just a platform — it's both together, and that combination is why it won.**

Docker as a Tool 
Simple commands any developer can learn
docker build .
docker run myapp
docker push myapp
Just a CLI tool on your machine that manages containers.



Docker as a Platform 
┌─────────────────────────────────────────────┐
│              Docker Platform                │
│                                             │
│  Docker Engine   → runs containers          │
│  Dockerfile      → standard build format    │
│  Docker Hub      → share & find images      │
│  Docker Compose  → run multi containers     │
│  Docker Desktop  → GUI for Mac/Windows      │
└─────────────────────────────────────────────┘
A complete ecosystem around containers.



What is a Builder?

A builder is the component responsible for building Docker images from your Dockerfile.


Think of it simply:

You write Dockerfile
       ↓
Builder reads it
       ↓
Builder executes each instruction
       ↓
Produces a Docker Image



Files and Folders in containers base images

    /bin: contains binary executable files, such as the ls, cp, and ps commands.

    /sbin: contains system binary executable files, such as the init and shutdown commands.

    /etc: contains configuration files for various system services.

    /lib: contains library files that are used by the binary executables.

    /usr: contains user-related files and utilities, such as applications, libraries, and documentation.

    /var: contains variable data, such as log files, spool files, and temporary files.

    /root: is the home directory of the root user.


Files and Folders that containers use from host operating system

    The host's file system: Docker containers can access the host file system using bind mounts, which allow the container to read and write files in the host file system.

    Networking stack: The host's networking stack is used to provide network connectivity to the container. Docker containers can be connected to the host's network directly or through a virtual network.

    System calls: The host's kernel handles system calls from the container, which is how the container accesses the host's resources, such as CPU, memory, and I/O.

    Namespaces: Docker containers use Linux namespaces to create isolated environments for the container's processes. Namespaces provide isolation for resources such as the file system, process ID, and network.

    Control groups (cgroups): Docker containers use cgroups to limit and control the amount of resources, such as CPU, memory, and I/O, that a container can access.
    





Container STOPPED  →  0 CPU, 0 RAM used        (just disk space for image)
Container RUNNING  →  CPU + RAM actively used

When Container is Stopped
┌─────────────────────────────┐
│      Stopped Container      │
│                             │
│  CPU usage    →  0%         │
│  RAM usage    →  0 MB       │
│  Network      →  nothing    │
│  Disk         →  ✅ Yes     │
│               (image stored)│
└─────────────────────────────┘
Only thing it uses is disk space to store the image layers.

When Container is Running
┌─────────────────────────────┐
│      Running Container      │
│                             │
│  CPU usage    →  ✅ Yes     │
│  RAM usage    →  ✅ Yes     │
│  Network      →  ✅ Yes     │
│  Disk I/O     →  ✅ Yes     │
└─────────────────────────────┘