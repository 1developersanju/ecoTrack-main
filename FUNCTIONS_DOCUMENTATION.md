# Ecotrack Functions Documentation

## Overview

This document provides comprehensive documentation for all functions, route handlers, and utility methods in the Ecotrack application. The application is built using Flask and includes authentication, user management, data retrieval, and billing calculation functions.

---

## Utility Functions

### 1. calculate_electricity_bill()

**Location:** `main.py` (lines 120-125)

Calculates electricity bill amount based on consumption using tiered pricing structure.

```python
def calculate_electricity_bill(bill):
    if bill>220 and bill<400:
        return 450 + (bill-250)*4.5
    elif bill>400:
        return 1350 + (bill-450)*6
```

**Parameters:**
- `bill` (float): Electricity consumption in kWh

**Returns:**
- `float`: Calculated bill amount in currency units

**Pricing Tiers:**
- **Tier 1**: 0-220 kWh - No calculation (returns None)
- **Tier 2**: 221-399 kWh - Base charge ₹450 + (consumption - 250) × ₹4.5/kWh
- **Tier 3**: 400+ kWh - Base charge ₹1350 + (consumption - 450) × ₹6/kWh

**Usage Example:**
```python
# Calculate bill for 350 kWh consumption
bill_amount = calculate_electricity_bill(350)
# Returns: 450 + (350-250)*4.5 = 450 + 450 = 900

# Calculate bill for 500 kWh consumption  
bill_amount = calculate_electricity_bill(500)
# Returns: 1350 + (500-450)*6 = 1350 + 300 = 1650
```

**Notes:**
- Function doesn't handle consumption below 220 kWh (returns None)
- Consider adding validation for negative values
- Pricing structure appears to be region-specific

### 2. calculate_water_bill()

**Location:** `main.py` (lines 129-132)

Calculates water bill based on consumption using flat rate pricing.

```python
def calculate_water_bill(bill):
    bill=bill/1000
    return bill*40
```

**Parameters:**
- `bill` (float): Water consumption in liters

**Returns:**
- `float`: Calculated bill amount in currency units

**Pricing Structure:**
- Flat rate of ₹40 per kiloliter (1000 liters)

**Usage Example:**
```python
# Calculate bill for 18,000 liters consumption
bill_amount = calculate_water_bill(18000)
# Returns: (18000/1000)*40 = 18*40 = 720

# Calculate bill for 25,500 liters consumption
bill_amount = calculate_water_bill(25500)
# Returns: (25500/1000)*40 = 25.5*40 = 1020
```

**Notes:**
- Simple flat-rate calculation
- No tiered pricing structure
- Consider adding minimum charge or validation

---

## Route Handler Functions

### Authentication Routes

#### 1. home()

**Route:** `GET/POST /`
**Location:** `main.py` (lines 143-147)

Root endpoint that checks for user authentication.

```python
@app.route('/',methods=['POST','GET'])
def home():
    if 'phone_no' not in session:
        return json.dumps({'success':False}), 401, {'ContentType':'application/json'}
```

**Parameters:** None

**Returns:**
- **Authenticated**: No explicit return (falls through)
- **Unauthenticated**: `{"success": false}` with HTTP 401

**Authentication:** Required (session-based)

**Usage Example:**
```bash
curl -X GET http://localhost:5000/ -b cookies.txt
```

#### 2. register()

**Route:** `POST /register`
**Location:** `main.py` (lines 150-242)

Creates a new user account with initial data seeding.

```python
@app.route('/register',methods=['POST','GET'])
def register():
    # Implementation details...
```

**Request Body:**
```json
{
    "phone_no": "string",
    "password": "string", 
    "firstname": "string",
    "lastname": "string"
}
```

**Returns:**
- **Success**: `{"success": true}` with HTTP 200
- **Error**: `{"success": "Phone number already exists!"}` with HTTP 401

**Process Flow:**
1. Validates phone number uniqueness
2. Creates user record with combined firstname + lastname
3. Generates mock billing data for 3 periods
4. Creates 7 days of mock hourly consumption data
5. Initializes user details and appliances with null values

**Data Seeding:**
- **Electricity consumption**: Random 250-500 kWh with 2.5-5% variations
- **Water consumption**: Random 18,000-20,000 liters with 2.5-5% variations
- **Achievement points**: Initialized to 0
- **Goal**: Set to 95% of initial consumption

**Usage Example:**
```python
# Mock data generation for new user
econsumption = randint(250, 500)  # Random electricity consumption
wconsumption = randint(18000, 20000)  # Random water consumption
goal = econsumption * 0.95  # Goal set to 5% reduction
```

#### 3. login()

**Route:** `POST /login`
**Location:** `main.py` (lines 246-267)

Authenticates user and creates session.

```python
@app.route('/login',methods=['POST','GET'])
def login():
    # Implementation details...
```

**Request Body:**
```json
{
    "phone_no": "string",
    "password": "string"
}
```

**Returns:**
- **Success**: `{"success": true}` with HTTP 200
- **Error**: `{"success": "user is None"}` with HTTP 401

**Process Flow:**
1. Extracts phone_no and password from request
2. Queries database for matching user
3. Creates session if credentials valid
4. Returns appropriate response

**Security Notes:**
- Plain text password comparison
- No rate limiting implemented
- Consider implementing password hashing

#### 4. logout()

**Route:** `GET /logout`
**Location:** `main.py` (lines 271-278)

Ends user session.

```python
@app.route("/logout", methods=['GET'])
def logout():
    session.pop('phone_no', None)
    return json.dumps({'success':True}), 200, {'ContentType':'application/json'}
```

**Parameters:** None

**Returns:**
- `{"success": true}` with HTTP 200

**Process:** Removes phone_no from session

### User Management Routes

#### 5. updateuserdetails()

**Route:** `POST /updateuserdetails`
**Location:** `main.py` (lines 282-321)

Updates user profile and utility account information.

```python
@app.route("/updateuserdetails", methods=['GET','POST'])
def updateuserdetails():
    # Implementation details...
```

**Authentication:** Required

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

**Returns:**
- **Success**: `{"success": true}` with HTTP 200
- **Error**: `{"success": false}` with HTTP 401

**Process Flow:**
1. Updates Details table with new information
2. Updates all Billing_details records with utility account info
3. Commits changes to database

**Database Operations:**
- Updates 1 record in Details table
- Updates 3 records in Billing_details table (current, previous, last month)

#### 6. updateapplaincedetails()

**Route:** `POST /updateapplaincedetails`
**Location:** `main.py` (lines 324-361)

Updates household appliance counts for consumption estimation.

```python
@app.route("/updateapplaincedetails", methods=['GET','POST'])
def updateapplaincedetails():
    # Implementation details...
```

**Authentication:** Required

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

**Appliance Mapping:**
- `AC` → `Air_conditioner`
- `TV` → `Television`
- Other fields map directly

**Returns:**
- **Success**: `{"success": true}` with HTTP 200
- **Error**: `{"success": false}` with HTTP 401

#### 7. goal()

**Route:** `POST /goal`
**Location:** `main.py` (lines 365-385)

Updates user's consumption reduction goal.

```python
@app.route("/goal", methods=['GET','POST'])
def goal():
    # Implementation details...
```

**Authentication:** Required

**Request Body:**
```json
{
    "goal": "number"
}
```

**Returns:**
- **Success**: `{"success": true}` with HTTP 200
- **Error**: `{"success": false}` with HTTP 401

**Process:** Updates goal field in User table

### Data Retrieval Routes

#### 8. userdetails()

**Route:** `GET /userdetails`
**Location:** `main.py` (lines 389-402)

Retrieves comprehensive user profile and appliance information.

```python
@app.route("/userdetails", methods=['GET'])
def userdetails():
    # Implementation details...
```

**Authentication:** Required

**Returns:**
```json
{
    "Applainces": [
        {"name": "Lights", "count": 15},
        {"name": "Fans", "count": 8},
        {"name": "Heater", "count": 1},
        {"name": "Iron_box", "count": 1},
        {"name": "Television", "count": 2},
        {"name": "Washing_machine", "count": 1},
        {"name": "Ac", "count": 3},
        {"name": "Microwave", "count": 1},
        {"name": "Refrigerator", "count": 1}
    ],
    "details": {
        "phone_no": "string",
        "fullname": "string",
        "address": "string",
        "electricity_consumer_no": "string",
        "water_consumer_no": "string", 
        "lat": "number",
        "lng": "number",
        "electricity_board": "string",
        "water_board": "string",
        "Number_of_people": "integer"
    }
}
```

**Database Queries:**
- Details table: 1 query by phone_no
- Appliances table: 1 query by phone_no

#### 9. billdetails()

**Route:** `GET /billdetails`
**Location:** `main.py` (lines 406-418)

Retrieves billing history for the last 3 periods.

```python
@app.route("/billdetails", methods=['GET'])
def billdetails():
    # Implementation details...
```

**Authentication:** Required

**Returns:** Array of 3 billing records with payment status

```json
[
    {
        "electricity_consumer_no": "string",
        "water_consumer_no": "string",
        "electricity_board": "string", 
        "water_board": "string",
        "water_date": "string",
        "electricity_date": "string",
        "electricity_bill": "number",
        "water_bill": "number",
        "electricity_consumption": "number",
        "water_consumption": "number",
        "water_payment_status": false,
        "electricity_payment_status": true
    }
    // ... 2 more records
]
```

**Hard-coded Values:**
- `water_payment_status`: Always false
- `electricity_payment_status`: Always true

#### 10. pointsdetails()

**Route:** `GET /pointsdetails`
**Location:** `main.py` (lines 423-435)

Retrieves user's points information.

```python
@app.route("/pointsdetails", methods=['GET'])
def pointsdetails():
    # Implementation details...
```

**Authentication:** Required

**Returns:**
```json
{
    "Achievement_points": "integer",
    "Redeemed_points": "integer", 
    "Contributed_points": "integer"
}
```

#### 11. goaldetails()

**Route:** `GET /goaldetails`
**Location:** `main.py` (lines 439-451)

Retrieves user's current consumption goal.

```python
@app.route("/goaldetails", methods=['GET'])
def goaldetails():
    # Implementation details...
```

**Authentication:** Required

**Returns:**
```json
{
    "goal": "number"
}
```

#### 12. leaderdetails()

**Route:** `GET /leaderdetails`
**Location:** `main.py` (lines 453-470)

Retrieves leaderboard data for all users.

```python
@app.route("/leaderdetails", methods=['GET'])
def leaderdetails():
    # Implementation details...
```

**Authentication:** Required

**Returns:** Array of user leaderboard entries

```json
[
    {
        "phone_no": "string",
        "username": "string",
        "achievement_points": "integer",
        "water_consumption": "number",
        "electricity_consumption": "number"
    }
    // ... other users
]
```

**Database Operations:**
- Queries all users from User table
- For each user, queries latest billing data
- Combines data into leaderboard format

**Performance Note:** N+1 query issue - consider optimizing with JOIN

#### 13. hourlydetails()

**Route:** `GET /hourlydetails`
**Location:** `main.py` (lines 474-490)

Retrieves daily consumption data broken down by time periods.

```python
@app.route("/hourlydetails", methods=['GET'])
def hourlydetails():
    # Implementation details...
```

**Authentication:** Required

**Returns:** Array of daily consumption entries

```json
[
    {
        "water_consumption_morning": "number",
        "water_consumption_afternoon": "number", 
        "electricity_consumption_morning": "number",
        "electricity_consumption_afternoon": "number",
        "date": "string"
    }
    // ... up to 7 days
]
```

### Comparison Routes

#### 14. electricitybillcomparison()

**Route:** `GET /electricitybillcomparison`
**Location:** `main.py` (lines 494-510)

Compares current and previous month's electricity consumption and bills.

```python
@app.route("/electricitybillcomparison", methods=['GET'])
def electricitybillcomparison():
    # Implementation details...
```

**Authentication:** Required

**Returns:**
```json
{
    "electricity_date_1": "string",
    "electricity_bill_1": "number",
    "electricity_consumption_1": "number",
    "electricity_date_2": "string",
    "electricity_bill_2": "number", 
    "electricity_consumption_2": "number",
    "electricity_consumption_comparison": "number",
    "electricity_comparison_average": "number"
}
```

**Calculations:**
- `electricity_consumption_comparison`: Current - Previous consumption
- `electricity_comparison_average`: ((Current - Previous) / Previous) × 100

**Usage Example:**
```python
# If current consumption is 350 kWh and previous is 367.5 kWh:
comparison = 350 - 367.5  # = -17.5 kWh (reduction)
percentage = ((350 - 367.5) / 367.5) * 100  # = -4.76% (improvement)
```

#### 15. waterbillcomparison()

**Route:** `GET /waterbillcomparison`
**Location:** `main.py` (lines 514-530)

Compares current and previous period's water consumption and bills.

```python
@app.route("/waterbillcomparison", methods=['GET'])
def waterbillcomparison():
    # Implementation details...
```

**Authentication:** Required

**Returns:**
```json
{
    "water_date_1": "string",
    "water_bill_1": "number",
    "water_consumption_1": "number", 
    "water_date_2": "string",
    "water_bill_2": "number",
    "water_consumption_2": "number",
    "water_consumption_comparison": "number",
    "water_comparison_average": "number"
}
```

**Calculations:** Same as electricity comparison but for water data

---

## Application Configuration Functions

### Database Initialization

**Location:** `main.py` (lines 136-137)

```python
with app.app_context():
    db.create_all()
```

**Purpose:** Creates all database tables when application starts

### Custom SQLAlchemy Class

**Location:** `main.py` (lines 30-33)

```python
class SQLAlchemy(_BaseSQLAlchemy):
    def apply_pool_defaults(self, app, options):
        super(SQLAlchemy, self).apply_pool_defaults(self, app, options)
        options["pool_pre_ping"] = True
```

**Purpose:** Enables connection pool pre-ping for better reliability

---

## Error Handling Patterns

### Common Error Responses

1. **Authentication Required:**
   ```json
   {"success": false}  // HTTP 401
   ```

2. **Invalid Method:**
   ```json
   {"success": false}  // HTTP 401
   ```

3. **Specific Errors:**
   ```json
   {"success": "Phone number already exists!"}  // HTTP 401
   ```

### Authentication Check Pattern

Most routes use this pattern:
```python
if 'phone_no' in session:
    # Authenticated logic
    return success_response
else:
    return json.dumps({'success': False}), 401, {'ContentType': 'application/json'}
```

---

## Performance Considerations

### Database Query Optimization

1. **N+1 Query Issue in leaderdetails():**
   ```python
   # Current implementation (inefficient)
   user = User.query.all()
   for i in user:
       consumption = Billing_details.query.filter_by(phone_no=i.phone_no).first()
   
   # Recommended optimization
   leaderboard = db.session.query(User, Billing_details).join(
       Billing_details, User.phone_no == Billing_details.phone_no
   ).all()
   ```

2. **Index Recommendations:**
   - Add index on `phone_no` fields for faster lookups
   - Add index on `date` fields for temporal queries

### Memory Usage

- Large JSON responses in billdetails() - consider pagination
- Leaderboard queries all users - consider limiting results

---

## Security Recommendations

1. **Password Security:**
   - Implement password hashing (bcrypt/scrypt)
   - Add password strength requirements

2. **Input Validation:**
   - Validate phone number format
   - Sanitize user inputs
   - Add CSRF protection

3. **Session Security:**
   - Implement session timeout
   - Use secure session cookies
   - Add rate limiting

4. **Database Security:**
   - Use parameterized queries (SQLAlchemy ORM provides this)
   - Implement proper error handling to avoid information leakage

---

## Testing Recommendations

### Unit Tests

```python
# Example test for calculate_electricity_bill()
def test_electricity_bill_calculation():
    assert calculate_electricity_bill(350) == 900
    assert calculate_electricity_bill(500) == 1650
    assert calculate_electricity_bill(200) is None  # Below threshold

# Example test for calculate_water_bill()  
def test_water_bill_calculation():
    assert calculate_water_bill(18000) == 720
    assert calculate_water_bill(25500) == 1020
```

### Integration Tests

```python
# Example API test
def test_login_success():
    response = client.post('/login', json={
        'phone_no': '1234567890',
        'password': 'testpass'
    })
    assert response.status_code == 200
    assert response.json['success'] is True
```