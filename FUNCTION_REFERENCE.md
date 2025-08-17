# Ecotrack Function Reference

## Overview

This document provides detailed information about all functions, classes, and methods in the Ecotrack application. It serves as a comprehensive reference for developers working with the codebase.

## Table of Contents

1. [Database Models](#database-models)
2. [Utility Functions](#utility-functions)
3. [Route Functions](#route-functions)
4. [Database Operations](#database-operations)
5. [Session Management](#session-management)

---

## Database Models

### User Model
**Class:** `User(db.Model)`  
**File:** `main.py` (lines 58-66)  
**Description:** Core user authentication and profile data

#### Attributes
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| `id` | Integer | Primary Key | Unique user identifier |
| `phone_no` | String(80) | Unique, Not Null | User's phone number |
| `password` | String(120) | Not Null | User's password hash |
| `fullname` | String(120) | Not Null | User's full name |
| `achievement_points` | Integer | Not Null | Points earned for achievements |
| `goal` | Integer | Not Null | Monthly consumption reduction goal |
| `contribution_points` | Integer | Not Null | Points earned for contributions |
| `redeemed_points` | Integer | Not Null | Points redeemed for rewards |

#### Usage Example
```python
# Create new user
user = User(
    phone_no="+1234567890",
    password="hashed_password",
    fullname="John Doe",
    achievement_points=0,
    goal=250,
    contribution_points=0,
    redeemed_points=0
)

# Query user
user = User.query.filter_by(phone_no="+1234567890").first()
```

### Details Model
**Class:** `Details(db.Model)`  
**File:** `main.py` (lines 69-81)  
**Description:** Extended user profile information

#### Attributes
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| `phone_no` | String(80) | Primary Key, Unique, Not Null | User's phone number |
| `username` | String(120) | Not Null | User's display name |
| `address` | String(200) | Nullable | User's residential address |
| `lng` | Float | Nullable | Longitude coordinate |
| `lat` | Float | Nullable | Latitude coordinate |
| `water_consumer_no` | String(120) | Nullable | Water utility consumer number |
| `water_board` | String(10) | Nullable | Water utility provider |
| `electricity_consumer_no` | String(120) | Nullable | Electricity utility consumer number |
| `electricity_board` | String(10) | Nullable | Electricity utility provider |
| `Number_of_people` | Integer | Nullable | Number of people in household |

#### Usage Example
```python
# Create user details
details = Details(
    phone_no="+1234567890",
    username="John Doe",
    address="123 Main St",
    lat=40.7128,
    lng=-74.0060
)

# Update existing details
details = Details.query.get("+1234567890")
details.address = "456 Oak Ave"
```

### Appliances Model
**Class:** `Appliances(db.Model)`  
**File:** `main.py` (lines 84-94)  
**Description:** User's household appliance inventory

#### Attributes
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| `phone_no` | String(80) | Primary Key, Unique, Not Null | User's phone number |
| `Television` | Integer | Nullable | Number of televisions |
| `Refrigerator` | Integer | Nullable | Number of refrigerators |
| `Microwave` | Integer | Nullable | Number of microwaves |
| `Washing_machine` | Integer | Nullable | Number of washing machines |
| `Air_conditioner` | Integer | Nullable | Number of air conditioners |
| `Iron_box` | Integer | Nullable | Number of iron boxes |
| `Heater` | Integer | Nullable | Number of heaters |
| `Fans` | Integer | Nullable | Number of fans |
| `Lights` | Integer | Nullable | Number of light fixtures |

#### Usage Example
```python
# Create appliance inventory
appliances = Appliances(
    phone_no="+1234567890",
    Television=2,
    Refrigerator=1,
    Lights=10,
    Fans=3
)

# Query appliance count
appliances = Appliances.query.get("+1234567890")
tv_count = appliances.Television
```

### Billing Details Model
**Class:** `Billing_details(db.Model)`  
**File:** `main.py` (lines 97-110)  
**Description:** Monthly billing and consumption records

#### Attributes
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| `id` | Integer | Primary Key | Unique billing record identifier |
| `phone_no` | String(80) | Not Null | User's phone number |
| `water_consumer_no` | String(120) | Nullable | Water utility consumer number |
| `electricity_consumer_no` | String(120) | Nullable | Electricity utility consumer number |
| `electricity_consumption` | Float | Nullable | Monthly electricity consumption (kWh) |
| `electricity_bill` | Float | Nullable | Monthly electricity bill amount |
| `water_consumption` | Float | Nullable | Monthly water consumption (liters) |
| `water_bill` | Float | Nullable | Monthly water bill amount |
| `water_billing_date` | String(10) | Nullable | Water billing date (DD-MM-YYYY) |
| `water_board` | String(10) | Nullable | Water utility provider |
| `electricity_billing_date` | String(10) | Nullable | Electricity billing date (DD-MM-YYYY) |
| `electricity_board` | String(10) | Nullable | Electricity utility provider |

#### Usage Example
```python
# Create billing record
billing = Billing_details(
    phone_no="+1234567890",
    electricity_consumption=350.5,
    electricity_bill=1577.25,
    water_consumption=18500.0,
    water_bill=740.0,
    electricity_billing_date="1-12-2024",
    water_billing_date="1-6-2024"
)

# Query billing history
billing_records = Billing_details.query.filter_by(phone_no="+1234567890").all()
```

### Hourly Entries Model
**Class:** `Hourly_entries(db.Model)`  
**File:** `main.py` (lines 113-121)  
**Description:** Daily hourly consumption tracking

#### Attributes
| Attribute | Type | Constraints | Description |
|-----------|------|-------------|-------------|
| `id` | Integer | Primary Key | Unique hourly record identifier |
| `phone_no` | String(80) | Not Null | User's phone number |
| `water_consumption_morning` | Float | Nullable | Morning water consumption (liters) |
| `water_consumption_afternoon` | Float | Nullable | Afternoon water consumption (liters) |
| `electricity_consumption_morning` | Float | Nullable | Morning electricity consumption (kWh) |
| `electricity_consumption_afternoon` | Float | Nullable | Afternoon electricity consumption (kWh) |
| `date` | String(10) | Nullable | Record date (DD-MM-YYYY) |

#### Usage Example
```python
# Create hourly entry
hourly = Hourly_entries(
    phone_no="+1234567890",
    water_consumption_morning=250.0,
    water_consumption_afternoon=235.0,
    electricity_consumption_morning=8.75,
    electricity_consumption_afternoon=11.67,
    date="15-12-2024"
)

# Query daily entries
daily_entries = Hourly_entries.query.filter_by(phone_no="+1234567890").all()
```

---

## Utility Functions

### calculate_electricity_bill()
**File:** `main.py` (lines 125-130)  
**Description:** Calculates electricity bill based on consumption tiers

#### Signature
```python
def calculate_electricity_bill(bill: float) -> float
```

#### Parameters
| Parameter | Type | Description |
|-----------|------|-------------|
| `bill` | float | Electricity consumption in kWh |

#### Return Value
- **Type:** float
- **Description:** Calculated electricity bill amount

#### Tier Structure
- **0-220 kWh:** Base rate (not specified in current implementation)
- **220-400 kWh:** 450 + (bill-250) × 4.5
- **400+ kWh:** 1350 + (bill-450) × 6

#### Implementation
```python
def calculate_electricity_bill(bill):
    if bill>220 and bill<400:
        return 450 + (bill-250)*4.5
    elif bill>400:
        return 1350 + (bill-450)*6
```

#### Usage Example
```python
# Calculate bill for 300 kWh consumption
bill = calculate_electricity_bill(300)
# Result: 450 + (300-250)*4.5 = 450 + 225 = 675

# Calculate bill for 500 kWh consumption
bill = calculate_electricity_bill(500)
# Result: 1350 + (500-450)*6 = 1350 + 300 = 1650
```

### calculate_water_bill()
**File:** `main.py` (lines 133-137)  
**Description:** Calculates water bill based on consumption

#### Signature
```python
def calculate_water_bill(bill: float) -> float
```

#### Parameters
| Parameter | Type | Description |
|-----------|------|-------------|
| `bill` | float | Water consumption in liters |

#### Return Value
- **Type:** float
- **Description:** Calculated water bill amount

#### Rate Structure
- **Rate:** 40 per 1000 liters
- **Formula:** (bill / 1000) × 40

#### Implementation
```python
def calculate_water_bill(bill):
    bill=bill/1000
    return bill*40
```

#### Usage Example
```python
# Calculate bill for 20,000 liters consumption
bill = calculate_water_bill(20000)
# Result: (20000/1000) * 40 = 20 * 40 = 800

# Calculate bill for 15,000 liters consumption
bill = calculate_water_bill(15000)
# Result: (15000/1000) * 40 = 15 * 40 = 600
```

---

## Route Functions

### home()
**File:** `main.py` (lines 150-153)  
**Route:** `/`  
**Methods:** POST, GET  
**Description:** Root endpoint for session validation

#### Implementation
```python
@app.route('/',methods=['POST','GET'])
def home():
    if 'phone_no' not in session:
        return json.dumps({'success':False}), 401, {'ContentType':'application/json'}
```

#### Behavior
- Checks if user has active session
- Returns 401 Unauthorized if no session exists
- No success response defined for authenticated users

#### Usage
- **Purpose:** Session validation endpoint
- **Authentication:** Required
- **Response:** 401 if no session, undefined if authenticated

### register()
**File:** `main.py` (lines 156-250)  
**Route:** `/register`  
**Methods:** POST, GET  
**Description:** User registration with comprehensive data setup

#### Implementation Details
1. **Input Validation:** Checks for unique phone number
2. **Data Generation:** Creates mock consumption data
3. **Database Creation:** Sets up all related tables
4. **Goal Setting:** Establishes initial environmental goals

#### Mock Data Generation
- **Electricity:** Random consumption between 250-500 kWh
- **Water:** Random consumption between 18,000-20,000 liters
- **Historical Data:** 3 months of billing records
- **Hourly Data:** 7 days of consumption entries

#### Database Operations
```python
# Create user records
user = User(phone_no=phone_no, password=password, fullname=fullname, ...)
user_details = Details(phone_no=phone_no, username=fullname, ...)
appliance_details = Appliances(phone_no=phone_no, ...)
bill_details = Billing_details(phone_no=phone_no, ...)

# Add to session and commit
db.session.add(user)
db.session.add(user_details)
db.session.add(appliance_details)
db.session.add(bill_details)
db.session.commit()
```

#### Response Format
- **Success (200):** `{"success": true}`
- **Error (401):** `{"success": "Phone number already exists!"}`

### login()
**File:** `main.py` (lines 253-270)  
**Route:** `/login`  
**Methods:** POST, GET  
**Description:** User authentication and session creation

#### Implementation
```python
@app.route('/login',methods=['POST','GET'])
def login():
    if request.method == 'POST':
        phone_no = request.json['phone_no']
        password = request.json['password']
        
        user = User.query.filter_by(phone_no=phone_no, password=password).first()
        
        if user is not None:
            session['phone_no'] = phone_no
            return json.dumps({'success':True}), 200, {'ContentType':'application/json'}
        else:
            return json.dumps({'success':"user is None"}), 401, {'ContentType':'application/json'}
```

#### Authentication Flow
1. Extract credentials from JSON request
2. Query database for matching user
3. Create session if authentication successful
4. Return appropriate response

#### Response Format
- **Success (200):** `{"success": true}`
- **Error (401):** `{"success": "user is None"}`

### logout()
**File:** `main.py` (lines 273-280)  
**Route:** `/logout`  
**Methods:** GET  
**Description:** Session termination

#### Implementation
```python
@app.route("/logout", methods=['GET'])
def logout():
    session.pop('phone_no', None)
    return json.dumps({'success':True}), 200, {'ContentType':'application/json'}
```

#### Behavior
- Removes phone number from session
- Always returns success response
- No authentication validation required

### updateuserdetails()
**File:** `main.py` (lines 283-320)  
**Route:** `/updateuserdetails`  
**Methods:** GET, POST  
**Description:** Updates user profile and billing information

#### Implementation Details
1. **Session Validation:** Checks for active user session
2. **Data Extraction:** Gets update data from JSON request
3. **Profile Update:** Updates user details table
4. **Billing Sync:** Synchronizes consumer numbers across billing records

#### Database Operations
```python
# Update user details
user_to_update = Details.query.get_or_404(phone_no)
user_to_update.water_board = water_board
user_to_update.water_consumer_no = water_consumer_no
# ... other fields

# Update billing details
details_to_update = Billing_details.query.filter_by(phone_no=session['phone_no']).all()
for i in range(3):
    details_to_update[i].water_board = water_board
    details_to_update[i].water_consumer_no = water_consumer_no
    # ... other fields

db.session.commit()
```

#### Response Format
- **Success (200):** `{"success": true}`
- **Error (401):** `{"success": false}`

### updateapplaincedetails()
**File:** `main.py` (lines 323-360)  
**Route:** `/updateapplaincedetails`  
**Methods:** GET, POST  
**Description:** Updates user's appliance inventory

#### Implementation Details
1. **Session Validation:** Checks for active user session
2. **Appliance Counts:** Updates all appliance quantities
3. **Database Update:** Commits changes to appliances table

#### Database Operations
```python
appliance_to_update = Appliances.query.get_or_404(phone_no)
appliance_to_update.Television = Television
appliance_to_update.Air_conditioner = Air_conditioner
# ... other appliances

db.session.commit()
```

#### Response Format
- **Success (200):** `{"success": true}`
- **Error (401):** `{"success": false}`

### goal()
**File:** `main.py` (lines 363-380)  
**Route:** `/goal`  
**Methods:** GET, POST  
**Description:** Sets or updates user's consumption reduction goal

#### Implementation Details
1. **Session Validation:** Checks for active user session
2. **Goal Update:** Updates goal in user table
3. **Database Commit:** Saves changes

#### Database Operations
```python
goal_to_update = User.query.filter_by(phone_no=session['phone_no']).first()
goal_to_update.goal = goal
db.session.commit()
```

#### Response Format
- **Success (200):** `{"success": true}`
- **Error (401):** `{"success": false}`

### userdetails()
**File:** `main.py` (lines 383-395)  
**Route:** `/userdetails`  
**Methods:** GET  
**Description:** Retrieves comprehensive user profile and appliance information

#### Implementation Details
1. **Session Validation:** Checks for active user session
2. **Data Retrieval:** Queries user details and appliances tables
3. **Data Formatting:** Structures response with appliances and profile data

#### Response Structure
```json
{
  "Applainces": [
    {"name": "Lights", "count": 10},
    {"name": "Fans", "count": 3}
    // ... other appliances
  ],
  "details": {
    "phone_no": "string",
    "fullname": "string",
    "address": "string"
    // ... other profile fields
  }
}
```

### billdetails()
**File:** `main.py` (lines 398-410)  
**Route:** `/billdetails`  
**Methods:** GET  
**Description:** Retrieves last 3 months of billing information

#### Implementation Details
1. **Session Validation:** Checks for active user session
2. **Data Retrieval:** Queries billing details table
3. **Response Formatting:** Returns array of billing records with payment status

#### Response Structure
```json
[
  {
    "electricity_consumer_no": "string",
    "water_consumer_no": "string",
    "electricity_bill": "float",
    "water_bill": "float"
    // ... other billing fields
  }
]
```

### pointsdetails()
**File:** `main.py` (lines 413-425)  
**Route:** `/pointsdetails`  
**Methods:** GET  
**Description:** Retrieves user's achievement and contribution points

#### Implementation Details
1. **Session Validation:** Checks for active user session
2. **Data Retrieval:** Queries user table for points
3. **Response Formatting:** Returns points summary

#### Response Structure
```json
{
  "Achievement_points": "integer",
  "Redeemed_points": "integer",
  "Contributed_points": "integer"
}
```

### goaldetails()
**File:** `main.py` (lines 428-440)  
**Route:** `/goaldetails`  
**Methods:** GET  
**Description:** Retrieves user's current consumption reduction goal

#### Implementation Details
1. **Session Validation:** Checks for active user session
2. **Data Retrieval:** Queries user table for goal
3. **Response Formatting:** Returns goal value

#### Response Structure
```json
{
  "goal": "integer (kWh)"
}
```

### leaderdetails()
**File:** `main.py` (lines 443-460)  
**Route:** `/leaderdetails`  
**Methods:** GET  
**Description:** Retrieves community leaderboard with consumption rankings

#### Implementation Details
1. **Session Validation:** Checks for active user session
2. **Data Retrieval:** Queries all users and their consumption data
3. **Data Aggregation:** Combines user and billing information
4. **Response Formatting:** Returns leaderboard array

#### Response Structure
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

### hourlydetails()
**File:** `main.py` (lines 463-480)  
**Route:** `/hourlydetails`  
**Methods:** GET  
**Description:** Retrieves last 7 days of hourly consumption data

#### Implementation Details
1. **Session Validation:** Checks for active user session
2. **Data Retrieval:** Queries hourly entries table
3. **Data Formatting:** Structures response with daily consumption data

#### Response Structure
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

### electricitybillcomparison()
**File:** `main.py` (lines 483-500)  
**Route:** `/electricitybillcomparison`  
**Methods:** GET  
**Description:** Compares current month vs previous month electricity consumption

#### Implementation Details
1. **Session Validation:** Checks for active user session
2. **Data Retrieval:** Queries billing details for comparison
3. **Calculation:** Computes consumption difference and percentage change
4. **Response Formatting:** Returns comparison data

#### Response Structure
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

### waterbillcomparison()
**File:** `main.py` (lines 503-520)  
**Route:** `/waterbillcomparison`  
**Methods:** GET  
**Description:** Compares current month vs previous month water consumption

#### Implementation Details
1. **Session Validation:** Checks for active user session
2. **Data Retrieval:** Queries billing details for comparison
3. **Calculation:** Computes consumption difference and percentage change
4. **Response Formatting:** Returns comparison data

#### Response Structure
```json
{
  "water_date_1": "string (DD-MM-YYYY)",
  "water_bill_1": "float",
  "water_consumption_1": "float (liters)",
  "water_date_2": "string (DD-MM-YYYY)",
  "water_bill_2": "float",
  "water_consumption_2": "float (liters)",
  "water_consumption_comparison": "float (difference)",
  "water_comparison_average": "float (percentage change)"
}
```

---

## Database Operations

### Database Initialization
**File:** `main.py` (lines 145-147)  
**Description:** Creates all database tables on application startup

#### Implementation
```python
with app.app_context():
    db.create_all()
```

#### Behavior
- Executes when application starts
- Creates all defined models if they don't exist
- Uses PostgreSQL database with connection pooling

### Database Configuration
**File:** `main.py` (lines 32-42)  
**Description:** Custom SQLAlchemy configuration with connection pooling

#### Implementation
```python
class SQLAlchemy(_BaseSQLAlchemy):
    def apply_pool_defaults(self, app, options):
        super(SQLAlchemy, self).apply_pool_defaults(self, app, options)
        options["pool_pre_ping"] = True

db = SQLAlchemy(app)
```

#### Features
- **Connection Pooling:** Manages database connections efficiently
- **Pre-ping:** Validates connections before use
- **PostgreSQL Support:** Optimized for PostgreSQL database

---

## Session Management

### Session Configuration
**File:** `main.py` (line 45)  
**Description:** Flask session configuration

#### Implementation
```python
app.secret_key='asdsdfsdfs13sdf_df%&'
```

#### Security Considerations
- Secret key should be stored in environment variables
- Current implementation uses hardcoded key
- Required for session encryption and validation

### Session Validation Pattern
**Pattern:** Used across all protected endpoints

#### Implementation
```python
if 'phone_no' not in session:
    return json.dumps({'success':False}), 401, {'ContentType':'application/json'}
```

#### Usage
- Applied to all endpoints except `/register` and `/login`
- Ensures user authentication before data access
- Returns 401 Unauthorized for invalid sessions

---

## Error Handling

### HTTP Status Codes
- **200:** Success
- **401:** Unauthorized (session validation failure)
- **404:** Resource not found (database query failures)

### Error Response Format
```json
{
  "success": "error message or false"
}
```

### Common Error Scenarios
1. **Missing Session:** 401 Unauthorized
2. **Invalid Credentials:** 401 with error message
3. **Resource Not Found:** 404 (from get_or_404)
4. **Database Errors:** Handled by SQLAlchemy

---

## Performance Considerations

### Database Optimization
- **Connection Pooling:** Efficient connection management
- **Pre-ping:** Connection validation
- **ORM Usage:** Parameterized queries for security

### Response Optimization
- **JSON Format:** Fast client-side processing
- **Minimal Data Transfer:** Only necessary fields returned
- **Session Caching:** Reduces database queries

---

## Security Features

### Input Validation
- **Phone Number Uniqueness:** Prevents duplicate registrations
- **Session Validation:** Protects all sensitive endpoints
- **Parameterized Queries:** Prevents SQL injection

### Authentication
- **Session-based:** Server-side session storage
- **Phone Number + Password:** Dual-factor authentication
- **Automatic Logout:** Session termination endpoint

---

## Development Notes

### Code Structure
- **Monolithic Design:** Single file application
- **Route-based Organization:** Clear endpoint separation
- **Database Models:** Well-defined data structures

### Areas for Improvement
1. **Error Handling:** More specific error messages
2. **Input Validation:** Enhanced data validation
3. **Code Organization:** Separate into modules
4. **Security:** Environment variable configuration
5. **Testing:** Unit test coverage

---

*This function reference covers all functions, classes, and methods in Ecotrack version 1.0.0. For implementation details, refer to the main.py source file.*