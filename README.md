# lily4499-Node.js-Microservice-with-Docker-Kubernetes-and-Helm

# Node.js Microservice with Docker, Kubernetes, and Helm

## 📦 Project Overview
This project demonstrates how to build, deploy, debug, and maintain a **Node.js microservice** using:

- **Docker** for containerization
- **Kubernetes (Minikube or EKS)** for orchestration
- **Helm** for streamlined deployments
- **Readiness & Liveness probes** for container health checks
- **kubectl logs** for centralized debugging

---

## 🧱 Project Structure
```
nodejs-k8s-helm-project/
├── app.js
├── Dockerfile
├── package.json
├── deployment.yaml
├── service.yaml
└── helm-node/
    ├── Chart.yaml
    ├── values.yaml
    └── templates/
        ├── deployment.yaml
        └── service.yaml
```

---

## ✅ Step 1: Build and Run Locally with Docker
```bash
docker build -t laly9999/node-microservice:1 .
docker run -d -p 3000:3000 laly9999/node-microservice:1
```

---

## 🚀 Step 2: Set up Kubernetes Cluster

### Minikube (local)
```bash
minikube start
kubectl get nodes
```

### EKS (cloud)
```bash
eksctl create cluster --name node-microservice-cluster --region us-east-1 --nodes 2
aws eks update-kubeconfig --name node-microservice-cluster --region us-east-1
kubectl get nodes
```

---

## 📂 Step 3: Deploy to Kubernetes

### Apply Deployment and Service
```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl get all
```

---

## 🔍 Step 4: Debugging and Monitoring

### View Logs
```bash
kubectl get pods
kubectl logs <pod-name>
kubectl logs -f <pod-name>
```

### Describe Pod
```bash
kubectl describe pod <pod-name>
```

---

## 🛡️ Health Probes
Included in `deployment.yaml` and Helm chart:
```yaml
livenessProbe:
  httpGet:
    path: /
    port: 3000
  initialDelaySeconds: 5
  periodSeconds: 10

readinessProbe:
  httpGet:
    path: /
    port: 3000
  initialDelaySeconds: 3
  periodSeconds: 5
```

---

## 🎯 Step 5: Deploy with Helm
```bash
helm install nodeapp ./helm-node
kubectl get all
```

---

## 📌 Outcome
- 🚀 Node.js microservice Dockerized
- ⚙️ Deployed to Kubernetes (Minikube or EKS)
- 🛡️ Health-monitored with probes
- 🧠 Debuggable via `kubectl logs`
- 📦 Packaged and repeatable deployment with Helm

---

## 🌐 Optional Enhancements
- Add Ingress + TLS (Cert-Manager)
- Add Monitoring (Prometheus + Grafana)
- Add Logging Stack (EFK or Loki)

---

## 🧪 Test It!
```bash
kubectl get svc
curl <external-ip>
```

Enjoy deploying your microservice like a pro! 🚀

