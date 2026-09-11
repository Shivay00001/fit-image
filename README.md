# 🖼️ fit-image

A lightweight, high-performance HTTP service written in **Go**, containerized with Docker for seamless deployment on any laptop or server.

[![Go Version](https://img.shields.io/badge/Go-1.20-00ADD8?logo=go)](https://go.dev/)
[![Docker](https://img.shields.io/badge/Docker-Ready-2496ED?logo=docker)](https://www.docker.com/)
[![License](https://img.shields.io/badge/License-VisionQuantech%20Custom-red)](LICENSE)

---

## 🚀 Overview

**fit-image** is a minimal, production-ready Go microservice built directly on the standard library — no external dependencies, no frameworks, no bloat. It exposes a single HTTP endpoint that reports live system operational status along with the current server timestamp.

Because it compiles to a single static binary and ships in an Alpine-based Docker image, the service is extremely fast to build, tiny in footprint, and trivially portable across environments.

## ✨ Features

- ⚡ **Zero-dependency** — built entirely on Go's standard library (`net/http`, `log`, `fmt`, `time`)
- 🐳 **Docker-first deployment** — single-stage Alpine build for minimal image size
- 📡 **Built-in health/status endpoint** — returns live system status with a real-time timestamp
- 🔧 **Single static binary** — compiles to one executable with no runtime dependencies
- 🧩 **Single-responsibility design** — clean entry point, easy to extend with additional routes

## 🏗️ Architecture / How It Works

The entire application lives in `main.go` and follows a deliberately simple request-handling flow:

```
Client Request ──► :8080 ──► Go HTTP Server (net/http)
                                   │
                                   ▼
                         Root Handler ("/")
                                   │
                                   ▼
              "System Operational: <current timestamp>"
```

### Request Lifecycle

1. **Startup** — `main()` registers a handler function on the root path `/` using `http.HandleFunc`, which attaches it to Go's `DefaultServeMux`.
2. **Listening** — The service logs a startup message and blocks on `http.ListenAndServe(":8080", nil)`, binding to port **8080** on all interfaces. `log.Fatal` ensures the process exits with a logged error if the server fails to start or crashes.
3. **Handling requests** — For every incoming request to `/`, the handler writes a plain-text response:
   ```
   System Operational: 2026-01-01 12:00:00.000000000 +0000 UTC
   ```
   The timestamp is generated fresh per-request via `time.Now()`, making the endpoint useful as a **liveness probe / health check** — a changing response proves the process is alive and serving traffic.

### Project Structure

```
fit-image/
├── main.go        # HTTP server entry point and root handler
├── go.mod         # Go module definition (Go 1.20, zero dependencies)
├── Dockerfile     # Alpine-based container build (golang:1.20-alpine)
├── LICENSE        # VisionQuantech Custom Commercial License
└── README.md      # This file
```

## 🐳 Running with Docker (Recommended)

The included `Dockerfile` uses `golang:1.20-alpine`, copies the source, compiles the binary, and runs it — all in one stage.

### 1. Build the image

```bash
docker build -t fit-image .
```

### 2. Run the container

```bash
docker run -d -p 8080:8080 --name fit-image fit-image
```

### 3. Verify it's running

```bash
curl http://localhost:8080/
```

Expected output:

```
System Operational: 2026-01-01 12:00:00.000000000 +0000 UTC
```

### Useful container commands

```bash
# View logs
docker logs -f fit-image

# Stop and remove
docker stop fit-image && docker rm fit-image
```

> **Note:** This repository does not include a `docker-compose.yml`. The plain `docker build` / `docker run` flow above is the supported deployment path. If you prefer Compose, you can add your own:
> ```yaml
> services:
>   fit-image:
>     build: .
>     ports:
>       - "8080:8080"
> ```
> and then run `docker-compose up -d --build`.

## 🛠️ Running Locally (Without Docker)

**Prerequisite:** Go 1.20+ installed.

```bash
# Run directly
go run main.go

# Or build a binary and execute it
go build -o app
./app
```

Then visit `http://localhost:8080/` in your browser or via `curl`.

## ⚙️ Configuration

| Setting | Value | Notes |
|---|---|---|
| Port | `8080` | Hardcoded in `main.go` |
| Endpoint | `/` | Returns status + timestamp |
| Response format | `text/plain` | No JSON envelope |

## 📄 License

This project is distributed under the **VisionQuantech Custom Commercial License**:

- ✅ **Free** for personal, educational, non-financial, and non-earning use
- 💰 **Revenue share (15–30%)** required for individuals/indie developers earning from projects using this software
- 🏢 **Commercial license required** for business/enterprise use — contact **visionquantech@proton.me**

See [LICENSE](LICENSE) for full terms.

---

<p align="center">© 2026 Shivay00001 / VisionQuantech — All rights reserved.</p>