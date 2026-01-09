---
title: "📑 Table of Contents"
layout: default
nav_order: 4
parent: "Lecture Notes"
description: "Lecture notes: 📑 Table of Contents"
last_modified_date: 2026-01-09
source_transcript: "004_Path_&_Query_Params_in_FastAPI_Video_4_CampusX"
generated_by: "NoteyBoy"
---

# 📑 Table of Contents

- [📖 Introduction/Overview](#-introductionoverview)
- [🛤️ Path Parameters](#️-path-parameters)
- [🔧 Implementing Path Parameters](#-implementing-path-parameters)
- [⚡ Improving Code with Path Function](#-improving-code-with-path-function)
- [📊 HTTP Status Codes](#-http-status-codes)
- [🚨 HTTP Exception Handling](#-http-exception-handling)
- [🔍 Query Parameters](#-query-parameters)
- [🎯 Implementing Query Parameters](#-implementing-query-parameters)
- [⚡ Quick Reference](#-quick-reference)
- [📊 Summary Table](#-summary-table)
- [🎯 Key Takeaways](#-key-takeaways)
- [💡 Pro Tips](#-pro-tips)

---

# 📖 Introduction/Overview

This video continues the FastAPI playlist, focusing on two fundamental concepts in API development: **Path Parameters** and **Query Parameters**. These are essential tools for building dynamic, flexible APIs that can handle different types of data requests.

## 🎯 What You'll Learn
- Understanding Path Parameters and their use cases
- Implementing Query Parameters for filtering and sorting
- HTTP Status Codes and proper error handling
- Best practices for API endpoint design
- Code improvement techniques using FastAPI utilities

---

# 🛤️ Path Parameters

## 📝 Definition

> **Path Parameters are dynamic segments of a URL path used to identify a specific resource.**

## 🤔 Why Path Parameters Matter

Path parameters solve a crucial problem in API design. Instead of creating separate endpoints for each resource, you can create one dynamic endpoint that handles multiple resources based on the parameter value.

### 🔄 Before vs After Example

**Before (Static):**
```
localhost:8000/view  # Shows all patients
```

**After (Dynamic):**
```
localhost:8000/patient/P001  # Shows specific patient
localhost:8000/patient/P002  # Shows different patient
localhost:8000/patient/P003  # Shows another patient
```

## 🎯 Use Cases for Path Parameters

| Operation | Example URL | Purpose |
|-----------|-------------|---------|
| **Retrieve** | `GET /patient/P001` | Get specific patient data |
| **Update** | `PUT /patient/P001` | Update specific patient |
| **Delete** | `DELETE /patient/P001` | Delete specific patient |

## 💡 Key Characteristics

- **Dynamic**: Values can change based on client needs
- **Required**: Must be provided for the endpoint to work
- **Resource Identification**: Used to locate specific resources on the server

---

# 🔧 Implementing Path Parameters

## 📋 Basic Implementation

```python
from fastapi import FastAPI
from utils import load_data

app = FastAPI()

@app.get("/patient/{patient_id}")
def view_patient(patient_id: str):
    # Load all patient data
    data = load_data()
    
    # Check if patient exists
    if patient_id in data:
        return data[patient_id]
    else:
        return {"error": "Patient not found"}
```

## 🔍 Code Breakdown

### 1. Route Definition
```python
@app.get("/patient/{patient_id}")
```
- `{patient_id}` is the dynamic segment
- Curly braces indicate a path parameter
- The name inside braces becomes the variable name

### 2. Function Parameter
```python
def view_patient(patient_id: str):
```
- Parameter name must match the route parameter
- Type annotation (`str`) provides validation
- FastAPI automatically extracts the value from URL

### 3. Logic Implementation
```python
data = load_data()  # Load all data
if patient_id in data:  # Check existence
    return data[patient_id]  # Return specific patient
else:
    return {"error": "Patient not found"}  # Handle not found
```

## 🧪 Testing the Endpoint

### Direct URL Testing
```
http://localhost:8000/patient/P001  # Returns patient P001 data
http://localhost:8000/patient/P003  # Returns patient P003 data
http://localhost:8000/patient/P005  # Returns patient P005 data
```

### Interactive Documentation
- Navigate to `http://localhost:8000/docs`
- Find the `/patient/{patient_id}` endpoint
- Click "Try it out"
- Enter patient ID (e.g., P001)
- Execute to see results

---

# ⚡ Improving Code with Path Function

## 🎯 Why Use Path Function?

The `Path` function enhances path parameters by providing:
- Better documentation
- Data validation
- Examples for clients
- Metadata for API docs

## 📚 Path Function Capabilities

```python
from fastapi import Path

Path(
    ...,  # Required (three dots)
    title="Patient ID",
    description="ID of the patient in the DB",
    example="P001",
    min_length=1,
    max_length=10,
    regex="^P[0-9]{3}$"  # Pattern validation
)
```

## 🔧 Implementation Example

```python
from fastapi import FastAPI, Path

@app.get("/patient/{patient_id}")
def view_patient(
    patient_id: str = Path(
        ...,  # Required parameter
        description="ID of the patient in the DB",
        example="P001"
    )
):
    data = load_data()
    if patient_id in data:
        return data[patient_id]
    else:
        return {"error": "Patient not found"}
```

## 📈 Benefits of Using Path Function

| Feature | Benefit |
|---------|---------|
| **Documentation** | Auto-generates clear API docs |
| **Examples** | Helps clients understand expected format |
| **Validation** | Ensures data meets requirements |
| **Error Messages** | Provides clear feedback on invalid input |

---

# 📊 HTTP Status Codes

## 📖 Definition

> **HTTP Status Codes are three-digit numbers returned by a web server to indicate the result of a client's request.**

## 🎯 Why Status Codes Matter

Status codes provide crucial information about request processing:
- **Success**: Request processed correctly
- **Client Error**: Problem with the request
- **Server Error**: Problem on server side
- **Redirection**: Further action needed

## 📋 Status Code Categories

| Range | Category | Meaning |
|-------|----------|---------|
| **2xx** | Success | Request successful |
| **3xx** | Redirection | Further action required |
| **4xx** | Client Error | Problem with request |
| **5xx** | Server Error | Problem on server |

## 🔢 Common Status Codes

### ✅ Success Codes (2xx)
| Code | Name | When to Use |
|------|------|-------------|
| **200** | OK | Successful GET request |
| **201** | Created | Resource successfully created |
| **204** | No Content | Successful DELETE (no response body) |

### ❌ Client Error Codes (4xx)
| Code | Name | When to Use |
|------|------|-------------|
| **400** | Bad Request | Invalid request format/data |
| **401** | Unauthorized | Authentication required |
| **403** | Forbidden | Access denied (even if authenticated) |
| **404** | Not Found | Resource doesn't exist |

### 🔥 Server Error Codes (5xx)
| Code | Name | When to Use |
|------|------|-------------|
| **500** | Internal Server Error | Unexpected server problem |
| **502** | Bad Gateway | Communication problem |
| **503** | Service Unavailable | Server overloaded/down |

---

# 🚨 HTTP Exception Handling

## ❗ The Problem

Current implementation returns status 200 even when patient not found:

```python
# Problem: Always returns 200 status
if patient_id in data:
    return data[patient_id]  # 200 OK
else:
    return {"error": "Patient not found"}  # Still 200 OK!
```

## ✅ The Solution: HTTPException

```python
from fastapi import HTTPException

@app.get("/patient/{patient_id}")
def view_patient(patient_id: str):
    data = load_data()
    
    if patient_id in data:
        return data[patient_id]
    else:
        raise HTTPException(
            status_code=404,
            detail="Patient not found"
        )
```

## 🔍 HTTPException Benefits

| Feature | Benefit |
|---------|---------|
| **Proper Status Codes** | Returns appropriate HTTP status |
| **Consistent Error Format** | Standardized error responses |
| **Client Clarity** | Clear indication of what went wrong |
| **API Standards** | Follows REST API conventions |

## 🧪 Testing Error Handling

```python
# Test with invalid patient ID
GET /patient/P999

# Response:
{
    "detail": "Patient not found"
}
# Status: 404 Not Found
```

---

# 🔍 Query Parameters

## 📖 Definition

> **Query Parameters are optional key-value pairs appended to the end of a URL used to pass additional data to the server in an HTTP request. They are typically employed for operations like filtering, sorting, searching, and pagination without altering the endpoint path itself.**

## 🎯 Why Query Parameters?

Query parameters solve the problem of adding functionality to existing endpoints without creating new routes. They provide flexibility for:
- **Filtering** data based on criteria
- **Sorting** results in different orders
- **Searching** through resources
- **Pagination** for large datasets

## 🔗 URL Structure with Query Parameters

```
http://localhost:8000/sort?sort_by=weight&order=descending
```

**Breakdown:**
- `http://localhost:8000/sort` - Base endpoint
- `?` - Query parameter separator
- `sort_by=weight` - First parameter (key=value)
- `&` - Parameter separator
- `order=descending` - Second parameter

## 📊 Query vs Path Parameters

| Aspect | Path Parameters | Query Parameters |
|--------|----------------|------------------|
| **Required** | Yes | Optional |
| **Purpose** | Resource identification | Data modification |
| **URL Position** | In path | After `?` |
| **Use Cases** | CRUD operations | Filtering, sorting |

---

# 🎯 Implementing Query Parameters

## 📋 Scenario: Patient Sorting Endpoint

Create an endpoint that allows clients to sort patients by different criteria with optional ordering.

### Requirements:
- **sort_by**: weight, height, or BMI (required)
- **order**: ascending or descending (optional, default: ascending)

## 🔧 Basic Implementation

```python
from fastapi import FastAPI, Query, HTTPException

@app.get("/sort")
def sort_patients(
    sort_by: str = Query(..., description="Sort on the basis of height, weight and BMI"),
    order: str = Query("ascending", description="Sort in ascending and descending order")
):
    # Implementation here
    pass
```

## 🛡️ Adding Validation

```python
@app.get("/sort")
def sort_patients(
    sort_by: str = Query(..., description="Sort on the basis of height, weight and BMI"),
    order: str = Query("ascending", description="Sort in ascending and descending order")
):
    # Validate sort_by field
    valid_fields = ["height", "weight", "BMI"]
    if sort_by not in valid_fields:
        raise HTTPException(
            status_code=400,
            detail=f"Invalid field. Select from {valid_fields}"
        )
    
    # Validate order
    if order not in ["ascending", "descending"]:
        raise HTTPException(
            status_code=400,
            detail="Invalid order. Select between ascending and descending"
        )
    
    # Load and sort data
    data = load_data()
    sort_order = True if order == "descending" else False
    sorted_data = sorted(data.values(), key=lambda x: x[sort_by], reverse=sort_order)
    
    return sorted_data
```

## 🔍 Complete Implementation Breakdown

### 1. Query Function Usage
```python
from fastapi import Query

sort_by: str = Query(
    ...,  # Required (three dots)
    description="Sort on the basis of height, weight and BMI"
)

order: str = Query(
    "ascending",  # Default value (makes it optional)
    description="Sort in ascending and descending order"
)
```

### 2. Validation Logic
```python
# Field validation
valid_fields = ["height", "weight", "BMI"]
if sort_by not in valid_fields:
    raise HTTPException(status_code=400, detail=f"Invalid field. Select from {valid_fields}")

# Order validation
if order not in ["ascending", "descending"]:
    raise HTTPException(status_code=400, detail="Invalid order. Select between ascending and descending")
```

### 3. Sorting Implementation
```python
# Load data
data = load_data()

# Determine sort order
sort_order = True if order == "descending" else False

# Sort data
sorted_data = sorted(
    data.values(),  # Get all patient records
    key=lambda x: x[sort_by],  # Sort by specified field
    reverse=sort_order  # Apply order
)

return sorted_data
```

## 🧪 Testing Query Parameters

### Valid Requests
```bash
# Sort by BMI in descending order
GET /sort?sort_by=BMI&order=descending

# Sort by weight (ascending by default)
GET /sort?sort_by=weight

# Sort by height in ascending order
GET /sort?sort_by=height&order=ascending
```

### Invalid Requests
```bash
# Invalid field
GET /sort?sort_by=age&order=ascending
# Response: 400 Bad Request - "Invalid field. Select from ['height', 'weight', 'BMI']"

# Invalid order
GET /sort?sort_by=weight&order=random
# Response: 400 Bad Request - "Invalid order. Select between ascending and descending"
```

---

# ⚡ Quick Reference

## 🛤️ Path Parameters Syntax

```python
from fastapi import FastAPI, Path

@app.get("/resource/{resource_id}")
def get_resource(
    resource_id: str = Path(
        ...,
        description="Resource identifier",
        example="RES001"
    )
):
    pass
```

## 🔍 Query Parameters Syntax

```python
from fastapi import Query

@app.get("/resources")
def list_resources(
    limit: int = Query(10, description="Number of items to return"),
    offset: int = Query(0, description="Number of items to skip"),
    search: str = Query(None, description="Search term")
):
    pass
```

## 🚨 HTTP Exception Syntax

```python
from fastapi import HTTPException

raise HTTPException(
    status_code=404,
    detail="Resource not found"
)
```

## 📊 Common Status Codes

| Code | Usage |
|------|-------|
| `200` | Successful GET |
| `400` | Bad Request |
| `404` | Not Found |
| `500` | Server Error |

---

# 📊 Summary Table

| Concept | Purpose | Required | Example |
|---------|---------|----------|---------|
| **Path Parameters** | Resource identification | Yes | `/patient/{patient_id}` |
| **Query Parameters** | Data filtering/sorting | No | `?sort_by=weight&order=desc` |
| **Path Function** | Enhanced documentation | No | `Path(..., description="ID")` |
| **Query Function** | Enhanced validation | No | `Query("default", description="Sort")` |
| **HTTPException** | Proper error handling | No | `HTTPException(404, "Not found")` |

---

# 🎯 Key Takeaways

## 🛤️ Path Parameters
- **Use for**: Resource identification (GET, PUT, DELETE operations)
- **Always required** - endpoint won't work without them
- **Part of URL path** - dynamic segments in curly braces
- **Best for**: Accessing specific resources

## 🔍 Query Parameters  
- **Use for**: Filtering, sorting, searching, pagination
- **Optional** - endpoint works with or without them
- **After `?` in URL** - key=value pairs separated by `&`
- **Best for**: Mo