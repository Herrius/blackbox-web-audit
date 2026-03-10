# 11 - Caido: Proxy Principal

Caido es el proxy de intercepcion principal para esta metodologia. Escrito en Rust, ofrece rendimiento superior, menor consumo de memoria y una interfaz moderna comparado con alternativas como Burp Suite.

## Por que Caido

- **Rendimiento:** Rust nativo, bajo consumo de RAM, respuesta rapida incluso con grandes volumenes de trafico
- **Costo:** Community edition gratuita cubre la mayoria de casos, Pro significativamente mas barato que Burp Pro
- **UI moderna:** Interfaz limpia, intuitiva, sin la complejidad heredada de herramientas legacy
- **HTTPQL:** Lenguaje de consultas propio para filtrar trafico con precision
- **Workflows visuales:** Automatizacion sin necesidad de codigo
- **Plugins en TypeScript:** Ecosistema de extensiones moderno y facil de desarrollar
- **Guest Mode:** Uso sin cuenta para sesiones rapidas

---

## Instalacion

```bash
# Linux - descarga directa
# https://caido.io/download

# Arch Linux (AUR)
yay -S caido-desktop

# Desde release de GitHub
# https://github.com/caido/caido/releases

# Docker (modo headless/CLI)
docker run --rm -p 8080:8080 caido/caido

# Verificar instalacion
caido --version
```

## Setup Inicial

```
1. Iniciar Caido
   $ caido
   # Abre en http://localhost:8080 (o puerto configurado)

2. Configurar proxy del navegador
   - Proxy: 127.0.0.1:8080 (default)
   - Instalar certificado CA de Caido en el navegador
   - Usar FoxyProxy para alternar proxy facilmente

3. Configurar scope del proyecto
   - Agregar dominios objetivo
   - Excluir dominios fuera de scope (CDNs, analytics, etc)
```

---

## Equivalencia Burp Suite -> Caido

| Burp Suite | Caido | Notas |
|-----------|-------|-------|
| Proxy (Intercept) | **Intercept** | Intercepcion HTTP/WebSocket |
| HTTP History | **HTTP History** | Historial completo de trafico |
| WS History | **WS History** | Trafico WebSocket |
| Target / Site Map | **Sitemap** | Mapa visual de endpoints |
| Repeater | **Replay** | Reenvio y modificacion manual de requests |
| Intruder | **Automate** | Ataques automatizados con payloads |
| Decoder | **Convert Workflows** | Encoding/decoding/transformacion |
| Comparer | **Compare plugin** | Comparacion de requests/responses |
| Scanner | **Scanner plugin** | Escaneo de vulnerabilidades |
| Collaborator | **QuickSSRF / OmniOAST** | Deteccion de interacciones OOB |
| Search | **HTTPQL** | Filtrado avanzado de trafico |
| Organizer | **Findings** | Organizacion de hallazgos |
| Match & Replace | **Match & Replace** | Reemplazo automatico en trafico |
| Filter settings | **Filters** | Filtros de visualizacion |
| Scope | **Scopes** | Definicion de alcance |

---

## HTTPQL - Lenguaje de Consultas

HTTPQL permite filtrar trafico con precision quirurgica.

### Sintaxis Basica

```httpql
# Filtrar por host
req.host.eq:"target.com"

# Filtrar por metodo HTTP
req.method.eq:"POST"

# Filtrar por path
req.path.cont:"/api/"

# Filtrar por status code
resp.code.eq:200
resp.code.gte:400

# Filtrar por content-type de respuesta
resp.headers.cont:"application/json"

# Filtrar por body de request
req.body.cont:"password"

# Filtrar por longitud de respuesta
resp.length.gte:1000

# Combinaciones con AND/OR
req.host.eq:"target.com" AND req.method.eq:"POST"
req.path.cont:"/admin" OR req.path.cont:"/api"

# Negacion
NOT req.host.eq:"fonts.googleapis.com"

# Filtrar por extension (excluir estaticos)
NOT req.path.ext.in:["css","js","png","jpg","gif","svg","woff","woff2"]
```

### Consultas Utiles para Pentesting

```httpql
# Solo trafico al target, sin estaticos
req.host.cont:"target.com" AND NOT req.path.ext.in:["css","js","png","jpg","gif","svg","ico","woff","woff2","ttf"]

# Requests con parametros (posibles puntos de inyeccion)
req.query.cont:"=" AND req.host.cont:"target.com"

# Responses con errores del servidor
resp.code.gte:500 AND req.host.cont:"target.com"

# Buscar tokens o credenciales en responses
resp.body.cont:"token" OR resp.body.cont:"api_key" OR resp.body.cont:"password"

# Solo POST requests (formularios, APIs)
req.method.eq:"POST" AND req.host.cont:"target.com"

# Responses con JSON (APIs)
resp.headers.cont:"application/json"

# Redirects
resp.code.gte:300 AND resp.code.lt:400

# Requests con cookies de sesion
req.headers.cont:"session" OR req.headers.cont:"token"

# Buscar reflections (posible XSS)
resp.body.cont:"<script" OR resp.body.cont:"onerror"
```

---

## Workflows

### Passive Workflows (automaticos)

Se ejecutan automaticamente en background cuando se cumple una condicion.

**Casos de uso:**
- Detectar tokens expuestos en responses
- Marcar endpoints con parametros de inyeccion potencial
- Identificar headers de seguridad ausentes
- Detectar information disclosure
- Flag de CORS misconfiguration

```
Ejemplo: Detectar API keys en responses
Condicion: resp.body matches regex "(api[_-]?key|apikey|secret[_-]?key)\s*[:=]\s*['\"][a-zA-Z0-9]+"
Accion: Crear finding con severidad Medium
```

### Active Workflows (manuales)

Se activan con click derecho sobre un request en HTTP History.

**Casos de uso:**
- Enviar request a herramienta externa
- Transformar y reenviar con payloads
- Generar variaciones de un request para testing
- Ejecutar scripts personalizados contra un endpoint

### Convert Workflows

Transformaciones de datos (equivalente a Decoder/Hackvertor en Burp).

**Transformaciones disponibles:**
- URL encode/decode
- Base64 encode/decode
- HTML encode/decode
- Hex encode/decode
- JSON <-> URL-encoded <-> Multipart <-> XML (conversion de body)
- Hash (MD5, SHA1, SHA256)
- Custom transforms via JavaScript

---

## Automate (equivalente a Intruder)

Ataques automatizados con payloads personalizados.

```
CONFIGURACION DE ATAQUE:

1. Seleccionar request base (desde HTTP History -> Send to Automate)

2. Marcar insertion points (placeholders)
   - Seleccionar texto en el request
   - Marcar como placeholder: §valor§

3. Configurar payloads
   - Lista de palabras (wordlist)
   - Numeros (rango)
   - Custom payloads

4. Configurar Match & Replace (opcional)
   - Aplicar workflows durante el ataque
   - Utl para mantener sesion activa

5. Configurar conexiones
   - Threads/concurrencia
   - Delay entre requests
   - Timeout

6. Ejecutar y analizar resultados
   - Filtrar por status code, longitud, tiempo de respuesta
   - Ordenar para identificar anomalias
```

### Ejemplos de Ataques

```
BRUTE FORCE DE DIRECTORIOS:
- Request: GET /§FUZZ§ HTTP/1.1
- Payload: wordlist (common.txt)
- Filtrar: resp.code != 404

BRUTE FORCE DE LOGIN:
- Request: POST /login con user=admin&pass=§FUZZ§
- Payload: wordlist (passwords)
- Filtrar: resp.length diferente o resp.code != 401

IDOR TESTING:
- Request: GET /api/users/§ID§
- Payload: numeros 1-1000
- Filtrar: resp.code == 200

PARAMETER FUZZING:
- Request: GET /page?§PARAM§=test
- Payload: wordlist (param-names)
- Filtrar: resp.length varía
```

---

## Plugins Esenciales

| Plugin | Funcion | Equivalente Burp |
|--------|---------|-----------------|
| **Scanner** | Escaneo de vulnerabilidades | Scanner |
| **ParamFinder** | Descubrimiento de parametros ocultos | Param Miner |
| **JWT Analyzer** | Analisis y ataque de JWT | JWT Editor |
| **Autorize / Authify** | Testing de autorizacion automatizado | Autorize |
| **403Bypasser** | Bypass de restricciones 403 | 403 Bypasser |
| **GraphQL Analyzer** | Introspection y testing GraphQL | InQL |
| **Data Grep** | Busqueda de datos sensibles en trafico | JS Miner |
| **CSP Auditor** | Analisis de Content-Security-Policy | CSP Auditor |
| **AuthMatrix** | Testing de matriz de autorizacion | AuthMatrix |
| **QuickSSRF** | Deteccion de SSRF via OOB | Collaborator |
| **Squash** | Minimizar requests | Request Minimizer |
| **Notes++** | Notas y documentacion en proyecto | Notes |
| **Shift** | Asistencia AI para analisis | Burp AI |
| **Cerebrum** | Logging avanzado | Logger++ |

### Instalacion de Plugins

```
En Caido:
1. Ir a Plugins (sidebar)
2. Buscar plugin por nombre
3. Click Install
4. Configurar segun necesidad
```

---

## Flujo de Trabajo Completo con Caido

```
1. SETUP
   - Crear nuevo proyecto para el engagement
   - Configurar scope con dominios objetivo
   - Instalar plugins necesarios
   - Configurar passive workflows

2. RECONOCIMIENTO INTERACTIVO
   - Navegar la aplicacion manualmente con proxy activo
   - Revisar Sitemap generado automaticamente
   - Analizar HTTP History con HTTPQL
   - Identificar endpoints, parametros, tecnologias

3. ANALISIS DE TRAFICO
   - Filtrar con HTTPQL para aislar trafico relevante
   - Revisar cookies, headers, tokens
   - Identificar patrones de autenticacion/sesion
   - Buscar datos sensibles en responses (Data Grep)

4. TESTING MANUAL
   - Replay: reenviar requests modificados
   - Probar inyecciones en parametros
   - Verificar controles de acceso (Autorize/AuthMatrix)
   - Analizar JWT (JWT Analyzer)
   - Probar SSRF con QuickSSRF

5. TESTING AUTOMATIZADO
   - Automate: fuzzing de parametros
   - Automate: brute force de directorios
   - Scanner: escaneo de vulnerabilidades
   - Active Workflows para testing custom

6. DOCUMENTACION
   - Findings: registrar cada hallazgo
   - Notes++: documentar observaciones
   - Exportar evidencia (requests/responses)
```

---

## Tips y Atajos

```
ATAJOS UTILES:
- Ctrl+Shift+I: Toggle intercept
- Click derecho -> Send to Replay: Enviar a replay
- Click derecho -> Send to Automate: Enviar a automate

PERFORMANCE:
- Usar scopes agresivos para reducir ruido
- Excluir dominios de CDN/analytics
- Filtrar extensiones estaticas por defecto

EXPORTACION:
- Exportar requests como cURL
- Exportar proyecto completo
- Compartir workflows con el equipo
```
