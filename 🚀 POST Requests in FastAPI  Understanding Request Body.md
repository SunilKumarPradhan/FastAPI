---
title: "🚀 POST Requests in FastAPI | Understanding Request Body"
layout: default
nav_order: 6
parent: "Lecture Notes"
description: "Lecture notes: 🚀 POST Requests in FastAPI | Understanding Request Body"
last_modified_date: 2026-01-09
source_transcript: "006_Post_Request_in_FastAPI_What_is_Request_Body_Video_5_CampusX"
generated_by: "NoteyBoy"
---

# 🚀 POST Requests in FastAPI | Understanding Request Body

## 📑 Table of Contents

- [📖 Introduction/Overview](#-introductionoverview)
- [🔄 Recap of Previous Videos](#-recap-of-previous-videos)
- [📝 Understanding POST Requests and Request Body](#-understanding-post-requests-and-request-body)
- [🏗️ Building the Create Patient Endpoint](#️-building-the-create-patient-endpoint)
- [⚡ Quick Reference](#-quick-reference)
- [📊 Summary Table](#-summary-table)
- [🎯 Key Takeaways](#-key-takeaways)
- [💡 Pro Tips](#-pro-tips)

---

## 📖 Introduction/Overview

This video covers the implementation of **POST requests** in FastAPI, specifically focusing on creating a new patient in our Patient Management System. We'll learn about **Request Body**, data validation using **Pydantic models**, and complete the CREATE operation in our CRUD API.

> **⚠️ Important Prerequisite**: This video requires solid understanding of **Pydantic**. Watch the Pydantic crash course on the channel before proceeding.

### 🎯 What We'll Build Today
- A `/create` endpoint that accepts POST requests
- Pydantic model for data validation
- Request body handling and processing
- Automatic BMI and health verdict calculation

---

## 🔄 Recap of Previous Videos

### 📚 Videos 1-4 Summary

| Video | Topic | Key Concepts |
|-------|-------|--------------|
| **Video 1** | API Fundamentals | What are APIs, why they're useful |
| **Video 2** | FastAPI Basics | Strengths, weaknesses, Flask comparison |
| **Video 3** | Project Start | Patient Management System, first endpoints |
| **Video 4** | Advanced Retrieval | Sorting, query parameters |

### 🔗 Current Endpoints Built

| Endpoint | Method | Purpose | Key Feature |
|----------|--------|---------|-------------|
| `/` | GET | Hello message | Basic toy endpoint |
| `/about` | GET | Project description | Project overview |
| `/view` | GET | View all patients | Complete patient list |
| `/patient/{id}` | GET | View specific patient | **Path parameters** |
| `/sort` | GET | Sorted patient data | **Query parameters** |

### 🎯 Today's Goal
Complete the **CREATE** operation by building a POST endpoint that allows clients to add new patients to our database.

---

## 📝 Understanding POST Requests and Request Body

### 🔍 What is a Request Body?

> **Request Body** is the portion of an HTTP request that contains data sent by the client to the server. It is typically used in HTTP methods such as POST and PUT to transmit structured data such as JSON and XML for the purpose of creating and updating resources on the server.

### 📊 GET vs POST Comparison

| Aspect | GET Requests | POST Requests |
|--------|--------------|---------------|
| **Purpose** | Retrieve data from server | Send data to server |
| **Data Location** | URL parameters/query strings | Request body |
| **Data Size** | Limited by URL length | Much larger capacity |
| **Use Cases** | Reading, searching, filtering | Creating, updating records |
| **Our Examples** | View patients, sort data | Create new patient |

### 🔄 The CREATE Process Flow

```
Step 1: Client → Server
├── HTTP POST Request
├── Request Body (JSON)
└── Patient data (name, age, etc.)

Step 2: Server Processing
├── Data Validation (Pydantic)
├── Check for duplicates
└── Calculate BMI & verdict

Step 3: Database Update
├── Add to JSON file
└── Return success response
```

---

## 🏗️ Building the Create Patient Endpoint

### 📋 Step 1: Creating the Pydantic Model

First, we need to install and import Pydantic:

```bash
pip install pydantic
```

```python
from pydantic import BaseModel, Field, computed_field
from typing import Annotated, Literal
```

#### 🏗️ Basic Patient Model Structure

```python
class Patient(BaseModel):
    id: Annotated[str, Field(
        description="ID of the patient",
        examples=["P01"]
    )]
    
    name: Annotated[str, Field(
        description="Name of the patient"
    )]
    
    city: Annotated[str, Field(
        description="City where the patient is living"
    )]
    
    age: Annotated[int, Field(
        gt=0, lt=120,
        description="Age of the patient"
    )]
    
    gender: Annotated[Literal["male", "female", "others"], Field(
        description="Gender of the patient"
    )]
    
    height: Annotated[float, Field(
        gt=0,
        description="Height of the patient in meters"
    )]
    
    weight: Annotated[float, Field(
        gt=0,
        description="Weight of the patient in kgs"
    )]
```

### 🧮 Step 2: Adding Computed Fields

#### BMI Calculation
```python
@computed_field
@property
def bmi(self) -> float:
    return round(self.weight / (self.height ** 2), 2)
```

#### Health Verdict Calculation
```python
@computed_field
@property
def verdict(self) -> str:
    if self.bmi < 18.5:
        return "underweight"
    elif self.bmi < 25:
        return "normal"
    elif self.bmi < 30:
        return "overweight"
    else:
        return "obese"
```

### 🔧 Step 3: Building the Endpoint

```python
from fastapi.responses import JSONResponse

@app.post("/create")
def create_patient(patient: Patient):
    # Step 1: Load existing data
    data = load_data()
    
    # Step 2: Check if patient already exists
    if patient.id in data:
        raise HTTPException(
            status_code=400,
            detail="Patient already exists"
        )
    
    # Step 3: Add new patient to database
    data[patient.id] = patient.model_dump(exclude={"id"})
    
    # Step 4: Save updated data
    save_data(data)
    
    # Step 5: Return success response
    return JSONResponse(
        status_code=201,
        content={"message": "Patient created successfully"}
    )
```

### 💾 Step 4: Utility Function for Saving Data

```python
def save_data(data):
    with open("patients.json", "w") as f:
        json.dump(data, f)
```

---

## ⚡ Quick Reference

### 🔧 Essential Imports
```python
from pydantic import BaseModel, Field, computed_field
from typing import Annotated, Literal
from fastapi.responses import JSONResponse
```

### 📊 HTTP Status Codes
```python
# Success codes
201  # Created (for successful POST)
200  # OK (for successful GET)

# Error codes
400  # Bad Request (validation errors, duplicates)
404  # Not Found (resource doesn't exist)
```

### 🎯 Key Pydantic Methods
```python
# Convert Pydantic object to dictionary
patient.model_dump()

# Exclude specific fields
patient.model_dump(exclude={"id"})

# Access computed fields
patient.bmi  # Automatically calculated
patient.verdict  # Automatically calculated
```

---

## 📊 Summary Table

| Component | Purpose | Key Features |
|-----------|---------|--------------|
| **Pydantic Model** | Data validation & structure | Type checking, field validation, computed fields |
| **Request Body** | Data transmission | JSON format, automatic parsing |
| **POST Endpoint** | Create new resources | Data validation, duplicate checking, database update |
| **Computed Fields** | Automatic calculations | BMI calculation, health verdict |
| **Error Handling** | Robust API responses | HTTP exceptions, status codes |

### 🔄 Data Flow Summary

```
Client Request → Pydantic Validation → Duplicate Check → Database Update → Success Response
```

---

## 🎯 Key Takeaways

### ✅ Essential Concepts

1. **Request Body vs Query Parameters**
   - Request body for complex data (POST/PUT)
   - Query parameters for simple filters (GET)

2. **Pydantic Integration**
   - Automatic validation and parsing
   - Type safety and error handling
   - Computed fields for derived data

3. **HTTP Methods Matter**
   - GET: Retrieve data
   - POST: Create new resources
   - Status codes communicate results

4. **Data Validation Layers**
   - Type validation (int, str, float)
   - Range validation (gt=0, lt=120)
   - Choice validation (Literal types)

### 🔒 Security & Best Practices

- Always validate incoming data
- Check for duplicate resources
- Use appropriate HTTP status codes
- Provide meaningful error messages

---

## 💡 Pro Tips

### 🚀 Performance Tips
- Use `model_dump()` for efficient serialization
- Exclude unnecessary fields when saving to database
- Validate data at the API boundary

### 🎨 User Experience Tips
- Add descriptive field documentation
- Provide example values in Pydantic fields
- Use clear error messages for validation failures

### 🔧 Development Tips
- Test endpoints using FastAPI's automatic documentation (`/docs`)
- Use computed fields for derived data instead of manual calculations
- Leverage FastAPI's automatic request body parsing

### 🏗️ Architecture Tips
- Separate data models from business logic
- Create utility functions for common operations (load_data, save_data)
- Use proper HTTP status codes for different scenarios

### 📝 Documentation Benefits
The detailed Pydantic model automatically generates:
- Interactive API documentation
- Request/response schemas
- Validation error descriptions
- Example request bodies

### 🔄 Next Steps
In upcoming videos, we'll implement:
- **UPDATE** endpoint (PUT method)
- **DELETE** endpoint (DELETE method)
- Complete CRUD operations

---

### 🎯 Testing Your Endpoint

1. **Access Documentation**: Navigate to `/docs`
2. **Try the Endpoint**: Click "Try it out" on the POST `/create`
3. **Test Validation**: Try invalid data to see error responses
4. **Test Success**: Create a new patient with valid data
5. **Verify Creation**: Check the JSON file or use `/view` endpoint

**Example Test Data:**
```json
{
  "id": "P006",
  "name": "Rahul Gupta",
  "city": "Hyderabad",
  "age": 19,
  "gender": "male",
  "height": 1.74,
  "weight": 55
}
```

This comprehensive approach ensures robust, user-friendly APIs that handle data validation, error cases, and provide excellent developer experience through automatic documentation.