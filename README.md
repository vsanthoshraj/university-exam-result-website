text
# 🎓 College Exam Result Management System

![AWS](https://img.shields.io/badge/AWS-EC2%20%7C%20ALB%20%7C%20Route53-orange)
![Python](https://img.shields.io/badge/Python-3.10+-blue.svg)
![Flask](https://img.shields.io/badge/Flask-2.3.3-green.svg)
![MySQL](https://img.shields.io/badge/MySQL-8.0-blue.svg)
![HTTPS](https://img.shields.io/badge/HTTPS-Enabled-success)

A production-ready Flask web application for **Chennai University** that enables students to securely check their semester exam results using Registration Number and Date of Birth authentication.

##  perview
see the pngs

---

## ✨ Features

- 🔐 **Secure Authentication** - Registration Number + Date of Birth verification
- 📊 **Detailed Results** - Subject-wise marks (Internal, External, Total, Grade, Pass/Fail)
- 📈 **Summary Statistics** - Total marks, percentage, overall grade, result status
- 🖨️ **Print-Friendly** - Save or print results as PDF directly from browser
- 📱 **Responsive Design** - Works seamlessly on desktop, tablet, and mobile
- ✅ **Input Validation** - Both client-side and server-side validation
- 🛠️ **Admin Utilities** - CLI tools for managing students and results
- 🎨 **Modern UI** - Clean, professional interface with smooth animations

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

text

**Production Stack:**
- **Frontend:** HTML5, CSS3, JavaScript (ES6)
- **Backend:** Python 3.10 + Flask 2.3.3
- **Database:** MySQL 8.0
- **Web Server:** Apache 2.4 with mod_wsgi
- **Load Balancer:** AWS Application Load Balancer
- **SSL Certificate:** AWS Certificate Manager (ACM) - Auto-renewing
- **DNS:** AWS Route 53
- **Hosting:** AWS EC2 (Ohio - us-east-2)
- **OS:** Ubuntu 22.04 LTS

---

## 🚀 Quick Start

### Local Development

Clone repository
git clone https://github.com/vsanthoshraj/university-exam-result-management-system.git
cd university-exam-result-management-system

Create virtual environment
python3 -m venv venv
source venv/bin/activate # Windows: venv\Scripts\activate

Install dependencies
pip install -r requirements.txt

Setup MySQL database
mysql -u root -p < database_schema.sql

Configure application
cp config.example.py config.py
nano config.py # Update with your MySQL credentials

Run application
python3 app.py

text

Visit: `http://localhost:5000`

### Production Deployment

For AWS EC2 + ALB deployment, see [INSTALLATION.md](INSTALLATION.md)

---

## 📁 Project Structure

university-exam-result-management-system/
├── app.py # Main Flask application
├── admin_utils.py # CLI admin utilities
├── config.example.py # Configuration template
├── requirements.txt # Python dependencies
├── database_schema.sql # MySQL schema with sample data
├── index.html # Frontend HTML
├── styles.css # Application styles
├── script.js # Frontend JavaScript
├── README.md # This file
├── INSTALLATION.md # Detailed installation guide
├── .gitignore # Git ignore rules
└── LICENSE # MIT License

text

---

## 📖 Usage

### For Students

1. Visit [https://domain.digitalplat.org](https://domain.digitalplat.org)
2. Enter **Registration Number**
3. Select **Date of Birth**
4. Click **"Check Result"**
5. View detailed results and summary

### For Administrators

Activate virtual environment
source venv/bin/activate

Add new student
python3 admin_utils.py add-student
--name "John Doe"
--reg "CSE2025001"
--roll "21CSE001"
--course 1
--semester 4
--year "2024-25"
--dob "2002-05-15"

Update results
python3 admin_utils.py update-result
--reg "CSE2025001"
--subject "CSE401"
--internal 18
--external 65
--grade "A"

Export to CSV
python3 admin_utils.py export-csv --output results.csv

text

---

## 📡 API Documentation

### Check Result Endpoint

**Endpoint:** `POST /check-result`  
**Content-Type:** `application/json`

**Request:**
{
"registration_number": "CSE2025001",
"date_of_birth": "2002-05-15"
}

text

**Response (200 OK):**
{
"success": true,
"student": {
"name": "John Doe",
"registration_number": "CSE2025001",
"course": "Computer Science Engineering",
"semester": 4
},
"results": [
{
"subject_code": "CSE401",
"subject_name": "Data Structures",
"total_marks": 83,
"grade": "A",
"status": "Pass"
}
],
"summary": {
"total_marks": 250,
"percentage": 83.33,
"overall_grade": "A",
"result": "Pass"
}
}

text

---

## 🗄️ Database Schema

**Tables:**
- `courses` - Course information
- `subjects` - Subject details with max marks
- `students` - Student enrollment information
- `results` - Exam results with marks and grades

**Relationships:**
- `subjects.course_id` → `courses.course_id`
- `students.course_id` → `courses.course_id`
- `results.student_id` → `students.student_id`
- `results.subject_id` → `subjects.subject_id`

See `database_schema.sql` for complete schema.

---

## 🔒 Security

- ✅ HTTPS enforced with AWS ACM certificate
- ✅ SQL injection prevention (parameterized queries)
- ✅ Input validation (client & server-side)
- ✅ Sensitive data protected (.gitignore)
- ✅ Security headers (X-Frame-Options, X-XSS-Protection)
- ✅ EC2 restricted to ALB traffic only

---

## 🤝 Contributing

Contributions are welcome!

1. Fork the repository
2. Create feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit changes (`git commit -m 'Add AmazingFeature'`)
4. Push to branch (`git push origin feature/AmazingFeature`)
5. Open Pull Request

---

## 📄 License

This project is licensed under the MIT License - see [LICENSE](LICENSE) for details.

---

## 👨‍💻 Author

**Santhosh Raj V**
- GitHub: [@vsanthoshraj](https://github.com/vsanthoshraj)
- Email: santhoshrajv10@gmail.com
- Repository: [university-exam-result-management-system](https://github.com/vsanthoshraj/university-exam-result-management-system)

---

## 📞 Support

- **GitHub Issues:** [Open an issue](https://github.com/vsanthoshraj/university-exam-result-management-system/issues)
- **Email:** santhoshrajv10@gmail.com
- **Documentation:** See [INSTALLATION.md](INSTALLATION.md)

---

## ⭐ Star This Repository

If you find this project useful, please give it a star on GitHub!

---

**Made with ❤️ for educational institutions**
