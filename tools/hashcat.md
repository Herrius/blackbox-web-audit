# Hashcat - Referencia

Cracker de passwords via GPU. El mas rapido para cracking offline. Soporta 350+ tipos de hash con multiples modos de ataque.

## Instalacion

```bash
sudo apt install hashcat        # Debian/Kali
sudo pacman -S hashcat          # Arch
brew install hashcat            # macOS
```

## Flags

### Basicos
| Flag | Descripcion |
|------|-------------|
| `-m MODE` | Tipo de hash (ver tabla abajo) |
| `-a MODE` | Modo de ataque (ver tabla abajo) |
| `-o FILE` | Output de passwords crackeados |
| `--show` | Mostrar hashes ya crackeados |
| `--left` | Mostrar hashes no crackeados |
| `--username` | Ignorar campo username (formato user:hash) |
| `-r FILE` | Archivo de reglas |
| `-j RULE` | Regla para wordlist 1 |
| `-k RULE` | Regla para wordlist 2 |
| `--force` | Ignorar warnings |

### Performance
| Flag | Descripcion |
|------|-------------|
| `-w LEVEL` | Workload: 1=bajo, 2=default, 3=alto, 4=nightmare |
| `-D TYPES` | Device types: 1=CPU, 2=GPU, 3=FPGA |
| `-d DEVICES` | Devices especificos |
| `--opencl-device-types` | Tipos OpenCL |
| `-O` | Optimized kernels (limita password length) |
| `-T THREADS` | Threads por device |
| `--backend-devices` | Devices backend |

### Session
| Flag | Descripcion |
|------|-------------|
| `--session NAME` | Nombre de sesion |
| `--restore` | Restaurar sesion |
| `--restore-file-path` | Path del restore file |
| `-p SEPARATOR` | Separador para output (default: :) |
| `--potfile-path FILE` | Path del potfile |
| `--potfile-disable` | Desactivar potfile |

### Config
| Flag | Descripcion |
|------|-------------|
| `--hex-salt` | Salt en hex |
| `--hex-charset` | Charset en hex |
| `-1 CHARSET` | User-defined charset 1 |
| `-2 CHARSET` | User-defined charset 2 |
| `-3 CHARSET` | User-defined charset 3 |
| `-4 CHARSET` | User-defined charset 4 |
| `--increment` | Incrementar longitud de mask |
| `--increment-min N` | Longitud minima |
| `--increment-max N` | Longitud maxima |
| `--loopback` | Agregar passwords encontrados a la queue |
| `--status` | Habilitar status screen automatico |
| `--status-timer N` | Intervalo de status en segundos |
| `--stdout` | Solo generar candidatos (no crackear) |

## Modos de Ataque (-a)

| Modo | Nombre | Descripcion |
|------|--------|-------------|
| `0` | Straight | Wordlist directa (+ reglas opcionales) |
| `1` | Combination | Combinar dos wordlists |
| `3` | Brute-Force/Mask | Mask attack con charsets |
| `6` | Hybrid Wordlist+Mask | Wordlist + mask al final |
| `7` | Hybrid Mask+Wordlist | Mask + wordlist al final |
| `9` | Association | Wordlist asociada a cada hash |

## Charsets para Mask (-3)

| Charset | Descripcion |
|---------|-------------|
| `?l` | Lowercase (a-z) |
| `?u` | Uppercase (A-Z) |
| `?d` | Digits (0-9) |
| `?s` | Special chars |
| `?a` | All printable (?l?u?d?s) |
| `?b` | Byte (0x00-0xff) |
| `?1-?4` | User-defined |

## Hash Modes Relevantes para Web (-m)

| Mode | Hash |
|------|------|
| `0` | MD5 |
| `100` | SHA1 |
| `900` | MD4 |
| `1000` | NTLM |
| `1400` | SHA256 |
| `1700` | SHA512 |
| `1800` | sha512crypt (Linux) |
| `2500` | WPA/WPA2 |
| `3200` | bcrypt |
| `5600` | NetNTLMv2 |
| `10000` | Django PBKDF2-SHA256 |
| `10900` | PBKDF2-HMAC-SHA256 |
| `11600` | 7-Zip |
| `13100` | Kerberoast TGS-REP (RC4) |
| `16500` | JWT (HMAC-SHA256) |
| `17200` | PKZIP |
| `18200` | Kerberoast AS-REP |
| `22000` | WPA-PBKDF2-PMKID+EAPOL |
| `1000` | NTLM |
| `5500` | NetNTLMv1 |
| `3000` | LM |
| `500` | md5crypt (Apache/Linux) |
| `1500` | descrypt |
| `400` | phpass (WordPress/phpBB) |

## Reglas Incluidas

| Archivo | Descripcion |
|---------|-------------|
| `best64.rule` | Top 64 reglas mas efectivas |
| `rockyou-30000.rule` | 30k reglas derivadas de rockyou |
| `d3ad0ne.rule` | Reglas agresivas |
| `dive.rule` | Muy completa |
| `toggles1-5.rule` | Toggle case en posiciones |
| `OneRuleToRuleThemAll.rule` | La mas completa (descargar aparte) |

## Ejemplos Practicos

```bash
# MD5 con wordlist
hashcat -m 0 -a 0 hashes.txt /usr/share/wordlists/rockyou.txt

# SHA256 con wordlist + reglas
hashcat -m 1400 -a 0 hashes.txt rockyou.txt -r /usr/share/hashcat/rules/best64.rule

# bcrypt con wordlist
hashcat -m 3200 -a 0 hashes.txt rockyou.txt -w 3

# JWT HMAC-SHA256
hashcat -m 16500 -a 0 jwt.txt rockyou.txt

# WordPress (phpass)
hashcat -m 400 -a 0 wp_hashes.txt rockyou.txt

# NTLM
hashcat -m 1000 -a 0 ntlm.txt rockyou.txt -r best64.rule

# Mask attack: 8 chars (lower+digits)
hashcat -m 0 -a 3 hashes.txt ?l?l?l?l?l?d?d?d

# Mask con increment (6-10 chars)
hashcat -m 0 -a 3 hashes.txt ?a?a?a?a?a?a?a?a?a?a --increment --increment-min 6

# Hybrid: wordlist + 4 digits
hashcat -m 0 -a 6 hashes.txt rockyou.txt ?d?d?d?d

# Hybrid: 2 digits + wordlist
hashcat -m 0 -a 7 hashes.txt ?d?d rockyou.txt

# Combination: dos wordlists
hashcat -m 0 -a 1 hashes.txt words1.txt words2.txt

# Mostrar crackeados
hashcat -m 0 hashes.txt --show

# Mostrar no crackeados
hashcat -m 0 hashes.txt --left

# Sesion con nombre (para resume)
hashcat -m 0 -a 0 hashes.txt rockyou.txt --session=webscan

# Restaurar sesion
hashcat --session=webscan --restore

# Kerberoast
hashcat -m 13100 -a 0 tgs_hashes.txt rockyou.txt -r best64.rule

# AS-REP roast
hashcat -m 18200 -a 0 asrep_hashes.txt rockyou.txt

# Benchmark
hashcat -b

# Identificar tipo de hash
hashcat --identify hash.txt

# Output a archivo
hashcat -m 0 -a 0 hashes.txt rockyou.txt -o cracked.txt

# NetNTLMv2
hashcat -m 5600 -a 0 ntlmv2.txt rockyou.txt

# sha512crypt
hashcat -m 1800 -a 0 shadow_hashes.txt rockyou.txt -w 3
```
