# mitmproxy - Referencia

Proxy de intercepcion SSL/TLS interactivo en Python. Tres interfaces: `mitmproxy` (TUI), `mitmweb` (web GUI), `mitmdump` (CLI scriptable). Ideal para automatizacion y scripting.

## Instalacion

```bash
pip install mitmproxy           # pip
pipx install mitmproxy          # pipx (recomendado)
brew install mitmproxy          # macOS
sudo apt install mitmproxy      # Debian/Kali
# Docker
docker run --rm -it -p 8080:8080 mitmproxy/mitmproxy mitmdump
```

## Componentes

| Componente | Uso |
|------------|-----|
| `mitmproxy` | TUI interactivo (ncurses) |
| `mitmweb` | Interfaz web (browser) |
| `mitmdump` | CLI puro, scriptable, ideal para automatizacion |

## Flags (comunes a los 3)

### Proxy
| Flag | Descripcion |
|------|-------------|
| `-p PORT` / `--listen-port PORT` | Puerto de escucha (default: 8080) |
| `--listen-host HOST` | IP de escucha (default: todas) |
| `--mode MODE` | Modo: regular, transparent, socks5, reverse:URL, upstream:URL |
| `--ssl-insecure` | No verificar certificados SSL upstream |
| `--set block_global=false` | Permitir conexiones no-localhost |

### Certificados
| Flag | Descripcion |
|------|-------------|
| `--certs <spec>` | Certificado custom (domain=path.pem) |
| `--set confdir=DIR` | Directorio de config/certs (default: ~/.mitmproxy) |

### Filtrado y Modificacion
| Flag | Descripcion |
|------|-------------|
| `--intercept FILTER` | Interceptar flows que matcheen filtro |
| `--view-filter FILTER` | Solo mostrar flows que matcheen filtro |
| `--modify-headers PATTERN` | Modificar headers (formato: /regex/header/value) |
| `--modify-body PATTERN` | Modificar body (formato: /regex/old/new) |
| `--map-remote PATTERN` | Redirigir URLs (formato: /regex/replacement) |
| `--map-local PATTERN` | Servir archivos locales para URLs |
| `--anticache` | Eliminar headers de cache |
| `--anticomp` | Eliminar Accept-Encoding (forzar no-compresion) |
| `--showhost` | Usar header Host en vez de IP |

### Scripts y Addons
| Flag | Descripcion |
|------|-------------|
| `-s SCRIPT` | Cargar script Python (repetible) |

### Recording y Replay
| Flag | Descripcion |
|------|-------------|
| `-w FILE` | Escribir flows a archivo |
| `-r FILE` | Leer flows de archivo |
| `--rfile FILE` | Cargar flows al inicio |
| `--server-replay FILE` | Replay de respuestas del servidor |
| `--client-replay FILE` | Replay de requests del cliente |
| `--set server_replay_nopop=true` | No eliminar flows del replay |
| `--save-stream-file FILE` | Streaming save de flows |

### Output (mitmdump)
| Flag | Descripcion |
|------|-------------|
| `--set flow_detail=N` | Nivel de detalle (0-4) |
| `--set dumper_filter=FILTER` | Filtro para output |
| `-q` | Modo silencioso |
| `-v` | Verboso |

## Filtros de Flow

Sintaxis de filtros usada en `--intercept`, `--view-filter`, y scripts:

| Filtro | Descripcion |
|--------|-------------|
| `~d domain` | Dominio |
| `~u regex` | URL matchea regex |
| `~m METHOD` | Metodo HTTP |
| `~c CODE` | Response status code |
| `~h regex` | Header matchea regex |
| `~b regex` | Body matchea regex |
| `~bq regex` | Request body matchea |
| `~bs regex` | Response body matchea |
| `~t content-type` | Content-Type |
| `~q` | Solo requests |
| `~s` | Solo responses |
| `~a` | Assets (CSS/JS/imagenes) |
| `!` | NOT |
| `&` | AND |
| `\|` | OR |

## Script Basico (Addon)

```python
# addon.py
from mitmproxy import http

class ModifyRequest:
    def request(self, flow: http.HTTPFlow):
        # Agregar header a todas las requests
        flow.request.headers["X-Custom"] = "injected"

    def response(self, flow: http.HTTPFlow):
        # Log responses con status 200
        if flow.response.status_code == 200:
            print(f"[200] {flow.request.url}")

addons = [ModifyRequest()]
```

## Ejemplos Practicos

```bash
# Proxy basico
mitmproxy -p 8080

# mitmdump: capturar todo el trafico
mitmdump -p 8080 -w traffic.flow

# Solo trafico de un dominio
mitmdump -p 8080 --view-filter "~d target.com"

# Reverse proxy a un backend
mitmdump --mode reverse:https://target.com -p 8443

# Eliminar cache y compresion (ver todo en claro)
mitmdump -p 8080 --anticache --anticomp

# Modificar headers en todas las requests
mitmdump -p 8080 --modify-headers "/~q/User-Agent/Mozilla 5.0 Custom"

# Modificar body de responses
mitmdump -p 8080 --modify-body "/~s/original/replaced"

# Cargar script custom
mitmdump -p 8080 -s addon.py

# Replay de requests
mitmdump --client-replay recorded.flow -p 8080

# Replay de responses (mock server)
mitmdump --server-replay recorded.flow -p 8080

# Guardar solo requests a APIs
mitmdump -p 8080 -w api_traffic.flow "~u /api/"

# Detalle maximo de flows
mitmdump -p 8080 --set flow_detail=4

# Upstream proxy (chain con Caido)
mitmdump -p 9090 --mode upstream:http://127.0.0.1:8080

# SSL inseguro (targets con cert self-signed)
mitmdump -p 8080 --ssl-insecure

# Filtro complejo: POST a APIs que retornan JSON
mitmdump -p 8080 "~m POST & ~u /api & ~t json"

# Interfaz web en puerto custom
mitmweb -p 8080 --web-port 8081

# Exportar como HAR (via script built-in)
mitmdump -nr traffic.flow -s har_dump.py --set hardump=output.har

# Transparent proxy (requiere iptables)
sudo mitmdump --mode transparent -p 8080

# Leer y filtrar capture guardado
mitmdump -nr traffic.flow "~d target.com & ~c 200"
```
