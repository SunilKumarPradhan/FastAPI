---
title: "Pydantic Complete Guide: Data Validation and Modeling in Python"
layout: default
nav_order: 5
parent: "Lecture Notes"
description: "Lecture notes: Pydantic Complete Guide: Data Validation and Modeling in Python"
last_modified_date: 2026-01-09
source_transcript: "005_Pydantic_Crash_Course_Data_Validation_in_Python_CampusX"
generated_by: "NoteyBoy"
---

# Pydantic Complete Guide: Data Validation and Modeling in Python

## Table of Contents
1. [Introduction to Pydantic](#introduction)
2. [Core Problems and Solutions](#core-problems)
3. [Building Pydantic Models](#building-models)
4. [Data Validation Techniques](#validation-techniques)
5. [Advanced Validation Features](#advanced-validation)
6. [Model Organization and Export](#model-organization)
7. [Summary and Best Practices](#summary)

---

## Introduction to Pydantic {#introduction}

### What is Pydantic?
**Pydantic** is a crucial Python library for **data validation** that solves fundamental problems in Python's dynamic typing system. It's essential for building production-grade applications where type safety and data integrity are critical.

### Why Pydantic is Important
Pydantic is extensively used in:
- **FastAPI** for building robust APIs
- **YAML config files** validation
- **Data Science ML pipelines** for data integrity
- **Production-grade code** development

---

## Core Problems and Solutions {#core-problems}

### Problem 1: Type Validation

**The Challenge:**
```python
def insert_patient_data(name, age):
    print(name)
    print(age)
    print("Inserted into database")
```

**Issues with Python's Dynamic Typing:**
- No enforcement of data types
- Runtime errors when wrong types are passed
- Data corruption in databases

**Manual Solution (Not Scalable):**
```python
def insert_patient_data(name: str, age: int):
    if type(name) == str and type(age) == int:
        # Database insertion logic
        pass
    else:
        raise TypeError("Incorrect data type")
```

### Problem 2: Data Validation
Beyond type checking, applications need:
- Business logic validation (age cannot be negative)
- Format validation (email, phone numbers)
- Custom constraints and rules

### The Pydantic Solution: 3-Step Process

#### Step 1: Build a Pydantic Model
```python
from pydantic import BaseModel

class Patient(BaseModel):
    name: str
    age: int
```

#### Step 2: Instantiate Model with Raw Data
```python
patient_info = {
    "name": "Nitish",
    "age": 30
}

patient_one = Patient(**patient_info)
```

#### Step 3: Use Validated Object in Functions
```python
def insert_patient_data(patient: Patient):
    print(patient.name)
    print(patient.age)
    print("Inserted into database")

insert_patient_data(patient_one)
```

---

## Building Pydantic Models {#building-models}

### Import Required Modules
```python
from pydantic import BaseModel
from typing import List, Dict, Optional
```

### Complex Model Example
```python
class Patient(BaseModel):
    name: str
    age: int
    weight: float
    married: bool
    allergies: List[str]
    contact_details: Dict[str, str]
```

### Usage Example
```python
patient_info = {
    "name": "Nitish",
    "age": 30,
    "weight": 75.2,
    "married": True,
    "allergies": ["pollen", "dust"],
    "contact_details": {
        "email": "abc@gmail.com",
        "phone": "1234567890"
    }
}
```

### Required vs Optional Fields

**Default Behavior:**
- All fields in Pydantic models are **required** by default
- Missing any field will raise validation error

**Making Fields Optional:**
```python
from typing import Optional

class Patient(BaseModel):
    name: str
    age: int
    allergies: Optional[List[str]] = None  # Optional with default None
    married: bool = False  # Optional with default False
```

---

## Data Validation Techniques {#validation-techniques}

### 1. Custom Data Types from Pydantic

**Email Validation:**
```python
from pydantic import BaseModel, EmailStr

class Patient(BaseModel):
    name: str
    email: EmailStr  # Automatically validates email format
```

**URL Validation:**
```python
from pydantic import AnyUrl

class Patient(BaseModel):
    name: str
    linkedin_url: AnyUrl  # Validates URL format
```

### 2. Field Function for Custom Validation

**Import Field:**
```python
from pydantic import BaseModel, Field
```

**Numeric Constraints:**
```python
class Patient(BaseModel):
    age: int = Field(gt=0, lt=120)  # Age between 0 and 120
    weight: float = Field(gt=0)     # Weight greater than 0
```

**String Constraints:**
```python
class Patient(BaseModel):
    name: str = Field(max_length=50)  # Name max 50 characters
```

**List Constraints:**
```python
class Patient(BaseModel):
    allergies: List[str] = Field(max_length=5)  # Max 5 allergies
```

### 3. Field Function for Metadata

**Using Annotated for Metadata:**
```python
from typing import Annotated
from pydantic import BaseModel, Field

class Patient(BaseModel):
    name: Annotated[str, Field(
        max_length=50,
        title="Name of the Patient",
        description="Give the name of the patient in less than 50 characters",
        examples=["Nitish", "Amit"]
    )]
```

**Field Function Capabilities:**
1. **Custom Data Validation** - Set constraints
2. **Metadata** - Add title, description, examples
3. **Default Values** - Set default values
4. **Type Coercion Control** - Use `strict=True` to disable automatic type conversion

---

## Advanced Validation Features {#advanced-validation}

### Field Validators for Complex Business Logic

**When to Use Field Validators:**
- For business-specific validation requirements
- When built-in validators aren't sufficient
- For custom transformations

**Example: Email Domain Validation**
```python
from pydantic import BaseModel, field_validator

class Patient(BaseModel):
    name: str
    email: str
    
    @field_validator('email')
    @classmethod
    def email_validator(cls, value):
        valid_domains = ['hdfc.com', 'icici.com']
        domain_name = value.split('@')[-1]
        
        if domain_name not in valid_domains:
            raise ValueError("Not a valid domain")
        
        return value
```

### Field Validator Modes

**Before vs After Mode:**
- **After Mode (default)**: Validator runs after type coercion
- **Before Mode**: Validator runs before type coercion

```python
@field_validator('age', mode='after')  # Explicitly set after mode
@classmethod
def validate_age(cls, value):
    if value < 0 or value > 100:  # Now works with integer
        raise ValueError("Age must be between 0 and 100")
    return value
```

### Model Validator

**When to Use Model Validator:**
- For validation that depends on **multiple fields**
- Field validator only works on single fields
- Model validator operates on the entire Pydantic model

**Example: Emergency Contact Validation**
```python
from pydantic import BaseModel, model_validator

class Patient(BaseModel):
    name: str
    age: int
    contact_details: dict
    
    @model_validator(mode='after')
    def validate_emergency_contact(cls, model):
        if model.age > 60 and 'emergency' not in model.contact_details:
            raise ValueError("Patients older than 60 must have an emergency contact")
        return model
```

### Computed Fields

**Definition:**
- Fields whose values are **not provided by user**
- Values are **calculated dynamically** using other fields
- Automatically computed based on existing field values

**Example: BMI Calculation**
```python
from pydantic import BaseModel, computed_field

class Patient(BaseModel):
    name: str
    weight: float  # in kg
    height: float  # in meters
    
    @computed_field
    @property
    def bmi(self) -> float:
        bmi_value = self.weight / (self.height ** 2)
        return round(bmi_value, 2)
```

**Usage:**
```python
patient = Patient(name="John", weight=70, height=1.72)
print(patient.bmi)  # Automatically calculated: 23.66
```

---

## Model Organization and Export {#model-organization}

### Nested Models

**Definition:**
- Using one Pydantic model as a field inside another model
- Helps organize complex data structures
- Enables better data hierarchy management

**Example: Address as Nested Model**
```python
from pydantic import BaseModel

class Address(BaseModel):
    city: str
    state: str
    pin_code: str

class Patient(BaseModel):
    name: str
    gender: str
    age: int
    address: Address  # Nested model
```

**Usage:**
```python
# Create address object
address_dict = {
    "city": "Gurgaon",
    "state": "Haryana", 
    "pin_code": "122001"
}
address1 = Address(**address_dict)

# Create patient with nested address
patient_dict = {
    "name": "Nitish",
    "gender": "Male",
    "age": 30,
    "address": address1
}
patient1 = Patient(**patient_dict)

# Access nested data
print(patient1.name)                    # Nitish
print(patient1.address.city)            # Gurgaon
print(patient1.address.pin_code)        # 122001
```

### Exporting Pydantic Models

**Export to Python Dictionary:**
```python
# Convert to dictionary
temp = patient1.model_dump()
print(type(temp))  # <class 'dict'>
```

**Export to JSON:**
```python
# Convert to JSON string
json_data = patient1.model_dump_json()
print(type(json_data))  # <class 'str'>
```

**Export Control Options:**

**Include Specific Fields:**
```python
# Export only name field
patient1.model_dump(include=['name'])

# Export multiple fields
patient1.model_dump(include=['name', 'age'])
```

**Exclude Specific Fields:**
```python
# Exclude name and gender
patient1.model_dump(exclude=['name', 'gender'])

# Exclude nested field (state from address)
patient1.model_dump(exclude={'address': ['state']})
```

**Exclude Unset Fields:**
```python
class Patient(BaseModel):
    name: str
    gender: str = "Male"  # Default value
    age: int

# Create patient without explicitly setting gender
patient_dict = {"name": "John", "age": 30}
patient = Patient(**patient_dict)

# Export excluding unset fields
patient.model_dump(exclude_unset=True)  # gender won't appear
```

---

## Summary and Best Practices {#summary}

### Key Concepts Mastered

1. **Problem Solving**: Pydantic addresses Python's dynamic typing challenges
2. **Model Creation**: Three-step process for data validation
3. **Validation Layers**: Built-in types, Field constraints, Custom validators
4. **Advanced Features**: Model validators, computed fields, nested models
5. **Data Export**: Flexible serialization with control options

### When to Use Each Feature

| Feature | Use Case |
|---------|----------|
| **Field Validator** | Single field validation and transformation |
| **Model Validator** | Cross-field validation logic |
| **Computed Fields** | Calculated values from existing fields |
| **Nested Models** | Complex, hierarchical data structures |
| **Export Methods** | API responses, logging, debugging |

### Benefits of Using Pydantic

1. **Scalable Solution**: Write validation once, use everywhere
2. **Production-Ready**: Handles complex business logic
3. **Type Safety**: Ensures data integrity at runtime
4. **Developer Experience**: Clear error messages and documentation
5. **Integration**: Works seamlessly with FastAPI and other frameworks

This comprehensive guide provides the foundation needed for using Pydantic effectively in real-world applications, from simple data validation to complex API development.