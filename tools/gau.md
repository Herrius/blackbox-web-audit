# gau (GetAllUrls) - Referencia

Herramienta en Go para obtener URLs conocidas de multiples fuentes: Wayback Machine, Common Crawl, OTX (AlienVault) y URLScan.io.

## Instalacion

```bash
go install github.com/lc/gau/v2/cmd/gau@latest
```

## Flags

| Flag | Descripcion |
|------|-------------|
| `--threads INT` | Threads (default: 1) |
| `--retries INT` | Reintentos por request (default: 0) |
| `--timeout INT` | Timeout en segundos (default: 45) |
| `--verbose` | Output verboso |
| `--version` | Mostrar version |
| `--subs` | Incluir subdominios |
| `--o FILE` | Output a archivo |
| `--json` | Output JSON |
| `--blacklist EXTENSIONS` | Extensiones a excluir (ej: png,jpg,gif) |
| `--fc CODES` | Filtrar por status code |
| `--mc CODES` | Match por status code |
| `--fp` | Filtrar por patron (regex) |
| `--from DATE` | URLs desde fecha (YYYYMM) |
| `--to DATE` | URLs hasta fecha (YYYYMM) |
| `--providers SOURCES` | Providers: wayback, commoncrawl, otx, urlscan |

## Config (~/.gau.toml)

```toml
threads = 5
retries = 3
timeout = 60
blacklist = ["png", "jpg", "gif", "css", "woff", "svg", "ico"]

[urlscan]
apikey = "your-api-key"

[filters]
from = "202301"
to = "202612"
```

## Ejemplos Practicos

```bash
# URLs de un dominio
echo target.com | gau

# Con subdominios
echo target.com | gau --subs

# Solo Wayback Machine
echo target.com | gau --providers wayback

# Solo URLs recientes (2025 en adelante)
echo target.com | gau --from 202501

# Excluir assets estaticos
echo target.com | gau --blacklist png,jpg,gif,css,woff,svg,ico

# Output a archivo
echo target.com | gau --o urls.txt

# Output JSON
echo target.com | gau --json --o urls.json

# Multiples dominios
cat domains.txt | gau --threads 5

# Multiples providers
echo target.com | gau --providers wayback,commoncrawl,otx

# Filtrar URLs con parametros
echo target.com | gau | grep "=" | sort -u

# Buscar endpoints interesantes
echo target.com | gau --blacklist png,jpg,gif,css | grep -E "(api|admin|login|config|debug)" | sort -u

# Buscar archivos JS
echo target.com | gau | grep "\.js$" | sort -u

# Pipeline con httpx
echo target.com | gau --blacklist png,jpg,gif,css | httpx -silent -mc 200

# Pipeline con nuclei
echo target.com | gau | httpx -silent | nuclei -t cves/

# Pipeline buscar SQLi potenciales
echo target.com | gau | grep "=" | uro | sort -u | gf sqli

# Pipeline buscar XSS potenciales
echo target.com | gau | grep "=" | uro | sort -u | gf xss

# Pipeline buscar SSRF potenciales
echo target.com | gau | grep "=" | uro | sort -u | gf ssrf

# Combinado con waybackurls para maxima cobertura
(echo target.com | gau; echo target.com | waybackurls) | sort -u > all_urls.txt
```
