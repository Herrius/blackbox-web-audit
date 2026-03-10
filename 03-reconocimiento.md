# 03 - Fase 1: Reconocimiento

El reconocimiento es la fase mas critica. La calidad del reconocimiento determina directamente la efectividad de las fases posteriores.

## Reconocimiento Pasivo

Recoleccion de informacion **sin interactuar directamente** con el objetivo.

### 1. OSINT del Dominio

```bash
# Whois - informacion de registro del dominio
whois target.com

# DNS records
dig target.com ANY
dig target.com A
dig target.com MX
dig target.com NS
dig target.com TXT
dig target.com CNAME

# Reverse DNS
dig -x <IP>

# DNS zone transfer (puede funcionar si esta mal configurado)
dig axfr target.com @ns1.target.com
```

### 2. Enumeracion de Subdominios (Pasiva)

```bash
# Subfinder - enumeracion pasiva desde multiples fuentes
subfinder -d target.com -o subdominios.txt

# Amass (modo pasivo)
amass enum -passive -d target.com -o amass-pasivo.txt

# Certificate Transparency Logs
# Via crt.sh
curl -s "https://crt.sh/?q=%25.target.com&output=json" | jq -r '.[].name_value' | sort -u

# theHarvester - emails, subdominios, IPs
theHarvester -d target.com -b all -f harvester-output

# Busqueda en motores
# Google dorks
# site:target.com
# site:target.com filetype:pdf
# site:target.com inurl:admin
# site:target.com intitle:"index of"

# Shodan
shodan search hostname:target.com

# Censys
# Buscar certificados y hosts asociados al dominio
```

### 3. Busqueda de Informacion Expuesta

```bash
# GitHub/GitLab - buscar repos, credenciales expuestas
# Usar GitHub Search: "target.com" password/api_key/secret

# Google Dorks especificos
# site:github.com "target.com"
# site:pastebin.com "target.com"
# site:trello.com "target.com"

# Wayback Machine - versiones anteriores
waybackurls target.com | sort -u > wayback-urls.txt

# Buscar en breaches (Have I Been Pwned API)
# Verificar emails del dominio en breaches conocidos

# Buscar archivos de configuracion expuestos
# robots.txt, sitemap.xml, .env, web.config, .git/
```

### 4. Analisis de Tecnologias (Pasivo)

```bash
# Wappalyzer (extension de navegador)
# Identifica CMS, frameworks, librerias JS, servidor web, CDN

# BuiltWith
# https://builtwith.com/target.com

# Netcraft
# https://toolbar.netcraft.com/site_report?url=target.com

# HTTP headers (sin escaneo agresivo)
curl -sI https://target.com | grep -iE "server|x-powered|x-aspnet|x-generator"
```

---

## Reconocimiento Activo

Interaccion directa con el objetivo. **Requiere autorizacion.**

### 1. Enumeracion de Subdominios (Activa)

```bash
# Amass (modo activo - incluye brute force DNS)
amass enum -active -d target.com -o amass-activo.txt

# Gobuster DNS
gobuster dns -d target.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -o gobuster-dns.txt

# Puredns - resolucion masiva y brute force
puredns bruteforce /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt target.com --resolvers resolvers.txt -w puredns-results.txt

# Verificar subdominios activos
httpx -l subdominios.txt -o subdominios-activos.txt -status-code -title -tech-detect
```

### 2. Fingerprinting de Tecnologias (Activo)

```bash
# whatweb - identificacion de tecnologias web
whatweb https://target.com -v

# Wapiti - fingerprinting + vuln scan ligero
wapiti -u https://target.com --scope url

# WhatWeb agresivo
whatweb -a 3 https://target.com

# Identificacion de WAF
wafw00f https://target.com

# SSL/TLS analysis
testssl.sh https://target.com
sslyze target.com
```

### 3. Mapeo de la Aplicacion

```bash
# Spidering con Caido
# 1. Configurar proxy en navegador (127.0.0.1:8080)
# 2. Navegar manualmente por toda la aplicacion
# 3. Revisar Sitemap generado automaticamente
# 4. Usar HTTPQL para filtrar trafico relevante

# Crawling con gospider
gospider -s https://target.com -d 3 -o gospider-output

# Hakrawler
echo "https://target.com" | hakrawler -d 3

# Katana (ProjectDiscovery)
katana -u https://target.com -d 5 -o katana-output.txt

# Extraer endpoints de archivos JS
# Usando LinkFinder
python3 linkfinder.py -i https://target.com -o cli

# GAP (GetAllParams)
# Extraer parametros de URLs recolectadas
```

### 4. Enumeracion de Directorios y Archivos

```bash
# Feroxbuster (recomendado - rapido y recursivo)
feroxbuster -u https://target.com -w /usr/share/seclists/Discovery/Web-Content/raft-large-directories.txt -o ferox-output.txt

# Gobuster
gobuster dir -u https://target.com -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,asp,aspx,jsp,html,js,txt,bak -o gobuster-dirs.txt

# ffuf (fuzzing)
ffuf -u https://target.com/FUZZ -w /usr/share/seclists/Discovery/Web-Content/common.txt -mc 200,301,302,403 -o ffuf-output.json

# Archivos especificos a buscar
# /.git/HEAD
# /.env
# /robots.txt
# /sitemap.xml
# /crossdomain.xml
# /.well-known/security.txt
# /wp-config.php.bak
# /web.config
# /server-info
# /server-status
# /.svn/entries
# /.DS_Store
# /backup.zip, /backup.tar.gz
```

---

## Consolidacion del Reconocimiento

### Organizar Hallazgos

```
target.com/
├── recon/
│   ├── subdominios/
│   │   ├── pasivos.txt
│   │   ├── activos.txt
│   │   └── consolidado.txt
│   ├── tecnologias/
│   │   ├── whatweb.txt
│   │   ├── waf-detection.txt
│   │   └── ssl-analysis.txt
│   ├── directorios/
│   │   ├── feroxbuster.txt
│   │   └── archivos-interesantes.txt
│   ├── endpoints/
│   │   ├── js-endpoints.txt
│   │   ├── api-endpoints.txt
│   │   └── parametros.txt
│   └── osint/
│       ├── whois.txt
│       ├── dns-records.txt
│       ├── emails.txt
│       └── google-dorks.txt
└── notas-recon.md
```

### Preguntas Clave a Responder

Tras el reconocimiento, debes poder responder:

1. Que tecnologias usa? (servidor, lenguaje, framework, CMS, DB)
2. Cuantos subdominios/hosts tiene la superficie de ataque?
3. Hay WAF? Cual?
4. Que endpoints y APIs estan expuestos?
5. Hay archivos o directorios sensibles accesibles?
6. Que informacion publica existe sobre la organizacion?
7. Hay credenciales o datos filtrados?
8. Cual es la version de las tecnologias detectadas? (para buscar CVEs)
