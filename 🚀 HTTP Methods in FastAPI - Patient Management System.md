---
title: "🚀 HTTP Methods in FastAPI - Patient Management System"
layout: default
nav_order: 3
parent: "Lecture Notes"
description: "Lecture notes: 🚀 HTTP Methods in FastAPI - Patient Management System"
last_modified_date: 2026-01-09
source_transcript: "003_HTTP_Methods_in_FastAPI_Video_3_CampusX"
generated_by: "NoteyBoy"
---

# 🚀 HTTP Methods in FastAPI - Patient Management System

## 📑 Table of Contents

- [📖 Introduction](#introduction)
- [🏥 Project Overview](#project-overview)
- [💻 Software Classification](#software-classification)
- [🌐 HTTP Methods Deep Dive](#http-methods-deep-dive)
- [🛠️ Hands-on Implementation](#hands-on-implementation)
- [⚡ Quick Reference](#quick-reference)
- [📊 Summary Table](#summary-table)
- [🎯 Key Takeaways](#key-takeaways)

---

## 📖 Introduction

This lecture marks the beginning of hands-on FastAPI development, transitioning from theoretical concepts to practical implementation. We'll build a **Patient Management System API** while learning HTTP methods and their real-world applications.

> **Why This Matters**: Understanding HTTP methods is crucial for any web developer as they form the foundation of client-server communication in web applications and APIs.

---

## 🏥 Project Overview

### 🎯 Problem Statement

Traditional doctor-patient record management faces several challenges:
- **Paper-based prescriptions** that can be easily lost
- **No centralized storage** of patient history
- **Difficulty in tracking** patient progress over time
- **Manual processes** prone to human error

### 💡 Our Solution

We're building a **digital patient management system** that provides:

| Feature | Description | Benefit |
|---------|-------------|---------|
| **Digital Records** | Store patient data electronically | No more lost papers |
| **Centralized Database** | All records in one place | Easy access and backup |
| **CRUD Operations** | Create, Read, Update, Delete patients | Complete data management |
| **BMI Calculation** | Automatic BMI computation | Reduces manual errors |

### 👥 Patient Profile Structure

```json
{
  "id": 1,
  "name": "Patient Name",
  "city": "City Name",
  "age": 30,
  "gender": "Male/Female",
  "height": 175,
  "weight": 70,
  "bmi": 22.86
}
```

### 🔧 Technical Architecture

```
┌─────────────────┐    HTTP Requests    ┌─────────────────┐
│   Doctor's App  │ ──────────────────► │   FastAPI       │
│   (Frontend)    │                     │   Backend       │
│                 │ ◄────────────────── │                 │
└─────────────────┘    JSON Response    └─────────────────┘
                                                │
                                                ▼
                                        ┌─────────────────┐
                                        │  patients.json  │
                                        │   (Database)    │
                                        └─────────────────┘
```

### 📋 API Endpoints We'll Build

| Endpoint | HTTP Method | Purpose | Example URL |
|----------|-------------|---------|-------------|
| **View All** | GET | Get all patients | `/view` |
| **View One** | GET | Get specific patient | `/patient/{id}` |
| **Create** | POST | Add new patient | `/create` |
| **Update** | PUT | Modify patient data | `/update/{id}` |
| **Delete** | DELETE | Remove patient | `/delete/{id}` |

---

## 💻 Software Classification

### 🔍 Understanding Software Types

> **Key Concept**: All software can be classified based on user interaction levels.

#### 📊 Classification Table

| Type | Interaction Level | Examples | Use Case |
|------|------------------|----------|----------|
| **Static Software** | Low | Calendar, Clock | Information retrieval |
| **Dynamic Software** | High | Excel, Word, Instagram | Data manipulation |

#### 🔄 The CRUD Pattern

Every dynamic software supports exactly **4 types of interactions**:

```
C - CREATE    (Add new data)
R - RETRIEVE  (View existing data)  
U - UPDATE    (Modify existing data)
D - DELETE    (Remove data)
```

### 🌐 Websites vs Desktop Software

| Aspect | Desktop Software | Websites |
|--------|------------------|----------|
| **Installation** | Same machine | Remote server |
| **Access** | Local | Internet-based |
| **Communication** | Direct | HTTP protocol |
| **Examples** | Excel, Word | Instagram, Zomato |

> **Important**: Websites are just software running on remote servers, accessed via HTTP protocol.

---

## 🌐 HTTP Methods Deep Dive

### 🎯 Why HTTP Methods Matter

When building web applications, we need to tell the server **what type of operation** we want to perform. HTTP methods serve as **verbs** in our requests.

### 📋 The Four Essential HTTP Methods

| HTTP Method | CRUD Operation | Purpose | Example Use Case |
|-------------|----------------|---------|------------------|
| **GET** | Retrieve | Fetch data | View profile page |
| **POST** | Create | Send new data | Submit registration form |
| **PUT** | Update | Modify existing data | Edit profile information |
| **DELETE** | Delete | Remove data | Delete a post |

### 🔍 Real-World Examples

#### Instagram Operations
```
✅ CREATE: Upload new photo → POST request
✅ RETRIEVE: View feed → GET request  
✅ UPDATE: Edit caption → PUT request
✅ DELETE: Remove post → DELETE request
```

#### Zomato Operations
```
✅ CREATE: Place new order → POST request
✅ RETRIEVE: View past orders → GET request
✅ UPDATE: Change delivery address → PUT request  
✅ DELETE: Cancel order → DELETE request
```

### 🛠️ HTTP Request Structure

```http
GET /profile HTTP/1.1
Host: example.com
Content-Type: application/json

{
  "user_id": 123
}
```

### 💡 Pro Tips

- **GET and POST** are most commonly used (80% of cases)
- **PUT and DELETE** are used less frequently but equally important
- Always choose the **semantically correct** HTTP method
- **GET requests** should never modify data on the server

---

## 🛠️ Hands-on Implementation

### 🚀 Project Setup

#### 1. Environment Activation
```bash
# Activate virtual environment
env\Scripts\activate
```

#### 2. Project Structure
```
project/
├── main.py
├── patients.json
└── env/
```

#### 3. Sample Data (patients.json)
```json
[
  {
    "id": 1,
    "name": "John Doe",
    "city": "Mumbai",
    "age": 30,
    "gender": "Male",
    "height": 175,
    "weight": 70,
    "bmi": 22.86
  },
  {
    "id": 2,
    "name": "Jane Smith",
    "city": "Delhi",
    "age": 25,
    "gender": "Female", 
    "height": 165,
    "weight": 60,
    "bmi": 22.04
  }
]
```

### 📝 Code Implementation

#### Helper Function for Data Loading
```python
import json
from fastapi import FastAPI

app = FastAPI()

def load_data():
    """Load patient data from JSON file"""
    with open('patients.json', 'r') as f:
        data = json.load(f)
    return data
```

#### Basic Endpoints
```python
@app.get("/")
def home():
    return {"message": "Patient Management System API"}

@app.get("/about")
def about():
    return {"message": "A fully functional API to manage your patient records"}
```

#### Main View Endpoint
```python
@app.get("/view")
def view():
    """Retrieve all patient records"""
    data = load_data()
    return data
```

### 🏃‍♂️ Running the Application

```bash
# Start the FastAPI server
uvicorn main:app --reload
```

### 🌐 Testing the API

#### Access Points:
- **Home**: `http://localhost:8000/`
- **About**: `http://localhost:8000/about`
- **View All Patients**: `http://localhost:8000/view`
- **API Documentation**: `http://localhost:8000/docs`

#### Expected Response from `/view`:
```json
[
  {
    "id": 1,
    "name": "John Doe",
    "city": "Mumbai",
    "age": 30,
    "gender": "Male",
    "height": 175,
    "weight": 70,
    "bmi": 22.86
  },
  {
    "id": 2,
    "name": "Jane Smith", 
    "city": "Delhi",
    "age": 25,
    "gender": "Female",
    "height": 165,
    "weight": 60,
    "bmi": 22.04
  }
]
```

---

## ⚡ Quick Reference

### HTTP Methods Cheat Sheet
```python
# GET - Retrieve data
@app.get("/endpoint")
def get_data():
    return data

# POST - Create new data  
@app.post("/endpoint")
def create_data(item: dict):
    return {"status": "created"}

# PUT - Update existing data
@app.put("/endpoint/{id}")
def update_data(id: int, item: dict):
    return {"status": "updated"}

# DELETE - Remove data
@app.delete("/endpoint/{id}")  
def delete_data(id: int):
    return {"status": "deleted"}
```

### FastAPI Decorators
```python
from fastapi import FastAPI

app = FastAPI()

# Route decorators
@app.get("/path")      # GET method
@app.post("/path")     # POST method  
@app.put("/path")      # PUT method
@app.delete("/path")   # DELETE method
```

### JSON File Operations
```python
import json

# Read JSON file
def load_data():
    with open('file.json', 'r') as f:
        return json.load(f)

# Write JSON file
def save_data(data):
    with open('file.json', 'w') as f:
        json.dump(data, f, indent=2)
```

---

## 📊 Summary Table

| Concept | Description | Implementation |
|---------|-------------|----------------|
| **HTTP Methods** | Verbs that define request type | `@app.get()`, `@app.post()` etc. |
| **CRUD Operations** | Create, Read, Update, Delete | Maps to POST, GET, PUT, DELETE |
| **FastAPI Decorators** | Define API endpoints | `@app.method("/route")` |
| **JSON Storage** | Simple database alternative | `json.load()` and `json.dump()` |
| **Route Parameters** | Dynamic URL segments | `/patient/{id}` |
| **Request/Response** | Client-server communication | JSON format |

---

## 🎯 Key Takeaways

### 🔑 Essential Concepts

1. **HTTP Methods are Universal**
   - Every web interaction uses GET, POST, PUT, or DELETE
   - Choose methods based on the operation type, not convenience

2. **CRUD Pattern is Everywhere**
   - All dynamic software follows Create, Read, Update, Delete
   - This pattern applies to databases, APIs, and user interfaces

3. **FastAPI Simplifies API Development**
   - Decorators make endpoint creation intuitive
   - Automatic documentation generation saves time

4. **JSON as Simple Database**
   - Perfect for prototyping and learning
   - Easy to understand and manipulate

### 🚀 Next Steps

- Implement specific patient retrieval by ID
- Add sorting and filtering capabilities  
- Create POST endpoint for adding new patients
- Build UPDATE and DELETE functionality

### ⚠️ Common Mistakes to Avoid

- **Don't use GET for data modification** - GET should only retrieve data
- **Don't ignore HTTP status codes** - Use appropriate codes for different scenarios
- **Don't forget error handling** - Always handle file not found, invalid JSON, etc.
- **Don't hardcode file paths** - Use relative paths or configuration

### 💡 Pro Tips

- **Use FastAPI's automatic documentation** - Visit `/docs` for interactive API testing
- **Follow RESTful conventions** - Use plural nouns for collections (`/patients` not `/patient`)
- **Validate input data** - Use Pydantic models for request/response validation
- **Keep functions small** - Single responsibility principle applies to API endpoints

---

> **Remember**: This is just the beginning! We've built the foundation for a complete patient management system. In upcoming videos, we'll add more sophisticated features like patient search, data validation, and advanced CRUD operations.