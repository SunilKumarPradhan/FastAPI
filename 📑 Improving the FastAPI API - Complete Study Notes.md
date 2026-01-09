---
title: "📑 Improving the FastAPI API - Complete Study Notes"
layout: default
nav_order: 9
parent: "Lecture Notes"
description: "Lecture notes: 📑 Improving the FastAPI API - Complete Study Notes"
last_modified_date: 2026-01-09
source_transcript: "009_Improving_the_FastAPI_API_Video_8_CampusX"
generated_by: "NoteyBoy"
---

# 📑 Improving the FastAPI API - Complete Study Notes

## 📋 Table of Contents

1. [📖 Introduction/Overview](#introduction-overview)
2. [🔄 Quick Recap](#quick-recap)
3. [🎯 Improvement Goals](#improvement-goals)
4. [🏗️ Project Structure Improvement](#project-structure-improvement)
5. [🔧 Field Validation Enhancement](#field-validation-enhancement)
6. [🏠 Home and Health Endpoints](#home-and-health-endpoints)
7. [📁 Code Refactoring and Separation of Concerns](#code-refactoring-and-separation-of-concerns)
8. [🎯 Enhanced API Response](#enhanced-api-response)
9. [📊 Response Model Implementation](#response-model-implementation)
10. [⚡ Quick Reference](#quick-reference)
11. [📊 Summary Table](#summary-table)
12. [🎯 Key Takeaways](#key-takeaways)
13. [💡 Pro Tips](#pro-tips)

---

## 📖 Introduction/Overview

This video focuses on **improving a FastAPI application** that was built for insurance premium prediction. The goal is to transform a basic API into a **production-grade, industry-standard application** ready for deployment.

> **Why This Matters**: Basic APIs work for learning, but production environments require robust, maintainable, and scalable code with proper error handling, validation, and structure.

### 🎯 What We'll Accomplish
- Transform basic API into production-ready application
- Implement industry best practices
- Prepare for Docker containerization and AWS deployment
- Add proper validation, error handling, and response models

---

## 🔄 Quick Recap

### 📝 Previous Work Summary
In the last video, we built:
- **Machine Learning Model**: Random Forest for insurance premium prediction
- **API Endpoint**: `/predict` endpoint using FastAPI
- **Frontend**: Streamlit application to interact with the API

### 🔍 Current State
- Model predicts: `High`, `Medium`, or `Low` premium categories
- Basic API structure with minimal validation
- All code in single file (not production-ready)

---

## 🎯 Improvement Goals

| Improvement | Purpose | Industry Benefit |
|-------------|---------|------------------|
| **Dedicated Project Folder** | Clean separation from other projects | Better for Docker containerization |
| **Field Validation** | Handle case-sensitive inputs | Consistent data processing |
| **Home & Health Endpoints** | API status monitoring | Required for cloud deployment |
| **Code Refactoring** | Separation of concerns | Maintainable, scalable code |
| **Enhanced Responses** | Rich output with confidence scores | Better user experience |
| **Response Models** | Output validation | Type safety and documentation |
| **Error Handling** | Graceful failure management | Production reliability |

---

## 🏗️ Project Structure Improvement

### 🚫 Problem with Current Structure
```
project_folder/
├── app.py (API code)
├── model.pkl
├── jupyter_notebooks/
├── other_projects/
└── random_files/
```

### ✅ Improved Structure
```
insurance_premium_prediction/
├── app.py
├── requirements.txt
├── model/
│   ├── model.pkl
│   └── predict.py
├── schema/
│   ├── user_input.py
│   └── prediction_response.py
└── config/
    └── city_tier.py
```

### 🔧 Implementation Steps

#### Step 1: Create Dedicated Project Folder
```bash
mkdir insurance_premium_prediction
cd insurance_premium_prediction
```

#### Step 2: Set Up Virtual Environment
```bash
python -m venv myenv
myenv\Scripts\activate  # Windows
# or
source myenv/bin/activate  # Linux/Mac
```

#### Step 3: Generate Requirements File
```bash
pip freeze > requirements.txt
```

#### Step 4: Install Dependencies
```bash
pip install -r requirements.txt
```

### 💡 Pro Tip
> Always create a dedicated project folder for APIs. This makes Docker containerization much easier and keeps your codebase organized.

---

## 🔧 Field Validation Enhancement

### 🚫 Current Problem
- City names must be in **Title Case** (e.g., "Mumbai")
- If user sends "mumbai" (lowercase), it's treated as Tier 3 city
- Inconsistent behavior based on input formatting

### ✅ Solution: Field Validator

```python
from pydantic import BaseModel, field_validator

class UserInput(BaseModel):
    # ... other fields ...
    city: str
    
    @field_validator('city')
    @classmethod
    def validate_city(cls, v):
        return v.strip().title()
```

### 🔍 How It Works
1. **Strip**: Removes extra whitespaces
2. **Title**: Converts to proper case (first letter capital)
3. **Consistent Processing**: "mumbai" → "Mumbai"

### 🎯 Benefits
- **Consistency**: Same output regardless of input case
- **User-Friendly**: Users don't need to worry about formatting
- **Data Quality**: Standardized city names for processing

---

## 🏠 Home and Health Endpoints

### 🎯 Why Add These Endpoints?

| Endpoint | Purpose | Target Audience |
|----------|---------|-----------------|
| **Home (`/`)** | Human-readable API status | Developers, testers |
| **Health (`/health`)** | Machine-readable status | Cloud services, load balancers |

### 🏠 Home Endpoint Implementation

```python
@app.get("/")
def home():
    return {"message": "Insurance Premium Prediction API"}
```

**Purpose**: When someone visits your API's base URL, they see a clear message about what the API does.

### 🏥 Health Check Endpoint Implementation

```python
@app.get("/health")
def health_check():
    return {
        "status": "OK",
        "version": model_version,
        "model_loaded": model is not None
    }
```

### 🔍 Health Check Components

| Field | Type | Purpose |
|-------|------|---------|
| `status` | String | Overall API status |
| `version` | String | Model version tracking |
| `model_loaded` | Boolean | Confirms ML model is loaded |

### ☁️ Cloud Deployment Requirement
> **Important**: AWS services like Kubernetes and Elastic Load Balancer **require** health check endpoints to monitor your application's status.

---

## 📁 Code Refactoring and Separation of Concerns

### 🚫 Before: Monolithic Structure
```python
# app.py - Everything in one file
from fastapi import FastAPI
from pydantic import BaseModel
import pickle
import pandas as pd

# Pydantic model
class UserInput(BaseModel):
    # ... all fields ...

# City lists
tier_1_cities = ["Mumbai", "Delhi", ...]
tier_2_cities = ["Pune", "Ahmedabad", ...]

# Model loading
with open("model.pkl", "rb") as f:
    model = pickle.load(f)

# API endpoints
@app.post("/predict")
def predict(user_input: UserInput):
    # ML prediction logic
    # Data processing
    # Return results
```

### ✅ After: Modular Structure

#### 📁 schema/user_input.py
```python
from pydantic import BaseModel, field_validator
from config.city_tier import tier_1_cities, tier_2_cities

class UserInput(BaseModel):
    age: int
    weight: float
    height: float
    income_lakhs: float
    smoker: bool
    city: str
    occupation: str
    
    @field_validator('city')
    @classmethod
    def validate_city(cls, v):
        return v.strip().title()
```

#### 📁 config/city_tier.py
```python
tier_1_cities = ["Mumbai", "Delhi", "Bangalore", "Hyderabad", "Chennai", "Kolkata"]
tier_2_cities = ["Pune", "Ahmedabad", "Jaipur", "Lucknow", "Kanpur", "Nagpur"]
```

#### 📁 model/predict.py
```python
import pickle
import pandas as pd
from pathlib import Path

# Load model
model_path = Path(__file__).parent / "model.pkl"
with open(model_path, "rb") as f:
    model = pickle.load(f)

model_version = "1.0.0"
class_labels = model.classes_

def predict_output(user_input_dict):
    # Convert to DataFrame
    input_df = pd.DataFrame([user_input_dict])
    
    # Get prediction and probabilities
    predicted_class = model.predict(input_df)[0]
    probabilities = model.predict_proba(input_df)[0]
    
    # Create confidence scores
    class_probabilities = {
        class_name: float(prob) 
        for class_name, prob in zip(class_labels, probabilities)
    }
    
    return {
        "predicted_category": predicted_class,
        "confidence": float(max(probabilities)),
        "class_probabilities": class_probabilities
    }
```

#### 📁 app.py (Clean and Focused)
```python
from fastapi import FastAPI, HTTPException
from fastapi.responses import JSONResponse
from schema.user_input import UserInput
from schema.prediction_response import PredictionResponse
from model.predict import predict_output, model, model_version

app = FastAPI()

@app.get("/")
def home():
    return {"message": "Insurance Premium Prediction API"}

@app.get("/health")
def health_check():
    return {
        "status": "OK",
        "version": model_version,
        "model_loaded": model is not None
    }

@app.post("/predict", response_model=PredictionResponse)
def predict_premium(user_input: UserInput):
    try:
        user_input_dict = user_input.dict()
        result = predict_output(user_input_dict)
        return result
    except Exception as e:
        return JSONResponse(
            status_code=500,
            content={"error": str(e)}
        )
```

### 🎯 Benefits of Separation of Concerns

| Aspect | Benefit |
|--------|---------|
| **Maintainability** | Easy to find and modify specific functionality |
| **Testability** | Can test individual components separately |
| **Scalability** | Easy to add new features without affecting existing code |
| **Collaboration** | Multiple developers can work on different modules |
| **Debugging** | Easier to isolate and fix issues |

---

## 🎯 Enhanced API Response

### 🚫 Before: Basic Response
```json
{
  "predicted_category": "High"
}
```

### ✅ After: Rich Response
```json
{
  "predicted_category": "Low",
  "confidence": 0.39,
  "class_probabilities": {
    "High": 0.36,
    "Low": 0.39,
    "Medium": 0.25
  }
}
```

### 🔧 Implementation Details

```python
def predict_output(user_input_dict):
    input_df = pd.DataFrame([user_input_dict])
    
    # Get prediction
    predicted_class = model.predict(input_df)[0]
    
    # Get probability scores for all classes
    probabilities = model.predict_proba(input_df)[0]
    
    # Create detailed response
    class_probabilities = {
        class_name: float(prob) 
        for class_name, prob in zip(class_labels, probabilities)
    }
    
    return {
        "predicted_category": predicted_class,
        "confidence": float(max(probabilities)),
        "class_probabilities": class_probabilities
    }
```

### 🎯 Why Enhanced Responses Matter

| Information | Value to User |
|-------------|---------------|
| **Predicted Category** | Primary decision |
| **Confidence Score** | How sure the model is |
| **All Class Probabilities** | Alternative scenarios and their likelihood |

### 💡 Real-World Example
If model predicts "Low" with 39% confidence:
- User knows it's a close call
- "High" has 36% probability (almost as likely)
- Can make informed decisions based on uncertainty

---

## 📊 Response Model Implementation

### 🎯 What is a Response Model?

> **Response Model**: A Pydantic model that defines and validates the structure of data your API endpoint returns.

### 🔧 Benefits of Response Models

| Benefit | Description |
|---------|-------------|
| **Validation** | Ensures output matches expected format |
| **Documentation** | Auto-generates API documentation |
| **Type Safety** | Catches type errors before response is sent |
| **Filtering** | Removes unnecessary data from response |

### 📁 schema/prediction_response.py

```python
from pydantic import BaseModel, Field
from typing import Dict

class PredictionResponse(BaseModel):
    predicted_category: str = Field(
        ...,
        description="The predicted insurance premium category",
        example="High"
    )
    confidence: float = Field(
        ...,
        description="Confidence score of the prediction (0-1)",
        example=0.73
    )
    class_probabilities: Dict[str, float] = Field(
        ...,
        description="Probability scores for all categories",
        example={
            "High": 0.73,
            "Medium": 0.17,
            "Low": 0.10
        }
    )
```

### 🔧 Using Response Model in Endpoint

```python
@app.post("/predict", response_model=PredictionResponse)
def predict_premium(user_input: UserInput):
    try:
        user_input_dict = user_input.dict()
        result = predict_output(user_input_dict)
        return result
    except Exception as e:
        return JSONResponse(
            status_code=500,
            content={"error": str(e)}
        )
```

### 📖 Enhanced API Documentation

With response models, FastAPI automatically generates:
- **Schema definitions** in the docs
- **Example responses** for each endpoint
- **Type information** for all fields
- **Field descriptions** and constraints

---

## ⚡ Quick Reference

### 🚀 Running the Application

```bash
# Activate virtual environment
myenv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Run the API
uvicorn app:app --reload
```

### 🌐 API Endpoints

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/` | GET | Home page with API info |
| `/health` | GET | Health check for monitoring |
| `/predict` | POST | Insurance premium prediction |
| `/docs` | GET | Interactive API documentation |

### 📁 Project Structure Commands

```bash
# Create project structure
mkdir insurance_premium_prediction
cd insurance_premium_prediction
mkdir model schema config

# Create files
touch app.py requirements.txt
touch model/predict.py
touch schema/user_input.py schema/prediction_response.py
touch config/city_tier.py
```

### 🔧 Key Imports

```python
# FastAPI
from fastapi import FastAPI, HTTPException
from fastapi.responses import JSONResponse

# Pydantic
from pydantic import BaseModel, Field, field_validator

# ML and Data
import pickle
import pandas as pd
from pathlib import Path
```

---

## 📊 Summary Table

| Improvement | Before | After | Impact |
|-------------|--------|-------|---------|
| **Project Structure** | Mixed with other projects | Dedicated folder | Clean, Docker-ready |
| **Field Validation** | Case-sensitive cities | Auto-formatting | User-friendly |
| **Endpoints** | Only `/predict` | `/`, `/health`, `/predict` | Monitoring-ready |
| **Code Organization** | Single file | Modular structure | Maintainable |
| **Response Format** | Simple string | Rich JSON with confidence | Informative |
| **Output Validation** | No validation | Pydantic response model | Type-safe |
| **Error Handling** | Basic | Try-catch with proper HTTP codes | Production-ready |

---

## 🎯 Key Takeaways

### 🏆 Production-Ready