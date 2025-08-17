# Ecotrack API Documentation

## Overview

Ecotrack is a Flask-based REST API for tracking water and electricity consumption, managing user accounts, and providing analytics for environmental impact. The API uses JSON for data exchange and session-based authentication.

## Base URL
```
http://localhost:5000
```

## Authentication

The API uses session-based authentication. Users must login to access most endpoints. Authentication is maintained through Flask sessions.

## Response Format

All API responses are in JSON format with the following structure:
- Success responses: `{"success": true}` with HTTP 200
- Error responses: `{"success": false}` or `{"success": "error message"}` with HTTP 401

---

## Endpoints

### Authentication Endpoints

#### Register User
**POST** `/register`

Creates a new user account with initial mock data for billing and consumption tracking.

**Request Body:**
```json
{
    "phone_no": "string",
    "password": "string", 
    "firstname": "string",
    "lastname": "string"
}
```

**Response:**
```json
{
    "success": true
}
```

**Error Cases:**
- Phone number already exists: `{"success": "Phone number already exists!"}` (401)

**Example:**
```bash
curl -X POST http://localhost:5000/register \
  -H "Content-Type: application/json" \
  -d '{
    "phone_no": "1234567890",
    "password": "mypassword",
    "firstname": "John",
    "lastname": "Doe"
  }'
```

#### Login
**POST** `/login`

Authenticates a user and creates a session.

**Request Body:**
```json
{
    "phone_no": "string",
    "password": "string"
}
```

**Response:**
```json
{
    "success": true
}
```

**Error Cases:**
- Invalid credentials: `{"success": "user is None"}` (401)

**Example:**
```bash
curl -X POST http://localhost:5000/login \
  -H "Content-Type: application/json" \
  -d '{
    "phone_no": "1234567890",
    "password": "mypassword"
  }'
```

#### Logout
**GET** `/logout`

Ends the user session.

**Response:**
```json
{
    "success": true
}
```

**Example:**
```bash
curl -X GET http://localhost:5000/logout
```

### User Management Endpoints

#### Update User Details
**POST** `/updateuserdetails`

Updates user profile information including utility account details.

**Authentication Required:** Yes

**Request Body:**
```json
{
    "water_consumer_no": "string",
    "water_board": "string",
    "electricity_consumer_no": "string", 
    "electricity_board": "string",
    "Number_of_people": "integer"
}
```

**Response:**
```json
{
    "success": true
}
```

**Example:**
```bash
curl -X POST http://localhost:5000/updateuserdetails \
  -H "Content-Type: application/json" \
  -d '{
    "water_consumer_no": "WB123456",
    "water_board": "BWSSB",
    "electricity_consumer_no": "EB789012",
    "electricity_board": "BESCOM",
    "Number_of_people": 4
  }'
```

#### Update Appliance Details
**POST** `/updateapplaincedetails`

Updates the count of household appliances for consumption estimation.

**Authentication Required:** Yes

**Request Body:**
```json
{
    "AC": "integer",
    "TV": "integer", 
    "Refrigerator": "integer",
    "Washing_machine": "integer",
    "Microwave": "integer",
    "Fans": "integer",
    "Lights": "integer",
    "Heater": "integer",
    "Iron_box": "integer"
}
```

**Response:**
```json
{
    "success": true
}
```

**Example:**
```bash
curl -X POST http://localhost:5000/updateapplaincedetails \
  -H "Content-Type: application/json" \
  -d '{
    "AC": 2,
    "TV": 3,
    "Refrigerator": 1,
    "Washing_machine": 1,
    "Microwave": 1,
    "Fans": 8,
    "Lights": 15,
    "Heater": 1,
    "Iron_box": 1
  }'
```

#### Set Goal
**POST** `/goal`

Updates the user's consumption reduction goal.

**Authentication Required:** Yes

**Request Body:**
```json
{
    "goal": "number"
}
```

**Response:**
```json
{
    "success": true
}
```

**Example:**
```bash
curl -X POST http://localhost:5000/goal \
  -H "Content-Type: application/json" \
  -d '{
    "goal": 300
  }'
```

### Data Retrieval Endpoints

#### Get User Details
**GET** `/userdetails`

Retrieves user profile information and appliance details.

**Authentication Required:** Yes

**Response:**
```json
{
    "Applainces": [
        {
            "name": "Lights",
            "count": 15
        },
        {
            "name": "Fans", 
            "count": 8
        }
        // ... other appliances
    ],
    "details": {
        "phone_no": "1234567890",
        "fullname": "John Doe",
        "address": "string",
        "electricity_consumer_no": "EB789012",
        "water_consumer_no": "WB123456",
        "lat": "number",
        "lng": "number",
        "electricity_board": "BESCOM",
        "water_board": "BWSSB",
        "Number_of_people": 4
    }
}
```

**Example:**
```bash
curl -X GET http://localhost:5000/userdetails
```

#### Get Bill Details
**GET** `/billdetails`

Retrieves billing history for the last 3 periods.

**Authentication Required:** Yes

**Response:**
```json
[
    {
        "electricity_consumer_no": "EB789012",
        "water_consumer_no": "WB123456",
        "electricity_board": "BESCOM",
        "water_board": "BWSSB",
        "water_date": "1-12-2023",
        "electricity_date": "1-12-2023",
        "electricity_bill": 1250.5,
        "water_bill": 720.0,
        "electricity_consumption": 350.0,
        "water_consumption": 18000.0,
        "water_payment_status": false,
        "electricity_payment_status": true
    }
    // ... 2 more billing periods
]
```

**Example:**
```bash
curl -X GET http://localhost:5000/billdetails
```

#### Get Points Details
**GET** `/pointsdetails`

Retrieves user's achievement, redeemed, and contribution points.

**Authentication Required:** Yes

**Response:**
```json
{
    "Achievement_points": 150,
    "Redeemed_points": 50,
    "Contributed_points": 200
}
```

**Example:**
```bash
curl -X GET http://localhost:5000/pointsdetails
```

#### Get Goal Details
**GET** `/goaldetails`

Retrieves the user's current consumption goal.

**Authentication Required:** Yes

**Response:**
```json
{
    "goal": 300
}
```

**Example:**
```bash
curl -X GET http://localhost:5000/goaldetails
```

#### Get Leaderboard Details
**GET** `/leaderdetails`

Retrieves leaderboard data for all users with their consumption and points.

**Authentication Required:** Yes

**Response:**
```json
[
    {
        "phone_no": "1234567890",
        "username": "John Doe",
        "achievement_points": 150,
        "water_consumption": 18000.0,
        "electricity_consumption": 350.0
    }
    // ... other users
]
```

**Example:**
```bash
curl -X GET http://localhost:5000/leaderdetails
```

#### Get Hourly Details
**GET** `/hourlydetails`

Retrieves daily consumption data broken down by morning and afternoon periods.

**Authentication Required:** Yes

**Response:**
```json
[
    {
        "water_consumption_morning": 175.0,
        "water_consumption_afternoon": 160.0,
        "electricity_consumption_morning": 8.75,
        "electricity_consumption_afternoon": 11.67,
        "date": "25-12-2023"
    }
    // ... other days (up to 7 days)
]
```

**Example:**
```bash
curl -X GET http://localhost:5000/hourlydetails
```

### Comparison Endpoints

#### Electricity Bill Comparison
**GET** `/electricitybillcomparison`

Compares current and previous month's electricity consumption and bills.

**Authentication Required:** Yes

**Response:**
```json
{
    "electricity_date_1": "1-12-2023",
    "electricity_bill_1": 1250.5,
    "electricity_consumption_1": 350.0,
    "electricity_date_2": "1-11-2023", 
    "electricity_bill_2": 1312.75,
    "electricity_consumption_2": 367.5,
    "electricity_consumption_comparison": -17.5,
    "electricity_comparison_average": -4.76
}
```

**Fields Explanation:**
- `electricity_consumption_comparison`: Difference between current and previous month (negative = reduction)
- `electricity_comparison_average`: Percentage change from previous month

**Example:**
```bash
curl -X GET http://localhost:5000/electricitybillcomparison
```

#### Water Bill Comparison
**GET** `/waterbillcomparison`

Compares current and previous period's water consumption and bills.

**Authentication Required:** Yes

**Response:**
```json
{
    "water_date_1": "1-10-2023",
    "water_bill_1": 720.0,
    "water_consumption_1": 18000.0,
    "water_date_2": "1-4-2023",
    "water_bill_2": 756.0,
    "water_consumption_2": 18900.0,
    "water_consumption_comparison": -900.0,
    "water_comparison_average": -4.76
}
```

**Fields Explanation:**
- `water_consumption_comparison`: Difference between current and previous period (negative = reduction)
- `water_comparison_average`: Percentage change from previous period

**Example:**
```bash
curl -X GET http://localhost:5000/waterbillcomparison
```

---

## Error Handling

### Common Error Responses

#### Authentication Required (401)
```json
{
    "success": false
}
```

#### Invalid Input/Method (401)
```json
{
    "success": false
}
```

#### Specific Error Messages
```json
{
    "success": "Phone number already exists!"
}
```

---

## Rate Limiting

Currently, no rate limiting is implemented. Consider implementing rate limiting for production use.

## CORS

CORS is enabled for all origins using Flask-CORS.

## Database

The API uses PostgreSQL as the primary database with SQLAlchemy ORM for data management.

---

## Usage Examples

### Complete User Registration Flow

```bash
# 1. Register a new user
curl -X POST http://localhost:5000/register \
  -H "Content-Type: application/json" \
  -d '{
    "phone_no": "9876543210",
    "password": "securepass123",
    "firstname": "Jane",
    "lastname": "Smith"
  }'

# 2. Login with the new user
curl -X POST http://localhost:5000/login \
  -H "Content-Type: application/json" \
  -c cookies.txt \
  -d '{
    "phone_no": "9876543210", 
    "password": "securepass123"
  }'

# 3. Update user details (using saved cookies)
curl -X POST http://localhost:5000/updateuserdetails \
  -H "Content-Type: application/json" \
  -b cookies.txt \
  -d '{
    "water_consumer_no": "WB654321",
    "water_board": "BWSSB",
    "electricity_consumer_no": "EB987654",
    "electricity_board": "BESCOM",
    "Number_of_people": 3
  }'

# 4. Get user details
curl -X GET http://localhost:5000/userdetails -b cookies.txt

# 5. Set consumption goal
curl -X POST http://localhost:5000/goal \
  -H "Content-Type: application/json" \
  -b cookies.txt \
  -d '{
    "goal": 280
  }'

# 6. View consumption comparison
curl -X GET http://localhost:5000/electricitybillcomparison -b cookies.txt
```

### Data Analysis Workflow

```bash
# Get hourly consumption data
curl -X GET http://localhost:5000/hourlydetails -b cookies.txt

# Compare electricity bills
curl -X GET http://localhost:5000/electricitybillcomparison -b cookies.txt

# Compare water bills  
curl -X GET http://localhost:5000/waterbillcomparison -b cookies.txt

# Check leaderboard position
curl -X GET http://localhost:5000/leaderdetails -b cookies.txt

# View achievement points
curl -X GET http://localhost:5000/pointsdetails -b cookies.txt
```