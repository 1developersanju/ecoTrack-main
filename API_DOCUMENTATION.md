# Ecotrack API Documentation

## Overview

Ecotrack is a Flask-based web application designed to help users track and manage their electricity and water consumption. The application provides comprehensive APIs for user management, consumption tracking, billing analysis, and environmental goal setting.

**Version:** 1.0.0  
**Base URL:** `/`  
**Framework:** Flask 3.0.0  
**Database:** PostgreSQL with SQLAlchemy ORM

## Table of Contents

1. [Authentication & Session Management](#authentication--session-management)
2. [User Management APIs](#user-management-apis)
3. [Consumption & Billing APIs](#consumption--billing-apis)
4. [Analytics & Comparison APIs](#analytics--comparison-apis)
5. [Database Models](#database-models)
6. [Utility Functions](#utility-functions)
7. [Error Handling](#error-handling)
8. [Usage Examples](#usage-examples)

---

## Authentication & Session Management

### Session Configuration
- **Secret Key:** `asdsdfsdfs13sdf_df%&`
- **Session Storage:** Flask session (server-side)
- **Authentication Method:** Phone number + password
- **Session Validation:** Required for all protected endpoints

### Protected Endpoints
All endpoints except `/register` and `/login` require an active session with a valid `phone_no`.

---

## User Management APIs

### 1. User Registration
**Endpoint:** `POST /register`  
**Description:** Creates a new user account with initial consumption data  
**Authentication:** Not required  

#### Request Body
```json
{
  "phone_no": "string (required)",
  "password": "string (required)",
  "firstname": "string (required)",
  "lastname": "string (required)"
}
```

#### Response
- **Success (200):** `{"success": true}`
- **Error (401):** `{"success": "Phone number already exists!"}`

#### Features
- Generates mock consumption data for the last 3 months
- Creates user profile, details, appliances, and billing records
- Sets initial environmental goals based on consumption patterns
- Generates 7 days of hourly consumption entries

#### Example Usage
```python
import requests

response = requests.post('/register', json={
    "phone_no": "+1234567890",
    "password": "securepassword",
    "firstname": "John",
    "lastname": "Doe"
})
```

### 2. User Login
**Endpoint:** `POST /login`  
**Description:** Authenticates user and creates session  
**Authentication:** Not required  

#### Request Body
```json
{
  "phone_no": "string (required)",
  "password": "string (required)"
}
```

#### Response
- **Success (200):** `{"success": true}`
- **Error (401):** `{"success": "user is None"}`

#### Example Usage
```python
response = requests.post('/login', json={
    "phone_no": "+1234567890",
    "password": "securepassword"
})
```

### 3. User Logout
**Endpoint:** `GET /logout`  
**Description:** Terminates user session  
**Authentication:** Required  

#### Response
- **Success (200):** `{"success": true}`

### 4. Update User Details
**Endpoint:** `POST /updateuserdetails`  
**Description:** Updates user profile information  
**Authentication:** Required  

#### Request Body
```json
{
  "water_consumer_no": "string",
  "water_board": "string",
  "electricity_consumer_no": "string",
  "electricity_board": "string",
  "Number_of_people": "integer"
}
```

#### Response
- **Success (200):** `{"success": true}`
- **Error (401):** `{"success": false}`

#### Features
- Updates user details in both `Details` and `Billing_details` tables
- Synchronizes consumer numbers across all billing records

### 5. Update Appliance Details
**Endpoint:** `POST /updateapplaincedetails`  
**Description:** Updates user's appliance inventory  
**Authentication:** Required  

#### Request Body
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

#### Response
- **Success (200):** `{"success": true}`
- **Error (401):** `{"success": false}`

### 6. Set Environmental Goal
**Endpoint:** `POST /goal`  
**Description:** Sets or updates user's consumption reduction goal  
**Authentication:** Required  

#### Request Body
```json
{
  "goal": "integer (consumption target in kWh)"
}
```

#### Response
- **Success (200):** `{"success": true}`
- **Error (401):** `{"success": false}`

---

## Consumption & Billing APIs

### 7. Get User Details
**Endpoint:** `GET /userdetails`  
**Description:** Retrieves comprehensive user profile and appliance information  
**Authentication:** Required  

#### Response
```json
{
  "Applainces": [
    {"name": "Lights", "count": 10},
    {"name": "Fans", "count": 3},
    {"name": "Heater", "count": 1},
    {"name": "Iron_box", "count": 1},
    {"name": "Television", "count": 2},
    {"name": "Washing_machine", "count": 1},
    {"name": "Ac", "count": 2},
    {"name": "Microwave", "count": 1},
    {"name": "Refrigerator", "count": 1}
  ],
  "details": {
    "phone_no": "string",
    "fullname": "string",
    "address": "string",
    "electricity_consumer_no": "string",
    "water_consumer_no": "string",
    "lat": "float",
    "lng": "float",
    "electricity_board": "string",
    "water_board": "string",
    "Number_of_people": "integer"
  }
}
```

### 8. Get Bill Details
**Endpoint:** `GET /billdetails`  
**Description:** Retrieves last 3 months of billing information  
**Authentication:** Required  

#### Response
```json
[
  {
    "electricity_consumer_no": "string",
    "water_consumer_no": "string",
    "electricity_board": "string",
    "water_board": "string",
    "water_date": "string (DD-MM-YYYY)",
    "electricity_date": "string (DD-MM-YYYY)",
    "electricity_bill": "float",
    "water_bill": "float",
    "electricity_consumption": "float (kWh)",
    "water_consumption": "float (liters)",
    "water_payment_status": "boolean",
    "electricity_payment_status": "boolean"
  }
]
```

### 9. Get Points Details
**Endpoint:** `GET /pointsdetails`  
**Description:** Retrieves user's achievement and contribution points  
**Authentication:** Required  

#### Response
```json
{
  "Achievement_points": "integer",
  "Redeemed_points": "integer",
  "Contributed_points": "integer"
}
```

### 10. Get Goal Details
**Endpoint:** `GET /goaldetails`  
**Description:** Retrieves user's current consumption reduction goal  
**Authentication:** Required  

#### Response
```json
{
  "goal": "integer (kWh)"
}
```

### 11. Get Hourly Details
**Endpoint:** `GET /hourlydetails`  
**Description:** Retrieves last 7 days of hourly consumption data  
**Authentication:** Required  

#### Response
```json
[
  {
    "water_consumption_morning": "float (liters)",
    "water_consumption_afternoon": "float (liters)",
    "electricity_consumption_morning": "float (kWh)",
    "electricity_consumption_afternoon": "float (kWh)",
    "date": "string (DD-MM-YYYY)"
  }
]
```

---

## Analytics & Comparison APIs

### 12. Get Leaderboard Details
**Endpoint:** `GET /leaderdetails`  
**Description:** Retrieves community leaderboard with consumption rankings  
**Authentication:** Required  

#### Response
```json
[
  {
    "phone_no": "string",
    "username": "string",
    "achievement_points": "integer",
    "water_consumption": "float",
    "electricity_consumption": "float"
  }
]
```

### 13. Electricity Bill Comparison
**Endpoint:** `GET /electricitybillcomparison`  
**Description:** Compares current month vs previous month electricity consumption  
**Authentication:** Required  

#### Response
```json
{
  "electricity_date_1": "string (DD-MM-YYYY)",
  "electricity_bill_1": "float",
  "electricity_consumption_1": "float (kWh)",
  "electricity_date_2": "string (DD-MM-YYYY)",
  "electricity_bill_2": "float",
  "electricity_consumption_2": "float (kWh)",
  "electricity_consumption_comparison": "float (difference)",
  "electricity_comparison_average": "float (percentage change)"
}
```

### 14. Water Bill Comparison
**Endpoint:** `GET /waterbillcomparison`  
**Description:** Compares current month vs previous month water consumption  
**Authentication:** Required  

#### Response
```json
{
  "water_date_1": "string (DD-MM-YYYY)",
  "water_bill_1": "float",
  "water_date_2": "string (DD-MM-YYYY)",
  "water_bill_2": "float",
  "water_consumption_1": "float (liters)",
  "water_consumption_2": "float (liters)",
  "water_consumption_comparison": "float (difference)",
  "water_comparison_average": "float (percentage change)"
}
```

---

## Database Models

### User Model
```python
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    phone_no = db.Column(db.String(80), unique=True, nullable=False)
    password = db.Column(db.String(120), nullable=False)
    fullname = db.Column(db.String(120), nullable=False)
    achievement_points = db.Column(db.Integer, nullable=False)
    goal = db.Column(db.Integer, nullable=False)
    contribution_points = db.Column(db.Integer, nullable=False)
    redeemed_points = db.Column(db.Integer, nullable=False)
```

### Details Model
```python
class Details(db.Model):
    phone_no = db.Column(db.String(80), unique=True, nullable=False, primary_key=True)
    username = db.Column(db.String(120), nullable=False)
    address = db.Column(db.String(200), nullable=True)
    lng = db.Column(db.Float, nullable=True)
    lat = db.Column(db.Float, nullable=True)
    water_consumer_no = db.Column(db.String(120), nullable=True)
    water_board = db.Column(db.String(10), nullable=True)
    electricity_consumer_no = db.Column(db.String(120), nullable=True)
    electricity_board = db.Column(db.String(10), nullable=True)
    Number_of_people = db.Column(db.Integer)
```

### Appliances Model
```python
class Appliances(db.Model):
    phone_no = db.Column(db.String(80), unique=True, nullable=False, primary_key=True)
    Television = db.Column(db.Integer, nullable=True)
    Refrigerator = db.Column(db.Integer, nullable=True)
    Microwave = db.Column(db.Integer, nullable=True)
    Washing_machine = db.Column(db.Integer, nullable=True)
    Air_conditioner = db.Column(db.Integer, nullable=True)
    Iron_box = db.Column(db.Integer, nullable=True)
    Heater = db.Column(db.Integer, nullable=True)
    Fans = db.Column(db.Integer, nullable=True)
    Lights = db.Column(db.Integer, nullable=True)
```

### Billing Details Model
```python
class Billing_details(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    phone_no = db.Column(db.String(80), nullable=False)
    water_consumer_no = db.Column(db.String(120), nullable=True)
    electricity_consumer_no = db.Column(db.String(120), nullable=True)
    electricity_consumption = db.Column(db.Float, nullable=True)
    electricity_bill = db.Column(db.Float, nullable=True)
    water_consumption = db.Column(db.Float, nullable=True)
    water_bill = db.Column(db.Float, nullable=True)
    water_billing_date = db.Column(db.String(10), nullable=True)
    water_board = db.Column(db.String(10), nullable=True)
    electricity_billing_date = db.Column(db.String(10), nullable=True)
    electricity_board = db.Column(db.String(10), nullable=True)
```

### Hourly Entries Model
```python
class Hourly_entries(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    phone_no = db.Column(db.String(80), nullable=False)
    water_consumption_morning = db.Column(db.Float, nullable=True)
    water_consumption_afternoon = db.Column(db.Float, nullable=True)
    electricity_consumption_morning = db.Column(db.Float, nullable=True)
    electricity_consumption_afternoon = db.Column(db.Float, nullable=True)
    date = db.Column(db.String(10), nullable=True)
```

---

## Utility Functions

### Electricity Bill Calculation
```python
def calculate_electricity_bill(bill):
    """
    Calculates electricity bill based on consumption tiers.
    
    Args:
        bill (float): Consumption in kWh
        
    Returns:
        float: Calculated bill amount
        
    Tier Structure:
    - 0-220 kWh: Base rate
    - 220-400 kWh: 450 + (bill-250)*4.5
    - 400+ kWh: 1350 + (bill-450)*6
    """
```

### Water Bill Calculation
```python
def calculate_water_bill(bill):
    """
    Calculates water bill based on consumption.
    
    Args:
        bill (float): Consumption in liters
        
    Returns:
        float: Calculated bill amount (rate: 40 per 1000 liters)
    """
```

---

## Error Handling

### HTTP Status Codes
- **200:** Success
- **401:** Unauthorized (invalid session or authentication failure)
- **404:** Resource not found (when using get_or_404)

### Error Response Format
```json
{
  "success": "error message or false"
}
```

### Common Error Scenarios
1. **Session Expired:** User must re-login
2. **Invalid Phone Number:** Phone number already exists during registration
3. **Authentication Failure:** Invalid credentials during login
4. **Missing Session:** Protected endpoints require valid session

---

## Usage Examples

### Complete User Workflow

#### 1. User Registration
```python
import requests

# Register new user
register_data = {
    "phone_no": "+1234567890",
    "password": "securepassword",
    "firstname": "John",
    "lastname": "Doe"
}

response = requests.post('/register', json=register_data)
if response.status_code == 200:
    print("User registered successfully")
```

#### 2. User Login
```python
# Login with credentials
login_data = {
    "phone_no": "+1234567890",
    "password": "securepassword"
}

response = requests.post('/login', json=login_data)
if response.status_code == 200:
    print("Login successful")
    # Session is now established
```

#### 3. Update User Profile
```python
# Update user details
user_details = {
    "water_consumer_no": "WC123456",
    "water_board": "City Water",
    "electricity_consumer_no": "EC789012",
    "electricity_board": "City Power",
    "Number_of_people": 4
}

response = requests.post('/updateuserdetails', json=user_details)
if response.status_code == 200:
    print("Profile updated successfully")
```

#### 4. Update Appliance Inventory
```python
# Update appliance counts
appliances = {
    "AC": 2,
    "TV": 3,
    "Refrigerator": 1,
    "Washing_machine": 1,
    "Microwave": 1,
    "Fans": 4,
    "Lights": 12,
    "Heater": 1,
    "Iron_box": 1
}

response = requests.post('/updateapplaincedetails', json=appliances)
if response.status_code == 200:
    print("Appliances updated successfully")
```

#### 5. Set Environmental Goal
```python
# Set consumption reduction goal
goal_data = {
    "goal": 200  # Target: 200 kWh per month
}

response = requests.post('/goal', json=goal_data)
if response.status_code == 200:
    print("Goal set successfully")
```

#### 6. Retrieve User Data
```python
# Get comprehensive user information
response = requests.get('/userdetails')
if response.status_code == 200:
    user_data = response.json()
    print(f"User: {user_data['details']['fullname']}")
    print(f"Appliances: {len(user_data['Applainces'])} types")
```

#### 7. Analyze Consumption
```python
# Get bill comparison
response = requests.get('/electricitybillcomparison')
if response.status_code == 200:
    comparison = response.json()
    change_percent = comparison['electricity_comparison_average']
    print(f"Consumption changed by {change_percent:.1f}%")

# Get leaderboard
response = requests.get('/leaderdetails')
if response.status_code == 200:
    leaderboard = response.json()
    print(f"Total users: {len(leaderboard)}")
```

#### 8. Logout
```python
# End session
response = requests.get('/logout')
if response.status_code == 200:
    print("Logged out successfully")
```

---

## Configuration

### Database Configuration
```python
app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://username:password@host/database?sslmode=require'
```

### CORS Configuration
```python
CORS(app)  # Enables Cross-Origin Resource Sharing
```

### Session Configuration
```python
app.secret_key = 'your-secret-key-here'
```

---

## Security Considerations

1. **Session Management:** All protected endpoints require valid session
2. **Input Validation:** Phone numbers and credentials are validated
3. **Database Security:** Uses parameterized queries via SQLAlchemy
4. **CORS:** Configured for cross-origin requests
5. **Secret Key:** Should be stored in environment variables

---

## Performance Notes

1. **Database Pooling:** Configured with connection pooling and pre-ping
2. **Session Storage:** Server-side session storage for scalability
3. **Query Optimization:** Uses SQLAlchemy ORM for efficient database operations
4. **Response Format:** JSON responses for fast client-side processing

---

## Dependencies

### Core Dependencies
- **Flask 3.0.0:** Web framework
- **Flask-SQLAlchemy 3.1.1:** Database ORM
- **Flask-CORS:** Cross-origin resource sharing
- **psycopg2-binary:** PostgreSQL adapter
- **gunicorn:** Production WSGI server

### Development Dependencies
- **Werkzeug 3.0.1:** WSGI utilities
- **Jinja2 3.1.2:** Template engine
- **SQLAlchemy 2.0.23:** Database toolkit

---

## Deployment

### Development
```bash
python main.py
```

### Production
```bash
gunicorn -w 4 -b 0.0.0.0:8000 main:app
```

### Environment Variables
```bash
export FLASK_ENV=production
export DATABASE_URL=your_database_url
export SECRET_KEY=your_secret_key
```

---

## Support & Maintenance

For technical support or questions about the API:
- Review this documentation
- Check the Flask application logs
- Verify database connectivity
- Ensure proper session management

---

*This documentation covers Ecotrack API version 1.0.0. For updates and changes, refer to the main application code.*