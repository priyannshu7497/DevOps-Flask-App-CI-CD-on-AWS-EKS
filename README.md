# DevOps-Flask-App-CI-CD-on-AWS-EKS
End-to-end CI/CD implementation for a Python Flask application deployed on AWS EKS using Terraform, Docker, Jenkins, and Kubernetes. The project demonstrates automated infrastructure provisioning, containerization, continuous integration, and continuous deployment following DevOps best practices.



AWS EKS CI/CD Projects Repository

This repository contains multiple cloud-native projects demonstrating deployment of applications on AWS EKS using Terraform, Docker, Jenkins, Kubernetes, and Helm. Each project follows industry-standard best practices for CI/CD, Infrastructure as Code (IaC), containerization, and scalable deployments.

Table of Contents

Project Overview

Folder Structure

Scenario 1: Python Flask App Deployment on EKS

Scenario 2: Blue-Green Deployment for Node.js App

Scenario 3: Multi-Environment Pipeline for Microservices

Scenario 4: React Frontend & Node Backend Deployment

Scenario 5: High Availability App Deployment

Tech Stack

Prerequisites

Setup Instructions

CI/CD Pipeline Overview

Security and Best Practices

License

Project Overview

These projects demonstrate end-to-end CI/CD pipelines for cloud-native applications deployed on AWS EKS. The applications include Python Flask, Node.js, and full-stack React-Node.js apps. Key focus areas:

Infrastructure provisioning using Terraform (VPC, EKS, ECR).

Containerization using Docker.

Continuous Integration & Deployment using Jenkins.

Application deployment with Kubernetes manifests and Helm charts.

Advanced deployment strategies: Blue-Green Deployment, multi-environment pipelines, high availability with auto-scaling.

Folder Structure
├── README.md
├── terraform/
│   ├── eks/
│   ├── vpc/
│   └── ecr/
├── flask-app/
│   ├── app.py
│   ├── requirements.txt
│   └── Dockerfile
├── node-app/
│   ├── app.js
│   ├── package.json
│   └── Dockerfile
├── react-app/
│   ├── package.json
│   └── Dockerfile
├── jenkins/
│   └── Jenkinsfile
├── k8s/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── hpa.yaml
├── helm/
│   ├── blue/
│   └── green/

Scenario 1: Python Flask App Deployment on EKS

Objective: Deploy a Python Flask web service on AWS EKS with automated CI/CD.

Components & Commands:

Terraform (Infrastructure)

# Initialize Terraform
terraform init

# Plan infrastructure
terraform plan

# Apply infrastructure
terraform apply


Docker (Containerization)

# Build Docker image
docker build -t flask-app:latest .

# Tag for ECR
docker tag flask-app:latest <AWS_ACCOUNT_ID>.dkr.ecr.<REGION>.amazonaws.com/flask-app:latest

# Push to ECR
aws ecr get-login-password --region <REGION> | docker login --username AWS --password-stdin <AWS_ACCOUNT_ID>.dkr.ecr.<REGION>.amazonaws.com
docker push <AWS_ACCOUNT_ID>.dkr.ecr.<REGION>.amazonaws.com/flask-app:latest


Kubernetes Deployment

# Apply Deployment and Service
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml

# Check pods
kubectl get pods -n <namespace>


Jenkins Pipeline

pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'docker build -t flask-app:latest ./flask-app'
            }
        }
        stage('Test') {
            steps {
                sh 'pytest ./flask-app/tests'
            }
        }
        stage('Push to ECR') {
            steps {
                sh 'docker push <ECR_REPO>'
            }
        }
        stage('Deploy to EKS') {
            steps {
                sh 'kubectl apply -f k8s/deployment.yaml'
            }
        }
    }
}

Scenario 2: Blue-Green Deployment for Node.js App

Objective: Deploy a Node.js app using Blue-Green Deployment for zero-downtime upgrades.

Components:

Helm charts defining Blue and Green environments.

Jenkins automation for Helm upgrades.

Commands:

# Helm install
helm install blue-app ./helm/blue
helm install green-app ./helm/green

# Upgrade using Jenkins
helm upgrade green-app ./helm/green --set image.tag=latest

# Rollback
helm rollback green-app 1

Scenario 3: Multi-Environment Pipeline for Microservices

Objective: Implement dev, staging, and prod environments with Terraform workspaces.

Commands:

# Create and select workspace
terraform workspace new dev
terraform workspace select dev

# Apply infrastructure per environment
terraform apply -var="env=dev"


Separate Dockerfiles & manifests for each environment.

Jenkins promotion pipeline: dev → staging → prod.

Scenario 4: React Frontend & Node Backend Deployment

Objective: Deploy full-stack app with shared CI/CD.

Components:

Dockerfiles for frontend and backend.

Shared Jenkins pipeline for parallel builds.

Commands:

# Build frontend
docker build -t react-app ./react-app

# Build backend
docker build -t node-app ./node-app

# Deploy both to EKS
kubectl apply -f k8s/react-deployment.yaml
kubectl apply -f k8s/node-deployment.yaml

Scenario 5: High Availability App Deployment

Objective: Deploy auto-scaling app on EKS using HPA and Cluster Autoscaler.

Commands:

# Apply HPA
kubectl apply -f k8s/hpa.yaml

# Enable Cluster Autoscaler
terraform apply -var="enable_autoscaling=true"

# Check scaling
kubectl get hpa
kubectl get nodes

Tech Stack

Infrastructure: AWS (EKS, VPC, ECR, IAM)

IaC: Terraform

Containerization: Docker

Orchestration: Kubernetes, Helm

CI/CD: Jenkins

Applications: Python Flask, Node.js, React.js

Prerequisites

AWS CLI configured with admin access.

Terraform >= 1.5

Docker >= 24

kubectl >= 1.28

Jenkins server with Docker & Kubernetes plugins.

Helm >= 3

CI/CD Pipeline Overview

Build Stage: Docker images for app(s).

Test Stage: Run unit tests.

Push Stage: Push images to ECR.

Deploy Stage: Deploy to EKS using Kubernetes manifests or Helm charts.

Promotion & Rollback: Dev → Staging → Prod, Blue-Green deployment supported.

Security and Best Practices

IAM Roles: Use least privilege for EKS & ECR access.

Secrets Management: Store secrets in AWS Secrets Manager or Kubernetes secrets.

Resource Limits: Set CPU & memory limits for containers.

Auto-scaling: Enable HPA & Cluster Autoscaler for resilience.

Immutable Deployments: Use versioned Docker images.
