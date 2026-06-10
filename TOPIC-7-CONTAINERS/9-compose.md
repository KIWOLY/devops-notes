# Docker Compose

---

## What is Docker Compose?

> **Docker Compose** is a tool that lets you define and run **multi-container Docker applications** using a single YAML file.

### Simple Analogy

```
Docker Compose     =  Recipe book for your whole app
Each docker run    =  One recipe step
docker-compose.yml =  The complete recipe book
docker compose up  =  Cook the entire meal at once
```

---

## The Problem it Solves

**Without Docker Compose** — 4 long commands, easy to make mistakes, hard to share:

```bash
docker run -d --name postgres --network appnet \
  -e POSTGRES_PASSWORD=secret \
  -v pgdata:/var/lib/postgresql/data \
  -p 5432:5432 postgres:15

docker run -d --name redis --network appnet \
  -v redisdata:/data redis:7

docker run -d --name api --network appnet \
  -e DB_HOST=postgres -e REDIS_HOST=redis \
  -p 3000:3000 myapi

docker run -d --name frontend --network appnet \
  -e API_URL=http://api:3000 \
  -p 80:80 myfrontend
```

**With Docker Compose** — one command starts everything:

```bash
docker compose up -d   # ✅
```

---

## `docker-compose.yml` Structure

```yaml
version: "3.9"          # compose file version

services:               # your containers
  app:                  # service name
    image: node:20      # image to use
    ports:
      - "3000:3000"     # port mapping
    environment:
      - NODE_ENV=prod   # env variables
    volumes:
      - .:/app          # volumes
    networks:
      - appnet          # networks
    depends_on:
      - postgres        # start order

  postgres:             # another service
    image: postgres:15

networks:               # define networks
  appnet:

volumes:                # define volumes
  pgdata:
```

---

## Every Key Explained

---

### `services`

```yaml
services:
  # Each key = one container
  # Container name will be: projectname-frontend-1
  frontend:
    ...
  api:
    ...
  database:
    ...
```

---

### `image` vs `build`

```yaml
services:
  # Use an existing image from a registry
  database:
    image: postgres:15

  # Build from your Dockerfile
  api:
    build: .                        # build from current directory

  # Build with more options
  frontend:
    build:
      context: .
      dockerfile: Dockerfile.prod
      args:
        NODE_ENV: production
```

---

### `ports`

```yaml
services:
  app:
    ports:
      - "3000:3000"   # HOST:CONTAINER
      - "8080:80"
      - "443:443"
```

---

### `environment`

```yaml
services:
  app:
    # List format
    environment:
      - NODE_ENV=production
      - DB_HOST=postgres
      - DB_PORT=5432

    # Map format
    environment:
      NODE_ENV: production
      DB_HOST: postgres
      DB_PORT: 5432

    # Load from .env file
    env_file:
      - .env
      - .env.production
```

---

### `volumes`

```yaml
services:
  app:
    volumes:
      - ./src:/app/src           # bind mount
      - ./config:/app/config:ro  # bind mount — read-only
      - appdata:/app/data        # named volume
      - /app/node_modules        # anonymous volume

  postgres:
    volumes:
      - pgdata:/var/lib/postgresql/data

# Declare named volumes at the top level
volumes:
  pgdata:
  appdata:
```

---

### `networks`

```yaml
services:
  frontend:
    networks:
      - frontend_net
      - backend_net       # connected to both

  api:
    networks:
      - frontend_net
      - backend_net

  postgres:
    networks:
      - backend_net       # backend only — more secure ✅

networks:
  frontend_net:
    driver: bridge
  backend_net:
    driver: bridge
```

---

### `depends_on`

| | Without `depends_on` | With `depends_on` + healthcheck |
|---|---|---|
| Startup | All containers start at the same time | Postgres starts first, API waits until it's healthy |
| Risk | API may start before DB is ready ❌ | API starts only when DB is healthy ✅ |

```yaml
services:
  api:
    depends_on:
      postgres:
        condition: service_healthy   # wait until healthy ✅

  postgres:
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "postgres"]
      interval: 5s
      timeout: 5s
      retries: 5
```

---

### `restart`

| Policy | Behaviour | Best For |
|--------|-----------|---------|
| `no` | Never restart (default) | Development |
| `always` | Always restart | Production ✅ |
| `on-failure` | Only restart if it crashes | Background workers |
| `unless-stopped` | Restart unless manually stopped | Long-running services |

```yaml
services:
  app:
    restart: always
```

---

### `healthcheck`

```yaml
services:
  api:
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s      # check every 30 seconds
      timeout: 10s       # wait 10s for response
      retries: 3         # fail after 3 attempts
      start_period: 40s  # wait 40s before first check
```

---

### `deploy` — Resource Limits

```yaml
services:
  app:
    deploy:
      resources:
        limits:
          cpus: "0.5"     # max 50% of one CPU
          memory: 512M    # max 512 MB RAM
        reservations:
          cpus: "0.25"    # guaranteed 25% CPU
          memory: 256M    # guaranteed 256 MB RAM
```

---

## Real-World Examples

### Node.js + PostgreSQL + Redis

```yaml
services:

  # Frontend
  frontend:
    build:
      context: ./frontend
    ports:
      - "80:80"
    environment:
      - API_URL=http://api:3000
    depends_on:
      - api
    networks:
      - frontend_net
    restart: always

  # API
  api:
    build:
      context: ./api
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DB_HOST=postgres
      - DB_PORT=5432
      - DB_NAME=mydb
      - DB_PASSWORD=secret
      - REDIS_HOST=redis
      - REDIS_PORT=6379
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_healthy
    networks:
      - frontend_net
      - backend_net
    restart: always
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s
      timeout: 10s
      retries: 3

  # PostgreSQL
  postgres:
    image: postgres:15-alpine
    environment:
      - POSTGRES_USER=admin
      - POSTGRES_PASSWORD=secret
      - POSTGRES_DB=mydb
    volumes:
      - pgdata:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    networks:
      - backend_net
    restart: always
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "admin"]
      interval: 5s
      timeout: 5s
      retries: 5

  # Redis
  redis:
    image: redis:7-alpine
    volumes:
      - redisdata:/data
    networks:
      - backend_net
    restart: always
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 5s
      timeout: 5s
      retries: 5

networks:
  frontend_net:
    driver: bridge
  backend_net:
    driver: bridge

volumes:
  pgdata:
  redisdata:
```

---

### Development Setup

```yaml
# docker-compose.dev.yml

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile.dev
    ports:
      - "3000:3000"
    volumes:
      - .:/app               # live code updates ✅
      - /app/node_modules    # keep container's node_modules
    environment:
      - NODE_ENV=development
    command: npm run dev     # override CMD from Dockerfile
    restart: unless-stopped

  postgres:
    image: postgres:15-alpine
    environment:
      - POSTGRES_PASSWORD=secret
      - POSTGRES_DB=devdb
    ports:
      - "5432:5432"          # expose for local DB tools ✅
    volumes:
      - pgdata_dev:/var/lib/postgresql/data

volumes:
  pgdata_dev:
```

---

### Using a `.env` File

```bash
# .env
POSTGRES_PASSWORD=supersecret
POSTGRES_DB=mydb
NODE_ENV=production
API_PORT=3000
```

```yaml
# docker-compose.yml
services:
  api:
    ports:
      - "${API_PORT}:3000"
    environment:
      - NODE_ENV=${NODE_ENV}
      - DB_PASSWORD=${POSTGRES_PASSWORD}

  postgres:
    image: postgres:15
    environment:
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
      - POSTGRES_DB=${POSTGRES_DB}
```

---

## All Docker Compose Commands

```bash
# Start & Stop
docker compose up                  # start all services
docker compose up -d               # start in background
docker compose up --build          # build then start
docker compose up api              # start one service only
docker compose down                # stop and remove containers
docker compose down -v             # stop and remove volumes too
docker compose down --rmi all      # stop and remove images too

# Build
docker compose build               # build all images
docker compose build api           # build one service
docker compose build --no-cache    # build without cache

# Logs
docker compose logs                # see all logs
docker compose logs -f             # follow logs live
docker compose logs -f api         # follow one service
docker compose logs --tail 50      # last 50 lines

# Container Management
docker compose ps                  # list running services
docker compose exec api bash       # go inside a container
docker compose exec api sh         # alpine containers
docker compose run api node migrate.js   # run one-off command
docker compose restart api         # restart a service
docker compose stop api            # stop a service
docker compose start api           # start a stopped service

# Scaling
docker compose up -d --scale api=3 # run 3 instances of api

# Other
docker compose top                 # see running processes
docker compose pull                # pull latest images
docker compose config              # validate compose file
docker compose images              # list images
```

---

## Multiple Compose Files

```bash
# Development
docker compose -f docker-compose.yml \
               -f docker-compose.dev.yml \
               up -d

# Production
docker compose -f docker-compose.yml \
               -f docker-compose.prod.yml \
               up -d
```

> Later files **override** values from earlier files — great for environment-specific config.

---

## Startup Order — Full Flow

```
docker compose up -d
        ↓
Docker reads docker-compose.yml
        ↓
Creates networks
        ↓
Creates volumes
        ↓
Starts services respecting depends_on
        ↓
postgres starts → healthcheck passes ✅
redis starts    → healthcheck passes ✅
        ↓
api starts (depends on postgres + redis)
        ↓
frontend starts (depends on api)
        ↓
Everything running ✅
```

---

## Key Takeaway

`docker-compose.yml` defines your **entire application** in one file:

| Key | What it defines |
|-----|----------------|
| `services` | Your containers |
| `networks` | How they connect |
| `volumes` | Where data lives |
| `environment` | Configuration |

```bash
docker compose up -d    # start everything ✅
docker compose down     # stop everything  ✅
docker compose logs -f  # watch everything ✅
```

> One file = entire app definition.
> One command = entire app running.
> This is why every real project uses Docker Compose.
