---
name: blackbox-web-audit
description: Complete black-box web application security audit methodology, tooling, and reporting. USE WHEN the user needs to perform a web application pentest, plan a black-box audit, look up security testing commands or tool flags, check OWASP Top 10 2025 testing vectors, write a pentest report, score vulnerabilities with CVSS 4.0, configure proxy interception (Caido/ZAP/mitmproxy), set up a pentesting terminal workflow (tmux), or needs guidance on any phase of a web security assessment. Triggers for: web application auditing, penetration testing methodology, vulnerability assessment, security scanning tool usage, OWASP testing, pentest reporting, recon/enumeration/exploitation phases, SSL/TLS analysis, brute force attacks, injection testing (SQLi, XSS, SSRF, SSTI, RCE), JWT attacks, authentication/authorization testing, content discovery, subdomain enumeration, and any black-box web security assessment task. Also triggers when the user asks "how do I test for X vulnerability", "what tool should I use for Y", "run a scan against Z", or needs command syntax for any of the 34 supported security tools.
---

# Black-Box Web Application Audit

Complete methodology for authorized black-box web application security audits. Covers the full lifecycle from legal framework through exploitation to final reporting, with 34 tool references and actionable checklists.

All content assumes **authorized testing context** (pentesting engagements, CTFs, security labs, research).

---

## Methodology Overview — 5 Phases

```
Pre-Engagement → Recon → Enumeration → Vuln Analysis → Exploitation → Reporting
     (legal)     (passive   (active      (OWASP Top 10   (PoC +       (CVSS 4.0 +
                  + active)  scanning)    + extras)        chaining)    deliverables)
```

### Phase Flow

| Phase | Goal | Key Tools | Reference |
|-------|------|-----------|-----------|
| 0. Pre-Engagement | Legal framework, RoE, scope | N/A | `01-fundamentos-marco-legal.md` |
| 1. Reconnaissance | Map attack surface | subfinder, amass, httpx, nmap, whatweb, gospider, gau | `03-reconocimiento.md` |
| 2. Enumeration | Discover endpoints, params, tech stack | ffuf, feroxbuster, nuclei, arjun, wpscan | `04-enumeracion-escaneo.md` |
| 3. Vuln Analysis | Identify vulnerabilities (OWASP Top 10) | nuclei, nikto, owasp-zap, sqlmap, dalfox | `05-analisis-vulnerabilidades.md` |
| 4. Exploitation | Prove impact with PoC | sqlmap, commix, tplmap, jwt_tool, hydra | `06-explotacion.md` |
| 5. Reporting | CVSS scoring, deliverables | N/A | `07-post-explotacion-reporte.md` |

---

## Quick Tool Selection

When a tool is needed, use this guide to pick the right one. Read the corresponding file in `tools/` for full flags and examples.

### Reconnaissance

| Task | Tool | File |
|------|------|------|
| Port scanning (precise) | nmap | `tools/nmap.md` |
| Port scanning (fast/massive) | masscan | `tools/masscan.md` |
| Subdomain enumeration (passive) | subfinder | `tools/subfinder.md` |
| Subdomain enumeration (comprehensive) | amass | `tools/amass.md` |
| HTTP probing + fingerprinting | httpx | `tools/httpx.md` |
| Web crawling (fast) | katana | `tools/katana.md` |
| Web spidering + sitemap/wayback | gospider | `tools/gospider.md` |
| Historical URLs (Wayback Machine) | waybackurls | `tools/waybackurls.md` |
| Historical URLs (multi-source) | gau | `tools/gau.md` |
| OSINT (emails, subdomains, IPs) | theharvester | `tools/theharvester.md` |
| Technology fingerprinting | whatweb | `tools/whatweb.md` |
| WAF detection | wafw00f | `tools/wafw00f.md` |

### Content Discovery & Enumeration

| Task | Tool | File |
|------|------|------|
| Directory/file fuzzing (fastest) | ffuf | `tools/ffuf.md` |
| Recursive content discovery | feroxbuster | `tools/feroxbuster.md` |
| Directory brute force + DNS/vhosts | gobuster | `tools/gobuster.md` |
| Web vulnerability scanning | nikto | `tools/nikto.md` |
| WordPress scanning | wpscan | `tools/wpscan.md` |
| Template-based vuln scanning | nuclei | `tools/nuclei.md` |
| HTTP parameter discovery | arjun | `tools/arjun.md` |
| Custom wordlist generation | cewl | `tools/cewl.md` |

### Vulnerability Scanning & Exploitation

| Task | Tool | File |
|------|------|------|
| Full web app scanning (CLI/API/Docker) | owasp-zap | `tools/owasp-zap.md` |
| SQL injection (detection + exploitation) | sqlmap | `tools/sqlmap.md` |
| XSS detection (advanced) | xsstrike | `tools/xsstrike.md` |
| XSS detection (pipeline-friendly) | dalfox | `tools/dalfox.md` |
| Command injection | commix | `tools/commix.md` |
| Server-Side Template Injection | tplmap | `tools/tplmap.md` |
| JWT analysis and attacks | jwt_tool | `tools/jwt_tool.md` |

### Brute Force & Credentials

| Task | Tool | File |
|------|------|------|
| Online credential brute force | hydra | `tools/hydra.md` |
| Offline hash cracking (GPU) | hashcat | `tools/hashcat.md` |
| Offline hash cracking (CPU + rules) | john | `tools/john.md` |

### SSL/TLS Analysis

| Task | Tool | File |
|------|------|------|
| Comprehensive SSL/TLS testing | testssl | `tools/testssl.md` |
| SSL/TLS scanning (fast, C-based) | sslscan | `tools/sslscan.md` |
| SSL/TLS analysis (Python, CI-friendly) | sslyze | `tools/sslyze.md` |

### Proxy & Interception

| Task | Tool | File |
|------|------|------|
| Proxy (primary, modern) | Caido | `11-caido-proxy.md` |
| Proxy (scriptable, Python) | mitmproxy | `tools/mitmproxy.md` |
| Proxy + scanner (OWASP) | owasp-zap | `tools/owasp-zap.md` |

---

## OWASP Top 10 — 2025 Quick Reference

For full testing vectors and payloads, read `05-analisis-vulnerabilidades.md`.

| # | Category | Key Test Focus |
|---|----------|---------------|
| A01 | Broken Access Control | IDOR, privilege escalation, forced browsing, HTTP method bypass |
| A02 | Security Misconfiguration | Default creds, verbose errors, unnecessary services, directory listing |
| A03 | Supply Chain Failures | Outdated dependencies, known CVEs, compromised libraries |
| A04 | Cryptographic Failures | Weak TLS, cleartext transmission, weak algorithms, exposed secrets |
| A05 | Injection | SQLi, XSS, command injection, LDAP, SSTI, NoSQL injection |
| A06 | Insecure Design | Business logic flaws, missing rate limits, abuse case gaps |
| A07 | Authentication Failures | Weak passwords, missing MFA, session fixation, credential stuffing |
| A08 | Software/Data Integrity | Unsigned updates, deserialization, CI/CD tampering |
| A09 | Logging/Monitoring Failures | Missing audit logs, no alerting, insufficient log detail |
| A10 | Exceptional Conditions | Unhandled errors, stack traces, resource exhaustion |

---

## Common Command Patterns

Quick-reference commands for frequent tasks. For full flags and options, read the specific tool file.

### Subdomain Enumeration Pipeline
```bash
# Passive enumeration → probe → filter alive
subfinder -d target.com -all -o subs.txt
cat subs.txt | httpx -sc -title -tech-detect -o alive.txt
```

### Full Port Scan
```bash
# Fast discovery + detailed scan
masscan -p1-65535 TARGET_IP --rate=1000 -oL ports.txt
nmap -sC -sV -p$(cat ports.txt | awk -F/ '{print $1}' | sort -u | tr '\n' ',') TARGET_IP -oA detailed
```

### Content Discovery
```bash
# Recursive directory brute force
feroxbuster -u https://target.com -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -x php,html,js -t 50
# Parameter fuzzing
ffuf -u "https://target.com/page?FUZZ=test" -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -fs 4242
```

### Vulnerability Scanning
```bash
# Nuclei — broad scan
nuclei -u https://target.com -as -rl 50
# SQLMap — test parameter
sqlmap -u "https://target.com/page?id=1" --batch --risk=2 --level=3
# XSS scan via pipeline
echo "https://target.com/search?q=test" | dalfox pipe --skip-bav
```

### SSL/TLS Quick Audit
```bash
testssl.sh --html target.com:443
```

---

## CVSS 4.0 Quick Scoring

For detailed metrics and examples, read `07-post-explotacion-reporte.md`.

| Metric | Values |
|--------|--------|
| Attack Vector (AV) | N (Network) / A (Adjacent) / L (Local) / P (Physical) |
| Attack Complexity (AC) | L (Low) / H (High) |
| Attack Requirements (AT) | N (None) / P (Present) |
| Privileges Required (PR) | N (None) / L (Low) / H (High) |
| User Interaction (UI) | N (None) / P (Passive) / A (Active) |
| Impact (Vuln System) | VC / VI / VA — H (High) / L (Low) / N (None) |
| Impact (Subseq System) | SC / SI / SA — H (High) / L (Low) / N (None) |

**Severity Ranges:** Critical 9.0-10.0 | High 7.0-8.9 | Medium 4.0-6.9 | Low 0.1-3.9

**Common vectors:**
- Unauthenticated SQLi with data exfil: `CVSS:4.0/AV:N/AC:L/AT:N/PR:N/UI:N/VC:H/VI:H/VA:N/SC:N/SI:N/SA:N` → 9.3 Critical
- Reflected XSS: `CVSS:4.0/AV:N/AC:L/AT:N/PR:N/UI:A/VC:L/VI:L/VA:N/SC:N/SI:N/SA:N` → 5.1 Medium
- IDOR with data access: `CVSS:4.0/AV:N/AC:L/AT:N/PR:L/UI:N/VC:H/VI:N/VA:N/SC:N/SI:N/SA:N` → 6.9 Medium

---

## Report Structure

For full template and examples, read `07-post-explotacion-reporte.md`.

### Executive Report
1. Cover page (client, dates, scope, classification)
2. Executive summary (2 pages max — risk posture, critical stats, key recommendations)
3. Findings summary table (sorted by severity)

### Technical Report
1. Methodology (standards followed, tools used)
2. Detailed findings (each with: title, severity, CVSS, description, evidence, impact, remediation, references)
3. Remediation priority table
4. Appendices (raw output, screenshots)

### Individual Finding Template
```
Title:        [Descriptive name]
Severity:     [Critical/High/Medium/Low/Info]
CVSS 4.0:    [Vector + Score]
Location:     [URL/endpoint]
Description:  [What the vulnerability is]
Evidence:     [Screenshots, request/response, PoC]
Impact:       [Business impact if exploited]
Remediation:  [Specific fix + timeline recommendation]
References:   [CWE, OWASP, external links]
```

---

## Workspace Setup

### tmux Audit Session
For the full tmux configuration and session script, read `12-tmux-workflow.md`.

Quick launch:
```bash
tmux new-session -s audit -n recon
# Split: Ctrl+a | (vertical) or Ctrl+a - (horizontal)
# Navigate: Alt+arrow keys
# Zoom pane: Ctrl+a z
```

### Caido Proxy Setup
For full HTTPQL syntax and workflows, read `11-caido-proxy.md`.

Quick HTTPQL queries:
```
req.host.cont:"target.com" AND resp.code.gte:400
req.method.eq:"POST" AND req.body.cont:"password"
req.path.cont:"/api/" AND resp.code.eq:200
```

---

## Reference Files Index

Read these files for detailed content on each topic. This SKILL.md provides the overview — the numbered files contain the full methodology, payloads, checklists, and configurations.

| File | Content | When to Read |
|------|---------|-------------|
| `01-fundamentos-marco-legal.md` | Legal framework, RoE template, contracts, legislation | Starting a new engagement, need authorization template |
| `02-metodologias-estandares.md` | OWASP WSTG, PTES, OSSTMM, NIST 800-115, comparison | Planning methodology, need standard compliance reference |
| `03-reconocimiento.md` | Passive + active recon, OSINT, subdomain enum, crawling | Executing Phase 1 reconnaissance |
| `04-enumeracion-escaneo.md` | Port scanning, CMS enum, API discovery, vuln scanning | Executing Phase 2 enumeration |
| `05-analisis-vulnerabilidades.md` | OWASP Top 10 2025 testing vectors, payloads, techniques | Testing for specific vulnerabilities |
| `06-explotacion.md` | SQLi, XSS, SSRF, IDOR, JWT, file upload, chaining | Exploiting confirmed vulnerabilities |
| `07-post-explotacion-reporte.md` | CVSS 4.0 scoring, report structure, finding templates | Writing the final report |
| `08-toolkit-herramientas.md` | Tool arsenal by phase, SecLists paths, installation | Setting up tools, finding wordlist paths |
| `09-checklists.md` | 18 checklists (200+ items), per-phase and per-vuln type | Tracking audit progress, ensuring coverage |
| `10-recursos-referencias.md` | Labs, certifications, blogs, CWE mappings | Learning resources, external references |
| `11-caido-proxy.md` | Caido setup, HTTPQL syntax, workflows, plugins | Configuring and using Caido proxy |
| `12-tmux-workflow.md` | tmux session layout, commands, audit patterns | Setting up terminal workspace |

---

## Tool References (`tools/` — 34 files)

Each tool file contains: installation, flags/options in tables, and practical examples.

**By category:**
- **Network Recon:** nmap, masscan
- **Subdomain Enum:** subfinder, amass
- **Web Recon:** httpx, katana, gospider, waybackurls, gau, theharvester, whatweb, wafw00f
- **Content Discovery:** ffuf, feroxbuster, gobuster
- **Vuln Scanning:** nuclei, nikto, wpscan, owasp-zap
- **Injection Exploitation:** sqlmap, xsstrike, dalfox, commix, tplmap
- **Auth/Token Attacks:** jwt_tool, hydra
- **Password Cracking:** hashcat, john, cewl
- **SSL/TLS:** testssl, sslscan, sslyze
- **Proxy:** mitmproxy
- **Parameter Discovery:** arjun

To look up any tool: read `tools/<toolname>.md`

---

## Supported Methodologies

This skill integrates concepts from four major standards. For full comparison, read `02-metodologias-estandares.md`.

- **OWASP WSTG 4.2** — 12 testing categories (INFO through APIT), primary framework
- **PTES** — 7 phases from pre-engagement to reporting
- **OSSTMM** — 5 security channels + RAV metrics
- **NIST SP 800-115** — Technical guide for security testing

---

## Key Wordlist Paths (SecLists)

```
Directories:  /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
Files:        /usr/share/seclists/Discovery/Web-Content/raft-medium-files.txt
APIs:         /usr/share/seclists/Discovery/Web-Content/api/api-endpoints.txt
Subdomains:   /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
Passwords:    /usr/share/seclists/Passwords/Common-Credentials/10-million-password-list-top-10000.txt
Usernames:    /usr/share/seclists/Usernames/top-usernames-shortlist.txt
Fuzzing:      /usr/share/seclists/Fuzzing/special-chars.txt
```
