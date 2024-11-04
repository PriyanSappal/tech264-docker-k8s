
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
- **Containerisation**: Tools like Docker package and deploy services easily.
- **Service Discovery**: Tools like Consul or Eureka for identifying service locations dynamically.

### Benefits
1. **Scalability**: Each service can be scaled independently.
2. **Resilience**: Failure in one service does not crash the entire application.
3. **Faster Deployment**: Independent development and deployment improve agility.

---

## Docker

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

