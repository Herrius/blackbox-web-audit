# 10 - Recursos y Referencias

## Documentacion Oficial

### OWASP
- **OWASP Web Security Testing Guide (WSTG):** https://owasp.org/www-project-web-security-testing-guide/
- **OWASP Top 10 2025:** https://owasp.org/Top10/2025/
- **OWASP Cheat Sheet Series:** https://cheatsheetseries.owasp.org/
- **OWASP Testing Guide v4.2:** https://owasp.org/www-project-web-security-testing-guide/v42/
- **OWASP ASVS (Application Security Verification Standard):** https://owasp.org/www-project-application-security-verification-standard/

### Estandares de Pentesting
- **PTES (Penetration Testing Execution Standard):** http://www.pentest-standard.org/
- **OSSTMM:** https://www.isecom.org/OSSTMM.3.pdf
- **NIST SP 800-115:** https://csrc.nist.gov/publications/detail/sp/800-115/final

### Scoring
- **CVSS 4.0 Specification:** https://www.first.org/cvss/specification-document
- **CVSS 4.0 Calculator:** https://www.first.org/cvss/calculator/4.0
- **CWE (Common Weakness Enumeration):** https://cwe.mitre.org/
- **NVD (National Vulnerability Database):** https://nvd.nist.gov/

---

## Repositorios de Payloads y Cheatsheets

- **PayloadsAllTheThings:** https://github.com/swisskyrepo/PayloadsAllTheThings
  Coleccion masiva de payloads para cada tipo de vulnerabilidad.

- **SecLists:** https://github.com/danielmiessler/SecLists
  Wordlists para fuzzing, passwords, usernames, subdominios, directorios.

- **HackTricks:** https://book.hacktricks.wiki/
  Wiki completa de tecnicas de pentesting web y mas.

- **OWASP Cheat Sheets:** https://cheatsheetseries.owasp.org/
  Guias de prevencion para cada tipo de vulnerabilidad.

- **PortSwigger Web Security Academy - Cheat Sheets:**
  - SQL Injection: https://portswigger.net/web-security/sql-injection/cheat-sheet
  - XSS: https://portswigger.net/web-security/cross-site-scripting/cheat-sheet

---

## Laboratorios de Practica

### Gratuitos

| Plataforma | URL | Descripcion |
|-----------|-----|-------------|
| **PortSwigger Web Security Academy** | https://portswigger.net/web-security | Labs interactivos por tipo de vulnerabilidad. El mejor recurso gratuito |
| **OWASP Juice Shop** | https://owasp.org/www-project-juice-shop/ | Aplicacion vulnerable moderna (Node.js) con challenges |
| **DVWA** | https://github.com/digininja/DVWA | Damn Vulnerable Web Application clasica |
| **HackTheBox** | https://www.hackthebox.com/ | Maquinas y challenges (free tier disponible) |
| **TryHackMe** | https://tryhackme.com/ | Paths guiados para aprender pentesting web |
| **PentesterLab** | https://pentesterlab.com/ | Ejercicios especificos de web vulnerabilities |
| **VulnHub** | https://www.vulnhub.com/ | VMs vulnerables para descargar |
| **bWAPP** | http://www.itsecgames.com/ | Buggy Web Application con 100+ vulnerabilidades |
| **WebGoat** | https://owasp.org/www-project-webgoat/ | Aplicacion de OWASP para aprender seguridad web |
| **Damn Vulnerable GraphQL** | https://github.com/dolevf/Damn-Vulnerable-GraphQL-Application | Practica de ataques GraphQL |

### De Pago

| Plataforma | URL | Descripcion |
|-----------|-----|-------------|
| **HackTheBox Pro Labs** | https://www.hackthebox.com/pro-labs | Entornos empresariales simulados |
| **Offensive Security Labs** | https://www.offsec.com/ | Labs de OSCP, OSWE |
| **PentesterLab Pro** | https://pentesterlab.com/pro | Todos los ejercicios desbloqueados |

---

## Certificaciones Relevantes

### Enfocadas en Web

| Certificacion | Organizacion | Nivel | Enfoque |
|--------------|-------------|-------|---------|
| **OSWE** (Web Expert) | OffSec | Avanzado | Whitebox web, code review, exploit dev |
| **BSCP** (Burp Suite Certified) | PortSwigger | Intermedio | Web testing con Burp Suite |
| **eWPT** (Web Penetration Tester) | INE Security | Intermedio | Web pentesting practico |
| **eWPTX** (Web Penetration Tester eXtreme) | INE Security | Avanzado | Web pentesting avanzado |
| **GWAPT** (Web App Penetration Tester) | SANS/GIAC | Intermedio | Web app testing |

### Pentesting General (incluyen web)

| Certificacion | Organizacion | Nivel |
|--------------|-------------|-------|
| **OSCP** (Penetration Testing) | OffSec | Intermedio |
| **CPTS** (Penetration Testing Specialist) | HackTheBox | Intermedio |
| **eCPPT** (Professional Penetration Tester) | INE Security | Intermedio |
| **PNPT** (Practical Network Penetration Tester) | TCM Security | Entrada |
| **CEH** (Certified Ethical Hacker) | EC-Council | Entrada |
| **CompTIA PenTest+** | CompTIA | Entrada |

---

## Blogs y Recursos de Investigacion

### Blogs de Seguridad Web
- **PortSwigger Research:** https://portswigger.net/research
- **Project Zero (Google):** https://googleprojectzero.blogspot.com/
- **HackerOne Hacktivity:** https://hackerone.com/hacktivity
- **Bugcrowd Blog:** https://www.bugcrowd.com/blog/
- **Assetnote Research:** https://blog.assetnote.io/
- **Intigriti Bug Bytes:** https://blog.intigriti.com/
- **NahamSec:** https://www.youtube.com/@NahamSec

### Writeups y Reports
- **PentestReports.com:** https://pentestreports.com/reports
  Reportes reales de pentesting publicados
- **HackerOne Disclosed Reports:** https://hackerone.com/hacktivity?disclosed=true
  Reportes de bug bounty divulgados
- **Pentester Land Writeups:** https://pentester.land/list-of-bug-bounty-writeups.html

---

## Herramientas AI para Pentesting (2026)

El panorama de herramientas AI para pentesting ha madurado significativamente:

- **Nuclei AI:** Templates generados y sugeridos por AI
- **Burp Suite AI Scanner:** Deteccion mejorada con modelos de lenguaje
- **AI-assisted recon pipelines:** Automatizacion de reconocimiento con LLMs
- **Automated report generation:** Generacion de reportes asistida por AI

**Nota:** Las herramientas AI complementan pero no reemplazan el testing manual. El criterio humano sigue siendo esencial para vulnerabilidades de logica de negocio, chaining complejo y evaluacion de impacto real.

---

## CWEs Mas Relevantes para Web

| CWE | Nombre | OWASP Top 10 |
|-----|--------|-------------|
| CWE-79 | Cross-site Scripting (XSS) | A05 - Injection |
| CWE-89 | SQL Injection | A05 - Injection |
| CWE-22 | Path Traversal | A01 - Broken Access Control |
| CWE-352 | Cross-Site Request Forgery (CSRF) | A01 - Broken Access Control |
| CWE-287 | Improper Authentication | A07 - Authentication Failures |
| CWE-862 | Missing Authorization | A01 - Broken Access Control |
| CWE-918 | Server-Side Request Forgery (SSRF) | A05 - Injection |
| CWE-502 | Deserialization of Untrusted Data | A08 - Integrity Failures |
| CWE-611 | XXE | A05 - Injection |
| CWE-798 | Use of Hard-coded Credentials | A07 - Authentication Failures |
| CWE-306 | Missing Authentication for Critical Function | A07 - Authentication Failures |
| CWE-639 | Authorization Bypass Through User-Controlled Key (IDOR) | A01 - Broken Access Control |
| CWE-94 | Code Injection | A05 - Injection |
| CWE-78 | OS Command Injection | A05 - Injection |
| CWE-434 | Unrestricted File Upload | A05 - Injection |

---

## Fuentes Utilizadas para esta Documentacion

- OWASP Web Security Testing Guide v4.2
- OWASP Top 10:2025
- PTES (Penetration Testing Execution Standard)
- OSSTMM 3
- NIST SP 800-115
- CVSS v4.0 Specification (FIRST)
- PortSwigger Web Security Academy
- HackTricks Wiki
- PayloadsAllTheThings
- SecLists
- Investigacion de herramientas actualizada a marzo 2026
