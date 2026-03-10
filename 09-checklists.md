# 09 - Checklists Operativos

## Checklist General - Auditoria Caja Negra Web

### Pre-Engagement
```
[ ] Contrato firmado con autorizacion explicita
[ ] Alcance definido (URLs, IPs, dominios)
[ ] Reglas de engagement documentadas
[ ] Contactos de emergencia establecidos
[ ] Ventana temporal acordada
[ ] Entregables definidos
[ ] VPN/IP de origen comunicada al cliente
[ ] Entorno de trabajo preparado (VM, herramientas, notas)
```

### Fase 1: Reconocimiento
```
[ ] WHOIS y DNS records
[ ] Enumeracion pasiva de subdominios (subfinder, amass, crt.sh)
[ ] Enumeracion activa de subdominios (bruteforce DNS)
[ ] Verificacion de subdominios activos (httpx)
[ ] Google dorks ejecutados
[ ] theHarvester (emails, hosts)
[ ] Shodan/Censys consultados
[ ] Wayback Machine URLs recolectadas
[ ] Busqueda de codigo fuente expuesto (GitHub, GitLab)
[ ] Busqueda de credenciales en breaches
[ ] Fingerprinting de tecnologias (whatweb, wappalyzer)
[ ] Deteccion de WAF (wafw00f)
[ ] Analisis SSL/TLS (testssl.sh)
[ ] Crawling/spidering de la aplicacion
[ ] Extraccion de endpoints de archivos JS
[ ] Resultados consolidados y organizados
```

### Fase 2: Enumeracion y Escaneo
```
[ ] Escaneo de puertos (nmap - TCP todos, UDP top 100)
[ ] Enumeracion de directorios y archivos (feroxbuster/gobuster)
[ ] Busqueda de archivos sensibles (.env, .git, backups, configs)
[ ] Enumeracion de endpoints API
[ ] Descubrimiento de parametros (arjun)
[ ] Identificacion de CMS y version
[ ] Scanner de CMS especifico (wpscan, joomscan, droopescan)
[ ] Escaneo con Nikto
[ ] Escaneo con Nuclei
[ ] Analisis de security headers
[ ] Verificacion de CORS
[ ] Mapa de superficie de ataque completo
```

### Fase 3: Analisis de Vulnerabilidades
```
OWASP Top 10 2025:

A01 - Broken Access Control:
[ ] Test de IDOR en todos los endpoints con IDs
[ ] Escalacion vertical (acceso a funciones de admin)
[ ] Escalacion horizontal (acceso a datos de otros usuarios)
[ ] Bypass de controles con metodos HTTP alternativos
[ ] Force browsing a endpoints no enlazados
[ ] Verificacion de CORS misconfiguration

A02 - Security Misconfiguration:
[ ] Credenciales por defecto
[ ] Paginas de error con informacion sensible
[ ] Directory listing
[ ] Archivos de configuracion accesibles
[ ] Headers de seguridad
[ ] Servicios de debug/admin expuestos
[ ] Versiones con CVEs conocidos

A03 - Software Supply Chain Failures:
[ ] Librerias JS con versiones vulnerables
[ ] Dependencias con CVEs conocidos
[ ] Scripts de terceros sin SRI
[ ] Integraciones inseguras

A04 - Cryptographic Failures:
[ ] SSL/TLS correctamente configurado
[ ] No transmision de datos sensibles por HTTP
[ ] Cookies con flags Secure y HttpOnly
[ ] Tokens/passwords no expuestos en frontend
[ ] Algoritmos criptograficos adecuados

A05 - Injection:
[ ] SQL Injection en todos los parametros
[ ] XSS Reflected en todos los inputs reflejados
[ ] XSS Stored en todos los campos almacenados
[ ] Command Injection donde aplique
[ ] SSTI en campos que usan templates
[ ] LDAP Injection donde aplique
[ ] NoSQL Injection en APIs JSON
[ ] XXE en endpoints que aceptan XML
[ ] Header injection

A06 - Insecure Design:
[ ] Logica de negocio (flujos, precios, cupones)
[ ] Rate limiting en funciones criticas
[ ] Race conditions
[ ] Flujos multi-step con posibilidad de saltar pasos

A07 - Authentication Failures:
[ ] Brute force en login
[ ] Enumeracion de usuarios
[ ] Password policy debil
[ ] Falta de MFA
[ ] Session fixation
[ ] Session timeout adecuado
[ ] Password reset seguro
[ ] JWT vulnerabilities
[ ] OAuth misconfiguration

A08 - Software and Data Integrity:
[ ] Deserializacion insegura
[ ] Falta de SRI en scripts externos
[ ] CI/CD pipeline expuesto

A09 - Logging and Monitoring:
[ ] Login fallidos generan alerta?
[ ] Actividad sospechosa se detecta?

A10 - Mishandling of Exceptional Conditions:
[ ] Inputs malformados revelan informacion
[ ] Errores 500 con stack traces
[ ] Fail open en autenticacion/autorizacion
[ ] Valores limite y tipos inesperados
```

### Fase 4: Explotacion
```
[ ] PoC para cada vulnerabilidad confirmada
[ ] Screenshots y logs de evidencia
[ ] Chaining de vulnerabilidades intentado
[ ] Impacto documentado para cada hallazgo
[ ] Notificacion inmediata de hallazgos criticos
```

### Fase 5: Post-Explotacion y Reporte
```
[ ] Limpieza de artefactos de prueba
[ ] Evaluacion de impacto (CIA) por hallazgo
[ ] CVSS 4.0 calculado para cada vulnerabilidad
[ ] Reporte ejecutivo redactado
[ ] Reporte tecnico con todos los hallazgos
[ ] Remediaciones especificas para cada hallazgo
[ ] Reporte revisado por QA antes de entrega
[ ] Entrega cifrada al cliente
[ ] Presentacion realizada
[ ] Retest planificado (si aplica)
```

---

## Checklist por Tipo de Vulnerabilidad

### SQL Injection
```
[ ] Probar comilla simple en cada parametro
[ ] Error-based SQLi
[ ] Boolean-based blind SQLi
[ ] Time-based blind SQLi
[ ] UNION-based SQLi
[ ] Probar en parametros GET
[ ] Probar en parametros POST
[ ] Probar en cookies
[ ] Probar en headers (User-Agent, Referer, X-Forwarded-For)
[ ] Probar en parametros JSON
[ ] Verificar con SQLMap (--level 3 --risk 2)
[ ] Documentar base de datos y tablas accesibles
[ ] Capturar screenshot de exfiltracion de datos
```

### XSS
```
[ ] Identificar todos los puntos de inyeccion
[ ] Reflected XSS basico
[ ] Probar bypass de filtros (encoding, case, event handlers)
[ ] Stored XSS en campos persistentes
[ ] DOM XSS (analizar sinks en JS)
[ ] XSS en headers HTTP reflejados
[ ] XSS en uploads (nombre de archivo, metadata)
[ ] XSS en error pages
[ ] Verificar contexto de inyeccion (HTML, JS, atributo, URL)
[ ] Probar exfiltracion de cookies como PoC
[ ] Documentar impacto (session hijacking, defacement, etc)
```

### SSRF
```
[ ] Identificar parametros que aceptan URLs
[ ] Probar acceso a localhost (127.0.0.1, 0.0.0.0, [::1])
[ ] Probar acceso a metadata de cloud (AWS, GCP, Azure)
[ ] Probar esquemas alternativos (file://, gopher://, dict://)
[ ] Bypass de filtros (hex, decimal, DNS rebinding)
[ ] Escaneo de puertos internos
[ ] Acceso a servicios internos (Redis, Elasticsearch, etc)
[ ] Documentar servicios internos descubiertos
```

### Autenticacion
```
[ ] Credenciales por defecto
[ ] Brute force con top passwords
[ ] Enumeracion de usuarios via login
[ ] Enumeracion via forgot password
[ ] Enumeracion via registro
[ ] Password policy (longitud, complejidad)
[ ] Account lockout tras intentos fallidos
[ ] MFA implementado para funciones criticas
[ ] Token de reset predecible?
[ ] Session ID entropia adecuada
[ ] Session timeout configurado
[ ] Invalidacion de sesion al cambiar password
[ ] JWT: algoritmo none
[ ] JWT: confusion de algoritmo (RS256->HS256)
[ ] JWT: brute force de secreto
[ ] JWT: claims manipulables
[ ] OAuth: open redirect en redirect_uri
[ ] OAuth: state parameter presente y validado
```

### Control de Acceso
```
[ ] IDOR en endpoints GET (lectura)
[ ] IDOR en endpoints POST/PUT/DELETE (escritura/borrado)
[ ] Acceso a panel de admin como usuario normal
[ ] Acceso a funciones de admin via API
[ ] Manipulacion de roles en requests
[ ] Path traversal
[ ] Method tampering (GET->POST->PUT->DELETE)
[ ] Header bypass (X-Original-URL, X-Forwarded-For)
[ ] Verificar que logout invalida la sesion
[ ] Verificar acceso despues de cambio de rol/permisos
```

---

## Checklist de Calidad del Reporte

```
ANTES DE ENTREGAR:

Contenido:
[ ] Todos los hallazgos tienen CVSS score
[ ] Todos los hallazgos tienen pasos de reproduccion
[ ] Todos los hallazgos tienen evidencia (screenshots/requests)
[ ] Todas las remediaciones son especificas y accionables
[ ] No hay falsos positivos incluidos
[ ] Hallazgos clasificados correctamente por severidad

Formato:
[ ] Sin errores ortograficos/gramaticales
[ ] Screenshots numerados y referenciados
[ ] Datos sensibles censurados/redactados
[ ] Tabla resumen de hallazgos presente
[ ] Indice de contenidos actualizado
[ ] Numeracion de paginas correcta

Seguridad:
[ ] Reporte cifrado para entrega
[ ] Datos del cliente protegidos
[ ] Evidencias almacenadas de forma segura
[ ] Plan de destruccion de datos post-retencion
```
