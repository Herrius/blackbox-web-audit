# SSLyze - Referencia

Analizador SSL/TLS en Python. Escanea protocolos, cipher suites, certificados y vulnerabilidades (Heartbleed, ROBOT, CCS Injection).

## Instalacion

```bash
pip install --upgrade sslyze    # pip
sudo apt install sslyze         # Kali/Debian
```

## Flags

### El shortcut --regular (mas usado)

`--regular` equivale a: `--sslv2 --sslv3 --tlsv1 --tlsv1_1 --tlsv1_2 --tlsv1_3 --reneg --resum --certinfo --hide_rejected_ciphers --compression --heartbleed --openssl_ccs --fallback --robot`

### Protocolos
| Flag | Descripcion |
|------|-------------|
| `--sslv2` | Test SSL 2.0 |
| `--sslv3` | Test SSL 3.0 |
| `--tlsv1` | Test TLS 1.0 |
| `--tlsv1_1` | Test TLS 1.1 |
| `--tlsv1_2` | Test TLS 1.2 |
| `--tlsv1_3` | Test TLS 1.3 |

### Certificado
| Flag | Descripcion |
|------|-------------|
| `--certinfo` | Analizar certificado(s) del servidor |
| `--certinfo_ca_file=<path>` | CA PEM custom para verificar (ej: CA interna) |

### Vulnerabilidades
| Flag | Descripcion |
|------|-------------|
| `--heartbleed` | Test Heartbleed |
| `--openssl_ccs` | Test CCS Injection (CVE-2014-0224) |
| `--robot` | Test ROBOT (RSA oracle) |
| `--fallback` | Test TLS_FALLBACK_SCSV |
| `--reneg` | Test renegociacion insegura |
| `--compression` | Test compresion TLS (CRIME) |

### Output
| Flag | Descripcion |
|------|-------------|
| `--json_out=<file>` | Output JSON |
| `--json_out=-` | JSON a stdout |
| `--hide_rejected_ciphers` | Solo cipher suites aceptadas |
| `--quiet` | Suprimir output |

### Config
| Flag | Descripcion |
|------|-------------|
| `--targets_in=<file>` | Leer targets de archivo |
| `--https_tunnel=<proxy>` | Tunnel via HTTP CONNECT proxy |
| `--starttls=<protocol>` | STARTTLS: smtp, pop3, imap, ftp, ldap, rdp, postgres, xmpp |
| `--mozilla_config={old,intermediate,modern}` | Comparar con config recomendada de Mozilla |
| `--slow_connection` | Para conexiones lentas |
| `--sni=<hostname>` | SNI custom |

## Ejemplos Practicos

```bash
# Scan completo (mas comun)
sslyze --regular target.com

# Solo certificado
sslyze --certinfo target.com

# Con JSON
sslyze --regular --json_out=results.json target.com

# Protocolos especificos
sslyze --tlsv1_2 --tlsv1_3 target.com

# Multiples targets
sslyze --regular target1.com target2.com target3.com:8443

# Targets desde archivo
sslyze --regular --targets_in=hosts.txt

# Comparar con Mozilla intermediate
sslyze --mozilla_config=intermediate target.com

# Via proxy
sslyze --regular --https_tunnel=proxy.corp.com:8080 internal.target.com

# Mail server STARTTLS
sslyze --regular --starttls=smtp mail.target.com:25

# CA interna
sslyze --certinfo --certinfo_ca_file=/path/to/internal-ca.pem internal.corp.com

# Solo vulnerabilidades
sslyze --heartbleed --openssl_ccs --robot --fallback --compression --reneg target.com
```
