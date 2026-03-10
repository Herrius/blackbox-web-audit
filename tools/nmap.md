# Nmap - Referencia

Scanner de red y puertos. Descubrimiento de hosts, deteccion de servicios/versiones, OS fingerprinting, scripting (NSE). Herramienta fundamental de reconocimiento activo.

## Instalacion

```bash
sudo apt install nmap          # Debian/Kali
sudo pacman -S nmap            # Arch
brew install nmap              # macOS
```

## Flags

### Host Discovery
| Flag | Descripcion |
|------|-------------|
| `-sn` | Ping scan (solo discovery, sin port scan) |
| `-Pn` | Skip host discovery (asumir online) |
| `-PS <ports>` | TCP SYN ping |
| `-PA <ports>` | TCP ACK ping |
| `-PU <ports>` | UDP ping |
| `-PE` | ICMP echo ping |
| `-PP` | ICMP timestamp ping |
| `-PM` | ICMP netmask ping |
| `-PR` | ARP ping (red local) |
| `-n` | No DNS resolution |
| `-R` | DNS resolution siempre |
| `--dns-servers <srv>` | DNS servers custom |

### Tecnicas de Scan
| Flag | Descripcion |
|------|-------------|
| `-sS` | TCP SYN scan (stealth, default con root) |
| `-sT` | TCP Connect scan (default sin root) |
| `-sU` | UDP scan |
| `-sA` | TCP ACK scan (detectar firewalls) |
| `-sW` | TCP Window scan |
| `-sM` | TCP Maimon scan |
| `-sN` | TCP Null scan (sin flags) |
| `-sF` | TCP FIN scan |
| `-sX` | TCP Xmas scan (FIN+PSH+URG) |
| `-sI <zombie>` | Idle scan (spoof via zombie host) |
| `-sO` | IP protocol scan |

### Puertos
| Flag | Descripcion |
|------|-------------|
| `-p <ports>` | Puertos especificos (ej: `80,443,8080-8090`) |
| `-p-` | Todos los puertos (1-65535) |
| `-p T:80,U:53` | Puertos TCP y UDP especificos |
| `--top-ports <n>` | Top N puertos mas comunes |
| `-F` | Fast scan (top 100 puertos) |
| `--exclude-ports <ports>` | Excluir puertos |
| `-r` | Scan secuencial (no random) |

### Deteccion de Servicios y Version
| Flag | Descripcion |
|------|-------------|
| `-sV` | Detectar version de servicios |
| `--version-intensity <0-9>` | Intensidad de version probes |
| `--version-light` | Intensidad 2 (rapido) |
| `--version-all` | Intensidad 9 (completo) |

### Deteccion de OS
| Flag | Descripcion |
|------|-------------|
| `-O` | Deteccion de OS |
| `--osscan-limit` | Solo en hosts prometedores |
| `--osscan-guess` | Guess agresivo de OS |

### Timing y Performance
| Flag | Descripcion |
|------|-------------|
| `-T0` | Paranoid (IDS evasion, muy lento) |
| `-T1` | Sneaky |
| `-T2` | Polite |
| `-T3` | Normal (default) |
| `-T4` | Aggressive (recomendado en LAN) |
| `-T5` | Insane (puede perder puertos) |
| `--min-rate <n>` | Minimo de packets/segundo |
| `--max-rate <n>` | Maximo de packets/segundo |
| `--min-parallelism <n>` | Probes paralelos minimos |
| `--max-parallelism <n>` | Probes paralelos maximos |
| `--host-timeout <time>` | Timeout por host |
| `--scan-delay <time>` | Delay entre probes |
| `--max-retries <n>` | Max reintentos por port |

### NSE (Nmap Scripting Engine)
| Flag | Descripcion |
|------|-------------|
| `-sC` | Scripts default (equivale a `--script=default`) |
| `--script=<scripts>` | Scripts especificos o categorias |
| `--script-args=<args>` | Argumentos para scripts |
| `--script-updatedb` | Actualizar base de datos de scripts |
| `--script-help=<script>` | Ayuda de un script |

### Output
| Flag | Descripcion |
|------|-------------|
| `-oN <file>` | Output normal (texto) |
| `-oX <file>` | Output XML |
| `-oG <file>` | Output grepable |
| `-oA <basename>` | Los tres formatos |
| `-oS <file>` | Script kiddie output |
| `-v` | Verboso (-vv para mas) |
| `-d` | Debug (-dd para mas) |
| `--open` | Solo puertos abiertos |
| `--reason` | Motivo del estado del puerto |
| `--stats-every <time>` | Mostrar progreso periodicamente |
| `--packet-trace` | Trace de cada packet |
| `--resume <file>` | Resumir scan interrumpido |

### Firewall/IDS Evasion
| Flag | Descripcion |
|------|-------------|
| `-f` | Fragmentar paquetes |
| `-D <decoys>` | Decoy scan (IPs falsas) |
| `-S <IP>` | IP origen spoofeada |
| `-g <port>` | Puerto origen |
| `--data-length <n>` | Append datos random |
| `--ttl <n>` | TTL custom |
| `--spoof-mac <MAC>` | MAC spoofeada |
| `--badsum` | Checksum invalido |
| `--proxies <urls>` | Proxy chain (SOCKS4/HTTP) |

### Misc
| Flag | Descripcion |
|------|-------------|
| `-6` | Scan IPv6 |
| `-A` | Aggressive: -O -sV -sC --traceroute |
| `-iL <file>` | Leer targets de archivo |
| `--exclude <hosts>` | Excluir hosts |
| `--excludefile <file>` | Excluir hosts de archivo |
| `-e <iface>` | Interfaz de red |

## NSE Scripts para Web

| Script | Descripcion |
|--------|-------------|
| `http-enum` | Enumerar directorios/archivos comunes |
| `http-title` | Obtener titulo de paginas web |
| `http-headers` | Headers HTTP del servidor |
| `http-methods` | Metodos HTTP permitidos |
| `http-server-header` | Header Server |
| `http-robots.txt` | Contenido de robots.txt |
| `http-sitemap-generator` | Generar sitemap |
| `http-sql-injection` | Deteccion basica de SQLi |
| `http-xssed` | Buscar XSS reportados en xssed.com |
| `http-vuln-*` | Familia de scripts de vulns web |
| `http-waf-detect` | Detectar WAF |
| `http-waf-fingerprint` | Fingerprint de WAF |
| `http-cors` | Verificar CORS |
| `http-security-headers` | Security headers |
| `http-cookie-flags` | Flags de cookies |
| `http-auth-finder` | Encontrar paginas de login |
| `http-form-brute` | Brute force de formularios |
| `http-wordpress-*` | Scripts especificos WordPress |
| `ssl-enum-ciphers` | Enumerar cipher suites SSL/TLS |
| `ssl-cert` | Info del certificado SSL |
| `ssl-heartbleed` | Test Heartbleed |
| `ssl-poodle` | Test POODLE |

## Ejemplos Practicos

```bash
# Scan basico rapido
nmap -sS -sV -T4 target.com

# Scan agresivo completo
nmap -A -T4 target.com

# Puertos web comunes
nmap -sS -sV -p80,443,8080,8443,8000,8888,9090 target.com

# Todos los puertos + version
nmap -sS -sV -p- -T4 target.com

# Solo discovery en subred
nmap -sn 192.168.1.0/24

# Top 1000 puertos con scripts default
nmap -sS -sV -sC --top-ports 1000 target.com

# Scripts web especificos
nmap -sV --script=http-enum,http-title,http-headers,http-methods target.com

# Deteccion de WAF
nmap --script=http-waf-detect,http-waf-fingerprint target.com

# SSL/TLS completo
nmap --script=ssl-enum-ciphers,ssl-cert,ssl-heartbleed -p443 target.com

# Vuln scan web
nmap -sV --script=http-vuln-* -p80,443 target.com

# Brute force de formulario
nmap --script=http-form-brute --script-args='http-form-brute.path=/login' target.com

# UDP scan servicios comunes
nmap -sU -sV --top-ports 20 target.com

# Evasion basica de firewall
nmap -sS -T2 -f --data-length 24 -D RND:5 target.com

# Output en todos los formatos
nmap -sS -sV -sC -T4 -oA scan_results target.com

# Scan desde archivo de targets
nmap -sS -sV -iL targets.txt -oA results

# Solo puertos abiertos con razon
nmap -sS -sV --open --reason target.com

# Scan rate-limited (stealth)
nmap -sS -T2 --max-rate 100 target.com

# Script con argumentos
nmap --script=http-enum --script-args http-enum.displayall target.com

# Via proxy SOCKS
nmap --proxies socks4://127.0.0.1:9050 -sT -Pn target.com

# Multiples targets
nmap -sS -sV -T4 target1.com target2.com 192.168.1.0/24

# WordPress enum
nmap --script=http-wordpress-enum,http-wordpress-users -p80,443 target.com
```
