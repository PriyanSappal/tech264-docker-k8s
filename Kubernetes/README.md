# Kubernetes

## Why is Kubernetes needed
Kubernetes is needed to manage containerised applications at scale. It automates deployment, scaling, and operations of application containers across clusters of hosts, helping developers and system administrators to manage complex microservices architectures.

## Benefits of Kubernetes
- **Scalability**: Automatically scale applications based on demand.
- **Resource Optimization**: Efficiently manage resources to improve performance and reduce costs.
- **High Availability**: Provides failover mechanisms to ensure service uptime.
- **Portability**: Runs on various cloud providers and on-premises infrastructure.
- **Self-Healing**: Automatically restarts containers that fail and replaces or reschedules them.
- **Declarative Configuration**: Manages configurations as code using manifests.

## Success Stories
1. **Spotify**: Used Kubernetes to speed up development and improve infrastructure efficiency.
2. **Airbnb**: Improved service reliability and reduced operational complexity with Kubernetes.
3. **Pinterest**: Enhanced resource utilization and streamlined their infrastructure with Kubernetes.

## Kubernetes Architecture
Kubernetes architecture is based on a master-worker model. It includes multiple components that work together to maintain the desired state of applications.

### Diagram
![Kubernetes Architecture Diagram](kubernetes-architecture-diagram.png)

## The Cluster Setup
### What is a Cluster
A cluster in Kubernetes is a set of nodes (physical or virtual machines) used to run containerized applications. It consists of a **control plane** and one or more **worker nodes**.

### Master vs Worker Nodes
- **Master Node**: Manages the cluster, maintaining the desired state, scheduling workloads, and handling API requests.
- **Worker Node**: Runs containers and hosts application workloads. It has components like the kubelet, container runtime, and kube-proxy.

## Pros and Cons of Using Managed Service vs Launching Your Own
### Managed Service
- **Pros**: Simplifies management, automatic updates, high availability, and integrated security.
- **Cons**: Potentially higher cost and limited customisation.
### Self-Hosted
- **Pros**: Greater control, customisation, and possible cost savings.
- **Cons**: Requires expertise to manage, complex updates, and additional operational overhead.

## Control Plane vs Data Plane
- **Control Plane**: Manages the state of the cluster, making global decisions about scheduling and monitoring.
- **Data Plane**: Consists of worker nodes that run the actual workloads, executing the commands received from the control plane.

## Kubernetes Objects
Kubernetes uses several types of objects to manage applications, including:
- **Pods**: The smallest deployable unit, representing a single instance of a running process.
- **Deployments**: Manages stateless applications, handling updates and scaling.
- **ReplicaSets**: Ensures a specified number of pod replicas are running.
- **Services**: Defines a logical set of pods and a policy for accessing them.

## What Does it Mean a Pod is "Ephemeral"
Pods are considered ephemeral because they are temporary and can be created, destroyed, or replaced at any time, usually by automated processes like scaling events or node failures.

## How to Mitigate Security Concerns with Containers
- **Use Namespaces**: Isolate sensitive workloads using namespaces.
- **Resource Limits**: Set resource limits to prevent resource exhaustion attacks.
- **Pod Security Policies**: Enforce security standards on your pods.
- **Network Policies**: Control communication between pods and external traffic.

## Maintained Images
### What Are They
Maintained images are container images provided by trusted sources or organizations that regularly update and patch vulnerabilities.

### Pros and Cons of Using Maintained Images for Your Base Container Images
- **Pros**: Regular updates, security patches, and trusted sources improve reliability.
- **Cons**: Potential compatibility issues, larger image sizes, and dependency on external maintainers.

