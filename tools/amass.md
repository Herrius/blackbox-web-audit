# Amass (OWASP) - Referencia

Enumeracion de subdominios y mapeo de superficie de ataque. Soporta modo pasivo (OSINT) y activo (DNS queries directas).

## Instalacion

```bash
sudo apt install amass                                          # Kali/Debian
go install -v github.com/owasp-amass/amass/v4/...@master       # Go
sudo snap install amass                                         # Snap
docker pull caffix/amass                                        # Docker
```

## Subcomandos

| Subcomando | Uso |
|------------|-----|
| `enum` | Enumeracion DNS y mapeo de red (principal) |
| `intel` | Descubrir dominios raiz de una organizacion |
| `viz` | Visualizar datos (D3, Gephi, Maltego) |
| `track` | Comparar enumeraciones para rastrear cambios |
| `db` | Ver y manipular la base de datos |

## Flags de enum

### Modo

| Flag | Descripcion |
|------|-------------|
| `-passive` | Solo OSINT, sin queries DNS directas al target |
| `-active` | DNS resolution, zone transfers, SSL certs, web crawling |

### Target

| Flag | Descripcion |
|------|-------------|
| `-d` | Dominio(s) objetivo (separados por coma) |
| `-df` | Archivo con lista de dominios |
| `-asn` | ASN(s) a incluir |
| `-cidr` | Rango(s) CIDR a incluir |
| `-ip` | Mostrar IPs de nombres descubiertos |
| `-ipv4` | Solo IPv4 |
| `-ipv6` | Solo IPv6 |

### Brute Force

| Flag | Descripcion |
|------|-------------|
| `-brute` | Habilitar brute force de subdominios |
| `-w` | Wordlist para brute force |
| `-recursive` | Brute force recursivo en subdominios descubiertos |
| `-min-for-recursive` | Minimo de subdominios antes de recursion (default: 1) |

### DNS

| Flag | Descripcion |
|------|-------------|
| `-rf` | Archivo de DNS resolvers |
| `-rqps` | Max queries DNS/segundo por resolver |
| `-trqps` | Max queries DNS/segundo por trusted resolver |
| `-nf` | Archivo de dominios a excluir (blacklist) |
| `-include-unresolvable` | Incluir nombres sin resolver en output |

### Output

| Flag | Descripcion |
|------|-------------|
| `-o` | Archivo de salida (texto) |
| `-json` | Output en JSON lines |
| `-dir` | Directorio para graph database (habilita tracking) |
| `-config` | Ruta a config.yaml |
| `-log` | Ruta a archivo de log |
| `-list` | Listar todas las fuentes de datos disponibles |
| `-src` | Mostrar fuente de datos de cada resultado |
| `-timeout` | Minutos antes de timeout (default: 0 = sin limite) |

## Pasivo vs Activo

| Aspecto | Pasivo | Activo |
|---------|--------|--------|
| Velocidad | Rapido | Mas lento |
| Sigilo | Sin contacto con target | Queries directas al target |
| DNS Resolution | No | Si |
| Zone Transfers | No | Intenta |
| SSL/TLS Certs | No | Extrae certs del target |
| Brute Force | No (aunque se ponga -brute) | Si (con -brute) |
| Precision | Puede incluir subdominios muertos | Resultados verificados |

## Config de API Keys

`~/.config/amass/datasources.yaml`:

```yaml
datasources:
  - name: Shodan
    ttl: 10080
    creds:
      account:
        apikey: "TU_SHODAN_KEY"
  - name: SecurityTrails
    creds:
      account:
        apikey: "TU_ST_KEY"
  - name: VirusTotal
    creds:
      account:
        apikey: "TU_VT_KEY"
  - name: Censys
    creds:
      account:
        apikey: "TU_CENSYS_API_ID"
        secret: "TU_CENSYS_SECRET"
  - name: PassiveTotal
    creds:
      account:
        username: "tu@email.com"
        apikey: "TU_PT_KEY"
  - name: IntelX
    creds:
      account:
        apikey: "TU_INTELX_KEY"
```

## Ejemplos Practicos

```bash
# Pasivo (OSINT puro)
amass enum -passive -d target.com -o pasivo.txt

# Activo con IPs
amass enum -active -d target.com -ip -o activo.txt

# Activo + brute force con wordlist y resolvers
amass enum -active -brute -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt \
  -rf resolvers.txt -d target.com -o brute.txt

# Brute force recursivo
amass enum -active -brute -recursive -d target.com -o recursivo.txt

# Con config y API keys
amass enum -active -d target.com -config ~/.config/amass/config.yaml \
  -dir ~/amass_data -o resultados.txt

# Mostrar fuente de cada resultado
amass enum -passive -d target.com -src

# JSON output
amass enum -active -d target.com -json resultados.json

# Multiples dominios desde archivo
amass enum -passive -df dominios.txt -o todos.txt

# Intel: descubrir dominios de una organizacion
amass intel -org "Target Corp" -whois

# Intel: reverse WHOIS desde dominio conocido
amass intel -d target.com -whois

# Intel: investigar un ASN
amass intel -asn 12345

# Rastrear cambios entre enumeraciones
amass track -d target.com -dir ~/amass_data

# Visualizar resultados (HTML D3)
amass viz -d3 -d target.com -dir ~/amass_data

# Listar fuentes disponibles
amass enum -list

# Con timeout de 60 minutos
amass enum -active -d target.com -timeout 60

# Enumerar ASN y CIDR especificos
amass enum -active -d target.com -asn 12345 -cidr 192.168.0.0/16

# Incluir nombres sin resolver (analisis historico)
amass enum -passive -d target.com -include-unresolvable -o todos.txt
```
