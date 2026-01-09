---
title: "🚀 How to Deploy a FastAPI API on AWS - Complete Deployment Guide"
layout: default
nav_order: 12
parent: "Lecture Notes"
description: "Lecture notes: 🚀 How to Deploy a FastAPI API on AWS - Complete Deployment Guide"
last_modified_date: 2026-01-09
source_transcript: "012_How_to_Deploy_a_FastAPI_API_on_AWS_Video_10_CampusX"
generated_by: "NoteyBoy"
---

# 🚀 How to Deploy a FastAPI API on AWS - Complete Deployment Guide

## 📑 Table of Contents

1. [📖 Introduction & Overview](#introduction--overview)
2. [🔄 Project Recap](#project-recap)
3. [☁️ AWS EC2 Deployment Process](#aws-ec2-deployment-process)
4. [🔧 Setting Up EC2 Instance](#setting-up-ec2-instance)
5. [🐳 Docker Installation & Configuration](#docker-installation--configuration)
6. [🌐 Network Security Configuration](#network-security-configuration)
7. [🎨 Frontend Integration](#frontend-integration)
8. [⚡ Quick Reference](#quick-reference)
9. [📊 Summary Table](#summary-table)
10. [🎯 Key Takeaways](#key-takeaways)
11. [💡 Pro Tips](#pro-tips)

---

## 📖 Introduction & Overview

This tutorial covers the **final step** in building a complete ML API deployment pipeline - deploying a **Dockerized FastAPI application** to **AWS EC2**. This is the 10th and final video in the API playlist series.

### 🎯 What You'll Learn
- Deploy FastAPI Docker images to AWS EC2
- Configure security groups for public access
- Set up a complete production-ready API
- Create a frontend that communicates with deployed API

### 📋 Prerequisites
- Basic cloud computing fundamentals
- AWS account with billing information
- Understanding of Docker basics
- FastAPI knowledge from previous videos

> **Important**: This is a **beginner-friendly deployment** using EC2. For production-scale applications, consider more advanced deployment options like ECS, EKS, or Lambda.

---

## 🔄 Project Recap

### 🏗️ What We've Built So Far

| Step | Description | Technology |
|------|-------------|------------|
| **1** | ML Model Creation | Insurance Premium Prediction Model |
| **2** | API Development | FastAPI with `/predict` endpoint |
| **3** | Code Improvement | Industry best practices implementation |
| **4** | Containerization | Docker image creation and Docker Hub push |
| **5** | Deployment | AWS EC2 deployment (today's focus) |

### 🔗 API Functionality
- **Endpoint**: `/predict`
- **Input**: User details (age, location, etc.)
- **Process**: Communicates with ML model
- **Output**: Insurance premium prediction
- **Documentation**: Auto-generated with FastAPI docs

### 🐳 Current State
- Docker image available on Docker Hub
- Accessible to anyone for local deployment
- Ready for cloud deployment

---

## ☁️ AWS EC2 Deployment Process

### 🤔 Why EC2?

> **EC2 (Elastic Compute Cloud)** provides rental computers in the cloud that you can scale up or down based on your needs.

**Benefits:**
- **Scalability**: Add/remove instances as needed
- **Cost-effective**: Pay only for what you use
- **Flexibility**: Choose hardware specifications
- **Reliability**: AWS infrastructure backing

### 🎯 Deployment Overview

```
Docker Hub Image → EC2 Instance → Public Access
     ↓                ↓              ↓
  Pull Image    →  Run Container  →  Configure Security
```

---

## 🔧 Setting Up EC2 Instance

### 1️⃣ Create AWS Account

1. Go to `aws.amazon.com`
2. Create account (requires credit/debit card for verification)
3. No charges for free tier usage
4. Sign in to AWS Console

### 2️⃣ Launch EC2 Instance

#### Navigate to EC2
```
AWS Console → Search "EC2" → EC2 Dashboard → Instances
```

#### Instance Configuration

| Setting | Value | Reason |
|---------|-------|---------|
| **Name** | `my-fastapi-server` | Easy identification |
| **OS** | Ubuntu | Linux-based, Docker-friendly |
| **Instance Type** | `t2.micro` | Free tier eligible |
| **Memory** | 1 GB | Sufficient for basic API |
| **Key Pair** | Create new or use existing | SSH access (required by AWS) |

#### 🔐 Key Pair Setup
- **Purpose**: Remote login to EC2 instance
- **File**: Downloads as `.pem` file
- **Note**: Not needed for our deployment method, but AWS requires it

#### 🌐 Network Settings
```
✅ Allow SSH traffic from: Anywhere
✅ Default VPC settings
✅ 8GB storage (default)
```

#### 🚀 Launch Instance
Click **"Launch Instance"** → Instance will be created and start running

### 3️⃣ Verify Instance Creation

```
EC2 Dashboard → Instances → Check "Running" status
```

---

## 🐳 Docker Installation & Configuration

### 🔌 Connect to EC2 Instance

**Method 1: AWS Console (Recommended)**
```
Instance Details → Connect → EC2 Instance Connect
```

**Method 2: SSH with PuTTY**
- Use downloaded `.pem` file
- More complex setup

### 📦 Install Required Software

#### Step 1: Update System Packages
```bash
sudo apt update
```

#### Step 2: Install Docker
```bash
sudo apt install docker.io -y
```

#### Step 3: Start Docker Service
```bash
sudo systemctl start docker
```

#### Step 4: Enable Docker Auto-start
```bash
sudo systemctl enable docker
```

> **Why Enable Docker?** If EC2 instance restarts, Docker will automatically start without manual intervention.

#### Step 5: Grant Docker Permissions
```bash
sudo usermod -aG docker $USER
```

> **Important**: After this command, you must **exit** the connection and **reconnect** for permissions to take effect.

### 🔄 Reconnect and Verify

1. **Exit current connection**: `exit`
2. **Close connection window**
3. **Reconnect** via EC2 Console
4. **Verify Docker**: `sudo docker --version`

### 📥 Pull and Run Docker Image

#### Pull Image from Docker Hub
```bash
sudo docker pull niteshkumarsaini/insurance-premium-prediction:latest
```

#### Run Container
```bash
sudo docker run -d -p 8000:8000 niteshkumarsaini/insurance-premium-prediction:latest
```

**Command Breakdown:**
- `-d`: Run in detached mode (background)
- `-p 8000:8000`: Map host port 8000 to container port 8000
- Image name from Docker Hub

### ✅ Verify Deployment
```bash
sudo docker ps
```
Should show running container with Uvicorn server.

---

## 🌐 Network Security Configuration

### 🚫 Common Access Issues

When you try to access your API using the public IP:
```
http://YOUR-PUBLIC-IP:8000
```

**Problems you'll encounter:**
1. **HTTPS not available** (no SSL certificate)
2. **Connection refused** (port 8000 blocked)

### 🔓 Configure Security Groups

#### Navigate to Security Groups
```
EC2 Dashboard → Security → Security Groups → Select your instance's group
```

#### Add Inbound Rule

| Setting | Value | Description |
|---------|-------|-------------|
| **Type** | Custom TCP | Allow custom port |
| **Port** | 8000 | Our API port |
| **Source** | 0.0.0.0/0 | Allow from anywhere |
| **Description** | FastAPI access | Optional documentation |

#### Save Rules
Click **"Save rules"** → Changes apply immediately

### 🌍 Test Public Access

#### Get Public IP
```
EC2 Instance Details → Public IPv4 address → Copy
```

#### Access API
```
http://YOUR-PUBLIC-IP:8000/          # Home endpoint
http://YOUR-PUBLIC-IP:8000/health    # Health check
http://YOUR-PUBLIC-IP:8000/docs      # API documentation
```

#### Test Prediction Endpoint
```
http://YOUR-PUBLIC-IP:8000/docs → Try it out → Execute
```

**Sample Input:**
```json
{
  "age": 25,
  "bmi": 22.5,
  "children": 1,
  "region": "northeast"
}
```

---

## 🎨 Frontend Integration

### 🖥️ Streamlit Frontend Setup

The frontend code connects to our deployed API instead of localhost.

#### Update API URL
```python
# Before (local development)
API_URL = "http://localhost:8000"

# After (production deployment)
API_URL = "http://YOUR-PUBLIC-IP:8000"
```

#### Frontend Features
- **User-friendly form** for input data
- **Real-time predictions** from deployed API
- **Error handling** for API communication
- **Responsive design** for better UX

#### Code Structure
```python
import streamlit as st
import requests

# API configuration
API_URL = "http://YOUR-PUBLIC-IP:8000"

# Form inputs
age = st.number_input("Age")
bmi = st.number_input("BMI")
children = st.selectbox("Children", [0, 1, 2, 3, 4, 5])
region = st.selectbox("Region", ["northeast", "northwest", "southeast", "southwest"])

# Prediction
if st.button("Predict"):
    response = requests.post(f"{API_URL}/predict", json={
        "age": age,
        "bmi": bmi,
        "children": children,
        "region": region
    })
    result = response.json()
    st.write(f"Predicted Premium: ${result['premium']}")
```

### 🔗 End-to-End Flow

```
User Input → Streamlit Frontend → AWS API → ML Model → Prediction → User
```

---

## ⚡ Quick Reference

### 🐳 Essential Docker Commands
```bash
# Update system
sudo apt update

# Install Docker
sudo apt install docker.io -y

# Start Docker
sudo systemctl start docker

# Enable auto-start
sudo systemctl enable docker

# Add user to docker group
sudo usermod -aG docker $USER

# Pull image
sudo docker pull niteshkumarsaini/insurance-premium-prediction:latest

# Run container
sudo docker run -d -p 8000:8000 niteshkumarsaini/insurance-premium-prediction:latest

# Check running containers
sudo docker ps
```

### 🔧 AWS Configuration Steps
```
1. Create AWS Account
2. Launch EC2 Instance (t2.micro, Ubuntu)
3. Connect to Instance
4. Install & Configure Docker
5. Pull & Run Docker Image
6. Configure Security Groups (Port 8000)
7. Test Public Access
```

### 🌐 Security Group Rule
```
Type: Custom TCP
Port: 8000
Source: 0.0.0.0/0 (Anywhere)
```

### 📱 API Endpoints
```
GET  /          # Home page
GET  /health     # Health check
POST /predict    # ML prediction
GET  /docs       # API documentation
```

---

## 📊 Summary Table

| Component | Technology | Purpose | Status |
|-----------|------------|---------|---------|
| **ML Model** | Scikit-learn | Insurance premium prediction | ✅ Complete |
| **API Backend** | FastAPI | Serve ML model via REST API | ✅ Complete |
| **Containerization** | Docker | Package application | ✅ Complete |
| **Image Registry** | Docker Hub | Store and distribute image | ✅ Complete |
| **Cloud Infrastructure** | AWS EC2 | Host application | ✅ Complete |
| **Security** | Security Groups | Control network access | ✅ Complete |
| **Frontend** | Streamlit | User interface | ✅ Complete |

### 🔄 Deployment Pipeline

| Stage | Input | Process | Output |
|-------|-------|---------|---------|
| **Development** | Python code | FastAPI development | Local API |
| **Containerization** | FastAPI app | Docker build | Docker image |
| **Registry** | Docker image | Docker push | Public image |
| **Infrastructure** | AWS account | EC2 setup | Cloud server |
| **Deployment** | Docker image | Pull & run | Running API |
| **Security** | EC2 instance | Security groups | Public access |
| **Integration** | Frontend code | API connection | Complete app |

---

## 🎯 Key Takeaways

### ✅ What You've Accomplished

1. **Complete ML Pipeline**: From model training to production deployment
2. **Industry Best Practices**: Docker containerization, cloud deployment
3. **Scalable Architecture**: Ready for production scaling
4. **Security Configuration**: Proper network access controls
5. **End-to-End Integration**: Frontend connected to deployed backend

### 🚀 Production Readiness Checklist

- ✅ **Containerized Application**: Docker image created
- ✅ **Cloud Deployment**: Running on AWS EC2
- ✅ **Public Access**: Accessible via internet
- ✅ **API Documentation**: Auto-generated docs available
- ✅ **Frontend Integration**: User-friendly interface
- ⚠️ **SSL Certificate**: Not configured (HTTP only)
- ⚠️ **Domain Name**: Using IP address
- ⚠️ **Load Balancing**: Single instance deployment
- ⚠️ **Monitoring**: Basic setup only

### 🎓 Skills Developed

| Skill Category | Technologies Learned |
|----------------|---------------------|
| **API Development** | FastAPI, Pydantic, Uvicorn |
| **Containerization** | Docker, Docker Hub |
| **Cloud Computing** | AWS EC2, Security Groups |
| **DevOps** | Deployment pipelines, Infrastructure |
| **Frontend Development** | Streamlit, API integration |

---

## 💡 Pro Tips

### 🔒 Security Best Practices

> **Never expose unnecessary ports**: Only open port 8000 for your API, keep SSH (22) restricted to your IP if possible.

### 💰 Cost Optimization

> **Use t2.micro for learning**: Stays within free tier limits. Monitor usage to avoid unexpected charges.

### 🔄 Auto-restart Configuration

```bash
# Enable Docker auto-start on system reboot
sudo systemctl enable docker

# Run container with restart policy
sudo docker run -d --restart unless-stopped -p 8000:8000 your-image
```

### 📊 Monitoring Your Application

```bash
# Check container status
sudo docker ps

# View container logs
sudo docker logs CONTAINER_ID

# Monitor system resources
htop
```

### 🚀 Scaling Considerations

**For Production Deployment, Consider:**
- **Load Balancers**: Distribute traffic across multiple instances
- **Auto Scaling Groups**: Automatically add/remove instances
- **Container Orchestration**: ECS, EKS for better container management
- **Database**: External database instead of in-memory storage
- **CDN**: CloudFront for static content delivery
- **SSL/TLS**: HTTPS certificates for secure communication

### 🔧 Troubleshooting Common Issues

| Problem | Cause | Solution |
|---------|-------|----------|
| **Connection Refused** | Port 8000 not open | Configure security group |
| **Docker Permission Denied** | User not in docker group | Run `sudo usermod -aG docker $USER` and reconnect |
| **Image Pull Failed** | Network/permission issues | Check internet connectivity and Docker permissions |
| **API Not Responding** | Container not running | Check with `sudo docker ps` and restart if needed |
| **HTTPS Not Working** | No SSL certificate | Use HTTP or configure SSL certificate |

### 📈 Next Steps for Advanced Deployment

1. **Domain Configuration**: Purchase domain and configure DNS
2. **SSL Certificate**: Use Let's Encrypt or AWS Certificate Manager
3. **Load Balancing**: AWS Application Load Balancer
4. **Database Integration**: RDS for persistent data storage
5. **Monitoring**: CloudWatch for application monitoring
6. **CI/CD Pipeline**: Automated deployment with GitHub Actions
7. **Container Orchestration**: Migrate to ECS or EKS

### 🎯 Learning Path Continuation

> **Interested in Advanced FastAPI?** The instructor mentions a comprehensive paid course in development covering deeper FastAPI concepts, advanced deployment strategies, and production-ready patterns.

**Course Topics (Upcoming):**
- Advanced FastAPI features
- Database integration patterns
- Authentication and authorization
- Testing strategies
- Performance optimization
- Production deployment patterns

---

**🎉 Congratulations!** You've successfully completed the entire API development and deployment pipeline - from ML model creation to production deployment on AWS! This foundation prepares you for building and deploying real-