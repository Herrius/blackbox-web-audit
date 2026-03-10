# John the Ripper - Referencia

Cracker de passwords CPU-based. Versatil con auto-deteccion de formatos, reglas potentes y utilidades *2john para extraer hashes de cualquier formato.

## Instalacion

```bash
sudo apt install john           # Debian/Kali (version community)
sudo pacman -S john             # Arch
brew install john               # macOS
# Jumbo (version completa)
git clone https://github.com/openwall/john -b bleeding-jumbo
cd john/src && ./configure && make -s clean && make -sj4
```

## Flags

### Basicos
| Flag | Descripcion |
|------|-------------|
| `hashfile` | Archivo con hashes (ultimo argumento) |
| `--wordlist=FILE` | Modo wordlist |
| `--rules=RULESET` | Aplicar reglas (ej: best64, jumbo, all) |
| `--format=FORMAT` | Forzar formato de hash |
| `--show` | Mostrar passwords crackeados |
| `--show=left` | Mostrar hashes no crackeados |

### Modos de Ataque
| Flag | Descripcion |
|------|-------------|
| `--single` | Single crack mode (info del username) |
| `--wordlist=FILE` | Wordlist mode |
| `--incremental[=MODE]` | Brute force incremental |
| `--mask=MASK` | Mask attack |
| `--prince=FILE` | PRINCE attack (combinar palabras) |
| `--external=FILTER` | External mode con filtro custom |
| `--markov[=OPTIONS]` | Markov chain mode |

### Config
| Flag | Descripcion |
|------|-------------|
| `--fork=N` | Paralelizar en N procesos |
| `--node=N/M` | Distribuir trabajo (nodo N de M) |
| `--session=NAME` | Nombre de sesion |
| `--restore=NAME` | Restaurar sesion |
| `--pot=FILE` | Archivo potfile custom |
| `--list=formats` | Listar formatos soportados |
| `--list=format-details` | Detalles de formatos |
| `--test` | Benchmark |

### Output
| Flag | Descripcion |
|------|-------------|
| `--show` | Mostrar crackeados |
| `--show=left` | Mostrar no crackeados |
| `--log-stderr` | Log a stderr |

### Mask Charsets
| Charset | Descripcion |
|---------|-------------|
| `?l` | Lowercase |
| `?u` | Uppercase |
| `?d` | Digits |
| `?s` | Special |
| `?a` | All (l+u+d+s) |
| `?L` | Lowercase (unicode) |
| `?U` | Uppercase (unicode) |

## Utilidades *2john

Extraen hashes de archivos protegidos para cracking:

| Utilidad | Formato |
|----------|---------|
| `zip2john` | Archivos ZIP |
| `rar2john` | Archivos RAR |
| `pdf2john` | PDFs protegidos |
| `ssh2john` | Claves SSH privadas |
| `keepass2john` | Bases KeePass |
| `office2john` | Documentos Office |
| `gpg2john` | Claves GPG |
| `pfx2john` | Certificados PFX/P12 |
| `7z2john` | Archivos 7-Zip |
| `bitlocker2john` | BitLocker volumes |
| `wpa2john` | Captures WPA/WPA2 |
| `ethereum2john` | Wallets Ethereum |
| `hccapx2john` | hccapx files |
| `vncpcap2john` | VNC pcap captures |
| `kirbi2john` | Kerberos tickets |
| `dmg2john` | DMG images (macOS) |

## Formatos Web Relevantes

| Formato | Uso |
|---------|-----|
| `raw-md5` | MD5 puro |
| `raw-sha1` | SHA1 puro |
| `raw-sha256` | SHA256 puro |
| `raw-sha512` | SHA512 puro |
| `bcrypt` | bcrypt |
| `phpass` | WordPress/phpBB |
| `django` | Django hashes |
| `NT` | NTLM |
| `netntlmv2` | NetNTLMv2 |
| `sha512crypt` | Linux shadow ($6$) |
| `sha256crypt` | Linux shadow ($5$) |
| `md5crypt` | Linux/Apache ($1$) |
| `descrypt` | Traditional DES |
| `krb5tgs` | Kerberoast |
| `krb5asrep` | AS-REP roast |
| `HMAC-SHA256` | JWT |

## Ejemplos Practicos

```bash
# Auto-detectar formato y crackear
john hashes.txt

# Wordlist mode
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt

# Wordlist + reglas
john --wordlist=rockyou.txt --rules=best64 hashes.txt

# Forzar formato
john --format=raw-md5 --wordlist=rockyou.txt hashes.txt

# bcrypt
john --format=bcrypt --wordlist=rockyou.txt hashes.txt

# WordPress (phpass)
john --format=phpass --wordlist=rockyou.txt wp_hashes.txt

# Single crack mode
john --single hashes.txt

# Incremental (brute force)
john --incremental hashes.txt

# Mask attack: 8 chars lower+digits
john --mask='?l?l?l?l?l?d?d?d' hashes.txt

# Mask con formato especifico
john --format=raw-md5 --mask='?a?a?a?a?a?a' hashes.txt

# Mostrar crackeados
john --show hashes.txt

# Mostrar crackeados con formato
john --show --format=raw-md5 hashes.txt

# Listar formatos disponibles
john --list=formats

# Buscar formato especifico
john --list=formats | grep -i bcrypt

# Benchmark
john --test

# Paralelizar
john --fork=4 --wordlist=rockyou.txt hashes.txt

# Session management
john --session=webscan --wordlist=rockyou.txt hashes.txt
john --restore=webscan

# === UTILIDADES *2john ===

# ZIP protegido
zip2john protected.zip > zip_hash.txt
john --wordlist=rockyou.txt zip_hash.txt

# PDF protegido
pdf2john protected.pdf > pdf_hash.txt
john --wordlist=rockyou.txt pdf_hash.txt

# Clave SSH
ssh2john id_rsa > ssh_hash.txt
john --wordlist=rockyou.txt ssh_hash.txt

# KeePass
keepass2john database.kdbx > keepass_hash.txt
john --wordlist=rockyou.txt keepass_hash.txt

# Documento Office
office2john documento.docx > office_hash.txt
john --wordlist=rockyou.txt office_hash.txt

# Kerberos TGS
kirbi2john ticket.kirbi > tgs_hash.txt
john --format=krb5tgs --wordlist=rockyou.txt tgs_hash.txt

# 7-Zip
7z2john archive.7z > 7z_hash.txt
john --wordlist=rockyou.txt 7z_hash.txt

# Multiples reglas agresivas
john --wordlist=rockyou.txt --rules=jumbo hashes.txt
```
