
# Microservices Kubernetes Deployment Guide (Minikube)

This README provides **step-by-step, beginner-friendly instructions** to deploy and test a Node.js microservices application on **Kubernetes using Minikube (Ubuntu / EC2)**.

---

## 📌 Architecture Overview

The application consists of **4 Node.js microservices**:

| Service         | Port | Type                 |
| --------------- | ---- | -------------------- |
| Gateway Service | 3003 | NodePort (Exposed)   |
| User Service    | 3000 | ClusterIP (Internal) |
| Product Service | 3001 | ClusterIP (Internal) |
| Order Service   | 3002 | ClusterIP (Internal) |

🔹 Only **Gateway Service** is exposed externally.
🔹 Other services communicate internally via Kubernetes DNS.

---

## 🛠️ Prerequisites

* Ubuntu 20.04+ (EC2 or local)
* Docker installed
* kubectl installed
* Minikube installed

---

## 1️⃣ Install Docker

```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
newgrp docker
```

Verify:

```bash
docker --version
```

---

## 2️⃣ Install kubectl

```bash
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

Verify:

```bash
kubectl version --client
```

---

## 3️⃣ Install Minikube

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

Verify:

```bash
minikube version
```

---

## 4️⃣ Start Minikube

```bash
minikube start --driver=docker
```

Check status:

```bash
minikube status
```

Get Minikube IP:

```bash
minikube ip
```

---

## 5️⃣ Configure Docker to Use Minikube

```bash
eval $(minikube docker-env)
```

This allows Kubernetes to use locally built Docker images.

---

## 6️⃣ Build Docker Images

Navigate to each service directory and build images:

```bash
docker build -t adish786/user-service:latest .
docker build -t adish786/product-service:latest .
docker build -t adish786/order-service:latest .
docker build -t adish786/gateway-service:latest .
```

Verify images:

```bash
docker images
```

---

## 7️⃣ Deploy Kubernetes Resources

Apply Deployments:

```bash
kubectl apply -f deployments/
```

Apply Services:

```bash
kubectl apply -f services/
```

(Optional) Apply Ingress:

```bash
kubectl apply -f ingress/
```

---

## 8️⃣ Verify Kubernetes Resources

Check pods:

```bash
kubectl get pods
```

Check services:

```bash
kubectl get svc
```

Expected:

* All pods in `Running` state
* Gateway service as `NodePort`

---

## 9️⃣ Access the Application

### ✅ Using NodePort (Recommended)

```bash
curl http://<MINIKUBE-IP>:30080/api/users
curl http://<MINIKUBE-IP>:30080/api/products
curl http://<MINIKUBE-IP>:30080/api/orders
```

Example:

```bash
curl http://192.168.49.2:30080/api/products
```

---

### 🔁 Using Port Forward (Debugging Only)

```bash
kubectl port-forward svc/gateway-service 8080:80
```

Then:

```bash
curl http://localhost:8080/api/users
```

⚠️ Port-forward works **only on localhost**, not Minikube IP.

---

## 🔍 Health Checks

```bash
curl http://<MINIKUBE-IP>:30080/health
```

---

## 🧪 Validate Internal Service Communication

```bash
kubectl exec -it <gateway-pod-name> -- sh
```

Inside pod:

```bash
curl http://user-service:3000/users
```

---

## 🐞 Troubleshooting

### Pods not running

```bash
kubectl describe pod <pod-name>
```

### View logs

```bash
kubectl logs <pod-name>
```

### Restart deployment

```bash
kubectl rollout restart deployment gateway-service
```

### Cannot access service

* Ensure correct **NodePort**
* Ensure using **Minikube IP**, not localhost

---

## 🎯 Key Kubernetes Concepts Used

* Deployments
* Services (ClusterIP, NodePort)
* API Gateway Pattern
* Kubernetes DNS
* Health Probes
* Minikube local cluster

---

## ✅ Conclusion

This setup demonstrates a **real-world microservices architecture** using Kubernetes with:

* Secure internal services
* Single exposed gateway
* Scalable deployments
* Production-ready design

🚀 You now have a fully working Kubernetes microservices platform!
