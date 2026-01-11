# Cloud-Native E-Commerce Application on Azure (AKS)

## Project Overview

This project demonstrates the design, deployment, and automation of a cloud-native web application using modern cloud and DevOps practices. The goal was to implement the full lifecycle of a cloud-native system, starting from local development and containerization, through infrastructure provisioning using Infrastructure as Code (IaC), and ending with automated CI/CD deployment on Kubernetes.

The application is a Flask-based e-commerce web application backed by a MySQL database. It is containerized using Docker, deployed on Azure Kubernetes Service (AKS), and automatically built and deployed using GitHub Actions.

---

## High-Level Architecture

The overall flow of the system is as follows:

- A developer pushes code changes to GitHub
- GitHub Actions runs an automated CI/CD pipeline
- Docker images are built and pushed to Azure Container Registry (ACR)
- Azure Kubernetes Service (AKS) pulls the image and deploys it
- The application is exposed to users through a public LoadBalancer IP

<img width="447" height="306" alt="image" src="https://github.com/user-attachments/assets/1e8bd88b-cc78-459d-b067-dd9cb1019622" />


---

## Application Architecture

The system consists of two main components.

### Web Application
- Python Flask application
- Handles product browsing, cart management, and user interaction
- Runs behind a Kubernetes LoadBalancer
- Scaled horizontally using multiple replicas

### Database
- MySQL database
- Stores application data
- Runs as a pod inside the Kubernetes cluster
- Credentials are managed using Kubernetes Secrets

---

## Containerization

The Flask application is packaged into a Docker image.

- A Dockerfile defines the base image, dependencies, and and startup command
- Gunicorn is used as the production WSGI server
- The image is lightweight and production-ready

---

## Kubernetes Deployment (AKS)

The application is deployed to Azure Kubernetes Service using Kubernetes manifests.

Kubernetes resources used:
- Deployments for Flask and MySQL
- Services for internal and external networking
- ConfigMaps for non-sensitive configuration
- Secrets for sensitive credentials

The Flask application is exposed externally using a LoadBalancer Service, which assigns a public IP address.  
The MySQL database is exposed internally using a ClusterIP Service.

---

## Infrastructure as Code (Terraform)

Terraform is used to provision all required cloud infrastructure.

Terraform creates:
- Azure Resource Group
- Azure Kubernetes Service (AKS)
- Azure Container Registry (ACR)
- Supporting networking resources

All infrastructure is created using a single Terraform workflow.

---

## CI/CD Pipeline (GitHub Actions)

GitHub Actions is used to automate building and deploying the application.

### Pipeline Trigger
- Any push to the `main` branch triggers the workflow

### Pipeline Steps
1. Checkout source code
2. Authenticate to Azure using a Service Principal
3. Build Docker image
4. Push image to Azure Container Registry
5. Retrieve AKS credentials
6. Restart the Kubernetes deployment

Kubernetes performs a rolling update, ensuring zero downtime during deployment.

---

### Deployment Flow Summary

1. Developer pushes code to GitHub
2. GitHub Actions pipeline is triggered
3. Docker image is built and pushed to ACR
4. AKS pulls the updated image
5. Kubernetes replaces pods using rolling updates
6. 
Application becomes available via public IP

## How to Run the Application

### Create the Infrastructure on Azure

```bash
terraform init
terraform plan
terraform apply
```
### Redeploy When Code Changes

```bash
git add .
git commit -m "New redeploy"
git push origin main
```
### Redeploy Without Code Changes

```bash
git commit --allow-empty -m "New redeploy"
git push origin main
```

