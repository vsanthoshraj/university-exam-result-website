# 📦 Installation Guide

Complete guide for installing the College Exam Result Management System in local, server, and production environments.

---

## Table of Contents

1. [Local Development](#1-local-development)
2. [Ubuntu Server](#2-ubuntu-server)
3. [AWS Production Deployment](#3-aws-production-deployment)
4. [Troubleshooting](#4-troubleshooting)

---

## 1. Local Development

### Prerequisites
- Python 3.8+
- MySQL 8.0+
- Git

### Quick Setup

Clone repository
git clone https://github.com/vsanthoshraj/university-exam-result-website.git
cd university-exam-result-website

Create virtual environment
python3 -m venv venv
source venv/bin/activate # Windows: venv\Scripts\activate

Install dependencies
pip install -r requirements.txt

Setup MySQL
mysql -u root -p

text
undefined
CREATE DATABASE college_results CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'collegeuser'@'localhost' IDENTIFIED BY 'YourPassword123!';
GRANT ALL PRIVILEGES ON college_results.* TO 'collegeuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;

text
undefined
Import schema
mysql -u collegeuser -p college_results < database_schema.sql

Configure application
cp config.example.py config.py
nano config.py # Update MySQL credentials

Run application
python3 app.py

text

Visit: `http://localhost:5000`

---

## 2. Ubuntu Server

### Prerequisites
- Ubuntu 22.04 LTS
- Sudo access

### Installation

Update system
sudo apt update && sudo apt upgrade -y

Install dependencies
sudo apt install -y apache2 libapache2-mod-wsgi-py3
python3 python3-pip python3-venv
mysql-server libmysqlclient-dev git

Clone repository
cd /var/www
sudo git clone https://github.com/vsanthoshraj/university-exam-result-website.git collegeresults-app
cd collegeresults-app

Setup Python
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

Configure MySQL
sudo mysql_secure_installation

sudo mysql <<EOF
CREATE DATABASE college_results CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'collegeuser'@'localhost' IDENTIFIED BY 'SecurePassword123!';
GRANT ALL PRIVILEGES ON college_results.* TO 'collegeuser'@'localhost';
FLUSH PRIVILEGES;
EOF

Import schema
mysql -u collegeuser -p college_results < database_schema.sql

Configure application
cp config.example.py config.py
nano config.py # Update credentials

Setup Apache
sudo nano /etc/apache2/sites-available/collegeresults.conf

text

**Apache Configuration:**
<VirtualHost *:80>
ServerName yourdomain.com

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
</VirtualHost> ```
text
# Create WSGI file
cat > app.wsgi <<'EOF'
#!/usr/bin/python3
import sys
sys.path.insert(0, '/var/www/collegeresults-app')
from app import app as application
EOF

# Set permissions
sudo chown -R www-data:www-data /var/www/collegeresults-app
sudo chmod -R 755 /var/www/collegeresults-app

# Enable site
sudo a2enmod wsgi
sudo a2dissite 000-default.conf
sudo a2ensite collegeresults.conf
sudo systemctl restart apache2
3. AWS Production Deployment
For complete AWS deployment with ALB, ACM, and Route 53, see DEPLOYMENT.md

Quick EC2 Setup
text
# SSH to EC2
ssh -i your-key.pem ubuntu@ec2-public-ip

# Install dependencies
sudo apt update && sudo apt upgrade -y
sudo apt install -y apache2 libapache2-mod-wsgi-py3 python3 python3-pip python3-venv mysql-server git

# Clone and setup
cd /var/www
sudo git clone https://github.com/vsanthoshraj/university-exam-result-website.git collegeresults-app
cd collegeresults-app
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# Configure MySQL and Apache (same as Ubuntu Server section)
For load balancer, SSL, and DNS setup, see DEPLOYMENT.md

4. Troubleshooting
MySQL Connection Error
text
# Reset user
sudo mysql -u root -p
DROP USER IF EXISTS 'collegeuser'@'localhost';
CREATE USER 'collegeuser'@'localhost' IDENTIFIED BY 'NewPassword123!';
GRANT ALL PRIVILEGES ON college_results.* TO 'collegeuser'@'localhost';
FLUSH PRIVILEGES;
Python Module Not Found
text
# Reinstall requirements
source venv/bin/activate
pip install --force-reinstall -r requirements.txt
Apache Permission Denied
text
# Fix permissions
sudo chown -R www-data:www-data /var/www/collegeresults-app
sudo chmod -R 755 /var/www/collegeresults-app
sudo systemctl restart apache2
Port Already in Use
text
# Find and kill process
sudo lsof -i :5000
sudo kill -9 <PID>

# Or change port in config.py
APP_PORT = 5001
Quick Reference
Application Commands
text
# Run development server
python3 app.py

# Run in background
nohup python3 app.py > app.log 2>&1 &

# Stop
pkill -f app.py
Apache Commands
text
# Restart
sudo systemctl restart apache2

# Status
sudo systemctl status apache2

# Logs
sudo tail -f /var/log/apache2/collegeresults-error.log

# Test config
sudo apache2ctl configtest
MySQL Commands
text
# Login
mysql -u collegeuser -p college_results

# Backup
mysqldump -u collegeuser -p college_results > backup.sql

# Restore
mysql -u collegeuser -p college_results < backup.sql
Security Checklist
 Change default passwords

 Set DEBUG = False in production

 Enable firewall

 Setup HTTPS/SSL

 Configure backups

 Keep system updated

 Restrict database access

Support
Documentation: README.md, DEPLOYMENT.md

Issues: GitHub Issues

Email: santhoshrajv10@gmail.com

Last updated: October 2025
