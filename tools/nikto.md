# Nikto - Referencia

Scanner de servidor web. Realiza 7,000+ checks de vulnerabilidades, misconfiguraciones, software desactualizado y archivos peligrosos.

## Instalacion

```bash
sudo apt install nikto       # Kali/Debian
git clone https://github.com/sullo/nikto.git  # GitHub
nikto -update                # Actualizar base de datos
```

## Flags

| Flag | Descripcion |
|------|-------------|
| `-h <host>` | Host objetivo (IP, hostname o URL). Requerido |
| `-p <port>` | Puerto(s). Rango: `-p 80-443`, lista: `-p 80,443,8080` |
| `-ssl` | Forzar modo SSL |
| `-nossl` | Deshabilitar SSL |
| `-root <dir>` | Prepend a todas las requests (ej: `-root /app`) |
| `-vhost <name>` | Especificar header Host |
| `-id <user:pass>` | Credenciales HTTP auth |
| `-404code` | Ignorar estos HTTP codes como negativos |
| `-404string` | Ignorar paginas con este string como negativos |
| `-Cgidirs <dirs>` | Dirs CGI a escanear. `all` o `none` o path literal |
| `-Plugins <list>` | Plugins a ejecutar. `ALL`, `NONE`, o lista separada por comas |
| `-Tuning <opts>` | Categorias de pruebas (ver tabla) |
| `-mutate <num>` | Tecnicas de mutacion (ver tabla) |
| `-evasion <num>` | Tecnicas de evasion IDS (ver tabla) |
| `-timeout <sec>` | Timeout por request (default: 10s) |
| `-Pause <sec>` | Delay entre requests |
| `-maxtime <sec>` | Duracion maxima total del scan |
| `-nolookup` | No hacer DNS lookups |
| `-nointeractive` | Sin prompts interactivos |
| `-Display <opts>` | Control de display (ver tabla) |
| `-output <file>` | Escribir resultados a archivo |
| `-Format <fmt>` | Formato: `csv`, `htm`, `nbe`, `sql`, `txt`, `xml` |
| `-useproxy` | Usar proxy definido en config |

## Tuning (-Tuning)

| Valor | Categoria |
|-------|-----------|
| `0` | File Upload |
| `1` | Archivos interesantes / vistos en logs |
| `2` | Misconfiguracion / Archivos por defecto |
| `3` | Information Disclosure |
| `4` | Inyeccion (XSS / Script / HTML) |
| `5` | Remote File Retrieval - dentro de web root |
| `6` | Denial of Service |
| `7` | Remote File Retrieval - server wide |
| `8` | Command Execution / Remote Shell |
| `9` | SQL Injection |
| `a` | Authentication Bypass |
| `b` | Software Identification |
| `c` | Remote Source Inclusion |
| `x` | Reverse tuning (excluir en vez de incluir) |

Se combinan: `-Tuning 1234` ejecuta tipos 1, 2, 3 y 4.
Prefijo `x` para excluir: `-Tuning x6` ejecuta todo excepto DoS.

## Mutate (-mutate)

| Valor | Tecnica |
|-------|---------|
| `1` | Probar todos los archivos con todos los root dirs |
| `2` | Adivinar nombres de archivos de passwords |
| `3` | Enumerar usuarios via Apache (`/~user`) |
| `4` | Enumerar usuarios via cgiwrap |
| `5` | Brute force de subdominios |
| `6` | Adivinar dirs desde diccionario |

## Evasion (-evasion)

| Valor | Tecnica |
|-------|---------|
| `1` | Random URI encoding (non-UTF-8) |
| `2` | Directory self-reference (`/./`) |
| `3` | Premature URL ending |
| `4` | Prepend long random string |
| `5` | Fake parameter |
| `6` | TAB como spacer |
| `7` | Cambiar case de la URL |
| `8` | Windows directory separator (`\`) |
| `A` | Carriage return (0x0d) |
| `B` | Binary value 0x0b |

## Display (-Display)

| Valor | Muestra |
|-------|---------|
| `1` | Redirects |
| `2` | Cookies recibidas |
| `3` | Todas las respuestas 200/OK |
| `4` | URLs que requieren auth |
| `D` | Debug output |
| `E` | Todos los errores HTTP |
| `P` | Progreso a STDOUT |
| `V` | Verbose |

## Ejemplos Practicos

```bash
# Scan basico
nikto -h https://target.com

# Puerto especifico con SSL
nikto -h target.com -p 8443 -ssl

# Multiples puertos
nikto -h target.com -p 80,443,8080,8443

# Solo SQLi y XSS
nikto -h target.com -Tuning 49

# Todo excepto DoS
nikto -h target.com -Tuning x6

# Scan sigiloso con evasion y delay
nikto -h target.com -evasion 1267 -Pause 3

# Reporte HTML
nikto -h target.com -output report.html -Format htm

# Con autenticacion HTTP
nikto -h target.com -id admin:password123

# Via proxy (Caido/Burp)
nikto -h target.com -useproxy http://127.0.0.1:8080

# Enumerar usuarios Apache
nikto -h target.com -mutate 3

# Max 30 minutos
nikto -h target.com -maxtime 1800

# Verbose completo con XML
nikto -h target.com -Display V -output scan.xml -Format xml

# Virtual host
nikto -h 192.168.1.100 -vhost www.target.com -p 443 -ssl

# Automatizado sin interaccion
nikto -h target.com -nointeractive -ask no -output auto.csv -Format csv
```
