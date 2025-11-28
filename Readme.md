
# CI/CD-Driven Kubernetes Deployment with Helm for Frontend and Backend

This project involves creating Kubernetes deployment files for both frontend, backend and admin services to ensure scalable, reliable application delivery. A Helm chart is developed to simplify configuration and streamline deployments. Additionally, Jenkins Groovy scripts automate the build and deployment pipeline, enabling a consistent and efficient CI/CD workflow using the provided repositories.

## Application:
- **Customer App** (React frontend)  
- **Admin Dashboard** (React admin panel)  
- **Backend API** (Express + MongoDB)
  
---

## 1) Dockerize the application


Create separate Dockerfiles for:

Frontend (React)
- Multi-stage build using Node → NGINX
- Build React code in stage 1
- Serve static files from NGINX

Backend (Node.js/Express)
- Install dependencies
- Copy source code
- Expose API port (usually 5000)
  
Admin Panel (React)
- Same multi-stage approach as frontend


---
## 2) ECR repo 
Create three ECR repositories:

- `shopnow-name/frontend`
- `shopnow-name/backend`
- `shopnow-name/admin`

<img width="1628" height="437" alt="Screenshot 2025-11-27 203428" src="https://github.com/user-attachments/assets/e555028e-63c4-4caa-a645-0b08addd135a" />
---

## 3) Jenkinsfile 
Build, Tag and Push the images to respective ECR repo using JenkinsFile

Add webhook
GitHub → Jenkins webhook ensures pipeline triggers automatically on commits.

<img width="1888" height="901" alt="Screenshot 2025-11-27 203935" src="https://github.com/user-attachments/assets/347e345a-8a50-4a9a-8c01-95daa7937682" />


---
## 4) AWS EKS Cluster Setup
- create the EKS cluster 
<img width="1621" height="664" alt="Screenshot 2025-11-26 202054" src="https://github.com/user-attachments/assets/a546a514-8507-4abe-85af-c597528574eb" />

--- 
- Connect to the EKS Cluster and check nodes
```bash
# For EKS cluster
aws eks update-kubeconfig --region <region> --name <your-cluster-name>

# Verify access
kubectl cluster-info
kubectl get nodes
```

---
### Kubernetes Environment Prerequisites
These steps are cluster-wide (done only once):

## Install Metrics Server

Required for:
- Horizontal Pod Autoscaling (HPA)
- Resource usage monitoring

`kubectl apply -f kubernetes/pre-req/metrics-server.yaml`

## Install Ingress-NGINX Controller
Provides public access to services via LoadBalancer.
For AWS:

`kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.12.0-beta.0/deploy/static/provider/aws/deploy.yaml
`
### 5) Helm Chart (Deployment Layer)
Once the cluster & prerequisites are ready, deploy all components:

- Deploy MongoDB

```bash
helm upgrade --install mongo kubernetes/helm/charts/mongo -n shopnow-demo --create-namespace
 ```
- Deploy Backend
```bash
 helm upgrade --install backend kubernetes/helm/charts/backend -n shopnow-demo 
```
- Deploy Frontend
```bash 
helm upgrade --install frontend kubernetes/helm/charts/frontend -n shopnow-demo
```
- Deploy Admin Dashboard
```bash
helm upgrade --install admin kubernetes/helm/charts/admin -n shopnow-demo
```
## 6) Verify Deployment
Check Resources
```bash
kubectl get pods -n shopnow-demo
kubectl get svc -n shopnow-demo
kubectl get ingress -n shopnow-demo

```
<img width="1224" height="1016" alt="Screenshot 2025-11-22 171917" src="https://github.com/user-attachments/assets/ac854a38-0c90-4932-b202-1613bb5e8fd4" />

## 7) Access the Applications
Use the LoadBalancer DNS from:

`kubectl get ingress -n shopnow-demo`


* **Customer App** → http://loadbalancer_ip_or_dns/your_name
* **Admin Dashboard** → http://loadbalancer_ip_or_dns/your_name-admin
<img width="1919" height="965" alt="Screenshot 2025-11-22 171306" src="https://github.com/user-attachments/assets/b1bbf4d3-dd05-414e-a135-af039c6767a1" />

---




<img width="1911" height="966" alt="Screenshot 2025-11-22 171321" src="https://github.com/user-attachments/assets/f9126b4f-5c26-4fdf-83e4-3c77278475f6" />

---



<img width="1904" height="969" alt="Screenshot 2025-11-22 171415" src="https://github.com/user-attachments/assets/4fba8c0f-7001-405a-99c9-203e8da5c4af" />


<img width="1919" height="974" alt="Screenshot 2025-11-22 171522" src="https://github.com/user-attachments/assets/b08db5ed-074d-4ef0-9838-a04749d3de0c" />

---
## 8) End-to-End Deployment Flow Summary
Developer pushes code → GitHub

⬇

GitHub triggers Jenkins via Webhook

⬇

Jenkins builds containers & pushes to ECR

⬇

Jenkins deploys to EKS using Helm

⬇

Kubernetes pulls images from ECR

⬇

Pods run inside EKS

⬇

Ingress exposes services to internet

⬇

User accesses frontend & admin UI

