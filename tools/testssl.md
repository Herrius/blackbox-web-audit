# testssl / testssl - Referencia

Script bash para testing completo de SSL/TLS. No requiere dependencias externas (usa su propio OpenSSL bundled). El mas completo para auditoria de configuracion SSL/TLS.

> **Binary name varies by OS:** On Kali/Debian the binary is `/usr/bin/testssl` (no `.sh`). On macOS (via Homebrew) and when running from source, the binary is `testssl.sh`. Always verify with `which testssl testssl.sh` before running. All examples below use `testssl` for consistency with Kali.

## Instalacion

```bash
# Kali/Debian — binary is /usr/bin/testssl
sudo apt install testssl.sh     # package name has .sh, binary does NOT

# macOS — binary is testssl.sh
brew install testssl

# From source — binary is ./testssl.sh
git clone --depth 1 https://github.com/drwetter/testssl.sh.git
cd testssl.sh && ./testssl.sh
```

## Flags

### Target
| Flag | Descripcion |
|------|-------------|
| `<URI>` | host:port o URL (default port 443) |
| `--file FILE` | Leer targets de archivo (uno por linea) |
| `--mode MODE` | Modo serial o parallel (con --file) |

### Tests Individuales
| Flag | Descripcion |
|------|-------------|
| `-e` / `--each-cipher` | Test cada cipher individualmente |
| `-E` / `--cipher-per-proto` | Ciphers por protocolo |
| `-p` / `--protocols` | Test protocolos TLS/SSL |
| `-s` / `--std` / `--standard` | Test cipher suites estandar |
| `-S` / `--server-defaults` | Server defaults (cert, extensions) |
| `-P` / `--server-preference` | Server cipher preference |
| `-h` / `--header` / `--headers` | HTTP headers (HSTS, HPKP, etc) |
| `-U` / `--vulnerable` | Test todas las vulnerabilidades |
| `-B` / `--heartbleed` | Test Heartbleed |
| `-I` / `--ccs` / `--ccs-injection` | Test CCS Injection |
| `-T` / `--ticketbleed` | Test Ticketbleed |
| `-BB` / `--robot` | Test ROBOT |
| `-R` / `--renegotiation` | Test renegociacion |
| `-C` / `--compression` / `--crime` | Test CRIME |
| `-A` / `--beast` | Test BEAST |
| `-O` / `--poodle` | Test POODLE |
| `-Z` / `--tls-fallback` | Test TLS Fallback SCSV |
| `-W` / `--sweet32` | Test SWEET32 |
| `-F` / `--freak` | Test FREAK |
| `-D` / `--drown` | Test DROWN |
| `-J` / `--logjam` | Test Logjam |
| `-K` / `--breach` | Test BREACH |
| `-L` / `--lucky13` | Test Lucky13 |
| `--winshock` | Test WinShock |
| `--grease` | Test GREASE |

### Certificado
| Flag | Descripcion |
|------|-------------|
| `--cert-form PEM|DER` | Formato de cert para client auth |
| `--cert FILE` | Client certificate |
| `--certkey FILE` | Client private key |
| `--ca-file FILE` | CA bundle PEM custom |

### Output
| Flag | Descripcion |
|------|-------------|
| `--json` | Output JSON (pretty) |
| `--jsonfile FILE` | JSON a archivo |
| `--json-pretty` | JSON formateado |
| `--csv` | Output CSV |
| `--csvfile FILE` | CSV a archivo |
| `--html` | Output HTML |
| `--htmlfile FILE` | HTML a archivo |
| `--log` | Log a archivo (texto) |
| `--logfile FILE` | Log a archivo especifico |
| `--severity LEVEL` | Nivel minimo: LOW, MEDIUM, HIGH, CRITICAL |
| `-q` / `--quiet` | Menos output |
| `--color N` | Colores: 0=off, 1=on, 2=on+severity, 3=auto |
| `--wide` | Output wide (no truncar) |

### Config
| Flag | Descripcion |
|------|-------------|
| `--fast` | Omitir tests lentos |
| `--ip IP` | IP especifica (bypass DNS) |
| `--proxy HOST:PORT` | Proxy (SOCKS/HTTP CONNECT) |
| `--starttls PROTO` | STARTTLS: ftp, smtp, pop3, imap, xmpp, telnet, ldap, postgres, mysql |
| `--sneaky` | User-Agent no agresivo |
| `--assume-http` | Asumir HTTP para tests de headers |
| `--connect-timeout N` | Timeout de conexion |
| `--openssl-timeout N` | Timeout de OpenSSL |
| `--bugs` | Workarounds para bugs OpenSSL |
| `--debug N` | Debug level (0-6) |
| `--ssl-native` | Usar OpenSSL del sistema |
| `--add-ca FILE` | CA adicional |
| `--nodns none|min|max` | Control de DNS lookups |
| `--phone-out` | Permitir requests externas (CRL, OCSP) |
| `--mapping MAP` | Mapping: no-openssl, no-iana, openssl, iana |
| `-n` / `--no-dns` | No DNS lookup |
| `--hints` | Mostrar hints de mejora |
| `-9` / `--full` | Test completo (todos los checks) |

## Ejemplos Practicos

```bash
# Test completo (mas comun)
testssl target.com

# Test completo en puerto custom
testssl target.com:8443

# Solo vulnerabilidades
testssl -U target.com

# Solo protocolos
testssl -p target.com

# Solo cipher suites
testssl -E target.com

# Solo headers de seguridad
testssl -h target.com

# Solo server defaults (certificado)
testssl -S target.com

# Vulnerabilidades especificas
testssl -B -I -T -BB target.com

# Output JSON
testssl --jsonfile results.json target.com

# Output HTML
testssl --htmlfile report.html target.com

# Multiples formatos
testssl --jsonfile scan.json --htmlfile scan.html --csvfile scan.csv target.com

# Solo severidad alta y critica
testssl --severity HIGH target.com

# Scan rapido
testssl --fast target.com

# SMTP STARTTLS
testssl --starttls smtp mail.target.com:25

# IMAP STARTTLS
testssl --starttls imap mail.target.com:143

# Multiples targets
testssl --file targets.txt --mode parallel

# Via proxy
testssl --proxy proxy.corp.com:8080 target.com

# IP especifica (bypass DNS)
testssl --ip 10.0.0.1 target.com

# CA interna
testssl --add-ca /path/to/internal-ca.pem internal.corp.com

# Stealth (user-agent no obvio)
testssl --sneaky target.com

# Debug verboso
testssl --debug 2 target.com

# Full test con CRL/OCSP checking
testssl --full --phone-out target.com

# Con hints de mejora
testssl --hints target.com

# Wide output (para terminales anchas)
testssl --wide target.com
```
