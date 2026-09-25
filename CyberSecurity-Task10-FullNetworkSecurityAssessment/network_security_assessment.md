
# TASK 10 - Full Network Security Assessment Report
**Date:** 2026-09-25 14:51 UTC (GMT-4)
**Analyst:** Prince Quarcoo
**Authorization:** Lab Assessment - 10.0.2.0/24

## 1. Executive Summary
This assessment evaluated the security posture of the lab network 10.0.2.0/24 following PTES and OWASP WSTG methodologies. Three active hosts were identified from 256 scanned.

**Overall Risk: MEDIUM - Not Production Ready, Safe for Lab.**

No remote code execution or critical CVSS 9.0+ was found. However, high-risk exposures exist on 10.0.2.2 (RDP 3389, SMB 445, Intel AMT 16992) that violate least-privilege principles. Both web services (10.0.2.15:80 Apache 2.4.68 and 10.0.2.2:8090 QEMU Gateway) fail OWASP WSTG-INFO-08 and WSTG-CONF-12 security header tests, with 9 and 5 findings respectively. The Apache /server-status disclosure is a PTES Technical Guideline - Vulnerability Analysis phase finding.

Immediate remediation is low-effort: disable server-status, implement 5 security headers, and firewall RDP/SMB/AMT to management VLAN. A re-capture of network traffic for the required 300 seconds is recommended as current capture is 94.45s (6,188 bytes, 45 packets).

## 2. Scope Definition
- **Target Range:** 10.0.2.0/24 (255.255.255.0) - 256 addresses
- **Assets in Scope:**
    - 10.0.2.2 - QEMU Virtual NIC (52:54:00:12:35:00) - Gateway / Windows-like host
    - 10.0.2.3 - DNS/Infra device
    - 10.0.2.15 - Linux host Apache/2.4.68 (Debian)
- **Services Tested:** TCP 135, 445, 3389, 53, 80, 8090, 16992
- **Time Window:** 2026-09-25 08:42:31 to 14:51:00 EST
- **Assessment Type:** Black-box Network + Web Vulnerability Assessment
- **Out of Scope:** DoS testing, social engineering, physical security

## 3. Methodology & Standards Referenced

This report follows industry frameworks as per guideline:

**A) PTES Technical Guidelines (pentest-standard.org):**
We executed 7 PTES phases:
1. Pre-engagement (scope 10.0.2.0/24 defined)
2. Intelligence Gathering (Nmap -sV -O)
3. Threat Modeling (RDP/SMB/AMT = lateral movement threat)
4. Vulnerability Analysis (Nikto v2.6.1 on 80 and 8090)
5. Exploitation - Not performed (assessment only)
6. Post-Exploitation - Not performed
7. Reporting - This document

**B) OWASP Web Security Testing Guide v4.2 (owasp.org/www-project-web-security-testing-guide/):**
- WSTG-INFO-02: Fingerprint Web Server (Apache 2.4.68 identified)
- WSTG-INFO-05: Review Web Page Content for Information Leakage (/server-status)
- WSTG-CONF-12: Test for Security Header - Not Properly Configured (Missing HSTS, CSP, X-Content-Type-Options)
- WSTG-CONF-08: Test for HTTP Methods (OPTIONS: POST, OPTIONS, HEAD, GET)

**C) CVSS v3.1 (first.org/cvss):** All findings scored with Base Score + Vector for consistency. See Findings Register.
**D) Professional Report Structure:** Based on industry best practice for penetration test reports (Executive -> Technical -> Findings -> Remediation).

## 4. Tools & Evidence
| Tool | Version | Command | Evidence File |
|---|---|---|---|
| Nmap | 7.99 | `nmap -sV -O -oN ~/nmap_results.txt 10.0.2.0/24` | nmap_results.txt (3.4K, 3 hosts up, 47 sec) |
| Wireshark | 4.x | `dumpcap` / GUI capture | wireshark_capture.pcap (6.1K, 45 pkts, 94.45 sec) + pcapng 148K |
| Nikto | 2.6.1 | `nikto -h http://10.0.2.15 -o ~/nikto_results.txt` | nikto_results.txt (8047 req, 20 sec, 9 items, 0 errors) |
| Nikto | 2.6.1 | `nikto -h http://10.0.2.2:8090 -o ~/nikto_8090.txt` | nikto_8090.txt (708B, 5 items, No banner) |
| Curl/Nmap | - | `curl -I --connect-timeout 5 http://10.0.2.2:8090` | HTTP/1.1 501 Not Implemented proof |

**Traffic Analysis Filters Used (Wireshark):**
- `http` -> 0 packets (no cleartext creds)
- `dns` -> 8 packets (normal queries)
- `arp` -> 6 packets (PCS Systemtechnik GmbH MACs - normal)
- `tcp` -> 16 packets (35.6% - SYN, ACK, Retransmission)

## 5. Technical Findings - Detailed

### Phase 1: Reconnaissance
Nmap identified 3 hosts. 10.0.2.2 exposes management interfaces that increase attack surface per PTES Threat Modeling.

### Phase 2: Traffic Analysis
Capture shows no DNS tunneling or ARP spoofing. However, capture duration fails task requirement of 300s (94s only). This limits detection of intermittent cleartext credentials per OWASP WSTG.

### Phase 3: Vulnerability Analysis - Web

**Target A: 10.0.2.15:80**
- Banner: Apache/2.4.68 (Debian)
- Finding: GET /server-status mod_status reveals Apache info - OWASP WSTG-INFO-05 - Information Disclosure
- Finding: Missing 5 security headers - OWASP WSTG-CONF-12
- Finding: X-Frame-Options deprecated, should use CSP frame-ancestors

**Target B: 10.0.2.2:8090**
- Banner: No banner retrieved - Server: Unknown
- Nmap: 8090/tcp open tcpwrapped syn-ack ttl 64 - QEMU virtual NIC
- Curl: HTTP/1.1 501 Not Implemented, Content-Type: application/json, Content-Length: 0
- Finding: Minimal API gateway, no proper implementation, hangs Nikto (required Ctrl+C and -maxtime 60)
- Finding: Same 5 missing headers as Target A

## 6. Findings Register with CVSS 3.1

| ID | Title | OWASP / PTES | Asset | CVSS Vector | Base Score | Severity |
|---|---|---|---|---|---|---|
| F-01 | Apache server-status Information Disclosure | WSTG-INFO-05 | 10.0.2.15:80 | CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:N/A:N | 5.3 | Medium |
| F-02 | Missing Strict-Transport-Security (HSTS) | WSTG-CONF-12 | 10.0.2.15:80, 10.0.2.2:8090 | CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:N/A:N | 5.3 -> adjusted to 4.3 (defense-in-depth) | Medium |
| F-03 | Missing Content-Security-Policy | WSTG-CONF-12 | Both web | CVSS:3.1/AV:N/AC:L/PR:N/UI:R/S:C/C:L/I:L/A:N | 6.1 -> 4.3 | Medium |
| F-04 | Missing X-Content-Type-Options nosniff | WSTG-CONF-12 | Both web | CVSS:3.1/AV:N/AC:L/PR:N/UI:R/S:U/C:L/I:L/A:N | 3.7 | Low |
| F-05 | Missing Referrer-Policy & Permissions-Policy | WSTG-CONF-12 | Both web | CVSS:3.1/AV:N/AC:H/PR:N/UI:N/S:U/C:L/I:N/A:N | 2.6 | Low |
| F-06 | RDP 3389 & SMB 445 Exposed - No Network Segmentation | PTES Threat Modeling | 10.0.2.2 | CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H | 7.5 | High |
| F-07 | Intel AMT 16992 & tcpwrapped 8090 Management Interface Exposed | PTES Threat Modeling | 10.0.2.2:16992,8090 | CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:N | 7.2 | High |
| F-08 | 8090 Service Returns 501 Not Implemented - Improper Error Handling | WSTG-ERR-01 | 10.0.2.2:8090 | CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:N/A:N | 2.0 | Info |
| F-09 | Network Capture Duration Insufficient (94s vs Required 300s) | Process Gap | Assessment Process | CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:N/I:N/A:N | 0.0 | Info |

**Severity Scale per CVSS:** 0.0 Info, 0.1-3.9 Low, 4.0-6.9 Medium, 7.0-8.9 High, 9.0-10.0 Critical

## 7. Remediation Roadmap (Prioritized)

| Priority | Finding | Action | Effort | OWASP / Best Practice |
|---|---|---|---|---|
| P1 | F-06 | Restrict 445/3389 via firewall, enable NLA, disable SMBv1 | Easy 5 min | CIS Benchmark |
| P1 | F-07 | Disable Intel AMT in BIOS if unused, move 8090 to mgmt VLAN | Medium 15 min | PTES Posture |
| P2 | F-01 | `a2dismod status; systemctl reload apache2` or Require ip 127.0.0.1 | Easy 2 min | OWASP WSTG-INFO-05 |
| P2 | F-02 to F-05 | Add to `/etc/apache2/conf-enabled/security.conf`: `Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"`, `Header set X-Content-Type-Options nosniff`, `Header set Content-Security-Policy "default-src 'self'"`, `Header set Referrer-Policy no-referrer`, `Header set Permissions-Policy "geolocation=()"` | Easy 10 min | OWASP Secure Headers Project |
| P3 | F-08 | Audit QEMU gateway service on host, document 501 API or disable | Medium 30 min | WSTG-ERR-01 |
| P3 | F-09 | Re-run: `dumpcap -i eth0 -w ~/wireshark_capture.pcap -a duration:300` | Easy 5 min | Task Requirement |

## 8. Conclusion
The lab network demonstrates typical hardening gaps seen in default installations. The use of PTES for lifecycle and OWASP WSTG for web-specific checks ensured comprehensive coverage. All findings are remediable within 1 hour total effort. After remediation, re-scan with Nikto and Nmap to validate.

## 9. Appendices
- **A:** nmap_results.txt
- **B:** wireshark_capture.pcap (45 packets)
- **C:** nikto_results.txt (10.0.2.15) + nikto_8090.txt (10.0.2.2)
- **D:** Screenshots: Nmap hosts up, capinfos, Wireshark filters (http/dns/arp/tcp), Nikto both targets, curl 501 proof, ls -lh
- **References:** OWASP WSTG (owasp.org), PTES Technical Guidelines (pentest-standard.org), CVSS (first.org/cvss), OWASP Secure Headers Project

---
## Demo Video
🎥 [Watch Demo Video - Task 10 ](https://youtu.be/s6ITiR0vmYk)
