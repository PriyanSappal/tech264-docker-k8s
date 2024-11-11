# Kubernetes
- [Kubernetes](#kubernetes)
  - [Why is Kubernetes needed](#why-is-kubernetes-needed)
  - [Benefits of Kubernetes](#benefits-of-kubernetes)
  - [Success Stories](#success-stories)
  - [Kubernetes Architecture](#kubernetes-architecture)
    - [Diagram](#diagram)
  - [The Cluster Setup](#the-cluster-setup)
    - [What is a Cluster](#what-is-a-cluster)
    - [Master vs Worker Nodes](#master-vs-worker-nodes)
  - [Pros and Cons of Using Managed Service vs Launching Your Own](#pros-and-cons-of-using-managed-service-vs-launching-your-own)
    - [Managed Service](#managed-service)
    - [Self-Hosted](#self-hosted)
  - [Control Plane vs Data Plane](#control-plane-vs-data-plane)
  - [Kubernetes Objects](#kubernetes-objects)
  - [What Does it Mean a Pod is "Ephemeral"](#what-does-it-mean-a-pod-is-ephemeral)
  - [How to Mitigate Security Concerns with Containers](#how-to-mitigate-security-concerns-with-containers)
  - [Maintained Images](#maintained-images)
    - [What Are They?](#what-are-they)
    - [Pros and Cons of Using Maintained Images for Your Base Container Images](#pros-and-cons-of-using-maintained-images-for-your-base-container-images)
  - [General commands for Kubernetes](#general-commands-for-kubernetes)
  - [Deploy App](#deploy-app)
  - [Deploy App with DB](#deploy-app-with-db)
  - [Blockers](#blockers)
- [Diagram with PV and PVC](#diagram-with-pv-and-pvc)
  - [Task: Create 2-tier deployment with PV for database](#task-create-2-tier-deployment-with-pv-for-database)
    - [Why Use PV and PVC?](#why-use-pv-and-pvc)
    - [Steps](#steps)
- [Kubernetes Autoscaling](#kubernetes-autoscaling)
- [Task: Use HPA to scale the app](#task-use-hpa-to-scale-the-app)
  - [Step 1: Install Apache Bench - this is to induce stress to CPU.](#step-1-install-apache-bench---this-is-to-induce-stress-to-cpu)
  - [Step 2: Define HPA for APP](#step-2-define-hpa-for-app)
  - [Step 3: Create the HPA](#step-3-create-the-hpa)
  - [Step 4 Load test with Apache Bench (ab)](#step-4-load-test-with-apache-bench-ab)

## Why is Kubernetes needed
Kubernetes is needed to manage containerised applications at scale. It automates deployment, scaling, and operations of application containers across clusters of hosts, helping developers and system administrators to manage complex microservices architectures.

## Benefits of Kubernetes
- **Scalability**: Automatically scale applications based on demand.
- **Resource Optimization**: Efficiently manage resources to improve performance and reduce costs.
- **High Availability**: Provides failover mechanisms to ensure service uptime.
- **Portability**: Runs on various cloud providers and on-premises infrastructure.
- **Self-Healing**: Automatically restarts containers that fail and replaces or reschedules them.
- **Declarative Configuration**: Manages configurations as code using manifests.
- **Open-Source**
- **Load-balancing**
- **Self-Healing**

## Success Stories
1. **Spotify**: Used Kubernetes to speed up development and improve infrastructure efficiency.
2. **Airbnb**: Improved service reliability and reduced operational complexity with Kubernetes.
3. **Pinterest**: Enhanced resource utilization and streamlined their infrastructure with Kubernetes.

## Kubernetes Architecture
Kubernetes architecture is based on a master-worker model. It includes multiple components that work together to maintain the desired state of applications.
- **Control plane (Master node)**: Manages the Kubernetes cluster, making scheduling decisions. It includes:
  - API Server: Main entry point of the cluster. 
  - etcd: A key-value store that stores all cluster data. 
  - Controller Manager: Monitors the cluster state and makes adjustments to meet the desired state. 
  - Scheduler: Assigns newly created pods to nodes based on resource availability, constraints and policies.
- **Worker nodes**: These nodes run the apps in pods:
  - Kubelet: The main agent on each worker node that receives instructions from the API server and manages pod lifecycle.
  - Kube-proxy: Manages network rules on each node to enable communication within and outside the cluster.
  - Container Runtime: The software (e.g., Docker, containerd) responsible for running containers.
- **Pods**: The smallest deployable units in Kubernetes, representing a single instance of a running process. Pods can contain one or more tightly coupled containers.
### Diagram
![alt text](../images/image-3.png)

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
- Never run containers with root privileges
- **Monitoring and Logging**: Should be monitored closely and check container activity.

## Maintained Images
### What Are They?
Maintained images are container images provided by trusted sources or organizations that regularly update and patch vulnerabilities.

### Pros and Cons of Using Maintained Images for Your Base Container Images
- **Pros**: Regular updates, security patches, and trusted sources improve reliability. More documentation. Smaller images.
- **Cons**: Potential compatibility issues, larger image sizes, and dependency on external maintainers.
![alt text](../images/image-4.png)

<br>

```mermaid
flowchart LR
    %% External Traffic Flow
    A[External Traffic] -->|Port 30001| B[Redirect]
    B -->|Port 3001| C[App Service]

    %% App Deployment and Pods
    subgraph App_Deployment[Application ReplicaSet]
        direction TB
        C --> D1[App Pod 1]
        C --> D2[App Pod 2]
        C --> D3[App Pod 3]
    end

    %% Deployment
    subgraph Deployment[Deployment]
        D1 -->|Contains| E1[App Image]
        D2 -->|Contains| E2[App Image]
        D3 -->|Contains| E3[App Image]
    end

    %% Database ReplicaSets
    subgraph Database_ReplicaSets[Database ReplicaSets]
        direction TB
        F1[DB Pod] --> G[DB Image]
    end

    %% Data Transfer Between App and DB
    D1 --- G
    D2 --- G
    D3 --- G

    %% Labels and Styling
    classDef purple fill:#D1C4E9,stroke:#6A1B9A,stroke-width:2px;
    classDef yellow fill:#FFEB3B,stroke:#FBC02D,stroke-width:2px;
    classDef blue fill:#B3E5FC,stroke:#0288D1,stroke-width:2px;
    
    class A purple
    class B,C yellow
    class App_Cluster yellow
    class D1,D2,D3 yellow
    class Replica_Group yellow
    class F1,G blue
    class Database_Cluster blue
```
## General commands for Kubernetes
* `kubectl get all`: this displays all the information (pods, services etc.)
* `kubectl get deploy` (you can even specify `replicasets`, `pods`, `service`): this displays just the deploys currently.
* `kubectl create -f <file-name>`: this will create/deploy. 
* ` kubectl delete pod <pod-name>`: this will delete a specific pod (if you delete one Kubernetes will make another one).
* `kubectl edit deploy nginx-deployment`: this will open a notepad editor and allow you to edit. You then save and exit the notepad once you have edited and it will save your changes. (`export KUBE_EDITOR=C:/Windows/notepad.exe` may need this if error message appears).
* `kubectl apply -f <file-name>`: if changes are made to a file, you can apply them using this command. 
* `kubectl scale --current-replicas=5 --replicas=6 deployment.apps/nginx-deployment`: this will change the number of replicas. 
* `kubectl delete -f <file-name>`: this will delete the file (yaml).
* `winpty kubectl exec -it <pod-name> -- sh`: to SSH into the pod and access the terminal to run commands. 
  
## Deploy App
1. Create folder `k8s-app-yaml-definitions`
2. Create two files [app-deploy.yml](../k8s-app-yaml-definitions/app-deploy.yml) and [app-service.yml](../k8s-app-yaml-definitions/app-service.yml)
3. Run these Kubernetes commands: `kubectl create -f app-deploy.yml` and `kubectl create -f app-service.yml`. 
4. As we have used `port 30002`. Search `localhost:30002`. 

## Deploy App with DB
1. Create folder `k8s-app-db-yaml-definitions`
2. You can use the same scripts as above. 
3. You need to add environment variable to the [app-deploy.yml](../k8s-app-db-yaml-definitions/app-deploy.yml). 
4. Make [db-deploy.yml](../k8s-app-db-yaml-definitions/db-deploy.yml) and [db-service.yml](../k8s-app-db-yaml-definitions/db-service.yml)
5. Run these Kubernetes commands: `kubectl create -f db-deploy.yml` and `kubectl create -f db-service.yml`, `kubectl create -f app-deploy.yml`, and `kubectl create -f app-service.yml`. 

## Blockers
* In your `app-service.yml` when connecting with the database you need to change the `NodePort` and it can't be the same port as the `db-service.yml`.
* Make sure you create the `db-deploy.yml` and `db-service.yml` as the app needs something to connect to. 

# Diagram with PV and PVC

```mermaid 
flowchart LR
    %% External Traffic Flow
    A[External Traffic] -->|Port 30001| B[Redirect]
    B -->|Port 3001| C[App Service]

    %% App Deployment and Pods
    subgraph App_Deployment[Application ReplicaSet]
        direction TB
        C --> D1[App Pod 1]
        C --> D2[App Pod 2]
        C --> D3[App Pod 3]
    end

    %% Deployment
    subgraph Deployment[Deployment]
        D1 -->|Contains| E1[App Image]
        D2 -->|Contains| E2[App Image]
        D3 -->|Contains| E3[App Image]
    end

    %% Database ReplicaSets
    subgraph Database_ReplicaSets[Database ReplicaSets]
        direction TB
        F1[DB Pod] --> G[DB Image]
    end

     %% Persistent Volume and Persistent Volume Claim
    subgraph PVC_PV[PVC and PV]
        direction TB
        H[PVC] --> I[PV]
    end

    %% PVC to DB Pod
    F1 --- H

    %% Data Transfer Between App and DB
    D1 --- G
    D2 --- G
    D3 --- G

    %% Database Pod Accessing PVC
    F1 -->|Attaches to| I

    %% Labels and Styling
    classDef purple fill:#D1C4E9,stroke:#6A1B9A,stroke-width:2px;
    classDef yellow fill:#FFEB3B,stroke:#FBC02D,stroke-width:2px;
    classDef blue fill:#B3E5FC,stroke:#0288D1,stroke-width:2px;
    classDef green fill:#C8E6C9,stroke:#388E3C,stroke-width:2px;
    
    class A purple
    class B,C yellow
    class App_Cluster yellow
    class D1,D2,D3 yellow
    class Replica_Group yellow
    class F1,G blue
    class Database_Cluster blue
    class H,I green
```

1. Traffic Flow:
* External Traffic (purple box) enters through Port 30001 and is redirected to Port 3001.
* It then reaches the App Service (yellow box), which handles incoming requests to the application.
2. Application ReplicaSet:
* The App Service distributes incoming requests to three App Pods (1, 2, and 3), providing redundancy and load distribution.
* Each App Pod contains an App Image, ensuring each pod runs the same application version.
3. Deployment:
* Each App Pod is linked to a specific instance of the App Image (purple boxes), representing the deployed application. 
* These are the image that are stored on Docker Hub.
4. Database ReplicaSets
* The DB Pod (blue box) is the database instance for the application, ensuring data storage and access.
It contains a DB Image, which represents the database configuration and software.
5. Persistent Volume and PVC
* PVC (Persistent Volume Claim) and PV (Persistent Volume) (green boxes) provide persistent storage for the database.
* The DB Pod attaches to the PVC, which binds to the PV, ensuring database data persists even if the pod restarts.


* Color Coding Summary
    * Purple: External and application images.
    * Yellow: Application service and pods.
    * Blue: Database components.
    * Green: Persistent storage components.

## Task: Create 2-tier deployment with PV for database

### Why Use PV and PVC?
* **Data Persistence**: Ensures that essential data, such as database entries, is not lost when pods are restarted or rescheduled, which is crucial for stateful applications.
* **Decoupling**: PVCs allow applications to request storage independently of the underlying storage infrastructure, making the deployment portable and adaptable.
* **Scalability**: Storage can be scaled and managed separately, enabling better resource allocation and usage in a dynamic Kubernetes environment.

### Steps
1. Create a **Persistent Volume (PV)** [PV yaml script](../k8s-pv-pvc/mongo-pv.yml):
Define a **PersistentVolume (PV)** resource to allocate storage in the cluster. The PV is the actual physical storage, which can be on local disks, NFS, or cloud storage (e.g., AWS EBS, GCP Persistent Disks).

    **Why**: Provides a storage resource that remains available even if pods are deleted or recreated.
2. Create a **Persistent Volume Claim (PVC)** [PVC yaml script](../k8s-pv-pvc/mongo-pvc.yml): Define a **PersistentVolumeClaim (PVC)** that requests storage of a specific size and access mode.
The PVC dynamically binds to an available PV that matches the claim requirements.
    
    **Why**: Ensures applications can request storage without knowing the details of the underlying storage resource. This keeps the app configuration flexible and portable.

    ![alt text](../images/image-5.png)

3. Attach **PVC** to the MongoDB Pod [DB deploy yaml script formatted for PV and PVC](../k8s-pv-pvc/db-deploy.yml):
In the MongoDB Deployment YAML, define a volume that references the PVC and mount it within the MongoDB container at `/data/db`.
    
    **Why**: Allows the MongoDB database to store data on persistent storage, which remains available even if the pod is restarted or rescheduled.
4. Verify Persistent Data:
     1. You need to remove the commands to automate the seeding of the DB in the [App deploy script without the commands](../k8s-pv-pvc/app-deploy.yml).
     2. Manually seed the DB by using the `winpty kubectl exec -it pod/sparta-app-deployment-ccdd85f8d-hjzfj -- sh` and seed the database (`node seeds/seed.js`). The records that show up on the `/posts` page should stay the same. 
   ![alt text](<../images/Screenshot 2024-11-08 123621.png>)
     3. Delete the `db-deploy.yml` or you could delete the pod. 
     4. Recreate it. 
     5. The same `/posts` page should come up.
   
    **Why**: Verifies the persistence of data, confirming that the PV and PVC are functioning correctly and providing durability.
*Blockers*:
1. You can have the PV and PVC in the same file. Makes it easier to create, instead of running each one by one. 
2. Make sure you indent properly. Standardise. 
# Kubernetes Autoscaling 
* Kubernetes (K8s) provides multiple autoscaling mechanisms to manage workloads efficiently. 
* Autoscaling enables Kubernetes clusters to dynamically adjust resources to meet demand, optimize performance, and reduce costs. 
* Here are the main types of autoscaling available in Kubernetes:
1. **Horizontal Pod Autoscaler (HPA)**
* Description: The Horizontal Pod Autoscaler automatically scales the number of pod replicas in a Deployment, ReplicaSet, or StatefulSet based on CPU or memory utilization or custom metrics.
* Key Features:
    * Metric-Based Scaling: HPA uses built-in metrics like CPU and memory or external metrics through custom metric providers.
    * Dynamic Scaling: Adjusts the number of pod replicas dynamically based on workload demands.
    * Use Cases: Ideal for stateless applications like web servers, APIs, and microservices that require scaling up and down based on traffic or workload.
    * How it Works: HPA periodically checks specified metrics (e.g., CPU).
    If the metric value surpasses the threshold, HPA increases pod replicas; if it drops below, HPA reduces replicas.
    HPA is configured through the kubectl autoscale command or YAML specifications.
* Configuration Example:
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: example-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: example-deployment
  minReplicas: 1
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 50
```
1. **Vertical Pod Autoscaler (VPA)**
* Description: The Vertical Pod Autoscaler automatically adjusts the CPU and memory requests/limits of containers within a pod based on actual usage. VPA is useful for optimizing resource utilization without modifying the number of pod replicas.

* Key Features:
    * Resource Optimization: Ensures pods have the right amount of resources to prevent over-provisioning or under-provisioning.
    * Automatic Resource Adjustment: Updates pod resource requests and limits based on observed usage patterns.
    * Use Cases: Suitable for applications with varying resource requirements or for long-running workloads where efficient use of resources is critical.
    * How it Works: VPA monitors the resource usage of pods and provides recommendations. Based on these recommendations, VPA can adjust resource requests and  limits. Pods may need to be restarted for resource changes to take effect.
* Configuration Example:
```yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: example-vpa
spec:
  targetRef:
    apiVersion: "apps/v1"
    kind:       Deployment
    name:       example-deployment
  updatePolicy:
    updateMode: "Auto"
```
1. **Cluster Autoscaler (CA)**
* Description: The Cluster Autoscaler dynamically adjusts the number of nodes in a cluster based on the overall resource requirements. It is typically used with cloud providers like AWS, GCP, and Azure that support auto-scaling node groups.

* Key Features:
    * Node Scaling: Adds nodes when there are pending pods that cannot be scheduled due to insufficient resources.
    * Resource Optimization: Removes underutilized nodes if they are no longer needed, reducing costs.
    * Use Cases: Ideal for environments where workload demand varies widely, ensuring nodes scale based on the cluster's overall resource needs.
    * How it Works: Cluster Autoscaler checks if there are unschedulable pods due to resource constraints. If additional resources are needed, it adds more nodes to the cluster. When resources are no longer needed, CA may scale down the cluster by removing underutilized nodes.
* Configuration: Cluster Autoscaler is typically configured at the cloud provider level with specific parameters for min/max node counts, node types, and scaling policies.

4. **Custom Autoscaling with KEDA** (Kubernetes Event-Driven Autoscaling)
* Description:
KEDA is an external Kubernetes component that provides event-driven autoscaling based on various types of custom metrics or external event sources, such as message queues, databases, or HTTP requests.
* Key Features:
    * Event-Driven: Scales workloads based on external events (e.g., messages in a queue, database load).
    * Custom Metrics Support: Allows autoscaling based on a wide range of custom metrics beyond CPU and memory.
    * Use Cases: Suitable for applications with bursty traffic patterns or for use cases requiring integration with external systems (e.g., Azure Event Hubs, AWS SQS).
    * How it Works: KEDA monitors specified event sources and metrics. When a scaling event is triggered, it automatically adjusts the number of replicas. KEDA works in conjunction with HPA, using external metrics to trigger scaling.
* Configuration Example:
```yaml
apiVersion: keda.sh/v1alpha1
kind: ScaledObject
metadata:
  name: example-scaledobject
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: example-deployment
  triggers:
    - type: rabbitmq
      metadata:
        queueName: my-queue
        host: "amqp://guest:guest@rabbitmq"
        queueLength: "5"
```

# Task: Use HPA to scale the app

## Step 1: Install Apache Bench - this is to induce stress to CPU.
1) Download the Win64 on https://www.apachelounge.com/download/. This will be a zip file (you need to extract the file).
2) Move these files to a file called `.apache`. 
3) Create an alias. `alias ab=~/.apache/bin/ab.exe`

## Step 2: Define HPA for APP
1) Add this to the end of your `app-deploy.yml` script. This allows Kubernetes to monitor CPU usage and automatically autoscale the number of pods. 
```yaml
 resources:
          requests:
            cpu: "100m"     # Request 100 millicores (0.1 CPU)
          limits:
            cpu: "500m"     # Limit the app to 500 millicores (0.5 CPU)
```
2) Apply this: `kubectl apply -f app-deply.yml`. 

## Step 3: Create the HPA
1) Create a HPA that will scale the number of pods between 2 and 10. 
[HPA script](../k8s-pv-pvc/hpa.yml)
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: sparta-app
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50  # Target 50% CPU utilization
```
2) Apply this: `kubectl apply -f hpa.yml`
3) Verify this setup using `kubectl get hpa`

## Step 4 Load test with Apache Bench (ab)
1) To simulate load on your application and test if the HPA scales the Pods, use Apache Bench (ab): `ab -n 10000 -c 100 http://<Node.js-Service-External-IP>:<port>/`
   * -n 10000: Total number of requests to send.

   * -c 100: Number of concurrent requests.

2) Nano into `app-service.yml` and change "NodePort" to "LoadBalancer".
```yaml
---
apiVersion: v1
kind: Service
metadata:
  name: app-svc
  namespace: default
spec:
  ports:
  - nodePort: 30003   # The port on each node where the service is exposed
    port: 80    # The port that the service listens on.
    targetPort: 3000    # The port on the pod that the service forwards traffic to.
  selector:
    app: sparta-app # Label to match service to deployment
  type: LoadBalancer # used to be NodePort, changed for external IP for hpa
```
3) Apply and update these changes: `kubectl apply -f app-service.yml`
Check this has refreshed: `kubectl get services`.

4) To simulate load on your application and test if the HPA scales the Pods, use Apache Bench (ab): `ab -n 20000 -c 200 http://localhost:30003/`
To view if **pods** have been made: `kubectl get pods`. You should see that the number of **pods** should be higher.