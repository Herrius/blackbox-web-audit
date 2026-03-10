# OWASP ZAP - Referencia CLI

Zed Attack Proxy. Scanner de seguridad web open source. Esta referencia cubre exclusivamente uso via CLI, API, Docker y Automation Framework.

## Instalacion

```bash
# Paquete
sudo apt install zaproxy         # Debian/Kali
sudo snap install zaproxy        # Snap
brew install --cask zap          # macOS

# Docker (recomendado para CLI)
docker pull ghcr.io/zaproxy/zaproxy:stable
docker pull ghcr.io/zaproxy/zaproxy:weekly    # Ultima version

# Desde source/release
# Descargar de https://www.zaproxy.org/download/
```

## Daemon Mode

Ejecutar ZAP sin GUI como servicio headless:

```bash
# Daemon basico
zap.sh -daemon -port 8090 -config api.key=your-api-key

# Sin API key (solo local, para testing)
zap.sh -daemon -port 8090 -config api.disablekey=true

# Con host y puerto custom
zap.sh -daemon -host 0.0.0.0 -port 8090 -config api.key=mykey

# Silent (sin output de consola)
zap.sh -daemon -port 8090 -config api.key=mykey -silent

# Con add-ons actualizados
zap.sh -daemon -port 8090 -addonupdate -config api.key=mykey

# Instalar add-on especifico
zap.sh -daemon -port 8090 -addoninstall ascanrules -config api.key=mykey

# Con directorio home custom
zap.sh -daemon -port 8090 -dir /path/to/zap/home

# Con certificado raiz custom
zap.sh -daemon -port 8090 -certload /path/to/cert.pem -certpubdump /tmp/zap_ca.pem
```

### Flags del Daemon
| Flag | Descripcion |
|------|-------------|
| `-daemon` | Modo headless |
| `-port PORT` | Puerto del proxy (default: 8080) |
| `-host HOST` | IP de escucha |
| `-config KEY=VAL` | Config inline |
| `-configfile FILE` | Archivo de config |
| `-silent` | Sin output de consola |
| `-addonupdate` | Actualizar todos los add-ons |
| `-addoninstall NAME` | Instalar add-on |
| `-addonuninstall NAME` | Desinstalar add-on |
| `-dir DIR` | Directorio home de ZAP |
| `-certload FILE` | Cargar certificado raiz |
| `-certpubdump FILE` | Exportar cert publico |
| `-certfulldump FILE` | Exportar cert completo |
| `-newsession NAME` | Crear nueva sesion |
| `-session FILE` | Cargar sesion existente |
| `-cmd` | Modo comando (ejecutar y salir) |
| `-quickurl URL` | Quick scan de URL |
| `-quickout FILE` | Output del quick scan |
| `-quickprogress` | Mostrar progreso del quick scan |

## API REST

ZAP expone una API REST completa en `http://localhost:8090`. Todas las calls requieren `apikey` como parametro (a menos que este desactivado).

### Spider
```bash
# Iniciar spider
curl "http://localhost:8090/JSON/spider/action/scan/?apikey=KEY&url=https://target.com&maxChildren=0&recurse=true&subtreeOnly=false"

# Status del spider
curl "http://localhost:8090/JSON/spider/view/status/?apikey=KEY&scanId=0"

# Resultados del spider
curl "http://localhost:8090/JSON/spider/view/results/?apikey=KEY&scanId=0"

# Detener spider
curl "http://localhost:8090/JSON/spider/action/stop/?apikey=KEY&scanId=0"

# Full subtree spider
curl "http://localhost:8090/JSON/spider/view/fullResults/?apikey=KEY&scanId=0"
```

### Ajax Spider
```bash
# Iniciar Ajax Spider (requiere browser/headless)
curl "http://localhost:8090/JSON/ajaxSpider/action/scan/?apikey=KEY&url=https://target.com&inScope=true"

# Status
curl "http://localhost:8090/JSON/ajaxSpider/view/status/?apikey=KEY"

# Resultados
curl "http://localhost:8090/JSON/ajaxSpider/view/results/?apikey=KEY&start=0&count=100"

# Detener
curl "http://localhost:8090/JSON/ajaxSpider/action/stop/?apikey=KEY"
```

### Active Scan
```bash
# Iniciar active scan
curl "http://localhost:8090/JSON/ascan/action/scan/?apikey=KEY&url=https://target.com&recurse=true&inScopeOnly=false&scanPolicyName=&method=&postData="

# Status (0-100%)
curl "http://localhost:8090/JSON/ascan/view/status/?apikey=KEY&scanId=0"

# Alertas del scan
curl "http://localhost:8090/JSON/ascan/view/alertsIds/?apikey=KEY&scanId=0"

# Detener
curl "http://localhost:8090/JSON/ascan/action/stop/?apikey=KEY&scanId=0"

# Pausar/Resumir
curl "http://localhost:8090/JSON/ascan/action/pause/?apikey=KEY&scanId=0"
curl "http://localhost:8090/JSON/ascan/action/resume/?apikey=KEY&scanId=0"

# Configurar scan policy
curl "http://localhost:8090/JSON/ascan/action/setOptionDefaultPolicy/?apikey=KEY&String=Default+Policy"
```

### Passive Scan
```bash
# Records pendientes
curl "http://localhost:8090/JSON/pscan/view/recordsToScan/?apikey=KEY"

# Habilitar/deshabilitar
curl "http://localhost:8090/JSON/pscan/action/setEnabled/?apikey=KEY&enabled=true"

# Reglas de passive scan
curl "http://localhost:8090/JSON/pscan/view/scanners/?apikey=KEY"
```

### Alertas
```bash
# Todas las alertas
curl "http://localhost:8090/JSON/alert/view/alerts/?apikey=KEY&baseurl=https://target.com&start=0&count=100"

# Resumen de alertas
curl "http://localhost:8090/JSON/alert/view/alertsSummary/?apikey=KEY&baseurl=https://target.com"

# Alertas por riesgo
curl "http://localhost:8090/JSON/alert/view/alertsByRisk/?apikey=KEY&url=https://target.com"

# Conteo
curl "http://localhost:8090/JSON/alert/view/alertCountsByRisk/?apikey=KEY&url=https://target.com"
```

### Contexto y Scope
```bash
# Crear contexto
curl "http://localhost:8090/JSON/context/action/newContext/?apikey=KEY&contextName=target"

# Incluir en scope
curl "http://localhost:8090/JSON/context/action/includeInContext/?apikey=KEY&contextName=target&regex=https://target.com.*"

# Excluir de scope
curl "http://localhost:8090/JSON/context/action/excludeFromContext/?apikey=KEY&contextName=target&regex=.*logout.*"

# Listar contextos
curl "http://localhost:8090/JSON/context/view/contextList/?apikey=KEY"
```

### Autenticacion via API
```bash
# Form-based auth
curl "http://localhost:8090/JSON/authentication/action/setAuthenticationMethod/?apikey=KEY&contextId=1&authMethodName=formBasedAuthentication&authMethodConfigParams=loginUrl=https://target.com/login&loginRequestData=username%3D%7B%25username%25%7D%26password%3D%7B%25password%25%7D"

# JSON-based auth
curl "http://localhost:8090/JSON/authentication/action/setAuthenticationMethod/?apikey=KEY&contextId=1&authMethodName=jsonBasedAuthentication&authMethodConfigParams=loginUrl=https://target.com/api/login&loginRequestData=%7B%22user%22%3A%22%7B%25username%25%7D%22%2C%22pass%22%3A%22%7B%25password%25%7D%22%7D"

# Crear usuario
curl "http://localhost:8090/JSON/users/action/newUser/?apikey=KEY&contextId=1&name=testuser"

# Configurar credenciales
curl "http://localhost:8090/JSON/users/action/setAuthenticationCredentials/?apikey=KEY&contextId=1&userId=0&authCredentialsConfigParams=username%3Dadmin%26password%3Dpassword123"

# Habilitar usuario
curl "http://localhost:8090/JSON/users/action/setUserEnabled/?apikey=KEY&contextId=1&userId=0&enabled=true"

# Indicador de login exitoso
curl "http://localhost:8090/JSON/authentication/action/setLoggedInIndicator/?apikey=KEY&contextId=1&loggedInIndicatorRegex=%5CQWelcome%5CE"

# Indicador de logout
curl "http://localhost:8090/JSON/authentication/action/setLoggedOutIndicator/?apikey=KEY&contextId=1&loggedOutIndicatorRegex=%5CQLogin%5CE"
```

### Reportes via API
```bash
# Generar reporte HTML
curl "http://localhost:8090/OTHER/reports/other/generate/?apikey=KEY&title=Scan+Report&template=traditional-html&sites=https://target.com&sections=&description=&contexts=&includedConfidences=&includedRisks=&reportFileName=report&reportFileNamePattern=&reportDir=/tmp&display="

# Reporte JSON
curl "http://localhost:8090/JSON/core/view/alerts/?apikey=KEY" > report.json

# Reporte XML
curl "http://localhost:8090/OTHER/core/other/xmlreport/?apikey=KEY" > report.xml

# Reporte HTML legacy
curl "http://localhost:8090/OTHER/core/other/htmlreport/?apikey=KEY" > report.html

# Reporte Markdown
curl "http://localhost:8090/OTHER/reports/other/generate/?apikey=KEY&title=Report&template=traditional-md&sites=https://target.com&reportDir=/tmp"
```

## Docker Scan Scripts

ZAP provee scripts pre-configurados para Docker que cubren los casos de uso mas comunes.

### zap-baseline.py (Scan Pasivo)

Scan rapido baseline. Solo spider + passive scan. No hace active attacks.

```bash
# Baseline scan basico
docker run --rm ghcr.io/zaproxy/zaproxy:stable zap-baseline.py \
  -t https://target.com

# Con reporte
docker run --rm -v $(pwd):/zap/wrk ghcr.io/zaproxy/zaproxy:stable zap-baseline.py \
  -t https://target.com \
  -r report.html \
  -J report.json

# Con configuracion custom
docker run --rm -v $(pwd):/zap/wrk ghcr.io/zaproxy/zaproxy:stable zap-baseline.py \
  -t https://target.com \
  -c baseline.conf \
  -r report.html

# Ajax Spider incluido
docker run --rm -v $(pwd):/zap/wrk ghcr.io/zaproxy/zaproxy:stable zap-baseline.py \
  -t https://target.com \
  -j -r report.html

# Con contexto y auth
docker run --rm -v $(pwd):/zap/wrk ghcr.io/zaproxy/zaproxy:stable zap-baseline.py \
  -t https://target.com \
  -n context.context \
  -U username
```

| Flag | Descripcion |
|------|-------------|
| `-t URL` | Target URL |
| `-r FILE` | Reporte HTML |
| `-w FILE` | Reporte Markdown |
| `-x FILE` | Reporte XML |
| `-J FILE` | Reporte JSON |
| `-c FILE` | Config file (reglas) |
| `-u FILE` | Config file (override) |
| `-j` | Usar Ajax Spider |
| `-g FILE` | Archivo de progreso |
| `-m MINS` | Minutos de spider (default: 1) |
| `-n FILE` | Archivo de contexto |
| `-U USER` | Usuario del contexto |
| `-d` | Debug |
| `-s` | Short output |
| `-l LEVEL` | Nivel minimo de alerta: PASS, IGNORE, INFO, WARN, FAIL |
| `-z OPTIONS` | Opciones ZAP adicionales |
| `-I` | No fallar en warnings |
| `-a` | Incluir alpha passive rules |

### zap-full-scan.py (Scan Completo)

Spider + passive scan + active scan. Scan completo de vulnerabilidades.

```bash
# Full scan basico
docker run --rm -v $(pwd):/zap/wrk ghcr.io/zaproxy/zaproxy:stable zap-full-scan.py \
  -t https://target.com \
  -r full_report.html

# Con Ajax Spider
docker run --rm -v $(pwd):/zap/wrk ghcr.io/zaproxy/zaproxy:stable zap-full-scan.py \
  -t https://target.com \
  -j -r full_report.html

# Con tiempo de spider extendido
docker run --rm -v $(pwd):/zap/wrk ghcr.io/zaproxy/zaproxy:stable zap-full-scan.py \
  -t https://target.com \
  -m 10 -r full_report.html

# Con contexto de auth
docker run --rm -v $(pwd):/zap/wrk ghcr.io/zaproxy/zaproxy:stable zap-full-scan.py \
  -t https://target.com \
  -n context.context -U testuser \
  -r full_report.html

# Multiples reportes
docker run --rm -v $(pwd):/zap/wrk ghcr.io/zaproxy/zaproxy:stable zap-full-scan.py \
  -t https://target.com \
  -r report.html -J report.json -x report.xml
```

Flags adicionales a los de baseline:

| Flag | Descripcion |
|------|-------------|
| `-m MINS` | Minutos de spider (default: 1) |
| `-T MINS` | Max minutos totales |
| `-P INT` | Max reglas por scan |

### zap-api-scan.py (Scan de APIs)

Scan especializado para APIs. Soporta OpenAPI/Swagger, SOAP/WSDL y GraphQL.

```bash
# OpenAPI/Swagger
docker run --rm -v $(pwd):/zap/wrk ghcr.io/zaproxy/zaproxy:stable zap-api-scan.py \
  -t https://target.com/api/v2/swagger.json \
  -f openapi \
  -r api_report.html

# OpenAPI desde archivo local
docker run --rm -v $(pwd):/zap/wrk ghcr.io/zaproxy/zaproxy:stable zap-api-scan.py \
  -t openapi.yaml \
  -f openapi \
  -r api_report.html

# SOAP/WSDL
docker run --rm -v $(pwd):/zap/wrk ghcr.io/zaproxy/zaproxy:stable zap-api-scan.py \
  -t https://target.com/service?wsdl \
  -f soap \
  -r soap_report.html

# GraphQL
docker run --rm -v $(pwd):/zap/wrk ghcr.io/zaproxy/zaproxy:stable zap-api-scan.py \
  -t https://target.com/graphql \
  -f graphql \
  -r graphql_report.html

# Con header de auth
docker run --rm -v $(pwd):/zap/wrk ghcr.io/zaproxy/zaproxy:stable zap-api-scan.py \
  -t https://target.com/api/v2/swagger.json \
  -f openapi \
  -z "-config replacer.full_list(0).description=auth \
      -config replacer.full_list(0).enabled=true \
      -config replacer.full_list(0).matchtype=REQ_HEADER \
      -config replacer.full_list(0).matchstr=Authorization \
      -config replacer.full_list(0).replacement='Bearer eyJ...'" \
  -r api_report.html
```

| Flag | Descripcion |
|------|-------------|
| `-t TARGET` | URL de definicion de API o archivo |
| `-f FORMAT` | Formato: openapi, soap, graphql |
| `-O` | Override hostname en la definicion |
| `-S` | Safe mode (no seguir redirects) |

## Automation Framework

El Automation Framework permite definir scans complejos en YAML. Es la forma mas potente de usar ZAP via CLI.

### Ejecutar Plan de Automatizacion

```bash
# Desde CLI
zap.sh -cmd -autorun automation.yaml

# Desde Docker
docker run --rm -v $(pwd):/zap/wrk ghcr.io/zaproxy/zaproxy:stable \
  zap.sh -cmd -autorun /zap/wrk/automation.yaml

# Con ZAP ya corriendo como daemon
curl "http://localhost:8090/JSON/automation/action/runPlan/?apikey=KEY&filePath=/path/to/automation.yaml"
```

### Tipos de Jobs

| Job | Descripcion |
|-----|-------------|
| `addOns` | Instalar/actualizar add-ons |
| `passiveScan-config` | Configurar reglas de passive scan |
| `spider` | Traditional spider |
| `spiderAjax` | Ajax spider (headless browser) |
| `delay` | Esperar N segundos |
| `passiveScan-wait` | Esperar a que passive scan termine |
| `activeScan` | Active scan |
| `alertFilter` | Filtrar alertas (false positives) |
| `requestor` | Enviar requests especificas |
| `replacer` | Reemplazar strings en requests/responses |
| `script` | Ejecutar scripts |
| `import` | Importar definiciones de API (OpenAPI, SOAP) |

### YAML - Ejemplo Completo de Auditoria Web

```yaml
---
env:
  contexts:
    - name: "Target Context"
      urls:
        - "https://target.com"
      includePaths:
        - "https://target.com.*"
      excludePaths:
        - ".*logout.*"
        - ".*\.css$"
        - ".*\.js$"
        - ".*\.png$"
        - ".*\.jpg$"
      authentication:
        method: "form"
        parameters:
          loginPageUrl: "https://target.com/login"
          loginRequestUrl: "https://target.com/login"
          loginRequestBody: "username={%username%}&password={%password%}"
        verification:
          method: "response"
          loggedInRegex: "\\QWelcome\\E"
          loggedOutRegex: "\\QPlease login\\E"
      sessionManagement:
        method: "cookie"
      users:
        - name: "testuser"
          credentials:
            username: "admin"
            password: "password123"
  parameters:
    failOnError: true
    failOnWarning: false
    progressToStdout: true

jobs:
  # 1. Instalar add-ons necesarios
  - type: addOns
    install:
      - ascanrules
      - pscanrules
      - retire
      - openapi

  # 2. Configurar passive scan
  - type: passiveScan-config
    parameters:
      maxAlertsPerRule: 10
      scanOnlyInScope: true

  # 3. Replacer (agregar headers a todas las requests)
  - type: replacer
    parameters:
      deleteAllRules: true
      rules:
        - description: "Auth Header"
          matchType: "REQ_HEADER"
          matchString: "Authorization"
          replacement: "Bearer eyJhbGciOiJIUzI1NiIs..."
          enabled: true
        - description: "Custom Header"
          matchType: "REQ_HEADER"
          matchString: "X-Custom"
          replacement: "pentest"
          enabled: true

  # 4. Spider traditional
  - type: spider
    parameters:
      context: "Target Context"
      user: "testuser"
      url: "https://target.com"
      maxDuration: 5
      maxDepth: 10
      maxChildren: 0

  # 5. Ajax Spider
  - type: spiderAjax
    parameters:
      context: "Target Context"
      url: "https://target.com"
      maxDuration: 5
      maxCrawlDepth: 5
      browserId: "firefox-headless"

  # 6. Esperar passive scan
  - type: passiveScan-wait
    parameters:
      maxDuration: 3

  # 7. Active scan
  - type: activeScan
    parameters:
      context: "Target Context"
      user: "testuser"
      policy: "Default Policy"
      maxRuleDurationInMins: 5
      maxScanDurationInMins: 30

  # 8. Filtrar false positives conocidos
  - type: alertFilter
    alertFilters:
      - ruleId: 10015
        newRisk: "False Positive"
        urlRegex: ".*static.*"

  # 9. Reporte
  - type: report
    parameters:
      template: "traditional-html"
      reportDir: "/zap/wrk"
      reportFile: "zap-report"
      reportTitle: "Security Audit Report"
      reportDescription: "Black-box web audit"
    risks:
      - high
      - medium
      - low
      - info
```

### YAML - Scan de API

```yaml
---
env:
  contexts:
    - name: "API Context"
      urls:
        - "https://api.target.com"
  parameters:
    progressToStdout: true

jobs:
  - type: import
    parameters:
      type: "openapi"
      file: "/zap/wrk/swagger.json"
    # o URL:
    # parameters:
    #   type: "openapi"
    #   url: "https://api.target.com/swagger.json"

  - type: replacer
    parameters:
      rules:
        - description: "API Key"
          matchType: "REQ_HEADER"
          matchString: "Authorization"
          replacement: "Bearer eyJ..."
          enabled: true

  - type: spider
    parameters:
      url: "https://api.target.com"
      maxDuration: 2

  - type: passiveScan-wait
    parameters:
      maxDuration: 2

  - type: activeScan
    parameters:
      policy: "Default Policy"
      maxScanDurationInMins: 20

  - type: report
    parameters:
      template: "traditional-json-plus"
      reportDir: "/zap/wrk"
      reportFile: "api-report"
```

### YAML - Scan Basico Rapido

```yaml
---
env:
  contexts:
    - name: "Quick Scan"
      urls:
        - "https://target.com"
  parameters:
    progressToStdout: true

jobs:
  - type: spider
    parameters:
      url: "https://target.com"
      maxDuration: 2
      maxDepth: 5

  - type: passiveScan-wait
    parameters:
      maxDuration: 2

  - type: activeScan
    parameters:
      maxScanDurationInMins: 10

  - type: report
    parameters:
      template: "traditional-html"
      reportDir: "/zap/wrk"
      reportFile: "quick-report"
```

## Scripts desde CLI

### Tipos de Scripts
| Tipo | Descripcion |
|------|-------------|
| `standalone` | Ejecuta una vez y sale |
| `targeted` | Contra URL especifica |
| `active` | Se ejecuta durante active scan |
| `passive` | Se ejecuta durante passive scan |
| `proxy` | Intercepta trafico del proxy |
| `authentication` | Maneja login custom |
| `httpsender` | Modifica requests/responses |
| `variant` | Define como parametrizar URLs |

### Cargar y Ejecutar Scripts via API
```bash
# Cargar script
curl "http://localhost:8090/JSON/script/action/load/?apikey=KEY&scriptName=custom&scriptType=standalone&scriptEngine=ECMAScript%20%3A%20Graal.js&fileName=/path/to/script.js"

# Ejecutar script standalone
curl "http://localhost:8090/JSON/script/action/runStandAloneScript/?apikey=KEY&scriptName=custom"

# Habilitar script (para tipos no-standalone)
curl "http://localhost:8090/JSON/script/action/enable/?apikey=KEY&scriptName=custom"

# Listar scripts
curl "http://localhost:8090/JSON/script/view/listScripts/?apikey=KEY"
```

## Add-ons desde CLI

```bash
# Listar add-ons instalados
curl "http://localhost:8090/JSON/autoupdate/view/installedAddons/?apikey=KEY"

# Listar add-ons disponibles en marketplace
curl "http://localhost:8090/JSON/autoupdate/view/marketplaceAddons/?apikey=KEY"

# Instalar add-on
curl "http://localhost:8090/JSON/autoupdate/action/installAddon/?apikey=KEY&id=retire"

# Desinstalar
curl "http://localhost:8090/JSON/autoupdate/action/uninstallAddon/?apikey=KEY&id=retire"

# Actualizar todos
curl "http://localhost:8090/JSON/autoupdate/action/updateAddons/?apikey=KEY"
```

## Workflow CLI Completo

```bash
# 1. Iniciar ZAP daemon
docker run -d --name zap -p 8090:8090 ghcr.io/zaproxy/zaproxy:stable \
  zap.sh -daemon -port 8090 -host 0.0.0.0 -config api.disablekey=true

# 2. Esperar a que ZAP este listo
sleep 10
curl -s http://localhost:8090/JSON/core/view/version/ | jq

# 3. Crear contexto
curl -s "http://localhost:8090/JSON/context/action/newContext/?contextName=audit"

# 4. Incluir target en scope
curl -s "http://localhost:8090/JSON/context/action/includeInContext/?contextName=audit&regex=https://target.com.*"

# 5. Spider
SCAN_ID=$(curl -s "http://localhost:8090/JSON/spider/action/scan/?url=https://target.com&maxChildren=0&recurse=true" | jq -r '.scan')
# Esperar spider
while [ "$(curl -s http://localhost:8090/JSON/spider/view/status/?scanId=$SCAN_ID | jq -r '.status')" != "100" ]; do sleep 5; done

# 6. Passive scan wait
while [ "$(curl -s http://localhost:8090/JSON/pscan/view/recordsToScan/ | jq -r '.recordsToScan')" != "0" ]; do sleep 2; done

# 7. Active scan
ASCAN_ID=$(curl -s "http://localhost:8090/JSON/ascan/action/scan/?url=https://target.com&recurse=true" | jq -r '.scan')
# Esperar active scan
while [ "$(curl -s http://localhost:8090/JSON/ascan/view/status/?scanId=$ASCAN_ID | jq -r '.status')" != "100" ]; do sleep 10; done

# 8. Obtener alertas
curl -s "http://localhost:8090/JSON/alert/view/alertsSummary/?baseurl=https://target.com" | jq

# 9. Generar reporte
curl -s "http://localhost:8090/OTHER/core/other/htmlreport/" > report.html

# 10. Cleanup
docker stop zap && docker rm zap
```
