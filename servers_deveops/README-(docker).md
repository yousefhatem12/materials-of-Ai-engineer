# The Complete Docker Guide: Beginner to Advanced

A single-file reference that takes you from "what is a container?" to production-grade multi-stage builds, networking, security hardening, and orchestration basics.

---

## Table of Contents

1. [What Is Docker & Why It Matters](#1-what-is-docker--why-it-matters)
2. [Core Concepts](#2-core-concepts)
3. [Installation](#3-installation)
4. [Your First Container](#4-your-first-container)
5. [Essential Docker CLI Commands](#5-essential-docker-cli-commands)
6. [Images Deep Dive](#6-images-deep-dive)
7. [Writing Dockerfiles](#7-writing-dockerfiles)
8. [Multi-Stage Builds](#8-multi-stage-builds)
9. [Volumes & Persistent Data](#9-volumes--persistent-data)
10. [Networking](#10-networking)
11. [Environment Variables, Config & Secrets](#11-environment-variables-config--secrets)
12. [Docker Compose](#12-docker-compose)
13. [Image Registries](#13-image-registries)
14. [Logging & Debugging](#14-logging--debugging)
15. [Resource Limits & Healthchecks](#15-resource-limits--healthchecks)
16. [Security Best Practices](#16-security-best-practices)
17. [Image Size & Build Performance Optimization](#17-image-size--build-performance-optimization)
18. [Docker in CI/CD](#18-docker-in-cicd)
19. [Orchestration: Swarm & Kubernetes Overview](#19-orchestration-swarm--kubernetes-overview)
20. [Advanced Topics](#20-advanced-topics)
21. [Troubleshooting Guide](#21-troubleshooting-guide)
22. [Command Cheat Sheet](#22-command-cheat-sheet)

---

## 1. What Is Docker & Why It Matters

Docker is a platform for building, shipping, and running applications inside **containers** — lightweight, isolated environments that package an application together with everything it needs to run (code, runtime, system tools, libraries, configuration).

### Containers vs. Virtual Machines

| | Virtual Machine | Container |
|---|---|---|
| Isolation level | Full OS (own kernel) | Process-level (shares host kernel) |
| Boot time | Minutes | Milliseconds to seconds |
| Size | GBs | MBs to low GBs |
| Overhead | High (hypervisor + guest OS) | Low (namespaces + cgroups) |
| Portability | Good | Excellent |

A VM virtualizes hardware and runs a full guest operating system. A container virtualizes the operating system itself: every container on a host shares that host's kernel but gets its own isolated filesystem, process tree, network stack, and resource limits. This is why containers start almost instantly and use a fraction of the resources.

### Why teams use Docker

- **Consistency** — "works on my machine" stops being a problem because the container carries its own environment everywhere: laptop, CI runner, staging, production.
- **Isolation** — Each app's dependencies are sandboxed, so you can run conflicting versions of the same library side by side (e.g., two services needing different Python versions).
- **Density & efficiency** — Because containers share the host kernel, you can run far more containers than VMs on the same hardware.
- **Reproducible builds** — A `Dockerfile` is a versioned, text-based recipe for an environment. Anyone can rebuild the exact same image from it.
- **Ecosystem** — Docker Hub and other registries give you a vast library of pre-built, maintained base images (databases, language runtimes, web servers).

### The underlying technology

Docker itself doesn't implement isolation from scratch — it orchestrates Linux kernel features:

- **Namespaces** — isolate what a process can *see* (its own PID tree, network interfaces, mount points, hostname, users).
- **Control groups (cgroups)** — limit what a process can *use* (CPU, memory, disk I/O, network bandwidth).
- **Union filesystems (OverlayFS, etc.)** — let images be built from stacked, reusable, read-only layers with a thin writable layer on top.

On macOS and Windows, Docker runs inside a lightweight Linux VM automatically, since containers fundamentally need a Linux kernel to share.

---

## 2. Core Concepts

| Term | Definition |
|---|---|
| **Image** | A read-only, immutable template containing an application and its dependencies, built from a set of stacked layers. |
| **Container** | A running (or stopped) instance of an image, with its own writable layer, process space, and network interface. |
| **Dockerfile** | A text file of instructions describing how to build an image. |
| **Layer** | A single filesystem diff produced by one Dockerfile instruction (e.g., `RUN`, `COPY`). Layers are cached and reused. |
| **Registry** | A server that stores and distributes images (e.g., Docker Hub, GitHub Container Registry, AWS ECR). |
| **Repository** | A named collection of related images distinguished by tags (e.g., `nginx:1.27`, `nginx:latest`). |
| **Volume** | Docker-managed persistent storage that lives outside a container's writable layer. |
| **Bind mount** | A direct mapping of a host directory/file into a container. |
| **Network** | A virtual network that containers attach to in order to communicate with each other and the outside world. |
| **Docker Engine** | The client-server application (`dockerd` daemon + `docker` CLI) that builds and runs containers. |
| **Docker Compose** | A tool for defining and running multi-container applications from a single YAML file. |

### The image-container relationship, visually

```
Dockerfile  --build-->  Image (read-only layers)  --run-->  Container (image layers + 1 writable layer)
```

You can run many containers from one image; each gets its own writable layer and is fully independent, but they all share the same underlying read-only image layers on disk (saving space).

---

## 3. Installation

### Linux (Ubuntu/Debian example)

```bash
# Remove old versions first
sudo apt-get remove docker docker-engine docker.io containerd runc

# Set up Docker's official repository
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Run Docker without sudo (log out/in after this)
sudo usermod -aG docker $USER

# Verify
docker run hello-world
```

### macOS & Windows

Install **Docker Desktop** from docker.com. It bundles the Docker Engine (running inside a managed lightweight VM), the CLI, Docker Compose, and a GUI. On Windows, enable WSL2 integration for the best performance.

### Verifying your install

```bash
docker --version
docker compose version
docker info
docker run hello-world
```

---

## 4. Your First Container

```bash
# Run an interactive Ubuntu container and drop into a shell
docker run -it ubuntu bash

# Run Nginx and map host port 8080 to container port 80
docker run -d -p 8080:80 --name my-nginx nginx

# Visit it
curl http://localhost:8080

# Stop and remove it
docker stop my-nginx
docker rm my-nginx
```

Breaking down `docker run -d -p 8080:80 --name my-nginx nginx`:

- `-d` — detached mode, runs in the background
- `-p 8080:80` — publish container port 80 to host port 8080 (`host:container`)
- `--name my-nginx` — give the container a friendly name instead of a random one
- `nginx` — the image to run (pulled automatically from Docker Hub if not present locally)

---

## 5. Essential Docker CLI Commands

### Images

```bash
docker pull <image>[:tag]        # Download an image
docker images                    # List local images
docker rmi <image>               # Remove an image
docker build -t <name>:<tag> .   # Build an image from a Dockerfile in current dir
docker tag <image> <new-name>    # Add a new tag to an existing image
docker history <image>           # Show the layer history of an image
docker inspect <image>           # Full JSON metadata
```

### Containers

```bash
docker run <image>                     # Create + start a container
docker ps                              # List running containers
docker ps -a                           # List ALL containers (including stopped)
docker start <container>               # Start a stopped container
docker stop <container>                # Gracefully stop (SIGTERM, then SIGKILL after timeout)
docker restart <container>
docker kill <container>                # Force stop (SIGKILL)
docker rm <container>                  # Remove a stopped container
docker rm -f <container>               # Force-remove a running container
docker logs <container>                # View stdout/stderr logs
docker logs -f <container>             # Follow (tail) logs
docker exec -it <container> bash       # Open a shell inside a running container
docker cp <container>:/path ./local    # Copy files out of a container
docker cp ./local <container>:/path    # Copy files into a container
docker stats                           # Live resource usage of all containers
docker top <container>                 # Processes running inside a container
docker rename <old> <new>
```

### Cleanup

```bash
docker container prune     # Remove all stopped containers
docker image prune         # Remove dangling (untagged) images
docker image prune -a      # Remove ALL unused images
docker volume prune        # Remove unused volumes
docker network prune       # Remove unused networks
docker system prune -a --volumes   # Nuke everything unused (use with care)
docker system df           # See disk usage breakdown
```

### Useful run flags

| Flag | Purpose |
|---|---|
| `-d` | Detached (background) mode |
| `-it` | Interactive + allocate a TTY (for shells) |
| `--rm` | Auto-remove the container when it exits |
| `-p host:container` | Publish a port |
| `-v host:container` | Mount a volume or bind mount |
| `-e KEY=value` | Set an environment variable |
| `--env-file .env` | Load environment variables from a file |
| `--name` | Assign a container name |
| `--network` | Attach to a specific network |
| `--restart` | Restart policy (`no`, `on-failure`, `always`, `unless-stopped`) |
| `--memory`, `--cpus` | Resource limits |
| `-w /path` | Set the working directory inside the container |
| `--entrypoint` | Override the image's default entrypoint |

---

## 6. Images Deep Dive

### Layers and caching

Every instruction in a Dockerfile that modifies the filesystem (`RUN`, `COPY`, `ADD`) creates a new, cached layer. When you rebuild, Docker reuses cached layers for any instruction that hasn't changed **and** whose preceding instructions haven't changed either — so instruction *order* matters a lot for build speed (see Section 17).

```bash
docker history nginx:latest
```

shows each layer, its size, and the command that created it.

### Tags

A tag is a human-readable pointer to a specific image digest, e.g. `python:3.12-slim`. `latest` is just a conventional tag name (nothing magic) and is **not** guaranteed to be the newest or most stable version — always pin explicit versions in production.

```bash
docker pull node:20.11-alpine
docker pull node:20.11-alpine@sha256:abcdef...   # pull an exact, immutable digest
```

### Base image choices

| Base type | Example | Size | Trade-off |
|---|---|---|---|
| Full distro | `ubuntu:24.04` | ~80 MB+ | Most compatible, largest |
| Slim | `python:3.12-slim` | Medium | Debian-based, minimal packages |
| Alpine | `python:3.12-alpine` | Smallest | musl libc (can break some native deps) |
| Distroless | `gcr.io/distroless/*` | Very small | No shell, no package manager — great for security, harder to debug |
| Scratch | `FROM scratch` | 0 bytes | Truly empty — used for statically compiled binaries (e.g. Go) |

---

## 7. Writing Dockerfiles

### Anatomy of a Dockerfile

```dockerfile
# Base image
FROM node:20-alpine

# Metadata
LABEL maintainer="you@example.com"

# Set working directory (creates it if missing)
WORKDIR /app

# Copy only dependency manifests first (better layer caching)
COPY package.json package-lock.json ./

# Install dependencies
RUN npm ci --omit=dev

# Copy the rest of the source
COPY . .

# Document the port the app listens on (informational only)
EXPOSE 3000

# Run as a non-root user
USER node

# Default command when the container starts
CMD ["node", "server.js"]
```

### Key instructions explained

| Instruction | Purpose |
|---|---|
| `FROM` | Sets the base image. Must be the first instruction (except `ARG` before it). |
| `WORKDIR` | Sets/creates the working directory for subsequent instructions. |
| `COPY` | Copies files from the build context into the image. |
| `ADD` | Like `COPY`, but also auto-extracts local tar archives and can fetch URLs — prefer `COPY` unless you specifically need those features. |
| `RUN` | Executes a command at **build time** and commits the result as a new layer. |
| `CMD` | The default command run at **container start**. Overridable via `docker run <image> <other-cmd>`. Only the last `CMD` takes effect. |
| `ENTRYPOINT` | The fixed executable a container always runs; `CMD` becomes its default arguments. Harder to override (needs `--entrypoint`). |
| `EXPOSE` | Documents which port(s) the app uses. Does **not** actually publish the port — that's what `-p` at runtime is for. |
| `ENV` | Sets environment variables, available at build and run time. |
| `ARG` | Build-time-only variable, passed via `--build-arg`. Not available inside the running container. |
| `USER` | Sets the user (and optionally group) that subsequent instructions and the container process run as. |
| `VOLUME` | Declares a mount point, causing Docker to create an anonymous volume there if none is bind-mounted. |
| `HEALTHCHECK` | Defines a command Docker runs periodically to check container health. |
| `COPY --from=` | Copies files from another build stage (see multi-stage builds). |
| `.dockerignore` | Not a Dockerfile instruction, but a sibling file that excludes paths from the build context (like `.gitignore`). |

### CMD vs ENTRYPOINT — the classic confusion

```dockerfile
# CMD alone: fully replaceable
CMD ["python", "app.py"]
# docker run myimage   -> runs `python app.py`
# docker run myimage echo hi -> runs `echo hi` instead

# ENTRYPOINT + CMD: CMD supplies default *arguments* to ENTRYPOINT
ENTRYPOINT ["python", "app.py"]
CMD ["--port", "8000"]
# docker run myimage             -> python app.py --port 8000
# docker run myimage --port 9000 -> python app.py --port 9000
```

Use `ENTRYPOINT` when you want the image to always run as a specific executable (like a CLI tool), and `CMD` for defaults that a user is expected to override freely.

### Exec form vs shell form

```dockerfile
CMD ["nginx", "-g", "daemon off;"]   # exec form (preferred): no shell, PID 1, proper signal handling
CMD nginx -g "daemon off;"           # shell form: runs via /bin/sh -c, can swallow signals like SIGTERM
```

Always prefer exec form (`["executable", "arg1", "arg2"]`) so signals (e.g., `docker stop`'s SIGTERM) reach your application process directly instead of being absorbed by an intermediate shell.

### .dockerignore example

```
node_modules
.git
.env
*.log
dist
__pycache__
.DS_Store
```

This both speeds up builds (smaller context sent to the daemon) and prevents secrets or bloat from accidentally being baked into layers.

---

## 8. Multi-Stage Builds

Multi-stage builds let you use one image to *compile/build* your app and a separate, much smaller image to *run* it — the build tools never end up in your final image.

```dockerfile
# ---- Stage 1: build ----
FROM golang:1.22 AS builder
WORKDIR /src
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -o /app ./cmd/server

# ---- Stage 2: runtime ----
FROM scratch
COPY --from=builder /app /app
EXPOSE 8080
ENTRYPOINT ["/app"]
```

The final image here contains nothing but the compiled Go binary — no compiler, no source code, no package manager. This can shrink an image from ~1 GB down to a few MB, and dramatically reduces the attack surface.

### Node.js multi-stage example

```dockerfile
FROM node:20-alpine AS deps
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci

FROM node:20-alpine AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

FROM node:20-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/package.json ./
RUN npm ci --omit=dev
USER node
CMD ["node", "dist/server.js"]
```

You can also build only up to a specific stage — useful for a dedicated test stage:

```bash
docker build --target builder -t myapp:build-stage .
```

---

## 9. Volumes & Persistent Data

Containers are ephemeral by design — when a container is removed, its writable layer (and anything written to it) is gone. Volumes and bind mounts solve this.

### Named volumes (Docker-managed, recommended for most cases)

```bash
docker volume create mydata
docker run -d -v mydata:/var/lib/mysql --name db mysql:8

docker volume ls
docker volume inspect mydata
docker volume rm mydata
```

Docker manages the actual storage location on the host (typically under `/var/lib/docker/volumes/`), which makes volumes portable across environments and easy to back up.

### Bind mounts (map a specific host path in)

```bash
docker run -d -v $(pwd)/src:/app/src --name dev-app myapp
# or the more explicit --mount syntax:
docker run -d --mount type=bind,source="$(pwd)/src",target=/app/src myapp
```

Bind mounts are ideal for local development (live-reloading source code into a container) since edits on the host show up instantly inside the container.

### tmpfs mounts (in-memory, never persisted to disk)

```bash
docker run -d --tmpfs /app/cache myapp
```

Useful for secrets or scratch space you explicitly never want touching disk.

### Volumes vs bind mounts vs tmpfs

| | Managed by | Use case | Persists after container removal |
|---|---|---|---|
| Named volume | Docker | Databases, production data | Yes |
| Bind mount | You (host path) | Local dev, config injection | Yes (it's just a host folder) |
| tmpfs | Docker (RAM) | Secrets, ephemeral scratch space | No |

### Backing up a volume

```bash
docker run --rm -v mydata:/data -v $(pwd):/backup alpine \
  tar czf /backup/mydata-backup.tar.gz -C /data .
```

---

## 10. Networking

By default, `docker run` attaches a container to the default **bridge** network. Docker also offers several other network drivers:

| Driver | Use case |
|---|---|
| `bridge` (default) | Single-host container-to-container communication via a private internal network |
| `host` | Container shares the host's network namespace directly (no isolation, best performance) |
| `none` | No networking at all |
| `overlay` | Multi-host networking for Swarm/Kubernetes clusters |
| `macvlan` | Assigns containers a MAC address, making them appear as physical devices on the network |

### Custom bridge networks (recommended over the default bridge)

```bash
docker network create mynet
docker run -d --name db --network mynet postgres:16
docker run -d --name api --network mynet myapi
```

Containers on the same **user-defined** bridge network can resolve each other by container name via Docker's built-in DNS (`api` can just connect to `db:5432` — no IPs needed). This does **not** work on the default bridge network without extra flags, which is one of the main reasons to always create a custom network.

### Port publishing

```bash
docker run -p 8080:80 nginx          # bind to all host interfaces
docker run -p 127.0.0.1:8080:80 nginx  # bind only to localhost
docker run -P nginx                  # publish ALL exposed ports to random host ports
```

### Inspecting networks

```bash
docker network ls
docker network inspect mynet
docker network connect mynet <container>      # attach a running container to a network
docker network disconnect mynet <container>
```

---

## 11. Environment Variables, Config & Secrets

```bash
docker run -e API_KEY=abc123 myapp
docker run --env-file .env myapp
```

`.env` file format:

```
DATABASE_URL=postgres://user:pass@db:5432/app
DEBUG=false
```

### Handling secrets properly

- **Never** `COPY` a secrets file into an image layer or bake secrets into `ENV`/`ARG` in a Dockerfile — they persist permanently in the image history and are extractable by anyone with the image, even if a later layer deletes the file.
- Use **BuildKit secret mounts** for build-time secrets (see Section 20) — they're available only during that specific `RUN` step and never persist in a layer.
- For runtime secrets, prefer `--env-file`, mounted secret files, or your orchestrator's secret store (Docker Swarm secrets, Kubernetes Secrets, or a vault like HashiCorp Vault / AWS Secrets Manager).

```dockerfile
# BAD — secret ends up permanently baked into the image layer history
RUN echo "API_KEY=supersecret" > .env

# GOOD — BuildKit secret, never persisted in any layer
RUN --mount=type=secret,id=api_key \
    API_KEY=$(cat /run/secrets/api_key) ./build.sh
```

```bash
docker build --secret id=api_key,src=./secret.txt .
```

---

## 12. Docker Compose

Compose lets you define a full multi-container application (services, networks, volumes) declaratively in one `compose.yaml` (formerly `docker-compose.yml`) and manage it as a unit.

### Example: a web app + database + cache

```yaml
services:
  web:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgres://user:pass@db:5432/app
      - REDIS_URL=redis://cache:6379
    depends_on:
      db:
        condition: service_healthy
      cache:
        condition: service_started
    volumes:
      - ./src:/app/src   # live-reload in dev
    restart: unless-stopped

  db:
    image: postgres:16-alpine
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=pass
      - POSTGRES_DB=app
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user"]
      interval: 5s
      timeout: 5s
      retries: 5

  cache:
    image: redis:7-alpine
    restart: unless-stopped

volumes:
  pgdata:
```

### Common Compose commands

```bash
docker compose up            # create + start everything (foreground)
docker compose up -d         # detached mode
docker compose up --build    # rebuild images before starting
docker compose down          # stop and remove containers, networks
docker compose down -v       # also remove named volumes
docker compose ps            # list this project's containers
docker compose logs -f       # follow logs from all services
docker compose logs -f web   # follow logs from one service
docker compose exec web bash # shell into a running service
docker compose restart web
docker compose config        # validate & print the fully-resolved config
```

### Multiple environments with overrides

```bash
# compose.yaml (base) + compose.override.yaml (auto-merged) are combined by default
docker compose up

# Explicitly layer a production override
docker compose -f compose.yaml -f compose.prod.yaml up -d
```

### Scaling a service

```bash
docker compose up -d --scale web=3
```

(Note: this only makes sense for stateless services without a fixed host-port mapping, since three containers can't all bind the same host port.)

---

## 13. Image Registries

### Docker Hub

```bash
docker login
docker tag myapp:latest username/myapp:1.0
docker push username/myapp:1.0
docker pull username/myapp:1.0
```

### Other registries

| Registry | Notes |
|---|---|
| Docker Hub | Default registry; free tier has pull-rate limits for anonymous/free accounts |
| GitHub Container Registry (`ghcr.io`) | Tightly integrated with GitHub Actions and repo permissions |
| AWS ECR / Google Artifact Registry / Azure ACR | Cloud-native, IAM-integrated, ideal if you're already on that cloud |
| Self-hosted (`registry:2` image) | Full control, useful for air-gapped or compliance-heavy environments |

```bash
# Push to a non-Docker-Hub registry — the registry host goes in the tag
docker tag myapp:latest ghcr.io/username/myapp:1.0
docker push ghcr.io/username/myapp:1.0
```

### Running your own private registry

```bash
docker run -d -p 5000:5000 --name registry --restart always \
  -v registry-data:/var/lib/registry registry:2

docker tag myapp:latest localhost:5000/myapp:1.0
docker push localhost:5000/myapp:1.0
```

---

## 14. Logging & Debugging

```bash
docker logs <container>                # dump logs
docker logs -f --tail 100 <container>  # follow, starting from last 100 lines
docker logs --since 10m <container>    # logs from the last 10 minutes

docker exec -it <container> sh         # shell in (use sh if bash isn't installed, e.g. Alpine)
docker inspect <container>             # full JSON: env vars, mounts, network, config, state
docker inspect -f '{{.State.ExitCode}}' <container>   # extract one field with Go templates

docker events                          # live stream of Docker daemon events

docker diff <container>                # files changed vs. the base image
```

### Debugging a container that keeps crashing

```bash
# Override the entrypoint to get a shell instead of the normal startup command
docker run -it --entrypoint sh myimage

# Check why it exited
docker ps -a          # look at the STATUS/exit code column
docker logs <container>
docker inspect <container> --format '{{json .State}}'
```

### Debugging distroless / shell-less images

Since distroless images have no shell to `exec` into, use **ephemeral debug containers** (Docker 20.10+ / `docker debug` in newer Docker Desktop, or attach a sidecar container that shares the target's process namespace):

```bash
docker run -it --rm --pid=container:<target> --network=container:<target> \
  --cap-add SYS_PTRACE busybox sh
```

---

## 15. Resource Limits & Healthchecks

### Limiting CPU and memory

```bash
docker run -d --memory=512m --memory-swap=512m --cpus=1.5 myapp
```

- `--memory` — hard cap; the container is OOM-killed if it exceeds this.
- `--cpus` — fractional CPU limit (e.g., `1.5` = one and a half cores).
- `--memory-swap` — total memory+swap limit; set equal to `--memory` to disable swap entirely.

In Compose:

```yaml
services:
  web:
    image: myapp
    deploy:
      resources:
        limits:
          cpus: "1.5"
          memory: 512M
        reservations:
          cpus: "0.5"
          memory: 256M
```

### Healthchecks

A healthcheck tells Docker (and orchestrators) whether the *application inside* the container is actually working, not just whether the process is running.

```dockerfile
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD curl -f http://localhost:3000/health || exit 1
```

```bash
docker ps    # STATUS column shows (healthy) / (unhealthy) / (starting)
```

```yaml
# Compose
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
  interval: 30s
  timeout: 5s
  retries: 3
  start_period: 10s
```

### Restart policies

```bash
docker run -d --restart unless-stopped myapp
```

| Policy | Behavior |
|---|---|
| `no` (default) | Never restart automatically |
| `on-failure[:N]` | Restart only on non-zero exit, up to N times |
| `always` | Always restart, even after a manual stop (unless explicitly removed) |
| `unless-stopped` | Like `always`, but stays stopped if you manually stopped it before a daemon restart |

---

## 16. Security Best Practices

1. **Don't run as root inside the container.** Add a dedicated user in your Dockerfile:
   ```dockerfile
   RUN addgroup -S app && adduser -S app -G app
   USER app
   ```
   Many official images (like `node`) already ship a non-root user you can just `USER node`.

2. **Use minimal base images.** Fewer packages = smaller attack surface. Prefer `-slim`, `-alpine`, or distroless images, and multi-stage builds so build tools never ship in the final image.

3. **Pin exact versions,** not `latest`, for both base images and dependencies, so builds are reproducible and you're not silently pulling in a breaking or compromised update.

4. **Scan images for known vulnerabilities:**
   ```bash
   docker scout cves myimage:tag       # Docker's built-in scanner
   trivy image myimage:tag             # popular open-source alternative
   ```

5. **Never bake secrets into layers** (see Section 11) — use BuildKit secret mounts or runtime secret injection instead.

6. **Set the filesystem to read-only where possible:**
   ```bash
   docker run --read-only --tmpfs /tmp myapp
   ```

7. **Drop unnecessary Linux capabilities:**
   ```bash
   docker run --cap-drop=ALL --cap-add=NET_BIND_SERVICE myapp
   ```

8. **Avoid `--privileged` mode** unless you specifically need full host device access (e.g., certain CI/DinD setups) — it effectively disables container isolation.

9. **Use `.dockerignore`** to keep `.git`, `.env`, credentials, and other sensitive local files out of the build context entirely.

10. **Verify image provenance** with content trust / image signing (Docker Content Trust, Sigstore/cosign) in supply-chain-sensitive environments.

11. **Keep the Docker Engine itself updated** — vulnerabilities in the daemon or runtime are just as real a risk as vulnerabilities in your images.

12. **Limit resources** (Section 15) so a compromised or runaway container can't starve the host or other containers.

---

## 17. Image Size & Build Performance Optimization

### Order Dockerfile instructions from least → most frequently changing

```dockerfile
# GOOD — dependency install is cached until package.json actually changes
FROM node:20-alpine
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci
COPY . .          # source code changes don't invalidate the npm ci layer above
CMD ["node", "server.js"]
```

```dockerfile
# BAD — any source change invalidates the cached dependency install, every single build
FROM node:20-alpine
WORKDIR /app
COPY . .
RUN npm ci
CMD ["node", "server.js"]
```

### Combine RUN instructions to reduce layer count and clean up in the same layer

```dockerfile
# GOOD — apt cache cleanup happens in the SAME layer, so it doesn't bloat the image
RUN apt-get update && \
    apt-get install -y --no-install-recommends curl && \
    rm -rf /var/lib/apt/lists/*
```

```dockerfile
# BAD — the cleanup RUN creates a new layer on top; the earlier layer's bloat is still in the image history
RUN apt-get update && apt-get install -y curl
RUN rm -rf /var/lib/apt/lists/*
```

### Other techniques

- Use **multi-stage builds** (Section 8) to exclude build-time-only tooling from the final image.
- Use **BuildKit cache mounts** for package manager caches that shouldn't be baked into a layer at all:
  ```dockerfile
  # syntax=docker/dockerfile:1
  RUN --mount=type=cache,target=/root/.npm npm ci
  ```
- Prefer `COPY` over `ADD` unless you need auto-extraction, since `ADD`'s extra behavior can cause confusing, unintended results.
- Use a tight `.dockerignore` so the build context sent to the daemon is small and doesn't accidentally invalidate cache.
- Check image size and layer contribution:
  ```bash
  docker images
  docker history --no-trunc myimage
  dive myimage    # third-party tool for interactively exploring layers
  ```

---

## 18. Docker in CI/CD

### Typical CI pipeline steps

1. Checkout code
2. Build the image, tagged with the commit SHA (and often a semantic version/branch tag too)
3. Run tests inside the built image (or a dedicated test stage of a multi-stage build)
4. Scan the image for vulnerabilities
5. Push to a registry
6. Deploy (trigger a rolling update on the target environment/orchestrator)

### GitHub Actions example

```yaml
name: build-and-push
on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Log in to GHCR
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build and push
        uses: docker/build-push-action@v6
        with:
          context: .
          push: true
          tags: ghcr.io/${{ github.repository }}:${{ github.sha }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
```

### Docker-in-Docker vs. mounting the host socket

CI runners commonly need to build/run Docker itself. Two approaches:

- **Mount the host's Docker socket** (`-v /var/run/docker.sock:/var/run/docker.sock`) — the CI job's containers are actually built/run by the *host's* Docker daemon. Fast and commonly used, but the job effectively gets root-equivalent access to the host.
- **Docker-in-Docker (DinD)** — run an entirely separate, nested Docker daemon inside a privileged container. More isolated from the host but slower and requires `--privileged`.

For most CI providers (GitHub Actions, GitLab CI), the managed Buildx/build-push actions handle this complexity for you.

---

## 19. Orchestration: Swarm & Kubernetes Overview

Compose is great for one host. Once you need to run containers reliably across **multiple machines** — with automatic rescheduling, rolling updates, service discovery, and load balancing — you need an orchestrator.

### Docker Swarm (built into Docker Engine)

```bash
docker swarm init                       # turn this host into a Swarm manager
docker swarm join --token <token> <ip>  # join a worker to the swarm

docker stack deploy -c compose.yaml mystack   # deploy a Compose file as a Swarm "stack"
docker service ls
docker service scale mystack_web=5
docker service update --image myapp:2.0 mystack_web   # rolling update
docker node ls
```

Swarm reuses Compose file syntax (with a `deploy:` section for replicas, resources, update strategy) and is the simplest way to go from "one Compose file" to "a small resilient cluster" with minimal new concepts to learn.

### Kubernetes (the industry-standard for larger/complex deployments)

Kubernetes is a much larger, more powerful orchestration system. Core objects you'll meet immediately:

| Object | Purpose |
|---|---|
| **Pod** | The smallest deployable unit — one or more tightly coupled containers sharing network/storage |
| **Deployment** | Manages a set of replica Pods, handles rolling updates and self-healing |
| **Service** | A stable network endpoint/load balancer in front of a set of Pods |
| **ConfigMap / Secret** | External configuration and sensitive data injected into Pods |
| **Ingress** | HTTP(S) routing from outside the cluster to internal Services |
| **Namespace** | Logical partitioning of cluster resources |

A minimal Deployment + Service:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 3
  selector:
    matchLabels: { app: myapp }
  template:
    metadata:
      labels: { app: myapp }
    spec:
      containers:
        - name: myapp
          image: ghcr.io/username/myapp:1.0
          ports: [{ containerPort: 8000 }]
---
apiVersion: v1
kind: Service
metadata:
  name: myapp
spec:
  selector: { app: myapp }
  ports: [{ port: 80, targetPort: 8000 }]
  type: ClusterIP
```

**Swarm vs. Kubernetes, in short:** Swarm is simpler and ships with Docker itself — a natural next step from Compose. Kubernetes has a much steeper learning curve but is the de facto industry standard, with a vastly larger ecosystem (Helm, operators, service meshes, autoscaling, and near-universal cloud-provider support).

---

## 20. Advanced Topics

### BuildKit

BuildKit is Docker's modern build engine (default since Docker 23+), offering parallel layer builds, better caching, secret/SSH mounts, and cache mounts.

```dockerfile
# syntax=docker/dockerfile:1
FROM golang:1.22 AS builder
WORKDIR /src
COPY . .
RUN --mount=type=cache,target=/root/go/pkg/mod \
    --mount=type=cache,target=/root/.cache/go-build \
    go build -o /app .
```

```bash
DOCKER_BUILDKIT=1 docker build .     # explicit opt-in on older Docker versions
docker buildx build --platform linux/amd64,linux/arm64 -t myapp:multi --push .
```

### Multi-platform (multi-arch) builds with Buildx

```bash
docker buildx create --use
docker buildx build \
  --platform linux/amd64,linux/arm64,linux/arm/v7 \
  -t username/myapp:1.0 \
  --push .
```

This produces a single manifest list/tag that automatically resolves to the right architecture's image when pulled on different hardware (e.g., an Apple Silicon Mac vs. an x86 server).

### Init process & zombie reaping

Applications that spawn child processes can accumulate "zombie" processes if PID 1 doesn't reap them properly (a common gotcha since your app, not a real init system, is PID 1 inside a container).

```bash
docker run --init myapp
```

The `--init` flag inserts a tiny init process (`tini`) as PID 1, which correctly reaps zombies and forwards signals to your actual application.

### Rootless Docker

Runs the entire Docker daemon itself as a non-root user, removing an entire class of container-escape-to-host-root risk. Useful in multi-tenant or security-sensitive hosts.

```bash
curl -fsSL https://get.docker.com/rootless | sh
```

### Storage drivers

Docker abstracts the union filesystem behind pluggable storage drivers. `overlay2` is the modern default on Linux and is what you'll use in the vast majority of cases; older drivers (`aufs`, `devicemapper`, `btrfs`, `zfs`) exist for specific legacy or filesystem needs.

```bash
docker info | grep "Storage Driver"
```

### Inter-container communication patterns at scale

- **Sidecar pattern** — a helper container (logging agent, proxy, config reloader) sharing the network/process namespace with the main container.
- **Ambassador pattern** — a proxy container abstracting connection details to external services.
- **Service mesh** (Istio, Linkerd, Consul Connect) — typically layered on top of Kubernetes, handling mTLS, retries, and traffic shaping between services transparently.

### Image layer immutability & content-addressable storage

Every image layer is identified by a SHA256 digest of its content. This is why `docker pull` skips layers you already have (by digest) and why two images built from identical instructions on identical inputs can share layers on disk, even with different tags.

```bash
docker inspect --format='{{.Id}}' myimage
```

---

## 21. Troubleshooting Guide

| Symptom | Likely cause | Fix |
|---|---|---|
| `Cannot connect to the Docker daemon` | Daemon not running, or your user lacks permission | `sudo systemctl start docker`; add your user to the `docker` group |
| Container exits immediately | The main process finished/crashed instantly (e.g., `CMD` was interactive-only) | `docker logs <container>`; check exit code with `docker ps -a` |
| `port is already allocated` | Another process/container is already using that host port | `docker ps` to find the conflicting container, or pick a different host port |
| Changes to source code aren't reflected | You edited files locally but the image wasn't rebuilt, or a volume isn't mounted where you think | Rebuild (`docker build` / `docker compose up --build`), or double-check bind mount paths |
| `npm ci`/`pip install` reruns on every build despite no dependency changes | Dockerfile instruction order copies source before installing deps | Reorder so manifest files are copied and installed before the rest of the source (Section 17) |
| Container can't reach another container by name | Both containers are on the default bridge network (no built-in DNS there), or on different networks | Create/use a custom user-defined network (Section 10) |
| `OOMKilled` in `docker inspect` | Container exceeded its `--memory` limit | Raise the limit, fix a memory leak, or optimize the app's memory usage |
| Image is huge | Using a full-distro base image; not cleaning package manager caches; not using multi-stage builds | Switch to `-slim`/`-alpine`/distroless base, combine and clean `RUN` layers, use multi-stage builds (Sections 17, 8) |
| `permission denied` writing to a mounted volume | UID/GID mismatch between the container's user and the host directory's ownership | Match UIDs, or `chown` the host directory, or run as root only where truly necessary |
| Build is slow every time, cache never hits | `.dockerignore` missing/too permissive, or a frequently-changing file (like a timestamp) is copied early | Tighten `.dockerignore`; reorder Dockerfile so volatile files are copied last |
| `exec format error` | Trying to run an image built for a different CPU architecture (e.g., arm64 image on an amd64 host) | Rebuild/pull the correct platform variant, or use `docker buildx` for multi-arch builds |

---

## 22. Command Cheat Sheet

```bash
# --- Images ---
docker pull <image>
docker build -t name:tag .
docker images
docker rmi <image>
docker tag <src> <dest>
docker push <image>

# --- Containers ---
docker run -d -p 8080:80 --name web nginx
docker ps / docker ps -a
docker stop|start|restart|rm|kill <container>
docker exec -it <container> sh
docker logs -f <container>
docker inspect <container>
docker stats

# --- Volumes ---
docker volume create|ls|inspect|rm <name>
docker run -v myvol:/data ...

# --- Networks ---
docker network create|ls|inspect|rm <name>
docker run --network mynet ...

# --- Compose ---
docker compose up -d
docker compose down -v
docker compose logs -f
docker compose exec <service> sh
docker compose build

# --- Cleanup ---
docker system prune -a --volumes
docker container prune
docker image prune -a

# --- Debug ---
docker inspect -f '{{.State.ExitCode}}' <container>
docker diff <container>
docker events
```

---

## روابط تعليمية لشرح Docker بالعربية (Arabic Docker Tutorials)

مجموعة مصادر عربية مجانية (فيديو وكورسات) لشرح Docker من الصفر حتى الاحتراف:

- [تعلم و احترف الدوكر في 50 دقيقة - شرح Docker بالعربي](https://www.youtube.com/watch?v=ieHB004jARI) — فيديو مكثف يغطي الأساسيات بسرعة.
- [كيف غيرت الـ Containers بناء البرمجيات عالمياً - كورس دوكر (Docker, Containers, Images, Volumes)](https://www.youtube.com/watch?v=Xnu-zoqopNM) — شرح للمفاهيم الأساسية والعملية.
- [Docker Tutorial in Arabic - دورة دوكر بالعربي (قائمة تشغيل كاملة)](https://www.youtube.com/playlist?list=PLzhWJrmO-SPV-yAYiDx7l78zWF4BAtnHt) — دورة شاملة بالتفصيل.
- [دورة احتراف الدوكر - Docker Master Course In Arabic (قائمة تشغيل)](https://www.youtube.com/playlist?list=PLzhWJrmO-SPX3iJF3oJsF-CteW2vnL54O) — مستوى متقدم بعد إتقان الأساسيات.
- [Docker Practical Course in Arabic - تطبيق عملي (سلسلة فيديوهات، مثال: إعداد تطبيق NodeJS)](https://www.youtube.com/watch?v=DsY6p8albWY) — أمثلة عملية تطبيقية خطوة بخطوة.
- [Docker Mastering Course in Arabic - شرح دوكر (قائمة تشغيل)](https://www.youtube.com/playlist?list=PLk9nVQF4WDct8p3NrYxgPmSZ89jvwWYGJ) — كورس متكامل آخر للمراجعة والتعمق.
- [Learn Docker from Zero to Hero (Arabic) - كورس على Udemy](https://www.udemy.com/course/docker-ar/) — كورس مدفوع منظم بشكل تدريجي من الصفر للاحتراف.

> ملاحظة: هذه روابط لقنوات ومنصات خارجية (يوتيوب و Udemy)، تأكد من توفرها عند فتحها لاحقاً لأن محتوى الفيديوهات قد يتغير أو يُحذف بمرور الوقت.

---

### Suggested Learning Path

1. Run pre-built images (`docker run hello-world`, `nginx`, `postgres`).
2. Write your first `Dockerfile` for a simple app; understand `CMD` vs `ENTRYPOINT`.
3. Learn volumes and networking so multi-container apps can talk to each other and persist data.
4. Move to Docker Compose for local multi-service development.
5. Learn multi-stage builds and image-size optimization for production-quality images.
6. Add healthchecks, resource limits, and security hardening (non-root users, minimal base images, secret handling).
7. Wire Docker into a CI/CD pipeline.
8. Learn one orchestrator in depth — start with Swarm if you want to stay in the Docker ecosystem, or go straight to Kubernetes if that's your target production environment.
9. Explore BuildKit features, multi-arch builds, and rootless Docker as you push toward production-grade, security-conscious setups.
