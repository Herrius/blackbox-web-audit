# Katana (ProjectDiscovery) - Referencia

Web crawler de nueva generacion con soporte para JavaScript parsing, headless browsing y extraccion de endpoints.

## Instalacion

```bash
go install github.com/projectdiscovery/katana/cmd/katana@latest  # Go
brew install katana                                                # Homebrew
docker pull projectdiscovery/katana                                # Docker
```

## Flags

### Input

| Flag | Descripcion |
|------|-------------|
| `-u`, `-list` | URL(s) objetivo o archivo con lista |
| `-resume` | Reanudar escaneo desde resume.cfg |
| `-e`, `-exclude` | Excluir hosts (cdn, private-ips, CIDR, IP, regex) |

### Configuracion

| Flag | Descripcion |
|------|-------------|
| `-d`, `-depth` | Profundidad maxima de crawl (default: 3) |
| `-jc`, `-js-crawl` | Habilitar parsing de archivos JS para endpoints |
| `-jsl`, `-jsluice` | Habilitar JSLuice para scraping profundo de JS |
| `-ct`, `-crawl-duration` | Duracion maxima de crawl en segundos |
| `-kf`, `-known-files` | Crawlear archivos conocidos: `all`, `robotstxt`, `sitemapxml` |
| `-mrs`, `-max-response-size` | Tamano maximo de response a leer |
| `-timeout` | Timeout de request en segundos (default: 10) |
| `-aff`, `-automatic-form-fill` | Llenado automatico de formularios (experimental) |
| `-fx`, `-form-extraction` | Extraer form, input, textarea, select |
| `-retry` | Reintentos por request (default: 1) |
| `-proxy` | Proxy HTTP/SOCKS5 |
| `-H`, `-headers` | Headers/cookies custom |
| `-xhr`, `-xhr-extraction` | Extraer endpoints de requests XHR |
| `-s`, `-strategy` | Estrategia: `depth-first` (default) o `breadth-first` |

### Headless

| Flag | Descripcion |
|------|-------------|
| `-hl`, `-headless` | Habilitar crawling headless hibrido |
| `-sc`, `-system-chrome` | Usar Chrome instalado localmente |
| `-sb`, `-show-browser` | Mostrar navegador (debug) |
| `-nos`, `-no-sandbox` | Chrome sin sandbox |
| `-scp`, `-system-chrome-path` | Ruta a binario de Chrome |
| `-noi`, `-no-incognito` | Chrome sin modo incognito |

### Scope

| Flag | Descripcion |
|------|-------------|
| `-cs`, `-crawl-scope` | Regex de URLs in-scope a seguir |
| `-cos`, `-crawl-out-scope` | Regex de URLs out-of-scope a excluir |
| `-fs`, `-field-scope` | Campo de scope: `dn`, `rdn` (default), `fqdn` |
| `-ns`, `-no-scope` | Deshabilitar scope basado en host |
| `-do`, `-display-out-scope` | Mostrar endpoints externos encontrados |

### Filtros

| Flag | Descripcion |
|------|-------------|
| `-em`, `-extension-match` | Matchear solo extensiones especificas |
| `-ef`, `-extension-filter` | Excluir extensiones especificas |
| `-ndef` | Deshabilitar filtros de extension por defecto |

### Output

| Flag | Descripcion |
|------|-------------|
| `-o`, `-output` | Archivo de salida |
| `-f`, `-field` | Campo a mostrar (ver tabla abajo) |
| `-j`, `-jsonl` | Output en JSON lines |
| `-sr`, `-store-response` | Almacenar requests y responses HTTP |
| `-srd`, `-store-response-dir` | Directorio para responses almacenadas |

### Rate Limiting

| Flag | Descripcion |
|------|-------------|
| `-c`, `-concurrency` | Fetchers concurrentes (default: 10) |
| `-p`, `-parallelism` | Inputs concurrentes (default: 10) |
| `-rd`, `-delay` | Delay entre requests en segundos |
| `-rl`, `-rate-limit` | Max requests/segundo (default: 150) |
| `-rlm`, `-rate-limit-minute` | Max requests/minuto |

## Campos de Extraccion (-f)

| Campo | Output |
|-------|--------|
| `url` | URL completa |
| `path` | Solo path |
| `fqdn` | FQDN |
| `rdn` | Root domain |
| `rurl` | Root URL (scheme + host) |
| `qurl` | URLs con query parameters |
| `qpath` | Paths con query parameters |
| `file` | Nombres de archivo |
| `key` | Claves de parametros |
| `value` | Valores de parametros |
| `kv` | Pares key=value |
| `dir` | Directorios |
| `udir` | Directorios unicos |

## Modos de Crawl

- **Standard** (default): Go net/http. Rapido, paginas server-rendered.
- **Headless** (`-hl`): Chrome headless. SPAs (React, Angular, Vue). Mas lento pero captura endpoints generados dinamicamente.

## Ejemplos Practicos

```bash
# Crawl basico
katana -u https://target.com

# Crawl con JavaScript parsing (encuentra endpoints ocultos)
katana -u https://target.com -jc

# Crawl profundo
katana -u https://target.com -d 5 -jc

# Headless para SPAs con extraccion XHR
katana -u https://target.com -hl -jc -xhr

# Crawl completo con archivos conocidos
katana -u https://target.com -d 5 -jc -kf all -ef png,jpg,css,svg,gif,ico,woff,woff2,ttf

# Extraer solo URLs con query parameters (para fuzzing)
katana -u https://target.com -jc -f qurl

# Extraer directorios unicos
katana -u https://target.com -d 5 -f udir

# Extraer claves de parametros (para inyeccion)
katana -u https://target.com -jc -f key

# Solo archivos PHP/JSP/ASPX
katana -u https://target.com -em php,jsp,aspx

# Filtrar assets estaticos
katana -u https://target.com -ef png,jpg,gif,css,svg,woff,ico,mp4,pdf

# Output a archivo
katana -u https://target.com -jc -o endpoints.txt

# JSON lines con request/response completo
katana -u https://target.com -jc -jsonl -o endpoints.jsonl

# Almacenar responses para analisis posterior
katana -u https://target.com -jc -sr -srd ./responses

# Con headers custom (autenticacion)
katana -u https://target.com -H "Authorization: Bearer TOKEN" -H "Cookie: session=abc123"

# Via proxy (Caido)
katana -u https://target.com -proxy http://127.0.0.1:8080

# Multiples targets desde archivo
katana -list targets.txt -jc -d 3 -o all.txt

# Rate limited (escaneo respetuoso)
katana -u https://target.com -rl 10 -rd 2

# Solo crawl de paths que matcheen patron
katana -u https://target.com -cs ".*\/api\/.*"

# Breadth-first
katana -u https://target.com -s breadth-first -d 3

# Form extraction + headless completo
katana -u https://target.com -hl -aff -jc -xhr -d 5

# Pipeline: katana -> httpx -> nuclei
katana -u https://target.com -jc -f qurl | httpx -silent | nuclei -t cves/

# Duracion maxima de 5 minutos
katana -u https://target.com -ct 300 -jc

# Reanudar escaneo interrumpido
katana -resume resume.cfg

# Mostrar links externos encontrados
katana -u https://target.com -do
```
