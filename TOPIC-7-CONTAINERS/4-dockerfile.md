# Dockerfile

---

## What is a Dockerfile?

> A **Dockerfile** is a plain text file with instructions that tells Docker **how to build your image** step by step.

### Simple Analogy

```
Dockerfile  =  Recipe
Image       =  Cooked meal
Container   =  Serving of that meal

Recipe tells you:
  - What ingredients (dependencies)
  - How to prepare  (instructions)
  - How to serve    (run command)
```

---

## Dockerfile Structure

```dockerfile
# Every line is an instruction
INSTRUCTION argument

# Example
FROM node:20             # base image
WORKDIR /app             # set working directory
COPY . .                 # copy files
RUN npm install          # execute command during build
CMD ["node", "app.js"]   # start command when container runs
```

---

## Every Instruction Explained

---

### `FROM` — Base Image

```dockerfile
# Always the FIRST instruction
# Every image starts from another image

FROM node:20
FROM python:3.11
FROM ubuntu:22.04
FROM alpine:3.18    # minimal — only 5MB
FROM scratch        # completely empty image
```

> "Start with this as my foundation" — like choosing which OS/platform to build on.

---

### `WORKDIR` — Set Working Directory

```dockerfile
# Sets the directory for all following commands
# Creates it if it doesn't exist — like doing cd /app

WORKDIR /app
```

| | Example |
|---|---|
| ❌ Without WORKDIR | `RUN cd /app && npm install` |
| ✅ With WORKDIR | `WORKDIR /app` then `RUN npm install` |

---

### `COPY` — Copy Files into Image

```dockerfile
# COPY source destination

COPY . .                  # copy everything
COPY package.json .       # copy single file
COPY src/ /app/src/       # copy folder
COPY package*.json /app/  # copy by pattern
```

---

### `ADD` — Like COPY but More Powerful

```dockerfile
ADD archive.tar.gz /app/        # auto-extracts tar files
ADD https://example.com/file .  # downloads from URL
```

> **Best practice:** Use `COPY` for simple file copying. Use `ADD` only when you need auto-extract or URL download.

---

### `RUN` — Execute Commands at Build Time

```dockerfile
# Runs during IMAGE BUILD — each RUN creates a new layer

RUN npm install
RUN apt-get update
RUN pip install -r requirements.txt

# Combine commands to reduce layers
RUN apt-get update && \
    apt-get install -y curl && \
    apt-get clean
```

---

### `CMD` — Default Start Command

```dockerfile
# Runs when CONTAINER STARTS
# Can be overridden — only last CMD counts

CMD ["node", "app.js"]           # ✅ exec form (preferred)
CMD ["python", "main.py"]
CMD ["nginx", "-g", "daemon off;"]
CMD node app.js                  # shell form (avoid)
```

```bash
# CMD can be overridden at runtime:
docker run myapp node other.js
```

---

### `ENTRYPOINT` — Fixed Start Command

```dockerfile
# Like CMD but cannot be easily overridden
# Container ALWAYS runs this

ENTRYPOINT ["node", "app.js"]
```

### CMD vs ENTRYPOINT

| | `CMD` | `ENTRYPOINT` |
|---|---|---|
| Overridable? | ✅ Yes — easily | ❌ No — always runs |
| Use case | Default command | Fixed executable |

**Best combo — use both together:**

```dockerfile
ENTRYPOINT ["node"]   # fixed — always node
CMD ["app.js"]        # default file — can override

# docker run myapp            → runs: node app.js
# docker run myapp other.js   → runs: node other.js
```

---

### `ENV` — Environment Variables

```dockerfile
ENV NODE_ENV=production
ENV PORT=3000
ENV DB_HOST=localhost

# Use in other instructions
ENV APP_DIR=/app
WORKDIR $APP_DIR
```

```bash
# Override at runtime:
docker run -e NODE_ENV=development myapp
```

---

### `ARG` — Build-Time Arguments

```dockerfile
# Only available during BUILD — not when container runs

ARG NODE_VERSION=20
FROM node:${NODE_VERSION}

ARG APP_ENV=production
RUN echo "Building for ${APP_ENV}"
```

```bash
# Pass at build time:
docker build --build-arg NODE_VERSION=18 .
```

### ARG vs ENV

| | `ARG` | `ENV` |
|---|---|---|
| Available at build? | ✅ Yes | ✅ Yes |
| Available at runtime? | ❌ No | ✅ Yes |

---

### `EXPOSE` — Document Port

```dockerfile
# Documents which port the app listens on
# Does NOT actually publish the port — documentation only

EXPOSE 3000
EXPOSE 80
EXPOSE 5432
```

```bash
# You still need -p to actually publish:
docker run -p 3000:3000 myapp
```

---

### `VOLUME` — Persistent Storage Mount Point

```dockerfile
# Data survives container restarts

VOLUME /app/data
VOLUME /var/lib/postgresql/data
```

---

### `USER` — Set User

```dockerfile
# Run as a specific user, not root — security best practice

RUN useradd -m appuser
USER appuser

# All commands after this run as appuser
```

---

### `LABEL` — Metadata

```dockerfile
LABEL maintainer="you@email.com"
LABEL version="1.0"
LABEL description="My Node App"
```

---

## Real-World Dockerfile Examples

### Node.js App

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json .
RUN npm ci --only=production

COPY . .

EXPOSE 3000
ENV NODE_ENV=production

RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

CMD ["node", "server.js"]
```

---

### Python App

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

CMD ["python", "main.py"]
```

---

### Multi-Stage Build

```dockerfile
# Stage 1: Build
FROM node:20 AS builder

WORKDIR /app
COPY package*.json .
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Production (only what's needed)
FROM node:20-alpine

WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules

EXPOSE 3000
CMD ["node", "dist/index.js"]
```

| Stage | Size |
|-------|------|
| Builder stage | ~800 MB (has everything) |
| Final image | ~120 MB (only what's needed) ✅ |

---

## Layer Caching — Very Important

```dockerfile
# ❌ BAD — cache breaks every time code changes
FROM node:20
WORKDIR /app
COPY . .             # copies everything, including source code
RUN npm install      # reinstalls every time ❌

# ✅ GOOD — cache works correctly
FROM node:20
WORKDIR /app
COPY package*.json . # copy package.json FIRST
RUN npm install      # only reruns if package.json changes ✅
COPY . .             # copy source AFTER dependencies
```

> **Rule:** Put things that change least at the top, things that change most at the bottom.

```
Each instruction  =  one layer
Layer is cached   =  if nothing above it changed
Order matters     =  stable instructions first
```

---

## `.dockerignore` — Exclude Files from Build

```
# .dockerignore — works like .gitignore but for Docker

node_modules
.git
.env
*.log
dist
coverage
README.md
```

| | Without `.dockerignore` | With `.dockerignore` |
|---|---|---|
| `COPY . .` | Copies `node_modules` (~300MB) | Skips `node_modules` |
| Build speed | Slow | Fast |
| Image size | Larger | Smaller ✅ |

---

## All Instructions Summary

| Instruction | When It Runs | Purpose |
|-------------|-------------|---------|
| `FROM` | Build time | Base image |
| `WORKDIR` | Build time | Set working directory |
| `COPY` | Build time | Copy files into image |
| `ADD` | Build time | Copy + auto-extract / download |
| `RUN` | Build time | Execute commands |
| `ENV` | Build + Run time | Set environment variables |
| `ARG` | Build time only | Pass build arguments |
| `EXPOSE` | Documentation | Document which port app uses |
| `VOLUME` | Run time | Persistent storage mount point |
| `USER` | Build + Run time | Set which user runs commands |
| `LABEL` | Build time | Add metadata to image |
| `CMD` | Run time | Default start command (overridable) |
| `ENTRYPOINT` | Run time | Fixed start command |

---

## Key Takeaway

```
FROM        →  start with this base
WORKDIR     →  work in this folder
COPY        →  bring my files in
RUN         →  set things up at build time
EXPOSE      →  my app uses this port
ENV         →  set these variables
CMD         →  start my app like this
```

> A Dockerfile is just a **step-by-step recipe** telling Docker exactly how to package your app into a portable, runnable image.
