# MERN App on Kubernetes (Production Setup)

This document explains the Kubernetes architecture for deploying a **MERN (MongoDB, Express, React, Node.js)** application in a **production environment**.  

---

## 🧩 Kubernetes Components Used
- **Namespace** → Isolates the MERN app
- **ConfigMap** → Stores non-sensitive environment configs
- **Secrets** → Stores sensitive credentials (Mongo URI, JWT secret, API keys)
- **PersistentVolume & PersistentVolumeClaim** → Storage for MongoDB
- **Deployments** → Define Pods for frontend, backend, and MongoDB
- **Services** → Expose Pods internally or externally
- **Ingress** → Routes external HTTP traffic to frontend/backend
- **HPA** → Auto-scales backend pods
- **NetworkPolicy** → Restricts traffic between pods
- **PodDisruptionBudget (PDB)** → Ensures minimum pods remain available

---

## ⚙️ High-Level Architecture (ASCII)

```text
                         ┌─────────────────────────────┐
                         │         Internet            │
                         └─────────────┬───────────────┘
                                       │
                             Ingress Controller (Nginx)
                                       │
             ┌─────────────────────────┴─────────────────────────┐
             │                                                   │
   ┌─────────▼───────────┐                           ┌───────────▼───────────┐
   │ Frontend Service     │                           │ Backend Service       │
   │ (ClusterIP / LB)     │                           │ (ClusterIP)           │
   └─────────┬───────────┘                           └───────────┬───────────┘
             │                                                   │
   ┌─────────▼───────────┐                           ┌───────────▼───────────┐
   │ Frontend Deployment │                           │ Backend Deployment    │
   │ (React + Nginx Pods)│                           │ (Node/Express Pods)   │
   └─────────────────────┘                           └───────────┬───────────┘
                                                                 │
                                                     ┌───────────▼───────────┐
                                                     │ MongoDB Service       │
                                                     │ (ClusterIP)           │
                                                     └───────────┬───────────┘
                                                                 │
                                                     ┌───────────▼───────────┐
                                                     │ MongoDB Deployment    │
                                                     │ (Pods + PVC + PV)     │
                                                     └───────────────────────┘
````

---

## 🎨 High-Level Architecture (Mermaid Diagram)
```mermaid
flowchart TD
    A[Internet] --> B[Ingress Controller - Nginx]

    B --> C[Frontend Service - ClusterIP/LoadBalancer]
    B --> D[Backend Service - ClusterIP]

    C --> E[Frontend Deployment: React + Nginx Pods]
    D --> F[Backend Deployment: Node.js/Express Pods]

    F --> G[MongoDB Service - ClusterIP]
    G --> H[MongoDB Deployment: Pods + PVC + PV]

---

## 📂 YAML File Structure Example

```
k8s/
│── namespace.yaml
│── configmap.yaml
│── secret.yaml
│── mongo-pv-pvc.yaml
│── frontend-deployment.yaml
│── backend-deployment.yaml
│── mongo-deployment.yaml
│── frontend-service.yaml
│── backend-service.yaml
│── mongo-service.yaml
│── ingress.yaml
│── hpa.yaml
│── networkpolicy.yaml
│── pdb.yaml
```
