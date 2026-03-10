# Gobuster - Referencia

Brute forcer en Go para directorios/archivos, DNS subdomains, vhosts y S3 buckets. Rapido y simple con multiples modos.

## Instalacion

```bash
go install github.com/OJ/gobuster/v3@latest
sudo apt install gobuster       # Kali
brew install gobuster            # macOS
```

## Modos

| Modo | Descripcion |
|------|-------------|
| `dir` | Brute force de directorios y archivos |
| `dns` | Brute force de subdominios DNS |
| `vhost` | Brute force de virtual hosts |
| `fuzz` | Fuzzing generico (reemplazar FUZZ keyword) |
| `s3` | Enumeracion de buckets S3 |
| `gcs` | Enumeracion de buckets Google Cloud Storage |
| `tftp` | Enumeracion TFTP |

## Flags Globales
| Flag | Descripcion |
|------|-------------|
| `-w WORDLIST` | Wordlist (requerido) |
| `-t THREADS` | Threads (default: 10) |
| `-o FILE` | Output a archivo |
| `--no-color` | Sin colores |
| `-z` | No mostrar progreso |
| `-q` | Silencioso |
| `-v` | Verboso |
| `--delay DURATION` | Delay entre requests (ej: 500ms) |
| `--no-error` | No mostrar errores |

## Flags Modo `dir`
| Flag | Descripcion |
|------|-------------|
| `-u URL` | URL objetivo |
| `-x EXTENSIONS` | Extensiones (ej: php,html,txt) |
| `-s CODES` | Status codes positivos (default: 200,204,301,302,307,401,403) |
| `-b CODES` | Status codes negativos (blacklist) |
| `-e` | Mostrar URL completa |
| `-r` | Seguir redirects |
| `-k` | Skip TLS verification |
| `-c COOKIE` | Cookies |
| `-H HEADER` | Header custom (repetible) |
| `-a USER_AGENT` | User-Agent custom |
| `-m METHOD` | Metodo HTTP (default: GET) |
| `-d DATA` | POST body |
| `-p PROXY` | Proxy URL |
| `-f` | Append / al final |
| `-n` | No mostrar status code |
| `--timeout DURATION` | Timeout (default: 10s) |
| `--wildcard` | Forzar procesamiento con wildcard |
| `--exclude-length SIZES` | Excluir por tamaño de response |
| `--no-tls-validation` | Skip TLS validation |
| `--retry N` | Reintentos |
| `--random-agent` | User-Agent aleatorio |
| `--discover-backup` | Buscar archivos backup de cada descubrimiento |

## Flags Modo `dns`
| Flag | Descripcion |
|------|-------------|
| `-d DOMAIN` | Dominio objetivo |
| `-r RESOLVER` | DNS resolver custom |
| `-c` | Mostrar CNAMEs |
| `-i` | Mostrar IPs |
| `--wildcard` | Forzar aunque haya wildcard DNS |
| `--timeout DURATION` | Timeout de DNS |

## Flags Modo `vhost`
| Flag | Descripcion |
|------|-------------|
| `-u URL` | URL objetivo |
| `--domain DOMAIN` | Domain para append |
| `--append-domain` | Append dominio base al wordlist |
| `-r` | Seguir redirects |
| `-k` | Skip TLS |
| `-c COOKIE` | Cookies |
| `-H HEADER` | Headers |
| `--exclude-length SIZES` | Excluir por tamaño |

## Flags Modo `fuzz`
| Flag | Descripcion |
|------|-------------|
| `-u URL` | URL con keyword FUZZ |
| `-b CODES` | Status codes a excluir |
| `-r` | Seguir redirects |
| `-k` | Skip TLS |
| `-H HEADER` | Headers |
| `--exclude-length SIZES` | Excluir por tamaño |

## Ejemplos Practicos

```bash
# === MODO DIR ===

# Directory brute force basico
gobuster dir -u https://target.com -w /usr/share/seclists/Discovery/Web-Content/common.txt

# Con extensiones
gobuster dir -u https://target.com \
  -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt \
  -x php,html,txt,bak

# Filtrar status codes
gobuster dir -u https://target.com -w wordlist.txt -b 404,403

# Excluir por tamaño (paginas 404 custom)
gobuster dir -u https://target.com -w wordlist.txt --exclude-length 4242

# Con autenticacion
gobuster dir -u https://target.com -w wordlist.txt \
  -c "session=abc123" -H "Authorization: Bearer eyJ..."

# Via proxy
gobuster dir -u https://target.com -w wordlist.txt -p http://127.0.0.1:8080 -k

# Mostrar URLs completas
gobuster dir -u https://target.com -w wordlist.txt -e

# Buscar backups
gobuster dir -u https://target.com -w wordlist.txt --discover-backup

# Rate limited
gobuster dir -u https://target.com -w wordlist.txt -t 5 --delay 200ms

# Output a archivo
gobuster dir -u https://target.com -w wordlist.txt -o results.txt

# === MODO DNS ===

# Subdomain brute force
gobuster dns -d target.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# Con IPs y CNAMEs
gobuster dns -d target.com -w subdomains.txt -i -c

# Resolver custom
gobuster dns -d target.com -w subdomains.txt -r 8.8.8.8

# === MODO VHOST ===

# Virtual host discovery
gobuster vhost -u https://target.com \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
  --append-domain

# Filtrar tamaños de respuesta default
gobuster vhost -u https://target.com -w vhosts.txt --exclude-length 0

# === MODO FUZZ ===

# Fuzz parametros
gobuster fuzz -u "https://target.com/page?FUZZ=test" -w params.txt

# Fuzz paths
gobuster fuzz -u "https://target.com/api/FUZZ/info" -w wordlist.txt -b 404

# Fuzz headers
gobuster fuzz -u https://target.com -w values.txt \
  -H "X-Custom: FUZZ" -b 403
```
