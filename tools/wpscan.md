# WPScan - Referencia

Scanner de seguridad especifico para WordPress. Identifica vulnerabilidades, enumera plugins, themes, usuarios y realiza brute force.

## Instalacion

```bash
sudo apt install wpscan                    # Kali/Debian
gem install wpscan                         # RubyGems
docker pull wpscanteam/wpscan              # Docker
wpscan --update                            # Actualizar DB
```

API Token: registrar en https://wpscan.com/register (free: 25 requests/dia).

## Flags

| Flag | Descripcion |
|------|-------------|
| `--url URL` | URL del WordPress objetivo (requerido) |
| `--api-token TOKEN` | Token de API WPScan para datos de vulnerabilidades |
| `--enumerate [OPTS]` | Modo de enumeracion (ver tabla) |
| `--detection-mode MODE` | `mixed` (default), `passive`, `aggressive` |
| `--plugins-detection MODE` | Deteccion de plugins: `mixed`, `passive`, `aggressive` |
| `--plugins-version-detection MODE` | Version de plugins: `mixed`, `passive`, `aggressive` |
| `--themes-detection MODE` | Deteccion de themes: `mixed`, `passive`, `aggressive` |
| `-U, --usernames LIST` | Lista de usuarios o archivo para brute force |
| `-P, --passwords FILE` | Wordlist de passwords para brute force |
| `--password-attack MODE` | Tipo: `wp-login` (default), `xmlrpc`, `xmlrpc-multicall` |
| `--max-passwords NUM` | Max passwords por usuario |
| `--login-uri URI` | URI custom de login |
| `--stealthy` | Alias para `--random-user-agent --detection-mode passive --plugins-version-detection passive` |
| `--force` | Forzar scan aunque no detecte WordPress |
| `--random-user-agent, --rua` | User-Agent aleatorio |
| `--proxy PROTOCOL://IP:PORT` | Proxy HTTP/SOCKS |
| `--cookie-string COOKIE` | Cookie para requests |
| `-t, --max-threads NUM` | Threads maximos (default: 5) |
| `--throttle MS` | Delay entre requests en ms |
| `--disable-tls-checks` | Deshabilitar verificacion SSL/TLS |
| `--wp-content-dir DIR` | Directorio wp-content custom |
| `-o, --output FILE` | Guardar output |
| `-f, --format FORMAT` | Formato: `cli`, `json`, `cli-no-colour` |
| `--tor` | Rutear via TOR |

## Enumeracion (-e / --enumerate)

| Valor | Que Enumera |
|-------|-------------|
| `vp` | Plugins vulnerables |
| `ap` | Todos los plugins |
| `p` | Plugins populares |
| `vt` | Themes vulnerables |
| `at` | Todos los themes |
| `t` | Themes populares |
| `tt` | Timthumbs |
| `cb` | Config backups (wp-config.php backups) |
| `dbe` | Database exports |
| `u` | User IDs (default 1-10) |
| `u1-30` | User IDs rango 1-30 |
| `m` | Media IDs (default 1-100) |
| `m1-500` | Media IDs rango 1-500 |

Default (sin valor): `vp,vt,tt,cb,dbe,u,m`

## Ejemplos Practicos

```bash
# Scan basico
wpscan --url https://target.com

# Con API token para vulnerabilidades
wpscan --url https://target.com --api-token TOKEN

# Enumerar plugins y themes vulnerables
wpscan --url https://target.com -e vp,vt --api-token TOKEN

# Enumerar TODOS los plugins agresivamente
wpscan --url https://target.com -e ap --plugins-detection aggressive

# Enumerar usuarios (IDs 1-50)
wpscan --url https://target.com -e u1-50

# Enumeracion completa
wpscan --url https://target.com -e vp,vt,tt,cb,dbe,u,m --api-token TOKEN

# Brute force de password
wpscan --url https://target.com -U admin,editor -P /usr/share/wordlists/rockyou.txt

# Brute force XML-RPC multicall (mas rapido)
wpscan --url https://target.com -U admin -P wordlist.txt --password-attack xmlrpc-multicall

# Scan sigiloso
wpscan --url https://target.com --stealthy

# Forzar scan en WordPress no estandar
wpscan --url https://target.com/blog --force --wp-content-dir custom-content

# Via proxy (Caido)
wpscan --url https://target.com --proxy http://127.0.0.1:8080

# Throttled para evitar deteccion
wpscan --url https://target.com --throttle 2000 --random-user-agent

# Scan agresivo completo con JSON
wpscan --url https://target.com -e vp,ap,vt,at,tt,cb,dbe,u \
  --detection-mode aggressive --plugins-detection aggressive \
  -o results.json -f json --api-token TOKEN

# Con cookies (scan autenticado)
wpscan --url https://target.com --cookie-string "wordpress_logged_in_abc=admin%7C..."

# Self-signed certs
wpscan --url https://target.com --disable-tls-checks

# Via TOR
wpscan --url https://target.com --tor --random-user-agent
```
