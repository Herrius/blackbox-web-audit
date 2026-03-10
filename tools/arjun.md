# Arjun - Referencia

Descubrimiento de parametros HTTP ocultos. Soporta GET, POST, JSON y XML. Wordlist built-in de ~25,890 parametros.

## Instalacion

```bash
pip3 install arjun       # pip
pipx install arjun       # pipx
sudo apt install arjun   # Kali
```

## Flags

| Flag | Descripcion |
|------|-------------|
| `-u URL` | URL objetivo |
| `-i FILE` | Importar targets de archivo |
| `-m METHOD` | Metodo: GET, POST, XML, JSON (default: GET) |
| `-w WORDLIST` | Wordlist custom |
| `-c CHUNKS` | Parametros por request (default: 500) |
| `-t THREADS` | Threads (default: 5) |
| `-d DELAY` | Delay entre requests en segundos |
| `-T TIMEOUT` | Timeout de request |
| `--headers` | Headers custom (JSON inline o editor) |
| `--include DATA` | Datos a incluir en cada request |
| `-o FILE` | Output JSON |
| `-oT FILE` | Output texto |
| `-oB [PROXY]` | Output a Burp/Caido proxy (default: 127.0.0.1:8080) |
| `--passive [SOURCE]` | Recolectar params de fuentes pasivas (wayback, commoncrawl, otx) |
| `--stable` | 1 thread + delay aleatorio 6-12s |
| `--disable-redirects` | No seguir redirects |
| `-q` | Modo silencioso |
| `--rate-limit N` | Max requests/segundo |

## Ejemplos Practicos

```bash
# GET params
arjun -u "https://target.com/endpoint"

# POST params
arjun -u "https://target.com/api/login" -m POST

# JSON params (APIs)
arjun -u "https://target.com/api/v2/users" -m JSON

# XML params
arjun -u "https://target.com/api/soap" -m XML

# Con headers de autenticacion
arjun -u "https://target.com/api/data" -m POST \
  --headers '{"Authorization": "Bearer eyJ...", "Content-Type": "application/json"}'

# Incluir params estaticos
arjun -u "https://target.com/search" --include "csrf_token=abc123&session=xyz"

# Wordlist custom con throttling
arjun -u "https://target.com/page" -w /path/to/params.txt -d 2 -t 2

# Multiples targets
arjun -i targets.txt -m GET -o results.json

# Pasivo (sin requests activas)
arjun -u "https://target.com" --passive

# Output a proxy para testing posterior
arjun -u "https://target.com/search" -oB 127.0.0.1:8080

# Para targets con rate limiting
arjun -u "https://target.com/api" --stable

# Chunk size reducido (URLs con limite de longitud)
arjun -u "https://target.com/page" -c 100
```
