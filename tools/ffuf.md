# ffuf - Referencia

Fast web fuzzer en Go. Fuzzing de directorios, parametros, headers, POST data, vhosts y mas. Extremadamente rapido y flexible con sistema de matchers/filters.

## Instalacion

```bash
go install github.com/ffuf/ffuf/v2@latest
sudo apt install ffuf           # Kali
brew install ffuf               # macOS
```

## Flags

### Target y Request
| Flag | Descripcion |
|------|-------------|
| `-u URL` | URL objetivo (usar FUZZ como placeholder) |
| `-w WORDLIST` | Wordlist (formato: path o path:KEYWORD) |
| `-X METHOD` | Metodo HTTP (default: GET) |
| `-d DATA` | POST data (usar FUZZ como placeholder) |
| `-H HEADER` | Header custom (repetible, usar FUZZ) |
| `-b COOKIE` | Cookie data |
| `-r` | Seguir redirects |
| `-recursion` | Fuzzing recursivo |
| `-recursion-depth N` | Profundidad de recursion |
| `-recursion-strategy` | greedy o default |
| `-x PROXY` | Proxy URL |
| `-replay-proxy URL` | Proxy solo para matches |
| `-timeout N` | Timeout HTTP en segundos (default: 10) |

### Matchers (incluir resultados)
| Flag | Descripcion |
|------|-------------|
| `-mc CODES` | Match por status code (default: 200,204,301,302,307,401,403,405,500) |
| `-ml LINES` | Match por cantidad de lineas |
| `-mw WORDS` | Match por cantidad de palabras |
| `-ms SIZE` | Match por tamaño de response |
| `-mr REGEX` | Match por regex en response |
| `-mt TIME` | Match por tiempo de respuesta (ms) |

### Filters (excluir resultados)
| Flag | Descripcion |
|------|-------------|
| `-fc CODES` | Filtrar por status code |
| `-fl LINES` | Filtrar por cantidad de lineas |
| `-fw WORDS` | Filtrar por cantidad de palabras |
| `-fs SIZE` | Filtrar por tamaño de response |
| `-fr REGEX` | Filtrar por regex en response |
| `-ft TIME` | Filtrar por tiempo de respuesta (ms) |

### Input
| Flag | Descripcion |
|------|-------------|
| `-w PATH:KEYWORD` | Wordlist con keyword custom (para multi-fuzz) |
| `-input-cmd CMD` | Comando como input |
| `-input-num N` | Numero de inputs del comando |
| `-mode MODE` | Modo multi-wordlist: clusterbomb, pitchfork, sniper |
| `-request FILE` | Request HTTP raw desde archivo |
| `-request-proto PROTO` | Protocolo para request raw (default: https) |
| `-e EXTENSIONS` | Extensiones a probar (ej: .php,.html,.txt) |

### Output
| Flag | Descripcion |
|------|-------------|
| `-o FILE` | Archivo de salida |
| `-of FORMAT` | Formato: json, ejson, html, md, csv, all |
| `-od DIR` | Directorio para almacenar responses |
| `-or` | No output de redirects |
| `-v` | Output verboso (mostrar redirect URLs) |
| `-s` | Silencioso (sin banner ni progreso) |

### Config General
| Flag | Descripcion |
|------|-------------|
| `-t THREADS` | Threads concurrentes (default: 40) |
| `-rate N` | Max requests/segundo |
| `-p DELAY` | Delay entre requests (ej: "0.1" o "0.1-0.5") |
| `-maxtime N` | Tiempo maximo total en segundos |
| `-maxtime-job N` | Tiempo maximo por job |
| `-ac` | Auto-calibrate filtering |
| `-acc` | Auto-calibrate per-host |
| `-c` | Output colorizado |
| `-ic` | Ignorar comentarios en wordlist |
| `-noninteractive` | Desactivar UI interactivo |
| `-sa` | Stop on all error cases |
| `-se` | Stop on spurious errors |
| `-sf` | Stop when >95% responses son errores |
| `-config FILE` | Archivo de config TOML |

## Ejemplos Practicos

```bash
# Directory fuzzing basico
ffuf -u https://target.com/FUZZ -w /usr/share/seclists/Discovery/Web-Content/common.txt

# Con extensiones
ffuf -u https://target.com/FUZZ -w /usr/share/seclists/Discovery/Web-Content/common.txt \
  -e .php,.html,.txt,.bak,.old

# Filtrar por tamaño (eliminar paginas 404 custom)
ffuf -u https://target.com/FUZZ -w wordlist.txt -fs 4242

# Filtrar por status code
ffuf -u https://target.com/FUZZ -w wordlist.txt -fc 404,403

# Auto-calibrate (detectar y filtrar respuestas base automaticamente)
ffuf -u https://target.com/FUZZ -w wordlist.txt -ac

# Subdomain/vhost fuzzing
ffuf -u https://target.com -w subdomains.txt -H "Host: FUZZ.target.com" -fs 0

# Parameter fuzzing GET
ffuf -u "https://target.com/page?FUZZ=test" -w params.txt -fs 4242

# Parameter value fuzzing
ffuf -u "https://target.com/page?id=FUZZ" -w /usr/share/seclists/Fuzzing/integers.txt

# POST data fuzzing
ffuf -u https://target.com/login -X POST \
  -d "username=admin&password=FUZZ" -w passwords.txt -fc 401

# POST JSON fuzzing
ffuf -u https://target.com/api/login -X POST \
  -H "Content-Type: application/json" \
  -d '{"user":"admin","pass":"FUZZ"}' -w passwords.txt -fc 401

# Header fuzzing
ffuf -u https://target.com/admin -H "X-Custom-Header: FUZZ" -w values.txt

# Multi-wordlist (clusterbomb: todas las combinaciones)
ffuf -u https://target.com/login -X POST \
  -d "username=USER&password=PASS" \
  -w users.txt:USER -w passwords.txt:PASS \
  -mode clusterbomb -fc 401

# Multi-wordlist (pitchfork: en paralelo)
ffuf -u https://target.com/FUZZ1/FUZZ2 \
  -w dirs.txt:FUZZ1 -w files.txt:FUZZ2 -mode pitchfork

# Recursivo
ffuf -u https://target.com/FUZZ -w wordlist.txt -recursion -recursion-depth 3

# Con autenticacion
ffuf -u https://target.com/api/FUZZ -w endpoints.txt \
  -H "Authorization: Bearer eyJ..." -b "session=abc123"

# Rate limited
ffuf -u https://target.com/FUZZ -w big_wordlist.txt -rate 50 -t 5

# Output JSON completo
ffuf -u https://target.com/FUZZ -w wordlist.txt -o results.json -of json

# Via proxy (Caido)
ffuf -u https://target.com/FUZZ -w wordlist.txt -x http://127.0.0.1:8080

# Replay solo matches via proxy
ffuf -u https://target.com/FUZZ -w wordlist.txt -replay-proxy http://127.0.0.1:8080

# Match por regex (buscar errores SQL)
ffuf -u "https://target.com/page?id=FUZZ" -w sqli_payloads.txt \
  -mr "SQL syntax|mysql_fetch|ORA-|PostgreSQL"

# Match por tiempo (time-based detection)
ffuf -u "https://target.com/page?id=FUZZ" -w payloads.txt -mt 5000

# Request desde archivo raw
ffuf -request raw_request.txt -request-proto https -w wordlist.txt

# Guardar responses de matches
ffuf -u https://target.com/FUZZ -w wordlist.txt -od responses/
```
