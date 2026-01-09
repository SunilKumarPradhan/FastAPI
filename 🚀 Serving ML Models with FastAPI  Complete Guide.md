---
title: "🚀 Serving ML Models with FastAPI | Complete Guide"
layout: default
nav_order: 8
parent: "Lecture Notes"
description: "Lecture notes: 🚀 Serving ML Models with FastAPI | Complete Guide"
last_modified_date: 2026-01-09
source_transcript: "008_Serving_ML_Models_with_FastAPI_Video_7_CampusX"
generated_by: "NoteyBoy"
---

# 🚀 Serving ML Models with FastAPI | Complete Guide

## 📑 Table of Contents

1. [📖 Introduction & Overview](#-introduction--overview)
2. [🔄 Recap of Previous Learning](#-recap-of-previous-learning)
3. [🏗️ Project Architecture](#️-project-architecture)
4. [🤖 Part 1: Building the ML Model](#-part-1-building-the-ml-model)
5. [🔌 Part 2: Creating FastAPI Endpoint](#-part-2-creating-fastapi-endpoint)
6. [🎨 Part 3: Building Frontend with Streamlit](#-part-3-building-frontend-with-streamlit)
7. [⚡ Quick Reference](#-quick-reference)
8. [📊 Summary Table](#-summary-table)
9. [🎯 Key Takeaways](#-key-takeaways)

---

## 📖 Introduction & Overview

This comprehensive tutorial demonstrates how to serve Machine Learning models using FastAPI, creating a complete end-to-end solution from model building to user interface. We'll build an **Insurance Premium Prediction System** that categorizes users into High, Medium, or Low premium categories based on their personal attributes.

### 🎯 What You'll Learn
- How to build and export ML models for production
- Creating FastAPI endpoints for ML model inference
- Data validation using Pydantic models
- Building user interfaces with Streamlit
- Complete API consumption workflow

### 🔧 Why This Matters
In real-world scenarios, data scientists build models in Jupyter notebooks, but these models need to be accessible to end users. FastAPI bridges this gap by providing a robust framework to serve ML models as web APIs.

---

## 🔄 Recap of Previous Learning

### 📚 What We've Covered So Far
In the previous 6 videos of this FastAPI playlist, we built a **Patient Management System** covering:

| Concept | Description | Implementation |
|---------|-------------|----------------|
| **CRUD Operations** | Create, Read, Update, Delete | Built endpoints for patient management |
| **HTTP Methods** | GET, POST, PUT, DELETE | Implemented different request types |
| **Path Parameters** | Dynamic URL segments | `/patients/{patient_id}` |
| **Query Parameters** | URL query strings | `?age=25&city=Mumbai` |
| **Request Body** | JSON data in requests | Patient information payload |
| **Pydantic Models** | Data validation | Type checking and validation |

### 💡 Foundation Knowledge
With these fundamentals, we're ready to tackle more advanced topics like ML model serving, which is crucial for production deployments.

---

## 🏗️ Project Architecture

### 🔄 Complete Workflow Diagram

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   PART 1        │    │   PART 2        │    │   PART 3        │
│                 │    │                 │    │                 │
│  ML Model       │───▶│  FastAPI        │───▶│  Streamlit      │
│  Building       │    │  Endpoint       │    │  Frontend       │
│                 │    │                 │    │                 │
│ • Data Prep     │    │ • /predict      │    │ • User Form     │
│ • Feature Eng   │    │ • Validation    │    │ • API Calls     │
│ • Model Train   │    │ • Inference     │    │ • Results       │
│ • Export .pkl   │    │ • JSON Response │    │ • Display       │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

### 🌊 Data Flow Process

1. **User Input** → Streamlit form collects user data
2. **API Request** → Frontend sends POST request to `/predict`
3. **Validation** → Pydantic models validate incoming data
4. **Feature Engineering** → Raw data transformed to model features
5. **Prediction** → ML model generates prediction
6. **Response** → JSON response sent back to frontend
7. **Display** → User sees prediction results

---

## 🤖 Part 1: Building the ML Model

### 🎯 Problem Statement: Insurance Premium Prediction

We're building a model that predicts insurance premium categories based on user attributes.

> **Business Context**: Insurance companies need to categorize customers into risk groups to determine appropriate premium amounts. Users can also benefit by understanding their risk category before purchasing insurance.

### 📊 Dataset Features

#### Raw Input Features (User Provides)
| Feature | Type | Description | Validation |
|---------|------|-------------|------------|
| `age` | Integer | User's age | 0 < age ≤ 120 |
| `weight` | Float | Weight in kg | > 0 |
| `height` | Float | Height in meters | > 0, < 2.5 |
| `income_lpa` | Float | Annual income in lakhs | > 0 |
| `smoker` | Boolean | Smoking status | True/False |
| `city` | String | City of residence | Any string |
| `occupation` | String | Job category | Predefined options |

#### Engineered Features (Model Uses)
| Feature | Calculation | Purpose |
|---------|-------------|---------|
| `bmi` | weight / (height²) | Health indicator |
| `age_group` | Age ranges | Categorical age representation |
| `lifestyle_risk` | BMI + Smoking status | Combined health risk |
| `city_tier` | City classification | Urban development level |

### 🔧 Feature Engineering Strategy

#### 1. BMI Calculation
```python
bmi = weight / (height ** 2)
```

#### 2. Age Group Classification
```python
if age < 25: return "Young"
elif age < 45: return "Adult" 
elif age < 60: return "Middle-aged"
else: return "Senior"
```

#### 3. Lifestyle Risk Assessment
```python
if smoker and bmi > 30: return "High"
elif smoker and bmi > 27: return "Medium"
else: return "Low"
```

#### 4. City Tier Classification
- **Tier 1**: Mumbai, Delhi, Bangalore, Chennai, Kolkata, Hyderabad
- **Tier 2**: Pune, Ahmedabad, Jaipur, Lucknow, Kanpur, Nagpur
- **Tier 3**: All other cities

### 🛠️ Model Implementation

```python
import pandas as pd
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import train_test_split
from sklearn.compose import ColumnTransformer
from sklearn.preprocessing import OneHotEncoder
from sklearn.pipeline import Pipeline
import pickle

# Load and prepare data
df = pd.read_csv('insurance_data.csv')

# Feature engineering
df['bmi'] = df['weight'] / (df['height'] ** 2)
df['age_group'] = df['age'].apply(lambda x: 
    'Young' if x < 25 else 'Adult' if x < 45 else 'Middle-aged' if x < 60 else 'Senior')

# Define categorical and numerical features
categorical_features = ['age_group', 'lifestyle_risk', 'city_tier', 'occupation']
numerical_features = ['bmi', 'income_lpa']

# Create preprocessing pipeline
preprocessor = ColumnTransformer(
    transformers=[
        ('cat', OneHotEncoder(), categorical_features),
        ('num', 'passthrough', numerical_features)
    ]
)

# Create ML pipeline
pipeline = Pipeline([
    ('preprocessor', preprocessor),
    ('classifier', RandomForestClassifier(n_estimators=100, random_state=42))
])

# Train model
X = df[categorical_features + numerical_features]
y = df['premium_category']
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

pipeline.fit(X_train, y_train)

# Export model
with open('model.pkl', 'wb') as f:
    pickle.dump(pipeline, f)
```

### 📈 Model Performance
- **Algorithm**: Random Forest Classifier
- **Accuracy**: ~90% (on toy dataset)
- **Output Classes**: High, Medium, Low

> **⚠️ Important Note**: This is a toy dataset created for demonstration purposes. In production, you'd use real-world data with proper validation and testing.

---

## 🔌 Part 2: Creating FastAPI Endpoint

### 🎯 API Design Decisions

#### Why POST Method?
```python
@app.post("/predict")
```

**Explanation**: POST is used when the client sends data to the server for processing. While traditionally associated with creating resources, POST is also appropriate for:
- ML model inference
- Data processing operations
- Any operation where client sends data for server-side computation

### 📝 Pydantic Model for Input Validation

```python
from fastapi import FastAPI
from pydantic import BaseModel, Field, computed_field
from typing import Annotated, Literal
import pickle
import pandas as pd

# Load the trained model
with open('model.pkl', 'rb') as f:
    model = pickle.load(f)

app = FastAPI()

class UserInput(BaseModel):
    age: Annotated[int, Field(gt=0, le=120, description="Age of the user")]
    weight: Annotated[float, Field(gt=0, description="Weight of the patient")]
    height: Annotated[float, Field(gt=0, le=2.5, description="Height in meters")]
    income_lpa: Annotated[float, Field(gt=0, description="Annual salary in LPA")]
    smoker: Annotated[bool, Field(description="Is the user a smoker")]
    city: Annotated[str, Field(description="The city that the user belongs to")]
    occupation: Annotated[Literal[
        "Government Job", "Private Job", "Business Owner", 
        "Freelancer", "Student", "Retired"
    ], Field(description="Occupation of the user")]
    
    # Computed fields for feature engineering
    @computed_field
    @property
    def bmi(self) -> float:
        return self.weight / (self.height ** 2)
    
    @computed_field
    @property
    def age_group(self) -> str:
        if self.age < 25:
            return "Young"
        elif self.age < 45:
            return "Adult"
        elif self.age < 60:
            return "Middle-aged"
        else:
            return "Senior"
    
    @computed_field
    @property
    def lifestyle_risk(self) -> str:
        if self.smoker and self.bmi > 30:
            return "High"
        elif self.smoker and self.bmi > 27:
            return "Medium"
        else:
            return "Low"
    
    @computed_field
    @property
    def city_tier(self) -> int:
        tier_1_cities = ["Mumbai", "Delhi", "Bangalore", "Chennai", "Kolkata", "Hyderabad"]
        tier_2_cities = ["Pune", "Ahmedabad", "Jaipur", "Lucknow", "Kanpur", "Nagpur"]
        
        if self.city in tier_1_cities:
            return 1
        elif self.city in tier_2_cities:
            return 2
        else:
            return 3
```

### 🔄 Prediction Endpoint Implementation

```python
from fastapi.responses import JSONResponse

@app.post("/predict")
def predict_premium(data: UserInput):
    # Create input DataFrame for the model
    input_df = pd.DataFrame([{
        'bmi': data.bmi,
        'age_group': data.age_group,
        'lifestyle_risk': data.lifestyle_risk,
        'city_tier': data.city_tier,
        'income_lpa': data.income_lpa,
        'occupation': data.occupation
    }])
    
    # Make prediction
    prediction = model.predict(input_df)[0]
    
    # Return JSON response
    return JSONResponse(
        status_code=200,
        content={"predicted_category": prediction}
    )
```

### 🚀 Running the API

```bash
# Install dependencies
pip install fastapi uvicorn pandas scikit-learn

# Run the server
uvicorn app:app --reload
```

### 🧪 Testing the API

Access the interactive documentation at `http://localhost:8000/docs`

**Sample Request**:
```json
{
  "age": 35,
  "weight": 79,
  "height": 1.72,
  "income_lpa": 10,
  "smoker": false,
  "city": "Gurgaon",
  "occupation": "Government Job"
}
```

**Sample Response**:
```json
{
  "predicted_category": "Low"
}
```

---

## 🎨 Part 3: Building Frontend with Streamlit

### 🌐 Why Streamlit?

Streamlit allows Python developers to create web interfaces without HTML/CSS/JavaScript knowledge. Perfect for:
- Rapid prototyping
- Data science applications
- Internal tools
- MVP development

### 🎨 Frontend Implementation

```python
import streamlit as st
import requests

# API configuration
API_URL = "http://localhost:8000"
PREDICT_ENDPOINT = f"{API_URL}/predict"

# Page configuration
st.title("Insurance Premium Prediction")
st.write("Enter your details below:")

# Create input form
with st.form("prediction_form"):
    # Numerical inputs
    age = st.number_input("Age", min_value=1, max_value=120, value=30)
    weight = st.number_input("Weight (kg)", min_value=1.0, max_value=300.0, value=70.0)
    height = st.number_input("Height (m)", min_value=0.5, max_value=2.5, value=1.70)
    income_lpa = st.number_input("Annual Income (LPA)", min_value=0.1, value=5.0)
    
    # Categorical inputs
    smoker = st.selectbox("Are you a smoker?", [True, False])
    city = st.text_input("City", value="Mumbai")
    occupation = st.selectbox("Occupation", [
        "Government Job", "Private Job", "Business Owner", 
        "Freelancer", "Student", "Retired"
    ])
    
    # Submit button
    submitted = st.form_submit_button("Predict Premium Category")
    
    if submitted:
        # Prepare data for API
        user_data = {
            "age": age,
            "weight": weight,
            "height": height,
            "income_lpa": income_lpa,
            "smoker": smoker,
            "city": city,
            "occupation": occupation
        }
        
        try:
            # Make API request
            response = requests.post(PREDICT_ENDPOINT, json=user_data)
            
            if response.status_code == 200:
                result = response.json()
                predicted_category = result["predicted_category"]
                
                # Display result with styling
                if predicted_category == "Low":
                    st.success(f"🟢 Predicted Insurance Premium: {predicted_category}")
                elif predicted_category == "Medium":
                    st.warning(f"🟡 Predicted Insurance Premium: {predicted_category}")
                else:
                    st.error(f"🔴 Predicted Insurance Premium: {predicted_category}")
            else:
                st.error("Error occurred while making prediction")
                
        except Exception as e:
            st.error(f"Connection error: {str(e)}")
```

### 🚀 Running the Frontend

```bash
# Install Streamlit
pip install streamlit requests

# Run the application
streamlit run frontend.py
```

### 🔄 Complete User Journey

1. **User Access**: Opens Streamlit app in browser
2. **Data Entry**: Fills form with personal information
3. **Submission**: Clicks "Predict Premium Category"
4. **API Call**: Frontend sends POST request to FastAPI
5. **Processing**: API validates data and runs ML inference
6. **Response**: API returns prediction as JSON
7