# 🔐 Big Data Security – Complete Notes

---

## 🔵 UNIT I: Introduction to Big Data and Data Security Basics

### 1. Overview of Big Data
Big Data refers to datasets so large or complex that traditional data processing tools can't manage them efficiently.

**Characteristics (5 Vs):**
- **Volume** – Data size is in terabytes to zettabytes.
- **Velocity** – Data is generated rapidly (e.g., real-time streaming, social media).
- **Variety** – Structured, semi-structured, and unstructured data (e.g., logs, videos).
- **Veracity** – Data quality and trustworthiness can vary.
- **Value** – Insights or advantages extracted from data.

### 2. Use Cases of Big Data
- **Healthcare** – Predictive diagnostics, genomics.
- **Retail** – Customer behavior analysis, inventory prediction.
- **Finance** – Fraud detection, credit scoring.
- **Manufacturing** – Predictive maintenance, supply chain optimization.
- **Government** – Smart cities, crime prediction.
- **Education** – Learning analytics, adaptive learning systems.

### 3. Introduction to Data Security
Protecting digital information from unauthorized access, corruption, or theft across its lifecycle—at rest, in use, or in transit.

### 4. Data Management
Efficiently organizing, storing, and maintaining secure data.

### 5. Importance of Data Security
- Avoid reputational damage
- Comply with GDPR, HIPAA
- Prevent data theft or loss
- Ensure business continuity

### 6. Enterprise-Level Damage Due to Data Breach
- **Financial Losses** – Lawsuits, penalties
- **Regulatory Fines** – GDPR: Up to €20M or 4% global turnover
- **Loss of Trust & Reputation**

### 7. Prevention of Data Breaches
- Role-based Access Control
- Data Encryption
- Monitoring & Logging
- Security Awareness Training

### 8. CIA Triad
- **Confidentiality** – Only authorized access
- **Integrity** – Data is accurate and unaltered
- **Availability** – Data is available when needed

### 9. Data Securing Controls
- **Physical** – Biometric access, CCTV
- **Technical** – Encryption, firewalls
- **Administrative** – Policies, training

### 10. Common Data Security Pitfalls
- Weak passwords
- Unpatched systems
- No incident response plan

### 11. Data Discovery and Classification
- **Discovery** – Locate data across systems
- **Classification** – Tag data by sensitivity (e.g., Public, Confidential)

### 12. Data Encryption
Makes data unreadable without a key.

- **Symmetric** – Same key for encryption/decryption (e.g., AES)
- **Asymmetric** – Public-private key pair (e.g., RSA)

### 13. Data Anonymization
Removing personal identifiers to protect privacy.

### 14. Tokenization
Replacing sensitive data with a meaningless token. Real data stored in a vault.

### 15. Risk Assessment
Identify threats/vulnerabilities to prioritize responses.

### 16. Data Auditing
Track access, changes, and anomalies.

### 17. Real-Time Alerting
Systems monitor and send alerts for suspicious activity.

### 18. Data Minimization
- Collect only what's needed
- Retain only as long as required

---

## 📚 Glossary of Key Terms

| Term      | Full Form & Meaning |
|-----------|----------------------|
| **OWASP** | Open Web Application Security Project – Publishes Top 10 web security risks |
| **SANS**  | SysAdmin, Audit, Network, Security – Top 25 most dangerous coding errors |
| **GDPR**  | General Data Protection Regulation – EU data protection law |
| **HIPAA** | Health Insurance Portability and Accountability Act – US health data protection |
| **SOX**   | Sarbanes-Oxley Act – US corporate financial reporting law |
| **PCI-DSS** | Payment Card Industry Data Security Standard – Protects cardholder data |

---

## 🟢 UNIT II: Web Application Security

### 1. What is Web Application Security?
Protects web apps from:
- XSS (Cross-Site Scripting)
- SQL Injection
- CSRF (Cross-Site Request Forgery)

### 2. Importance of Web Application Firewall (WAF)
- Filters HTTP traffic
- Blocks OWASP Top 10 attacks

### 3. Vulnerability Scanning Tools & Techniques
**Tools:** Nessus, Nikto, Acunetix, OpenVAS, Burp Suite  
**Techniques:** Port scanning, fuzzing, brute-force login

### 4. Difference: VA vs PT

| Feature           | Vulnerability Assessment (VA) | Penetration Testing (PT)     |
|------------------|-------------------------------|------------------------------|
| Purpose          | Identify weaknesses           | Simulate actual attack       |
| Method           | Automated scanning            | Manual + automated           |
| Output           | List of vulnerabilities        | Exploited vulnerabilities    |

### 5. OWASP Top 10
Most critical web vulnerabilities:
- Injection
- Broken Authentication
- Sensitive Data Exposure
- Security Misconfiguration  
🔗 [OWASP Website](https://owasp.org/www-project-top-ten/)

### 6. SANS Top 25
Common dangerous software errors:
- Buffer overflows
- Integer overflows
- Input validation flaws  
🔗 [SANS Top 25](https://www.sans.org/top25)

---

## 🟠 UNIT III: Data Security Compliance and Standards

### 1. PII, PHI, Sensitive Data
- **PII** – Name, address, SSN
- **PHI** – Medical data (lab reports, history)
- **Sensitive Data** – Passwords, bank info, government IDs

### 2. GDPR – General Data Protection Regulation
- **Scope** – EU personal data
- **Fines** – Up to €20M or 4% turnover
- **Deadline** – Report breaches within 72 hours

### 3. HIPAA – Health Insurance Portability and Accountability Act
- U.S. law for health data
- Applies to healthcare providers, insurers

### 4. SOX – Sarbanes-Oxley Act
- U.S. law requiring financial data accuracy
- Enforces IT controls for audit trails

### 5. PCI-DSS – Payment Card Industry Data Security Standard
- Applies to businesses that process card payments
- Requires data encryption, secure access, and logging

### 6. ISO 27000 Series
- International standards for information security
- ISO 27001 focuses on ISMS (Information Security Management System)

### 7. Data Protection by Design
Security should be integrated during system design—not added later.

---

## 🔴 UNIT IV: Data Security Technologies and Solutions

### 1. Modern Data Security Technologies
- Firewalls
- DLP (Data Loss Prevention)
- SIEM (Security Info and Event Management)
- CASBs (Cloud Access Security Brokers)

### 2. Data Protection Modernization
- From perimeter security to data-centric
- Uses AI/ML for proactive detection

### 3. Data Lifecycle Security

| Phase     | Security Measures                   |
|-----------|-------------------------------------|
| Creation  | Classification                      |
| Storage   | Encryption, role-based access       |
| Usage     | Logging, real-time alerts           |
| Sharing   | Tokenization, masking               |
| Archival  | Retention policies, secure backup   |
| Destruction | Wiping, degaussing                |

### 4. Data Risk Manager
- Identifies high-risk data
- Uses dashboards, risk heatmaps

### 5. Privacy Strategies
- **Zero Trust** – Never assume trust
- **Data Masking** – Hide sensitive values
- **Consent Management** – User data rights

---

## 🟣 UNIT V: IBM Guardium Data Protection

### 1. IBM Guardium Overview
Platform that secures databases, files, cloud data

### 2. Features
- Monitors activity
- Detects unauthorized access
- Generates compliance reports

### 3. Architecture
- **Collector** – Captures data logs
- **Aggregator** – Centralizes analysis
- **Console** – GUI for dashboards and alerts

### 4. Deployment
- On-premise/cloud
- Agent-based or agentless

### 5. Supported Databases
- Oracle, MySQL, DB2, SQL Server, PostgreSQL, SAP HANA, NoSQL

---

## 🟤 UNIT VI: Monitoring Policies and Reporting

### 1. Monitoring Policies
Define:
- What to monitor
- Who to monitor
- Thresholds for alerts

### 2. Audit Process
Tracks:
- Who accessed what
- When and from where
- What actions were performed

### 3. Entities and Groups
- **Entities** – Users, DBs, IPs
- **Groups** – Collections of entities for easier policy application

### 4. Security Policies
Rules to detect violations (e.g., out-of-hours access)

### 5. Reports and Dashboards
- Real-time and historical data
- Visual widgets
- Compliance snapshots

### 6. SIEM Integration
Guardium integrates with:
- **IBM QRadar**
- **Splunk**
- **ArcSight**

### 7. Self-Monitoring
Guardium monitors:
- Log errors
- Policy failures
- Resource usage

---

## 👤 Author & Contributors

**Primary Maintainer:** [Anand Maurya](https://github.com/coderanandmaurya)

✅ **Last Updated**: July 30, 2025  

This repository is maintained as a public educational resource.  
Feel free to fork, star ⭐, and contribute to improve its quality and reach!

---

## 📝 Attribution & Disclaimer

This project references **IBM Guardium** to demonstrate data protection practices in real-world enterprise environments.  

**IBM Guardium** is a product and trademark of **International Business Machines Corporation (IBM)**.  
All product names, trademarks, and registered trademarks are the property of their respective owners.

> ⚠️ This repository is not affiliated with or endorsed by IBM. It is intended solely for educational and non-commercial purposes.

