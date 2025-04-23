#  Node.js Microservice Deployment with Docker, Kubernetes & Helm

```markdown


This project demonstrates how to containerize a simple Node.js microservice, deploy it to Kubernetes (via Minikube or Amazon EKS), configure health probes, enable centralized logging, and package it with Helm for repeatable deployments.

---

## 📁 Project Structure

```plaintext
.
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

## 🧱 Chapter 1: Create and Containerize Node.js Microservice

### `app.js`
```js
const express = require('express');
const app = express();
app.get('/', (req, res) => res.send('Node.js Microservice is live!'));
app.listen(3000, () => console.log('App running on port 3000'));
```

### `package.json`
```json
{
  "name": "node-microservice",
  "version": "1.0.0",
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "express": "^4.18.2"
  }
}
```

### `Dockerfile`
```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "app.js"]
```

### Docker Commands
```bash
docker build -t laly9999/node-microservice:1 .
docker run -d -p 3000:3000 laly9999/node-microservice:1
```

---

## ☸️ Chapter 2: Set Up Kubernetes Cluster

### Option 1: Minikube (local)
```bash
minikube start
kubectl get nodes
```

### Option 2: Amazon EKS (cloud)
```bash
eksctl create cluster --name node-microservice-cluster --region us-east-1 --nodes 2
aws eks update-kubeconfig --name node-microservice-cluster --region us-east-1
kubectl get nodes
```

---

## 🧩 Chapter 3: Kubernetes Deployment & Service


Kubernetes uses probes to check the health of your container. These are important for:

Liveness | Checks if the app is alive. If not, Kubernetes restarts it.
Readiness | Checks if the app is ready to serve requests. If not, Kubernetes removes it from the service endpoints.


### `deployment.yaml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: node-microservice
spec:
  replicas: 2
  selector:
    matchLabels:
      app: node-microservice
  template:
    metadata:
      labels:
        app: node-microservice
    spec:
      containers:
      - name: node-microservice
        image: laly9999/node-microservice:1
        ports:
        - containerPort: 3000
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

### `service.yaml`
```yaml
apiVersion: v1
kind: Service
metadata:
  name: node-microservice-service
spec:
  type: LoadBalancer
  selector:
    app: node-microservice
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
```

### Kubernetes Commands
```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl get all
```

---

## 🧪 Chapter 4: Debugging & Monitoring

When something goes wrong, logs are your best friend.


### View Logs
```bash
kubectl get pods
kubectl logs <pod-name>
```

### Describe Pod for Errors
```bash
kubectl describe pod <pod-name>
```

### Check Probe Status
```bash
kubectl get pods -o wide
```

---

## 🎯 Chapter 5: Helm Chart Deployment

### `helm-node/Chart.yaml`
```yaml
apiVersion: v2
name: helm-node
description: A Helm chart for Kubernetes
version: 0.1.0
```

### `helm-node/values.yaml`
```yaml
replicaCount: 2
image:
  repository: laly9999/node-microservice
  tag: "1"
service:
  type: LoadBalancer
  port: 80
containerPort: 3000
```


### `helm-node/templates/deployment.yaml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Chart.Name }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ .Chart.Name }}
  template:
    metadata:
      labels:
        app: {{ .Chart.Name }}
    spec:
      containers:
      - name: {{ .Chart.Name }}
        image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
        ports:
        - containerPort: {{ .Values.containerPort }}
        livenessProbe:
          httpGet:
            path: /
            port: {{ .Values.containerPort }}
          initialDelaySeconds: 5
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /
            port: {{ .Values.containerPort }}
          initialDelaySeconds: 3
          periodSeconds: 5
```

### `helm-node/templates/service.yaml`
```yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ .Chart.Name }}-service
spec:
  type: {{ .Values.service.type }}
  selector:
    app: {{ .Chart.Name }}
  ports:
    - protocol: TCP
      port: {{ .Values.service.port }}
      targetPort: {{ .Values.containerPort }}
```

### Helm Commands
```bash
helm install nodeapp ./helm-node
kubectl get all
```

---

## ✅ Outcome

By completing this project, you will have:

- A Dockerized Node.js application
- A Kubernetes deployment (via Minikube or EKS)
- Health probes (liveness & readiness) configured
- Centralized logging via `kubectl logs`
- A Helm chart for consistent, reusable deployments

---

## 📌 Notes

- Ensure Docker and Kubernetes are installed and running before proceeding.
- Update the image tag in Helm values and `deployment.yaml` as needed.
- Expose services externally using `minikube service` or your cloud LoadBalancer IP.

---

## 📎 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
```

Let me know if you’d like a `LICENSE` file or `.gitignore` added too!
