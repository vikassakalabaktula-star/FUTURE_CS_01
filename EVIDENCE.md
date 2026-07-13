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

### Open Ports Identified

| PORT | STATE | SERVICE | VERSION |
|------|-------|---------|---------|
| 80/tcp | open | HTTP | Apache Tomcat/Coyote JSP engine 1.1 |
| 443/tcp | open | HTTPS/SSL | Apache Tomcat/Coyote JSP engine 1.1 |
| 8080/tcp | open | HTTP | Apache Tomcat/Coyote JSP engine 1.1 |

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

### SQL Injection - SQLite (Time Based)
**Risk Level:** 🔴 **HIGH**

**URL:** `http://zero.webappsecurity.com/search.html?searchTerm=ZAP`  
**Parameter:** searchTerm  
**CWE ID:** 89  
**WASC ID:** 19  
**Confidence:** Medium  

**Attack Pattern:**
```
case randomblob(100000) when not null then 1 else 1 end
```

**Evidence:**
- Query time controllable using parameter value
- Query execution time: [1.924] milliseconds (original unmodified)
- Query execution time: [1.935] milliseconds (with parameter modification)
- Time delay caused by ZAP test payload

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

---

## 6. Evidence Summary

### Critical Findings
1. ⚠️ **Expired SSL Certificate**
2. ⚠️ **SSLv2 Protocol Support (Deprecated)**
3. ⚠️ **Weak SSL/TLS Ciphers with MD5**
4. ⚠️ **SQL Injection Vulnerability (Time-Based)**
5. ⚠️ **Dangerous HTTP Methods Enabled (PUT, DELETE, TRACE, PATCH)**

### High Priority Issues
6. ⚠️ **Missing Security Headers (CSP, X-Content-Type-Options)**
7. ⚠️ **Server Version Disclosure**
8. ⚠️ **Cross-Domain Misconfiguration**
9. ⚠️ **Vulnerable JavaScript Libraries**

### Medium Priority Issues
10. ⚠️ **Missing Anti-clickjacking Headers**
11. ⚠️ **Hidden Files Exposed**

---

## 7. Recommendations

### Immediate Actions (Critical)
1. Update and replace expired SSL certificate
2. Disable SSLv2 and weak ciphers
3. Patch SQL injection vulnerability with parameterized queries
4. Disable unnecessary HTTP methods (PUT, DELETE, TRACE, PATCH)

### Short-term Actions (High)
5. Implement comprehensive security headers (CSP, HSTS, X-Frame-Options)
6. Remove version disclosure from HTTP headers
7. Fix cross-domain configuration
8. Update vulnerable JavaScript libraries

### Ongoing Security Practices
9. Implement Web Application Firewall (WAF)
10. Regular security scanning and updates
11. Security training for development team
12. Implement secure coding practices

---

**Report Generated:** 2026-07-12  
**Tools Used:** Nmap 7.94SVN, OWASP ZAP by Checkmarx  
**Auditor:** Security Testing Team
