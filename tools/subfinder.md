# Subfinder (ProjectDiscovery) - Referencia

Enumeracion pasiva rapida de subdominios usando multiples fuentes de datos.

## Instalacion

```bash
go install -v github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest
sudo apt install subfinder     # Kali
brew install subfinder         # macOS
docker pull projectdiscovery/subfinder
```

## Flags

### Input
| Flag | Descripcion |
|------|-------------|
| `-d, -domain` | Dominio(s) objetivo |
| `-dL, -list` | Archivo con lista de dominios |

### Fuentes
| Flag | Descripcion |
|------|-------------|
| `-s, -sources` | Fuentes especificas (ej: `-s crtsh,github`) |
| `-recursive` | Solo fuentes que manejan subdominios recursivamente |
| `-all` | Usar todas las fuentes (lento pero completo) |
| `-es, -exclude-sources` | Excluir fuentes especificas |
| `-ls, -list-sources` | Mostrar todas las fuentes disponibles |

### Filtros
| Flag | Descripcion |
|------|-------------|
| `-m, -match` | Subdominios a matchear |
| `-f, -filter` | Subdominios a filtrar (excluir) |

### Rate Limit
| Flag | Descripcion |
|------|-------------|
| `-rl, -rate-limit` | Max requests HTTP/segundo (global) |
| `-rls` | Rate limits por proveedor (ej: `"hackertarget=10/s,shodan=15/s"`) |
| `-t` | Goroutines concurrentes (default: 10) |

### Output
| Flag | Descripcion |
|------|-------------|
| `-o, -output` | Archivo de salida |
| `-oJ, -json` | Output JSON lines |
| `-oD, -output-dir` | Directorio de output (multi-dominio) |
| `-cs, -collect-sources` | Incluir fuentes en JSON output |
| `-oI, -ip` | Incluir IPs (solo modo activo) |

### Config
| Flag | Descripcion |
|------|-------------|
| `-config` | Archivo config custom |
| `-pc, -provider-config` | Config de providers custom |
| `-r` | DNS resolvers (separados por coma) |
| `-rL, -rlist` | Archivo de resolvers |
| `-nW, -active` | Mostrar solo subdominios activos |
| `-proxy` | Proxy HTTP |
| `-silent` | Solo subdominios (sin banner) |
| `-timeout` | Timeout HTTP (default: 30s) |
| `-max-time` | Max tiempo de enumeracion en minutos (default: 10) |

## Config de API Keys

`$HOME/.config/subfinder/provider-config.yaml`:

```yaml
shodan:
  - TU_SHODAN_KEY
securitytrails:
  - TU_ST_KEY
virustotal:
  - TU_VT_KEY
github:
  - ghp_TU_GITHUB_TOKEN
censys:
  - TU_API_ID:TU_API_SECRET
chaos:
  - TU_CHAOS_KEY
intelx:
  - 2.intelx.io:TU_INTELX_KEY
hunter:
  - TU_HUNTER_KEY
fofa:
  - email@example.com:TU_FOFA_KEY
```

## Ejemplos Practicos

```bash
# Enumeracion basica
subfinder -d target.com

# Silent (limpio para piping)
subfinder -d target.com -silent

# Todas las fuentes (completo)
subfinder -d target.com -all

# Fuentes especificas
subfinder -d target.com -s crtsh,github,shodan

# Recursivo
subfinder -d target.com -recursive

# Bulk desde archivo
subfinder -dL domains.txt

# Guardar a archivo
subfinder -d target.com -o subs.txt

# JSON con fuentes
subfinder -d target.com -oJ -cs -o results.jsonl

# Modo activo (resolver + mostrar IPs)
subfinder -d target.com -nW -oI

# Rate limited
subfinder -d target.com -rl 10

# Rate limit por proveedor
subfinder -d target.com -rls "hackertarget=10/s,shodan=15/s"

# Matchear patrones
subfinder -d target.com -m "api,dev,staging"

# Filtrar subdominios
subfinder -d target.com -f "www,mail"

# Multi-dominio con directorio
subfinder -dL domains.txt -oD results/

# Max tiempo 5 minutos
subfinder -d target.com -max-time 5

# Via proxy
subfinder -d target.com -proxy http://127.0.0.1:8080

# Pipeline: subfinder -> httpx
subfinder -d target.com -silent | httpx -silent

# Pipeline completo: subfinder -> httpx -> nuclei
subfinder -d target.com -silent | httpx -silent | nuclei -s critical,high

# subfinder -> httpx con info
subfinder -d target.com -silent | httpx -sc -title -td
```
