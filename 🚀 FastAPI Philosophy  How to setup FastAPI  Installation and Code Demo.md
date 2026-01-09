---
title: "🚀 FastAPI Philosophy | How to setup FastAPI | Installation and Code Demo"
layout: default
nav_order: 2
parent: "Lecture Notes"
description: "Lecture notes: 🚀 FastAPI Philosophy | How to setup FastAPI | Installation and Code Demo"
last_modified_date: 2026-01-09
source_transcript: "002_FastAPI_Philosophy_How_to_setup_FastAPI_Installation_and_Code_Demo_Video_2_CampusX"
generated_by: "NoteyBoy"
---

# 🚀 FastAPI Philosophy | How to setup FastAPI | Installation and Code Demo

## 📑 Table of Contents

1. [📖 Introduction/Overview](#-introductionoverview)
2. [🔍 What is FastAPI?](#-what-is-fastapi)
3. [🏗️ FastAPI Architecture](#️-fastapi-architecture)
4. [💭 Core Philosophy](#-core-philosophy)
5. [⚡ Fast to Run - Performance](#-fast-to-run---performance)
6. [🚀 Fast to Code - Development Speed](#-fast-to-code---development-speed)
7. [🛠️ Installation and Setup](#️-installation-and-setup)
8. [💻 First API Demo](#-first-api-demo)
9. [📊 Summary Table](#-summary-table)
10. [🎯 Key Takeaways](#-key-takeaways)

---

## 📖 Introduction/Overview

This lecture covers **FastAPI**, a modern, high-performance web framework for building APIs with Python. We'll explore why FastAPI is essential for data science, AI, and ML professionals, understand its core philosophy, and build our first API.

> **Why FastAPI matters**: If you're studying data science, AI, or machine learning, FastAPI is crucial for deploying your models and creating production-ready applications.

---

## 🔍 What is FastAPI?

### 📝 Definition

> **FastAPI is a modern, high-performance web framework for building APIs with Python.**

### 🏗️ Built on Two Powerful Libraries

FastAPI is internally built on top of two famous Python libraries:

| Library | Purpose | Role in FastAPI |
|---------|---------|-----------------|
| **Starlette** | ASGI framework | Manages HTTP request/response handling |
| **Pydantic** | Data validation | Validates incoming data format and types |

#### 🌟 Starlette's Role
- Handles how your API receives requests and sends back responses
- Manages the HTTP communication layer
- Provides asynchronous capabilities

#### 🔍 Pydantic's Role
- Data validation library that checks if incoming data is in correct format
- Enables type checking in Python (which doesn't have it by default)
- Essential for API development where you need to validate client inputs

### 💡 Real-World Example
Imagine you're building a train booking API that takes:
- Two station names (must be strings)
- A date (must be valid date format)
- Returns available trains

Without Pydantic, you'd manually check if station names are strings, if they exist in your database, if the date is valid, etc. With Pydantic, this validation happens automatically behind the scenes.

---

## 🏗️ FastAPI Architecture

### 📊 API Components Overview

Every API has two essential components:

| Component | Description | Example |
|-----------|-------------|---------|
| **API Code** | Business logic implementation | ML model prediction logic |
| **Web Server** | Listens for HTTP requests on ports | Handles incoming client requests |

### 🔄 Information Flow Process

```
Client Request → HTTP Request → Web Server → SGI → API Code → ML Model
                                                                    ↓
Client Response ← HTTP Response ← Web Server ← SGI ← API Response ← Prediction
```

### 🔧 Server Gateway Interface (SGI)

> **SGI**: A translator between web servers and Python applications, converting HTTP requests to Python-understandable format and vice versa.

**Why needed?**: HTTP requests can't be directly understood by Python code, so SGI acts as a bridge for two-way communication.

---

## 💭 Core Philosophy

FastAPI was created with **two primary objectives**:

### 🎯 The Two Core Philosophies

| Philosophy | Meaning | Benefit |
|------------|---------|---------|
| **Fast to Run** | APIs built with FastAPI are high-performance | Handle concurrent users, low latency |
| **Fast to Code** | Development process is quick and efficient | Less boilerplate code, more productivity |

> **Why "Fast" in the name?**: The name reflects both aspects - fast execution and fast development.

---

## ⚡ Fast to Run - Performance

### 🔄 Flask vs FastAPI Comparison

#### 🐌 Flask Architecture (Traditional)

| Component | Technology | Nature | Limitation |
|-----------|------------|--------|------------|
| **SGI Protocol** | WSGI (Web Server Gateway Interface) | Synchronous | One request at a time |
| **Implementation Library** | Werkzeug | Blocking | Resources get blocked during processing |
| **Web Server** | Gunicorn | Synchronous | Performance issues with concurrent requests |
| **API Code** | Standard Python | Synchronous | Sequential processing only |

#### ⚡ FastAPI Architecture (Modern)

| Component | Technology | Nature | Advantage |
|-----------|------------|--------|-----------|
| **SGI Protocol** | ASGI (Asynchronous Server Gateway Interface) | Asynchronous | Multiple concurrent requests |
| **Implementation Library** | Starlette | Non-blocking | Efficient resource utilization |
| **Web Server** | Uvicorn | Asynchronous | High performance, low latency |
| **API Code** | Python with async/await | Asynchronous | Parallel processing support |

### 🍽️ Restaurant Analogy

#### 🐌 Flask = Traditional Waiter
- Takes order from customer
- Goes to kitchen, places order
- **Waits in kitchen** until food is ready
- Serves food to customer
- Only then takes next customer's order

#### ⚡ FastAPI = Smart Waiter
- Takes order from customer
- Goes to kitchen, places order
- **Doesn't wait** - goes to take another customer's order
- When first order is ready, serves it
- Handles multiple customers simultaneously

### 🔧 Async/Await Feature

```python
# Traditional synchronous code
def predict():
    result = ml_model.predict(data)  # Waits here, blocks other requests
    return result

# FastAPI asynchronous code
async def predict():
    result = await ml_model.predict(data)  # Doesn't block, can handle other requests
    return result
```

**Key Benefit**: While one request waits for ML model prediction, the API can process other incoming requests.

---

## 🚀 Fast to Code - Development Speed

### 🎯 Three Major Advantages

#### 1️⃣ Automatic Input Validation

| Feature | Description | Benefit |
|---------|-------------|---------|
| **Pydantic Integration** | Built-in data validation | No manual type checking needed |
| **Type Hints Support** | Python type annotations work seamlessly | Catch errors early |
| **Automatic Conversion** | Data types converted automatically | Less boilerplate code |

```python
# FastAPI automatically validates this
@app.post("/predict")
async def predict(feature1: int, feature2: float):
    # No need to manually check if feature1 is integer
    # No need to manually check if feature2 is float
    # FastAPI + Pydantic handles this automatically
    return {"prediction": model.predict([feature1, feature2])}
```

#### 2️⃣ Auto-Generated Interactive Documentation

| Documentation Type | URL | Features |
|-------------------|-----|----------|
| **Swagger UI** | `/docs` | Interactive testing interface |
| **ReDoc** | `/redoc` | Alternative documentation view |

**What you get automatically**:
- List of all endpoints
- Request/response formats
- Interactive testing capability
- No need for Postman during development

#### 3️⃣ Modern Library Integration

| Domain | Libraries | Integration Quality |
|--------|-----------|-------------------|
| **Machine Learning** | Scikit-learn, TensorFlow, PyTorch | Seamless |
| **Authentication** | OAuth, JWT | Built-in support |
| **Database** | SQLAlchemy, MongoDB | Native compatibility |
| **Deployment** | Docker, Kubernetes | Direct integration |

---

## 🛠️ Installation and Setup

### 📋 Prerequisites
- Python 3.7+
- Basic understanding of Python functions
- Code editor (VS Code recommended)

### 🔧 Step-by-Step Installation

#### 1️⃣ Create Project Directory
```bash
mkdir api-tutorials
cd api-tutorials
```

#### 2️⃣ Create Virtual Environment
```bash
python -m venv myenv
```

#### 3️⃣ Activate Virtual Environment
```bash
# Windows
myenv\Scripts\activate

# macOS/Linux
source myenv/bin/activate
```

#### 4️⃣ Install Required Packages
```bash
pip install fastapi uvicorn
```

**What gets installed**:
- `fastapi`: The main framework
- `uvicorn`: ASGI server for running the application
- `starlette`: Automatically installed as FastAPI dependency
- `pydantic`: Automatically installed for data validation

---

## 💻 First API Demo

### 📝 Creating Your First API

#### 1️⃣ Create main.py file
```python
from fastapi import FastAPI

# Create FastAPI application instance
app = FastAPI()

# Define root endpoint
@app.get("/")
async def hello():
    return {"message": "Hello World"}

# Define about endpoint
@app.get("/about")
async def about():
    return {"message": "CampusX is an education platform where you can learn AI"}
```

#### 2️⃣ Code Explanation

| Component | Purpose | Example |
|-----------|---------|---------|
| `FastAPI()` | Creates application instance | `app = FastAPI()` |
| `@app.get()` | Defines GET endpoint decorator | `@app.get("/")` |
| Route path | URL endpoint definition | `"/"` for home, `"/about"` for about page |
| Function | Business logic handler | `async def hello():` |
| Return value | Response data | `{"message": "Hello World"}` |

#### 3️⃣ Running the Application
```bash
uvicorn main:app --reload
```

**Command breakdown**:
- `uvicorn`: The ASGI server
- `main`: Python file name (main.py)
- `app`: FastAPI instance variable name
- `--reload`: Auto-restart on code changes

#### 4️⃣ Testing Your API

| URL | Response | Purpose |
|-----|----------|---------|
| `http://127.0.0.1:8000/` | `{"message": "Hello World"}` | Home endpoint |
| `http://127.0.0.1:8000/about` | `{"message": "CampusX is..."}` | About endpoint |
| `http://127.0.0.1:8000/docs` | Interactive documentation | API testing interface |

### 🔍 Interactive Documentation Features

When you visit `/docs`, you get:

#### 📊 Automatic Documentation
- All endpoints listed automatically
- Request/response schemas
- HTTP methods clearly marked
- Parameter requirements

#### 🧪 Interactive Testing
- "Try it out" buttons for each endpoint
- Execute requests directly from browser
- See real responses
- No need for external tools like Postman

#### 📝 Example Documentation View
```
GET /
- Summary: Hello
- Responses: 200 (Successful Response)

GET /about  
- Summary: About
- Responses: 200 (Successful Response)
```

---

## ⚡ Quick Reference

### 🔧 Essential Commands
```bash
# Create virtual environment
python -m venv myenv

# Activate environment (Windows)
myenv\Scripts\activate

# Install FastAPI
pip install fastapi uvicorn

# Run application
uvicorn main:app --reload
```

### 📝 Basic API Structure
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/endpoint")
async def function_name():
    return {"key": "value"}
```

### 🌐 Important URLs
```
Application: http://127.0.0.1:8000/
Documentation: http://127.0.0.1:8000/docs
Alternative docs: http://127.0.0.1:8000/redoc
```

---

## 📊 Summary Table

| Aspect | Flask | FastAPI |
|--------|-------|---------|
| **SGI Protocol** | WSGI (Synchronous) | ASGI (Asynchronous) |
| **Web Server** | Gunicorn | Uvicorn |
| **Performance** | Sequential processing | Concurrent processing |
| **Documentation** | Manual creation needed | Auto-generated |
| **Type Validation** | Manual implementation | Built-in with Pydantic |
| **Modern Features** | Limited async support | Full async/await support |
| **Development Speed** | More boilerplate code | Minimal code required |
| **Learning Curve** | Moderate | Gentle (similar to Flask) |

---

## 🎯 Key Takeaways

### 🌟 Why Choose FastAPI?

1. **🚀 Performance**: Asynchronous architecture enables handling multiple concurrent requests
2. **⚡ Development Speed**: Less code, more functionality with automatic validation and documentation
3. **🔧 Modern Stack**: Built for contemporary web development with async/await support
4. **📚 Auto Documentation**: Interactive docs generated automatically
5. **🔗 Integration**: Seamless compatibility with ML libraries and modern tools

### 💡 Pro Tips

- **Always use virtual environments** for Python projects
- **Use `--reload` flag** during development for automatic code reloading
- **Explore `/docs` endpoint** for testing APIs without external tools
- **Start simple** with GET endpoints before moving to POST requests
- **Type hints are your friend** - they enable automatic validation

### 🎯 Next Steps

1. Practice creating multiple endpoints
2. Learn about different HTTP methods (POST, PUT, DELETE)
3. Explore request/response models with Pydantic
4. Build a complete ML model API
5. Learn about authentication and database integration

### ⚠️ Common Mistakes to Avoid

- Forgetting to activate virtual environment
- Not using async/await for I/O operations
- Ignoring type hints (missing out on validation benefits)
- Not exploring the auto-generated documentation
- Trying to learn everything at once - start with basics

---

**🎓 Remember**: FastAPI combines the simplicity of Flask with the performance of modern asynchronous frameworks. It's designed to make you productive quickly while building high-performance applications. The key is to start simple and gradually explore its powerful features!