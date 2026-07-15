# FUTURE_CS_01 - Vulnerability Assessment Report — Zero Bank Demo Site

## Overview
This repository contains my Task 1 submission for the Future Interns Cyber Security internship — a vulnerability assessment of a public test website.

## Website Tested
http://zero.webappsecurity.com (a legitimate demo banking site by Micro Focus, built for security testing practice)

## Scope
- Passive and non-destructive testing only
- No login bypass, brute force, or active exploitation attempted
- Testing limited to publicly accessible pages

## Tools Used
- Nmap — port scanning and service/version detection
- OWASP ZAP — automated passive + active vulnerability scan
- Chrome DevTools — HTTP response header inspection

## Process
1. Ran Nmap scans to identify open ports, running services, and SSL/TLS configuration
2. Ran an OWASP ZAP automated scan (spider + passive/active analysis) against the target
3. Inspected response headers via Chrome DevTools for missing security headers and CORS config
4. Classified all findings by risk level (High/Medium/Low) and documented remediation steps

## Key Findings (Summary)
- High: SQL Injection possible via search parameter
- High: SSL certificate expired
- High: SSLv2 and weak export-grade ciphers still enabled
- Medium: Missing Content-Security-Policy and X-Frame-Options headers
- Medium: CORS misconfigured (wildcard origin)
- Medium: Risky HTTP methods (PUT/DELETE/TRACE) enabled
- Low: Server version disclosed, duplicate app exposed on port 8080

Full details, risk classification, and remediation steps are in the report PDF.

## Files in this Repo
- `report.pdf` — Full Vulnerability Assessment Report (designed in Canva)
- `evidence` — Screenshots from Nmap, ZAP, and DevTools
- `README.md`- This file

## Disclaimer
This assessment was performed on a public test/demo application intended for security practice. No production systems or unauthorized targets were tested.
