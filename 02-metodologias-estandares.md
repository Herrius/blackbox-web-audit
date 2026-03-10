# 02 - Metodologias y Estandares

## Metodologias Principales

### 1. OWASP Web Security Testing Guide (WSTG)

La guia mas utilizada especificamente para auditorias de aplicaciones web. Version estable actual: **WSTG 4.2** (version 5.0 en desarrollo).

**Estructura de pruebas (12 categorias):**

| ID | Categoria | Descripcion |
|----|-----------|-------------|
| WSTG-INFO | Information Gathering | Reconocimiento y recoleccion de informacion |
| WSTG-CONF | Configuration and Deployment Management | Configuracion de servidor, plataforma y aplicacion |
| WSTG-IDNT | Identity Management | Registro, roles, provisioning de cuentas |
| WSTG-ATHN | Authentication | Mecanismos de autenticacion |
| WSTG-ATHZ | Authorization | Controles de autorizacion y acceso |
| WSTG-SESS | Session Management | Manejo de sesiones, cookies, tokens |
| WSTG-INPV | Input Validation | Inyeccion, XSS, y validacion de entrada |
| WSTG-ERRH | Error Handling | Manejo de errores y divulgacion de informacion |
| WSTG-CRYP | Cryptography | Cifrado, TLS, almacenamiento seguro |
| WSTG-BUSL | Business Logic | Flujos de negocio, abuso de funcionalidad |
| WSTG-CLNT | Client-Side Testing | DOM XSS, manipulacion de almacenamiento local |
| WSTG-APIT | API Testing | REST, GraphQL, SOAP testing |

**Flujo de trabajo OWASP para caja negra:**
1. Reconocimiento pasivo → recoleccion sin interaccion directa
2. Reconocimiento activo → interaccion directa con la aplicacion
3. Mapeo de la aplicacion → funcionalidades, endpoints, flujos
4. Pruebas por categoria → seguir cada WSTG-* sistematicamente
5. Verificacion y validacion → confirmar hallazgos, eliminar falsos positivos
6. Documentacion y reporte

---

### 2. PTES (Penetration Testing Execution Standard)

Creado en 2009, define siete fases para cualquier tipo de pentest.

**Las 7 fases:**

```
1. PRE-ENGAGEMENT INTERACTIONS
   - Definir alcance, objetivos y reglas
   - Firmar contratos y autorizaciones
   - Establecer canales de comunicacion

2. INTELLIGENCE GATHERING
   - OSINT pasivo y activo
   - Footprinting de la organizacion
   - Identificacion de tecnologias

3. THREAT MODELING
   - Identificar activos valiosos
   - Mapear vectores de ataque
   - Priorizar objetivos segun riesgo

4. VULNERABILITY ANALYSIS
   - Escaneo automatizado y manual
   - Correlacion de hallazgos
   - Eliminacion de falsos positivos

5. EXPLOITATION
   - Explotacion controlada de vulnerabilidades
   - Desarrollo de PoCs
   - Documentacion de cada paso

6. POST-EXPLOITATION
   - Evaluacion del impacto real
   - Determinacion del valor de los datos accedidos
   - Pivoting (si esta en scope)

7. REPORTING
   - Reporte ejecutivo y tecnico
   - Evidencias y reproducciones
   - Recomendaciones priorizadas
```

---

### 3. OSSTMM (Open Source Security Testing Methodology Manual)

Publicado por ISECOM. Cubre seguridad operacional mas alla del ambito tecnico.

**5 canales de seguridad:**
- Human Security (ingenieria social, awareness)
- Physical Security (acceso fisico)
- Wireless Security (WiFi, Bluetooth, RF)
- Telecommunications Security (VoIP, telecom)
- Data Networks Security (redes, aplicaciones)

**Metricas OSSTMM:**
- Usa el concepto de **RAV (Risk Assessment Values)** para cuantificar seguridad
- Mide la superficie de ataque vs controles implementados
- Proporciona metricas objetivas y repetibles

---

### 4. NIST SP 800-115

Guia tecnica del NIST para pruebas de seguridad:
- Review techniques (documentacion, logs, reglas)
- Target identification and analysis
- Target vulnerability validation
- Security testing planning
- Security testing execution
- Post-testing activities

---

## Flujo Completo - Auditoria Caja Negra Web

```
FLUJO DE EJECUCION INTEGRADO

PRE-ENGAGEMENT
    |
    v
[1] RECONOCIMIENTO (Pasivo + Activo)
    - OSINT, subdominios, tecnologias
    - Mapeo de superficie de ataque
    |
    v
[2] ENUMERACION Y ESCANEO
    - Puertos, directorios, endpoints
    - Identificacion de tecnologias/versiones
    - Mapeo de funcionalidades
    |
    v
[3] ANALISIS DE VULNERABILIDADES
    - Pruebas por categoria OWASP
    - Escaneo automatizado + testing manual
    - Clasificacion y priorizacion
    |
    v
[4] EXPLOTACION
    - PoC de vulnerabilidades confirmadas
    - Chaining de vulnerabilidades
    - Documentacion paso a paso
    |
    v
[5] POST-EXPLOTACION
    - Evaluacion de impacto
    - Determinacion de alcance del compromiso
    |
    v
[6] REPORTE
    - Hallazgos con CVSS 4.0
    - Recomendaciones de remediacion
    - Entrega y presentacion
    |
    v
[7] RETEST (si acordado)
    - Verificacion de remediaciones
    - Reporte de retest
```

---

## Comparativa de Metodologias

| Aspecto | OWASP WSTG | PTES | OSSTMM | NIST 800-115 |
|---------|-----------|------|--------|--------------|
| Enfoque principal | Web apps | Pentest general | Seguridad operacional | Evaluacion tecnica |
| Profundidad web | Excelente | Buena | General | General |
| Metricas propias | No (usa CVSS) | No (usa CVSS) | Si (RAV) | No |
| Fases definidas | 12 categorias | 7 fases | 5 canales | 4 fases |
| Aplicabilidad caja negra | Directa | Directa | Parcial | Parcial |
| Costo | Gratuito | Gratuito | Gratuito | Gratuito |
| Actualizacion | Activa (v5.0 en dev) | Estancada | Periodica | Periodica |

### Recomendacion para Caja Negra Web

Usar **OWASP WSTG como guia principal** de pruebas, complementada con las fases de **PTES** para estructura del engagement y **CVSS 4.0** para scoring de hallazgos.
