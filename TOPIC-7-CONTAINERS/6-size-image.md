# How to Minimize Docker Image Size

---

## Why Image Size Matters

| | Big Image | Small Image |
|---|---|---|
| Pull speed | Slow | Fast |
| Deploy speed | Slow | Fast |
| Storage cost | High | Low |
| Attack surface | Large | Small ✅ |

---

## 1. Choose the Right Base Image

```dockerfile
# ❌ Too heavy — full OS
FROM ubuntu:22.04                          # 200 MB
FROM node:20                               # 180 MB

# ✅ Better — slim variant
FROM node:20-slim                          # 100 MB

# ✅ Good — Alpine (minimal OS)
FROM node:20-alpine                        #  50 MB

# ✅ Better — Distroless (no OS tools)
FROM gcr.io/distroless/nodejs20-debian12   #  30 MB

# ✅ Best — Scratch (completely empty)
FROM scratch                               #   0 MB (only for compiled languages)
```

> Always start with the smallest base image your app can actually run on.

---

## 2. Use Multi-Stage Builds

```dockerfile
# ❌ Single stage — ships everything
FROM node:20
WORKDIR /app
COPY . .
RUN npm ci
RUN npm run build
# Final image = 900 MB — has compiler, devDeps, source code, build tools


# ✅ Multi-stage — ships only what runs
FROM node:20 AS builder
WORKDIR /app
COPY package*.json .
RUN npm ci
COPY . .
RUN npm run build

FROM node:20-alpine
WORKDIR /app
COPY package*.json .
RUN npm ci --only=production
COPY --from=builder /app/dist ./dist
CMD ["node", "dist/index.js"]
# Final image = 80 MB ✅
```

---

## 3. Use `.dockerignore`

```
# .dockerignore — stops unnecessary files being copied into image

node_modules        # biggest one — never copy this
.git                # git history not needed
.env                # never copy secrets!
*.log               # log files
dist                # will be rebuilt
coverage            # test coverage reports
.DS_Store           # Mac junk
README.md           # docs not needed
tests/              # test files not needed
docker-compose.yml
Dockerfile
```

| | Without `.dockerignore` | With `.dockerignore` |
|---|---|---|
| `COPY . .` | Copies `node_modules` (~300 MB) | Skips `node_modules` ✅ |
| Result | Slow, bloated image | Fast, lean image |

---

## 4. Combine `RUN` Commands

```dockerfile
# ❌ Bad — each RUN creates a new layer
RUN apt-get update
RUN apt-get install -y curl
RUN apt-get install -y git
RUN apt-get clean
# 4 layers, each stores data = bloated image


# ✅ Good — one RUN, one layer
RUN apt-get update && \
    apt-get install -y curl git && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
# 1 layer = smaller image ✅
```

```
Each RUN  =  one layer
Layers stack on top of each other
Combining = fewer layers = smaller image
```

---

## 5. Clean Up in the Same `RUN` Layer

```dockerfile
# ❌ Bad — cleanup in a separate layer doesn't help
RUN apt-get install -y curl
RUN apt-get clean             # too late — previous layer already saved


# ✅ Good — cleanup in the same layer
RUN apt-get update && \
    apt-get install -y curl && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*


# Python — avoid creating cache at all
RUN pip install --no-cache-dir -r requirements.txt

# Node — clean npm cache in same layer
RUN npm ci && npm cache clean --force
```

---

## 6. Only Install What You Need

```dockerfile
# ❌ Installs everything including devDependencies
RUN npm ci

# ✅ Production only — skips devDependencies
RUN npm ci --only=production


# ❌ Python — installs with cache
RUN pip install -r requirements.txt

# ✅ Python — no cache
RUN pip install --no-cache-dir -r requirements.txt
```

---

## 7. Use Specific Tags, Not `latest`

```dockerfile
# ❌ Unpredictable — version and size can change
FROM node:latest

# ✅ Specific version + alpine
FROM node:20-alpine
FROM node:20.10-alpine3.18    # even more specific
```

> Alpine variants are always smaller. Specific versions = predictable, reproducible builds.

---

## 8. Order Layers by Change Frequency

```dockerfile
# ❌ Bad order — cache breaks on every code change
FROM node:20-alpine
COPY . .                  # changes every time
RUN npm ci                # reinstalls every time


# ✅ Good order — stable things first
FROM node:20-alpine
COPY package*.json .      # changes rarely
RUN npm ci                # cached unless package.json changes ✅
COPY . .                  # source changes often — comes AFTER install
```

```
Most stable   → top of Dockerfile
Most changing → bottom of Dockerfile
= Better cache = faster builds
```

---

## 9. Avoid Storing Secrets or Temp Files

```dockerfile
# ❌ Bad — secret baked into image layer forever
RUN wget --header="Auth: mytoken" https://example.com/file


# ✅ Good — use build secrets (never stored in image)
RUN --mount=type=secret,id=mytoken \
    wget --header="Auth: $(cat /run/secrets/mytoken)" \
    https://example.com/file
```

---

## 10. Use Smaller Tool Alternatives

| Instead of | Use |
|-----------|-----|
| `curl` + `wget` | `wget` only |
| `bash` | `sh` (Alpine) |
| `python` (full) | `python:slim` |
| `node` (full) | `node:alpine` |
| `ubuntu` | `debian:slim` or `alpine` |

---

## All Techniques — Size Impact

| Technique | Size Saved |
|-----------|-----------|
| Multi-stage builds | ~700 MB |
| `.dockerignore` | ~300 MB |
| Production deps only | ~200 MB |
| Alpine base image | ~150 MB |
| Clean package manager cache | ~100 MB |
| Combine `RUN` commands | ~50 MB |
| Use Distroless | ~20 MB |

---

## Complete Optimized Dockerfile

```dockerfile
# Stage 1: Build
FROM node:20-alpine AS builder

WORKDIR /app

COPY package*.json .
RUN npm ci

COPY . .
RUN npm run build


# Stage 2: Production
FROM node:20-alpine

# Create non-root user (security best practice)
RUN addgroup -S appgroup && \
    adduser -S appuser -G appgroup

WORKDIR /app

COPY package*.json .
RUN npm ci --only=production && \
    npm cache clean --force

COPY --from=builder /app/dist ./dist

USER appuser

EXPOSE 3000
CMD ["node", "dist/index.js"]
```

| | Size |
|---|---|
| Before optimization | ~900 MB |
| After optimization | ~80 MB ✅ |

---

## Check Your Image Size

```bash
# See image sizes
docker images

# Inspect image layers and sizes
docker history myapp:v1

# Detailed layer analysis
docker image inspect myapp:v1

# Use dive — best tool for layer-by-layer analysis
docker run --rm -it \
  -v /var/run/docker.sock:/var/run/docker.sock \
  wagoodman/dive myapp:v1
```

---

## Quick Checklist

- [ ] Use Alpine or Distroless base image
- [ ] Use multi-stage builds
- [ ] Add `.dockerignore`
- [ ] Combine `RUN` commands into one layer
- [ ] Clean package manager cache in the same `RUN`
- [ ] Install only production dependencies
- [ ] Copy `package.json` before source code
- [ ] Never copy `node_modules` or `.git`
- [ ] Use specific image tags, not `latest`
- [ ] Run as non-root user

---

## Key Takeaway

The 5 biggest wins, in order of impact:

1. **Multi-stage builds** — remove all build tools from the final image
2. **Alpine base image** — tiny OS footprint
3. **`.dockerignore`** — don't copy junk into the build context
4. **Production deps only** — no devDependencies
5. **Combine `RUN` commands** — fewer layers

> Do all 5 and your image goes from **900 MB → 80 MB** easily.
