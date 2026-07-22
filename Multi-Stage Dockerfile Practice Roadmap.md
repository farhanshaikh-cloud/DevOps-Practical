Below is a complete **Multi-Stage Dockerfile Practice Roadmap** that starts from the basics and progresses to advanced topics. It is designed for **AWS DevOps interviews**, hands-on practice, and real-world projects.

---

# Multi-Stage Dockerfile Practice (Scratch to Advanced)

## Prerequisites

Install:

* Docker Desktop (Windows) or Docker Engine (Linux)
* VS Code
* Git

Verify installation:

```bash
docker --version
docker compose version
docker info
```

---

# Level 1 - Dockerfile Basics

## Project

```
docker-practice/
│
├── app.py
├── requirements.txt
└── Dockerfile
```

### app.py

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Docker Practice"

app.run(host="0.0.0.0", port=5000)
```

### requirements.txt

```
Flask
```

---

## Dockerfile

```dockerfile
FROM python:3.12

WORKDIR /app

COPY . .

RUN pip install -r requirements.txt

EXPOSE 5000

CMD ["python","app.py"]
```

Build

```bash
docker build -t flask:v1 .
```

Run

```bash
docker run -d -p 5000:5000 flask:v1
```

---

# Practice Questions

Explain

```
FROM
WORKDIR
COPY
RUN
CMD
EXPOSE
```

---

# Level 2 - Docker Cache

Modify

```dockerfile
COPY requirements.txt .

RUN pip install -r requirements.txt

COPY . .
```

Observe

```
Docker uses cache
```

Commands

```bash
docker build -t flask:v2 .

docker history flask:v2
```

---

# Level 3 - Single Stage Dockerfile

```
FROM python:3.12

WORKDIR /app

COPY . .

RUN pip install -r requirements.txt

CMD ["python","app.py"]
```

Image Size

```
docker images
```

Notice image is larger.

---

# Level 4 - Multi Stage Dockerfile

Example

```dockerfile
############
# Builder
############

FROM python:3.12 AS builder

WORKDIR /install

COPY requirements.txt .

RUN pip install \
    --prefix=/install \
    -r requirements.txt

############
# Runtime
############

FROM python:3.12-slim

WORKDIR /app

COPY --from=builder /install /usr/local

COPY app.py .

EXPOSE 5000

CMD ["python","app.py"]
```

Build

```bash
docker build -t flask:multistage .
```

Compare

```
docker images
```

Observe reduced image size.

---

# Level 5 - Builder Stage

Learn

```
Builder Image

↓

Compile

↓

Install

↓

Copy only output

↓

Delete Builder
```

Commands

```
docker history

docker image inspect

docker inspect
```

---

# Level 6 - DotNet MultiStage

Builder

```dockerfile
FROM mcr.microsoft.com/dotnet/sdk:9.0 AS build

WORKDIR /src

COPY . .

RUN dotnet restore

RUN dotnet publish -c Release -o /publish
```

Runtime

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:9.0

WORKDIR /app

COPY --from=build /publish .

ENTRYPOINT ["dotnet","HelloWorld.dll"]
```

Practice

```
Why SDK?

Why Runtime?

Why publish?

Why restore?
```

---

# Level 7 - NodeJS MultiStage

Builder

```dockerfile
FROM node:22 AS build

WORKDIR /app

COPY package*.json .

RUN npm install

COPY . .

RUN npm run build
```

Runtime

```dockerfile
FROM nginx:latest

COPY --from=build /app/dist /usr/share/nginx/html
```

---

# Level 8 - Java MultiStage

```dockerfile
FROM maven:3.9-eclipse-temurin-21 AS build

WORKDIR /app

COPY . .

RUN mvn clean package

FROM eclipse-temurin:21-jre

WORKDIR /app

COPY --from=build /app/target/app.jar .

ENTRYPOINT ["java","-jar","app.jar"]
```

---

# Level 9 - Golang MultiStage

```dockerfile
FROM golang:1.24 AS builder

WORKDIR /app

COPY . .

RUN go build -o myapp

FROM alpine

WORKDIR /app

COPY --from=builder /app/myapp .

CMD ["./myapp"]
```

---

# Level 10 - Distroless Image

```dockerfile
FROM golang AS builder

WORKDIR /app

COPY . .

RUN go build -o app

FROM gcr.io/distroless/base

COPY --from=builder /app/app .

ENTRYPOINT ["./app"]
```

Learn

```
No shell

No package manager

Very secure

Small image
```

---

# Level 11 - Alpine Images

Compare

```
python

python-slim

alpine

distroless
```

Practice

```
docker images
```

Compare size

---

# Level 12 - Docker Ignore

Create

```
.git

.env

__pycache__

node_modules

*.log
```

```
.dockerignore
```

Observe build speed.

---

# Level 13 - Build Arguments

```dockerfile
ARG VERSION=1.0

ENV APP_VERSION=$VERSION
```

Build

```bash
docker build \
--build-arg VERSION=2.0 \
-t app:v2 .
```

---

# Level 14 - Environment Variables

```dockerfile
ENV PORT=5000
```

Run

```bash
docker run -e PORT=8080 image
```

---

# Level 15 - ENTRYPOINT vs CMD

Example

```dockerfile
ENTRYPOINT ["python"]

CMD ["app.py"]
```

Override

```bash
docker run image test.py
```

---

# Level 16 - Docker BuildKit

Enable

```bash
export DOCKER_BUILDKIT=1
```

Build

```bash
docker build .
```

---

# Level 17 - Buildx

```bash
docker buildx ls

docker buildx create --use

docker buildx inspect
```

Multi Architecture

```bash
docker buildx build \
--platform linux/amd64,linux/arm64 \
-t username/app:v1 \
--push .
```

---

# Level 18 - Multi Architecture Images

Understand

```
amd64

arm64

Apple Silicon

AWS Graviton
```

---

# Level 19 - Security Best Practices

Create user

```dockerfile
RUN useradd appuser

USER appuser
```

Avoid

```
root user
```

---

# Level 20 - Production Dockerfile

```dockerfile
FROM python:3.12 AS builder

WORKDIR /install

COPY requirements.txt .

RUN pip install \
--prefix=/install \
-r requirements.txt

FROM python:3.12-slim

RUN useradd appuser

WORKDIR /app

COPY --from=builder /install /usr/local

COPY app.py .

USER appuser

EXPOSE 5000

HEALTHCHECK CMD curl --fail http://localhost:5000 || exit 1

CMD ["python","app.py"]
```

---

# Advanced Topics

Practice

* Multi-stage with 3 stages
* Multi-stage with 4 stages
* Cache mounts
* Secrets during build
* BuildKit
* Buildx
* Multi-platform images
* Distroless images
* Scratch images
* OCI image format
* Image signing (Cosign)
* SBOM generation
* Image scanning (Trivy, Docker Scout)
* Layer optimization
* Rootless containers
* Health checks
* Docker Compose integration
* CI/CD with GitHub Actions or Jenkins

---

# Interview Questions

1. What is a Multi-Stage Dockerfile?
2. Why do we use Multi-Stage builds?
3. Difference between Single-Stage and Multi-Stage?
4. Why use `COPY --from=builder`?
5. What is `dotnet publish`?
6. Difference between SDK and Runtime images?
7. Difference between `CMD` and `ENTRYPOINT`?
8. What is `WORKDIR`?
9. Difference between `ADD` and `COPY`?
10. What is `.dockerignore`?
11. What are Docker layers?
12. How does Docker cache work?
13. What is BuildKit?
14. What is `docker buildx`?
15. Difference between `python`, `python:slim`, `alpine`, and Distroless images?
16. Why avoid running containers as `root`?
17. How do you reduce Docker image size?
18. How do you debug a Multi-Stage build?
19. What is the purpose of `HEALTHCHECK`?
20. How do you build multi-architecture images?

---

## Final Challenge Project (End-to-End)

Build a production-ready **Flask Notes API** using a Multi-Stage Dockerfile with the following requirements:

* Multi-stage build (`builder` + `runtime`)
* `python:3.12-slim` runtime image
* `.dockerignore`
* Non-root user
* `HEALTHCHECK`
* Environment variables (`ENV`)
* Build arguments (`ARG`)
* Optimized layer caching
* Docker Compose with PostgreSQL
* Push image to Docker Hub
* Scan image using Trivy or Docker Scout
* Build for both `linux/amd64` and `linux/arm64` using `docker buildx`
* Deploy on an AWS EC2 instance and verify the application is accessible.

Completing this project will give you experience with the same Docker practices commonly expected in DevOps and cloud engineering roles.
