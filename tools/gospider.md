# Gospider - Referencia

Web spider rapido en Go. Crawling paralelo con soporte de sitemap, robots.txt, Wayback Machine y extraccion de URLs/JS.

## Instalacion

```bash
go install github.com/jaeles-project/gospider@latest
```

## Flags

### Input
| Flag | Descripcion |
|------|-------------|
| `-s URL` | URL objetivo unica |
| `-S FILE` | Lista de URLs desde archivo |
| `--stdin` | Leer URLs de stdin |

### Crawling
| Flag | Descripcion |
|------|-------------|
| `-d DEPTH` | Profundidad de crawl (default: 1) |
| `-c INT` | Concurrencia (default: 5) |
| `-t INT` | Threads (default: 1) |
| `--timeout INT` | Timeout en segundos (default: 10) |
| `-p INT` | Delay entre requests en ms |
| `-u USER_AGENT` | User-Agent custom |
| `-H HEADER` | Header custom (repetible) |
| `--cookie COOKIE` | Cookies |
| `--proxy PROXY` | Proxy URL |
| `--burp` | Proxy a 127.0.0.1:8080 |
| `-r` | Incluir recursos externos (otros dominios) |
| `--no-redirect` | No seguir redirects |
| `-m INT` | Longitud maxima de URL (default: 0=sin limite) |

### Sources
| Flag | Descripcion |
|------|-------------|
| `--sitemap` | Parsear sitemap.xml |
| `--robots` | Parsear robots.txt |
| `-a` | Buscar URLs en Wayback Machine, Common Crawl, VirusTotal |
| `--subs` | Incluir subdominios |

### Scope
| Flag | Descripcion |
|------|-------------|
| `--include-subs` | Crawlear subdominios encontrados |
| `--include-other-source` | Incluir URLs de otras fuentes |
| `-w` | Solo URLs del mismo dominio |
| `--blacklist EXTENSIONS` | Extensiones a ignorar |

### Output
| Flag | Descripcion |
|------|-------------|
| `-o DIR` | Directorio de output |
| `--json` | Output JSON |
| `-q` | Silencioso |
| `-v` | Verboso |
| `-l INT` | Max URLs a crawlear |
| `--length INT` | Longitud minima de response |

## Ejemplos Practicos

```bash
# Crawl basico
gospider -s https://target.com -d 3

# Crawl profundo con concurrencia
gospider -s https://target.com -d 5 -c 10 -t 5

# Con sitemap y robots
gospider -s https://target.com --sitemap --robots -d 3

# Incluir fuentes externas (Wayback, CommonCrawl)
gospider -s https://target.com -a -d 2

# Con subdominios
gospider -s https://target.com --subs --include-subs -d 3

# Con autenticacion
gospider -s https://target.com --cookie "session=abc123" \
  -H "Authorization: Bearer eyJ..." -d 3

# Via proxy (Caido)
gospider -s https://target.com --burp -d 3

# Output a directorio
gospider -s https://target.com -d 3 -o results/

# Output JSON
gospider -s https://target.com -d 3 --json -o results/

# Multiples targets
gospider -S targets.txt -d 2 -c 5 -t 3 -o results/

# Pipeline desde stdin
cat urls.txt | gospider --stdin -d 2

# Silencioso con output a archivo
gospider -s https://target.com -d 3 -q -o results/

# Blacklist extensiones de media
gospider -s https://target.com -d 3 --blacklist "jpg,png,gif,css,woff,svg"

# Rate limited
gospider -s https://target.com -d 3 -c 2 -p 500

# Pipeline: extraer URLs unicas
gospider -s https://target.com -d 3 -q | sort -u > urls.txt

# Pipeline: extraer JS files
gospider -s https://target.com -d 3 -q | grep "\.js" | sort -u > js_files.txt

# Pipeline completo con nuclei
gospider -s https://target.com -d 3 -q | sort -u | httpx -silent | nuclei -t cves/
```
