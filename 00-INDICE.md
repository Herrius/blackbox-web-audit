# Auditoria de Ciberseguridad Ofensiva - Caja Negra - Aplicaciones Web

## Documentacion Completa

Guia integral para la ejecucion de auditorias de seguridad ofensiva en modalidad caja negra sobre aplicaciones web. Basada en OWASP WSTG, PTES, OSSTMM y practicas actualizadas a 2026.

---

### Contenido

| # | Archivo | Descripcion |
|---|---------|-------------|
| 01 | [Fundamentos y Marco Legal](01-fundamentos-marco-legal.md) | Conceptos base, tipos de auditoria, marco legal, autorizacion y reglas de engagement |
| 02 | [Metodologias y Estandares](02-metodologias-estandares.md) | OWASP WSTG, PTES, OSSTMM, flujo de trabajo completo |
| 03 | [Fase 1 - Reconocimiento](03-reconocimiento.md) | OSINT, enumeracion de subdominios, fingerprinting, recoleccion pasiva y activa |
| 04 | [Fase 2 - Enumeracion y Escaneo](04-enumeracion-escaneo.md) | Escaneo de puertos, directorios, tecnologias, mapeo de superficie de ataque |
| 05 | [Fase 3 - Analisis de Vulnerabilidades](05-analisis-vulnerabilidades.md) | OWASP Top 10 2025, clasificacion, deteccion manual y automatizada |
| 06 | [Fase 4 - Explotacion](06-explotacion.md) | Tecnicas de explotacion por tipo de vulnerabilidad, PoC, chaining |
| 07 | [Fase 5 - Post-Explotacion y Reporte](07-post-explotacion-reporte.md) | Documentacion de hallazgos, CVSS 4.0, estructura del reporte, remediaciones |
| 08 | [Toolkit - Herramientas](08-toolkit-herramientas.md) | Arsenal completo de herramientas por fase, configuracion y uso |
| 09 | [Checklists Operativos](09-checklists.md) | Listas de verificacion por fase y por tipo de vulnerabilidad |
| 10 | [Recursos y Referencias](10-recursos-referencias.md) | Links, laboratorios de practica, certificaciones, comunidad |
| 11 | [Caido - Proxy Principal](11-caido-proxy.md) | Setup, HTTPQL, Workflows, Automate, plugins, equivalencia con Burp |
| 12 | [Workflow con tmux](12-tmux-workflow.md) | Sesiones de auditoria, layouts, comandos, integracion con Claude Code |

---

### Referencias de Herramientas (`tools/`)

Archivos individuales con flags, opciones y ejemplos practicos de cada herramienta:

| Herramienta | Descripcion |
|-------------|-------------|
| [nmap](tools/nmap.md) | Scanner de red y puertos, NSE scripts |
| [masscan](tools/masscan.md) | Scanner de puertos masivo |
| [subfinder](tools/subfinder.md) | Descubrimiento pasivo de subdominios |
| [amass](tools/amass.md) | Enumeracion de subdominios (pasivo/activo) |
| [httpx](tools/httpx.md) | Probing HTTP y fingerprinting |
| [katana](tools/katana.md) | Crawler web rapido |
| [gospider](tools/gospider.md) | Web spider con Wayback/sitemap |
| [waybackurls](tools/waybackurls.md) | URLs desde Wayback Machine |
| [gau](tools/gau.md) | URLs desde multiples fuentes |
| [theharvester](tools/theharvester.md) | OSINT de emails, subdominios, IPs |
| [whatweb](tools/whatweb.md) | Fingerprinting web |
| [wafw00f](tools/wafw00f.md) | Deteccion de WAF |
| [ffuf](tools/ffuf.md) | Fuzzer web rapido |
| [feroxbuster](tools/feroxbuster.md) | Content discovery recursivo |
| [gobuster](tools/gobuster.md) | Brute force dirs/DNS/vhosts |
| [nikto](tools/nikto.md) | Scanner de vulnerabilidades web |
| [wpscan](tools/wpscan.md) | Scanner de WordPress |
| [nuclei](tools/nuclei.md) | Scanner de vulnerabilidades basado en templates |
| [owasp-zap](tools/owasp-zap.md) | Scanner web (CLI, API, Docker, Automation Framework) |
| [sqlmap](tools/sqlmap.md) | Explotacion de SQL Injection |
| [xsstrike](tools/xsstrike.md) | Scanner de XSS |
| [dalfox](tools/dalfox.md) | Scanner de XSS con pipeline |
| [commix](tools/commix.md) | Explotacion de Command Injection |
| [tplmap](tools/tplmap.md) | Explotacion de SSTI |
| [arjun](tools/arjun.md) | Descubrimiento de parametros HTTP |
| [jwt_tool](tools/jwt_tool.md) | Analisis y ataque de JWT |
| [hydra](tools/hydra.md) | Brute force online de credenciales |
| [hashcat](tools/hashcat.md) | Cracking de passwords via GPU |
| [john](tools/john.md) | Cracking de passwords via CPU |
| [cewl](tools/cewl.md) | Generador de wordlists custom |
| [sslyze](tools/sslyze.md) | Analizador SSL/TLS en Python |
| [sslscan](tools/sslscan.md) | Scanner SSL/TLS rapido en C |
| [testssl](tools/testssl.md) | Testing completo SSL/TLS en bash |
| [mitmproxy](tools/mitmproxy.md) | Proxy de intercepcion scriptable |

---

### Uso

Esta documentacion esta disenada para ser utilizada como referencia durante auditorias reales autorizadas. Cada archivo puede consultarse de forma independiente o seguirse en orden secuencial como guia de ejecucion.

**Disclaimer:** Todo el contenido es exclusivamente para uso en contextos autorizados: pentesting profesional, CTFs, laboratorios de seguridad e investigacion.
