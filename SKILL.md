---
name: blackbox-web-audit
description: Complete black-box web application security audit methodology with 34 tool references, OWASP Top 10 2025 testing vectors, CVSS 4.0 scoring, and pentest reporting templates. Use this skill whenever the user needs to plan or execute a web application pentest, choose the right security tool for a task, look up tool flags or command syntax, test for specific vulnerabilities (SQLi, XSS, SSRF, SSTI, IDOR, command injection, JWT attacks, authentication bypass), write a pentest report, score findings with CVSS, configure a proxy (Caido, ZAP, mitmproxy), or set up a pentesting workspace. Also use it when the user asks things like "how do I test for X", "what tool should I use for Y", "run a scan against Z", or needs help with any phase of a black-box web security assessment — from reconnaissance through exploitation to final deliverables.
---

# Black-Box Web Application Audit

Complete methodology for authorized black-box web application security audits. Covers the full lifecycle from legal framework through exploitation to final reporting, with 34 tool references and actionable checklists.

All content assumes **authorized testing context** (pentesting engagements, CTFs, security labs, research).

---

## How This Skill Works

This file is the entry point. It gives you methodology overview, tool selection logic, and quick references for the most common needs. For deeper detail, follow the pointers to reference files (`01-` through `12-`) and individual tool docs (`tools/*.md`).

Think of it as three layers:
1. **This file** — decision-making, "which tool and why", workflow guidance
2. **Reference docs** (01-12) — full methodology, payloads, checklists, configurations
3. **Tool files** (tools/) — flags, options, and practical examples for each tool

---

## Methodology — Why This Order Matters

A black-box audit follows a specific sequence because each phase feeds the next. Skipping or reordering phases means working with incomplete information.

```
Pre-Engagement → Recon → Enumeration → Vuln Analysis → Exploitation → Reporting
```

| Phase | Goal | Why This Phase First | Reference |
|-------|------|---------------------|-----------|
| 0. Pre-Engagement | Legal framework, RoE, scope | Without written authorization, everything else is illegal. Define what you can and cannot touch before any packet hits the wire. | `01-fundamentos-marco-legal.md` |
| 1. Reconnaissance | Map the attack surface | You need to know what exists (subdomains, IPs, tech stack) before you can enumerate or test it. Passive recon first to avoid detection; active recon to fill gaps. | `03-reconocimiento.md` |
| 2. Enumeration | Discover endpoints, parameters, tech details | Now that you know what's there, dig deeper: hidden directories, API endpoints, parameters, CMS versions. This is where you find the targets for phase 3. | `04-enumeracion-escaneo.md` |
| 3. Vuln Analysis | Identify vulnerabilities (OWASP Top 10) | With a full map of endpoints and parameters, systematically test each one against known vulnerability classes. | `05-analisis-vulnerabilidades.md` |
| 4. Exploitation | Prove impact with PoC | A vulnerability without proof of impact is just a theory. Exploit to demonstrate real business risk — but stop at proof, don't cause damage. | `06-explotacion.md` |
| 5. Reporting | CVSS scoring, deliverables | The report is the actual product. A well-written report with clear remediation is worth more than the testing itself. | `07-post-explotacion-reporte.md` |

---

## Tool Selection — How to Choose

The tables below map tasks to tools. When multiple tools do similar things, the **"When to pick"** notes explain the difference. Read `tools/<toolname>.md` for full flags and examples.

### Reconnaissance

| Task | Tool | When to pick this one |
|------|------|-----------------------|
| Port scanning | **nmap** | Default choice. Precise, scriptable (NSE), service/version detection. Use for targeted scans. |
| Port scanning | **masscan** | When you need speed over precision — scanning large IP ranges or all 65k ports fast. Follow up with nmap on discovered ports. |
| Subdomain enum | **subfinder** | Fast, passive-only, API-driven. Best for quick initial enum. Needs API keys for full power. |
| Subdomain enum | **amass** | When you need thoroughness over speed. Active + passive modes, DNS brute force, graph analysis. Slower but more complete. |
| HTTP probing | **httpx** | After subdomain enum, to filter alive hosts and grab titles/tech/status codes in one pass. |
| Web crawling | **katana** | Fast, headless-capable, good for JS-heavy sites. Use when you need to discover endpoints by crawling. |
| Web spidering | **gospider** | When you also want sitemap parsing, robots.txt, and Wayback integration alongside crawling. |
| Historical URLs | **waybackurls** | Simple, single-source (Wayback Machine only). Quick and focused. |
| Historical URLs | **gau** | When you want URLs from multiple sources (Wayback + Common Crawl + OTX + URLScan). More complete but slower. |
| OSINT | **theharvester** | Emails, subdomains, IPs from search engines and public sources. Good for initial passive OSINT. |
| Tech fingerprinting | **whatweb** | Identifies CMS, frameworks, server software, versions. Run early to understand the stack. |
| WAF detection | **wafw00f** | Run before scanning/fuzzing. If a WAF is present, you'll need to adapt techniques (rate limiting, encoding, etc.). |

### Content Discovery & Enumeration

| Task | Tool | When to pick this one |
|------|------|-----------------------|
| Dir/file fuzzing | **ffuf** | Fastest fuzzer. Best for parameter fuzzing, header fuzzing, and any FUZZ-keyword based task. Most flexible. |
| Dir/file fuzzing | **feroxbuster** | When you want automatic recursion into discovered directories. "Set and forget" content discovery. |
| Dir/file fuzzing | **gobuster** | Simpler than ffuf, also does DNS and vhost brute force. Good all-rounder when you don't need ffuf's flexibility. |
| Vuln scanning (web) | **nikto** | Quick legacy scanner. Finds misconfigs, default files, outdated software. Run it first for low-hanging fruit. |
| CMS scanning | **wpscan** | WordPress only. Users, plugins, themes, known CVEs. If the target runs WordPress, always run this. |
| Template vuln scanning | **nuclei** | Broad vulnerability scanning with community templates. Run after enumeration to check for known CVEs, misconfigs, exposed panels. |
| Parameter discovery | **arjun** | Finds hidden GET/POST parameters that aren't visible in the UI. Use when you suspect hidden parameters on endpoints. |
| Custom wordlists | **cewl** | Generates wordlists from the target's own content. Use for brute force with site-specific terms (company names, products, jargon). |

### Vulnerability Scanning & Exploitation

| Task | Tool | When to pick this one |
|------|------|-----------------------|
| Full web app scan | **owasp-zap** | Comprehensive automated scanning (spider + active scan + passive analysis). Best for CLI/Docker/CI automation. Large reference: see `tools/owasp-zap.md`. |
| SQL injection | **sqlmap** | The standard. Detects and exploits SQLi automatically. Use `--batch --risk=2 --level=3` for thorough testing. |
| XSS detection | **dalfox** | Pipeline-friendly, works with stdin. Best when chaining with other tools (gau → dalfox). |
| XSS detection | **xsstrike** | More thorough analysis with fuzzing engine and context analysis. Use for manual, focused XSS testing on specific endpoints. |
| Command injection | **commix** | Automated command injection detection and exploitation. Multiple techniques (classic, time-based, file-based). |
| SSTI | **tplmap** | Template injection detection and exploitation. Supports 15+ template engines. |
| JWT attacks | **jwt_tool** | JWT analysis, forgery, and attack automation (none algorithm, key confusion, brute force). |

### Brute Force & Credentials

| Task | Tool | When to pick this one |
|------|------|-----------------------|
| Online brute force | **hydra** | Multi-protocol (50+). Use for login forms, SSH, FTP, etc. Set reasonable thread counts to avoid lockouts. |
| Offline cracking (GPU) | **hashcat** | When you have hashes and a GPU. Fastest cracker. Use rules and masks for efficiency. |
| Offline cracking (CPU) | **john** | When GPU isn't available, or you need `*2john` utilities to extract hashes from files (zip, pdf, ssh keys, etc.). Great rules engine. |

### SSL/TLS Analysis

| Task | Tool | When to pick this one |
|------|------|-----------------------|
| SSL/TLS testing | **testssl** | Most comprehensive. No dependencies (bundled OpenSSL). Default choice for SSL auditing. |
| SSL/TLS testing | **sslscan** | Fast scan in C. Use when you need quick results or colorized terminal output. |
| SSL/TLS testing | **sslyze** | Python-based, JSON output, CI-friendly. Best for automation pipelines. |

### Proxy & Interception

| Task | Tool | When to pick this one |
|------|------|-----------------------|
| Proxy (interactive) | **Caido** | Primary proxy. Modern UI, HTTPQL query language, fast. Full guide: `11-caido-proxy.md`. |
| Proxy (scriptable) | **mitmproxy** | When you need Python scripting for request/response modification. Best for automation. |
| Proxy + scanner | **owasp-zap** | When you need both proxying and scanning in one tool. |

---

## End-to-End Workflow Example

This is a typical flow when you receive a target domain. Adapt based on scope and RoE.

```bash
# === PHASE 1: RECON (passive first, then active) ===

# Passive subdomain enum
subfinder -d target.com -all -o subs.txt

# Enrich with historical data
echo target.com | gau --subs | sort -u > urls_historicas.txt

# Probe alive hosts + tech fingerprint
cat subs.txt | httpx -sc -title -tech-detect -o alive.txt

# WAF check (adapt strategy if detected)
wafw00f https://target.com

# Technology identification
whatweb https://target.com

# === PHASE 2: ENUMERATION ===

# Fast port scan → detailed follow-up
masscan -p1-65535 TARGET_IP --rate=1000 -oL ports.txt
nmap -sC -sV -p DISCOVERED_PORTS TARGET_IP -oA nmap_detailed

# Content discovery (recursive)
feroxbuster -u https://target.com -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -x php,html,js -t 50

# Hidden parameter discovery on interesting endpoints
arjun -u https://target.com/api/endpoint

# CMS-specific (if WordPress detected)
wpscan --url https://target.com -e ap,at,u --api-token YOUR_TOKEN

# === PHASE 3: VULN ANALYSIS ===

# Broad vulnerability scan
nuclei -u https://target.com -as -rl 50

# SSL/TLS audit
testssl.sh --html target.com:443

# === PHASE 4: TARGETED EXPLOITATION (based on findings) ===

# SQLi on discovered parameter
sqlmap -u "https://target.com/page?id=1" --batch --risk=2 --level=3

# XSS via pipeline
cat urls_historicas.txt | grep "=" | dalfox pipe --skip-bav

# === PHASE 5: REPORTING ===
# Score each finding with CVSS 4.0 (see below)
# Write report using the template (see Report Structure section)
```

---

## OWASP Top 10 — 2025 Quick Reference

Each category lists what to look for in black-box testing. For full payloads and techniques, read `05-analisis-vulnerabilidades.md`.

| # | Category | What to Test | Why It Matters |
|---|----------|-------------|----------------|
| A01 | Broken Access Control | IDOR, privilege escalation, forced browsing, HTTP method bypass | Most common web vuln. Direct path to unauthorized data access. |
| A02 | Security Misconfiguration | Default creds, verbose errors, directory listing, unnecessary services | Easy to find, easy to fix — but frequently missed. |
| A03 | Supply Chain Failures | Outdated dependencies, known CVEs in libraries | A single outdated library can compromise the entire app. |
| A04 | Cryptographic Failures | Weak TLS, cleartext transmission, exposed secrets | Data exposure without needing to "hack" anything. |
| A05 | Injection | SQLi, XSS, command injection, SSTI, NoSQL | Classic attack vectors. SQLi and XSS alone cover most web exploits. |
| A06 | Insecure Design | Business logic flaws, missing rate limits | Hardest to find with tools — requires understanding the application's purpose. |
| A07 | Authentication Failures | Weak passwords, missing MFA, session fixation | Front door to the application. |
| A08 | Software/Data Integrity | Deserialization, unsigned updates, CI/CD tampering | Can lead to RCE without traditional exploitation. |
| A09 | Logging/Monitoring Failures | Missing audit logs, no alerting | Won't find this in black-box directly, but note if errors reveal no logging. |
| A10 | Exceptional Conditions | Unhandled errors, stack traces, resource exhaustion | Stack traces leak internal paths, frameworks, and versions. |

---

## CVSS 4.0 Quick Scoring

For detailed examples, read `07-post-explotacion-reporte.md`.

| Metric | Values | Think about it as... |
|--------|--------|---------------------|
| Attack Vector (AV) | N / A / L / P | How close does the attacker need to be? |
| Attack Complexity (AC) | L / H | Does it work every time, or need special conditions? |
| Attack Requirements (AT) | N / P | Does the target need a specific config? |
| Privileges Required (PR) | N / L / H | Does the attacker need an account? |
| User Interaction (UI) | N / P / A | Does a victim need to click something? |
| Impact (VC/VI/VA) | H / L / N | Confidentiality, Integrity, Availability on the vulnerable system |
| Impact (SC/SI/SA) | H / L / N | Same, but on downstream systems |

**Severity:** Critical 9.0-10.0 | High 7.0-8.9 | Medium 4.0-6.9 | Low 0.1-3.9

**Common vectors:**
- Unauthenticated SQLi + data exfil: `AV:N/AC:L/AT:N/PR:N/UI:N/VC:H/VI:H/VA:N/SC:N/SI:N/SA:N` → **9.3 Critical**
- Reflected XSS (victim must click): `AV:N/AC:L/AT:N/PR:N/UI:A/VC:L/VI:L/VA:N/SC:N/SI:N/SA:N` → **5.1 Medium**
- IDOR (needs low-priv account): `AV:N/AC:L/AT:N/PR:L/UI:N/VC:H/VI:N/VA:N/SC:N/SI:N/SA:N` → **6.9 Medium**

---

## Report Structure

The report is the deliverable the client pays for. A clear, actionable report matters more than finding the vulnerability. Full templates in `07-post-explotacion-reporte.md`.

### Executive Report (for stakeholders)
1. Cover page (client, dates, scope, classification)
2. Executive summary (2 pages max — risk posture, critical stats, top recommendations)
3. Findings summary table (sorted by severity)

### Technical Report (for dev/security teams)
1. Methodology (standards followed, tools used)
2. Detailed findings (title, severity, CVSS, description, evidence, impact, remediation, references)
3. Remediation priority table
4. Appendices (raw output, screenshots)

### Individual Finding Template
```
Title:        [Descriptive name — e.g., "SQL Injection in /api/users endpoint"]
Severity:     [Critical/High/Medium/Low/Info]
CVSS 4.0:    [Vector + Score]
Location:     [URL/endpoint + parameter]
Description:  [What the vulnerability is and why it exists]
Evidence:     [Screenshots, request/response pairs, PoC steps]
Impact:       [What an attacker could achieve — in business terms]
Remediation:  [Specific fix with code examples where possible]
References:   [CWE-XXX, OWASP category, external links]
```

---

## Workspace Setup

### tmux — Why and How
Pentesting involves running multiple tools simultaneously and switching between contexts. tmux keeps everything organized in one terminal. Full config and session script in `12-tmux-workflow.md`.

```bash
tmux new-session -s audit -n recon
# Ctrl+a |  → vertical split     Ctrl+a -  → horizontal split
# Alt+arrows → navigate panes    Ctrl+a z  → zoom/unzoom pane
```

### Caido Proxy
Modern proxy with HTTPQL query language for filtering traffic. Full guide in `11-caido-proxy.md`.

```
# Useful HTTPQL filters
req.host.cont:"target.com" AND resp.code.gte:400        # errors only
req.method.eq:"POST" AND req.body.cont:"password"        # login attempts
req.path.cont:"/api/" AND resp.code.eq:200               # successful API calls
```

---

## Reference Files

| File | Content | Read when you need... |
|------|---------|----------------------|
| `01-fundamentos-marco-legal.md` | Legal framework, RoE template, contracts | Authorization template for a new engagement |
| `02-metodologias-estandares.md` | OWASP WSTG, PTES, OSSTMM, NIST comparison | Compliance reference or methodology planning |
| `03-reconocimiento.md` | Passive/active recon, OSINT, subdomain enum | Full recon command reference and techniques |
| `04-enumeracion-escaneo.md` | Port scanning, CMS enum, API discovery | Enumeration commands and scanning patterns |
| `05-analisis-vulnerabilidades.md` | OWASP Top 10 testing vectors and payloads | Specific vulnerability testing techniques |
| `06-explotacion.md` | SQLi, XSS, SSRF, IDOR, JWT, chaining | Exploitation commands and bypass techniques |
| `07-post-explotacion-reporte.md` | CVSS 4.0, report structure, finding templates | Writing findings and scoring vulnerabilities |
| `08-toolkit-herramientas.md` | Tool arsenal by phase, SecLists paths, install | Setting up tools or finding wordlist paths |
| `09-checklists.md` | 18 checklists, 200+ items, per-phase/per-vuln | Tracking progress and ensuring full coverage |
| `10-recursos-referencias.md` | Labs, certs, blogs, CWE mappings | Learning resources or external references |
| `11-caido-proxy.md` | Caido setup, HTTPQL, workflows, plugins | Configuring or using Caido proxy |
| `12-tmux-workflow.md` | tmux layout, commands, audit patterns | Terminal workspace setup |

## Tool References (`tools/` — 34 files)

Each file has: installation, flags in tables, practical examples. Read `tools/<toolname>.md`.

**By category:** Network (nmap, masscan) · Subdomains (subfinder, amass) · Web Recon (httpx, katana, gospider, waybackurls, gau, theharvester, whatweb, wafw00f) · Content Discovery (ffuf, feroxbuster, gobuster) · Vuln Scanning (nuclei, nikto, wpscan, owasp-zap) · Injection (sqlmap, xsstrike, dalfox, commix, tplmap) · Auth (jwt_tool, hydra) · Cracking (hashcat, john, cewl) · SSL/TLS (testssl, sslscan, sslyze) · Proxy (mitmproxy) · Params (arjun)

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
