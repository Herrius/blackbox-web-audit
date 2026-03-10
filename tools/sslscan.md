# sslscan - Referencia

Scanner SSL/TLS rapido en C. Muestra protocolos soportados, cipher suites, certificado y vulnerabilidades conocidas con output colorizado.

## Instalacion

```bash
sudo apt install sslscan      # Debian/Kali
brew install sslscan           # macOS
# Desde source (version mas reciente)
git clone https://github.com/rbsec/sslscan2.git
cd sslscan2 && make static && sudo make install
```

## Flags

### Target y Conexion
| Flag | Descripcion |
|------|-------------|
| `host:port` | Target (default port 443) |
| `--ipv4` | Solo IPv4 |
| `--ipv6` | Solo IPv6 |
| `--targets=<file>` | Leer targets de archivo (uno por linea) |
| `--sni-name=<name>` | SNI hostname custom |
| `--starttls-ftp` | STARTTLS en FTP |
| `--starttls-imap` | STARTTLS en IMAP |
| `--starttls-irc` | STARTTLS en IRC |
| `--starttls-ldap` | STARTTLS en LDAP |
| `--starttls-mysql` | STARTTLS en MySQL |
| `--starttls-pop3` | STARTTLS en POP3 |
| `--starttls-psql` | STARTTLS en PostgreSQL |
| `--starttls-smtp` | STARTTLS en SMTP |
| `--starttls-xmpp` | STARTTLS en XMPP |

### Protocolos
| Flag | Descripcion |
|------|-------------|
| `--ssl2` | Solo test SSL 2.0 |
| `--ssl3` | Solo test SSL 3.0 |
| `--tls10` | Solo test TLS 1.0 |
| `--tls11` | Solo test TLS 1.1 |
| `--tls12` | Solo test TLS 1.2 |
| `--tls13` | Solo test TLS 1.3 |
| `--no-ssl2` | Desactivar SSL 2.0 |
| `--no-ssl3` | Desactivar SSL 3.0 |
| `--no-tls10` | Desactivar TLS 1.0 |
| `--no-tls11` | Desactivar TLS 1.1 |
| `--no-tls12` | Desactivar TLS 1.2 |
| `--no-tls13` | Desactivar TLS 1.3 |

### Tests
| Flag | Descripcion |
|------|-------------|
| `--no-ciphersuites` | No enumerar cipher suites |
| `--no-cipher-details` | No mostrar detalles de cifrado |
| `--no-fallback` | No test TLS Fallback SCSV |
| `--no-renegotiation` | No test renegociacion |
| `--no-compression` | No test compresion TLS |
| `--no-heartbleed` | No test Heartbleed |
| `--no-groups` | No enumerar grupos de curvas |
| `--no-check-certificate` | No verificar certificado |

### Output
| Flag | Descripcion |
|------|-------------|
| `--show-certificate` | Mostrar certificado completo |
| `--show-client-cas` | Mostrar CAs aceptadas para client cert |
| `--show-ciphers` | Mostrar cipher suites soportadas por OpenSSL local |
| `--show-sigs` | Mostrar algoritmos de firma soportados |
| `--show-times` | Mostrar tiempos de handshake |
| `--xml=<file>` | Output XML |
| `--no-colour` | Sin colores (para parsear) |
| `--ocsp` | Request OCSP stapling |
| `--pk=<file>` | Archivo de clave PKCS#12 |
| `--pkpass=<pass>` | Password del PKCS#12 |
| `--verbose` | Output verboso |
| `--timeout=<sec>` | Timeout de conexion |
| `--connect-timeout=<sec>` | Timeout de connect |
| `--sleep=<ms>` | Pausa entre requests en ms |
| `--bugs` | Habilitar workarounds para bugs SSL |

## Ejemplos Practicos

```bash
# Scan completo (mas comun)
sslscan target.com

# Puerto custom
sslscan target.com:8443

# Con certificado completo
sslscan --show-certificate target.com

# Solo TLS 1.2 y 1.3
sslscan --no-ssl2 --no-ssl3 --no-tls10 --no-tls11 target.com

# Output XML para procesamiento
sslscan --xml=results.xml target.com

# SMTP STARTTLS
sslscan --starttls-smtp mail.target.com:25

# Multiples targets
sslscan --targets=hosts.txt

# Sin colores (para scripting)
sslscan --no-colour target.com > scan.txt

# Con SNI custom
sslscan --sni-name=virtual.target.com 10.0.0.1:443

# Scan rapido sin cipher details
sslscan --no-cipher-details target.com

# IMAP STARTTLS
sslscan --starttls-imap mail.target.com:143

# Con OCSP stapling
sslscan --ocsp target.com

# Rate limited
sslscan --sleep=500 target.com
```
