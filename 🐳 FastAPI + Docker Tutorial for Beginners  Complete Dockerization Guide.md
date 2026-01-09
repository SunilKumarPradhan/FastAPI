---
title: "🐳 FastAPI + Docker Tutorial for Beginners | Complete Dockerization Guide"
layout: default
nav_order: 11
parent: "Lecture Notes"
description: "Lecture notes: 🐳 FastAPI + Docker Tutorial for Beginners | Complete Dockerization Guide"
last_modified_date: 2026-01-09
source_transcript: "011_FastAPI_+_Docker_Tutorial_for_Beginners_How_to_Dockerize_a_FastAPI_API_Application_CampusX"
generated_by: "NoteyBoy"
---

# 🐳 FastAPI + Docker Tutorial for Beginners | Complete Dockerization Guide

## 📑 Table of Contents

1. [📖 Introduction & Overview](#introduction--overview)
2. [🔄 Quick Recap](#quick-recap)
3. [🎯 Today's Objectives](#todays-objectives)
4. [⚙️ Prerequisites & Setup](#prerequisites--setup)
5. [🐳 Docker Fundamentals](#docker-fundamentals)
6. [📝 Step-by-Step Dockerization Process](#step-by-step-dockerization-process)
7. [🔧 Testing the Dockerized Application](#testing-the-dockerized-application)
8. [⚡ Quick Reference](#quick-reference)
9. [📊 Summary Table](#summary-table)
10. [🎯 Key Takeaways](#key-takeaways)
11. [💡 Pro Tips](#pro-tips)

---

## 📖 Introduction & Overview

This tutorial covers **dockerizing a FastAPI application** - transforming your machine learning prediction API into a portable, deployable Docker container. You'll learn to create Docker images, push them to Docker Hub, and run them anywhere without environment conflicts.

> **Why Dockerization Matters**: Docker solves the "it works on my machine" problem by packaging your application with all its dependencies into a portable container that runs consistently across different environments.

---

## 🔄 Quick Recap

### 🏗️ Previous Work Completed

| Video | Achievement |
|-------|-------------|
| **Video 1** | Built ML model for insurance premium prediction |
| **Video 2** | Created basic FastAPI prediction endpoint (`/predict`) |
| **Video 3** | Enhanced API with industry best practices and improvements |

### 🎯 Current ML Model Details

Our **Insurance Premium Prediction Model** accepts user details and predicts premium category:

```python
# Input Parameters
{
    "age": 31,
    "weight": 91,
    "height": 1.72,
    "income": 10,  # in LPA
    "smoker": true,
    "city": "Gurgaon",
    "occupation": "Retired"
}

# Output: Premium Category (High/Medium/Low)
```

---

## 🎯 Today's Objectives

### 🚀 Two Main Goals

1. **🐳 Dockerize the FastAPI Application**
   - Create a Docker image from our improved API
   - Enable running the API anywhere without setup hassles

2. **☁️ Push to Docker Hub**
   - Upload the image to Docker Hub
   - Make it accessible to team members and testers

### 💡 Why Dockerization?

| Benefit | Description |
|---------|-------------|
| **Portability** | Run on any machine with Docker installed |
| **Consistency** | Same environment across development, testing, and production |
| **Easy Deployment** | Simple deployment to cloud services like AWS |
| **Team Collaboration** | Share exact working environment with teammates |

---

## ⚙️ Prerequisites & Setup

### 📋 Required Software

#### 1. 🐳 Docker Desktop Installation

```bash
# Visit official Docker website
https://www.docker.com/products/docker-desktop/

# Download for your OS:
# - Windows: Docker Desktop for Windows
# - macOS: Docker Desktop for Mac
# - Linux: Docker Engine
```

**Installation Steps:**
1. Download Docker Desktop from official website
2. Run installer with default settings (Next → Next → Next)
3. Restart computer if prompted
4. Verify installation by opening Docker Desktop

#### 2. 🌐 Docker Hub Account

```bash
# Create account at:
https://hub.docker.com/

# Required for:
# - Pushing images to cloud
# - Sharing images with team
# - Public/private repositories
```

### ✅ Verification Checklist

- [ ] Docker Desktop installed and running
- [ ] Docker Hub account created
- [ ] Previous FastAPI project ready
- [ ] Basic Docker knowledge (recommended: watch Docker crash course)

---

## 🐳 Docker Fundamentals

### 🔍 Key Concepts

> **Docker Image**: A blueprint/template containing your application and all its dependencies

> **Docker Container**: A running instance of a Docker image

> **Dockerfile**: Step-by-step instructions for building a Docker image

### 📊 Image vs Container Comparison

| Aspect | Docker Image | Docker Container |
|--------|--------------|------------------|
| **Definition** | Static template/blueprint | Running instance |
| **State** | Immutable | Dynamic/running |
| **Purpose** | Storage and distribution | Execution |
| **Analogy** | Recipe | Cooked dish |

---

## 📝 Step-by-Step Dockerization Process

### 📄 Step 1: Create Dockerfile

Create a file named `Dockerfile` (capital D, no extension) in your project root:

```dockerfile
# Use Python base image with pre-installed Python
FROM python:3.9

# Set working directory inside container
WORKDIR /app

# Copy requirements file first (for better caching)
COPY requirements.txt .

# Install Python dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY . .

# Expose port 8000 for FastAPI
EXPOSE 8000

# Command to run the application
CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "8000"]
```

### 🔧 Dockerfile Explanation

| Instruction | Purpose | Why Important |
|-------------|---------|---------------|
| `FROM python:3.9` | Base OS with Python pre-installed | Provides foundation |
| `WORKDIR /app` | Sets working directory | Organizes file structure |
| `COPY requirements.txt .` | Copy dependencies list first | Enables Docker layer caching |
| `RUN pip install...` | Install Python packages | Sets up environment |
| `COPY . .` | Copy application code | Includes your FastAPI app |
| `EXPOSE 8000` | Document port usage | Informs about network ports |
| `CMD [...]` | Default run command | Starts FastAPI server |

### 💡 Pro Tip: Layer Caching
```dockerfile
# ✅ Good: Copy requirements first
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .

# ❌ Bad: Copy everything first
COPY . .
RUN pip install -r requirements.txt
```

### 🏗️ Step 2: Build Docker Image

```bash
# Build command syntax
docker build -t <username>/<repository-name> .

# Example (replace 'tws24' with your Docker Hub username)
docker build -t tws24/fastapi-insurance-predictor .
```

**Command Breakdown:**
- `docker build`: Build image command
- `-t`: Tag/name the image
- `tws24`: Docker Hub username
- `fastapi-insurance-predictor`: Repository name
- `.`: Build context (current directory)

### ⏱️ Build Process Timeline

```
Step 1/7 : FROM python:3.9
 ---> Pulling base image...
Step 2/7 : WORKDIR /app
 ---> Creating directory...
Step 3/7 : COPY requirements.txt .
 ---> Copying file...
Step 4/7 : RUN pip install...
 ---> Installing packages...
Step 5/7 : COPY . .
 ---> Copying application...
Step 6/7 : EXPOSE 8000
 ---> Documenting port...
Step 7/7 : CMD ["uvicorn"...]
 ---> Setting startup command...
```

### 🚀 Step 3: Verify Image Creation

Check Docker Desktop → Images tab or run:

```bash
# List all images
docker images

# Expected output:
REPOSITORY                          TAG       IMAGE ID       CREATED         SIZE
tws24/fastapi-insurance-predictor   latest    abc123def456   2 minutes ago   1.2GB
```

### 🔐 Step 4: Login to Docker Hub

```bash
# Login command
docker login

# Prompts for:
Username: your-docker-hub-username
Password: your-docker-hub-password

# Success message:
Login Succeeded
```

### ☁️ Step 5: Push Image to Docker Hub

```bash
# Push command
docker push tws24/fastapi-insurance-predictor

# Process:
The push refers to repository [docker.io/tws24/fastapi-insurance-predictor]
layer1: Pushed
layer2: Pushed
layer3: Pushed
latest: digest: sha256:abc123... size: 2841
```

### ✅ Step 6: Verify Upload

1. Visit [Docker Hub](https://hub.docker.com/)
2. Login to your account
3. Check repositories section
4. Your image should appear with "latest" tag

---

## 🔧 Testing the Dockerized Application

### 🧪 Tester Perspective

Now let's test as if we're a different team member receiving the Docker image:

### 🗑️ Step 1: Clean Local Environment

```bash
# Remove local image (simulate fresh machine)
docker rmi tws24/fastapi-insurance-predictor

# Verify removal
docker images
```

### ⬇️ Step 2: Pull Image from Docker Hub

```bash
# Pull command
docker pull tws24/fastapi-insurance-predictor

# Alternative: Run directly (auto-pulls if not found)
docker run -p 8000:8000 tws24/fastapi-insurance-predictor
```

### 🌐 Step 3: Test API Endpoints

#### Home Endpoint
```bash
# Browser: http://localhost:8000
# Expected: Welcome message
```

#### Health Check
```bash
# Browser: http://localhost:8000/health
# Expected: {"status": "healthy"}
```

#### API Documentation
```bash
# Browser: http://localhost:8000/docs
# Expected: Interactive Swagger UI
```

#### Prediction Endpoint
```json
// POST http://localhost:8000/predict
{
    "age": 31,
    "weight": 91,
    "height": 1.72,
    "income": 10,
    "smoker": true,
    "city": "Gurgaon",
    "occupation": "Retired"
}

// Expected Response:
{
    "premium_category": "High",
    "confidence": 0.85
}
```

---

## ⚡ Quick Reference

### 🐳 Essential Docker Commands

```bash
# Build image
docker build -t <image-name> .

# Run container
docker run -p <host-port>:<container-port> <image-name>

# List images
docker images

# List running containers
docker ps

# Stop container
docker stop <container-id>

# Remove image
docker rmi <image-name>

# Login to Docker Hub
docker login

# Push to Docker Hub
docker push <image-name>

# Pull from Docker Hub
docker pull <image-name>
```

### 📁 Project Structure

```
fastapi-project/
├── app.py                 # Main FastAPI application
├── requirements.txt       # Python dependencies
├── Dockerfile            # Docker build instructions
├── model/
│   └── model.pkl         # Trained ML model
└── README.md             # Project documentation
```

### 🔧 Common Dockerfile Instructions

```dockerfile
FROM <base-image>          # Set base image
WORKDIR <directory>        # Set working directory
COPY <src> <dest>         # Copy files
RUN <command>             # Execute command during build
EXPOSE <port>             # Document port usage
CMD ["command", "args"]   # Default run command
ENV <key>=<value>         # Set environment variable
```

---

## 📊 Summary Table

| Phase | Action | Command | Result |
|-------|--------|---------|---------|
| **Setup** | Install Docker Desktop | Download from docker.com | Docker engine ready |
| **Setup** | Create Docker Hub account | Register at hub.docker.com | Cloud repository access |
| **Build** | Create Dockerfile | Text editor | Build instructions ready |
| **Build** | Build image | `docker build -t name .` | Local Docker image |
| **Verify** | Check image | Docker Desktop → Images | Image visible locally |
| **Deploy** | Login to hub | `docker login` | Authenticated access |
| **Deploy** | Push image | `docker push name` | Image on Docker Hub |
| **Test** | Pull image | `docker pull name` | Download to test machine |
| **Test** | Run container | `docker run -p 8000:8000 name` | API running locally |
| **Verify** | Test endpoints | Browser/Postman | API functioning correctly |

---

## 🎯 Key Takeaways

### 🌟 Major Achievements

1. **✅ Successfully Dockerized FastAPI Application**
   - Converted ML prediction API into portable container
   - Eliminated "works on my machine" problems

2. **✅ Implemented Cloud Distribution**
   - Pushed image to Docker Hub
   - Enabled team collaboration and easy deployment

3. **✅ Verified Cross-Platform Compatibility**
   - Tested pull and run process
   - Confirmed API works identically across environments

### 🚀 Business Benefits

| Benefit | Impact |
|---------|--------|
| **Faster Deployment** | Deploy to any cloud platform instantly |
| **Team Efficiency** | No setup time for new team members |
| **Consistent Testing** | Same environment for all testers |
| **Scalability** | Easy to scale horizontally |

### 🔮 Next Steps

- **Upcoming**: Deploy to AWS for public access
- **Goal**: Make API available to entire internet
- **Benefit**: Real-world production deployment experience

---

## 💡 Pro Tips

### 🎯 Docker Best Practices

#### 1. **Optimize Build Time**
```dockerfile
# ✅ Copy requirements first for better caching
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .

# ❌ Avoid copying everything first
COPY . .
RUN pip install -r requirements.txt
```

#### 2. **Use Specific Base Images**
```dockerfile
# ✅ Specific version for reproducibility
FROM python:3.9-slim

# ❌ Avoid latest tag in production
FROM python:latest
```

#### 3. **Minimize Image Size**
```dockerfile
# Use slim variants
FROM python:3.9-slim

# Clean up after installations
RUN pip install --no-cache-dir -r requirements.txt

# Use multi-stage builds for production
```

### 🔧 Troubleshooting Common Issues

#### Port Conflicts
```bash
# If port 8000 is busy
docker run -p 8001:8000 your-image-name

# Check what's using the port
netstat -tulpn | grep 8000
```

#### Permission Issues
```bash
# On Linux/Mac, might need sudo
sudo docker build -t image-name .

# Or add user to docker group
sudo usermod -aG docker $USER
```

#### Build Failures
```bash
# Check Dockerfile syntax
# Ensure requirements.txt exists
# Verify base image availability
```

### 🌐 Sharing Your Docker Image

```bash
# Public repository (free)
docker push username/repository-name

# Private repository (paid)
docker push username/private-repo-name

# Share with team
# Send Docker Hub link: https://hub.docker.com/r/username/repo-name
```

### 📈 Performance Optimization

#### Image Size Reduction
```dockerfile
# Use alpine variants
FROM python:3.9-alpine

# Multi-stage builds
FROM python:3.9 as builder
# ... build steps
FROM python:3.9-slim
COPY --from=builder /app /app
```

#### Startup Time Improvement
```dockerfile
# Pre-compile Python files
RUN python -m compileall .

# Use faster WSGI server for production
CMD ["gunicorn", "app:app", "-w", "4", "-k", "uvicorn.workers.UvicornWorker"]
```

### 🔒 Security Considerations

```dockerfile
# Don't run as root
RUN adduser --disabled-password --gecos '' appuser
USER appuser

# Use specific versions
FROM python:3.9.16-slim

# Scan for vulnerabilities
# docker scan your-image-name
```

---

### 🎉 Congratulations!

You've successfully:
- ✅ Dockerized a FastAPI ML application
- ✅ Pushed it to Docker Hub for distribution