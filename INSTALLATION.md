# 📦 Complete Installation Guide

This guide provides comprehensive step-by-step instructions for installing and deploying the College Exam Result Management System in various environments.

---

## Table of Contents

1. [Local Development Setup](#1-local-development-setup)
2. [Ubuntu Server Manual Installation](#2-ubuntu-server-manual-installation)
3. [AWS EC2 Production Deployment](#3-aws-ec2-production-deployment)
4. [Docker Installation (Optional)](#4-docker-installation-optional)
5. [Troubleshooting](#5-troubleshooting)

---

## 1. Local Development Setup

### Prerequisites
- Python 3.8 or higher
- MySQL 8.0 or higher
- Git
- pip (Python package manager)

### Installation Steps

#### Step 1: Clone Repository
git clone https://github.com/vsanthoshraj/university-exam-result-management-system.git
cd university-exam-result-management-system


git clone https://github.com/vsanthoshraj/university-exam-result-management-system.git
cd university-exam-result-management-system

text

#### Step 2: Create Virtual Environment

**Linux/Mac:**
python3 -m venv venv
source venv/bin/activate

text

**Windows:**
python -m venv venv
venv\Scripts\activate

text

#### Step 3: Install Python Dependencies

pip install --upgrade pip
pip install -r requirements.txt

text

#### Step 4: Install and Configure MySQL

**Linux (Ubuntu/Debian):**
sudo apt update
sudo apt install mysql-server
sudo mysql_secure_installation

text

**macOS:**
brew install mysql
brew services start mysql

text

**Windows:**
Download and install MySQL from: https://dev.mysql.com/downloads/installer/

#### Step 5: Create Database and User

Login to MySQL
mysql -u root -p

Run these SQL commands:
CREATE DATABASE college_results CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'collegeuser'@'localhost' IDENTIFIED BY 'YourSecurePassword123!';
GRANT ALL PRIVILEGES ON college_results.* TO 'collegeuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;

text

#### Step 6: Import Database Schema

mysql -u collegeuser -p college_results < database_schema.sql

text

Enter the password when prompted.

#### Step 7: Configure Application

Copy configuration template
cp config.example.py config.py

Edit configuration
nano config.py # or use your preferred text editor

text

Update the following in `config.py`:
MYSQL_HOST = 'localhost'
MYSQL_USER = 'collegeuser'
MYSQL_PASSWORD = 'YourSecurePassword123!'
MYSQL_DB = 'college_results'
APP_PORT = 5000
DEBUG = True # Only for development!
COLLEGE_NAME = 'Chennai University'

text

#### Step 8: Run the Application

python3 app.py

text

The application will start at: `http://localhost:5000`

---

## 2. Ubuntu Server Manual Installation

### Prerequisites
- Ubuntu 22.04 LTS (or 20.04)
- Root or sudo access
- Basic knowledge of Linux commands

### Complete Installation Script

#!/bin/bash

Update system packages
sudo apt update && sudo apt upgrade -y

Install required packages
sudo apt install -y python3 python3-pip python3-venv
mysql-server git curl build-essential
libmysqlclient-dev pkg-config

Clone repository
cd /opt
sudo git clone https://github.com/vsanthoshraj/university-exam-result-management-system.git
cd university-exam-result-management-system

Create virtual environment
python3 -m venv venv
source venv/bin/activate

Install Python packages
pip install --upgrade pip
pip install -r requirements.txt

Secure MySQL installation
sudo mysql_secure_installation

Create database and user
sudo mysql <<EOF
CREATE DATABASE IF NOT EXISTS college_results CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER IF NOT EXISTS 'collegeuser'@'localhost' IDENTIFIED BY 'ChangeThisPassword123!';
GRANT ALL PRIVILEGES ON college_results.* TO 'collegeuser'@'localhost';
FLUSH PRIVILEGES;
EOF

Import database schema
mysql -u collegeuser -pChangeThisPassword123! college_results < database_schema.sql

Configure application
cp config.example.py config.py
nano config.py # Edit with your credentials

echo "Installation complete!"
echo "Run: python3 app.py"

text

---

## 3. AWS EC2 Production Deployment

### Option A: Automated Deployment (User Data Script)

When launching a new EC2 instance, use this User Data script for automatic setup:

#!/bin/bash

EC2 User Data Script for Automated Deployment
Variables
PROJECT_NAME="collegeresults"
PROJECT_DIR="/var/www/collegeresults-app"
DB_NAME="college_results"
DB_USER="collegeuser"
DB_PASSWORD="ChangeThisPassword123!" # CHANGE THIS!
GITHUB_REPO="https://github.com/vsanthoshraj/university-exam-result-management-system.git"

Log all output
exec > >(tee /var/log/user-data.log)
exec 2>&1

echo "Starting automated deployment..."

Update system
apt update -y && apt upgrade -y

Install packages
apt install -y apache2 libapache2-mod-wsgi-py3
python3 python3-pip python3-venv python3-dev
mysql-server libmysqlclient-dev build-essential git curl

Start services
systemctl enable apache2 mysql
systemctl start apache2 mysql

Configure MySQL
mysql -e "ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '${DB_PASSWORD}';"
mysql -u root -p${DB_PASSWORD} <<MYSQL_SCRIPT
CREATE DATABASE IF NOT EXISTS ${DB_NAME} CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER IF NOT EXISTS '${DB_USER}'@'localhost' IDENTIFIED BY '${DB_PASSWORD}';
GRANT ALL PRIVILEGES ON ${DB_NAME}.* TO '${DB_USER}'@'localhost';
FLUSH PRIVILEGES;
MYSQL_SCRIPT

Clone project
mkdir -p ${PROJECT_DIR}
cd ${PROJECT_DIR}
git clone ${GITHUB_REPO} .

Setup Python virtual environment
python3 -m venv venv
source venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt

Create configuration
cat > ${PROJECT_DIR}/config.py <<EOF
MYSQL_HOST = 'localhost'
MYSQL_USER = '${DB_USER}'
MYSQL_PASSWORD = '${DB_PASSWORD}'
MYSQL_DB = '${DB_NAME}'
APP_PORT = 5000
DEBUG = False
COLLEGE_NAME = 'Chennai University'
EOF

Import database schema
mysql -u ${DB_USER} -p${DB_PASSWORD} ${DB_NAME} < database_schema.sql

Create WSGI entry point
cat > ${PROJECT_DIR}/app.wsgi <<'WSGIEOF'
#!/usr/bin/python3
import sys
sys.path.insert(0, '/var/www/collegeresults-app')
from app import app as application
application.config['SECRET_KEY'] = 'change-this-secret-key-in-production'
WSGIEOF

Configure Apache
cat > /etc/apache2/sites-available/${PROJECT_NAME}.conf <<'APACHEEOF'
<VirtualHost *:80>
ServerName default
ServerAdmin admin@example.com

text
WSGIDaemonProcess collegeresults python-home=/var/www/collegeresults-app/venv python-path=/var/www/collegeresults-app
WSGIProcessGroup collegeresults
WSGIScriptAlias / /var/www/collegeresults-app/app.wsgi

<Directory /var/www/collegeresults-app>
    Require all granted
</Directory>

Alias /static /var/www/collegeresults-app/static
<Directory /var/www/collegeresults-app/static>
    Require all granted
</Directory>

ErrorLog ${APACHE_LOG_DIR}/collegeresults-error.log
CustomLog ${APACHE_LOG_DIR}/collegeresults-access.log combined
</VirtualHost> APACHEEOF
Set permissions
chown -R www-data:www-data ${PROJECT_DIR}
chmod -R 755 ${PROJECT_DIR}

Enable Apache site
a2enmod wsgi
a2dissite 000-default.conf
a2ensite ${PROJECT_NAME}.conf
systemctl restart apache2

Get public IP
PUBLIC_IP=$(curl -s http://169.254.169.254/latest/meta-data/public-ipv4)

echo "============================================"
echo "Deployment Complete!"
echo "Access your application at: http://${PUBLIC_IP}"
echo "============================================"

text

### Option B: Manual EC2 Deployment

See the automated script above for detailed steps. Follow each command sequentially after SSH-ing into your EC2 instance.

---

## 4. Docker Installation (Optional)

### Dockerfile

Create a `Dockerfile`:

FROM python:3.10-slim

Install system dependencies
RUN apt-get update && apt-get install -y
default-libmysqlclient-dev
build-essential
pkg-config
&& rm -rf /var/lib/apt/lists/*

Set working directory
WORKDIR /app

Copy requirements and install
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

Copy application files
COPY . .

Expose port
EXPOSE 5000

Run application
CMD ["python3", "app.py"]

text

### docker-compose.yml

version: '3.8'

services:
web:
build: .
ports:
- "5000:5000"
environment:
- MYSQL_HOST=db
- MYSQL_USER=collegeuser
- MYSQL_PASSWORD=password123
- MYSQL_DB=college_results
depends_on:
- db
volumes:
- .:/app

db:
image: mysql:8.0
environment:
- MYSQL_ROOT_PASSWORD=rootpassword
- MYSQL_DATABASE=college_results
- MYSQL_USER=collegeuser
- MYSQL_PASSWORD=password123
volumes:
- mysql_data:/var/lib/mysql
- ./database_schema.sql:/docker-entrypoint-initdb.d/schema.sql
ports:
- "3306:3306"

volumes:
mysql_data:

text

### Running with Docker

Build and start
docker-compose up -d

View logs
docker-compose logs -f

Stop
docker-compose down

text

---

## 5. Troubleshooting

### Common Issues and Solutions

#### MySQL Connection Errors

**Error:** `Access denied for user 'collegeuser'@'localhost'`

**Solution:**
sudo mysql -u root -p
DROP USER IF EXISTS 'collegeuser'@'localhost';
CREATE USER 'collegeuser'@'localhost' IDENTIFIED BY 'NewPassword123!';
GRANT ALL PRIVILEGES ON college_results.* TO 'collegeuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;

text

#### Python Module Import Errors

**Error:** `ModuleNotFoundError: No module named 'flask'`

**Solution:**
Make sure virtual environment is activated
source venv/bin/activate

Reinstall requirements
pip install --force-reinstall -r requirements.txt

text

#### Apache Permission Errors

**Error:** `Permission denied: [client] AH00035: access to / denied`

**Solution:**
Fix directory permissions
sudo chmod 755 /var/www
sudo chown -R www-data:www-data /var/www/collegeresults-app
sudo chmod -R 755 /var/www/collegeresults-app
sudo systemctl restart apache2

text

#### Port Already in Use

**Error:** `Address already in use: Port 5000`

**Solution:**
Find process using port 5000
sudo lsof -i :5000

Kill the process
sudo kill -9 <PID>

Or use a different port in config.py
APP_PORT = 5001

text

#### Database Import Errors

**Error:** `ERROR 1044 (42000): Access denied`

**Solution:**
Drop and recreate database
mysql -u root -p
DROP DATABASE IF EXISTS college_results;
CREATE DATABASE college_results CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
GRANT ALL PRIVILEGES ON college_results.* TO 'collegeuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;

Import again
mysql -u collegeuser -p college_results < database_schema.sql

text

---

## Quick Reference Commands

### Application Management

Start application
python3 app.py

Run in background
nohup python3 app.py > app.log 2>&1 &

Stop application
pkill -f app.py

View logs
tail -f app.log

text

### Apache Management

Restart Apache
sudo systemctl restart apache2

Check status
sudo systemctl status apache2

View error logs
sudo tail -f /var/log/apache2/collegeresults-error.log

Test configuration
sudo apache2ctl configtest

text

### MySQL Management

Login to MySQL
mysql -u collegeuser -p college_results

Backup database
mysqldump -u collegeuser -p college_results > backup.sql

Restore database
mysql -u collegeuser -p college_results < backup.sql

Show tables
mysql -u collegeuser -p college_results -e "SHOW TABLES;"

text

### System Monitoring

Check disk space
df -h

Check memory usage
free -h

Check running processes
ps aux | grep python

Check Apache processes
ps aux | grep apache

text

---

## Security Checklist

Before deploying to production:

- [ ] Change all default passwords
- [ ] Update SECRET_KEY in production
- [ ] Set DEBUG = False in config.py
- [ ] Enable firewall (ufw)
- [ ] Set up HTTPS with SSL certificate
- [ ] Configure regular database backups
- [ ] Keep system packages updated
- [ ] Restrict MySQL remote access
- [ ] Use strong, unique passwords
- [ ] Enable Apache security modules
- [ ] Set up monitoring and logging
- [ ] Configure rate limiting (optional)

---

## Next Steps

After successful installation:

1. Test the application thoroughly
2. Import real data using admin utilities
3. Configure domain name (if applicable)
4. Set up SSL certificate for HTTPS
5. Configure automated backups
6. Set up monitoring and alerts
7. Document any custom configurations

---

## Support

For additional help:

- **GitHub Issues:** https://github.com/vsanthoshraj/university-exam-result-management-system/issues
- **Email:** santhoshrajv10@gmail.com
- **Documentation:** See README.md for project overview

---

**Installation guide last updated:** October 2025
