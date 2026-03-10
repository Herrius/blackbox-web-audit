# Nuclei (ProjectDiscovery) - Referencia

Scanner rapido y personalizable basado en templates. 9,000+ templates para CVEs, misconfigs, exposures, technologies.

## Instalacion

```bash
go install -v github.com/projectdiscovery/nuclei/v3/cmd/nuclei@latest
sudo apt install nuclei          # Kali
brew install nuclei              # macOS
docker pull projectdiscovery/nuclei
nuclei -ut                       # Actualizar templates
```

## Flags

### Target
| Flag | Descripcion |
|------|-------------|
| `-u, -target` | URL(s)/host(s) objetivo |
| `-l, -list` | Archivo con URLs |
| `-eh, -exclude-hosts` | Hosts a excluir |
| `-resume` | Reanudar desde checkpoint |
| `-sa, -scan-all-ips` | Escanear todas las IPs de DNS |

### Templates
| Flag | Descripcion |
|------|-------------|
| `-t, -templates` | Path(s) de templates/directorio |
| `-w, -workflows` | Path(s) de workflows |
| `-nt, -new-templates` | Solo templates nuevos |
| `-as, -automatic-scan` | Auto-detectar tech y mapear templates |
| `-ai, -prompt` | Generar template desde prompt AI |
| `-validate` | Validar sintaxis de templates |
| `-tl` | Listar todos los templates |
| `-tgl` | Listar tags disponibles |
| `-code` | Habilitar templates de protocolo code |
| `-dast` | Habilitar templates DAST/fuzzing |

### Filtrado de Templates
| Flag | Descripcion |
|------|-------------|
| `-tags` | Incluir por tags |
| `-etags, -exclude-tags` | Excluir por tags |
| `-id, -template-id` | Filtrar por ID |
| `-eid, -exclude-id` | Excluir por ID |
| `-s, -severity` | Filtrar por severidad: info, low, medium, high, critical |
| `-es, -exclude-severity` | Excluir por severidad |
| `-a, -author` | Filtrar por autor |
| `-pt, -type` | Filtrar por protocolo: http, dns, ssl, tcp, websocket, etc |

### Output
| Flag | Descripcion |
|------|-------------|
| `-o, -output` | Archivo de salida |
| `-j, -jsonl` | JSON lines |
| `-je, -json-export` | Exportar JSON |
| `-me, -markdown-export` | Exportar Markdown |
| `-se, -sarif-export` | Exportar SARIF (CI/CD) |
| `-silent` | Solo findings |
| `-sresp, -store-resp` | Almacenar request/response |
| `-srd, -store-resp-dir` | Directorio para responses |

### Config
| Flag | Descripcion |
|------|-------------|
| `-fr, -follow-redirects` | Seguir redirects |
| `-H, -header` | Headers custom |
| `-V, -var` | Variables custom (key=value) |
| `-p, -proxy` | Proxy HTTP/SOCKS5 |
| `-rl, -rate-limit` | Max requests/segundo (default: 150) |
| `-bs, -bulk-size` | Hosts paralelos por template (default: 25) |
| `-c, -concurrency` | Templates paralelos (default: 25) |
| `-timeout` | Timeout (default: 10s) |
| `-retries` | Reintentos (default: 1) |
| `-mhe, -max-host-error` | Max errores antes de saltar host (default: 30) |
| `-spm, -stop-at-first-match` | Parar al primer match por host |
| `-ni, -no-interactsh` | Deshabilitar OAST/Interactsh |
| `-headless` | Habilitar templates headless |
| `-ss, -scan-strategy` | Estrategia: auto, host-spray, template-spray |

### Interactsh (OOB)
| Flag | Descripcion |
|------|-------------|
| `-iserver` | Servidor Interactsh propio |
| `-itoken` | Token de autenticacion |

### Uncover (Asset Discovery)
| Flag | Descripcion |
|------|-------------|
| `-uc, -uncover` | Habilitar uncover |
| `-uq, -uncover-query` | Search queries |
| `-ue, -uncover-engine` | Engine: shodan, censys, fofa, hunter, zoomeye |

### Debug
| Flag | Descripcion |
|------|-------------|
| `-debug` | Request/response completo |
| `-dreq, -debug-req` | Solo requests |
| `-dresp, -debug-resp` | Solo responses |
| `-v, -verbose` | Verbose |
| `-stats` | Estadisticas del scan |

## Template Custom Basico

```yaml
id: my-custom-check
info:
  name: Custom Check
  author: enrique
  severity: medium
  tags: custom,web
http:
  - method: GET
    path:
      - "{{BaseURL}}/admin/config.php"
    matchers-condition: and
    matchers:
      - type: word
        words:
          - "database_password"
      - type: status
        status:
          - 200
```

## Ejemplos Practicos

```bash
# Scan con todos los templates
nuclei -u https://target.com

# Desde archivo
nuclei -l targets.txt

# Solo critical y high
nuclei -u target.com -s critical,high

# Por tags
nuclei -u target.com -tags cve
nuclei -u target.com -tags xss,sqli,rce
nuclei -u target.com -tags kev,vkev  # Known Exploited Vulnerabilities

# Excluir tags
nuclei -u target.com -etags dos,fuzz

# Templates especificos
nuclei -u target.com -t http/cves/
nuclei -u target.com -t http/cves/2024/ -t http/cves/2025/
nuclei -u target.com -t ssl/ -t dns/

# Por ID
nuclei -u target.com -id CVE-2024-1234

# Auto-scan (detecta tech y mapea templates)
nuclei -u target.com -as

# Template AI
nuclei -u target.com -ai "check for open redirect"

# Solo templates nuevos
nuclei -u target.com -nt

# Rate limited (stealth)
nuclei -u target.com -rl 3 -c 2

# Agresivo
nuclei -u target.com -rl 500 -bs 50 -c 50

# Via proxy
nuclei -u target.com -p http://127.0.0.1:8080

# Headers custom
nuclei -u target.com -H "Authorization: Bearer TOKEN" -H "Cookie: session=abc"

# JSON output
nuclei -u target.com -j -o results.jsonl

# SARIF (CI/CD)
nuclei -u target.com -se results.sarif

# Markdown report
nuclei -u target.com -me report/

# DAST fuzzing
nuclei -u target.com -dast
nuclei -u target.com -dast -fa high

# Parar al primer match
nuclei -u target.com -spm

# Sin Interactsh/OAST
nuclei -u target.com -ni

# Debug
nuclei -u target.com -debug

# Stats
nuclei -u target.com -stats

# Protocolo especifico
nuclei -u target.com -pt http
nuclei -u target.com -pt ssl

# Almacenar responses
nuclei -u target.com -sresp -srd ./evidence/

# Uncover (Shodan directo)
nuclei -uc -ue shodan -uq 'org:"Target Corp"' -s critical,high

# Dashboard
nuclei -u target.com -dashboard -sname "Q1-2026-Audit"

# OpenAPI input
nuclei -l openapi.yaml -im openapi -dast

# Workflows completos
subfinder -d target.com -silent | httpx -silent | nuclei -s critical,high -o findings.txt
subfinder -d target.com -silent | httpx -silent | nuclei -as
subfinder -d target.com -silent | httpx -silent | nuclei -tags kev,vkev -o kev.txt
```
