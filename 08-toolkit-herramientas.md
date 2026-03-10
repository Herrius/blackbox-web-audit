# 08 - Toolkit: Herramientas

## Arsenal Organizado por Fase

### Reconocimiento Pasivo

| Herramienta | Uso | Instalacion |
|-------------|-----|-------------|
| **Subfinder** | Enumeracion pasiva de subdominios | `go install github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest` |
| **theHarvester** | Emails, subdominios, IPs desde fuentes publicas | `pip install theHarvester` |
| **Amass** | Enumeracion de subdominios (pasivo/activo) | `go install github.com/owasp-amass/amass/v4/...@master` |
| **Shodan CLI** | Busqueda en Shodan | `pip install shodan` |
| **waybackurls** | URLs historicas de Wayback Machine | `go install github.com/tomnomnom/waybackurls@latest` |
| **gau** | URLs de AlienVault, Wayback, CommonCrawl | `go install github.com/lc/gau/v2/cmd/gau@latest` |
| **crt.sh** | Certificate Transparency logs | Web: crt.sh / curl API |

### Reconocimiento Activo

| Herramienta | Uso | Instalacion |
|-------------|-----|-------------|
| **Nmap** | Escaneo de puertos y servicios | `apt install nmap` |
| **Masscan** | Escaneo rapido de puertos | `apt install masscan` |
| **whatweb** | Fingerprinting de tecnologias web | `apt install whatweb` |
| **wafw00f** | Deteccion de WAF | `pip install wafw00f` |
| **httpx** | Verificacion de hosts HTTP activos | `go install github.com/projectdiscovery/httpx/cmd/httpx@latest` |

### Enumeracion Web

| Herramienta | Uso | Instalacion |
|-------------|-----|-------------|
| **Feroxbuster** | Fuerza bruta de directorios (rapido, recursivo) | `apt install feroxbuster` o release de GitHub |
| **Gobuster** | Fuerza bruta de directorios y DNS | `go install github.com/OJ/gobuster/v3@latest` |
| **ffuf** | Fuzzing web (directorios, parametros, vhosts) | `go install github.com/ffuf/ffuf/v2@latest` |
| **Katana** | Web crawler avanzado | `go install github.com/projectdiscovery/katana/cmd/katana@latest` |
| **gospider** | Spider web rapido | `go install github.com/jaeles-project/gospider@latest` |
| **Arjun** | Descubrimiento de parametros HTTP | `pip install arjun` |
| **ParamSpider** | Minado de parametros desde archivos web | `pip install paramspider` |
| **Kiterunner** | Descubrimiento de endpoints API | GitHub releases |

### Escaneo de Vulnerabilidades

| Herramienta | Uso | Instalacion |
|-------------|-----|-------------|
| **Nuclei** | Scanner basado en templates (CVEs, misconfig, etc) | `go install github.com/projectdiscovery/nuclei/v3/cmd/nuclei@latest` |
| **Nikto** | Scanner de servidor web | `apt install nikto` |
| **Wapiti** | Scanner de vulnerabilidades web | `pip install wapiti3` |
| **OWASP ZAP** | Proxy + scanner web (GUI/CLI) | `apt install zaproxy` |
| **WPScan** | Scanner especifico para WordPress | `gem install wpscan` |
| **JoomScan** | Scanner especifico para Joomla | GitHub: OWASP/joomscan |
| **Droopescan** | Scanner multi-CMS (Drupal, WordPress, etc) | `pip install droopescan` |

### Proxy y Analisis de Trafico

| Herramienta | Uso | Instalacion |
|-------------|-----|-------------|
| **Caido** (principal) | Proxy interceptor moderno en Rust, HTTPQL, Workflows, Automate | caido.io / `yay -S caido-desktop` |
| **OWASP ZAP** | Alternativa open source | `apt install zaproxy` |
| **mitmproxy** | Proxy interceptor CLI | `pip install mitmproxy` |
| **Burp Suite** | Alternativa legacy (Community/Pro) | portswigger.net |

> Ver [11-caido-proxy.md](11-caido-proxy.md) para documentacion completa de Caido: setup, HTTPQL, Workflows, plugins y equivalencia con Burp Suite.

### Explotacion Especifica

| Herramienta | Uso | Instalacion |
|-------------|-----|-------------|
| **SQLMap** | Explotacion automatizada de SQL injection | `apt install sqlmap` |
| **XSStrike** | Deteccion y explotacion de XSS | `pip install xsstrike` |
| **Dalfox** | Scanner XSS rapido | `go install github.com/hahwul/dalfox/v2@latest` |
| **Commix** | Explotacion de command injection | `apt install commix` |
| **tplmap** | Server-Side Template Injection | GitHub: epinna/tplmap |
| **jwt_tool** | Ataques contra JWT | GitHub: ticarpi/jwt_tool |
| **SSRFmap** | Explotacion de SSRF | GitHub: swisskyrepo/SSRFmap |
| **Metasploit** | Framework de explotacion general | `apt install metasploit-framework` |
| **BeEF** | Browser Exploitation Framework | GitHub: beefproject/beef |

### Fuerza Bruta y Credenciales

| Herramienta | Uso | Instalacion |
|-------------|-----|-------------|
| **Hydra** | Brute force de protocolos (HTTP, SSH, FTP, etc) | `apt install hydra` |
| **Patator** | Brute force multi-proposito | GitHub: lanjelot/patator |
| **CeWL** | Generacion de wordlists desde sitio web | `apt install cewl` |
| **Hashcat** | Cracking de hashes (GPU) | `apt install hashcat` |
| **John the Ripper** | Cracking de hashes (CPU) | `apt install john` |

### Analisis SSL/TLS

| Herramienta | Uso | Instalacion |
|-------------|-----|-------------|
| **testssl.sh** | Analisis completo de SSL/TLS | GitHub: testssl/testssl.sh |
| **sslyze** | Scanner SSL/TLS | `pip install sslyze` |
| **sslscan** | Enumeracion de ciphers SSL | `apt install sslscan` |

### Utilidades

| Herramienta | Uso | Instalacion |
|-------------|-----|-------------|
| **SecLists** | Coleccion de wordlists para pentesting | GitHub: danielmiessler/SecLists |
| **PayloadsAllTheThings** | Payloads para cada tipo de vulnerabilidad | GitHub: swisskyrepo/PayloadsAllTheThings |
| **CyberChef** | Encoding/decoding/analisis multi-tool | Web: gchq.github.io/CyberChef |
| **Retire.js** | Deteccion de librerias JS vulnerables | `npm install -g retire` |
| **LinkFinder** | Extraer endpoints de archivos JS | GitHub: GerbenJav);do/LinkFinder |
| **truffleHog** | Buscar secretos en repos Git | `pip install trufflehog` |

---

## Configuracion de Caido (Proxy Principal)

> Documentacion detallada en [11-caido-proxy.md](11-caido-proxy.md)

### Setup Rapido para Caja Negra

```
1. PROXY
   - Iniciar Caido: $ caido
   - Configurar navegador con proxy 127.0.0.1:8080
   - Instalar certificado CA de Caido en el navegador
   - Usar FoxyProxy para alternar facilmente

2. SCOPE
   - Agregar dominios objetivo al scope
   - Excluir dominios fuera de scope (CDN, analytics)

3. PASSIVE WORKFLOWS
   - Activar deteccion de tokens/secrets en responses
   - Activar deteccion de headers de seguridad ausentes
   - Configurar alertas de information disclosure

4. PLUGINS ESENCIALES
   - Scanner (escaneo de vulnerabilidades)
   - ParamFinder (parametros ocultos)
   - JWT Analyzer (tokens JWT)
   - Autorize/Authify (testing de autorizacion)
   - 403Bypasser (bypass de restricciones)
   - GraphQL Analyzer (introspection)
   - QuickSSRF (deteccion OOB)
   - Data Grep (datos sensibles en trafico)

5. HTTPQL FILTERS INICIALES
   - Excluir estaticos: NOT req.path.ext.in:["css","js","png","jpg","gif","svg","ico","woff"]
   - Solo target: req.host.cont:"target.com"
```

---

## Wordlists Esenciales (SecLists)

```
DIRECTORIOS:
  Discovery/Web-Content/common.txt (4,700 entries)
  Discovery/Web-Content/directory-list-2.3-medium.txt (220,000 entries)
  Discovery/Web-Content/raft-large-directories.txt (62,000 entries)
  Discovery/Web-Content/big.txt (20,000 entries)

ARCHIVOS:
  Discovery/Web-Content/raft-large-files.txt
  Discovery/Web-Content/common-and-critical.txt

APIs:
  Discovery/Web-Content/api/api-endpoints.txt
  Discovery/Web-Content/api/objects.txt

DNS/SUBDOMINIOS:
  Discovery/DNS/subdomains-top1million-5000.txt
  Discovery/DNS/subdomains-top1million-110000.txt

PASSWORDS:
  Passwords/Common-Credentials/10k-most-common.txt
  Passwords/Common-Credentials/best1050.txt
  Passwords/Leaked-Databases/rockyou.txt

USERNAMES:
  Usernames/top-usernames-shortlist.txt
  Usernames/Names/names.txt

FUZZING:
  Fuzzing/special-chars.txt
  Fuzzing/Unicode.txt
  Fuzzing/command-injection-commix.txt
  Fuzzing/SQLi/
  Fuzzing/XSS/
```

---

## Instalacion Rapida del Toolkit

### En Kali Linux / Parrot OS

La mayoria de herramientas vienen preinstaladas. Para las faltantes:

```bash
# Herramientas Go (requiere Go instalado)
go install github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest
go install github.com/projectdiscovery/httpx/cmd/httpx@latest
go install github.com/projectdiscovery/nuclei/v3/cmd/nuclei@latest
go install github.com/projectdiscovery/katana/cmd/katana@latest
go install github.com/ffuf/ffuf/v2@latest
go install github.com/OJ/gobuster/v3@latest
go install github.com/tomnomnom/waybackurls@latest
go install github.com/lc/gau/v2/cmd/gau@latest
go install github.com/hahwul/dalfox/v2@latest
go install github.com/jaeles-project/gospider@latest

# Herramientas Python
pip install theHarvester wapiti3 arjun paramspider droopescan sslyze wafw00f

# SecLists
git clone --depth 1 https://github.com/danielmiessler/SecLists.git /opt/SecLists

# testssl.sh
git clone --depth 1 https://github.com/drwetter/testssl.sh.git /opt/testssl.sh
```

### En Arch Linux

```bash
# Desde repositorios oficiales y AUR
sudo pacman -S nmap masscan nikto sqlmap hydra john hashcat
yay -S burpsuite feroxbuster gobuster nuclei-bin subfinder-bin httpx-bin
```
