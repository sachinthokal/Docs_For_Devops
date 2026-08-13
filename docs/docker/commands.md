# Docker Complete Commands Reference ⚡

Categorized command sheet for managing images, containers, networks, volumes, and Compose stacks.

---

## 1. Image Management

| Command | Description | Example |
| :--- | :--- | :--- |
| `docker build` | Build an image from a Dockerfile. | `docker build -t myapp:1.0 .` |
| `docker images` | List all local images. | `docker images` |
| `docker pull` | Pull an image from Docker Hub. | `docker pull nginx:alpine` |
| `docker push` | Push an image to registry. | `docker push username/myapp:1.0` |
| `docker rmi` | Remove one or more images. | `docker rmi myapp:1.0` |

---

## 2. Container Lifecycle Commands

```bash
# Run container in detached mode with port mapping
docker run -d -p 8080:80 --name web-server nginx:alpine

# List active containers
docker ps

# List all containers (including stopped)
docker ps -a

# Stop / Start / Restart container
docker stop web-server
docker start web-server
docker restart web-server

# Force remove container
docker rm -f web-server

# Inspect live container resource usage
docker stats
```

---

## 3. Debugging & Executing Inside Containers

```bash
# View live logs of a container
docker logs -f web-server

# Execute an interactive bash/sh session inside running container
docker exec -it web-server sh

# Inspect low-level details (IP, mounts, status)
docker inspect web-server
```

---

## 4. Volume & Network Management

```bash
# Volumes (Data Persistence)
docker volume create app_data
docker volume ls
docker run -d -v app_data:/var/lib/mysql mysql:latest

# Networks (Container Inter-communication)
docker network create app_net
docker network ls
docker run -d --network app_net --name backend-api my-api:v1
```

---

## 5. Docker Compose Commands

```bash
# Start multi-container stack in background
docker compose up -d

# Stop and remove containers, networks, and volumes
docker compose down

# View logs from all services in Compose stack
docker compose logs -f
```