# 🛡️ Damn Vulnerable Source Code (DVSC)

[![Security](https://img.shields.io/badge/Focus-Vulnerable%20Code%20Analysis-red?style=flat-square)](https://github.com/sairam-bathini/Damn-Vulnerable-Source-Code)
[![Languages](https://img.shields.io/badge/Languages-Multi%20Platform-blue?style=flat-square)](https://github.com/sairam-bathini/Damn-Vulnerable-Source-Code)
[![OWASP](https://img.shields.io/badge/Based%20On-OWASP%20Top%2010-brightgreen?style=flat-square)](https://owasp.org/www-project-top-ten/)
[![Educational](https://img.shields.io/badge/Purpose-Educational-yellowgreen?style=flat-square)](https://github.com/sairam-bathini/Damn-Vulnerable-Source-Code)

## 📋 Problem Statement

Security researchers, developers, and students need access to intentionally vulnerable source code to:
1. Understand real-world vulnerability patterns
2. Practice vulnerability identification and exploitation
3. Test security scanning tools and techniques
4. Learn secure coding practices through contrast
5. Build secure code analysis skills

Manually creating vulnerable applications is time-consuming and often incomplete. This project provides curated, well-documented vulnerable source code in multiple programming languages based on OWASP Top 10.

## 🎯 What It Does

DVSC is an open-source collection of intentionally vulnerable applications designed to demonstrate common security flaws. Each vulnerability includes:

- **Vulnerable Code** - Real exploitable code samples
- **Vulnerability Details** - CWE, CVSS, OWASP mapping
- **Exploitation Guide** - Step-by-step attack walkthroughs
- **Secure Code** - Remediated, secure alternatives
- **Testing Tools** - How to detect with burp, OWASP ZAP, etc.
- **References** - OWASP, CWE, and security best practices

### Supported Languages (Current & Planned):
- ✅ **Python** (Flask) - Complete
- 🔄 **Java** (Spring) - In Progress
- 🔄 **PHP** - In Progress
- 🔄 **Node.js** (Express) - In Progress
- 📋 **C#** (.NET) - Planned
- 📋 **Go** - Planned
- 📋 **Ruby** - Planned

## 🛠️ Tech Stack

| Language | Framework | Database | Auth | Testing |
|----------|-----------|----------|------|---------|
| Python | Flask 2.0+ | SQLite/PostgreSQL | Custom/OAuth | Pytest |
| Java | Spring Boot 2.6+ | MySQL | Spring Security | JUnit |
| PHP | Laravel/Raw | MySQL | Custom | PHPUnit |
| Node.js | Express 4.x | MongoDB | Passport | Mocha |

## 🚀 How to Run

### Prerequisites

```bash
# Check versions
python --version      # 3.8+
java -version         # 11+
node --version        # 14+
docker --version      # 20.10+
```

### Option 1: Python (Flask) Application

```bash
# 1. Clone repository
git clone https://github.com/sairam-bathini/Damn-Vulnerable-Source-Code.git
cd Damn-Vulnerable-Source-Code/python-flask

# 2. Create virtual environment
python -m venv venv
source venv/bin/activate  # Linux/Mac
# or
venv\Scripts\activate     # Windows

# 3. Install dependencies
pip install -r requirements.txt

# 4. Initialize database
python create_db.py

# 5. Run application
python app.py

# 6. Access at http://localhost:5000
# Default credentials: admin / admin123
```

### Option 2: Docker (All Languages)

```bash
# Build Python Flask vulnerable app
docker build -t dvsc-python ./python-flask
docker run -p 5000:5000 dvsc-python

# Build Java Spring vulnerable app
docker build -t dvsc-java ./java-spring
docker run -p 8080:8080 dvsc-java

# Run all with Docker Compose
docker-compose up -d

# Access applications:
# Python: http://localhost:5000
# Java: http://localhost:8080
# PHP: http://localhost:8081
```

### Option 3: Manual Setup (Each Language)

```bash
# Python
cd python-flask && pip install -r requirements.txt && python app.py

# Java
cd java-spring && mvn spring-boot:run

# Node.js
cd nodejs-express && npm install && npm start

# PHP
cd php-laravel && composer install && php artisan serve
```

## 📊 Vulnerability Catalog

### OWASP Top 10 Coverage

| Rank | Vulnerability | Count | Severity | Example |
|------|---------------|-------|----------|---------|
| A01 | Broken Access Control | 5 | HIGH | Forced browsing, privilege escalation |
| A02 | Cryptographic Failures | 4 | CRITICAL | Weak hashing, hardcoded keys |
| A03 | Injection | 6 | CRITICAL | SQL, LDAP, OS command injection |
| A04 | Insecure Design | 3 | MEDIUM | Missing rate limiting, weak validation |
| A05 | Security Misconfiguration | 4 | MEDIUM | Debug mode on, default credentials |
| A06 | Vulnerable Components | 3 | HIGH | Outdated libraries with known CVEs |
| A07 | Identification & Auth | 5 | CRITICAL | Weak password policy, session fixation |
| A08 | Data Integrity Failures | 2 | HIGH | Insecure deserialization |
| A09 | Logging & Monitoring | 3 | MEDIUM | Missing audit logs, error exposure |
| A10 | SSRF | 2 | HIGH | Server-side request forgery |

### Sample Vulnerabilities

#### 1. SQL Injection (Critical)

**File:** `app.py` / `UserController.java` / `index.php`

**Vulnerable Code (Python):**
```python
@app.route('/user/<user_id>')
def get_user(user_id):
    query = f"SELECT * FROM users WHERE id = {user_id}"  # ❌ VULNERABLE
    user = db.execute(query).fetchone()
    return render_template('user.html', user=user)
```

**Attack:**
```
http://localhost:5000/user/1 UNION SELECT password FROM admin
```

**Secure Code:**
```python
@app.route('/user/<user_id>')
def get_user(user_id):
    query = "SELECT * FROM users WHERE id = ?"  # ✅ SECURE
    user = db.execute(query, (user_id,)).fetchone()
    return render_template('user.html', user=user)
```

**Testing:**
```bash
# Manual testing with curl
curl "http://localhost:5000/user/1' UNION SELECT 1,2,3--"

# Automated with SQLmap
sqlmap -u "http://localhost:5000/user/1" --dbs
```

#### 2. Cross-Site Scripting (XSS) (High)

**Vulnerable Code:**
```html
<!-- Python Flask template -->
<div>
  <p>Welcome, {{ username }}</p>  <!-- ❌ Not escaped -->
</div>
```

**Attack:**
```
Register with username: <script>alert('XSS')</script>
```

**Secure Code:**
```html
<div>
  <p>Welcome, {{ username|escape }}</p>  <!-- ✅ Escaped -->
</div>
```

#### 3. Hardcoded Credentials (Critical)

**Vulnerable Code:**
```python
# ❌ VULNERABLE
DATABASE_URL = "postgresql://admin:P@ssw0rd123@localhost/dvsc"
SECRET_KEY = "hardcoded_secret_key_12345"
API_KEY = "sk_live_abc123def456"
```

**Secure Code:**
```python
# ✅ SECURE
DATABASE_URL = os.getenv('DATABASE_URL')
SECRET_KEY = os.getenv('SECRET_KEY')
API_KEY = os.getenv('API_KEY')
```

#### 4. Broken Authentication (Critical)

**Vulnerable Code:**
```python
def login(username, password):
    user = User.query.filter_by(username=username).first()
    if user.password == password:  # ❌ Plain text comparison
        session['user_id'] = user.id
        return True
    return False
```

**Secure Code:**
```python
def login(username, password):
    user = User.query.filter_by(username=username).first()
    if user and bcrypt.checkpw(password.encode(), user.password_hash):
        session['user_id'] = user.id
        session.permanent = False
        return True
    return False
```

## 📈 Application Structure

```
Damn-Vulnerable-Source-Code/
├── README.md                          # This file
├── VULNERABILITY_LIST.md              # All vulnerabilities explained
├── SETUP_GUIDE.md                     # Detailed setup instructions
├── docker-compose.yml                 # Multi-container setup
│
├── python-flask/
│   ├── app.py                         # Main Flask application
│   ├── models.py                      # Database models
│   ├── templates/
│   │   ├── index.html                 # Login page (XSS vulnerable)
│   │   ├── signup.html                # Registration (SQL injection)
│   │   ├── homepage.html              # Dashboard (IDOR)
│   │   ├── admin.html                 # Admin panel (auth bypass)
│   │   └── error.html                 # Error handling (info disclosure)
│   ├── requirements.txt
│   ├── Dockerfile
│   └── VULNERABILITIES.md             # Python-specific issues
│
├── java-spring/
│   ├── pom.xml
│   ├── src/main/java/
│   │   ├── Application.java
│   │   ├── controller/                # Vulnerable endpoints
│   │   ├── service/                   # Business logic flaws
│   │   └── security/                  # Auth issues
│   ├── Dockerfile
│   └── VULNERABILITIES.md
│
├── nodejs-express/                    # (Planned)
├── php-laravel/                       # (Planned)
└── docs/
    ├── OWASP_MAPPING.md               # OWASP Top 10 coverage
    ├── CWE_REFERENCES.md              # CWE mappings
    ├── TESTING_GUIDE.md               # How to find vulnerabilities
    └── REMEDIATION_GUIDE.md           # How to fix them
```

## 🔐 Security Impact

### Real-World Attack Scenarios

**Scenario 1: SQL Injection Attack**
```
Attacker: Uses /user/1' OR '1'='1' to extract all users
Impact: Exposure of all user records (GDPR violation)
Business Loss: $50K+ GDPR fine, customer trust
```

**Scenario 2: Cross-Site Scripting**
```
Attacker: Injects JavaScript to steal session cookies
Impact: Account takeover of all users who view malicious comment
Business Loss: $100K+ in fraud, reputation damage
```

**Scenario 3: Broken Authentication**
```
Attacker: Bypasses login with SQL injection or weak hashing
Impact: Unauthorized access to admin panel
Business Loss: Data breach, regulatory investigation
```

**Scenario 4: Insecure Direct Object Reference (IDOR)**
```
Attacker: Changes /profile/123 to /profile/456 to view others' data
Impact: Privacy violation, PII exposure
Business Loss: HIPAA/CCPA violation ($100K-$1M), lawsuits
```

## 🎓 Learning Outcomes

After working with DVSC, you will understand:

✅ How SQL injection works and how to prevent it  
✅ XSS exploitation techniques and mitigation  
✅ Authentication & authorization bypasses  
✅ Cryptographic failures and secure hashing  
✅ Secure configuration best practices  
✅ Input validation and sanitization  
✅ Error handling without information disclosure  
✅ Security testing methodologies  
✅ How to use security scanning tools effectively  
✅ OWASP Top 10 vulnerabilities in real code  

## 🏆 Key Features

✅ **Multi-Language Support** - Learn vulnerabilities across platforms  
✅ **Well-Documented** - Every vulnerability explained  
✅ **Progressive Difficulty** - Beginner to advanced  
✅ **Hands-On Learning** - Exploit, then fix  
✅ **Tool Integration** - Works with Burp, OWASP ZAP, etc.  
✅ **Automated Setup** - Docker makes it trivial  
✅ **Active Community** - Contributing developers welcome  
✅ **Educational License** - Free for learning  

## 📚 Resources

| Resource | Link |
|----------|------|
| OWASP Top 10 | https://owasp.org/www-project-top-ten/ |
| CWE/SANS Top 25 | https://cwe.mitre.org/top25/ |
| PortSwigger Web Security | https://portswigger.net/web-security |
| OWASP Cheat Sheets | https://cheatsheetseries.owasp.org/ |
| HackTheBox | https://www.hackthebox.eu/ |
| TryHackMe | https://tryhackme.com/ |

## 🤝 Contributing

Contributions welcome! Add:
- [ ] New vulnerabilities
- [ ] Additional languages
- [ ] Exploitation walkthroughs
- [ ] Remediation guides
- [ ] Video demonstrations

## ⚖️ Legal Notice

⚠️ **EDUCATIONAL PURPOSES ONLY**

This project is designed for:
- ✅ Educational learning
- ✅ Security training
- ✅ Authorized testing
- ✅ Demonstration purposes

It is NOT for:
- ❌ Unauthorized access to systems
- ❌ Production use without modification
- ❌ Illegal activities

## 📞 Support & Community

- **Issues:** [GitHub Issues](https://github.com/sairam-bathini/Damn-Vulnerable-Source-Code/issues)
- **Discussions:** [GitHub Discussions](https://github.com/sairam-bathini/Damn-Vulnerable-Source-Code/discussions)
- **Contact:** [@sairam-bathini](https://github.com/sairam-bathini)
- **Twitter:** [@sairam_bathini](https://twitter.com/sairam_bathini)

## 📄 License

MIT License - Educational and authorized testing use only

---

**For Recruiters:** This project demonstrates:
- ✅ Deep security vulnerability expertise
- ✅ Multi-language programming proficiency
- ✅ Secure coding best practices knowledge
- ✅ Ability to teach/mentor security concepts
- ✅ Open source contribution and community engagement
- ✅ Documentation and knowledge sharing skills

**Last Updated:** 2026-06-14 | **Maintained by:** [@sairam-bathini](https://github.com/sairam-bathini)
