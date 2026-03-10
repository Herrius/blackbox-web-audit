# WhatWeb - Referencia

Fingerprinting de tecnologias web. Identifica CMS, frameworks, librerias JS, servidores web, versiones, emails, errores SQL y mas. 1,800+ plugins.

## Instalacion

```bash
sudo apt install whatweb                        # Kali/Debian
gem install whatweb                              # RubyGems
git clone https://github.com/urbanadventurer/WhatWeb.git  # GitHub
```

## Flags

| Flag | Descripcion |
|------|-------------|
| `<URL(s)>` | URLs, hostnames, IPs, CIDR, o rangos |
| `-i, --input-file FILE` | Leer targets de archivo. `-i /dev/stdin` para piping |
| `-a, --aggression LEVEL` | Nivel de agresividad 1-4 (default: 1) |
| `-U, --user-agent AGENT` | User-Agent custom |
| `-H, --header HEADER` | Header HTTP adicional |
| `-u, --user USER:PASS` | HTTP basic auth |
| `-c, --cookie COOKIES` | Cookies |
| `--follow-redirect WHEN` | `never`, `http-only`, `meta-only`, `same-site`, `always` (default) |
| `--max-redirects NUM` | Max redirects (default: 10) |
| `--proxy HOST:PORT` | Proxy HTTP |
| `--proxy-user USER:PASS` | Auth de proxy |
| `-p, --plugins PLUGINS` | Seleccionar plugins por nombre |
| `-l, --list-plugins` | Listar todos los plugins |
| `--search-plugins KEYWORDS` | Buscar plugins por keyword |
| `--grep PLUGIN` | Buscar regex custom en todas las URLs |
| `-v, --verbose` | Verbose. `-vv` para debug |
| `--log-brief FILE` | Log breve una linea por target |
| `--log-verbose FILE` | Log verbose |
| `--log-xml FILE` | Output XML |
| `--log-json FILE` | Output JSON |
| `--max-threads NUM` | Threads simultaneos (default: 25) |
| `--wait SEC` | Espera entre conexiones |
| `--url-prefix PREFIX` | Prefijo para todas las URLs |
| `--url-suffix SUFFIX` | Sufijo para todas las URLs |

## Niveles de Agresividad

| Nivel | Nombre | Comportamiento |
|-------|--------|----------------|
| **1** | Stealthy (default) | Un GET por target + redirects. Rapido y silencioso |
| **2** | (Reservado) | Igual que nivel 1 |
| **3** | Aggressive | Requests adicionales por target. Checks agresivos solo para tecnologias detectadas en nivel 1 |
| **4** | Heavy | Muchas requests por target. Todos los checks agresivos contra todas las URLs. Mas lento y ruidoso |

## Ejemplos Practicos

```bash
# Fingerprint basico
whatweb https://target.com

# Verbose
whatweb -v https://target.com

# Scan agresivo
whatweb -a 3 https://target.com

# Deteccion maxima
whatweb -a 4 https://target.com

# Multiples targets
whatweb target1.com target2.com target3.com

# Desde archivo
whatweb -i targets.txt

# Subnet completa
whatweb 192.168.1.0/24

# Rango de IPs
whatweb 192.168.1.1-192.168.1.254

# User-Agent custom
whatweb -U "Mozilla/5.0 (Windows NT 10.0; Win64; x64)" https://target.com

# JSON output
whatweb --log-json=output.json https://target.com

# Via proxy
whatweb --proxy 127.0.0.1:8080 https://target.com

# Con autenticacion
whatweb -u admin:password https://target.com/admin

# URL suffix para probar paginas especificas
whatweb --url-suffix="/wp-login.php" 192.168.1.0/24

# Throttled
whatweb --wait 2 -i targets.txt

# Solo plugins especificos
whatweb -p Apache,PHP,WordPress https://target.com

# Listar plugins
whatweb -l

# Buscar plugins por keyword
whatweb --search-plugins "CMS"

# Grep custom
whatweb --grep "admin|login|password" https://target.com

# Scan completo con log
whatweb -v -a 3 --log-brief=brief.txt --log-verbose=detailed.txt https://target.com

# Pipe desde otros tools
subfinder -d target.com -silent | httpx -silent | whatweb -i /dev/stdin
```
