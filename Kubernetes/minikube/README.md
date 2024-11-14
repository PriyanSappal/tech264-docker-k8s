# Minikube

Using Terraform to formulate the infrastructure. 

# Deploying a 2-tier application on Minikube
- [Minikube](#minikube)
- [Deploying a 2-tier application on Minikube](#deploying-a-2-tier-application-on-minikube)
  - [Objectives:](#objectives)
  - [Provisioning Script for Kubernetes Metrics Server and Application Deployment](#provisioning-script-for-kubernetes-metrics-server-and-application-deployment)
    - [Step 1: Apply Metrics Server Components](#step-1-apply-metrics-server-components)
    - [Step 2: Patch the Metrics Server Deployment](#step-2-patch-the-metrics-server-deployment)
    - [Step 3: Restart the Metrics Server Deployment](#step-3-restart-the-metrics-server-deployment)
    - [Step 4: Apply Application Deployment](#step-4-apply-application-deployment)
    - [Step 5: Display All Resources](#step-5-display-all-resources)
- [App deployment using Minikube](#app-deployment-using-minikube)
  - [Minikube Deployment with NodePort and Nginx Reverse Proxy](#minikube-deployment-with-nodeport-and-nginx-reverse-proxy)
    - [Step 1: Define the Kubernetes Service with NodePort](#step-1-define-the-kubernetes-service-with-nodeport)
    - [Step 2: Apply the Kubernetes Service Configuration](#step-2-apply-the-kubernetes-service-configuration)
    - [Step 3: Get the Minikube IP Address](#step-3-get-the-minikube-ip-address)
    - [Step 4: Configure Nginx as a Reverse Proxy](#step-4-configure-nginx-as-a-reverse-proxy)
    - [Step 5: Test and Reload Nginx Configuration](#step-5-test-and-reload-nginx-configuration)
    - [Step 6: Access Your Application](#step-6-access-your-application)
    - [Recap of the Setup](#recap-of-the-setup)
- [Minikube with LoadBalancer](#minikube-with-loadbalancer)
  - [App deployment](#app-deployment)
  - [Setting Up Minikube Tunnel with a LoadBalancer](#setting-up-minikube-tunnel-with-a-loadbalancer)
    - [Step 1: Define the Kubernetes Service as a LoadBalancer](#step-1-define-the-kubernetes-service-as-a-loadbalancer)
    - [Step 2: Start Minikube Tunnel](#step-2-start-minikube-tunnel)
    - [Step 3: Check the LoadBalancer IP](#step-3-check-the-loadbalancer-ip)
    - [Step 4: Configure Nginx as a Reverse Proxy](#step-4-configure-nginx-as-a-reverse-proxy-1)
    - [Step 5: Test and Reload Nginx Configuration](#step-5-test-and-reload-nginx-configuration-1)
    - [Step 6: Access Your Application](#step-6-access-your-application-1)
- [Provisioning Docker, Minikube and Nginx](#provisioning-docker-minikube-and-nginx)
  - [Key concepts](#key-concepts)
    - [Why We Need to Restart the Script with Docker Group Permissions](#why-we-need-to-restart-the-script-with-docker-group-permissions)
    - [Why We Avoid Running Minikube as Root](#why-we-avoid-running-minikube-as-root)
  - [Overview of Steps](#overview-of-steps)
    - [1. Installing and Configuring Docker](#1-installing-and-configuring-docker)
      - [Adding the `ubuntu` User to the Docker Group](#adding-the-ubuntu-user-to-the-docker-group)
    - [2. Installing and Configuring Minikube](#2-installing-and-configuring-minikube)
    - [3. Starting Minikube as the `ubuntu` User](#3-starting-minikube-as-the-ubuntu-user)
    - [4. Installing and Configuring Nginx](#4-installing-and-configuring-nginx)
    - [Final Output and Status](#final-output-and-status)

## Objectives:

- Deploy containerised 2-tier deployment (app and database) on a single VM using Minikube
- Database should use a PV of 100 MB
- Use HPA to scale the app, min 2, max 10 replicas - load test to check it works
- Use NodePort service and Nginx reverse proxy to expose the app service to port 80 of the instance's public IP
- Make sure that minikube start happens automatically on re-start of the instance

## Provisioning Script for Kubernetes Metrics Server and Application Deployment

This script uses Minikube’s `kubectl` command to provision and configure the Kubernetes Metrics Server, then deploys an application using a specified YAML file.

### Step 1: Apply Metrics Server Components

```bash
minikube kubectl -- apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

- **Explanation**: Downloads and applies the YAML manifest for the Kubernetes Metrics Server from the official repository. The Metrics Server is a cluster add-on that collects resource usage data, such as CPU and memory, from nodes and pods, making it available for tools like `kubectl top`.

### Step 2: Patch the Metrics Server Deployment

```bash
minikube kubectl -- patch deployment metrics-server -n kube-system --type='json' -p='[
  {
    "op": "add",
    "path": "/spec/template/spec/containers/0/args/-",
    "value": "--kubelet-insecure-tls"
  }
]'
```

- **Explanation**: Patches the Metrics Server deployment to add an argument that allows insecure TLS connections to the Kubelet. This is often needed for local environments or self-signed certificates where secure Kubelet connections might not be available.

  - **Deployment**: `metrics-server`
  - **Namespace**: `kube-system`
  - **Patch Operation**: Adds the `--kubelet-insecure-tls` argument to the `args` list of the container specification, allowing it to bypass TLS certificate verification.

### Step 3: Restart the Metrics Server Deployment

```bash
minikube kubectl -- rollout restart deployment metrics-server -n kube-system
```

- **Explanation**: Restarts the Metrics Server deployment in the `kube-system` namespace. This restart ensures that the patched configuration takes effect.

### Step 4: Apply Application Deployment

```bash
minikube kubectl -- apply -f app-deploy.yml
```

- **Explanation**: Deploys an application to the Kubernetes cluster using a configuration file (`app-deploy.yml`). This YAML file should define the application’s deployment specifications, such as replicas, image, ports, etc.

### Step 5: Display All Resources

```bash
minikube kubectl -- get all
```

- **Explanation**: Lists all Kubernetes resources (pods, services, deployments, etc.) in the current namespace, providing an overview of the cluster's current state and ensuring that the Metrics Server and application deployment are running correctly.

---

This script automates the provisioning of the Metrics Server and application, applying essential configurations for both and verifying that all resources are up and running.

# App deployment using Minikube


## Minikube Deployment with NodePort and Nginx Reverse Proxy

This guide explains how to set up a Minikube deployment with a `NodePort` service and configure Nginx to forward traffic to your service.

### Step 1: Define the Kubernetes Service with NodePort

Create a Kubernetes service configuration file, `app-deployment.yml`, with the following content:

```yaml
---
apiVersion: v1
kind: Service
metadata:
  name: sparta-app-svc-1
  namespace: default
spec:
  ports:
    - nodePort: 30001  # External port on the host for accessing the service
      port: 3000        # Internal port inside the container
      targetPort: 3000  # Port where the app is running in the pod
  selector:
    app: sparta-app-1  # Label to match service to deployment
  type: NodePort
```

In this configuration:

- **nodePort** (`30001`): This is the port exposed on your Minikube host for accessing the service externally.
- **port** (`3000`): This is the port used internally within your Kubernetes cluster.
- **targetPort** (`3000`): This is the port the application is listening to within the pod.
- **type: NodePort**: This exposes the service on a static port (`30001`) on the host, making it accessible externally.

### Step 2: Apply the Kubernetes Service Configuration

Apply the configuration using Minikube to create the service:

```sh
minikube kubectl -- apply -f app-deployment.yml
```

This command will create the `sparta-app-svc-1` service with a `NodePort` of `30001` to expose your app externally.

### Step 3: Get the Minikube IP Address

To access the service externally, first retrieve the IP address of your Minikube instance:

```sh
minikube ip
```

This will return the IP address where Minikube is running (e.g., `192.168.49.2`). Use this IP to configure Nginx.

### Step 4: Configure Nginx as a Reverse Proxy

Next, configure Nginx to forward incoming traffic on port `80` to the `NodePort` (`30001`) of the Minikube service. Edit your Nginx configuration:

```nginx
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    server_name _;

    access_log /var/log/nginx/access_80.log;
    error_log /var/log/nginx/error_80.log;

    location / {
        proxy_pass http://192.168.49.2:30001;  # Replace with your Minikube IP and NodePort
    }
}
```

### Step 5: Test and Reload Nginx Configuration

1. **Test the Nginx configuration** for syntax errors:

    ```sh
    sudo nginx -t
    ```

2. **Reload Nginx** to apply the changes:

    ```sh
    sudo systemctl reload nginx
    ```

3. **Check the status of Nginx** to ensure it is running properly:

    ```sh
    sudo systemctl status nginx
    ```

### Step 6: Access Your Application

Now, you can access your application via `http://<your-ec2-instance-ip>` on port `80`, and Nginx will forward the requests to the Minikube service running on port `30001`.

---

### Recap of the Setup

- **Kubernetes Service**: Defined as `NodePort` to expose the app on port `30001`.
- **Minikube Tunnel**: Not required since you’re using `NodePort`, but Minikube IP is used in the Nginx configuration.
- **Nginx Reverse Proxy**: Configured to forward traffic from port `80` to Minikube’s IP and `NodePort` (`30001`).

This setup enables external access to your Minikube service through the EC2 instance with Nginx acting as the reverse proxy.

# Minikube with LoadBalancer

## App deployment

```yaml
---
# SPARTA APP DEPLOYMENT
apiVersion: apps/v1  # specify api to use for deployment
kind : Deployment  # kind of service/object you want to create
metadata:
  name: app-deployment-2
spec:
  selector:
    matchLabels:
      app: sparta-app-2 # look for this labe/tag to match the k8n service

  # Creaate a ReplicaSet with instances/pods
  replicas: 5
  template:
    metadata:
      labels:
        app: sparta-app-2
    spec:
      containers:
      - name: sparta-app-2
        image:  priyansappal1/sparta-app:v1
        ports:
        - containerPort: 3000
```

## Setting Up Minikube Tunnel with a LoadBalancer

This guide will help you expose a Kubernetes service on Minikube using a LoadBalancer, with traffic routed through Nginx.

### Step 1: Define the Kubernetes Service as a LoadBalancer

Create a service configuration file, `app-deployment.yml`, with the following content:

```yaml
---
apiVersion: v1
kind: Service
metadata:
  name: sparta-app-svc-2
  namespace: default
spec:
  ports:
    - protocol: TCP
      port: 30002           # This is the port your clients will use.
      targetPort: 3000       # This is the port your application is running on.
  selector:
    app: sparta-app-2        # Label to match service to deployment
  type: LoadBalancer
```

In this configuration:

- **port** (`30002`) is the external port exposed by the service.
- **targetPort** (`3000`) is the port on which your app is running inside the pod.
- **type: LoadBalancer** will make the service accessible through a LoadBalancer IP.

Apply the configuration using Minikube:

```sh
minikube kubectl -- apply -f app-deployment.yml
```

### Step 2: Start Minikube Tunnel

Run `minikube tunnel` on your machine to set up the LoadBalancer IP:

```sh
minikube tunnel
```

> **Note:** Running `minikube tunnel` may require root privileges and will keep the LoadBalancer IP accessible while it's active.

### Step 3: Check the LoadBalancer IP

After starting `minikube tunnel`, get the external IP assigned to your service:

```sh
minikube kubectl -- get services sparta-app-svc-2

# EXTERNAL-IP: 192.168.49.3
```

The `EXTERNAL-IP` column will display the IP assigned by Minikube.

### Step 4: Configure Nginx as a Reverse Proxy

Edit your Nginx configuration to forward requests on port `9000` to the LoadBalancer IP and port `30002`:

```nginx
server {
    listen 9000;
    listen [::]:9000;
    server_name _;

    access_log /var/log/nginx/access_9000.log;
    error_log /var/log/nginx/error_9000.log;

    location / {
        proxy_pass http://192.168.49.3:30002;  # Replace with your LoadBalancer IP and port.
    }
}
```

### Step 5: Test and Reload Nginx Configuration

1. Test the Nginx configuration for syntax errors:

    ```sh
    sudo nginx -t
    ```

2. If the test passes, reload Nginx to apply the changes:

    ```sh
    sudo systemctl reload nginx
    ```

3. Check the Nginx status to ensure it’s running:

    ```sh
    sudo systemctl status nginx
    ```

### Step 6: Access Your Application

Now, you can access your application through `http://<your-ec2-instance-ip>:9000`, which will route traffic to your Minikube service via the LoadBalancer.

---

This setup allows your EC2 instance to forward traffic to your Minikube cluster using an emulated LoadBalancer. Remember to keep `minikube tunnel` running as it provides the external IP for your LoadBalancer service.'

# Provisioning Docker, Minikube and Nginx

This script provisions Docker, Minikube, and Nginx on a server. It automates the installation and configuration of each component, ensuring Minikube uses Docker as its driver and setting up Nginx as a reverse proxy to expose a service running on Minikube.

## Key concepts

### Why We Need to Restart the Script with Docker Group Permissions

When we add the `ubuntu` user to the `docker` group, this change does not take effect immediately for the current session. Normally, group changes only take effect after the user logs out and logs back in. However, since the script is running in a non-interactive environment, we cannot rely on a logout/login to apply the new group permissions. Instead, we use the `exec sg docker "$0"` command to restart the script with the `docker` group permissions applied.

- **Effect of `sg docker "$0"`**: This command re-runs the entire script within a new shell session where the `ubuntu` user is a member of the `docker` group.
- **Why This Is Necessary**: Minikube requires the user to have non-root Docker access to start containers. By re-running the script, we ensure that the `ubuntu` user has immediate access to Docker without needing to log out and back in, thus allowing Minikube to start successfully.

### Why We Avoid Running Minikube as Root

Running Minikube as a non-root user, like `ubuntu`, is best practice for security and environment consistency. Here’s why:

1. **Security Concerns**: Running Minikube as `root` would mean that all Kubernetes containers and components, along with any processes they spawn, would have root-level access. This can create significant security risks, as any vulnerability in those containers could lead to privilege escalation.

2. **Docker Permission Management**: The Docker daemon itself runs as `root`, but by granting `docker` group access to the `ubuntu` user, we enable Minikube to start and manage containers through Docker without requiring root permissions.

3. **Environment Consistency**: Using a dedicated, non-root user to manage Minikube is consistent with Kubernetes’ general principle of running applications with the minimum privileges necessary. This separation of privileges helps prevent unintentional system changes and limits the impact of potential misconfigurations.

By re-running the script with `docker` group permissions applied to the `ubuntu` user, we set up Minikube to operate with restricted privileges, following best practices for security and maintainability.

---

## Overview of Steps

1. **Provision Docker**: Installs Docker and configures permissions for the `ubuntu` user.
2. **Provision Minikube**: Installs Minikube and sets it to use Docker as its driver.
3. **Start Minikube**: Starts Minikube as the `ubuntu` user.
4. **Provision Nginx**: Installs and configures Nginx to act as a reverse proxy to Minikube’s exposed service.

---

### 1. Installing and Configuring Docker

The first part of the script updates system packages, installs Docker, and adds the `ubuntu` user to the Docker group.

```bash
# Update and upgrade system packages
sudo apt-get update -y
sudo DEBIAN_FRONTEND=noninteractive apt-get upgrade -y

# Install dependencies and add Docker's GPG key
sudo apt-get install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add Docker's repository to Apt sources
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker packages
sudo apt-get update
sudo DEBIAN_FRONTEND=noninteractive apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

#### Adding the `ubuntu` User to the Docker Group

After Docker is installed, the `ubuntu` user is added to the Docker group to allow non-root access to Docker. The script then re-executes itself using `exec sg docker "$0"` to apply the group change without requiring a logout/login.

```bash
# Check if the 'ubuntu' user is in the docker group; add if not
if ! groups ubuntu | grep -q '\bdocker\b'; then
    sudo usermod -aG docker ubuntu
    echo "Restarting script to apply Docker group permissions..."
    exec sg docker "$0"
    exit
fi
```

---

### 2. Installing and Configuring Minikube

The script downloads and installs Minikube, setting it to use Docker as the driver.

```bash
# Download Minikube binary
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

# Install Minikube binary
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64

# Set Docker as the Minikube driver
minikube config set driver docker
```

---

### 3. Starting Minikube as the `ubuntu` User

To ensure Minikube runs with the correct permissions, it is started as the `ubuntu` user. This section runs the `minikube start` command and exports Minikube's IP address for later use.

```bash
sudo -u ubuntu -i bash <<'EOF'
echo "Starting Minikube as 'ubuntu' user..."
minikube start
minikube status

# Get Minikube IP
MINIKUBE_IP=$(minikube ip)
echo "Exported Minikube IP: $MINIKUBE_IP"
EOF
```

Here’s a breakdown of the code:

- **`sudo -u ubuntu -i bash`**: This part of the command switches to the `ubuntu` user and initiates an interactive shell (`-i`) as `ubuntu`, which allows us to run commands in that user’s environment.

- **`<<'EOF'`**: This is a "Here Document" syntax, which allows us to run multiple commands within the `sudo` session as `ubuntu`. Everything between `<<'EOF'` and the closing `EOF` is executed in the `ubuntu` user’s environment.

- **`echo "Starting Minikube as 'ubuntu' user..."`**: Prints a message indicating that Minikube is starting under the `ubuntu` user.

- **`minikube start`**: Starts the Minikube cluster, using Docker as the container driver (which we configured earlier). By running this as `ubuntu`, it uses `ubuntu`’s environment and permissions.

- **`minikube status`**: Checks and prints the current status of Minikube, confirming that it’s running and which components are active.

- **`MINIKUBE_IP=$(minikube ip)`**: Retrieves Minikube’s IP address and assigns it to the `MINIKUBE_IP` variable. This IP is useful for accessing Minikube services externally, such as setting up a reverse proxy.

- **`echo "Exported Minikube IP: $MINIKUBE_IP"`**: Prints the exported Minikube IP to confirm that it’s been successfully retrieved and stored.

- **`EOF`**: This signifies the end of the "Here Document," meaning all commands up to this point will be executed as the `ubuntu` user within the `sudo -u ubuntu` session. 

In summary, this block:
- Switches to the `ubuntu` user.
- Starts Minikube as `ubuntu`, verifies its status, retrieves its IP address, and prints it.V

---

### 4. Installing and Configuring Nginx

The script installs Nginx and sets it up as a reverse proxy. It updates the Nginx configuration to proxy traffic from the server’s IP to the Minikube NodePort.

```bash
# Install Nginx
sudo DEBIAN_FRONTEND=noninteractive apt-get install -y nginx

# Configure Nginx reverse proxy to Minikube IP and port
sudo sed -i "s|try_files \$uri \$uri/ =404;|proxy_pass http://$MINIKUBE_IP:30001;|" /etc/nginx/sites-available/default

# Check for syntax errors and restart Nginx
sudo nginx -t
sudo systemctl restart nginx
```

---

### Final Output and Status

The script outputs Minikube and Nginx statuses and configurations at each stage, ensuring each component is set up correctly before moving to the next. This structured approach ensures Docker permissions are applied effectively, Minikube is correctly configured to use Docker, and Nginx routes traffic to Minikube services.