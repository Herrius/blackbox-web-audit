# Wafw00f - Referencia

Deteccion y fingerprinting de WAFs (Web Application Firewalls). Identifica 100+ productos WAF.

## Instalacion

```bash
sudo apt install wafw00f       # Kali/Debian
pip install wafw00f            # pip
```

## Como Funciona (3 etapas)

1. Envia request HTTP normal y analiza headers/body buscando firmas de WAF
2. Si falla, envia requests potencialmente maliciosas y usa logica para identificar el WAF
3. Si falla, analiza todas las respuestas previas con algoritmo adicional para adivinar

## Flags

| Flag | Descripcion |
|------|-------------|
| `<URL>` | URL objetivo |
| `-v, --verbose` | Verbose. Multiple `-v` para mas detalle (`-vv`, `-vvv`) |
| `-a, --findall` | Encontrar TODOS los WAFs, no parar en el primero |
| `-r, --noredirect` | No seguir redirects HTTP 3xx |
| `-t TEST, --test TEST` | Probar solo un WAF especifico |
| `-l, --list` | Listar todos los WAFs detectables |
| `-p PROXY, --proxy PROXY` | Proxy HTTP (`http://127.0.0.1:8080`, `socks5://127.0.0.1:9050`) |
| `-o OUTPUT, --output OUTPUT` | Escribir a archivo. Formato por extension (`.csv`, `.json`, `.txt`) |
| `-f FORMAT, --format FORMAT` | Forzar formato: `csv`, `json`, `text` |
| `-i INPUT, --input-file INPUT` | Leer targets de archivo |
| `-H HEADERS` | Headers HTTP custom (`-H "Cookie:foo=bar"`) |

## WAFs Detectables (parcial)

**Cloud/CDN:** Cloudflare, AWS WAF, Akamai, Azure Front Door, Google Cloud Armor, Fastly, Sucuri, Imperva/Incapsula, StackPath, Vercel

**Comerciales:** F5 BIG-IP ASM, Fortinet FortiWeb, Barracuda, Citrix NetScaler, Radware, Palo Alto, Check Point, Wallarm, Trustwave ModSecurity

**Open-Source:** ModSecurity, NAXSI, Shadow Daemon, OpenWAF

**WordPress:** Wordfence, WP Cerber, WebARX

Ejecutar `wafw00f -l` para lista completa.

## Ejemplos Practicos

```bash
# Deteccion basica
wafw00f https://target.com

# Verbose
wafw00f -v https://target.com

# Encontrar TODOS los WAFs
wafw00f -a https://target.com

# Probar solo WAF especifico
wafw00f -t Cloudflare https://target.com

# Via proxy
wafw00f -p http://127.0.0.1:8080 https://target.com

# Via TOR
wafw00f -p socks5://127.0.0.1:9050 https://target.com

# Sin seguir redirects
wafw00f -r https://target.com

# Guardar JSON
wafw00f -o results.json https://target.com

# Guardar CSV
wafw00f -o results.csv https://target.com

# Multiples targets desde archivo
wafw00f -i targets.txt

# Con headers custom
wafw00f -H "Cookie:session=abc123" https://target.com

# Listar todos los WAFs detectables
wafw00f -l

# Maxima verbosidad + findall
wafw00f -vvv -a https://target.com

# Completo: verbose, findall, JSON, proxy
wafw00f -v -a -p http://127.0.0.1:8080 -o full_scan.json https://target.com

# Batch scan
wafw00f -a -i targets.txt -o batch.json
```
