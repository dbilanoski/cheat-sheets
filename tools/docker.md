# Docker Notes

## Installation & Setup

Windows:

Winget can be used as below but best results are when downloaded and executed manually from official site.

```powershell
# Executed as administrator
winget -e --id Docker.DockerDesktop
```


Validate with: `docker -v` to output the installed version.


## Basic Commands

List of commonly used commands grouped by topic.

|                      |                                                                |                                                                                                                                              |
| -------------------- | -------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| **Topic**            | **Command**                                                    | **Description**                                                                                                                              |
| General              | `docker --version`                                             | Shows the installed Docker version.                                                                                                          |
| Image Management     | `docker pull <image_name>[:<tag>]`                             | Downloads an image from a registry (Docker Hub by default). Example: `docker pull ubuntu:latest`                                             |
| Image Management     | `docker build -t <image_name>[:<tag>] <path>`                  | Builds a Docker image from a Dockerfile. The `<path>` is usually `.`. Example: `docker build -t my-app:1.0 .`                                |
| Image Management     | `docker images`                                                | Lists all Docker images stored locally.                                                                                                      |
| Image Management     | `docker rmi <image_id> or <image_name>[:<tag>]`                | Removes one or more Docker images.                                                                                                           |
| Container Management | `docker run [OPTIONS] <image_name>[:<tag>] [COMMAND] [ARG...]` | Creates and starts a container from an image. Example: `docker run -d -p 8080:80 nginx`                                                      |
| Container Management | `docker ps`                                                    | Lists running containers.                                                                                                                    |
| Container Management | `docker ps -a`                                                 | Lists all containers (running and stopped).                                                                                                  |
| Container Management | `docker stop <container_id> or <container_name>`               | Stops a running container.                                                                                                                   |
| Container Management | `docker start <container_id> or <container_name>`              | Starts a stopped container.                                                                                                                  |
| Container Management | `docker restart <container_id> or <container_name>`            | Restarts a container.                                                                                                                        |
| Container Management | `docker rm <container_id> or <container_name>`                 | Removes one or more stopped containers.                                                                                                      |
| Container Management | `docker exec -it <container_id> or <container_name> <command>` | Executes a command inside a running container (often used with `-it` for an interactive shell). Example: `docker exec -it my-container bash` |
| Container Management | `docker logs <container_id> or <container_name>`               | Fetches the logs of a container.                                                                                                             |
| Inspection           | `docker inspect <container_id> or <image_name>`                | Displays detailed information about a container or image.                                                                                    |
| Docker Compose       | `docker-compose up -d`                                         | Builds, (re)creates, and starts the services defined in a `docker-compose.yml` file in detached mode.                                        |
| Docker Compose       | `docker-compose down`                                          | Stops and removes containers, networks, volumes, and images created by `docker-compose up`.                                                  |
| Volume Management    | `docker volume ls`                                             | Lists all docker volumes.                                                                                                                    |
| Volume Management    | `docker volume rm <volume_name>`                               | Removes a docker volume.                                                                                                                     |
| Network Management   | `docker network ls`                                            | Lists docker networks.                                                                                                                       |
| Network Management   | `docker network rm <network_id> or <network_name>`             | Removes a docker network.                                                                                                                    |


## Docker Hub

Public repository of Docker images at [https://hub.docker.com/](https://hub.docker.com/)


## HowTos

### Building An Image

Here we'll be building and image to run a python script.

1. Prepare your Python app's .py files.
2. Create `requirements.txt` file for the package dependencies.
3. Create `.dockerignore` file to exclude stuff not needed
   
```text
# Exclude the virtual environment
venv/
Include/
Lib/
Scripts/
pyvenv.cfg
  

# Exclude Python bytecode files
__pycache__/
*.pyc
*.pyo

  

# Exclude Dockerfile and .dockerignore itself (optional but good practice)
Dockerfile
.dockerignore

  
# Exclude other common files or directories
.git
*.md
*.csv
.env
/test
/docs
/Logs
```

4.  Create `Dockerfile` file with image building steps
   
   ```dockerfile
	# Specify which dockerhub image to use as a parent image
	# Naming convencion: official Python runtime version 3.11 in Alpine 3.19 lightweght Linux distro
	FROM python:3.11-alpine3.19

	# Set the working directory (in the container, app root folder)
	WORKDIR /app
	
	# Copy the requirements file to the /app directory
	COPY requirements.txt .
	
	# Install dependencies using copied requirements file
	# --no-cache-dir means do not keep the pip cache dir inside container to reduce it's size
	RUN pip install --no-cache-dir -r requirements.txt
	
	# Copy the application code
	# Everything is copied from the local app folder to the container app folder, except the exceptions written in the .dockerignore file
	COPY . .
	
	# Define the command to run the application
	# Syntax explanation:
	# CMD - run shell commands
	# First array item is an executable, 
	CMD ["python", "sample_app.py"]
```
   
5. 