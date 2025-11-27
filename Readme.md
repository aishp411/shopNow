
# CI/CD-Driven Kubernetes Deployment with Helm for Frontend and Backend

This project involves creating Kubernetes deployment files for both frontend and backend services to ensure scalable, reliable application delivery. A Helm chart is developed to simplify configuration and streamline deployments. Additionally, Jenkins Groovy scripts automate the build and deployment pipeline, enabling a consistent and efficient CI/CD workflow using the provided frontend and backend repositories.

## Application:
- **Customer App** (React frontend)  
- **Admin Dashboard** (React admin panel)  
- **Backend API** (Express + MongoDB)
  
---

## 1) Dockerize the application
Create Dockerfile for frontend, backend and admin 
---
## 2) ECR repo 

Create the AWS ECR repo for frontend , backend, admin
<img width="1628" height="437" alt="Screenshot 2025-11-27 203428" src="https://github.com/user-attachments/assets/e555028e-63c4-4caa-a645-0b08addd135a" />
---
## 3) Jenkinsfile 
Build, Tag and Push the images to respective ECR repo using JenkinsFile

Add webhook for poll SCM

<img width="1888" height="901" alt="Screenshot 2025-11-27 203935" src="https://github.com/user-attachments/assets/347e345a-8a50-4a9a-8c01-95daa7937682" />


---
## 4) Helm chart
create the EKS cluster 
<img width="1621" height="664" alt="Screenshot 2025-11-26 202054" src="https://github.com/user-attachments/assets/a546a514-8507-4abe-85af-c597528574eb" />

--- 
Connect to the EKS Cluster and check nodes
```bash
# For EKS cluster
aws eks update-kubeconfig --region <region> --name <your-cluster-name>

# Verify access
kubectl cluster-info
kubectl get nodes
```

<img width="1224" height="1016" alt="Screenshot 2025-11-22 171917" src="https://github.com/user-attachments/assets/ac854a38-0c90-4932-b202-1613bb5e8fd4" />

---
Pre-requisites in the Kubernetes Environment (Has to be done once per Kubernetes Cluster)
```bash
# Install metrics server (required for resource monitoring and HPA)
kubectl apply -f kubernetes/pre-req/metrics-server.yaml

# Install ingress-nginx controller (for external access)
# For EKS, other cloud provider will have different file
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.12.0-beta.0/deploy/static/provider/aws/deploy.yaml
```

Deploy the application
```bash
helm upgrade --install mongo kubernetes/helm/charts/mongo -n shopnow-demo --create-namespace
helm upgrade --install backend kubernetes/helm/charts/backend -n shopnow-demo
helm upgrade --install frontend kubernetes/helm/charts/frontend -n shopnow-demo
helm upgrade --install admin kubernetes/helm/charts/admin -n shopnow-demo
```

## 4) Check the application


* **Customer App** → http://loadbalancer_ip_or_dns/your_name
* **Admin Dashboard** → http://loadbalancer_ip_or_dns/your_name-admin
<img width="1919" height="965" alt="Screenshot 2025-11-22 171306" src="https://github.com/user-attachments/assets/b1bbf4d3-dd05-414e-a135-af039c6767a1" />

---




<img width="1911" height="966" alt="Screenshot 2025-11-22 171321" src="https://github.com/user-attachments/assets/f9126b4f-5c26-4fdf-83e4-3c77278475f6" />

---



<img width="1904" height="969" alt="Screenshot 2025-11-22 171415" src="https://github.com/user-attachments/assets/4fba8c0f-7001-405a-99c9-203e8da5c4af" />

---

<img width="1919" height="974" alt="Screenshot 2025-11-22 171522" src="https://github.com/user-attachments/assets/b08db5ed-074d-4ef0-9838-a04749d3de0c" />
