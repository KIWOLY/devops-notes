# Introduction to Containers

---

## Virtualization

Virtualization is the technology that lets you run **multiple isolated computing environments** on a single physical machine. Instead of needing separate hardware for each system, you simulate it in software.

```
Physical Hardware (CPU, RAM, Disk, Network)
        ↓
  Virtualization Layer
        ↓
Multiple isolated environments running simultaneously
```

---

## VM vs Container

### Architecture Diagram

```
┌─────────────────────────────┐    ┌─────────────────────────────┐
│        VIRTUAL MACHINE      │    │          CONTAINER          │
├───────────┬─────────────────┤    ├───────────┬─────────────────┤
│  App A    │   App B         │    │  App A    │   App B         │
├───────────┴─────────────────┤    ├───────────┴─────────────────┤
│  Guest OS │   Guest OS      │    │  Libs     │   Libs          │
├───────────┴─────────────────┤    ├───────────────────────────  ┤
│        Hypervisor           │    │  Container Runtime (Docker) │
├─────────────────────────────┤    ├─────────────────────────────┤
│        Host OS              │    │          Host OS            │
├─────────────────────────────┤    ├─────────────────────────────┤
│   Physical Hardware         │    │      Physical Hardware      │
└─────────────────────────────┘    └─────────────────────────────┘
```

### Side-by-Side Comparison

| Feature | Virtual Machine (VM) | Container |
|---------|---------------------|-----------|
| Includes OS? | Full Guest OS | Shares Host OS kernel |
| Size | GBs (heavy) | MBs (lightweight) |
| Startup time | Minutes | Seconds |
| Isolation | Very strong (separate kernel) | Good (process-level) |
| Performance | Slightly slower (overhead) | Near-native speed |
| Portability | Less portable | Highly portable |

---

## Docker Container

A Docker container is a **lightweight, isolated, runnable instance** of a Docker image. Think of it as a running process that has everything it needs bundled inside it.

> You can make many containers from the same image, just like cooking the same recipe multiple times.

### How It Works

```
Dockerfile  →  docker build  →  Image  →  docker run  →  Container
(blueprint)                   (template)               (running process)
```

### What's Inside a Container

```
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
│   Shares Host OS Kernel         │
└─────────────────────────────────┘
```

### Key Properties

| Property | Description |
|----------|-------------|
| **Isolated** | Has its own filesystem, network, and process space |
| **Lightweight** | Shares OS kernel — no full OS needed |
| **Portable** | Runs the same on any machine with Docker |
| **Ephemeral** | Temporary by default — data lost when removed |
| **Fast** | Starts in seconds |

---

## Docker — Tool and Platform

Docker took powerful but complex container technology and made it **simple enough for any developer to use**, then built a complete ecosystem around it.

### Docker as a Tool

```bash
docker build .
docker run myapp
docker push myapp
```

Just a CLI tool on your machine that manages containers.

### Docker as a Platform

```
┌─────────────────────────────────────────────┐
│              Docker Platform                │
│                                             │
│  Docker Engine   → runs containers          │
│  Dockerfile      → standard build format   │
│  Docker Hub      → share & find images     │
│  Docker Compose  → run multi containers    │
│  Docker Desktop  → GUI for Mac/Windows     │
└─────────────────────────────────────────────┘
```

---

## What is a Builder?

A builder is the component responsible for **building Docker images** from your Dockerfile.

```
You write Dockerfile
       ↓
Builder reads it
       ↓
Builder executes each instruction
       ↓
Produces a Docker Image
```

---

## Files & Folders in Container Base Images

| Path | Contents |
|------|---------|
| `/bin` | Binary executables (`ls`, `cp`, `ps`) |
| `/sbin` | System binary executables (`init`, `shutdown`) |
| `/etc` | Configuration files for system services |
| `/lib` | Library files used by binaries |
| `/usr` | User-related files, applications, libraries, documentation |
| `/var` | Variable data — log files, spool files, temporary files |
| `/root` | Home directory of the root user |

---

## What Containers Use from the Host OS

| Resource | How It's Used |
|----------|--------------|
| **Host file system** | Via bind mounts — container reads/writes host files |
| **Networking stack** | Host network provides connectivity to the container |
| **System calls** | Host kernel handles system calls from the container |
| **Namespaces** | Create isolated environments for filesystem, PID, network |
| **Cgroups** | Limit and control CPU, memory, and I/O per container |

---

## Resource Usage: Stopped vs Running

```
Container STOPPED  →  0 CPU, 0 RAM   (only disk space for image layers)
Container RUNNING  →  CPU + RAM actively used
```

### Stopped Container

```
┌─────────────────────────────┐
│      Stopped Container      │
│                             │
│  CPU usage    →  0%         │
│  RAM usage    →  0 MB       │
│  Network      →  nothing    │
│  Disk         →  ✅ Yes     │
│               (image stored)│
└─────────────────────────────┘
```

### Running Container

```
┌─────────────────────────────┐
│      Running Container      │
│                             │
│  CPU usage    →  ✅ Yes     │
│  RAM usage    →  ✅ Yes     │
│  Network      →  ✅ Yes     │
│  Disk I/O     →  ✅ Yes     │
└─────────────────────────────┘
```
