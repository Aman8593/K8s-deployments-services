
# Kubernetes service deployment

## 1. Create an image on docker
If image Not Available in the Registry:

Ensure that the Docker image my-image:1.0 is built and pushed to a Docker registry (like Docker Hub, AWS ECR, GCP, etc.)

### Step 1: Docker Login

Log in to your Docker registry (Docker Hub in this case):

```bash
docker login
```

### Step 2: Build and Tag the Docker Image
```bash
docker build -t your-dockerhub-username/my-image:1.0 .
```
### Step 3: Push the Docker Image to the Registry
Once the image is built, push it to DockerHub (or another registry):
```bash
docker push your-dockerhub-username/my-image:1.0
```

### Docker File
```bash
FROM nginx:1.21
COPY . /usr/share/nginx/html
```

## 2. Install Windows Subsystem for Linux (WSL)

### Open PowerShell as Administrator:
Press Windows + X, select Windows PowerShell (Admin).
### Enable WSL:
In PowerShell, run the following command:
```bash
wsl --install
```
This command installs the WSL feature and a Linux distribution (by default Ubuntu).
### Note:
Ensure Virtualization Technology (VTx) is enabled in your system BIOS, as it’s required for WSL to work properly.

## 3.Docker Desktop and Minikube Setup

This guide provides step-by-step instructions to install Docker Desktop and Minikube.

## Step 1: Install Docker Desktop

Visit the official Docker Desktop website and download the installation file:

```bash
# Go to the Docker Desktop official download page
https://www.docker.com/products/docker-desktop
```
#### Ensure Docker Desktop is running as Minikube will use Docker to create a local Kubernetes cluster.

## Step 2: Install Minikube
### Open PowerShell as Administrator

Install Minikube
You can use Chocolatey to install Minikube. If you don’t have Chocolatey installed, you can get it from https://chocolatey.org/install.

Install Minikube:

You can use Chocolatey to install Minikube:
```bash
choco install minikube
```

Verify Installation:
```bash
minikube version
```
## 3.Start Minikube Cluster

## Installation

1. **Start Docker Desktop**  
   Ensure that Docker Desktop is running in the background.

2. **Start Minikube**  
   To start Minikube, open a terminal and run the following command:
   ```bash
   minikube start
   ```
3. **Stop Minikube** 
```bash
minikube stop

```
4. **Delete Minikube**
```bash
minikube delete
```

# Kubernetes Deployment Setup Guide

## Introduction
This guide provides steps to create a Kubernetes deployment using a YAML file. It covers how to create a project directory, write a deployment file, and configure it to run a Docker container in Kubernetes.

## 1. Create Project Directory

1. Create a new directory for your project and navigate to it:
   ```bash
   mkdir k8sproject
   cd k8sproject
   ```

2. Use a terminal (e.g., PowerShell or Linux terminal) to create the Kubernetes deployment file
```bash
touch deployment.yaml
```

3. Open the file with your code editor:
```bash
code deployment.yaml
```

4. Copy the following YAML into the file:
```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
  labels:
    app: my-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-container
        image: my-image:1.0
        ports:
        - containerPort: 80
```
### 
**Note**:
This YAML file describes a deployment with 3 replicas of a container (my-container) that runs the Docker image my-image:1.0.

## 2. Deploy the Application

### Apply the Deployment
```bash
kubectl apply -f deployment.yaml
```

### Check Deployment Status
```bash
kubectl get deployments
```

### Check Pods
```bash
kubectl get pods
```

### Edit the Deployment
Edit the YAML File
```bash
sudo nano deployment.yaml
```
Apply Changes
```bash
kubectl apply -f deployment.yaml
```

## 3. Manage Services and Resources
Check Services:
```bash
kubectl get services
```
Access Kubernetes Dashboard
Minikube provides an easy-to-use dashboard.
```bash
minikube dashboard
```

List all available Minikube addons:
```bash
minikube addons list
```

Enable the metrics server (used for monitoring):
```bash
minikube addons enable metrics-server
```
## 4. Clean up Kubernetes Resources

### Delete Pods/Deployments/Services:

To delete specific resources:
```bash
kubectl delete pods pod-name
kubectl delete deployments deployment-name
kubectl delete services service-name
```

To delete all deployments:
```bash
kubectl delete deployments --all
```

To delete a particular deployment by name:
```bash
kubectl delete deployment my-deployment
```


## Create Service

Create a new `service.yaml` file and add the following configuration:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: LoadBalancer  # or NodePort
  ports:
    - port: 80         # Port your application listens on
      targetPort: 80   # Port in your container
  selector:
    app: my-app        # Should match the labels in your deployment
```

Apply the Service Configuration
```bash
kubectl apply -f service.yaml
```
Get the Service IP
```bash
kubectl get services
```
# 📌 Impoertant Note on Deployment and Services:
**Kubernetes deployment is in charge with keeping the pods running in the cluster, a service is in charge of granting network access to those pods.**

---

## 📌 Introduction
This guide explains the purpose and usage of `deployment.yaml` and `service.yaml` in Kubernetes. These files help manage applications in a Kubernetes cluster by defining how they run and how they are exposed.

---

## 🚀 What is `deployment.yaml`?
A **Deployment** in Kubernetes manages the lifecycle of application instances (Pods). It ensures that the desired number of replicas are running and handles updates smoothly.

### **🔧 Key Components of `deployment.yaml`**
- **`replicas`**: Number of pod instances.
- **`selector`**: Identifies the pods this deployment manages.
- **`template`**: Defines the pod specification (containers, images, ports, etc.).

---

## 🌍 What is `service.yaml`?
A **Service** in Kubernetes provides a stable endpoint to expose pods to other services or external users. It allows communication between different components of an application.

### **🔧 Key Components of `service.yaml`**
- **`selector`**: Identifies the pods this service routes traffic to.
- **`ports`**: Defines the exposed ports.
- **`type`**: Specifies the exposure method (`ClusterIP`, `NodePort`, `LoadBalancer`).

---

## 📌 Summary Table
| File               | Purpose |
| ---- | --- |
| `deployment.yaml` | Defines how many instances (pods) of an application should run and manages updates. |
| `service.yaml`    | Exposes the pods internally or externally to make them accessible. |

---

## 🎯 Next Steps
- 📖 Learn more about Kubernetes [here](https://kubernetes.io/docs/concepts/)
- 🔥 Experiment with different `Service` types (`NodePort`, `LoadBalancer`)
- 🛠️ Try rolling updates and scaling using `kubectl scale`

---

## Access Your Application:

- **If you used LoadBalancer, you can access your application at http://<EXTERNAL-IP>.**
- **If you used NodePort, you need to access it via one of your node's IP addresses along with the NodePort assigned.**
- **For example, if your node IP is 192.168.99.100 and the NodePort is 30000, you would access it at http://192.168.99.100:30000**

## Reach out to me for any queries 

## Implementation Screenshots
![Screenshot (140)](https://github.com/user-attachments/assets/2750ecf2-3f3e-41c5-8953-c0b850e4b7af)

![Screenshot (138)](https://github.com/user-attachments/assets/4a98ca8a-cfe2-4e40-996a-7581b8a08cff)

![Screenshot (134)](https://github.com/user-attachments/assets/653ed7a8-025b-463f-b3b9-9f58bd511c88)

![Screenshot (135)](https://github.com/user-attachments/assets/60ade93f-5a49-4439-a4e9-2ffcd6b5c742)

![Screenshot (136)](https://github.com/user-attachments/assets/31463466-d027-482e-b227-1ad40c7a855a)

![Screenshot (137)](https://github.com/user-attachments/assets/48b78f26-bd35-4f3e-94ef-ffa684e748bb)



