# Ecotrack Setup and Installation Guide

## Overview

This guide provides step-by-step instructions for setting up and deploying the Ecotrack application, a Flask-based water and electricity consumption tracking system.

## Prerequisites

### System Requirements

- **Python**: 3.7 or higher
- **PostgreSQL**: 12.0 or higher
- **Operating System**: Linux, macOS, or Windows
- **Memory**: Minimum 2GB RAM
- **Storage**: Minimum 1GB free space

### Required Tools

- Git (for cloning the repository)
- pip (Python package manager)
- PostgreSQL client tools
- Text editor or IDE

---

## Installation Steps

### 1. Clone the Repository

```bash
git clone <repository-url>
cd ecotrack
```

### 2. Create Virtual Environment

**Linux/macOS:**
```bash
python3 -m venv venv
source venv/bin/activate
```

**Windows:**
```bash
python -m venv venv
venv\Scripts\activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

**Dependencies Overview:**
- **Flask 3.0.0**: Web framework
- **Flask-SQLAlchemy 3.1.1**: Database ORM
- **Flask-Cors**: Cross-origin resource sharing
- **psycopg2-binary**: PostgreSQL adapter
- **gunicorn**: Production WSGI server

---

## Database Configuration

### 1. Install PostgreSQL

**Ubuntu/Debian:**
```bash
sudo apt update
sudo apt install postgresql postgresql-contrib
```

**macOS (using Homebrew):**
```bash
brew install postgresql
brew services start postgresql
```

**Windows:**
Download and install from [PostgreSQL official website](https://www.postgresql.org/download/windows/)

### 2. Create Database

```bash
# Connect to PostgreSQL
sudo -u postgres psql

# Create database and user
CREATE DATABASE ecotrack;
CREATE USER ecotrack_user WITH PASSWORD 'your_secure_password';
GRANT ALL PRIVILEGES ON DATABASE ecotrack TO ecotrack_user;

# Exit PostgreSQL
\q
```

### 3. Configure Database Connection

Update the database URI in `main.py` (line 26):

```python
app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://username:password@host:port/database?sslmode=require'
```

**Local Development Example:**
```python
app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://ecotrack_user:your_secure_password@localhost:5432/ecotrack'
```

**Production Example (with SSL):**
```python
app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://user:password@host.amazonaws.com:5432/ecotrack?sslmode=require'
```

### 4. Initialize Database Tables

The application automatically creates tables on startup. To manually initialize:

```python
from main import app, db

with app.app_context():
    db.create_all()
    print("Database tables created successfully!")
```

---

## Environment Configuration

### 1. Environment Variables

Create a `.env` file in the project root:

```bash
# Database Configuration
DATABASE_URL=postgresql://user:password@localhost:5432/ecotrack
DB_HOST=localhost
DB_PORT=5432
DB_NAME=ecotrack
DB_USER=ecotrack_user
DB_PASSWORD=your_secure_password

# Flask Configuration
FLASK_APP=main.py
FLASK_ENV=development
FLASK_DEBUG=True
SECRET_KEY=your_super_secret_key_here

# Application Settings
PORT=5000
HOST=0.0.0.0

# Security Settings
SESSION_TIMEOUT=3600
CORS_ORIGINS=*
```

### 2. Update Application Configuration

Modify `main.py` to use environment variables:

```python
import os
from dotenv import load_dotenv

load_dotenv()

# Database configuration
app.config['SQLALCHEMY_DATABASE_URI'] = os.getenv('DATABASE_URL')

# Security configuration
app.secret_key = os.getenv('SECRET_KEY', 'fallback-secret-key')

# Additional configurations
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
app.config['SESSION_PERMANENT'] = False
app.config['PERMANENT_SESSION_LIFETIME'] = int(os.getenv('SESSION_TIMEOUT', 3600))
```

### 3. Install Additional Dependencies

```bash
pip install python-dotenv
```

---

## Running the Application

### Development Mode

```bash
# Activate virtual environment
source venv/bin/activate  # Linux/macOS
# or
venv\Scripts\activate     # Windows

# Set environment variables
export FLASK_APP=main.py
export FLASK_ENV=development

# Run the application
python main.py
```

The application will be available at `http://localhost:5000`

### Production Mode

#### Using Gunicorn (Recommended)

```bash
# Install gunicorn (already in requirements.txt)
pip install gunicorn

# Run with gunicorn
gunicorn --bind 0.0.0.0:5000 --workers 4 main:app
```

#### Advanced Gunicorn Configuration

Create `gunicorn.conf.py`:

```python
# Gunicorn configuration file
bind = "0.0.0.0:5000"
workers = 4
worker_class = "sync"
worker_connections = 1000
max_requests = 1000
max_requests_jitter = 100
timeout = 30
keepalive = 2
preload_app = True

# Logging
accesslog = "logs/access.log"
errorlog = "logs/error.log"
loglevel = "info"

# Process naming
proc_name = "ecotrack"

# Security
limit_request_line = 4094
limit_request_fields = 100
limit_request_field_size = 8190
```

Run with configuration:
```bash
gunicorn --config gunicorn.conf.py main:app
```

---

## Docker Deployment

### 1. Create Dockerfile

```dockerfile
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y \
    gcc \
    postgresql-client \
    && rm -rf /var/lib/apt/lists/*

# Copy requirements and install Python dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY . .

# Create logs directory
RUN mkdir -p logs

# Expose port
EXPOSE 5000

# Run application
CMD ["gunicorn", "--config", "gunicorn.conf.py", "main:app"]
```

### 2. Create docker-compose.yml

```yaml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "5000:5000"
    environment:
      - DATABASE_URL=postgresql://ecotrack:password@db:5432/ecotrack
      - FLASK_ENV=production
      - SECRET_KEY=your-production-secret-key
    depends_on:
      - db
    volumes:
      - ./logs:/app/logs

  db:
    image: postgres:13
    environment:
      - POSTGRES_DB=ecotrack
      - POSTGRES_USER=ecotrack
      - POSTGRES_PASSWORD=password
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    ports:
      - "5432:5432"

volumes:
  postgres_data:
```

### 3. Deploy with Docker

```bash
# Build and run
docker-compose up --build -d

# View logs
docker-compose logs -f

# Stop services
docker-compose down
```

---

## Cloud Deployment

### 1. Heroku Deployment

#### Prerequisites
- Heroku CLI installed
- Heroku account

#### Steps

```bash
# Login to Heroku
heroku login

# Create Heroku app
heroku create ecotrack-app

# Add PostgreSQL addon
heroku addons:create heroku-postgresql:hobby-dev

# Set environment variables
heroku config:set SECRET_KEY=your-secret-key
heroku config:set FLASK_ENV=production

# Deploy
git push heroku main

# Run database migrations
heroku run python -c "from main import app, db; app.app_context().push(); db.create_all()"
```

#### Create Procfile

```
web: gunicorn main:app
```

### 2. AWS Deployment

#### Using Elastic Beanstalk

1. Install EB CLI:
```bash
pip install awsebcli
```

2. Initialize EB application:
```bash
eb init ecotrack
```

3. Create environment:
```bash
eb create ecotrack-env
```

4. Deploy:
```bash
eb deploy
```

#### Using EC2

1. Launch EC2 instance (Ubuntu 20.04 LTS)
2. Connect to instance and install dependencies:

```bash
sudo apt update
sudo apt install python3 python3-pip postgresql postgresql-contrib nginx

# Clone repository
git clone <repository-url>
cd ecotrack

# Install Python dependencies
pip3 install -r requirements.txt

# Configure PostgreSQL
sudo -u postgres createdb ecotrack
sudo -u postgres createuser ecotrack

# Configure Nginx (see Nginx section below)
```

---

## Web Server Configuration

### Nginx Configuration

Create `/etc/nginx/sites-available/ecotrack`:

```nginx
server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://127.0.0.1:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Static files (if any)
    location /static {
        alias /path/to/ecotrack/static;
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # Security headers
    add_header X-Frame-Options DENY;
    add_header X-Content-Type-Options nosniff;
    add_header X-XSS-Protection "1; mode=block";
}
```

Enable the site:
```bash
sudo ln -s /etc/nginx/sites-available/ecotrack /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

### SSL Configuration (Let's Encrypt)

```bash
# Install Certbot
sudo apt install certbot python3-certbot-nginx

# Obtain SSL certificate
sudo certbot --nginx -d your-domain.com

# Auto-renewal (add to crontab)
0 12 * * * /usr/bin/certbot renew --quiet
```

---

## Monitoring and Logging

### 1. Application Logging

Add logging configuration to `main.py`:

```python
import logging
from logging.handlers import RotatingFileHandler

if not app.debug:
    # Create logs directory
    if not os.path.exists('logs'):
        os.mkdir('logs')
    
    # Configure file handler
    file_handler = RotatingFileHandler('logs/ecotrack.log', maxBytes=10240, backupCount=10)
    file_handler.setFormatter(logging.Formatter(
        '%(asctime)s %(levelname)s: %(message)s [in %(pathname)s:%(lineno)d]'
    ))
    file_handler.setLevel(logging.INFO)
    app.logger.addHandler(file_handler)
    
    app.logger.setLevel(logging.INFO)
    app.logger.info('Ecotrack startup')
```

### 2. Database Monitoring

```sql
-- Monitor database connections
SELECT * FROM pg_stat_activity WHERE datname = 'ecotrack';

-- Monitor database size
SELECT pg_size_pretty(pg_database_size('ecotrack'));

-- Monitor table sizes
SELECT 
    schemaname,
    tablename,
    pg_size_pretty(pg_total_relation_size(schemaname||'.'||tablename)) as size
FROM pg_tables 
WHERE schemaname = 'public'
ORDER BY pg_total_relation_size(schemaname||'.'||tablename) DESC;
```

### 3. System Monitoring

Install monitoring tools:

```bash
# Install htop for system monitoring
sudo apt install htop

# Install PostgreSQL monitoring
sudo apt install postgresql-contrib

# Monitor application processes
ps aux | grep gunicorn
```

---

## Security Configuration

### 1. Database Security

```sql
-- Create read-only user for reporting
CREATE USER ecotrack_readonly WITH PASSWORD 'readonly_password';
GRANT CONNECT ON DATABASE ecotrack TO ecotrack_readonly;
GRANT USAGE ON SCHEMA public TO ecotrack_readonly;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO ecotrack_readonly;

-- Restrict network access (pg_hba.conf)
# Allow local connections only
local   ecotrack        ecotrack                        md5
host    ecotrack        ecotrack        127.0.0.1/32    md5
```

### 2. Application Security

Update `main.py` with security enhancements:

```python
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address
import bcrypt

# Rate limiting
limiter = Limiter(
    app,
    key_func=get_remote_address,
    default_limits=["1000 per hour"]
)

# Password hashing
def hash_password(password):
    return bcrypt.hashpw(password.encode('utf-8'), bcrypt.gensalt())

def verify_password(password, hashed):
    return bcrypt.checkpw(password.encode('utf-8'), hashed)

# Apply rate limiting to sensitive endpoints
@app.route('/login', methods=['POST'])
@limiter.limit("5 per minute")
def login():
    # Implementation with password hashing
    pass
```

### 3. Firewall Configuration

```bash
# Configure UFW (Ubuntu)
sudo ufw enable
sudo ufw allow 22/tcp    # SSH
sudo ufw allow 80/tcp    # HTTP
sudo ufw allow 443/tcp   # HTTPS
sudo ufw deny 5432/tcp   # PostgreSQL (only allow local)
```

---

## Backup and Recovery

### 1. Database Backup

Create backup script (`backup.sh`):

```bash
#!/bin/bash

# Configuration
DB_NAME="ecotrack"
DB_USER="ecotrack"
BACKUP_DIR="/backups"
DATE=$(date +%Y%m%d_%H%M%S)

# Create backup directory
mkdir -p $BACKUP_DIR

# Perform backup
pg_dump -U $DB_USER -h localhost $DB_NAME > $BACKUP_DIR/ecotrack_$DATE.sql

# Compress backup
gzip $BACKUP_DIR/ecotrack_$DATE.sql

# Remove backups older than 30 days
find $BACKUP_DIR -name "ecotrack_*.sql.gz" -mtime +30 -delete

echo "Backup completed: ecotrack_$DATE.sql.gz"
```

### 2. Automated Backups

Add to crontab:
```bash
# Daily backup at 2 AM
0 2 * * * /path/to/backup.sh

# Weekly full backup
0 2 * * 0 /path/to/full_backup.sh
```

### 3. Recovery Process

```bash
# Restore from backup
gunzip ecotrack_20231225_020000.sql.gz
psql -U ecotrack -h localhost ecotrack < ecotrack_20231225_020000.sql
```

---

## Testing

### 1. Unit Tests

Create `test_app.py`:

```python
import unittest
from main import app, db, User, calculate_electricity_bill, calculate_water_bill

class EcotrackTestCase(unittest.TestCase):
    def setUp(self):
        app.config['TESTING'] = True
        app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///:memory:'
        self.app = app.test_client()
        
        with app.app_context():
            db.create_all()
    
    def tearDown(self):
        with app.app_context():
            db.drop_all()
    
    def test_electricity_bill_calculation(self):
        self.assertEqual(calculate_electricity_bill(350), 900)
        self.assertEqual(calculate_electricity_bill(500), 1650)
    
    def test_water_bill_calculation(self):
        self.assertEqual(calculate_water_bill(18000), 720)
    
    def test_registration(self):
        response = self.app.post('/register', json={
            'phone_no': '1234567890',
            'password': 'testpass',
            'firstname': 'Test',
            'lastname': 'User'
        })
        self.assertEqual(response.status_code, 200)

if __name__ == '__main__':
    unittest.main()
```

### 2. Run Tests

```bash
# Run unit tests
python -m unittest test_app.py

# Run with coverage
pip install coverage
coverage run -m unittest test_app.py
coverage report
coverage html
```

---

## Performance Optimization

### 1. Database Optimization

```sql
-- Add indexes for better performance
CREATE INDEX idx_user_phone_no ON "user"(phone_no);
CREATE INDEX idx_billing_phone_no ON billing_details(phone_no);
CREATE INDEX idx_billing_date ON billing_details(electricity_billing_date);
CREATE INDEX idx_hourly_phone_date ON hourly_entries(phone_no, date);

-- Analyze tables for query optimization
ANALYZE;
```

### 2. Application Optimization

```python
# Add caching
from flask_caching import Cache

cache = Cache(app, config={'CACHE_TYPE': 'simple'})

@app.route('/leaderdetails')
@cache.cached(timeout=300)  # Cache for 5 minutes
def leaderdetails():
    # Implementation
    pass
```

### 3. Connection Pooling

```python
# Configure connection pool
app.config['SQLALCHEMY_ENGINE_OPTIONS'] = {
    'pool_size': 10,
    'pool_recycle': 120,
    'pool_pre_ping': True,
    'max_overflow': 20
}
```

---

## Troubleshooting

### Common Issues

#### 1. Database Connection Error
```
Error: could not connect to server: Connection refused
```

**Solutions:**
- Check PostgreSQL service status: `sudo systemctl status postgresql`
- Verify database credentials
- Check firewall settings
- Ensure PostgreSQL is listening on correct port

#### 2. Import Errors
```
ModuleNotFoundError: No module named 'flask'
```

**Solutions:**
- Activate virtual environment
- Install requirements: `pip install -r requirements.txt`
- Check Python path

#### 3. Permission Denied
```
psycopg2.OperationalError: FATAL: permission denied for database
```

**Solutions:**
- Grant database permissions to user
- Check pg_hba.conf configuration
- Verify user credentials

### Debug Mode

Enable debug mode for development:

```python
if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0', port=5000)
```

### Logging Debug Information

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

---

## Maintenance

### 1. Regular Tasks

- **Daily**: Check application logs
- **Weekly**: Review database performance
- **Monthly**: Update dependencies
- **Quarterly**: Security audit

### 2. Update Process

```bash
# Backup database
./backup.sh

# Pull latest code
git pull origin main

# Update dependencies
pip install -r requirements.txt --upgrade

# Run migrations (if any)
python -c "from main import app, db; app.app_context().push(); db.create_all()"

# Restart application
sudo systemctl restart ecotrack
```

### 3. Health Checks

Create health check endpoint:

```python
@app.route('/health')
def health_check():
    try:
        # Check database connection
        db.session.execute('SELECT 1')
        return {'status': 'healthy', 'timestamp': time.time()}
    except Exception as e:
        return {'status': 'unhealthy', 'error': str(e)}, 500
```

---

## Support and Resources

### Documentation
- [Flask Documentation](https://flask.palletsprojects.com/)
- [SQLAlchemy Documentation](https://docs.sqlalchemy.org/)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)

### Community
- Flask Discord: [https://discord.gg/pallets](https://discord.gg/pallets)
- Stack Overflow: Tag questions with `flask`, `sqlalchemy`, `postgresql`

### Professional Support
For production deployments, consider:
- Database administration services
- Application monitoring (New Relic, DataDog)
- Security auditing services
- Performance optimization consulting