# Masscan - Referencia

Scanner de puertos masivo. Puede escanear todo el internet en ~6 minutos. Usa stack TCP/IP propio asincronico. Sintaxis similar a nmap.

## Instalacion

```bash
sudo apt install masscan       # Debian/Kali
brew install masscan           # macOS
# Desde source
git clone https://github.com/robertdavidgraham/masscan
cd masscan && make -j && sudo make install
```

## Flags

### Target
| Flag | Descripcion |
|------|-------------|
| `<IP/range>` | IP, rango CIDR o rango con guion |
| `-p <ports>` | Puertos a escanear (ej: `80,443,8000-8100`) |
| `--ports <ports>` | Alias de `-p` |
| `-iL <file>` | Leer targets de archivo |
| `--exclude <IP/range>` | Excluir IPs |
| `--excludefile <file>` | Excluir IPs desde archivo |
| `--top-ports <n>` | Top N puertos mas comunes |

### Rate y Timing
| Flag | Descripcion |
|------|-------------|
| `--rate <pps>` | Packets por segundo (default: 100) |
| `--max-rate <pps>` | Rate maximo |
| `--retries <n>` | Reintentos por puerto (default: 0) |
| `--wait <sec>` | Espera al final del scan (default: 10) |
| `--seed <n>` | Seed para randomizacion de IPs |
| `--ttl <n>` | TTL de paquetes |

### Deteccion
| Flag | Descripcion |
|------|-------------|
| `--banners` | Capturar banners de servicios |
| `--source-ip <IP>` | IP origen |
| `--source-port <port>` | Puerto origen |
| `--adapter <name>` | Interfaz de red |
| `--adapter-ip <IP>` | IP del adaptador |
| `--adapter-mac <MAC>` | MAC del adaptador |
| `--router-mac <MAC>` | MAC del gateway |
| `--ping` | ICMP ping scan |
| `--http-user-agent <ua>` | User-Agent para banner grab HTTP |

### Output
| Flag | Descripcion |
|------|-------------|
| `-oL <file>` | Output formato lista (texto) |
| `-oJ <file>` | Output JSON |
| `-oX <file>` | Output XML (compatible nmap) |
| `-oG <file>` | Output grepable |
| `-oB <file>` | Output binario (para `--resume`) |
| `--output-filename <f>` | Nombre generico de output |
| `--output-format <fmt>` | Formato: xml, binary, grepable, list, json |
| `--append-output` | Append en vez de sobrescribir |
| `--open` | Solo mostrar puertos abiertos |

### Config
| Flag | Descripcion |
|------|-------------|
| `-c <file>` | Archivo de configuracion |
| `--resume <file>` | Resumir scan desde archivo binario |
| `--echo` | Mostrar configuracion actual y salir |
| `-e <iface>` | Interfaz de red |
| `-v` | Verboso |
| `-d <n>` | Debug level |

## Ejemplos Practicos

```bash
# Scan basico de puertos web
sudo masscan -p80,443,8080,8443 10.0.0.0/24 --rate 1000

# Scan rapido con banner grabbing
sudo masscan -p80,443 10.0.0.0/16 --rate 10000 --banners

# Top 100 puertos
sudo masscan --top-ports 100 192.168.1.0/24 --rate 500

# Rango de puertos
sudo masscan -p1-65535 10.0.0.1 --rate 1000

# Output JSON
sudo masscan -p80,443 10.0.0.0/24 --rate 1000 -oJ results.json

# Output XML (compatible nmap)
sudo masscan -p80,443,8080 10.0.0.0/24 --rate 1000 -oX results.xml

# Solo puertos abiertos en lista
sudo masscan -p80,443 10.0.0.0/16 --rate 5000 --open -oL open_hosts.txt

# Targets desde archivo
sudo masscan -p80,443 -iL targets.txt --rate 1000

# Excluir rangos
sudo masscan -p80,443 10.0.0.0/8 --rate 10000 --excludefile exclude.txt

# Resumir scan interrumpido
sudo masscan --resume paused.conf

# Exportar config
sudo masscan -p80,443 10.0.0.0/24 --rate 1000 --echo > scan.conf
# Ejecutar con config
sudo masscan -c scan.conf

# Pipeline: masscan -> nmap (encontrar hosts, luego scan profundo)
sudo masscan -p80,443 10.0.0.0/24 --rate 1000 -oL - | \
  awk '/^open/{print $4}' | sort -u > live_hosts.txt
nmap -sV -sC -iL live_hosts.txt -p80,443

# Puertos web extendidos
sudo masscan -p80,443,8000-8010,8080-8090,8443,9443 10.0.0.0/24 --rate 2000 -oJ web.json
```

## Nota Importante

Masscan requiere **root/sudo** porque usa raw sockets. El rate por defecto (100 pps) es conservador; para redes locales se puede subir a 10000+, pero en targets remotos mantener rates moderados para no causar DoS.
