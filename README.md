# Cloud-Native To-Do App on Google Kubernetes Engine

A full-stack to-do application, containerised with Docker and deployed on **Google Kubernetes Engine (GKE)**, with autoscaling and load testing. Built as the term project for **CS 436 Cloud Computing** at Sabancı University (Spring 2025).

## Architecture
```
            Internet
               │
     ┌─────────┴──────────┐
     ▼                    ▼
 LoadBalancer         LoadBalancer
 todo-frontend-svc    todo-backend-svc
     │                    │
 React + Nginx        Node.js / Express API
 (Deployment)         (Deployment, autoscaled with HPA)
                          │
                   mongo-external (Service)
                          │
                 MongoDB on a separate VM
```
- **Frontend:** React app built in a multi-stage Docker image and served by Nginx.
- **Backend:** Express REST API with CRUD endpoints (`/todos`) backed by MongoDB through Mongoose.
- **Database:** MongoDB running on a separate virtual machine, reached from the cluster through a Kubernetes Service.
- **Configuration:** the backend reads its database URL and port from a ConfigMap.

## Scaling and load testing
- A **Horizontal Pod Autoscaler (HPA)** scaled the backend deployment with load.
- The service was load-tested with **Locust** to check how it behaved under concurrent users.

The HPA configuration and Locust scripts were used during the project and are not included in this repository.

## Repository structure
```
backend/    Express API + Dockerfile
frontend/   React app + multi-stage Dockerfile (Nginx)
k8s/        Deployments, Services and ConfigMap
```

## Deploying
```bash
# Build and push the images (replace <registry> with your container registry)
docker build -t <registry>/todo-backend:latest backend/
docker build -t <registry>/todo-frontend:latest \
  --build-arg REACT_APP_API_URL=http://<backend-external-ip>:5001 frontend/
docker push <registry>/todo-backend:latest
docker push <registry>/todo-frontend:latest

# Point the image fields in k8s/*-deployment.yaml at your images, then:
kubectl apply -f k8s/
kubectl get svc   # external IPs of the frontend and backend
```
`backend/.env.example` shows the environment variables the API expects.

## Tech stack
React · Node.js · Express · MongoDB · Docker · Kubernetes (GKE) · Google Cloud · Nginx · Locust

---
**Author:** Sude Kumbasar · CS 436 Cloud Computing group project, Sabancı University
