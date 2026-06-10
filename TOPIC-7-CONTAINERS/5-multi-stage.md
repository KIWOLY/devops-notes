# Multi-Stage Builds & Distroless Images

---

## The Problem

```
Your app code        =    1 MB
node_modules         =  300 MB
Build tools          =  200 MB
Compiler/TypeScript  =  100 MB
Full OS (ubuntu)     =  200 MB
─────────────────────────────
Final image          =  800 MB  ← way too big

But your actual running app only needs:
App code             =    1 MB
node_modules (prod)  =   50 MB
Node runtime         =   80 MB
─────────────────────────────
Actually needs       =  131 MB  ✅
```

> Why ship 800 MB when you only need 131 MB?
> This is the problem Multi-Stage builds and Distroless images solve.

---

## Part 1 — Multi-Stage Builds

---

### What is it?

> Use **multiple `FROM` statements** in one Dockerfile.
> Each `FROM` = one stage.
> **Copy only what you need** from previous stages.
> Everything else in the previous stage gets thrown away.

### Simple Analogy

```
Factory Stage
  - Has welding machines, paint booths, heavy equipment
  - Builds the car
        ↓
        Only the finished car moves forward
        All factory equipment stays behind
        ↓
Showroom Stage
  - Just the clean finished car
  - No factory mess
  - Ready for the customer
```

---

### Basic Multi-Stage Structure

```dockerfile
# Stage 1 — build
FROM node:20 AS builder
WORKDIR /app
COPY . .
RUN npm install
RUN npm run build

# Stage 2 — production
FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/dist .   # only take the built output
CMD ["node", "index.js"]

# Everything in Stage 1 is THROWN AWAY
# Final image only has Stage 2 content
```

> A **builder** is simply the first stage in a multi-stage build where you compile, install, or prepare your application.

---

### `npm install` vs `npm ci`

| | `npm install` | `npm ci` |
|---|---|---|
| Modifies lock file? | ✅ Yes | ❌ Never |
| Exact versions? | Not guaranteed | ✅ Always |
| Speed | Slower | Faster |
| Deletes `node_modules` first? | ❌ No | ✅ Yes |
| Fails if lock file missing? | ❌ No | ✅ Yes |
| Best for | Local development | Docker / CI/CD |

**Why `npm ci` is better in Docker — reproducible builds:**

```dockerfile
# npm install — different result possible
RUN npm install
# Today:    installs lodash 4.17.20
# Tomorrow: installs lodash 4.17.21  ← different!

# npm ci — always same result ✅
RUN npm ci
# Today:    installs lodash 4.17.20
# Tomorrow: installs lodash 4.17.20  ← exactly the same
```

---

### Real-World Examples

#### Node.js / TypeScript

```dockerfile
# Stage 1: Install & Build
FROM node:20 AS builder

WORKDIR /app

COPY package*.json .
RUN npm ci                   # exact versions, fast, reproducible

COPY . .
RUN npm run build            # compiles TypeScript → JavaScript


# Stage 2: Production
FROM node:20-alpine

WORKDIR /app

COPY package*.json .
RUN npm ci --only=production # only production dependencies

COPY --from=builder /app/dist ./dist

EXPOSE 3000
CMD ["node", "dist/index.js"]
```

| Stage | Size |
|-------|------|
| Stage 1 (builder) | ~900 MB — has TypeScript, dev tools |
| Stage 2 (final) | ~150 MB ✅ — only prod deps + built code |

---

#### React App

```dockerfile
# Stage 1: Build React App
FROM node:20 AS builder

WORKDIR /app
COPY package*.json .
RUN npm ci
COPY . .
RUN npm run build            # creates /app/build folder


# Stage 2: Serve with Nginx
FROM nginx:alpine

COPY --from=builder /app/build /usr/share/nginx/html

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

| Stage | Size |
|-------|------|
| Stage 1 (builder) | ~900 MB — node + react + deps |
| Stage 2 (final) | ~25 MB ✅ — just nginx + html/js/css |

---

### Multiple Stages — Advanced

```dockerfile
# Stage 1 — install dependencies
FROM node:20 AS deps
WORKDIR /app
COPY package*.json .
RUN npm ci

# Stage 2 — run tests
FROM deps AS tester
COPY . .
RUN npm test                 # build fails if tests fail ✅

# Stage 3 — build app
FROM deps AS builder
COPY . .
RUN npm run build

# Stage 4 — production
FROM node:20-alpine AS production
WORKDIR /app
COPY --from=builder /app/dist .
COPY --from=deps /app/node_modules .
CMD ["node", "index.js"]
```

```
deps        →  installs packages
tester      →  runs tests (build fails if tests fail)
builder     →  compiles code
production  →  lean final image
```

---

### `COPY --from` Options

```dockerfile
# Copy from a named stage
COPY --from=builder /app/dist ./dist

# Copy from a specific public image (not even a stage)
COPY --from=nginx:latest /etc/nginx/nginx.conf /nginx.conf

# Copy from a stage by index (0 = first stage)
COPY --from=0 /app/dist ./dist
```

---

## Part 2 — Distroless Images

---

### What is Distroless?

> **Distroless images** contain ONLY your app and its runtime.
> No shell. No package manager. No OS utilities. Nothing extra.

```
Normal image (Ubuntu based):
├── bash
├── sh
├── apt-get
├── curl
├── ls, cat, grep ...
├── 100s of OS utilities
├── Your Runtime
└── Your App

Distroless image:
├── Your Runtime
└── Your App    ← literally just this
```

---

### Why Distroless?

| Benefit | Reason |
|---------|--------|
| **Security** | No shell — attacker can't run commands if they get in |
| **Security** | No tools — attacker can't download malware |
| **Security** | No package manager — attacker can't install anything |
| **Size** | Smaller image = faster pulls = faster deploys |
| **Attack surface** | Less software = fewer vulnerabilities |

---

### Distroless vs Alpine vs Ubuntu

| Image | Size | Has Shell | Has Package Manager | Best For |
|-------|------|-----------|---------------------|---------|
| Ubuntu based | ~200 MB | ✅ Yes | ✅ Yes | Development |
| Alpine based | ~20 MB | ✅ Yes (`sh`) | ✅ Yes (`apk`) | Balanced / staging |
| Distroless | ~5 MB | ❌ No | ❌ No | Production ✅ |

---

### Distroless Images Available

Made by Google — `gcr.io/distroless/`

| Image | Runtime |
|-------|---------|
| `gcr.io/distroless/nodejs20-debian12` | Node.js |
| `gcr.io/distroless/python3` | Python |
| `gcr.io/distroless/java17` | Java |
| `gcr.io/distroless/base` | C / C++ |
| `gcr.io/distroless/static` | Go / Rust (no runtime needed) |

---

### Multi-Stage + Distroless Together

This is the **ultimate production pattern.**

#### Node.js

```dockerfile
# Stage 1: Build
FROM node:20 AS builder

WORKDIR /app
COPY package*.json .
RUN npm ci
COPY . .
RUN npm run build


# Stage 2: Distroless Production
FROM gcr.io/distroless/nodejs20-debian12

WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules

EXPOSE 3000
CMD ["dist/index.js"]        # no "node" prefix needed — distroless handles it
```

---

#### Python

```dockerfile
# Stage 1: Install dependencies
FROM python:3.11 AS builder

WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir \
    --target=/app/deps \
    -r requirements.txt


# Stage 2: Distroless
FROM gcr.io/distroless/python3

WORKDIR /app
COPY --from=builder /app/deps /app/deps
COPY . .

ENV PYTHONPATH=/app/deps
CMD ["main.py"]
```

---

### Debugging Distroless

```bash
# ❌ Cannot do this with distroless — no shell!
docker exec -it myapp bash    # fails
docker exec -it myapp sh      # fails

# ✅ Use the debug variant during development
FROM gcr.io/distroless/nodejs20-debian12:debug
#                                          ↑
#                              adds busybox shell for debugging

docker exec -it myapp sh      # works ✅
```

---

## Size Comparison — Same Node.js App

| Approach | Image Size |
|----------|-----------|
| `FROM node:20` (no multi-stage) | ~900 MB |
| `FROM node:20-alpine` (no multi-stage) | ~200 MB |
| `FROM node:20-alpine` + multi-stage | ~100 MB |
| Distroless + multi-stage | ~80 MB ✅ |

---

## When to Use What

| Environment | Recommended Base | Reason |
|-------------|-----------------|--------|
| **Development** | `node:20` / `python:3.11` | Easy debugging, all tools available |
| **Staging** | Alpine + multi-stage | Smaller, still has shell for debugging |
| **Production** | Distroless + multi-stage | Smallest, most secure ✅ |

---

## Key Takeaway

**Multi-Stage:**
- Use multiple `FROM` stages in one Dockerfile
- Build in one stage, copy only the output to the final stage
- Throw away all build tools
- Result: small image ✅

**Distroless:**
- No shell, no OS tools, nothing extra
- Only your runtime + your app
- Ultra secure, ultra small
- Hard to debug — use `:debug` tag during development
- Result: most secure image ✅

**Together:**
- Multi-stage builds the app cleanly
- Distroless runs it securely
- = Best production Docker image possible
