# Multi-Service App: Docker + Kubernetes
This project demonstrates how to build and deploy a multi-service application using Docker and Kubernetes. 

It includes: React frontend, Express.js, Backend Services(Auth, Products, Orders) An API gateway for routing, Docker for containerization. Kubernetes for orchestration

By following this guide, you will spin up the entire stack, step-by-step, even if you are a beginner

## Project Structure
```bash
ecommerce-microservices/
│
├── api-gateway/
├── auth-service/
├── products-service/
├── orders-service/
├── frontend/
├── kubernetes-manifests/
└── docker-compose.yaml
```
Each folder contains a microservice with its own Dockerfile.

## Step by Step Guide (Copy/Paste Friendly)

### Step 1: Prepare Project Folders
```bash
# Create project directory
mkdir ecommerce-microservices && cd ecommerce-microservices

# Create services
mkdir frontend api-gateway auth-service products-service orders-service

# Create Kubernetes folder
mkdir kubernetes-manifests
```
