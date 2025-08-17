# Ecotrack - Environmental Consumption Tracking System

[![Python](https://img.shields.io/badge/Python-3.7+-blue.svg)](https://python.org)
[![Flask](https://img.shields.io/badge/Flask-3.0.0-green.svg)](https://flask.palletsprojects.com/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-12+-blue.svg)](https://postgresql.org)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

Ecotrack is a comprehensive Flask-based REST API system designed to help users monitor, track, and reduce their water and electricity consumption. The application provides detailed analytics, goal setting, achievement tracking, and community leaderboards to promote environmental consciousness and sustainable living practices.

## 🌟 Features

### 📊 Consumption Tracking
- **Real-time Monitoring**: Track daily water and electricity usage with morning/afternoon breakdowns
- **Historical Data**: Access up to 3 months of billing history with detailed consumption patterns
- **Appliance Management**: Catalog household appliances to estimate consumption impact
- **Smart Calculations**: Automated bill calculations based on regional utility pricing structures

### 🎯 Goal Setting & Achievements
- **Personal Goals**: Set custom consumption reduction targets
- **Achievement Points**: Earn points for meeting conservation goals
- **Progress Tracking**: Monitor improvement over time with detailed comparison analytics
- **Community Contributions**: Participate in environmental initiatives

### 🏆 Social Features
- **Leaderboards**: Compare your conservation efforts with other users
- **Community Engagement**: Share achievements and contribute to environmental causes
- **Gamification**: Point-based system to encourage sustainable behavior

### 📱 API-First Design
- **RESTful Architecture**: Clean, well-documented API endpoints
- **JSON Communication**: Standardized data exchange format
- **Session Authentication**: Secure user authentication system
- **CORS Support**: Cross-origin resource sharing for web applications

## 🚀 Quick Start

### Prerequisites
- Python 3.7+
- PostgreSQL 12+
- Git

### Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd ecotrack
   ```

2. **Create virtual environment**
   ```bash
   python -m venv venv
   source venv/bin/activate  # Linux/macOS
   # or
   venv\Scripts\activate     # Windows
   ```

3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

4. **Set up PostgreSQL database**
   ```bash
   sudo -u postgres psql
   CREATE DATABASE ecotrack;
   CREATE USER ecotrack_user WITH PASSWORD 'your_password';
   GRANT ALL PRIVILEGES ON DATABASE ecotrack TO ecotrack_user;
   \q
   ```

5. **Configure database connection**
   
   Update line 26 in `main.py`:
   ```python
   app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://ecotrack_user:your_password@localhost:5432/ecotrack'
   ```

6. **Run the application**
   ```bash
   python main.py
   ```

The API will be available at `http://localhost:5000`

## 📖 Documentation

### Complete Documentation Suite

- **[API Documentation](API_DOCUMENTATION.md)** - Comprehensive API reference with examples
- **[Database Documentation](DATABASE_DOCUMENTATION.md)** - Database schema, models, and relationships
- **[Functions Documentation](FUNCTIONS_DOCUMENTATION.md)** - Detailed function reference
- **[Setup Documentation](SETUP_DOCUMENTATION.md)** - Complete installation and deployment guide

### Quick API Reference

#### Authentication
```bash
# Register new user
POST /register
{
  "phone_no": "1234567890",
  "password": "securepass",
  "firstname": "John",
  "lastname": "Doe"
}

# Login
POST /login
{
  "phone_no": "1234567890",
  "password": "securepass"
}
```

#### Data Retrieval
```bash
# Get user details and appliances
GET /userdetails

# Get billing history (3 months)
GET /billdetails

# Get consumption comparison
GET /electricitybillcomparison
GET /waterbillcomparison

# Get leaderboard
GET /leaderdetails
```

## 🏗️ Architecture

### Technology Stack

- **Backend Framework**: Flask 3.0.0
- **Database**: PostgreSQL with SQLAlchemy ORM
- **Authentication**: Session-based with Flask sessions
- **API Format**: RESTful JSON APIs
- **Production Server**: Gunicorn WSGI server
- **Cross-Origin**: Flask-CORS for web application support

### Database Schema

The application uses 5 main database models:

1. **User** - Authentication and profile data
2. **Details** - Extended user information and utility accounts
3. **Appliances** - Household appliance inventory
4. **Billing_details** - Historical consumption and billing data
5. **Hourly_entries** - Daily consumption tracking

### Key Components

```
├── main.py                     # Main application file
├── requirements.txt            # Python dependencies
├── API_DOCUMENTATION.md        # API reference
├── DATABASE_DOCUMENTATION.md   # Database schema
├── FUNCTIONS_DOCUMENTATION.md  # Function reference
├── SETUP_DOCUMENTATION.md      # Installation guide
└── instance/                   # Database files (SQLite for development)
```

## 🔧 Development

### Running in Development Mode

```bash
# Enable debug mode
export FLASK_ENV=development
export FLASK_DEBUG=1
python main.py
```

### Testing

```bash
# Run unit tests (create test_app.py first)
python -m unittest test_app.py

# With coverage
pip install coverage
coverage run -m unittest test_app.py
coverage report
```

### Code Quality

The application follows Python best practices:
- PEP 8 code style
- Comprehensive documentation
- Error handling
- Input validation
- Security considerations

## 🚀 Production Deployment

### Using Gunicorn

```bash
# Install gunicorn
pip install gunicorn

# Run with multiple workers
gunicorn --bind 0.0.0.0:5000 --workers 4 main:app
```

### Docker Deployment

```bash
# Build and run with Docker Compose
docker-compose up --build -d
```

### Cloud Platforms

- **Heroku**: One-click deployment with PostgreSQL addon
- **AWS**: Elastic Beanstalk or EC2 with RDS
- **Google Cloud**: App Engine with Cloud SQL
- **DigitalOcean**: App Platform with Managed Database

See [SETUP_DOCUMENTATION.md](SETUP_DOCUMENTATION.md) for detailed deployment instructions.

## 📊 API Endpoints Overview

| Endpoint | Method | Description | Auth Required |
|----------|--------|-------------|---------------|
| `/register` | POST | Create new user account | No |
| `/login` | POST | Authenticate user | No |
| `/logout` | GET | End user session | Yes |
| `/updateuserdetails` | POST | Update profile information | Yes |
| `/updateapplaincedetails` | POST | Update appliance inventory | Yes |
| `/goal` | POST | Set consumption goal | Yes |
| `/userdetails` | GET | Get user profile | Yes |
| `/billdetails` | GET | Get billing history | Yes |
| `/pointsdetails` | GET | Get achievement points | Yes |
| `/goaldetails` | GET | Get current goal | Yes |
| `/leaderdetails` | GET | Get leaderboard | Yes |
| `/hourlydetails` | GET | Get daily consumption | Yes |
| `/electricitybillcomparison` | GET | Compare electricity usage | Yes |
| `/waterbillcomparison` | GET | Compare water usage | Yes |

## 🔒 Security Features

- **Session-based Authentication**: Secure user sessions
- **Input Validation**: SQL injection protection via SQLAlchemy ORM
- **CORS Support**: Configurable cross-origin policies
- **Environment Configuration**: Sensitive data via environment variables
- **Connection Pooling**: Database connection management

### Security Recommendations for Production

1. **Password Hashing**: Implement bcrypt for password storage
2. **Rate Limiting**: Add request rate limiting
3. **HTTPS**: Use SSL/TLS encryption
4. **Environment Variables**: Store secrets securely
5. **Database Security**: Restrict database access
6. **Input Sanitization**: Validate all user inputs

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

### Development Guidelines

- Follow PEP 8 style guide
- Add tests for new features
- Update documentation
- Use meaningful commit messages
- Ensure backward compatibility

## 🐛 Issue Reporting

When reporting issues, please include:
- Python version
- Operating system
- Error messages or logs
- Steps to reproduce
- Expected vs actual behavior

## 📈 Roadmap

### Upcoming Features

- [ ] **Mobile App Integration**: React Native mobile application
- [ ] **Advanced Analytics**: Machine learning consumption predictions
- [ ] **IoT Integration**: Smart meter data integration
- [ ] **Social Features**: User groups and challenges
- [ ] **Reporting**: PDF bill generation and reports
- [ ] **Notifications**: Email/SMS alerts for high consumption
- [ ] **Multi-tenant**: Support for utility companies
- [ ] **API Versioning**: Backward-compatible API evolution

### Performance Improvements

- [ ] **Caching Layer**: Redis caching for frequently accessed data
- [ ] **Database Optimization**: Query optimization and indexing
- [ ] **Background Tasks**: Celery for asynchronous processing
- [ ] **CDN Integration**: Static asset delivery optimization
- [ ] **Load Balancing**: Multi-instance deployment support

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- **Flask Community**: For the excellent web framework
- **SQLAlchemy**: For the powerful ORM
- **PostgreSQL**: For reliable database management
- **Open Source Community**: For tools and inspiration

## 📞 Support

- **Documentation**: Comprehensive guides in the `/docs` folder
- **Issues**: [GitHub Issues](../../issues) for bug reports
- **Discussions**: [GitHub Discussions](../../discussions) for questions
- **Email**: [support@ecotrack.com](mailto:support@ecotrack.com)

---

**Built with ❤️ for a sustainable future**

*Ecotrack helps individuals and communities make informed decisions about their environmental impact through detailed consumption tracking and gamified conservation goals.*
