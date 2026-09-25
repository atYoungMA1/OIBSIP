# Task 7 – Vulnerability Scanning with Nikto

**Internship:** Oasis Infobyte (OIBSIP) – Security Analyst  
**Student:** Prince Quarcoo  
**Date:** September 2026

---

## What is Nikto?

Nikto is an open-source web server vulnerability scanner. It tests web servers for dangerous files, outdated server software, and other common security issues. It is useful for quickly finding known problems on web applications.

---

## Limitations of Nikto

Nikto is a **noisy** scanner. This means it sends a large number of requests in a short time and is easy to detect in logs or by intrusion detection systems. It is not a stealthy tool and should only be used on systems you own or have permission to test. It also does not perform deep exploitation — it only reports potential issues.

---

## Difference between Nikto and Nmap

- **Nmap** is mainly used for network discovery, port scanning, and service detection.
- **Nikto** is specialized in finding web application and web server vulnerabilities.

They are often used together: Nmap finds open ports, and Nikto scans the web services running on those ports.

---

## Target Used

- **Application:** DVWA (Damn Vulnerable Web Application)
- **URL:** http://localhost/DVWA
- **Environment:** Kali Linux virtual machine

---

## Scans Performed

1. Basic scan  
   Command: `nikto -h http://localhost/DVWA`

2. Scan with results saved to file  
   Command: `nikto -h http://localhost/DVWA > nikto_scan_results.txt`

3. SSL scan attempt  
   Command: `nikto -h http://localhost/DVWA -ssl`  
   (This failed as expected because DVWA is running on HTTP only)

---

## Key Findings and Analysis

### 1. Missing Security Headers
**Severity:** Medium / High  
**Description:** Several important security headers were missing (Strict-Transport-Security, Content-Security-Policy, X-Content-Type-Options, Referrer-Policy, Permissions-Policy).  
**Risk:** Makes the application more vulnerable to clickjacking, XSS, and MIME-type attacks.  
**Remediation:** Configure the web server (Apache) to send the recommended security headers.

### 2. Directory Indexing Enabled
**Severity:** Medium  
**Description:** Directory listing was possible in folders such as `/DVWA/config/`, `/DVWA/tests/`, and `/DVWA/docs/`.  
**Risk:** Attackers can see file names and structure of the application.  
**Remediation:** Disable directory indexing in the Apache configuration (`Options -Indexes`).

### 3. Sensitive Files Exposed (.git, config files)
**Severity:** High  
**Description:** `.git` directory and configuration-related files were accessible.  
**Risk:** Source code and sensitive configuration information can be leaked.  
**Remediation:** Restrict access to `.git` and sensitive directories using `.htaccess` or web server rules. Never deploy `.git` folders to production.

### 4. Outdated or Informative Server Details
**Severity:** Low / Informational  
**Description:** Server version (Apache) and other details were disclosed.  
**Risk:** Helps attackers identify known vulnerabilities for that version.  
**Remediation:** Hide server version information and keep the server updated.

---

## Conclusion

Nikto successfully identified multiple common web security issues on the DVWA application. Most of the findings are related to missing security headers and improper directory permissions. These issues are typical of intentionally vulnerable applications like DVWA and highlight the importance of secure web server configuration.

---

## Screenshots

Nikto Running (nikto_running.png)
Nikto final output (nikto_final_output.png)


---

## References

- Nikto Official GitHub: https://github.com/sullo/nikto
- OWASP Top 10: https://owasp.org/www-project-top-ten/
- DVWA: https://github.com/digininja/DVWA
