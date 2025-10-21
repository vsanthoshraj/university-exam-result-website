text
# 🚀 AWS Deployment Guide

Complete guide for deploying the College Exam Result Management System on AWS with production-grade architecture.

---

## 🏗️ Architecture Overview

Internet
↓
Route 53 (DNS)
↓
Application Load Balancer (HTTPS)
├─ SSL: AWS ACM Certificate
└─ Health Checks
↓
Target Group
↓
EC2 Instance (Ubuntu 22.04)
├─ Apache 2.4 + mod_wsgi
├─ Flask Application
└─ MySQL 8.0

text

### AWS Services Used
- **EC2:** Application server (t2.micro/t3.micro)
- **ALB:** Load balancer with SSL termination
- **ACM:** Free SSL certificate (auto-renewing)
- **Route 53:** DNS management
- **Security Groups:** Network firewall

---

## 📋 Prerequisites

- AWS Account with appropriate permissions
- Domain name (or use subdomain from free DNS provider)
- SSH key pair for EC2 access
- Basic knowledge of AWS Console

---

## 🚀 Deployment Steps

### **Phase 1: Launch EC2 Instance**

#### Step 1: Create EC2 Instance

1. **AWS Console** → EC2 → **Launch Instance**
2. **Name:** `college-results-app`
3. **AMI:** Ubuntu Server 22.04 LTS (Free tier eligible)
4. **Instance type:** t2.micro (Free tier) or t3.micro
5. **Key pair:** Create new or select existing
6. **Network settings:**
   - VPC: Default (or create new)
   - Subnet: Public subnet
   - Auto-assign public IP: **Enable**
   - Security group: Create new
     - **SSH (22)** from Your IP
     - **HTTP (80)** from Anywhere (temporary - will restrict later)

7. **Storage:** 8 GB gp3 (default)
8. Click **Launch Instance**

#### Step 2: Connect to EC2

ssh -i your-key.pem ubuntu@your-ec2-public-ip

text

#### Step 3: Install Dependencies

Update system
sudo apt update && sudo apt upgrade -y

Install packages
sudo apt install -y
apache2
libapache2-mod-wsgi-py3
python3
python3-pip
python3-venv
mysql-server
libmysqlclient-dev
build-essential
git

text

---

### **Phase 2: Deploy Application**

#### Step 1: Clone Repository

Navigate to web directory
cd /var/www

Clone from GitHub
sudo git clone https://github.com/vsanthoshraj/university-exam-result-management-system.git collegeresults-app

Set permissions
sudo chown -R ubuntu:ubuntu collegeresults-app
cd collegeresults-app

text

#### Step 2: Setup Python Environment

Create virtual environment
python3 -m venv venv

Activate environment
source venv/bin/activate

Install dependencies
pip install --upgrade pip
pip install -r requirements.txt

text

#### Step 3: Configure MySQL Database

Secure MySQL installation
sudo mysql_secure_installation

Login to MySQL
sudo mysql

Create database and user
CREATE DATABASE college_results CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'collegeuser'@'localhost' IDENTIFIED BY 'SecurePassword123!';
GRANT ALL PRIVILEGES ON college_results.* TO 'collegeuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;

Import schema
mysql -u collegeuser -p college_results < database_schema.sql

text

#### Step 4: Configure Application

Create config file
cp config.example.py config.py

Edit configuration
nano config.py

text

Update with your credentials:
MYSQL_HOST = 'localhost'
MYSQL_USER = 'collegeuser'
MYSQL_PASSWORD = 'SecurePassword123!'
MYSQL_DB = 'college_results'
DEBUG = False

text

#### Step 5: Configure Apache

Create WSGI file
cat > app.wsgi <<'EOF'
#!/usr/bin/python3
import sys
sys.path.insert(0, '/var/www/collegeresults-app')
from app import app as application
EOF

Create Apache virtual host
sudo nano /etc/apache2/sites-available/collegeresults.conf

text

Add configuration:
<VirtualHost *:80>
ServerName yourdomain.com
ServerAdmin admin@yourdomain.com

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
# Set permissions
sudo chown -R www-data:www-data /var/www/collegeresults-app
sudo chmod -R 755 /var/www/collegeresults-app

# Enable site
sudo a2enmod wsgi
sudo a2dissite 000-default.conf
sudo a2ensite collegeresults.conf

# Restart Apache
sudo systemctl restart apache2
Phase 3: Setup Load Balancer & SSL
Step 1: Request ACM Certificate
AWS Console → Certificate Manager (ACM)

Select region (same as EC2)

Click Request certificate

Domain names: yourdomain.com (add www.yourdomain.com if needed)

Validation: DNS validation

Click Request

Validate: Add CNAME records to your DNS provider

Wait for Issued status (5-30 minutes)

Step 2: Create Target Group
EC2 Console → Target Groups

Click Create target group

Target type: Instances

Name: college-results-tg

Protocol: HTTP, Port: 80

Health check path: /

Click Next

Select EC2 instance → Include as pending

Click Create target group

Step 3: Create Application Load Balancer
EC2 Console → Load Balancers

Click Create load balancer → Application Load Balancer

Name: college-results-alb

Scheme: Internet-facing

Network mapping: Select VPC and 2+ availability zones

Security groups: Create new

Allow HTTP (80) from 0.0.0.0/0

Allow HTTPS (443) from 0.0.0.0/0

Listeners:

HTTP:80 → Redirect to HTTPS:443

HTTPS:443 → Forward to target group

SSL certificate: Select your ACM certificate

Click Create load balancer

Wait for Active status

Step 4: Update EC2 Security Group
EC2 → Security Groups → Select EC2 security group

Edit Inbound rules:

Delete: HTTP from 0.0.0.0/0

Add: HTTP (80) from ALB Security Group

Keep: SSH (22) from Your IP

Save

Phase 4: Configure DNS
Step 1: Setup Route 53 (If using AWS DNS)
Route 53 → Hosted zones

Click Create hosted zone

Domain name: yourdomain.com

Click Create

Note the 4 nameserver records

Update nameservers at your domain registrar

Step 2: Create A Record
Select your hosted zone

Click Create record

Record type: A

Alias: Toggle ON

Route traffic to: Application Load Balancer

Region: Select your region

Load balancer: Select your ALB

Click Create records

Alternative: External DNS Provider
If using external DNS (Cloudflare, etc.):

Create CNAME record

Name: yourdomain.com or subdomain

Target: Your ALB DNS name

Proxy status: DNS only (gray cloud)

✅ Verification
Test Target Health
text
# AWS Console → EC2 → Target Groups → Select your TG
# Targets tab → Status should be "healthy"
Test Application
text
# Test ALB directly
curl -I https://your-alb-dns-name.elb.amazonaws.com

# Test domain (after DNS propagates)
curl -I https://yourdomain.com

# Open in browser
https://yourdomain.com
📊 Production Checklist
 EC2 security group restricted to ALB only

 HTTPS enabled with valid ACM certificate

 HTTP → HTTPS redirect configured

 Target group shows "healthy" status

 Database credentials secured (not in repo)

 Apache configured with mod_wsgi

 Domain resolving to ALB correctly

 Application accessible via HTTPS

 All features tested (result lookup, print, etc.)

 Logs monitoring configured

📈 Monitoring & Maintenance
CloudWatch Metrics
ALB request count

Target response time

Unhealthy host count

Application Logs
text
# Apache error logs
sudo tail -f /var/log/apache2/collegeresults-error.log

# Apache access logs
sudo tail -f /var/log/apache2/collegeresults-access.log
Update Application
text
cd /var/www/collegeresults-app
git pull origin main
sudo systemctl reload apache2
💰 Cost Estimate
Service	Cost	Notes
EC2 (t2.micro)	Free tier or ~$8/mo	750 hours/month free tier
ALB	~$16-22/mo	$0.0225/hour + data
Route 53	$0.50/mo	Per hosted zone + queries
ACM Certificate	FREE	Auto-renewing
Data Transfer	Variable	First 1GB free
Total	~$17-30/mo	Without free tier
🔒 Security Best Practices
 Change all default passwords

 Restrict SSH access to specific IPs

 Enable CloudTrail for audit logging

 Set up AWS Backup for EC2/RDS

 Enable GuardDuty for threat detection

 Use IAM roles instead of access keys

 Implement regular security updates

 Monitor CloudWatch alarms

🆘 Troubleshooting
Target Unhealthy
text
# Check Apache status
sudo systemctl status apache2

# Test local access
curl http://localhost

# Check security group allows ALB → EC2
502 Bad Gateway
text
# Check Apache logs
sudo tail -50 /var/log/apache2/collegeresults-error.log

# Verify WSGI configuration
cat /var/www/collegeresults-app/app.wsgi
SSL Certificate Issues
Verify ACM certificate status is "Issued"

Check ALB listener has correct certificate

Confirm domain matches certificate

📞 Support
For deployment issues:

Documentation: See README.md and INSTALLATION.md

GitHub Issues: Open an issue

Email: santhoshrajv10@gmail.com

Deployment guide last updated: October 2025
