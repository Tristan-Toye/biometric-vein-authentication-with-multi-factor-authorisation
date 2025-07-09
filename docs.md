# Biometric Vein Authentication System - Technical Documentation

## Table of Contents

1. [Overview](#overview)
2. [System Architecture](#system-architecture)
3. [Installation Guide](#installation-guide)
4. [Configuration](#configuration)
5. [API Reference](#api-reference)
6. [Database Schema](#database-schema)
7. [Security Implementation](#security-implementation)
8. [Troubleshooting](#troubleshooting)
9. [Development Guide](#development-guide)

## Overview

The Biometric Vein Authentication System is a comprehensive security solution that combines face recognition and vein pattern recognition for high-security access control. The system operates on a distributed architecture with local authentication servers and cloud-based management interfaces.

### Key Components

- **Local Authentication Server**: Handles biometric verification and access control
- **MQTT Message Broker**: Facilitates communication between components
- **Cloud Management Interface**: Web-based administrative dashboard
- **Security Monitoring**: Real-time security alerts and logging
- **Exit Management**: QR-based exit authentication system

## System Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        Cloud Layer                              │
├─────────────────────────────────────────────────────────────────┤
│  Heroku App (Web Admin)  │  PostgreSQL Database  │  Logging     │
└─────────────────────────────────────────────────────────────────┘
                                    │
                                    │ HTTPS/WebSocket
                                    ▼
┌─────────────────────────────────────────────────────────────────┐
│                      Local Network Layer                        │
├─────────────────────────────────────────────────────────────────┤
│  Local Server (Flask UI)  │  MQTT Broker  │  Security App      │
└─────────────────────────────────────────────────────────────────┘
                                    │
                                    │ MQTT
                                    ▼
┌─────────────────────────────────────────────────────────────────┐
│                      Hardware Layer                             │
├─────────────────────────────────────────────────────────────────┤
│  Raspberry Pi  │  Camera  │  Vein Scanner  │  Display          │
└─────────────────────────────────────────────────────────────────┘
```

### Component Details

#### 1. Local Authentication Server (`UI/`)
- **Purpose**: Primary authentication interface
- **Technology**: Flask web application
- **Features**: 
  - Face recognition using OpenCV and face-recognition library
  - Vein pattern recognition
  - Real-time camera processing
  - Local database storage for biometric templates

#### 2. MQTT Message Broker (`MQTTT/`)
- **Purpose**: Inter-component communication
- **Technology**: MQTT protocol
- **Configuration**:
  - Broker Address: `192.168.137.1`
  - Port: `1883`
  - Authentication: Username/Password required

#### 3. Security Monitoring (`securityapp/`)
- **Purpose**: Real-time security monitoring and alerts
- **Technology**: Flask with WebSocket support
- **Features**:
  - MQTT message processing
  - Real-time security alerts
  - WebSocket-based live updates

#### 4. Cloud Management Interface (`online_web_app/`)
- **Purpose**: Administrative web interface
- **Technology**: Flask deployed on Heroku
- **Features**:
  - User management
  - Role-based access control
  - Authentication logs
  - System configuration

#### 5. Exit Management (`Leaving_UI/`)
- **Purpose**: Exit authentication and logging
- **Technology**: Flask application
- **Features**:
  - QR code generation and validation
  - Time-based authentication
  - Exit logging

## Installation Guide

### Prerequisites

#### System Requirements
- **Operating System**: Linux, Windows, or macOS
- **Python Version**: 3.7 or higher
- **Memory**: Minimum 4GB RAM
- **Storage**: 10GB available space
- **Network**: Local network with MQTT broker access

#### Hardware Requirements
- **Camera**: USB camera or built-in webcam
- **Vein Scanner**: Compatible vein pattern recognition device
- **Display**: Touchscreen or standard monitor
- **Network**: Ethernet or WiFi connection

### Step-by-Step Installation

#### 1. Environment Setup

```bash
# Create virtual environment
python -m venv biometric_auth_env
source biometric_auth_env/bin/activate  # Linux/macOS
# or
biometric_auth_env\Scripts\activate     # Windows

# Upgrade pip
pip install --upgrade pip
```

#### 2. Install Dependencies

```bash
# Install core dependencies
pip install Flask==2.0.2
pip install Flask-SQLAlchemy
pip install Flask-SocketIO
pip install Flask-User
pip install face-recognition
pip install opencv-python
pip install numpy==1.21.4
pip install Pillow==8.4.0
pip install qrcode
pip install pyotp==2.6.0
pip install psycopg2-binary
pip install gunicorn
```

#### 3. Database Setup

```bash
# Install PostgreSQL (Ubuntu/Debian)
sudo apt-get update
sudo apt-get install postgresql postgresql-contrib

# Create database
sudo -u postgres createdb biometric_auth_db
sudo -u postgres createuser biometric_user
sudo -u postgres psql -c "ALTER USER biometric_user PASSWORD 'secure_password';"
sudo -u postgres psql -c "GRANT ALL PRIVILEGES ON DATABASE biometric_auth_db TO biometric_user;"
```

#### 4. MQTT Broker Setup

```bash
# Install Mosquitto MQTT broker
sudo apt-get install mosquitto mosquitto-clients

# Configure Mosquitto
sudo nano /etc/mosquitto/mosquitto.conf

# Add configuration:
listener 1883
allow_anonymous false
password_file /etc/mosquitto/passwd

# Create password file
sudo mosquitto_passwd -c /etc/mosquitto/passwd CW2B2

# Restart Mosquitto
sudo systemctl restart mosquitto
```

### Configuration

#### Environment Variables

Create `.env` files in each component directory:

```bash
# Local UI (.env)
DATABASE_URL=postgresql://biometric_user:secure_password@localhost/biometric_auth_db
MQTT_BROKER_URL=192.168.137.1
MQTT_BROKER_PORT=1883
MQTT_USERNAME=CW2B2
MQTT_PASSWORD=KULeuven
SECRET_KEY=your_secret_key_here
```

#### Database Configuration

```python
# Database connection string format
SQLALCHEMY_DATABASE_URI = "postgresql://username:password@host:port/database_name"

# Example for local development
SQLALCHEMY_DATABASE_URI = "postgresql://biometric_user:secure_password@localhost:5432/biometric_auth_db"
```

#### MQTT Configuration

```python
# MQTT settings
MQTT_CLIENT_ID = 'your_client_id'
MQTT_BROKER_URL = '192.168.137.1'
MQTT_BROKER_PORT = 1883
MQTT_USERNAME = 'CW2B2'
MQTT_PASSWORD = 'KULeuven'
MQTT_KEEPALIVE = 5
```

## API Reference

### Authentication Endpoints

#### POST /auth/face
Face recognition authentication endpoint.

**Request Body:**
```json
{
  "image_data": "base64_encoded_image",
  "user_id": "string"
}
```

**Response:**
```json
{
  "success": true,
  "user": {
    "id": 1,
    "username": "John Doe",
    "roles": ["staff"]
  },
  "confidence": 0.95
}
```

#### POST /auth/vein
Vein pattern authentication endpoint.

**Request Body:**
```json
{
  "vein_data": "base64_encoded_vein_pattern",
  "user_id": "string"
}
```

**Response:**
```json
{
  "success": true,
  "user": {
    "id": 1,
    "username": "John Doe",
    "roles": ["staff"]
  },
  "confidence": 0.92
}
```

#### POST /auth/qr
QR code validation endpoint.

**Request Body:**
```json
{
  "qr_code": "encoded_qr_string",
  "timestamp": "2024-01-01T12:00:00Z"
}
```

**Response:**
```json
{
  "success": true,
  "valid": true,
  "expires_at": "2024-01-01T12:05:00Z"
}
```

### User Management Endpoints

#### GET /api/users
Retrieve all users (admin only).

**Headers:**
```
Authorization: Bearer <token>
```

**Response:**
```json
{
  "users": [
    {
      "id": 1,
      "username": "John Doe",
      "email": "john@example.com",
      "roles": ["staff"],
      "created_at": "2024-01-01T00:00:00Z"
    }
  ]
}
```

#### POST /api/users
Create new user (admin only).

**Request Body:**
```json
{
  "username": "Jane Doe",
  "email": "jane@example.com",
  "password": "secure_password",
  "national_number": "123456789",
  "roles": ["staff"]
}
```

#### PUT /api/users/{user_id}
Update user (admin only).

#### DELETE /api/users/{user_id}
Delete user (admin only).

## Database Schema

### Users Table
```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(255) NOT NULL,
    national_number VARCHAR(255) NOT NULL UNIQUE,
    email_address VARCHAR(255) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    faces BYTEA,  -- Pickled face encodings
    qr_leave VARCHAR(255) NOT NULL UNIQUE,
    qr_leave_code VARCHAR(255) NOT NULL UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Roles Table
```sql
CREATE TABLE roles (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL UNIQUE,
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### User Roles Table
```sql
CREATE TABLE user_roles (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    role_id INTEGER REFERENCES roles(id) ON DELETE CASCADE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Authentication Logs Table
```sql
CREATE TABLE auth_logs (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id),
    auth_type VARCHAR(50) NOT NULL,  -- 'face', 'vein', 'qr'
    success BOOLEAN NOT NULL,
    confidence DECIMAL(5,4),
    ip_address INET,
    user_agent TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## Security Implementation

### Authentication Flow

1. **Biometric Capture**: Camera captures face image, vein scanner captures pattern
2. **Template Generation**: Biometric data converted to mathematical templates
3. **Template Comparison**: Stored templates compared with captured data
4. **Confidence Scoring**: Similarity score calculated (0.0 to 1.0)
5. **Decision Making**: Access granted if confidence exceeds threshold (typically 0.8)

### Security Measures

#### Data Encryption
- **Biometric Templates**: Encrypted using AES-256
- **Passwords**: Hashed using bcrypt with salt
- **Database Connections**: SSL/TLS encrypted
- **MQTT Communication**: TLS encryption (optional)

#### Access Control
- **Role-Based Access Control (RBAC)**: Four distinct roles
- **Session Management**: Secure session handling with expiration
- **Input Validation**: Comprehensive sanitization of all inputs
- **Rate Limiting**: Prevents brute force attacks

#### Network Security
- **Local Network Isolation**: MQTT communication on private network
- **Firewall Configuration**: Restricted access to necessary ports only
- **VPN Access**: Secure remote access for administrators

### Security Best Practices

1. **Regular Updates**: Keep all dependencies updated
2. **Log Monitoring**: Monitor authentication logs for suspicious activity
3. **Backup Strategy**: Regular encrypted backups of biometric data
4. **Incident Response**: Documented procedures for security incidents
5. **Audit Trail**: Comprehensive logging of all system activities

## Troubleshooting

### Common Issues

#### 1. Face Recognition Not Working
**Symptoms**: Face recognition fails consistently
**Solutions**:
- Check camera permissions
- Verify lighting conditions
- Ensure face is clearly visible
- Check face-recognition library installation

#### 2. MQTT Connection Issues
**Symptoms**: Components cannot communicate
**Solutions**:
- Verify MQTT broker is running
- Check network connectivity
- Verify credentials in configuration
- Check firewall settings

#### 3. Database Connection Errors
**Symptoms**: Database operations fail
**Solutions**:
- Verify database server is running
- Check connection string format
- Verify user permissions
- Check network connectivity

#### 4. Performance Issues
**Symptoms**: Slow authentication or system lag
**Solutions**:
- Optimize image processing parameters
- Increase system resources
- Check for memory leaks
- Optimize database queries

### Debug Mode

Enable debug mode for detailed logging:

```python
# In app.py
app.config['DEBUG'] = True
app.run(debug=True)
```

### Log Files

Check log files for detailed error information:
- Application logs: `logs/app.log`
- MQTT logs: `logs/mqtt.log`
- Database logs: `logs/database.log`

## Development Guide

### Code Structure

```
component/
├── app.py              # Main application file
├── requirements.txt    # Python dependencies
├── config.py          # Configuration settings
├── models.py          # Database models
├── routes.py          # API endpoints
├── utils.py           # Utility functions
├── static/            # Static files (CSS, JS, images)
├── templates/         # HTML templates
└── tests/             # Unit tests
```

### Development Environment Setup

1. **Clone Repository**
   ```bash
   git clone <repository_url>
   cd biometric-vein-authentication
   ```

2. **Create Virtual Environment**
   ```bash
   python -m venv venv
   source venv/bin/activate
   ```

3. **Install Development Dependencies**
   ```bash
   pip install -r requirements-dev.txt
   ```

4. **Setup Pre-commit Hooks**
   ```bash
   pre-commit install
   ```

### Testing

#### Unit Tests
```bash
# Run all tests
python -m pytest

# Run specific test file
python -m pytest tests/test_auth.py

# Run with coverage
python -m pytest --cov=app tests/
```

#### Integration Tests
```bash
# Run integration tests
python -m pytest tests/integration/

# Run with specific database
python -m pytest tests/integration/ --database=test
```

### Code Style

Follow PEP 8 guidelines:
```bash
# Format code
black .

# Check style
flake8 .

# Sort imports
isort .
```

### Documentation

Generate API documentation:
```bash
# Generate OpenAPI spec
flask-spec generate

# Serve documentation
flask-spec serve
```

### Deployment

#### Local Deployment
```bash
# Run development server
python app.py

# Run with Gunicorn
gunicorn -w 4 -b 0.0.0.0:5000 app:app
```

#### Production Deployment
```bash
# Build Docker image
docker build -t biometric-auth .

# Run container
docker run -p 5000:5000 biometric-auth
```

### Monitoring

#### Health Checks
```bash
# Check application health
curl http://localhost:5000/health

# Check database connectivity
curl http://localhost:5000/health/db

# Check MQTT connectivity
curl http://localhost:5000/health/mqtt
```

#### Metrics
- Authentication success rate
- Response times
- Error rates
- System resource usage

---

**Note**: This documentation is maintained by the development team. For questions or contributions, please contact the team or create an issue in the repository. 