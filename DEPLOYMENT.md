# 🚀 AWS Deployment Guide

## Architecture Overview

This application is deployed on AWS using a production-grade architecture:

### Components
- **EC2 Instance:** Ubuntu 22.04 LTS (t2.micro/t3.micro)
- **Application Load Balancer:** HTTPS with ACM certificate
- **Route 53:** DNS management
- **RDS MySQL:** (Optional - currently using EC2 MySQL)

### Deployment Steps

#### 1. EC2 Instance Setup

Launch Ubuntu 22.04 EC2 instance
Security Group: Allow HTTP (80) from ALB only, SSH (22) from your IP
SSH to instance
ssh -i your-key.pem ubuntu@ec2-public-ip

Install dependencies
sudo apt update && sudo apt upgrade -y
sudo apt install -y apache2 python3 python3-pip python3-venv mysql-server



#### 2. Application Setup

Clone repository
cd /var/www
sudo git clone https://github.com/vsanthoshraj/university-exam-result-management-system.git collegeresults-app
cd collegeresults-app

Setup Python environment
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

Configure database
sudo mysql < database_schema.sql

Configure Apache
sudo nano /etc/apache2/sites-available/collegeresults.conf

Add VirtualHost configuration (see INSTALLATION.md)


#### 3. ALB + SSL Setup
- Request ACM certificate for your domain
- Create Application Load Balancer
- Configure listeners (HTTP→HTTPS redirect, HTTPS→Target Group)
- Create target group with EC2 instance
- Point Route 53 A record to ALB

#### 4. Verification

Check target health
AWS Console → EC2 → Target Groups → Targets (should be "healthy")

Test application
curl https://your-domain.com


## Production Checklist
- [ ] EC2 security group restricted to ALB
- [ ] HTTPS enabled with valid certificate
- [ ] Database credentials secured
- [ ] Apache configured with mod_wsgi
- [ ] Health checks passing
- [ ] Domain resolving correctly
- [ ] Backup strategy in place

## Monitoring
- CloudWatch for ALB metrics
- Apache logs: `/var/log/apache2/collegeresults-error.log`
- Application metrics via CloudWatch agent

## Cost Estimate
- EC2 (t2.micro): Free tier or ~$8/month
- ALB: ~$16-22/month
- Route 53: $0.50/month + query fees
- ACM Certificate: FREE
- **Total: ~$17-30/month**

6. Create AWS-SETUP.md
Step-by-step AWS configuration guide.

7. Add Screenshots
Create screenshots/ folder with:
home-page.png - Landing page
result-display.png - Result view
aws-architecture.png - AWS architecture diagram
