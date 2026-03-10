# CeWL - Referencia

Custom Word List generator. Spider un sitio web y genera wordlists custom basadas en el contenido encontrado. Ideal para brute force con passwords especificas del target.

## Instalacion

```bash
sudo apt install cewl           # Debian/Kali
gem install cewl                 # Ruby gem
```

## Flags

| Flag | Descripcion |
|------|-------------|
| `-d DEPTH` | Profundidad de spider (default: 2) |
| `-m MIN` | Longitud minima de palabra (default: 3) |
| `-x MAX` | Longitud maxima de palabra |
| `-w FILE` | Output wordlist a archivo |
| `-o` | Permitir URLs externas |
| `-n` | No incluir palabras del sitio |
| `-a` | Incluir metadata de documentos |
| `-e` | Incluir emails encontrados |
| `--email_file FILE` | Guardar emails en archivo separado |
| `-c` | Mostrar conteo de cada palabra |
| `--lowercase` | Convertir todo a lowercase |
| `-u USER_AGENT` | User-Agent custom |
| `--auth_type TYPE` | Tipo de auth: basic, digest, ntlm |
| `--auth_user USER` | Usuario para auth |
| `--auth_pass PASS` | Password para auth |
| `-H HEADER` | Header custom (repetible) |
| `--proxy_host HOST` | Proxy host |
| `--proxy_port PORT` | Proxy port |
| `--proxy_username USER` | Proxy user |
| `--proxy_password PASS` | Proxy password |
| `-v` | Verboso |
| `--debug` | Debug mode |
| `--with-numbers` | Incluir palabras con numeros |
| `--convert-umlauts` | Convertir umlauts a ASCII |
| `--meta_file FILE` | Output metadata a archivo |
| `--offsite` | Seguir links offsite |

## Ejemplos Practicos

```bash
# Wordlist basica
cewl https://target.com -w wordlist.txt

# Profundidad alta + palabras largas
cewl https://target.com -d 5 -m 6 -w wordlist.txt

# Con numeros (incluir "admin123", "pass2024", etc)
cewl https://target.com --with-numbers -w wordlist.txt

# Longitud minima y maxima
cewl https://target.com -m 5 -x 15 -w wordlist.txt

# Extraer emails
cewl https://target.com -e --email_file emails.txt -w wordlist.txt

# Con metadata de documentos (PDF, DOC, etc)
cewl https://target.com -a --meta_file metadata.txt -w wordlist.txt

# Todo lowercase
cewl https://target.com --lowercase -w wordlist.txt

# Con conteo de palabras
cewl https://target.com -c -w wordlist.txt

# Con autenticacion basica
cewl https://target.com --auth_type basic --auth_user admin --auth_pass password -w wordlist.txt

# Via proxy
cewl https://target.com --proxy_host 127.0.0.1 --proxy_port 8080 -w wordlist.txt

# Header custom
cewl https://target.com -H "Authorization: Bearer eyJ..." -w wordlist.txt

# Verboso
cewl https://target.com -d 3 -v -w wordlist.txt

# Generar wordlist para brute force de password
cewl https://target.com -d 4 -m 5 --with-numbers --lowercase -w custom_passwords.txt

# Pipeline: generar wordlist y usar con hydra
cewl https://target.com --with-numbers -w cewl_words.txt
hydra -l admin -P cewl_words.txt target.com http-post-form \
  "/login:user=^USER^&pass=^PASS^:F=failed"

# Pipeline: generar wordlist y usar con ffuf
cewl https://target.com -m 4 -w dirs.txt
ffuf -u https://target.com/FUZZ -w dirs.txt

# Combinar con rockyou
cewl https://target.com --with-numbers --lowercase -w cewl.txt
cat cewl.txt /usr/share/wordlists/rockyou.txt | sort -u > combined.txt
```
