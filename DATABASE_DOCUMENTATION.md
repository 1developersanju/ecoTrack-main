# Ecotrack Database Documentation

## Overview

The Ecotrack application uses PostgreSQL as its primary database with SQLAlchemy ORM for data modeling and management. The database is designed to track user accounts, consumption data, billing information, and appliance details for environmental impact monitoring.

## Database Configuration

```python
app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://user:password@host/database?sslmode=require'
```

The application uses a custom SQLAlchemy class with connection pool pre-ping enabled for better connection reliability.

## Database Models

### 1. User Model

**Table Name:** `user`

The primary user authentication and profile model.

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

**Fields:**

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| `id` | Integer | Primary Key, Auto-increment | Unique user identifier |
| `phone_no` | String(80) | Unique, Not Null | User's phone number (used as login ID) |
| `password` | String(120) | Not Null | User's password (stored as plain text) |
| `fullname` | String(120) | Not Null | User's full name (firstname + lastname) |
| `achievement_points` | Integer | Not Null | Points earned through consumption reduction |
| `goal` | Integer | Not Null | User's consumption reduction goal |
| `contribution_points` | Integer | Not Null | Points earned through community contributions |
| `redeemed_points` | Integer | Not Null | Points redeemed by the user |

**Relationships:**
- One-to-One with `Details` (via phone_no)
- One-to-One with `Appliances` (via phone_no)
- One-to-Many with `Billing_details` (via phone_no)
- One-to-Many with `Hourly_entries` (via phone_no)

**Usage Example:**
```python
# Create a new user
user = User(
    phone_no="1234567890",
    password="securepass",
    fullname="John Doe",
    achievement_points=0,
    goal=300,
    contribution_points=0,
    redeemed_points=0
)
db.session.add(user)
db.session.commit()

# Query user
user = User.query.filter_by(phone_no="1234567890").first()
```

### 2. Details Model

**Table Name:** `details`

Extended user profile information including location and utility account details.

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

**Fields:**

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| `phone_no` | String(80) | Primary Key, Unique, Not Null | Reference to User.phone_no |
| `username` | String(120) | Not Null | Display name for the user |
| `address` | String(200) | Nullable | User's residential address |
| `lng` | Float | Nullable | Longitude coordinate of user's location |
| `lat` | Float | Nullable | Latitude coordinate of user's location |
| `water_consumer_no` | String(120) | Nullable | Water utility consumer number |
| `water_board` | String(10) | Nullable | Water board/utility provider code |
| `electricity_consumer_no` | String(120) | Nullable | Electricity utility consumer number |
| `electricity_board` | String(10) | Nullable | Electricity board/utility provider code |
| `Number_of_people` | Integer | Nullable | Number of people in the household |

**Relationships:**
- One-to-One with `User` (via phone_no)

**Usage Example:**
```python
# Create user details
details = Details(
    phone_no="1234567890",
    username="John Doe",
    address="123 Main St, City",
    water_consumer_no="WB123456",
    water_board="BWSSB",
    electricity_consumer_no="EB789012",
    electricity_board="BESCOM",
    Number_of_people=4
)
db.session.add(details)
db.session.commit()
```

### 3. Appliances Model

**Table Name:** `appliances`

Tracks the count of various household appliances for consumption estimation.

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

**Fields:**

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| `phone_no` | String(80) | Primary Key, Unique, Not Null | Reference to User.phone_no |
| `Television` | Integer | Nullable | Number of televisions |
| `Refrigerator` | Integer | Nullable | Number of refrigerators |
| `Microwave` | Integer | Nullable | Number of microwaves |
| `Washing_machine` | Integer | Nullable | Number of washing machines |
| `Air_conditioner` | Integer | Nullable | Number of air conditioners |
| `Iron_box` | Integer | Nullable | Number of iron boxes |
| `Heater` | Integer | Nullable | Number of heaters |
| `Fans` | Integer | Nullable | Number of fans |
| `Lights` | Integer | Nullable | Number of lights |

**Relationships:**
- One-to-One with `User` (via phone_no)

**Usage Example:**
```python
# Create appliance record
appliances = Appliances(
    phone_no="1234567890",
    Television=2,
    Refrigerator=1,
    Air_conditioner=3,
    Fans=8,
    Lights=15
)
db.session.add(appliances)
db.session.commit()
```

### 4. Billing_details Model

**Table Name:** `billing_details`

Stores historical billing and consumption data for water and electricity.

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

**Fields:**

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| `id` | Integer | Primary Key, Auto-increment | Unique billing record identifier |
| `phone_no` | String(80) | Not Null | Reference to User.phone_no |
| `water_consumer_no` | String(120) | Nullable | Water utility consumer number |
| `electricity_consumer_no` | String(120) | Nullable | Electricity utility consumer number |
| `electricity_consumption` | Float | Nullable | Electricity consumption in kWh |
| `electricity_bill` | Float | Nullable | Electricity bill amount in currency |
| `water_consumption` | Float | Nullable | Water consumption in liters |
| `water_bill` | Float | Nullable | Water bill amount in currency |
| `water_billing_date` | String(10) | Nullable | Water billing date (format: "DD-MM-YYYY") |
| `water_board` | String(10) | Nullable | Water board/utility provider code |
| `electricity_billing_date` | String(10) | Nullable | Electricity billing date (format: "DD-MM-YYYY") |
| `electricity_board` | String(10) | Nullable | Electricity board/utility provider code |

**Relationships:**
- Many-to-One with `User` (via phone_no)

**Usage Example:**
```python
# Create billing record
billing = Billing_details(
    phone_no="1234567890",
    electricity_consumption=350.0,
    electricity_bill=1250.5,
    water_consumption=18000.0,
    water_bill=720.0,
    electricity_billing_date="1-12-2023",
    water_billing_date="1-10-2023"
)
db.session.add(billing)
db.session.commit()
```

### 5. Hourly_entries Model

**Table Name:** `hourly_entries`

Tracks daily consumption data broken down by time periods (morning/afternoon).

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

**Fields:**

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| `id` | Integer | Primary Key, Auto-increment | Unique entry identifier |
| `phone_no` | String(80) | Not Null | Reference to User.phone_no |
| `water_consumption_morning` | Float | Nullable | Morning water consumption in liters |
| `water_consumption_afternoon` | Float | Nullable | Afternoon water consumption in liters |
| `electricity_consumption_morning` | Float | Nullable | Morning electricity consumption in kWh |
| `electricity_consumption_afternoon` | Float | Nullable | Afternoon electricity consumption in kWh |
| `date` | String(10) | Nullable | Entry date (format: "DD-MM-YYYY") |

**Relationships:**
- Many-to-One with `User` (via phone_no)

**Usage Example:**
```python
# Create hourly entry
entry = Hourly_entries(
    phone_no="1234567890",
    water_consumption_morning=175.0,
    water_consumption_afternoon=160.0,
    electricity_consumption_morning=8.75,
    electricity_consumption_afternoon=11.67,
    date="25-12-2023"
)
db.session.add(entry)
db.session.commit()
```

## Entity Relationship Diagram

```
User (1) -------- (1) Details
  |                     |
  | phone_no           | phone_no
  |                     |
  |                     |
User (1) -------- (1) Appliances
  |                     |
  | phone_no           | phone_no
  |                     |
  |                     |
User (1) -------- (*) Billing_details
  |                     |
  | phone_no           | phone_no
  |                     |
  |                     |
User (1) -------- (*) Hourly_entries
  |                     |
  | phone_no           | phone_no
```

## Database Initialization

The database tables are created automatically when the application starts:

```python
with app.app_context():
    db.create_all()
```

## Data Seeding

When a new user registers, the system automatically creates:
1. User record with basic information
2. Details record with null values for optional fields
3. Appliances record with null values for all appliances
4. Three Billing_details records (current, previous, and last month) with mock data
5. Seven Hourly_entries records with mock daily consumption data

## Billing Calculation Functions

The application includes utility functions for bill calculations:

### Electricity Bill Calculation
```python
def calculate_electricity_bill(consumption):
    """
    Calculates electricity bill based on consumption tiers.
    
    Args:
        consumption (float): Electricity consumption in kWh
        
    Returns:
        float: Calculated bill amount
    """
    if consumption > 220 and consumption < 400:
        return 450 + (consumption - 250) * 4.5
    elif consumption > 400:
        return 1350 + (consumption - 450) * 6
```

### Water Bill Calculation
```python
def calculate_water_bill(consumption):
    """
    Calculates water bill based on consumption.
    
    Args:
        consumption (float): Water consumption in liters
        
    Returns:
        float: Calculated bill amount
    """
    consumption_in_kiloliters = consumption / 1000
    return consumption_in_kiloliters * 40
```

## Query Patterns

### Common Query Examples

```python
# Get user with details
user_details = db.session.query(User, Details).join(
    Details, User.phone_no == Details.phone_no
).filter(User.phone_no == phone_no).first()

# Get user's latest billing information
latest_bill = Billing_details.query.filter_by(
    phone_no=phone_no
).order_by(Billing_details.id.desc()).first()

# Get user's consumption history
consumption_history = Hourly_entries.query.filter_by(
    phone_no=phone_no
).order_by(Hourly_entries.date.desc()).limit(7).all()

# Get leaderboard data
leaderboard = db.session.query(User, Billing_details).join(
    Billing_details, User.phone_no == Billing_details.phone_no
).order_by(User.achievement_points.desc()).all()
```

## Performance Considerations

1. **Indexing**: The `phone_no` field is used extensively for joins and should be indexed
2. **Connection Pooling**: The custom SQLAlchemy class enables connection pool pre-ping for reliability
3. **Query Optimization**: Consider using eager loading for related data to reduce N+1 queries

## Security Considerations

1. **Password Storage**: Currently passwords are stored as plain text - consider implementing password hashing
2. **SQL Injection**: SQLAlchemy ORM provides protection against SQL injection
3. **Data Validation**: Implement proper input validation before database operations

## Backup and Migration

1. **Backup Strategy**: Implement regular PostgreSQL backups
2. **Migration**: Use Flask-Migrate for database schema changes
3. **Data Integrity**: Implement foreign key constraints for better data integrity

## Database Schema Evolution

For production deployment, consider:
1. Adding foreign key constraints
2. Implementing proper indexing strategy
3. Adding data validation constraints
4. Implementing audit trails for data changes
5. Adding created_at and updated_at timestamps