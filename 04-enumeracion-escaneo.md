# 04 - Fase 2: Enumeracion y Escaneo

## Escaneo de Puertos y Servicios

```bash
# Nmap - escaneo completo
# Top 1000 puertos
nmap -sC -sV -oN nmap-default.txt target.com

# Todos los puertos TCP
nmap -p- --min-rate 5000 -oN nmap-allports.txt target.com

# Escaneo detallado de puertos abiertos
nmap -p <puertos> -sC -sV -A -oN nmap-detallado.txt target.com

# UDP (top 100)
nmap -sU --top-ports 100 -oN nmap-udp.txt target.com

# Scripts especificos para web
nmap -p 80,443,8080,8443 --script http-enum,http-headers,http-methods,http-title target.com

# Masscan (escaneo rapido de puertos)
masscan -p1-65535 --rate=1000 -oL masscan-output.txt <IP>
```

### Puertos Web Comunes a Verificar

| Puerto | Servicio | Notas |
|--------|----------|-------|
| 80 | HTTP | Web estandar |
| 443 | HTTPS | Web cifrado |
| 8080 | HTTP alternativo | Proxies, Tomcat, dev servers |
| 8443 | HTTPS alternativo | Admin panels |
| 3000 | Node.js/Grafana | Aplicaciones dev |
| 5000 | Flask/dev | Frameworks Python |
| 8000 | Django/dev | Frameworks Python |
| 8888 | Alternativo | Jupyter, admin panels |
| 9090 | Cockpit/admin | Paneles de gestion |
| 9200 | Elasticsearch | API REST |
| 4443 | HTTPS alt | VPN panels |

---

## Enumeracion de Tecnologias Web

### Identificacion del Stack

```bash
# Headers HTTP revelan informacion
curl -sI https://target.com

# Headers clave a buscar:
# Server: Apache/2.4.51, nginx/1.21.0, IIS/10.0
# X-Powered-By: PHP/8.1, Express, ASP.NET
# X-Generator: WordPress 6.x, Drupal
# X-AspNet-Version: 4.0.x
# Set-Cookie: PHPSESSID (PHP), JSESSIONID (Java), ASP.NET_SessionId (.NET)

# Identificacion de CMS
# WordPress
curl -s https://target.com/wp-login.php
curl -s https://target.com/wp-json/wp/v2/users

# Joomla
curl -s https://target.com/administrator/
curl -s https://target.com/language/en-GB/en-GB.xml

# Drupal
curl -s https://target.com/CHANGELOG.txt
curl -s https://target.com/core/CHANGELOG.txt

# CMSmap (multi-CMS scanner)
cmsmap https://target.com
```

### Enumeracion de CMS Especifica

```bash
# WordPress - WPScan
wpscan --url https://target.com --enumerate vp,vt,u,dbe --api-token <TOKEN>
# vp = vulnerable plugins
# vt = vulnerable themes
# u = usuarios
# dbe = database exports

# Joomla - JoomScan
joomscan -u https://target.com

# Drupal - Droopescan
droopescan scan drupal -u https://target.com
```

---

## Enumeracion de Endpoints y APIs

### Descubrimiento de APIs

```bash
# Buscar documentacion de API expuesta
# /api/
# /api/v1/
# /api/v2/
# /swagger.json
# /swagger-ui.html
# /api-docs
# /openapi.json
# /graphql
# /graphiql
# /.well-known/openapi.json

# ffuf para descubrir API endpoints
ffuf -u https://target.com/api/FUZZ -w /usr/share/seclists/Discovery/Web-Content/api/api-endpoints.txt -mc 200,201,401,403,405

# Kiterunner - descubrimiento de APIs
kr scan https://target.com -w /path/to/routes-large.kite

# GraphQL introspection
curl -s -X POST https://target.com/graphql \
  -H "Content-Type: application/json" \
  -d '{"query":"{__schema{types{name,fields{name}}}}"}'
```

### Mapeo de Parametros

```bash
# Arjun - descubrimiento de parametros HTTP
arjun -u https://target.com/page -m GET POST

# ParamSpider
paramspider -d target.com

# x8 - descubrimiento de parametros ocultos
x8 -u https://target.com -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt
```

---

## Escaneo de Vulnerabilidades Automatizado

### Scanners de Proposito General

```bash
# Nikto - scanner de servidor web
nikto -h https://target.com -o nikto-output.txt

# Nuclei - scanner basado en templates
nuclei -u https://target.com -o nuclei-output.txt
nuclei -u https://target.com -tags cve -o nuclei-cve.txt
nuclei -u https://target.com -severity critical,high -o nuclei-criticos.txt

# Wapiti
wapiti -u https://target.com --scope domain -o wapiti-report

# OWASP ZAP (linea de comandos)
zap-cli quick-scan https://target.com
zap-cli active-scan https://target.com
```

### Scanners Especificos

```bash
# SQLMap - deteccion de SQL injection
sqlmap -u "https://target.com/page?id=1" --batch --level 3 --risk 2

# XSStrike - deteccion de XSS
xsstrike -u "https://target.com/search?q=test"

# Commix - command injection
commix -u "https://target.com/page?cmd=test"

# SSRFmap - SSRF
python3 ssrfmap.py -r request.txt -p url -m readfiles

# tplmap - Server-Side Template Injection
tplmap -u "https://target.com/page?name=test"
```

---

## Analisis de Configuracion

### SSL/TLS

```bash
# testssl.sh (analisis completo)
testssl.sh https://target.com

# Verificar:
# - Versiones de protocolo (TLS 1.0/1.1 deben estar deshabilitadas)
# - Cipher suites debiles
# - Certificado valido y no expirado
# - HSTS habilitado
# - Certificate Transparency
```

### Security Headers

```bash
# Verificar headers de seguridad
curl -sI https://target.com | grep -iE "strict-transport|content-security|x-frame|x-content-type|x-xss|referrer-policy|permissions-policy"

# Headers esperados:
# Strict-Transport-Security (HSTS)
# Content-Security-Policy (CSP)
# X-Frame-Options
# X-Content-Type-Options: nosniff
# Referrer-Policy
# Permissions-Policy

# securityheaders.com (analisis online)
# https://securityheaders.com/?q=target.com
```

### CORS (Cross-Origin Resource Sharing)

```bash
# Verificar configuracion CORS
curl -sI -H "Origin: https://evil.com" https://target.com/api/endpoint

# Buscar:
# Access-Control-Allow-Origin: * (peligroso)
# Access-Control-Allow-Origin: https://evil.com (refleja origen - peligroso)
# Access-Control-Allow-Credentials: true (con * es critico)
```

---

## Consolidacion de Enumeracion

### Mapa de Superficie de Ataque

Al finalizar esta fase, construir un mapa que incluya:

```
SUPERFICIE DE ATAQUE - target.com

HOSTS ACTIVOS:
  - target.com (443) - nginx/1.x - WordPress 6.x
  - api.target.com (443) - nginx - REST API
  - admin.target.com (443) - Apache - Panel admin
  - dev.target.com (8080) - Node.js - Entorno dev

TECNOLOGIAS:
  - Frontend: React 18.x
  - Backend: PHP 8.1 / WordPress 6.x
  - API: Node.js / Express
  - DB: MySQL (inferido de WordPress)
  - WAF: Cloudflare
  - CDN: Cloudflare

ENDPOINTS CRITICOS:
  - /wp-admin/ (login WordPress)
  - /api/v1/ (API REST)
  - /graphql (GraphQL endpoint)
  - /uploads/ (directory listing?)

VECTORES POTENCIALES:
  - WordPress plugins desactualizados
  - API sin rate limiting
  - GraphQL introspection habilitada
  - Dev server expuesto sin autenticacion
```
