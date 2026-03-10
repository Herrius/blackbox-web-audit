# Waybackurls - Referencia

Herramienta simple en Go para extraer todas las URLs conocidas de un dominio desde la Wayback Machine (web.archive.org).

## Instalacion

```bash
go install github.com/tomnomnom/waybackurls@latest
```

## Flags

| Flag | Descripcion |
|------|-------------|
| `-dates` | Incluir fecha de captura |
| `-get-versions` | Obtener todas las versiones de cada URL |
| `-no-subs` | Solo dominio exacto (sin subdominios) |

## Uso Basico

```
echo domain.com | waybackurls
# o
waybackurls domain.com
```

## Ejemplos Practicos

```bash
# URLs de un dominio
echo target.com | waybackurls

# Sin subdominios
echo target.com | waybackurls -no-subs

# Con fechas
echo target.com | waybackurls -dates

# Guardar a archivo
echo target.com | waybackurls > urls.txt

# Multiples dominios
cat domains.txt | waybackurls > all_urls.txt

# Filtrar URLs con parametros (potenciales vulns)
echo target.com | waybackurls | grep "=" | sort -u

# Filtrar extensiones interesantes
echo target.com | waybackurls | grep -E "\.(php|asp|aspx|jsp|json|xml|config|env|bak|old|sql|log)" | sort -u

# Buscar archivos JS
echo target.com | waybackurls | grep "\.js$" | sort -u

# Buscar endpoints API
echo target.com | waybackurls | grep -i "/api/" | sort -u

# Pipeline con httpx (verificar cuales estan vivos)
echo target.com | waybackurls | httpx -silent -mc 200

# Pipeline con ffuf (fuzz params encontrados)
echo target.com | waybackurls | grep "=" | \
  uro | sort -u | qsreplace "FUZZ" | \
  xargs -I{} ffuf -u {} -w payloads.txt -mc 200

# Pipeline con gf (buscar patrones de vulns)
echo target.com | waybackurls | gf sqli
echo target.com | waybackurls | gf xss
echo target.com | waybackurls | gf ssrf

# Pipeline completo de recon
echo target.com | waybackurls | sort -u | httpx -silent | nuclei -t cves/
```
