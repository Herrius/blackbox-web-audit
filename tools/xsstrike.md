# XSStrike - Referencia

Suite avanzada de deteccion de XSS. Usa 4 parsers propios (HTML, JS, contexto, atributos) y genera payloads dinamicamente segun el contexto.

## Instalacion

```bash
git clone https://github.com/s0md3v/XSStrike.git
cd XSStrike
pip install -r requirements.txt
```

## Flags

| Flag | Descripcion |
|------|-------------|
| `-u TARGET` | URL objetivo con parametro |
| `--data DATA` | POST data |
| `-t THREADS` | Threads concurrentes (para crawling) |
| `--seeds SEEDS` | URLs desde archivo para seed del crawler |
| `--json` | Tratar POST data como JSON |
| `--path` | Inyectar payloads en el path de la URL |
| `--fuzzer` | Modo fuzzer (testa filtros y WAFs) |
| `--params` | Encontrar parametros ocultos automaticamente |
| `--crawl` | Crawlear el sitio desde la URL dada |
| `-l LEVEL` | Profundidad de crawling |
| `--blind` | Inyectar blind XSS en cada parametro (requiere --crawl) |
| `--skip-dom` | Saltar escaneo DOM XSS |
| `--headers HEADERS` | Headers custom (`"Header1: Val1\nHeader2: Val2"`) |
| `--proxy` | Usar proxy (configurar en core/config.py) |
| `-d DELAY` | Delay en segundos entre requests |
| `-e ENCODING` | Encodear payloads (base64) |
| `-f FILE` | Cargar payloads custom desde archivo |
| `--skip` | Saltar prompts de confirmacion |
| `--update` | Actualizar XSStrike |

## Modo Fuzzer

Testa que caracteres y tags son permitidos/bloqueados por filtros y WAFs. Reporta que pasa a traves de los filtros.

## Blind XSS

1. Registrarse en plataforma de blind XSS (xsshunter o self-hosted)
2. Editar `core/config.py` y configurar `blindPayload`
3. Usar `--blind` con `--crawl`

## Ejemplos Practicos

```bash
# Scan basico de XSS reflected en GET
python xsstrike.py -u "http://target.com/search?q=test"

# POST data
python xsstrike.py -u "http://target.com/login" --data "username=admin&password=test"

# JSON POST
python xsstrike.py -u "http://target.com/api/search" --data '{"query":"test"}' --json

# Inyeccion en path
python xsstrike.py -u "http://target.com/search/test" --path

# Con headers custom (autenticacion)
python xsstrike.py -u "http://target.com/search?q=test" \
  --headers "Authorization: Bearer TOKEN\nCookie: session=abc"

# Descubrimiento de parametros
python xsstrike.py -u "http://target.com/page.php" --params

# Con delay para evitar rate limiting
python xsstrike.py -u "http://target.com/search?q=test" -d 3

# Payloads en base64
python xsstrike.py -u "http://target.com/search?q=test" -e base64

# Payloads custom desde archivo
python xsstrike.py -u "http://target.com/search?q=test" -f /path/to/payloads.txt

# Fuzzer (testear filtros/WAF)
python xsstrike.py -u "http://target.com/search?q=test" --fuzzer

# Crawl basico
python xsstrike.py -u "http://target.com/" --crawl

# Crawl profundo con threads
python xsstrike.py -u "http://target.com/" --crawl -l 3 -t 10

# Crawl + skip DOM (mas rapido)
python xsstrike.py -u "http://target.com/" --crawl -l 3 --skip-dom

# Crawl con blind XSS
python xsstrike.py -u "http://target.com/" --crawl --blind

# Seed URLs desde archivo
python xsstrike.py --seeds urls.txt --crawl -l 2 -t 5

# Recon completo
python xsstrike.py -u "http://target.com/" --crawl -l 5 -t 10 --blind --skip-dom
```
