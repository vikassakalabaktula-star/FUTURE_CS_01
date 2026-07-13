# Security Audit Evidence Report
## Target: zero.webappsecurity.com

**Scan Date:** 2026-07-12  
**Scan Duration:** 58.40 seconds  
**Scanner:** Nmap 7.94SVN + OWASP ZAP  
**Target IP:** 54.82.22.214 (AWS EC2 Instance)

---

## 1. Network Reconnaissance Findings

### Host Discovery
- **Domain:** zero.webappsecurity.com
- **IP Address:** 54.82.22.214
- **Reverse DNS:** ec2-54-82-22-214.compute-1.amazonaws.com
- **Host Status:** Up (0.38s latency)
- **Filtered Ports:** 997 TCP ports (no-response)

### Nmap Scan Output
![Nmap Reconnaissance Scan](./images/nmap-scan-output.png)

### Open Ports Identified

| PORT | STATE | SERVICE | VERSION |
|------|-------|---------|---------|
| 80/tcp | open | HTTP | Apache Tomcat/Coyote JSP engine 1.1 |
| 443/tcp | open | HTTPS/SSL | Apache Tomcat/Coyote JSP engine 1.1 |
| 8080/tcp | open | HTTP | Apache Tomcat/Coyote JSP engine 1.1 |

**Scan Details from Output:**
- Host is up (0.38s latency)
- rDNS record confirmed: ec2-54-82-22-214.compute-1.amazonaws.com
- 997 filtered TCP ports (no-response)
- Multiple service banners detected with version information
- HTTP methods identified: PUT DELETE TRACE PATCH (risky)
- SSL/TLS support confirmed on port 443

---

## 2. HTTP Methods and Vulnerabilities

### Risky HTTP Methods Detected
```
Potentially risky methods: PUT DELETE TRACE PATCH
```

**Risk Level:** HIGH  
**Description:** The web server supports potentially dangerous HTTP methods that could allow:
- Data modification via PUT
- Data deletion via DELETE
- Information disclosure via TRACE
- Potential security bypass via PATCH

---

## 3. SSL/TLS Security Configuration

### SSL/TLS Certificate Information
- **Common Name:** zero.webappsecurity.com
- **Organization:** Micro Focus LLC
- **State/Province:** California
- **Country:** US
- **Certificate Validity:** 2021-04-26 to 2022-05-04 (EXPIRED)
- **Issue Date:** 2026-07-12T11:30:02+00:00

### SSL/TLS Supported Versions
```
SSLv2: Supported
Ciphers Detected:
- SSL2_DES_64_CBC_WITH_MD5
- SSL2_RC4_128_WITH_MD5
- SSL2_RC2_128_CBC_WITH_MD5
- SSL2_DES_192_EDE3_CBC_WITH_MD5
- SSL2_RC4_128_EXPORT40_WITH_MD5
- SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
```

**Risk Level:** CRITICAL  
**Issues Identified:**
1. **Expired Certificate** - Certificate expired on 2022-05-04
2. **SSLv2 Support** - Deprecated and insecure protocol
3. **Weak Ciphers** - MD5-based ciphers are cryptographically broken
4. **Export-grade Ciphers** - Severely weakened for export compliance

---

## 4. Web Application Vulnerabilities (ZAP Scan)

### ZAP Automated Scan Interface
![OWASP ZAP Automated Scan](./images/zap-scan-interface.png)

### SQL Injection - SQLite (Time Based)
**Risk Level:** 🔴 **HIGH**

**URL:** `http://zero.webappsecurity.com/search.html?searchTerm=ZAP`  
**Parameter:** searchTerm  
**CWE ID:** 89  
**WASC ID:** 19  
**Confidence:** Medium  

### ZAP Scan Results Detail
![ZAP SQL Injection Detection Results](./images/zap-sql-injection-results.png)

**Attack Pattern:**
```
case randomblob(100000) when not null then 1 else 1 end
```

**Evidence:**
- Query time controllable using parameter value
- Query execution time: [1.924] milliseconds (original unmodified)
- Query execution time: [1.935] milliseconds (with parameter modification)
- Time delay caused by ZAP test payload
- Request Method: GET
- Request URL: `http://zero.webappsecurity.com/search.html?searchTerm=ZAP`

**Remediation:**
- Do not trust client side input
- Implement server-side input validation
- Use PreparedStatement or CallableStatement with parameters passed by '?'
- Type check all data on the server side

---

### Identified Alerts from ZAP Scan

1. ✅ **SQL Injection - SQLite (Time Based)** - HIGH
2. ✅ **Content Security Policy (CSP) Header Not Set** - SYSTEMATIC
3. ✅ **Cross-Domain Misconfiguration** - SYSTEMATIC
4. ✅ **Hidden File Found** - MEDIUM
5. ✅ **Missing Anti-clickjacking Header** (4) - MEDIUM
6. ✅ **Vulnerable JS Library** - MEDIUM
7. ✅ **In Page Banner Information Leak** (2) - LOW
8. ✅ **Server Leaks Version Information via "Server" HTTP Response Header** - LOW
9. ✅ **X-Content-Type-Options Header Missing** - SYSTEMATIC
10. ✅ **Information Disclosure - Suspicious Comments** - LOW
11. ✅ **Modern Web Application** (3) - LOW
12. ✅ **User Agent Fuzzer** - SYSTEMATIC

---

## 5. Application Information Disclosure

### Browser Inspection & Network Analysis
![Application Interface Analysis](./images/browser-inspection.png)

### HTTP Response Headers Analysis
```
Server: Apache-Coyote/1.1
HTTP-Server-Header: Apache-Coyote/1.1
HTTP-Title: Zero - Personal Banking - Loans - Credit Cards
```

**Risk:** Information about server software and version is publicly disclosed.

### Active Scan Results
- **Status Code:** 200 OK
- **Remote Address:** 54.82.22.214
- **Referrer Policy:** strict-origin-when-cross-origin
- **Connection:** Keep-Alive
- **Content-Language:** en-GB
- **Content-Type:** text/html;charset=UTF-8

### Network Resources Identified
- **Main Domain:** zero.webappsecurity.com
- **CSS Resources:** bootstrap.min.css, font-awesome.css
- **JavaScript Libraries:** jquery-1.8.2.min.js, bootstrap.min.js
- **Images:** main-carousel_1.jpg, main-carousel_2.jpg, main-carousel_3.jpg, placeholders.min.js
- **Fonts:** fontawesome-webfont.woff

**Security Headers Missing:**
- No CSP (Content Security Policy)
- No X-Frame-Options
- No X-Content-Type-Options
- No HSTS (HTTP Strict-Transport-Security)

---

## 6. Evidence Summary

### Critical Findings
1. ⚠️ **Expired SSL Certificate** - Certificate validity expired in 2022
2. ⚠️ **SSLv2 Protocol Support (Deprecated)** - Severe cryptographic weakness
3. ⚠️ **Weak SSL/TLS Ciphers with MD5** - Cryptographically broken algorithms
4. ⚠️ **SQL Injection Vulnerability (Time-Based)** - Database manipulation possible
5. ⚠️ **Dangerous HTTP Methods Enabled (PUT, DELETE, TRACE, PATCH)** - Unauthorized actions possible

### High Priority Issues
6. ⚠️ **Missing Security Headers (CSP, X-Content-Type-Options)** - Vulnerable to injection attacks
7. ⚠️ **Server Version Disclosure** - Information leakage via headers
8. ⚠️ **Cross-Domain Misconfiguration** - Potential CORS vulnerabilities
9. ⚠️ **Vulnerable JavaScript Libraries** - Outdated jQuery (v1.8.2)

### Medium Priority Issues
10. ⚠️ **Missing Anti-clickjacking Headers** - Application vulnerable to clickjacking
11. ⚠️ **Hidden Files Exposed** - Directory enumeration possible
12. ⚠️ **Suspicious Comments in Response** - Information disclosure

---

## 7. Recommendations

### Immediate Actions (Critical)
1. Update and replace expired SSL certificate with valid one
2. Disable SSLv2 and weak ciphers (use TLS 1.2+)
3. Patch SQL injection vulnerability with parameterized queries
4. Disable unnecessary HTTP methods (PUT, DELETE, TRACE, PATCH)

### Short-term Actions (High)
5. Implement comprehensive security headers:
   - Content-Security-Policy
   - X-Frame-Options: DENY
   - X-Content-Type-Options: nosniff
   - Strict-Transport-Security
6. Remove version disclosure from HTTP headers
7. Fix cross-domain configuration
8. Update vulnerable JavaScript libraries (jQuery to latest stable version)

### Ongoing Security Practices
9. Implement Web Application Firewall (WAF)
10. Regular security scanning and penetration testing
11. Security training for development team
12. Implement secure coding practices and code reviews
13. Enable comprehensive logging and monitoring

---

## 8. Scan Configuration Details

**Nmap Command Used:**
```bash
nmap -sV -sC -Pn zero.webappsecurity.com
```

**Scan Parameters:**
- `-sV`: Service version detection
- `-sC`: Default script scanning
- `-Pn`: Treat all hosts as online (skip ping)
- Target: zero.webappsecurity.com (54.82.22.214)

**OWASP ZAP Configuration:**
- Automated Scan Mode
- URL Target: http://zero.webappsecurity.com
- Scanner: ZAP by Checkmarx v2.17.0
- Alert Threshold: All risks included

---

**Report Generated:** 2026-07-12  
**Report Updated:** 2026-07-13  
**Tools Used:** Nmap 7.94SVN, OWASP ZAP by Checkmarx, Browser DevTools  
**Auditor:** Security Testing Team  
**Classification:** Internal Security Audit
