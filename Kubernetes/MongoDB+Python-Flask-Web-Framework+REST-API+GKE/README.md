# MongoDB + Python-Flask-Web-Framework + REST-API + GKE

## Project Overview

This project deploys a MongoDB database along with two REST APIs on Google Kubernetes Engine (GKE). The project includes:
- **Student Server**: Provides an API to fetch student records from a MongoDB database.
- **Bookshelf API**: Manages a collection of books with CRUD operations.

## Introduction

- This project demonstrates the deployment of a MongoDB instance using Persistent Volumes on GKE.
- It includes REST APIs built with Node.js and Python Flask.
- The APIs are exposed using Nginx Ingress to manage inbound traffic.
- The project showcases basic CRUD operations and data retrieval from a MongoDB database.

## Design and Implementation

- **MongoDB Deployment**: Utilizes Persistent Volumes for data storage and deployment on GKE.
- **Student Server**: Node.js application that connects to MongoDB and provides an API to fetch student records.
- **Bookshelf API**: Python Flask application that offers CRUD operations for managing books.
- **Nginx Ingress**: Manages traffic routing to different services based on the URL path.

## Getting Started

### Prerequisites

- Google Cloud SDK
- Kubernetes CLI (kubectl)
- Docker

### Setup Instructions

1. **Create a GKE Cluster**
   ```bash
   gcloud container clusters create kubiaa --num-nodes=1 --machine-type=e2-micro --region=us-west1
   ```

2. **Create a Persistent Volume and Persistent Volume Claim**
   ```bash
   gcloud compute disks create --size=10GiB --zone=us-west1-a mongodb
   kubectl apply -f mongodb-deployment.yaml
   ```

3. **Deploy Applications**
   ```bash
   docker build -t student-server .
   docker tag student-server:latest nhaile/mydb:latest
   docker push nhaile/mydb:latest

   docker build -t nhaile/bookshelf .
   docker push nhaile/bookshelf
   ```

4. **Apply ConfigMaps, Deployments, and Services**
   ```bash
   kubectl apply -f studentserver-config.yaml
   kubectl apply -f bookshelf-configmap.yaml
   kubectl apply -f studentServer_deployment.yaml
   kubectl apply -f bookshelf_deployment.yaml
   kubectl apply -f studentServer_service.yaml
   kubectl apply -f bookshelf_service.yaml
   ```

5. **Set Up Ingress**
   ```bash
   minikube start
   minikube addons enable ingress
   kubectl apply -f ingress.yaml
   ```

### Usage

- **Student Server API**: `http://cs571.project.com/studentserver/api/score?student_id=11111`
- **Bookshelf API**: 
  - List books: `http://cs571.project.com/bookshelf/books`
  - Add a book: `POST /bookshelf/book`
  - Update a book: `PUT /bookshelf/book/{id}`
  - Delete a book: `DELETE /bookshelf/book/{id}`

## Author

Natnael Haile
