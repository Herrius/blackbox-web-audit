# Feroxbuster - Referencia

Content discovery rapido en Rust. Recursion automatica, filtrado avanzado, auto-tune de rate y soporte de resume. Reemplazo moderno de dirbuster/dirb.

## Instalacion

```bash
sudo apt install feroxbuster     # Kali
brew install feroxbuster         # macOS
cargo install feroxbuster        # Cargo
curl -sL https://raw.githubusercontent.com/epi052/feroxbuster/main/install-nix.sh | bash
```

## Flags

### Target
| Flag | Descripcion |
|------|-------------|
| `-u URL` | URL objetivo |
| `--stdin` | Leer URLs de stdin |
| `--url-file FILE` | Leer URLs de archivo |
| `--resume-from FILE` | Resumir scan desde state file |

### Wordlist
| Flag | Descripcion |
|------|-------------|
| `-w WORDLIST` | Wordlist (default: /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt) |
| `-e EXTENSIONS` | Extensiones (ej: -e php,html,txt) |
| `--dont-scan REGEX` | No escanear URLs que matcheen |

### Request
| Flag | Descripcion |
|------|-------------|
| `-a USER_AGENT` | User-Agent custom |
| `-b COOKIE` | Cookies |
| `-H HEADER` | Header custom (repetible) |
| `-m METHODS` | Metodos HTTP (default: GET) |
| `-d DATA` | POST body data |
| `-x PROXY` | Proxy URL |
| `-R` | Seguir redirects |
| `-k` | Desactivar verificacion TLS |
| `-T TIMEOUT` | Timeout en segundos (default: 7) |

### Recursion
| Flag | Descripcion |
|------|-------------|
| `-n` | No recursion |
| `-d DEPTH` | Profundidad maxima de recursion (default: 4) |
| `--force-recursion` | Forzar recursion en todos los directorios |
| `--dont-extract-links` | No extraer links para recursion |

### Filtros
| Flag | Descripcion |
|------|-------------|
| `-s CODES` | Status codes a incluir (default: todos excepto 404) |
| `-C CODES` | Status codes a excluir |
| `-S SIZE` | Filtrar por tamaño de response |
| `-W WORDS` | Filtrar por cantidad de palabras |
| `-N LINES` | Filtrar por cantidad de lineas |
| `--filter-regex REGEX` | Filtrar por regex en response body |
| `--filter-similar-to URL` | Filtrar responses similares a URL dada |
| `--dont-filter` | No aplicar filtros automaticos |

### Output
| Flag | Descripcion |
|------|-------------|
| `-o FILE` | Archivo de salida |
| `--json` | Output JSON |
| `-v` | Verboso (-vv para mas) |
| `-q` | Silencioso (solo resultados) |
| `--no-state` | No guardar state file |
| `--collect-backups` | Probar archivos backup (.bak, .old, ~) |
| `--collect-words` | Extraer palabras de responses para wordlist |
| `--collect-extensions` | Extraer extensiones para probar |

### Rate y Concurrencia
| Flag | Descripcion |
|------|-------------|
| `-t THREADS` | Threads concurrentes (default: 50) |
| `-L LIMIT` | Max requests/segundo |
| `--auto-tune` | Auto-ajustar rate basado en errores |
| `--auto-bail` | Parar si demasiados errores |
| `--time-limit TIME` | Limite de tiempo (ej: 10m, 1h) |
| `--scan-limit N` | Max scans concurrentes |
| `--parallel N` | URLs paralelas (con --stdin) |

### Config
| Flag | Descripcion |
|------|-------------|
| `--config FILE` | Archivo de config TOML |
| `--no-recursion` | Desactivar recursion |
| `--add-slash` | Append / a cada request |
| `--random-agent` | User-Agent aleatorio |
| `--burp` | Shortcut: --proxy http://127.0.0.1:8080 --insecure --no-recursion |
| `--burp-replay` | Enviar solo resultados al proxy |
| `--thorough` | Shortcut: --collect-words --collect-extensions --collect-backups |

## Ejemplos Practicos

```bash
# Scan basico
feroxbuster -u https://target.com

# Con extensiones
feroxbuster -u https://target.com -e php,html,txt,bak,old,js

# Wordlist custom + extensiones
feroxbuster -u https://target.com -w /usr/share/seclists/Discovery/Web-Content/big.txt \
  -e php,asp,aspx,jsp

# Sin recursion
feroxbuster -u https://target.com -n

# Profundidad limitada
feroxbuster -u https://target.com -d 2

# Filtrar status codes
feroxbuster -u https://target.com -C 404,403,500

# Filtrar por tamaño
feroxbuster -u https://target.com -S 0

# Filtrar responses similares a pagina 404
feroxbuster -u https://target.com --filter-similar-to https://target.com/nonexistent

# Con headers de autenticacion
feroxbuster -u https://target.com/api -H "Authorization: Bearer eyJ..." -b "session=abc123"

# Via proxy (Caido)
feroxbuster -u https://target.com -x http://127.0.0.1:8080 -k

# Shortcut Burp/Caido
feroxbuster -u https://target.com --burp

# Rate limited
feroxbuster -u https://target.com -L 30 -t 10

# Auto-tune para targets sensibles
feroxbuster -u https://target.com --auto-tune --auto-bail

# Output JSON
feroxbuster -u https://target.com -o results.json --json

# Silencioso (solo resultados)
feroxbuster -u https://target.com -q -o results.txt

# Thorough scan (collect everything)
feroxbuster -u https://target.com --thorough -e php,html

# Multiples URLs
echo -e "https://target1.com\nhttps://target2.com" | feroxbuster --stdin --parallel 2

# Resumir scan
feroxbuster --resume-from ferox-STATE.json

# Limite de tiempo
feroxbuster -u https://target.com --time-limit 30m

# POST requests
feroxbuster -u https://target.com/api -m POST -d '{"key":"FUZZ"}'

# Metodos multiples
feroxbuster -u https://target.com -m GET,POST,PUT,DELETE

# Random User-Agent
feroxbuster -u https://target.com --random-agent
```
