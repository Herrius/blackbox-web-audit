# httpx (ProjectDiscovery) - Referencia

Toolkit HTTP rapido y multi-proposito. Probing de hosts, fingerprinting de tecnologias, screenshots, extraccion de datos.

## Instalacion

```bash
go install -v github.com/projectdiscovery/httpx/cmd/httpx@latest
sudo apt install httpx-toolkit   # Kali
brew install httpx               # macOS
docker pull projectdiscovery/httpx
```

## Flags

### Input
| Flag | Descripcion |
|------|-------------|
| `-u, -target` | Host(s) objetivo |
| `-l, -list` | Archivo con hosts |

### Probes (informacion a mostrar)
| Flag | Descripcion |
|------|-------------|
| `-sc, -status-code` | Status code |
| `-cl, -content-length` | Content length |
| `-ct, -content-type` | Content type |
| `-title` | Titulo de pagina |
| `-bp, -body-preview[=N]` | Preview del body (default 100 chars) |
| `-server, -web-server` | Header Server |
| `-td, -tech-detect` | Tecnologias (wappalyzer) |
| `-ip` | IP del host |
| `-cname` | CNAME del host |
| `-asn` | Informacion ASN |
| `-cdn` | CDN/WAF en uso |
| `-location` | Redirect location |
| `-favicon` | Hash mmh3 del favicon |
| `-hash string` | Hash del body (md5, sha256) |
| `-jarm` | JARM TLS fingerprint |
| `-rt, -response-time` | Tiempo de respuesta |
| `-method` | Metodo HTTP |
| `-tls-grab` | Info de certificado TLS |

### Matchers (incluir)
| Flag | Descripcion |
|------|-------------|
| `-mc` | Match status codes (ej: `-mc 200,301`) |
| `-ml` | Match content length |
| `-ms` | Match string en response |
| `-mr` | Match regex en response |
| `-mrt` | Match response time (ej: `>2`) |

### Filters (excluir)
| Flag | Descripcion |
|------|-------------|
| `-fc` | Filtrar status codes (ej: `-fc 404,403`) |
| `-fl` | Filtrar content length |
| `-fs` | Filtrar string en response |
| `-fe` | Filtrar regex en response |
| `-fd` | Filtrar responses duplicadas |
| `-fep` | Filtrar error pages |
| `-fpt` | Filtrar tipos de pagina (login, captcha, parked) |

### Extractors
| Flag | Descripcion |
|------|-------------|
| `-er` | Extraer contenido por regex |
| `-ep` | Extraer patrones preset (url, ipv4, mail) |
| `-extract-fqdn` | Extraer subdominios/FQDNs de responses |

### Output
| Flag | Descripcion |
|------|-------------|
| `-o` | Archivo de salida |
| `-j, -json` | JSON lines |
| `-csv` | CSV |
| `-sr, -store-response` | Almacenar responses HTTP |
| `-srd, -store-response-dir` | Directorio para responses |
| `-irr, -include-response` | Incluir request/response completo en JSON |

### Config
| Flag | Descripcion |
|------|-------------|
| `-t, -threads` | Threads (default: 50) |
| `-rl, -rate-limit` | Max requests/segundo (default: 150) |
| `-H, -header` | Headers custom |
| `-proxy` | Proxy HTTP/SOCKS |
| `-fr, -follow-redirects` | Seguir redirects |
| `-p, -ports` | Puertos a probar |
| `-path` | Paths/endpoints a append |
| `-random-agent` | User-Agent aleatorio (on por default) |
| `-tls-probe` | Probar TLS y extraer SANs |
| `-csp-probe` | Probar dominios del header CSP |
| `-timeout` | Timeout (default: 10s) |
| `-silent` | Solo resultados |

### Screenshots
| Flag | Descripcion |
|------|-------------|
| `-ss, -screenshot` | Tomar screenshots |
| `-system-chrome` | Usar Chrome local |
| `-srd, -store-response-dir` | Directorio para screenshots |

## Ejemplos Practicos

```bash
# Probe basico
httpx -u https://target.com

# Desde archivo con status codes y titulos
httpx -l hosts.txt -sc -title

# Recon completo (combo recomendado)
httpx -l hosts.txt -sc -server -title -td -ip -o results.txt

# Deteccion de tecnologias
httpx -l hosts.txt -td -sc -title

# ASN fingerprinting
echo target.com | httpx -asn

# CIDR probing
echo 173.0.84.0/24 | httpx -silent

# JARM TLS fingerprint
subfinder -d target.com -silent | httpx -jarm

# Favicon hash (para Shodan)
subfinder -d target.com -silent | httpx -favicon

# Filtrar 404 y 403
httpx -l targets.txt -fc 404,403 -o active.txt

# Solo 200
httpx -l targets.txt -mc 200 -o live.txt

# Match string en body
httpx -l targets.txt -ms "admin" -o admin_panels.txt

# Filtrar duplicados
httpx -l targets.txt -fd -o unique.txt

# Seguir redirects
httpx -l targets.txt -fr -sc -location

# Probar puertos especificos
httpx -l hosts.txt -p 80,443,8080,8443

# Path brute-forcing
httpx -l hosts.txt -path /admin,/api/v1,/.env -sc

# Screenshots
httpx -l hosts.txt -ss -system-chrome -srd ./screenshots/

# Body preview
httpx -u https://target.com -silent -body-preview=200

# Via proxy con headers
httpx -l targets.txt -proxy http://127.0.0.1:8080 -H "Authorization: Bearer TOKEN"

# JSON con response completo
httpx -l hosts.txt -j -irr -o results.json

# CSV
httpx -l hosts.txt -sc -title -td -csv -o results.csv

# Almacenar responses
httpx -l hosts.txt -sr -srd ./responses/

# TLS probe (SANs de certificados)
httpx -l hosts.txt -tls-probe

# CSP probe (dominios adicionales)
httpx -l hosts.txt -csp-probe

# Extraer subdominios de response bodies
httpx -l hosts.txt -extract-fqdn

# Extraer emails
httpx -l hosts.txt -ep mail

# Probar todas las IPs (bypass CDN)
httpx -l hosts.txt -pa -sc -ip

# Pipeline: subfinder -> httpx -> nuclei
subfinder -d target.com -silent | httpx -silent | nuclei -s critical,high
```
