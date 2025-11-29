# CRUD DD Task – MEAN Stack Application with Docker, Nginx, and CI/CD

## Overview

This repository contains a MEAN-style CRUD application built using **MongoDB, Express, AngularJS, and Node.js**, deployed using **Docker**, and automated with a **GitHub Actions CI/CD pipeline**.

The complete development, containerization, and deployment workflow was executed directly on an **Ubuntu EC2 server**. Docker images were built and pushed to **Docker Hub**, the source code was then pushed to **GitHub**, and the **CI/CD pipeline deployed the application using Docker Compose** on the same server.

The application is exposed to the internet through an **Nginx reverse proxy on port 80**.

---

## Technology Stack

- Frontend: AngularJS (1.x)
- Backend: Node.js with Express
- Database: MongoDB
- Reverse Proxy: Nginx
- Containerization: Docker
- Orchestration: Docker Compose
- CI/CD: GitHub Actions
- Hosting Platform: AWS EC2 (Ubuntu)

---

## Project Structure

```
crud-dd-task-mean-app/
├─ backend/
│  ├─ src/
│  │  ├─ config/db.js
│  │  ├─ models/Item.js
│  │  └─ routes/items.js
│  ├─ server.js
│  ├─ package.json
│  ├─ Dockerfile
│  └─ .env.example
│
├─ frontend/
│  ├─ index.html
│  ├─ app.js
│  ├─ styles.css
│  └─ Dockerfile
│
├─ nginx/
│  └─ default.conf
│
├─ .github/
│  └─ workflows/docker-cicd.yml
│
├─ docker-compose.yml
└─ README.md

```

---

## Application Features

### Backend (Node.js + Express + MongoDB)

- RESTful CRUD API for managing items
- API Endpoints:
    - `GET /api/items` – Fetch all items
    - `POST /api/items` – Create a new item
    - `PUT /api/items/:id` – Update an item
    - `DELETE /api/items/:id` – Delete an item
- MongoDB integration using Mongoose
- CORS enabled for frontend communication

### Frontend (AngularJS)

- Single-page application
- Features:
    - View items
    - Add new items
    - Edit items
    - Delete items
- Communicates with backend through `/api` endpoints via Nginx
- Served as static content inside a Docker container

---

## Nginx Reverse Proxy

Nginx is used as a reverse proxy and traffic router.

Routing behavior:

- `http://<server-ip>/` routes to the frontend container
- `http://<server-ip>/api/` routes to the backend container

All routing rules are defined in:

```
nginx/default.conf

```

---

## Docker and Image Management

### Backend Image

- Built from `backend/Dockerfile`
- Uses Node.js runtime
- Runs the Express server

### Frontend Image

- Built from `frontend/Dockerfile`
- Uses a lightweight Node.js base image
- Serves static AngularJS files using an HTTP server

### MongoDB Image

- Uses the official `mongo` image
- Database name: `cruddb`

All images were built on the EC2 server and pushed to Docker Hub before CI/CD deployment.

---

## Exact Workflow Followed in This Project

This section documents the **exact order of execution used in this project**.

### 1. Application Development on EC2

- All backend, frontend, Nginx, Dockerfile, and Docker Compose files were created directly on the Ubuntu EC2 server.
- No cloning was performed initially.
- The entire codebase was written and tested locally on EC2.

---

### 2. Docker Image Build and Push to Docker Hub

Images were built on the EC2 instance and pushed to Docker Hub.

Backend image:

```
docker build -t <dockerhub-username>/crud-backend:latest ./backend
docker push <dockerhub-username>/crud-backend:latest

```

Frontend image:

```
docker build -t <dockerhub-username>/crud-frontend:latest ./frontend
docker push <dockerhub-username>/crud-frontend:latest

```

At this stage, both images were available in Docker Hub.

---

### 3. Git Repository Initialization and Push to GitHub

Only after images were successfully pushed to Docker Hub, Git version control was initialized.

```
git init
git add .
git commit -m "Initial project commit"
git remote add origin https://github.com/<your-username>/crud-dd-task-mean-app.git
git branch -M main
git push -u origin main

```

This was the **first time the project was pushed to GitHub**.

---

### 4. CI/CD Pipeline Configuration

The GitHub Actions pipeline is defined in:

```
.github/workflows/docker-cicd.yml

```

Secrets configured in GitHub:

- `DOCKERHUB_USERNAME`
- `DOCKERHUB_TOKEN`
- `SERVER_HOST`
- `SERVER_USER`
- `SERVER_SSH_KEY`
- `SERVER_APP_PATH`

The pipeline performs the following operations:

- Logs into Docker Hub
- SSHs into the EC2 server
- Pulls the latest images
- Runs Docker Compose to deploy the application

---

### 5. CI/CD-Based Deployment (Docker Compose)

Deployment was performed **only through the CI/CD pipeline** using:

```
docker compose pull
docker compose up -d --remove-orphans

```

This means:

- The images were not started manually at first.
- The application stack was started automatically through GitHub Actions after the push.

---

### 6. Verification and Testing

Verification steps performed:

- Container status:

```
docker ps

```

- Backend API health:

```
curl http://localhost:5000/api/items

```

- Frontend access:

```
http://<EC2-public-ip>/

```

- CRUD operations were tested successfully from the browser UI.
- Data persistence was verified in MongoDB.

**Screenshots included:**

- **CI/CD configuration** – screenshot of `.github/workflows` in GitHub UI.
- **CI/CD run** – screenshot of a successful Actions run.
- **Docker images build & push** – screenshot of terminal or Docker Hub UI.
- **App working UI** – browser screenshot showing the CRUD interface.
---

## Key Configuration Files

- `docker-compose.yml` – Defines all services and network
- `nginx/default.conf` – Reverse proxy routing rules
- `backend/Dockerfile` – Backend container build
- `frontend/Dockerfile` – Frontend container build
- `.github/workflows/docker-cicd.yml` – CI/CD pipeline

---

## Deployment Verification Checklist

- Docker images available in Docker Hub
- GitHub repository contains full source code
- GitHub Actions workflow ran successfully
- All containers running on EC2
- Application accessible via public IP
- CRUD functionality fully operational

---

## Conclusion

This project demonstrates a complete real-world DevOps workflow involving:

- Manual application development on a cloud server
- Docker image creation and registry management
- Source code versioning using GitHub
- Automated CI/CD deployment using GitHub Actions
- Production-grade reverse proxy using Nginx


---


