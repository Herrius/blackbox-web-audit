# theHarvester - Referencia

OSINT harvesting de emails, subdominios, IPs y hostnames desde fuentes publicas.

## Instalacion

```bash
sudo apt install theharvester          # Kali/Debian
pip3 install theHarvester              # pip
pipx install theHarvester              # pipx (recomendado)
docker pull ghcr.io/laramies/theharvester  # Docker
```

## Flags

| Flag | Descripcion |
|------|-------------|
| `-d` | Dominio o nombre de empresa objetivo (requerido) |
| `-b` | Fuente(s) de datos (separadas por coma, o `all`) |
| `-l` | Limite de resultados (default: 500) |
| `-S` | Empezar desde resultado N (paginacion) |
| `-f` | Guardar output en HTML y XML |
| `-p` | Port scan en hosts descubiertos (80, 443, 22, 21, 8080) |
| `-n` | DNS reverse lookup en IPs descubiertas |
| `-c` | DNS brute force de hostnames |
| `-t` | TLD expansion brute force (.com, .org, .net, etc.) |
| `-e` | Usar DNS server especifico |
| `-v` | Verificar hostname via DNS, buscar virtual hosts |
| `-w` | Wordlist para escaneo de API endpoints |
| `-a` | Escanear API endpoints |
| `-q` | Modo silencioso (suprime warnings de API keys) |
| `--screenshot` | Capturas de pantalla de subdominios (requiere Playwright) |

## Fuentes de Datos

`baidu`, `bevigil`, `bitbucket`, `brave`, `bufferoverun`, `builtwith`, `censys`, `certspotter`, `chaos`, `commoncrawl`, `criminalip`, `crtsh`, `dehashed`, `dnsdumpster`, `duckduckgo`, `fofa`, `fullhunt`, `github-code`, `gitlab`, `hackertarget`, `haveibeenpwned`, `hudsonrock`, `hunter`, `hunterhow`, `intelx`, `leakix`, `leaklookup`, `netlas`, `onyphe`, `otx`, `pentesttools`, `projectdiscovery`, `rapiddns`, `robtex`, `rocketreach`, `securityscorecard`, `securityTrails`, `shodan`, `subdomaincenter`, `subdomainfinderc99`, `thc`, `tomba`, `urlscan`, `venacus`, `virustotal`, `waybackarchive`, `whoisxml`, `windvane`, `yahoo`, `zoomeye`

API keys en: `~/.theHarvester/api-keys.yaml`

## Ejemplos Practicos

```bash
# Recon basico con todas las fuentes
theHarvester -d target.com -b all

# Emails desde fuentes especificas
theHarvester -d target.com -b google,bing,duckduckgo,yahoo -l 500

# Subdominios via certificate transparency
theHarvester -d target.com -b crtsh,certspotter

# Recon completo: emails + subdominios + DNS brute + reverse DNS + port scan
theHarvester -d target.com -b all -l 1000 -c -n -p

# Guardar resultados
theHarvester -d target.com -b all -f resultados_target

# OSINT de infraestructura via Shodan
theHarvester -d target.com -b shodan,censys,criminalip

# Escaneo de API endpoints con wordlist custom
theHarvester -d target.com -b all -a -w /path/to/api-wordlist.txt

# Paginacion (empezar desde resultado 200)
theHarvester -d target.com -b bing -S 200 -l 500

# DNS server especifico + TLD expansion
theHarvester -d target.com -b all -e 8.8.8.8 -t

# Screenshots de subdominios descubiertos
theHarvester -d target.com -b all --screenshot
```
