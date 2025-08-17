# Ecotrack Component Documentation

## Overview

Ecotrack is a comprehensive environmental tracking system built with Flask that monitors electricity and water consumption, provides billing analysis, and encourages sustainable practices through goal setting and community features.

## System Architecture

### High-Level Architecture
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Client Apps   │    │   Web Browser   │    │   Mobile Apps   │
└─────────────────┘    └─────────────────┘    └─────────────────┘
           │                       │                       │
           └───────────────────────┼───────────────────────┘
                                   │
                    ┌─────────────────────────────────┐
                    │         Flask Web API           │
                    │         (main.py)              │
                    └─────────────────────────────────┘
                                   │
                    ┌─────────────────────────────────┐
                    │      SQLAlchemy ORM Layer      │
                    └─────────────────────────────────┘
                                   │
                    ┌─────────────────────────────────┐
                    │      PostgreSQL Database       │
                    │    (Neon.tech hosted)         │
                    └─────────────────────────────────┘
```

### Component Layers

#### 1. Presentation Layer
- **Flask Web Framework:** Handles HTTP requests and responses
- **CORS Support:** Enables cross-origin resource sharing
- **Session Management:** Server-side session handling
- **JSON API:** RESTful API endpoints

#### 2. Business Logic Layer
- **User Management:** Registration, authentication, profile updates
- **Consumption Tracking:** Electricity and water usage monitoring
- **Billing Calculations:** Tiered pricing and consumption analysis
- **Goal Setting:** Environmental reduction targets
- **Community Features:** Leaderboards and comparisons

#### 3. Data Access Layer
- **SQLAlchemy ORM:** Database abstraction and query building
- **Connection Pooling:** Efficient database connection management
- **Model Definitions:** Database schema and relationships
- **Transaction Management:** ACID compliance for data integrity

#### 4. Data Storage Layer
- **PostgreSQL Database:** Relational database for structured data
- **Cloud Hosting:** Neon.tech managed PostgreSQL service
- **SSL Connection:** Secure database communication
- **Backup & Recovery:** Managed by cloud provider

---

## Core Components

### 1. Flask Application (`app`)

#### Configuration
```python
app = Flask(__name__)
CORS(app)  # Enable cross-origin requests
app.secret_key = 'asdsdfsdfs13sdf_df%&'  # Session encryption
```

#### Features
- **Multi-threaded:** Handles concurrent user requests
- **Session-based:** Secure user authentication
- **JSON API:** RESTful endpoint design
- **Error Handling:** Standardized error responses

#### Security Features
- **Session Validation:** All protected endpoints require valid session
- **Input Sanitization:** SQLAlchemy prevents injection attacks
- **CORS Configuration:** Controlled cross-origin access
- **Secret Key:** Encrypted session data

### 2. Database Engine (`db`)

#### Custom SQLAlchemy Configuration
```python
class SQLAlchemy(_BaseSQLAlchemy):
    def apply_pool_defaults(self, app, options):
        super(SQLAlchemy, self).apply_pool_defaults(self, app, options)
        options["pool_pre_ping"] = True
```

#### Connection Management
- **Connection Pooling:** Efficient resource utilization
- **Pre-ping Validation:** Ensures connection health
- **Automatic Reconnection:** Handles network interruptions
- **SSL Support:** Secure database communication

#### Database Configuration
```python
app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://username:password@host/database?sslmode=require'
```

### 3. Data Models

#### User Model (`User`)
**Purpose:** Core user authentication and profile data
**Key Features:**
- Phone number-based authentication
- Achievement and contribution point system
- Environmental goal setting
- Unique user identification

#### Details Model (`Details`)
**Purpose:** Extended user profile information
**Key Features:**
- Geographic location (lat/lng)
- Utility provider information
- Consumer account numbers
- Household demographics

#### Appliances Model (`Appliances`)
**Purpose:** Household appliance inventory
**Key Features:**
- Comprehensive appliance catalog
- Quantity tracking
- Energy consumption correlation
- Maintenance planning support

#### Billing Details Model (`Billing_details`)
**Purpose:** Monthly consumption and billing records
**Key Features:**
- Historical consumption data
- Billing cycle management
- Utility provider tracking
- Payment status monitoring

#### Hourly Entries Model (`Hourly_entries`)
**Purpose:** Daily consumption granularity
**Key Features:**
- Morning/afternoon consumption split
- Daily consumption patterns
- Trend analysis support
- Real-time monitoring capability

---

## API Endpoint Components

### 1. Authentication Endpoints

#### Registration System (`/register`)
**Component Purpose:** User onboarding and data initialization
**Key Features:**
- Phone number uniqueness validation
- Mock data generation for new users
- Comprehensive profile setup
- Initial goal establishment

**Data Flow:**
```
User Input → Validation → Mock Data Generation → Database Creation → Response
```

**Mock Data Generation:**
- **Electricity:** 250-500 kWh range with 5% monthly variation
- **Water:** 18,000-20,000 liters with 2.5% monthly variation
- **Historical Data:** 3 months of billing records
- **Hourly Data:** 7 days of consumption entries

#### Login System (`/login`)
**Component Purpose:** User authentication and session management
**Key Features:**
- Credential validation
- Session creation
- Secure cookie handling
- Authentication state management

**Security Flow:**
```
Credentials → Database Query → Session Creation → Authentication State
```

#### Logout System (`/logout`)
**Component Purpose:** Session termination and cleanup
**Key Features:**
- Session invalidation
- Cookie cleanup
- Security state reset
- Resource cleanup

### 2. Profile Management Endpoints

#### User Details Update (`/updateuserdetails`)
**Component Purpose:** Profile information management
**Key Features:**
- Multi-table synchronization
- Consumer number updates
- Utility provider management
- Geographic data handling

**Synchronization Logic:**
```python
# Update user details
user_details = Details.query.get_or_404(phone_no)
user_details.update(request_data)

# Synchronize billing records
billing_records = Billing_details.query.filter_by(phone_no=phone_no).all()
for record in billing_records:
    record.update(consumer_data)
```

#### Appliance Management (`/updateapplaincedetails`)
**Component Purpose:** Household inventory tracking
**Key Features:**
- Comprehensive appliance catalog
- Quantity updates
- Energy consumption correlation
- Maintenance planning

### 3. Data Retrieval Endpoints

#### User Profile Data (`/userdetails`)
**Component Purpose:** Comprehensive user information retrieval
**Key Features:**
- Profile and appliance data aggregation
- Structured JSON response
- Optimized data fetching
- Cached session validation

**Response Structure:**
```json
{
  "Applainces": [{"name": "string", "count": "integer"}],
  "details": {
    "profile_fields": "values",
    "utility_info": "data"
  }
}
```

#### Billing History (`/billdetails`)
**Component Purpose:** Historical consumption and billing data
**Key Features:**
- 3-month billing history
- Payment status tracking
- Consumption trends
- Utility provider information

#### Consumption Analytics (`/hourlydetails`)
**Component Purpose:** Granular consumption tracking
**Key Features:**
- Daily consumption patterns
- Morning/afternoon splits
- Trend analysis support
- Real-time monitoring

### 4. Analytics and Comparison Endpoints

#### Electricity Comparison (`/electricitybillcomparison`)
**Component Purpose:** Monthly consumption analysis
**Key Features:**
- Month-over-month comparison
- Percentage change calculation
- Consumption difference analysis
- Billing impact assessment

**Calculation Logic:**
```python
consumption_diff = current_month - previous_month
percentage_change = (consumption_diff / previous_month) * 100
```

#### Water Comparison (`/waterbillcomparison`)
**Component Purpose:** Water consumption analysis
**Key Features:**
- Monthly consumption tracking
- Usage pattern analysis
- Billing impact calculation
- Conservation opportunity identification

#### Community Leaderboard (`/leaderdetails`)
**Component Purpose:** Community engagement and competition
**Key Features:**
- Achievement point rankings
- Consumption comparisons
- Community benchmarking
- Motivation and engagement

**Data Aggregation:**
```python
users = User.query.all()
leaderboard = []
for user in users:
    consumption = Billing_details.query.filter_by(phone_no=user.phone_no).first()
    leaderboard.append({
        "phone_no": user.phone_no,
        "username": user.fullname,
        "achievement_points": user.achievement_points,
        "consumption_data": consumption
    })
```

### 5. Goal Management Endpoints

#### Goal Setting (`/goal`)
**Component Purpose:** Environmental target establishment
**Key Features:**
- Consumption reduction targets
- Goal validation
- Progress tracking
- Achievement motivation

#### Goal Retrieval (`/goaldetails`)
**Component Purpose:** Current goal information
**Key Features:**
- Goal value retrieval
- Progress assessment
- Target validation
- Achievement planning

---

## Utility Components

### 1. Billing Calculation Engine

#### Electricity Bill Calculator
**Function:** `calculate_electricity_bill(bill)`
**Purpose:** Tiered pricing calculation
**Tier Structure:**
- **0-220 kWh:** Base rate (implementation pending)
- **220-400 kWh:** 450 + (consumption-250) × 4.5
- **400+ kWh:** 1350 + (consumption-450) × 6

**Usage Example:**
```python
# 300 kWh consumption
bill = calculate_electricity_bill(300)
# Result: 450 + (300-250)*4.5 = 675

# 500 kWh consumption
bill = calculate_electricity_bill(500)
# Result: 1350 + (500-450)*6 = 1650
```

#### Water Bill Calculator
**Function:** `calculate_water_bill(bill)`
**Purpose:** Volume-based pricing
**Rate Structure:** 40 per 1000 liters
**Formula:** (consumption / 1000) × 40

**Usage Example:**
```python
# 20,000 liters consumption
bill = calculate_water_bill(20000)
# Result: (20000/1000) * 40 = 800

# 15,000 liters consumption
bill = calculate_water_bill(15000)
# Result: (15000/1000) * 40 = 600
```

### 2. Data Generation Engine

#### Mock Data Generation
**Purpose:** New user onboarding with realistic data
**Features:**
- **Randomization:** Realistic consumption variations
- **Seasonal Patterns:** Monthly consumption trends
- **Historical Context:** 3-month billing history
- **Hourly Granularity:** Daily consumption patterns

**Generation Logic:**
```python
# Electricity consumption with 5% monthly variation
base_consumption = randint(250, 500)
month1 = base_consumption
month2 = base_consumption + base_consumption * 0.05
month3 = base_consumption + base_consumption * 0.025

# Water consumption with 2.5% monthly variation
base_water = randint(18000, 20000)
water1 = base_water
water2 = base_water + base_water * 0.05
water3 = base_water + base_water * 0.025
```

---

## Security Components

### 1. Session Management

#### Session Configuration
```python
app.secret_key = 'asdsdfsdfs13sdf_df%&'
```

**Security Features:**
- **Encryption:** Session data encryption
- **Validation:** Session integrity checking
- **Expiration:** Automatic session timeout
- **Cleanup:** Secure session termination

#### Session Validation Pattern
```python
if 'phone_no' not in session:
    return json.dumps({'success': False}), 401, {'ContentType': 'application/json'}
```

**Implementation:**
- Applied to all protected endpoints
- Consistent error response format
- Secure authentication state
- Resource access control

### 2. Input Validation

#### Phone Number Validation
**Purpose:** Prevent duplicate registrations
**Implementation:**
```python
if User.query.filter_by(phone_no=phone_no).first() is not None:
    return json.dumps({'success': "Phone number already exists!"}), 401
```

#### Credential Validation
**Purpose:** Secure authentication
**Implementation:**
```python
user = User.query.filter_by(phone_no=phone_no, password=password).first()
if user is None:
    return json.dumps({'success': "user is None"}), 401
```

### 3. Database Security

#### SQL Injection Prevention
**Method:** SQLAlchemy ORM
**Features:**
- Parameterized queries
- Input sanitization
- Type safety
- Query validation

#### Connection Security
**Features:**
- SSL encryption
- Connection pooling
- Pre-ping validation
- Secure credentials

---

## Performance Components

### 1. Database Optimization

#### Connection Pooling
**Purpose:** Efficient resource management
**Features:**
- Connection reuse
- Load balancing
- Connection validation
- Automatic cleanup

#### Query Optimization
**Methods:**
- **Indexing:** Primary key optimization
- **Selective Queries:** Only necessary fields
- **Batch Operations:** Efficient data updates
- **Caching:** Session-based caching

### 2. Response Optimization

#### JSON Response Format
**Benefits:**
- Fast client-side processing
- Minimal data transfer
- Standardized format
- Easy parsing

#### Session Caching
**Features:**
- Reduced database queries
- Faster response times
- User state persistence
- Resource efficiency

---

## Error Handling Components

### 1. HTTP Status Codes

#### Success Responses
- **200 OK:** Successful operation
- **201 Created:** Resource creation success

#### Error Responses
- **401 Unauthorized:** Authentication failure
- **404 Not Found:** Resource not found
- **500 Internal Server Error:** Server-side errors

### 2. Error Response Format

#### Standardized Format
```json
{
  "success": "error message or false"
}
```

#### Error Scenarios
1. **Session Expired:** 401 with session message
2. **Invalid Credentials:** 401 with credential message
3. **Resource Not Found:** 404 with resource message
4. **Validation Failure:** 401 with validation message

---

## Configuration Components

### 1. Environment Configuration

#### Database Configuration
```python
app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://username:password@host/database?sslmode=require'
```

#### Application Configuration
```python
app.secret_key = 'your-secret-key'
app.config['DEBUG'] = True  # Development only
```

### 2. CORS Configuration

#### Cross-Origin Support
```python
CORS(app)  # Enable all origins
```

**Configuration Options:**
- **Origins:** All origins allowed
- **Methods:** All HTTP methods
- **Headers:** Standard headers
- **Credentials:** Session cookies

---

## Deployment Components

### 1. Development Environment

#### Local Development
```bash
python main.py
```

**Features:**
- Debug mode enabled
- Auto-reload on changes
- Development server
- Local database

### 2. Production Environment

#### WSGI Server
```bash
gunicorn -w 4 -b 0.0.0.0:8000 main:app
```

**Configuration:**
- **Workers:** 4 concurrent processes
- **Binding:** All network interfaces
- **Port:** 8000
- **Process Management:** Automatic

#### Environment Variables
```bash
export FLASK_ENV=production
export DATABASE_URL=your_database_url
export SECRET_KEY=your_secret_key
```

---

## Monitoring and Maintenance

### 1. Application Monitoring

#### Logging
**Features:**
- Request logging
- Error tracking
- Performance metrics
- User activity

#### Health Checks
**Endpoints:**
- Database connectivity
- Session management
- API responsiveness
- Resource utilization

### 2. Database Maintenance

#### Connection Management
**Features:**
- Connection pooling
- Pre-ping validation
- Automatic reconnection
- Resource cleanup

#### Performance Monitoring
**Metrics:**
- Query execution time
- Connection pool usage
- Database response time
- Resource utilization

---

## Future Enhancements

### 1. Planned Features

#### Real-time Monitoring
- WebSocket support
- Live consumption updates
- Real-time notifications
- Instant feedback

#### Advanced Analytics
- Machine learning insights
- Predictive consumption
- Anomaly detection
- Trend forecasting

#### Mobile Optimization
- Native mobile apps
- Push notifications
- Offline support
- Mobile-specific UI

### 2. Technical Improvements

#### Code Organization
- Modular architecture
- Service layer separation
- Configuration management
- Testing framework

#### Security Enhancements
- JWT authentication
- Rate limiting
- Input validation
- Security headers

#### Performance Optimization
- Redis caching
- Database indexing
- Query optimization
- Load balancing

---

## Component Dependencies

### 1. External Dependencies

#### Flask Ecosystem
- **Flask 3.0.0:** Core web framework
- **Flask-SQLAlchemy 3.1.1:** Database ORM
- **Flask-CORS:** Cross-origin support
- **Werkzeug 3.0.1:** WSGI utilities

#### Database Dependencies
- **psycopg2-binary:** PostgreSQL adapter
- **SQLAlchemy 2.0.23:** Database toolkit
- **greenlet 3.0.1:** Async support

#### Production Dependencies
- **gunicorn:** WSGI server
- **blinker 1.7.0:** Event system
- **click 8.1.7:** Command line interface

### 2. Internal Dependencies

#### Model Dependencies
- **User → Details:** One-to-one relationship
- **User → Appliances:** One-to-one relationship
- **User → Billing_details:** One-to-many relationship
- **User → Hourly_entries:** One-to-many relationship

#### Function Dependencies
- **Route functions → Session validation:** Authentication dependency
- **Data functions → Database models:** Data access dependency
- **Utility functions → Calculation logic:** Business logic dependency

---

## Testing and Quality Assurance

### 1. Testing Strategy

#### Unit Testing
**Coverage Areas:**
- Utility functions
- Model validation
- Business logic
- Error handling

#### Integration Testing
**Coverage Areas:**
- API endpoints
- Database operations
- Session management
- Authentication flow

#### Performance Testing
**Coverage Areas:**
- Response times
- Database performance
- Concurrent users
- Resource utilization

### 2. Quality Metrics

#### Code Quality
- **Complexity:** Cyclomatic complexity
- **Coverage:** Test coverage percentage
- **Documentation:** API documentation completeness
- **Standards:** PEP 8 compliance

#### Performance Metrics
- **Response Time:** API endpoint performance
- **Throughput:** Requests per second
- **Resource Usage:** Memory and CPU utilization
- **Scalability:** Load handling capacity

---

*This component documentation provides a comprehensive overview of the Ecotrack system architecture, components, and technical implementation details. For specific implementation details, refer to the source code and API documentation.*