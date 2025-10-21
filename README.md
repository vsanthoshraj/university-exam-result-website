# university-exam-result-management-system

A modern web application for Chennai University that empowers students to view their semester exam results securely using Registration Number and Date of Birth.

---

##  Project Overview

This system offers secure, subject-wise and overall result reporting with print-friendly, responsive UI. It’s easy to use on desktop or mobile and includes CLI utilities for admins.

---

##  Features

- **Student result lookup:** Registration Number + Date of Birth for secure access
- **Subject-wise marks:** Internal, external, total, grade, pass/fail status
- **Summary section:** Total marks, maximum marks, percentage, grade, overall result
- **Print-friendly:** Save or print results as PDF from browser
- **Responsive UI:** Works on all devices
- **Validation:** Client-side and server-side input checks
- **Admin utilities:** CLI for adding students, updating results, exporting CSV

---

##  Project File Structure

exam-result-system/
│
├── admin_utils.py # Admin CLI for DB management
├── app.py # Main Flask backend
├── config.py # DB and app configuration
├── database_schema.sql # MySQL tables + sample data
├── index.html # Frontend UI + client JS
├── README.md # Project documentation
├── requirements.txt # Python package dependencies
├── script.js # Extra JS (validation, interactions)
├── styles.css # App styling



## 🛠️ Requirements

- Python 3.7 or higher
- MySQL Server 5.7+ (or compatible)
- pip (Python package manager)

---

##  Installation Guide

### 1. Clone the Repository


### 2. Create Python Virtual Environment
python -m venv venv

Activate:
Windows:
venv\Scripts\activate

Linux/macOS:
source venv/bin/activate

### 3. Install Python Dependencies
pip install -r requirements.txt

### 4. Install MySQL

#### On **Windows**:
- Download MySQL Installer from [mysql.com](https://dev.mysql.com/downloads/installer/)
- Install and set your root password

#### On **Linux (Ubuntu)**:
sudo apt update
sudo apt install mysql-server
sudo mysql_secure_installation
sudo systemctl start mysql
sudo systemctl enable mysql


- Set and remember your MySQL password

---

##  Database Setup

### 1. Create the Database and Tables

- Start MySQL and log in:
mysql -u root -p


- Create and set up tables:

CREATE DATABASE exam_result_system;
USE exam_result_system;
SOURCE database_schema.sql;



Change the `SOURCE` path as needed.

### 2. Configure Flask to Connect to MySQL

In `config.py` (or top of `app.py`):

MYSQL_HOST = 'localhost'
MYSQL_USER = 'your_mysql_username'
MYSQL_PASSWORD = 'your_mysql_password'
MYSQL_DB = 'exam_result_system'


---

##  Running the Application

python app.py

Access the site at [http://localhost:5000](http://localhost:5000)

---

## 👩‍💻 Usage

- Students: Enter Registration Number and Date of Birth to get your result.
- Admins: Use `admin_utils.py` via terminal for student/result management and CSV export.

---

##  Troubleshooting

- **MySQL Connection Issues:** Make sure your MySQL server is running and your credentials are correct.
- **Missing Modules:** Double-check that your virtual environment is activated and all dependencies are installed.
- **Port Conflicts:** In `app.py`, update the run port:  
  `app.run(port=5001)` if 5000 is taken.
- **DOB Format:** Use YYYY-MM-DD (e.g., 2001-03-15).

---

##  License

This project is free for educational use and can be customized by your institution.

---

**Made with ❤️ for Chennai University — Secure, easy, and reliable results for every student.**
#
