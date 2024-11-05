
# Docker
- [Docker](#docker)
  - [Differences Between Virtualisation and Containerisation](#differences-between-virtualisation-and-containerisation)
    - [Virtualisation](#virtualisation)
    - [Containerisation](#containerisation)
    - [Summary of Differences](#summary-of-differences)
  - [What Is Usually Included in a Container vs. Virtual Machine?](#what-is-usually-included-in-a-container-vs-virtual-machine)
    - [Container](#container)
    - [Virtual Machine](#virtual-machine)
  - [Benefits](#benefits)
    - [Virtual Machine Over Traditional Architecture](#virtual-machine-over-traditional-architecture)
    - [Containerisation Benefits](#containerisation-benefits)
  - [Microservices](#microservices)
    - [What Are They?](#what-are-they)
    - [How Are They Made Possible?](#how-are-they-made-possible)
    - [Benefits](#benefits-1)
  - [Docker](#docker-1)
    - [What Is It?](#what-is-it)
    - [Alternatives](#alternatives)
    - [How It Works (Docker Architecture/API)](#how-it-works-docker-architectureapi)
    - [Success Story Using Docker](#success-story-using-docker)
- [How Docker works?](#how-docker-works)
- [Using Docker in GitBash](#using-docker-in-gitbash)
  - [Docker Work Flow - Nginx Container](#docker-work-flow---nginx-container)
    - [Run commands within a container](#run-commands-within-a-container)
- [Task: Push host-custom-static-webpage container image to Docker Hub](#task-push-host-custom-static-webpage-container-image-to-docker-hub)
- [Task: Automate docker image creation using a Dockerfile](#task-automate-docker-image-creation-using-a-dockerfile)
  - [1. Build the Custom Docker Image](#1-build-the-custom-docker-image)
  - [2. Run the Container](#2-run-the-container)
  - [3. Test the Container](#3-test-the-container)
  - [4. Log in to Docker Hub](#4-log-in-to-docker-hub)
  - [5. Tag the Image for Docker Hub](#5-tag-the-image-for-docker-hub)
  - [6. Push the Custom Image to Docker Hub](#6-push-the-custom-image-to-docker-hub)
  - [7. Share the Docker Run Command](#7-share-the-docker-run-command)

## Differences Between Virtualisation and Containerisation

### Virtualisation
- **Definition**: Virtualisation is the process of creating a virtual version of a physical computing resource, such as a virtual machine (VM).
- **How it Works**: It uses a hypervisor to emulate hardware, allowing multiple operating systems to run on a single physical machine.
- **Resource Isolation**: Each VM has its own OS, drivers, and libraries, which results in more overhead compared to containers.
- **Use Cases**: Often used for running multiple applications requiring different operating systems on the same hardware.

### Containerisation
- **Definition**: Containerisation encapsulates an application and its dependencies into a container, using the host system’s OS kernel.
- **How it Works**: Containers share the host operating system but isolate application processes using namespaces and control groups (cgroups).
- **Resource Isolation**: More lightweight compared to VMs, as they do not require separate OS instances.
- **Use Cases**: Ideal for microservices architecture and cloud-native applications.

### Summary of Differences
| Aspect              | Virtualisation                         | Containerisation                    |
|---------------------|----------------------------------------|-------------------------------------|
| Isolation Level     | Full OS isolation                      | Process-level isolation             |
| Resource Usage      | High overhead                          | Lightweight                         |
| Startup Time        | Minutes                                | Seconds                             |
| Portability         | Limited                                | Highly portable                     |
| Use Cases           | Legacy applications, multiple OS       | Microservices, cloud-native apps    |

## What Is Usually Included in a Container vs. Virtual Machine?

### Container
- **Application Code**: The main application being containerized.
- **Dependencies**: Libraries, environment variables, and other required software.
- **Container Runtime**: Tools that allow containers to run, like Docker Engine.
- **Lightweight OS**: Shares the host OS kernel, only necessary binaries are included.

### Virtual Machine
- **Guest Operating System**: A full OS instance, including kernel and drivers.
- **Application Code**: The software running on the virtualized OS.
- **Hypervisor**: Software to manage VMs, like VMware or Hyper-V.
- **Virtualised Hardware**: Simulated network interfaces, disks, and memory.

## Benefits

### Virtual Machine Over Traditional Architecture
1. **Isolation**: Complete OS isolation ensures robust security between VMs.
2. **Resource Utilisation**: Can run multiple VMs on a single physical machine, maximizing hardware usage.
3. **Legacy Support**: Useful for running legacy applications on modern infrastructure.

### Containerisation Benefits
1. **Efficiency**: Uses fewer resources than VMs and starts up faster.
2. **Portability**: Easily deployed across different environments.
3. **Microservices Compatibility**: Perfect for microservices architecture.

---

## Microservices

### What Are They?
- **Definition**: Microservices are an architectural style that structures an application as a collection of small, autonomous services.
- **Characteristics**: Each service is independent, responsible for a specific function, and can be developed and deployed separately.

### How Are They Made Possible?
- **API Communication**: Microservices communicate over HTTP/REST or messaging queues.
- **Containerisation** (Top priority): Tools like Docker package and deploy services easily.
- **Service Discovery**: Tools like Consul or Eureka for identifying service locations dynamically.
- **DevOps and CI/CD**: Support the management and deployment of microservices. 

### Benefits
1. **Scalability**: Each service can be scaled independently.
2. **Resilience**: Failure in one service does not crash the entire application.
3. **Faster Deployment**: Independent development and deployment improve agility.

---

## Docker

![alt text](../images/image.png)

https://dockerlabs.collabnix.com/beginners/difference-docker-vm.html

### What Is It?
- **Definition**: Docker is a platform for developing, shipping, and running applications in containers.
- **Purpose**: It allows developers to package applications with all dependencies into a single container.

### Alternatives
- **Kubernetes**: A powerful container orchestration tool.
- **Podman**: A daemonless container engine.
- **LXC/LXD**: Linux containers for system-level virtualization.
- **CRI-O**: Lightweight container runtime for Kubernetes.

### How It Works (Docker Architecture/API)
1. **Docker Engine**: The runtime for running containers.
2. **Docker Images**: Templates used to create containers.
3. **Docker Containers**: The running instances of Docker images.
4. **Docker CLI**: Command-line interface for interacting with Docker.
5. **Docker API**: REST API for automating Docker commands and managing containers.

### Success Story Using Docker
- **Spotify**: Utilized Docker to improve development and testing environments, accelerating software delivery. Docker allowed the team to ensure a consistent setup across development, staging, and production environments.

# How Docker works?
![alt text](../images/image-1.png)

* *API* is used between the **daemon** (server) and the **client**. 
* **Registry** includes **Docker Hub** which stores the container images. 
* The **Client** and the **DOCKER_HOST** are in the local machine. 

# Using Docker in GitBash
 
Commands to know:
- `docker --help`: displays commands you can use.
- `docker images`: useful for checking what images are available locally, so you can manage, update, or remove them as needed.
- `docker run hello-world`: A message that tells you that your docker is set up and working correctly.
- `docker ps`: Will display any containers running.
 
## Docker Work Flow - Nginx Container
- `docker run -d -p 80:80 nginx`: A command that runs a container from the nginx image in detached mode and maps port 80 of the host machine to port 80 in the container. Thisd will be viewable on a webpage for the IP `127.0.0.1` or simply input `localhost`.

  - `-d`: Detached mode. Makes it run in the background.
  - `-p host_port:container_port`: Maps port on the host machine to the port in the container. Allows external traffic to the host's port to be routed to the container's port.  
- `docker stop <container name>`: Used to stop a container.
- `docker ps --all (or -a)`: Shows all containers, including ones that are not currently active.
- `docker
- `docker start <container name>`: Start up the chosen container.
- `docker remove --force (or -f) <container name>`: Force removes the container, even if active. To do it without forcing, you must first use `docker stop`.

- `alis tf="terraform"`: Sets up an alias for the `terraform` command. Can be used to reduce input.
 
### Run commands within a container
- `docker exec -it <container ID> sh`: Used to execute commands within a specified container.
  - `-it`: Combines -i (interactive) and -t (allocates a pseudo-TTY) options to allow interaction with the container's terminal.
  - `sh`: The shell command that opens a shell session inside the container.
 
*Note! This will give you the following error: `the input device is not a TTY.  If you are using mintty, try prefixing the command with 'winpty'`. Fear not! This is normal. To fix this, we can use the `alias` command we saw ea:*
 
- `alias docker="winpty docker"`: This will replace `docker` with `winpty docker`, adding that preface we required from the error.
 
Running that `exec` command will now work, as we've solved he prexifxing issue AND reduced manual input required in the future.
 
The `#` means that we are now inside the container.
 
- `uname -a`: We can use this command to see the information regarding the container.
- `apt-get update -y`: Updates the local package index to ensure the package lists are current.
- `-y`: Flag for "yes" to skip manual inputs.
- `apt-get upgrade -y`: Upgrades all installed packages to their latest versions based on the current package index
- `apt-get install sudo`: Install sudo onto our container (we can do)

# Task: Push host-custom-static-webpage container image to Docker Hub

![alt text](<../images/Screenshot 2024-11-05 155125.png>)
1. Commit the Running Container to an Image: `docker commit <image-id> <YOUR_DOCKER_HUB_USERNAME>/<IMAGE_NAME>`
2. Authenticate yourself: `docker login`.
3. Push the image: `docker push <YOUR_DOCKER_HUB_USERNAME>/<IMAGE_NAME>`
4. Verify the image on **Docker Hub**. 
5. Delete the Local Image. Remove the image from your local Docker environment to test pulling it from Docker Hub: `docker rmi <YOUR_DOCKER_HUB_USERNAME>/<IMAGE_NAME>`
6. Run your image: `docker run -d -p 81:80 <YOUR_DOCKER_HUB_USERNAME>/<IMAGE_NAME>`
7. ![alt text](../images/image-2.png) You can see that on port 81 your localhost is displaying that image. 

# Task: Automate docker image creation using a Dockerfile

## 1. Build the Custom Docker Image
Use the `docker build` command to build your image. Make sure you are in the directory where your `Dockerfile` and `index.html` are located.

```bash
docker build -t tech2xx-nginx-auto:v1 .
```
- **-t**: This flag is used to tag the image. Here, `tech2xx-nginx-auto` is the name of your image, and `:v1` is the version tag.
- **.**: The dot represents the current directory (where the `Dockerfile` is located).

## 2. Run the Container
Run a container from your custom image:

```bash
docker run -d -p 80:80 tech2xx-nginx-auto:v1
```
- **-d**: Runs the container in detached mode (in the background).
- **-p 80:80**: Maps port 80 on the host machine to port 80 in the container.
- `tech2xx-nginx-auto:v1`: The name and tag of your image.

## 3. Test the Container
- Open a web browser and visit `http://localhost` (or `http://<your-docker-host-IP>` if not running on your local machine).
- You should see your custom Nginx page with the content from `index.html`.

## 4. Log in to Docker Hub
If you haven't logged in already, use the following command to log in to your Docker Hub account:

```bash
docker login
```
You will be prompted to enter your Docker Hub username and password. Or if already logged in it will just say succeeded. 

## 5. Tag the Image for Docker Hub
Tag your image with your Docker Hub username and the desired repository name:

```bash
docker tag tech2xx-nginx-auto:v1 <YOUR_DOCKER_HUB_USERNAME>/tech2xx-nginx-auto:v1
```
- Replace `<YOUR_DOCKER_HUB_USERNAME>` with your Docker Hub username.

## 6. Push the Custom Image to Docker Hub
Use the `docker push` command to upload your image to Docker Hub:

```bash
docker push <YOUR_DOCKER_HUB_USERNAME>/tech2xx-nginx-auto:v1
```
- This will push your custom image to your Docker Hub account.

## 7. Share the Docker Run Command
Once the image is pushed, you can share the following command so others can run your container:

```bash
docker run -d -p 80:80 <YOUR_DOCKER_HUB_USERNAME>/tech2xx-nginx-auto:v1
```
- Make sure to replace `<YOUR_DOCKER_HUB_USERNAME>` with your actual Docker Hub username.

---

Your image is now hosted on Docker Hub and can be pulled and run by anyone using the provided `docker run` command!
