# Docker Bind Mounts & Volumes

---

## The Problem

> Containers are **ephemeral** by default — all data is lost when a container is removed.

```
docker run myapp       # container starts, writes data to /app/data
docker rm myapp        # container removed → ALL data gone forever

Database removed  =  all your data lost
Log files         =  gone
User uploads      =  gone
```

---

## Three Ways to Store Data

| Type | Where Data Lives | Best For |
|------|-----------------|---------|
| **Bind Mount** | A folder you choose on your host machine | Development |
| **Volume** | Docker-managed storage on host | Production & databases |
| **tmpfs** | Memory only — temporary | Sensitive/ephemeral data |

---

## Part 1 — Bind Mount

---

### What is a Bind Mount?

> Mount a **specific folder from your host machine** directly into the container.
> Changes on the host are instantly seen in the container, and vice versa.

### How it Works

```
Your Machine                    Container
/home/user/myapp    ←──────→   /app
        │                          │
        └──── same files ──────────┘

You edit file on host   → container sees it instantly
Container writes file   → you see it on host instantly
```

---

### Bind Mount Commands

```bash
# Basic syntax: -v HOST_PATH:CONTAINER_PATH
docker run -v /host/path:/container/path myapp

# Mount current directory
docker run -v $(pwd):/app myapp

# Mount source code for live development
docker run -d \
  -v $(pwd):/app \
  -p 3000:3000 \
  node:20

# Mount a specific config file
docker run -d \
  -v $(pwd)/nginx.conf:/etc/nginx/nginx.conf \
  nginx

# Read-only mount (container cannot write)
docker run -d \
  -v $(pwd):/app:ro \
  myapp
```

### New Syntax (`--mount`)

```bash
# Old way (-v) — shorthand
docker run -v $(pwd):/app myapp

# New way (--mount) — more explicit and readable
docker run --mount \
  type=bind,\
  source=$(pwd),\
  target=/app \
  myapp
```

---

### Best Use Case — Local Development

| | Without Bind Mount | With Bind Mount |
|---|---|---|
| Dev cycle | Edit → rebuild image → restart container | Edit → container sees change instantly |
| Speed | Slow (minutes per change) | Instant ✅ |

```bash
# Perfect local dev setup
docker run -d \
  --name myapp \
  -v $(pwd):/app \           # your code mounted live
  -v /app/node_modules \     # keep container's node_modules
  -p 3000:3000 \
  -e NODE_ENV=development \
  node:20 \
  npm run dev                # nodemon watches for changes
```

---

## Part 2 — Volumes

---

### What is a Volume?

> A **Docker-managed** storage area on your host machine.
> Docker controls where it lives.
> Persists even when the container is removed.
> Best for production data.

### Simple Analogy

```
Bind Mount = You own the garage, Docker parks there
Volume     = Docker owns the managed car park, you just use it
```

### How Volumes Work

```
/var/lib/docker/volumes/
├── myvolume/
│   └── _data/          ← actual data lives here
├── pgdata/
│   └── _data/
└── redisdata/
    └── _data/

Docker manages this location — you don't need to know where it is.
```

---

### Volume Commands

```bash
# Create a volume
docker volume create myvolume

# List all volumes
docker volume ls

# Inspect a volume (see where data lives)
docker volume inspect myvolume

# Remove a volume
docker volume rm myvolume

# Remove all unused volumes
docker volume prune
```

---

### Using Volumes with Containers

```bash
# Named volume — Docker manages it
docker run -d \
  --name postgres \
  -v pgdata:/var/lib/postgresql/data \
  postgres:15

# Anonymous volume — Docker generates a random name
docker run -d \
  -v /app/data \
  myapp

# New syntax (--mount)
docker run -d \
  --mount type=volume,source=pgdata,target=/var/lib/postgresql/data \
  postgres:15
```

---

### Real-World Volume Examples

```bash
# PostgreSQL — data persists across restarts
docker run -d \
  --name postgres \
  -e POSTGRES_PASSWORD=secret \
  -e POSTGRES_DB=mydb \
  -p 5432:5432 \
  -v pgdata:/var/lib/postgresql/data \
  postgres:15

# Redis — persist cache data
docker run -d \
  --name redis \
  -v redisdata:/data \
  redis:7

# MongoDB
docker run -d \
  --name mongo \
  -v mongodata:/data/db \
  mongo:6

# Remove containers — data is still safe ✅
docker rm postgres
docker rm redis

# Restart with the same volume — data comes back ✅
docker run -d \
  -v pgdata:/var/lib/postgresql/data \
  postgres:15
```

---

### Share a Volume Between Containers

```bash
# Create shared volume
docker volume create shareddata

# Container 1 writes data
docker run -d --name writer -v shareddata:/data myapp

# Container 2 reads the same data
docker run -d --name reader -v shareddata:/data myapp

# Both containers see the same files ✅
```

---

## Bind Mount vs Volume — Comparison

| | Bind Mount | Volume |
|---|---|---|
| **Who manages location** | You | Docker |
| **Path** | Any path you choose | Docker controlled |
| **Best for** | Development | Production |
| **Persists after `docker rm`** | ✅ Yes (it's your folder) | ✅ Yes |
| **Share between containers** | ✅ Yes | ✅ Yes |
| **Performance** | Good | Better |
| **Backup** | Manual | `docker volume` commands |
| **Portable** | ❌ Path dependent | ✅ Yes |

---

## In Docker Compose

```yaml
services:
  app:
    image: node:20
    volumes:
      - .:/app                    # bind mount — live dev code
      - /app/node_modules         # anonymous volume

  postgres:
    image: postgres:15
    volumes:
      - pgdata:/var/lib/postgresql/data    # named volume

  nginx:
    image: nginx
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro  # bind mount, read-only

volumes:
  pgdata:    # declare named volumes here
```

---

## Common Patterns

### Development — Bind Mount

```bash
docker run -d \
  -v $(pwd):/app \
  -p 3000:3000 \
  node:20 npm run dev    # hot reload works ✅
```

### Production — Volume

```bash
docker run -d \
  -v pgdata:/var/lib/postgresql/data \
  postgres:15            # data is persistent ✅
```

### Hybrid — Both Together

```bash
docker run -d \
  -v $(pwd)/config:/app/config \   # bind mount — your config file
  -v appdata:/app/data \           # volume — persistent app data
  myapp
```

---

## Backup & Restore a Volume

```bash
# Backup volume to a tar file
docker run --rm \
  -v pgdata:/data \
  -v $(pwd):/backup \
  alpine \
  tar czf /backup/pgdata-backup.tar.gz /data

# Restore from backup
docker run --rm \
  -v pgdata:/data \
  -v $(pwd):/backup \
  alpine \
  tar xzf /backup/pgdata-backup.tar.gz -C /
```

---

## Key Takeaway

**Bind Mount:**
- You pick the folder — host and container share it
- Perfect for development — live code updates with no rebuild

**Volume:**
- Docker manages the storage — data survives container removal
- Perfect for databases and production data
- Easy to backup and share between containers

**Rule of thumb:**

| Use Case | Storage Type |
|----------|-------------|
| Local development | Bind Mount |
| Production app data | Volume |
| Database data | Always Volume ✅ |
