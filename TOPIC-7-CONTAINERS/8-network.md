# Docker Networking

---

## The Problem

```
You have 3 containers running:

┌──────────┐    ┌──────────┐    ┌──────────┐
│  React   │    │  Node    │    │ Postgres │
│ Frontend │    │  API     │    │ Database │
└──────────┘    └──────────┘    └──────────┘

How do they talk to each other?
How does the frontend reach the API?
How does the API reach the database?
```

> This is what **Docker Networking** solves.

---

## Docker Network Types

| Driver | Isolation | Description |
|--------|-----------|-------------|
| **bridge** (default) | Partial | Isolated network — containers talk via IP |
| **bridge** (custom) | Full + DNS | Containers talk by name — recommended ✅ |
| **host** | None | Container shares the host machine's network |
| **none** | Complete | No network at all |
| **overlay** | Cross-host | Multi-host — used with Swarm / Kubernetes |

---

## 1. Bridge Network (Default)

> The **default network** every container joins. Containers can talk via IP but are isolated from the host network.

```
┌────────────────────────────────────────┐
│           Bridge Network               │
│           172.17.0.0/16                │
│                                        │
│  ┌──────────────┐  ┌──────────────┐   │
│  │ Container 1  │  │ Container 2  │   │
│  │ 172.17.0.2   │  │ 172.17.0.3   │   │
│  └──────┬───────┘  └──────┬───────┘   │
│         │                 │           │
│  ╔══════▼═════════════════▼════════╗  │
│  ║         docker0 bridge          ║  │
│  ╚═════════════════════════════════╝  │
└────────────────────────────────────────┘
         │
    Host Machine
```

### Default Bridge — Problem

```bash
docker run -d --name app1 myapp
docker run -d --name app2 myapp

# app2 reaching app1 by IP works
# BUT IP can change — unreliable ❌
# Cannot use container NAME to reach each other ❌
```

### Custom Bridge — Solution

```bash
# Create a custom bridge network
docker network create mynetwork

# Run containers on the same network
docker run -d --name api      --network mynetwork myapi
docker run -d --name database --network mynetwork postgres:15

# Now api can reach database by NAME ✅
# ping database       ✅ works
# curl database:5432  ✅ works
```

**Why custom bridge is better:**

- Containers talk by name (built-in DNS)
- Isolated from other networks
- Better security
- Recommended for all apps ✅

---

## 2. Host Network

> Container shares the **host machine's network** directly. No isolation — it uses host ports directly.

```bash
docker run -d --network host nginx
# No port mapping needed — nginx uses host port 80 directly
```

**When to use:**
- Maximum network performance needed
- Container needs full host network access
- Running network monitoring tools

> Not recommended for production. ❌

---

## 3. None Network

> Container has **no network at all** — completely isolated.

```bash
docker run -d --network none myapp
# Cannot reach internet        ❌
# Cannot reach other containers ❌
# Cannot reach host             ❌
```

**When to use:**
- Maximum security required
- Batch processing jobs with no network needs

---

## 4. Overlay Network

> Connect containers across **multiple host machines**. Used with Docker Swarm / Kubernetes.

```
┌──────────────┐          ┌──────────────┐
│    Host 1    │          │    Host 2    │
│              │          │              │
│ ┌──────────┐ │          │ ┌──────────┐ │
│ │Container │ │◄────────►│ │Container │ │
│ └──────────┘ │  overlay │ └──────────┘ │
└──────────────┘  network └──────────────┘
```

**When to use:**
- Running Docker Swarm
- Multiple host machines
- Distributed applications

---

## Network Commands

```bash
# List all networks
docker network ls

# Create a network
docker network create mynetwork

# Create with a specific subnet
docker network create \
  --subnet 192.168.1.0/24 \
  mynetwork

# Inspect network (see containers connected)
docker network inspect mynetwork

# Connect a running container to a network
docker network connect mynetwork mycontainer

# Disconnect a container from a network
docker network disconnect mynetwork mycontainer

# Remove a network
docker network rm mynetwork

# Remove all unused networks
docker network prune
```

---

## Port Mapping

```
Outside World          Host Machine         Container
Browser                Port 8080      →     Port 3000
curl localhost:8080 →  Docker maps    →     App listens
                       8080 to 3000
```

```bash
# Syntax: -p HOST_PORT:CONTAINER_PORT
docker run -p 8080:3000 myapp

# Map multiple ports
docker run \
  -p 8080:80 \
  -p 443:443 \
  nginx

# Bind to a specific host IP (localhost only)
docker run -p 127.0.0.1:8080:3000 myapp

# Let Docker pick a random host port
docker run -p 3000 myapp
docker port myapp    # check what port was assigned
```

---

## Container Communication

### Same Network — Use Container Name

```bash
# Create network
docker network create appnet

# Start database
docker run -d \
  --name postgres \
  --network appnet \
  -e POSTGRES_PASSWORD=secret \
  postgres:15

# Start API — reaches postgres by name ✅
docker run -d \
  --name api \
  --network appnet \
  -e DB_HOST=postgres \
  -e DB_PORT=5432 \
  myapi
```

Inside the `api` container:

```bash
ping postgres       # ✅ works
curl postgres:5432  # ✅ works
psql -h postgres    # ✅ works
```

> Docker has a **built-in DNS server**. Container name = hostname on the same network.

---

### Different Networks — Cannot Talk

```bash
docker network create network1
docker network create network2

docker run -d --name app1 --network network1 myapp
docker run -d --name app2 --network network2 myapp

# app1 CANNOT reach app2 ❌ — different networks = isolated (good for security)

# To allow communication, connect app2 to network1:
docker network connect network1 app2
# Now app1 and app2 can talk ✅
```

---

## Real-World Setup — Frontend + API + Database

```bash
# Step 1 — Create network
docker network create appnet

# Step 2 — Start database (NOT exposed to outside)
docker run -d \
  --name postgres \
  --network appnet \
  -e POSTGRES_PASSWORD=secret \
  -e POSTGRES_DB=mydb \
  -v pgdata:/var/lib/postgresql/data \
  postgres:15

# Step 3 — Start API (internal only)
docker run -d \
  --name api \
  --network appnet \
  -e DB_HOST=postgres \
  -e DB_PORT=5432 \
  -p 3000:3000 \
  myapi

# Step 4 — Start Frontend (exposed to internet)
docker run -d \
  --name frontend \
  --network appnet \
  -e API_URL=http://api:3000 \
  -p 80:80 \
  myfrontend
```

```
Internet
   │
   ▼
port 80 (frontend)
   │
   ▼  appnet (internal)
frontend ──► api:3000 ──► postgres:5432

Only frontend is exposed to the internet ✅
API and database are hidden inside the network ✅
```

---

## Docker Compose Networking

Compose **automatically creates a network** for all services — no manual setup needed.

```yaml
services:
  frontend:
    image: myfrontend
    ports:
      - "80:80"
    networks:
      - appnet

  api:
    image: myapi
    ports:
      - "3000:3000"
    environment:
      - DB_HOST=postgres    # use service name ✅
    networks:
      - appnet

  postgres:
    image: postgres:15
    environment:
      - POSTGRES_PASSWORD=secret
    volumes:
      - pgdata:/var/lib/postgresql/data
    networks:
      - appnet
    # no ports: — not exposed to outside ✅

networks:
  appnet:
    driver: bridge

volumes:
  pgdata:
```

```bash
# One command starts everything with networking configured ✅
docker compose up -d
```

---

## DNS Resolution in Docker

| | Behaviour |
|---|---|
| Container name | Automatically becomes its hostname |
| Service name (Compose) | Automatically becomes its hostname |
| IP addresses | Change — never hardcode them |

```
api       →  hostname: api
postgres  →  hostname: postgres
redis     →  hostname: redis
```

> Any container on the same **custom** network can reach others by name — no need to hardcode IPs.

---

## Inspect a Network

```bash
docker network inspect appnet
```

```json
{
  "Name": "appnet",
  "Driver": "bridge",
  "Subnet": "172.18.0.0/16",
  "Containers": {
    "frontend": { "IPv4Address": "172.18.0.2/16" },
    "api":      { "IPv4Address": "172.18.0.3/16" },
    "postgres": { "IPv4Address": "172.18.0.4/16" }
  }
}
```

---

## Network Summary

| Network | Isolation | Best For |
|---------|-----------|---------|
| bridge (default) | Partial | Quick testing |
| bridge (custom) | Full + DNS | Production apps ✅ |
| host | None | Max performance / monitoring |
| none | Complete | Batch jobs, high security |
| overlay | Cross-host | Docker Swarm / Kubernetes |

---

## Key Takeaway

| Scenario | What to Use |
|----------|------------|
| Containers need to talk by name | Custom bridge network ✅ |
| Database — hide from internet | No port mapping, internal network only |
| Maximum performance | Host network |
| No network needed | None |
| Multi-host setup | Overlay |

**Rules:**
- Always create a **custom bridge network** — never rely on the default
- Use **container/service names**, not IPs
- Only expose ports that truly need outside access
- Keep databases on the internal network only
- Use **Docker Compose** — it handles networking automatically
