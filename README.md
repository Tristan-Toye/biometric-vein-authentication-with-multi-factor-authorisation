# Biometric Vein Authentication with Three-Factor Authorization

A high-security biometric authentication system developed as a second bachelor P&O3 project at KU Leuven. This system implements dual-factor biometric authentication using face recognition and vein pattern recognition, integrated with a comprehensive web-based management platform.

## 🚀 Features

### Core Authentication
- **Dual Biometric Authentication**: Face recognition + vein pattern recognition
- **Three-Factor Authorization**: Password, ID scanning, and facial recognition for admin access
- **QR Code Integration**: Time-based QR codes for secure access and exit
- **Real-time Processing**: Live biometric verification with immediate feedback

### Security Features
- **Role-Based Access Control**: Admin, Security, Recruiting, and Staff roles
- **Secure Communication**: MQTT message broker for local network communication
- **Database Security**: Separate local and cloud databases for different data types
- **Audit Logging**: Comprehensive logging of all authentication attempts

### Management Platform
- **Web Dashboard**: Flask-based web application for system management
- **Real-time Monitoring**: Live updates via WebSocket connections
- **User Management**: Add, delete, and promote users with different access levels
- **Data Analytics**: View authentication logs and employee statistics

## 🏗️ Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Raspberry Pi  │    │   Local Server  │    │   Cloud Server  │
│   (Biometric    │◄──►│   (MQTT Broker  │◄──►│   (Heroku App)  │
│    Sensors)     │    │   + Flask UI)   │    │   (Web Admin)   │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

### Components
- **Local UI**: Flask application for entrance/exit authentication
- **Security App**: Real-time security monitoring and alerts
- **Online Web App**: Administrative web interface
- **MQTT Server**: Message broker for local network communication
- **Leaving UI**: Exit authentication interface

## 🛠️ Technology Stack

### Backend
- **Python 3.x**
- **Flask** - Web framework
- **Flask-SQLAlchemy** - Database ORM
- **Flask-SocketIO** - Real-time communication
- **Flask-User** - User management
- **OpenCV** - Computer vision
- **face-recognition** - Face recognition library
- **MQTT** - Message queuing

### Frontend
- **HTML5/CSS3** - User interface
- **JavaScript** - Client-side functionality
- **jQuery** - DOM manipulation
- **WebSocket** - Real-time updates

### Database
- **PostgreSQL** - Cloud database (Heroku)
- **SQLite** - Local database

### Deployment
- **Heroku** - Cloud hosting
- **Gunicorn** - WSGI server

## 📁 Project Structure

```
biometric-vein-authentication/
├── securityapp/                 # Security monitoring application
├── online_web_app/             # Web-based admin interface
├── UI/                         # Local authentication interface
├── MQTTT/                      # MQTT message broker
├── Leaving_UI/                 # Exit authentication interface
└── README.md                   # This file
```

## 🚀 Quick Start

### Prerequisites
- Python 3.7+
- PostgreSQL database
- MQTT broker
- Camera and vein scanner hardware

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/yourusername/biometric-vein-authentication.git
   cd biometric-vein-authentication
   ```

2. **Install dependencies**
   ```bash
   # For online web app
   cd online_web_app/Wep_application_CW2B2_KUL
   pip install -r requirements.txt
   
   # For security app
   cd ../../securityapp/security_app
   pip install -r requirements.txt
   ```

3. **Configure environment**
   - Set up PostgreSQL database
   - Configure MQTT broker settings
   - Update database connection strings

4. **Run the applications**
   ```bash
   # Start MQTT server
   cd MQTTT
   python mqtt_server.py
   
   # Start security app
   cd ../securityapp/security_app
   python app.py
   
   # Start online web app
   cd ../../online_web_app/Wep_application_CW2B2_KUL
   python app.py
   ```

## 👥 User Roles

### Admin
- Full system access
- User management (add, delete, promote)
- View all data and logs
- System configuration

### Security
- View all data and logs
- Monitor real-time authentication
- No modification privileges

### Recruiting
- View partial data
- Add basic staff members
- Limited access to sensitive information

### Staff
- View own data only
- Basic authentication access
- No administrative privileges

## 🔒 Security Features

- **Biometric Encryption**: Secure storage of biometric templates
- **Network Security**: MQTT over local network with authentication
- **Database Security**: Separate databases for different data types
- **Session Management**: Secure session handling with Flask-Session
- **Input Validation**: Comprehensive form validation and sanitization

## 📊 Monitoring & Logging

- Real-time authentication monitoring
- Comprehensive audit logs
- Security alerts for failed attempts
- Performance metrics tracking
- User activity analytics

## 🤝 Contributing

This is an academic project developed for KU Leuven. For questions or contributions, please contact the development team.

## 📄 License

This project is developed for educational purposes at KU Leuven. All rights reserved.

## 👨‍💻 Development Team

- **Institution**: KU Leuven
- **Course**: P&O3 (Second Bachelor)
- **Project**: Biometric Entrance IDentiGate

---

**Note**: This system is designed for high-security environments and includes multiple layers of authentication and monitoring. Ensure proper security protocols are followed during deployment and operation.
