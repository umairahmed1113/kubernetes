# Kubernetes Demo Project

This project demonstrates a simple **Kubernetes microservices setup** including:

* Backend Node.js service
* Frontend NGINX service
* Horizontal Pod Autoscaling (HPA)
* Metrics Server integration
* NGINX Ingress Controller
* Traffic routing and scaling

---

# Project Structure

```
k8s-demo/
│
├── backend.yaml
├── frontend.yaml
├── hpa.yaml
├── ingress.yaml
└── README.md
```

---

# Backend Deployment

The backend is a simple **Node.js HTTP server** running inside a container.

### Features

* Runs on **port 5000**
* Health check endpoint: `/health`
* CPU and memory limits configured
* Readiness and liveness probes enabled

---

## Deploy Backend

```
kubectl apply -f backend.yaml
```

---

## Verify Backend Pods

```
kubectl get pods -l app=backend
```

---

## Check Backend Service

```
kubectl get svc backend-service
```

---

## Test Backend

Port forward the service:

```
kubectl port-forward svc/backend-service 5000:5000
```

Then test:

```
curl http://localhost:5000
curl http://localhost:5000/health
```

Expected output:

```
{"message":"Hello from Backend"}
```

---

# Frontend Deployment

The frontend uses **NGINX** to simulate a web service.

### Deploy Frontend

```
kubectl apply -f frontend.yaml
```

### Verify Deployment

```
kubectl get pods -l app=frontend
kubectl get svc frontend-service
```

---

# Install Metrics Server

Metrics Server is required for **Horizontal Pod Autoscaler (HPA)**.

### Install Metrics Server

```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

### Edit Metrics Server Deployment

```
kubectl edit deployment metrics-server -n kube-system
```

Add the following flag:

```
--kubelet-insecure-tls
```

### Verify Installation

```
kubectl top nodes
kubectl top pods
```

---

# Horizontal Pod Autoscaler (HPA)

HPA automatically scales **backend pods** based on CPU utilization.

### HPA Configuration

| Setting          | Value |
| ---------------- | ----- |
| Minimum Pods     | 1     |
| Maximum Pods     | 5     |
| Target CPU Usage | 20%   |

### Deploy HPA

```
kubectl apply -f hpa.yaml
```

### Verify HPA

```
kubectl get hpa
```

---

# Testing the HPA

Open a shell inside a backend pod:

```
kubectl exec -it <backend-pod-name> -- sh
```

Install curl:

```
apk add curl
```

Generate traffic:

```
while true; do curl http://localhost:5000 > /dev/null; done
```

Watch scaling:

```
kubectl get hpa -w
kubectl get pods -w
kubectl top pods
```

You should see **additional backend pods being created automatically**.

---

# Install NGINX Ingress Controller

Install the ingress controller:

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
```

Verify installation:

```
kubectl get pods -n ingress-nginx
```

---

# Ingress Configuration

The ingress routes traffic as follows:

| Path | Service  |
| ---- | -------- |
| /    | Frontend |
| /api | Backend  |

### Deploy Ingress

```
kubectl apply -f ingress.yaml
```

---

# Configure Local DNS

Get node IP:

```
kubectl get nodes -o wide
```

Edit hosts file:

```
sudo nano /etc/hosts
```

Add:

```
<node-ip> backend.local
```

Example:

```
192.168.49.2 backend.local
```

---

# Test Ingress

### Test Frontend

```
curl http://backend.local/
```

### Test Backend

```
curl http://backend.local/api
```

Expected backend response:

```
{"message":"Hello from Backend"}
```

---

# Useful Kubernetes Commands

### View Pods

```
kubectl get pods
```

### View Services

```
kubectl get svc
```

### View Nodes

```
kubectl get nodes -o wide
```

### Monitor Resources

```
kubectl top pods
kubectl top nodes
```

### Watch Scaling

```
kubectl get hpa -w
```

---

# Technologies Used

* Kubernetes
* Docker
* Node.js
* NGINX
* Metrics Server
* Horizontal Pod Autoscaler
* NGINX Ingress Controller

---

# Learning Objectives

This project demonstrates:

* Kubernetes Deployments
* Kubernetes Services
* Health Probes
* Resource Limits
* Horizontal Pod Autoscaling
* Metrics Server Setup
* NGINX Ingress Routing
* Traffic Simulation and Scaling

---

# Author

Demo project for learning **Kubernetes deployment, scaling, and ingress routing**.
