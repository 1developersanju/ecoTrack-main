# Ecotrack - Environmental Consumption Tracking System

## Overview

Ecotrack is a comprehensive Flask-based web application designed to help users track and manage their electricity and water consumption. The system provides detailed analytics, billing calculations, environmental goal setting, and community features to encourage sustainable practices.

**Version:** 1.0.0  
**Framework:** Flask 3.0.0  
**Database:** PostgreSQL with SQLAlchemy ORM  
**Author:** Xgen25

## 🚀 Features

### Core Functionality
- **User Management:** Registration, authentication, and profile management
- **Consumption Tracking:** Electricity and water usage monitoring
- **Billing Analysis:** Tiered pricing calculations and historical data
- **Goal Setting:** Environmental reduction targets and progress tracking
- **Community Features:** Leaderboards and consumption comparisons
- **Real-time Data:** Hourly consumption tracking and trend analysis

### Technical Features
- **RESTful API:** JSON-based API endpoints
- **Session Management:** Secure server-side session handling
- **Database Optimization:** Connection pooling and query optimization
- **CORS Support:** Cross-origin resource sharing enabled
- **Cloud Hosting:** PostgreSQL database on Neon.tech

## 📚 Documentation

This repository contains comprehensive documentation for all aspects of the Ecotrack system:

### 1. [API Documentation](API_DOCUMENTATION.md)
Complete API reference with:
- All endpoint descriptions and parameters
- Request/response examples
- Authentication requirements
- Error handling
- Usage examples and workflows

### 2. [Function Reference](FUNCTION_REFERENCE.md)
Detailed function documentation including:
- All database models and their attributes
- Utility functions and calculations
- Route function implementations
- Database operations and session management
- Error handling and security features

### 3. [Component Documentation](COMPONENT_DOCUMENTATION.md)
System architecture and components:
- High-level system architecture
- Component layers and dependencies
- Security and performance components
- Deployment and configuration
- Future enhancements and improvements

## 🏗️ System Architecture

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

## 🚀 Quick Start

### Prerequisites
- Python 3.8+
- PostgreSQL database
- pip package manager

### Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd ecotrack
   ```

2. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

3. **Configure database**
   ```python
   # Update database URI in main.py
   app.config['SQLALCHEMY_DATABASE_URI'] = 'your_database_connection_string'
   ```

4. **Run the application**
   ```bash
   python main.py
   ```

### Development
```bash
python main.py
```

### Production
```bash
gunicorn -w 4 -b 0.0.0.0:8000 main:app
```

## 📊 API Endpoints

### Authentication
- `POST /register` - User registration
- `POST /login` - User authentication
- `GET /logout` - Session termination

### User Management
- `POST /updateuserdetails` - Update profile information
- `POST /updateapplaincedetails` - Update appliance inventory
- `POST /goal` - Set environmental goals

### Data Retrieval
- `GET /userdetails` - User profile and appliances
- `GET /billdetails` - Billing history
- `GET /pointsdetails` - Achievement points
- `GET /goaldetails` - Current goals
- `GET /hourlydetails` - Hourly consumption data

### Analytics
- `GET /leaderdetails` - Community leaderboard
- `GET /electricitybillcomparison` - Electricity consumption analysis
- `GET /waterbillcomparison` - Water consumption analysis

## 🗄️ Database Models

### Core Models
- **User:** Authentication and profile data
- **Details:** Extended user information
- **Appliances:** Household inventory
- **Billing_details:** Monthly billing records
- **Hourly_entries:** Daily consumption tracking

### Key Features
- Phone number-based authentication
- Achievement and contribution point system
- Environmental goal setting
- Comprehensive appliance tracking
- Historical consumption data

## 🔒 Security Features

- **Session Management:** Server-side session validation
- **Input Validation:** SQL injection prevention via SQLAlchemy
- **CORS Configuration:** Controlled cross-origin access
- **Database Security:** SSL connections and connection pooling
- **Authentication:** Phone number + password dual-factor system

## 📈 Performance Features

- **Connection Pooling:** Efficient database connection management
- **Query Optimization:** Selective field retrieval and indexing
- **Session Caching:** Reduced database queries
- **JSON Responses:** Fast client-side processing
- **Pre-ping Validation:** Connection health monitoring

## 🛠️ Dependencies

### Core Dependencies
- **Flask 3.0.0:** Web framework
- **Flask-SQLAlchemy 3.1.1:** Database ORM
- **Flask-CORS:** Cross-origin support
- **psycopg2-binary:** PostgreSQL adapter
- **gunicorn:** Production WSGI server

### Development Dependencies
- **Werkzeug 3.0.1:** WSGI utilities
- **Jinja2 3.1.2:** Template engine
- **SQLAlchemy 2.0.23:** Database toolkit

## 🔧 Configuration

### Environment Variables
```bash
export FLASK_ENV=production
export DATABASE_URL=your_database_url
export SECRET_KEY=your_secret_key
```

### Database Configuration
```python
app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://username:password@host/database?sslmode=require'
```

### CORS Configuration
```python
CORS(app)  # Enable cross-origin requests
```

## 📱 Usage Examples

### Complete User Workflow

1. **Register new user**
   ```python
   import requests
   
   response = requests.post('/register', json={
       "phone_no": "+1234567890",
       "password": "securepassword",
       "firstname": "John",
       "lastname": "Doe"
   })
   ```

2. **Login and establish session**
   ```python
   response = requests.post('/login', json={
       "phone_no": "+1234567890",
       "password": "securepassword"
   })
   ```

3. **Update profile and appliances**
   ```python
   # Update user details
   response = requests.post('/updateuserdetails', json={
       "water_consumer_no": "WC123456",
       "electricity_consumer_no": "EC789012",
       "Number_of_people": 4
   })
   
   # Update appliances
   response = requests.post('/updateapplaincedetails', json={
       "AC": 2, "TV": 3, "Lights": 12
   })
   ```

4. **Set environmental goals**
   ```python
   response = requests.post('/goal', json={"goal": 200})
   ```

5. **Retrieve consumption data**
   ```python
   # Get user details
   user_data = requests.get('/userdetails').json()
   
   # Get billing comparison
   comparison = requests.get('/electricitybillcomparison').json()
   ```

## 🧪 Testing

### Testing Strategy
- **Unit Testing:** Function and model validation
- **Integration Testing:** API endpoint testing
- **Performance Testing:** Load and stress testing

### Quality Metrics
- **Code Coverage:** Target 90%+ coverage
- **Performance:** Response time < 200ms
- **Security:** OWASP compliance
- **Documentation:** Complete API coverage

## 🚀 Deployment

### Development Environment
- Local Flask development server
- Debug mode enabled
- Auto-reload on changes

### Production Environment
- Gunicorn WSGI server
- Multiple worker processes
- Environment variable configuration
- SSL/TLS encryption
- Load balancing support

## 🔮 Future Enhancements

### Planned Features
- **Real-time Monitoring:** WebSocket support and live updates
- **Advanced Analytics:** Machine learning insights and predictions
- **Mobile Optimization:** Native mobile applications
- **IoT Integration:** Smart meter and sensor support

### Technical Improvements
- **Modular Architecture:** Service layer separation
- **Enhanced Security:** JWT authentication and rate limiting
- **Performance Optimization:** Redis caching and database indexing
- **Testing Framework:** Comprehensive test suite

## 📞 Support

For technical support or questions about the Ecotrack system:

1. **Review Documentation:** Start with the API documentation
2. **Check Logs:** Review Flask application logs
3. **Verify Configuration:** Ensure proper database and environment setup
4. **Test Endpoints:** Use the provided usage examples

## 📄 License

This project is proprietary software developed by Xgen25. All rights reserved.

## 🤝 Contributing

This is a proprietary system. For feature requests or bug reports, please contact the development team.

---

## 📋 Quick Reference

### Key Files
- **`main.py`** - Main application file with all routes and logic
- **`requirements.txt`** - Python dependencies
- **`API_DOCUMENTATION.md`** - Complete API reference
- **`FUNCTION_REFERENCE.md`** - Function and model documentation
- **`COMPONENT_DOCUMENTATION.md`** - System architecture and components

### Common Commands
```bash
# Install dependencies
pip install -r requirements.txt

# Run development server
python main.py

# Run production server
gunicorn -w 4 -b 0.0.0.0:8000 main:app

# Check database connection
python -c "from main import db; print('Database connected')"
```

### Database Schema
- **5 main models** with comprehensive relationships
- **Phone number-based** user identification
- **Historical data** for 3+ months
- **Hourly granularity** for consumption tracking

---

*For detailed information about any specific aspect of the Ecotrack system, refer to the appropriate documentation file listed above.*
