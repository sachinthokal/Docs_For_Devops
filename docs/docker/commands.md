## Essential Docker Commands

Docker allows you to build, run, and manage applications inside lightweight containers.

| Command | Description | Example |
| :--- | :--- | :--- |
| `docker pull` | Downloads an image from Docker Hub | `docker pull nginx` |
| `docker run` | Creates and starts a new container from an image | `docker run -d -p 80:80 --name web nginx` |
| `docker ps` | Lists running containers (`-a` shows all containers) | `docker ps -a` |
| `docker stop` | Safely stops one or more running containers | `docker stop web` |
| `docker start` | Starts a previously stopped container | `docker start web` |
| `docker exec` | Runs a command inside a running container | `docker exec -it web bash` |
| `docker logs` | Displays the logs or output of a container | `docker logs -f web` |
| `docker images` | Lists all downloaded Docker images on your machine | `docker images` |
| `docker rm` | Removes a stopped container | `docker rm web` |
| `docker rmi` | Removes an unused Docker image | `docker rmi nginx` |
| `docker build` | Builds a Docker image from a `Dockerfile` | `docker build -t myapp:1.0 .` |
| `docker compose up` | Starts multi-container apps defined in `docker-compose.yml` | `docker compose up -d` |

### Docker Tips

* **Detached Mode (`-d`):** Runs the container in the background, freeing up your terminal prompt.
* **Interactive Mode (`-it`):** Connects your terminal directly to the container's interactive terminal.
* **System Cleanup:** Run `docker system prune` to clear stopped containers, unused networks, and unreferenced images to reclaim disk space.
