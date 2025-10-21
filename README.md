# 🎓 College Exam Result Management System

![Status](https://img.shields.io/badge/Status-Live-brightgreen)
![AWS](https://img.shields.io/badge/AWS-EC2%20%7C%20ALB%20%7C%20Route53-orange)
![Python](https://img.shields.io/badge/Python-3.10+-blue.svg)
![Flask](https://img.shields.io/badge/Flask-2.3.3-green.svg)
![MySQL](https://img.shields.io/badge/MySQL-8.0-blue.svg)
![HTTPS](https://img.shields.io/badge/HTTPS-Enabled-success)
![License](https://img.shields.io/badge/License-MIT-yellow.svg)

A production-ready Flask web application for **Chennai University** that enables students to securely check their semester exam results using Registration Number and Date of Birth authentication.

## 🚀 Quick Start

### Access Live Application
Visit: [**https://domain.digitalplat.org**](https://domain.digitalplat.org)

**Test Credentials:**
- Registration Number: `CSE2025001`
- Date of Birth: `2002-05-15`

### Local Development


## 🌐 Live Demo

**Production URL:** https://domain.digitalplat.org

**Note:** The application is deployed on AWS with:
- Application Load Balancer (HTTPS)
- Auto-scaling capability
- SSL Certificate (AWS ACM)
- Route 53 DNS
---
## 🏗️ Architecture
User Browser
↓ HTTPS
Route 53 DNS (domain.digitalplat.org)
↓
Application Load Balancer (SSL/TLS)
↓ HTTP
Target Group
↓
EC2 Instance (us-east-2)
├─ Apache 2.4.58
├─ Flask + WSGI
└─ MySQL 8.0


**Production Stack:**
- **Frontend:** HTML5, CSS3, JavaScript
- **Backend:** Python 3.10 + Flask 2.3.3
- **Database:** MySQL 8.0
- **Web Server:** Apache 2.4 with mod_wsgi
- **Load Balancer:** AWS Application Load Balancer
- **SSL Certificate:** AWS Certificate Manager (ACM) - Auto-renewing
- **DNS:** AWS Route 53
- **Hosting:** AWS EC2 (Ohio - us-east-2)
- **OS:** Ubuntu 22.04 LTS



## ✨ Features

- 🔐 **Secure Authentication** - Registration Number + Date of Birth verification
- 📊 **Detailed Results** - Subject-wise marks with Internal, External, Total, Grade, and Pass/Fail status
- 📈 **Summary Statistics** - Total marks, maximum marks, percentage, overall grade, and result status
- 🖨️ **Print-Friendly** - Save or print results as PDF directly from browser
- 📱 **Responsive Design** - Works seamlessly on desktop, tablet, and mobile devices
- ✅ **Input Validation** - Both client-side and server-side validation
- 🛠️ **Admin Utilities** - CLI tools for managing students, results, and data export
- 🎨 **Modern UI** - Clean, professional interface with smooth animations

---

## 🛠️ Tech Stack

### Backend
- **Python 3.8+** - Programming language
- **Flask 2.3.3** - Web framework
- **Flask-MySQLdb 1.0.1** - MySQL database integration
- **Gunicorn 20.1.0** - WSGI HTTP server

### Frontend
- **HTML5** - Semantic markup
- **CSS3** - Modern styling with gradients and animations
- **JavaScript (ES6)** - Dynamic client-side interactions

### Database
- **MySQL 8.0** - Relational database management

### Deployment
- **Apache2 2.4** - Web server with mod_wsgi
- **Ubuntu 22.04 LTS** - Server operating system
- **AWS EC2** - Cloud hosting platform

---

## 📁 Project Structure

college-exam-result-system/
├── app.py # Main Flask application
├── config.py # Database and app configuration (not in repo)
├── config.example.py # Configuration template
├── requirements.txt # Python dependencies
├── database_schema.sql # MySQL database schema with sample data
├── admin_utils.py # CLI utilities for admin tasks
├── index.html # Main HTML page
├── styles.css # Application styles
├── script.js # Frontend JavaScript
├── README.md # This file
├── INSTALLATION.md # Detailed installation guide
├── .gitignore # Git ignore configuration
└── LICENSE # MIT License



---

## 📦 Prerequisites

### For Local Development
- Python 3.8 or higher
- MySQL 8.0 or higher
- pip (Python package manager)
- virtualenv (recommended)

### For Production Deployment
- Ubuntu 22.04 LTS server
- Apache2 with mod_wsgi
- MySQL Server 8.0
- Domain name (optional)
- SSL certificate (recommended for HTTPS)

---

## 🚀 Quick Start

### 1. Clone Repository

git clone https://github.com/vsanthoshraj/university-exam-result-management-system.git
cd university-exam-result-management-system


### 2. Create Virtual Environment

python3 -m venv venv
source venv/bin/activate # On Windows: venv\Scripts\activate

### 3. Install Dependencies

pip install --upgrade pip
pip install -r requirements.txt


### 4. Setup MySQL Database
Login to MySQL
mysql -u root -p

Create database and user
CREATE DATABASE college_results CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'collegeuser'@'localhost' IDENTIFIED BY 'your_secure_password';
GRANT ALL PRIVILEGES ON college_results.* TO 'collegeuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;

Import schema
mysql -u collegeuser -p college_results < database_schema.sql


### 5. Configure Application

Copy configuration template
cp config.example.py config.py

Edit with your database credentials
nano config.py

Update `config.py`:

MYSQL_HOST = 'localhost'
MYSQL_USER = 'collegeuser'
MYSQL_PASSWORD = 'your_secure_password'
MYSQL_DB = 'college_results'
DEBUG = True # Set to False in production


### 6. Run Application
python3 app.py

Visit: `http://localhost:5000`

---

## 🌐 Production Deployment

### AWS EC2 Deployment

For complete production deployment on AWS EC2 with Apache2, see [INSTALLATION.md](INSTALLATION.md)

**Quick Deployment with User Data Script:**
- Launch Ubuntu 22.04 EC2 instance
- Use the automated User Data script included in the repository
- Access your application at `http://your-ec2-public-ip`

---

## 📖 Usage

### For Students

1. Visit the application URL
2. Enter your **Registration Number** (e.g., CSE2025001)
3. Select your **Date of Birth**
4. Click **"Check Result"**
5. View your detailed results and summary
6. Print or save as PDF if needed

### For Administrators

Use the CLI admin utilities to manage data:

Activate virtual environment
source venv/bin/activate

Add a new student
python3 admin_utils.py add-student
--name "John Doe"
--reg "CSE2025001"
--roll "21CSE001"
--course 1
--semester 4
--year "2024-25"
--dob "2002-05-15"

Update student results
python3 admin_utils.py update-result
--reg "CSE2025001"
--subject "CSE401"
--internal 18
--external 65
--grade "A"

Export results to CSV
python3 admin_utils.py export-csv --output results.csv


---

## 📡 API Documentation

### Check Result Endpoint

**URL:** `/check-result`  
**Method:** `POST`  
**Content-Type:** `application/json`

**Request Body:**
{
"registration_number": "CSE2025001",
"date_of_birth": "2002-05-15"
}


**Success Response (200 OK):**

{
"success": true,
"student": {
"name": "John Doe",
"registration_number": "CSE2025001",
"roll_number": "21CSE001",
"course": "Computer Science Engineering",
"semester": 4,
"academic_year": "2024-25"
},
"results": [
{
"subject_code": "CSE401",
"subject_name": "Data Structures",
"internal_marks": 18,
"external_marks": 65,
"total_marks": 83,
"max_marks": 100,
"grade": "A",
"status": "Pass"
}
],
"summary": {
"total_marks": 250,
"max_marks": 300,
"percentage": 83.33,
"overall_grade": "A",
"result": "Pass"
}
}


**Error Response (404 Not Found):**
{
"success": false,
"message": "No results found. Please check your registration number and date of birth."
}


---

## 🗄️ Database Schema

### Tables

1. **courses** - Course information (Computer Science, Mechanical, etc.)
2. **subjects** - Subject details with course mapping and max marks
3. **students** - Student personal and enrollment information
4. **results** - Student exam results with marks and grades

### Entity Relationships

- `subjects.course_id` → `courses.course_id`
- `students.course_id` → `courses.course_id`
- `results.student_id` → `students.student_id`
- `results.subject_id` → `subjects.subject_id`

For detailed schema, see `database_schema.sql`.

---

## 🔒 Security Features

- ✅ **SQL Injection Prevention** - Parameterized queries with MySQLdb
- ✅ **Input Validation** - Client-side and server-side validation
- ✅ **Secure Password Handling** - MySQL credentials in config (not in repo)
- ✅ **No Sensitive Data Exposure** - Passwords and keys protected via .gitignore
- ✅ **CSRF Protection** - Recommended to enable for production
- ⚠️ **HTTPS** - Strongly recommended for production deployment

---

## 🧪 Testing

### Test with Sample Data

The `database_schema.sql` includes sample test data:

- **Student Name:** John Doe
- **Registration Number:** CSE2025001
- **Date of Birth:** 2002-05-15

Use these credentials to test the application after setup.

---

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the **MIT License** - see the [LICENSE](LICENSE) file for details.

---

## 👨‍💻 Author

**Santhosh Raj V**

- **GitHub:** [@vsanthoshraj](https://github.com/vsanthoshraj)
- **Email:** santhoshrajv10@gmail.com
- **Repository:** [university-exam-result-management-system](https://github.com/vsanthoshraj/university-exam-result-management-system)

---

## 🙏 Acknowledgments

- Built with **Flask** and **MySQL**
- Deployed on **AWS EC2** with **Apache2**
- UI inspired by modern web design principles
- Sample data structure based on Indian university grading system

---

## 📞 Support

For issues, questions, or suggestions:
- **GitHub Issues:** [Open an issue](https://github.com/vsanthoshraj/university-exam-result-management-system/issues)
- **Email:** santhoshrajv10@gmail.com
- **Documentation:** See [INSTALLATION.md](INSTALLATION.md) for detailed setup instructions

---

## ⭐ Star This Repository

If you find this project useful, please give it a ⭐ on GitHub!

---

## 📊 Project Statistics

![GitHub last commit](https://img.shields.io/github/last-commit/vsanthoshraj/university-exam-result-management-system)
![GitHub issues](https://img.shields.io/github/issues/vsanthoshraj/university-exam-result-management-system)
![GitHub stars](https://img.shields.io/github/stars/vsanthoshraj/university-exam-result-management-system)
![GitHub forks](https://img.shields.io/github/forks/vsanthoshraj/university-exam-result-management-system)

---

**Made with ❤️ for educational institutions**
