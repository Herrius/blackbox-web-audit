# 07 - Fase 5: Post-Explotacion y Reporte

## Tabla de Contenidos

- [Post-Explotacion](#post-explotacion) — Evaluacion de Impacto, Limpieza
- [CVSS 4.0 - Scoring](#cvss-40---scoring-de-vulnerabilidades) — Metricas Base, Rangos, Ejemplos
- [Estructura del Reporte](#estructura-del-reporte) — Ejecutivo, Tecnico
- [Plantilla de Hallazgo Individual](#plantilla-de-hallazgo-individual)
- [Entrega y Presentacion](#entrega-y-presentacion)

## Post-Explotacion

En caja negra web, la post-explotacion se centra en evaluar el impacto real del compromiso.

### Evaluacion de Impacto

```
Para cada vulnerabilidad explotada, determinar:

1. CONFIDENCIALIDAD
   - Que datos se pueden acceder?
   - Datos personales (PII)? Datos financieros? Credenciales?
   - Volumen de datos accesibles

2. INTEGRIDAD
   - Se pueden modificar datos?
   - Se puede alterar funcionalidad de la aplicacion?
   - Se puede inyectar contenido malicioso?

3. DISPONIBILIDAD
   - Se puede causar denegacion de servicio?
   - Se pueden borrar datos criticos?
   - Se puede bloquear a usuarios legitimos?

4. ALCANCE
   - El compromiso afecta solo la aplicacion o se puede pivotar?
   - Hay acceso a otros sistemas internos (via SSRF, RCE)?
   - Se pueden comprometer cuentas de otros usuarios?
```

### Limpieza

```
Checklist post-explotacion:
[ ] Eliminar cualquier archivo subido durante pruebas
[ ] Revertir cambios en datos (si se modificaron)
[ ] Cerrar sesiones de prueba
[ ] Documentar cualquier cambio residual
[ ] Notificar al cliente de cualquier impacto no intencionado
```

---

## CVSS 4.0 - Scoring de Vulnerabilidades

### Metricas Base

```
ATTACK VECTOR (AV):
  Network (N)  - Explotable remotamente via red
  Adjacent (A) - Requiere red adyacente
  Local (L)    - Requiere acceso local
  Physical (P) - Requiere acceso fisico

ATTACK COMPLEXITY (AC):
  Low (L)  - No requiere condiciones especiales
  High (H) - Requiere condiciones especificas

ATTACK REQUIREMENTS (AT): [Nuevo en CVSS 4.0]
  None (N)   - Sin requisitos previos
  Present (P) - Requiere condiciones de despliegue especificas

PRIVILEGES REQUIRED (PR):
  None (N) - Sin autenticacion
  Low (L)  - Usuario normal
  High (H) - Administrador

USER INTERACTION (UI):
  None (N)    - Sin interaccion del usuario
  Passive (P) - Interaccion minima (ej: visitar pagina) [Nuevo en CVSS 4.0]
  Active (A)  - Interaccion deliberada del usuario [Nuevo en CVSS 4.0]

IMPACTO EN SISTEMA VULNERABLE:
  Confidentiality (VC): None/Low/High
  Integrity (VI): None/Low/High
  Availability (VA): None/Low/High

IMPACTO EN SISTEMAS SUBSECUENTES:
  Confidentiality (SC): None/Low/High
  Integrity (SI): None/Low/High
  Availability (SA): None/Low/High
```

### Rangos de Severidad

| Score | Severidad | Color |
|-------|-----------|-------|
| 9.0 - 10.0 | Critical | Rojo |
| 7.0 - 8.9 | High | Naranja |
| 4.0 - 6.9 | Medium | Amarillo |
| 0.1 - 3.9 | Low | Azul |
| 0.0 | None/Info | Gris |

### Ejemplos de Scoring

```
SQL Injection (sin autenticacion, acceso a BD completa):
AV:N/AC:L/AT:N/PR:N/UI:N/VC:H/VI:H/VA:H/SC:N/SI:N/SA:N
Score: 9.3 (Critical)

Reflected XSS:
AV:N/AC:L/AT:N/PR:N/UI:P/VC:L/VI:L/VA:N/SC:N/SI:N/SA:N
Score: 5.1 (Medium)

Stored XSS (afecta a admin):
AV:N/AC:L/AT:N/PR:L/UI:P/VC:H/VI:H/VA:N/SC:N/SI:N/SA:N
Score: 7.1 (High)

IDOR (acceso a datos de otros usuarios):
AV:N/AC:L/AT:N/PR:L/UI:N/VC:H/VI:N/VA:N/SC:N/SI:N/SA:N
Score: 6.9 (Medium)

SSRF con acceso a metadata de cloud:
AV:N/AC:L/AT:N/PR:N/UI:N/VC:H/VI:N/VA:N/SC:H/SI:N/SA:N
Score: 9.3 (Critical)

Security Headers ausentes:
AV:N/AC:H/AT:P/PR:N/UI:P/VC:N/VI:N/VA:N/SC:N/SI:N/SA:N
Score: 0.0 (Info - se reporta como best practice)
```

---

## Estructura del Reporte

### Reporte Ejecutivo (para stakeholders no tecnicos)

```
REPORTE EJECUTIVO DE AUDITORIA DE SEGURIDAD WEB

1. PORTADA
   - Nombre del cliente
   - Tipo de auditoria: Caja Negra - Aplicacion Web
   - Fecha de ejecucion
   - Clasificacion: CONFIDENCIAL
   - Version del documento
   - Empresa auditora y auditor responsable

2. RESUMEN EJECUTIVO (1-2 paginas)
   - Objetivo de la auditoria
   - Alcance
   - Resumen de hallazgos:
     * X vulnerabilidades criticas
     * X vulnerabilidades altas
     * X vulnerabilidades medias
     * X vulnerabilidades bajas
     * X informativas
   - Nivel de riesgo general: [Critico/Alto/Medio/Bajo]
   - Conclusion ejecutiva (2-3 parrafos)
   - Top 3 recomendaciones prioritarias

3. GRAFICO RESUMEN
   - Distribucion de vulnerabilidades por severidad (pie chart)
   - Vulnerabilidades por categoria OWASP (bar chart)
   - Tendencia vs auditorias anteriores (si aplica)
```

### Reporte Tecnico (para equipo de desarrollo/seguridad)

```
4. METODOLOGIA
   - Estandares utilizados (OWASP WSTG, PTES)
   - Herramientas empleadas
   - Fechas y duracion de las pruebas
   - Limitaciones encontradas

5. HALLAZGOS DETALLADOS

   Para cada vulnerabilidad:

   5.X TITULO DEL HALLAZGO
   ========================
   Severidad: [Critical/High/Medium/Low/Info]
   CVSS 4.0 Score: [X.X]
   CVSS Vector: [CVSS:4.0/AV:N/AC:L/...]
   Categoria OWASP: [A0X:2025 - Nombre]
   CWE: [CWE-XXX - Nombre]
   Estado: [Confirmada/Potencial]

   DESCRIPCION:
   Explicacion clara de la vulnerabilidad, que es y por que existe.

   UBICACION:
   - URL afectada
   - Parametro vulnerable
   - Componente afectado

   EVIDENCIA:
   - Screenshots numerados
   - HTTP Requests/Responses relevantes
   - Datos obtenidos (censurados si son sensibles)

   PASOS DE REPRODUCCION:
   1. Paso detallado con datos especificos
   2. ...
   3. ...

   IMPACTO:
   Descripcion del impacto real en el negocio.

   REMEDIACION:
   - Solucion recomendada (especifica y accionable)
   - Codigo de ejemplo si es relevante
   - Referencias a guias de correccion

6. HALLAZGOS INFORMATIVOS
   - Security headers ausentes
   - Buenas practicas no implementadas
   - Observaciones generales

7. RESUMEN DE REMEDIACIONES
   Tabla priorizada:
   | # | Hallazgo | Severidad | Esfuerzo | Prioridad |
   |---|----------|-----------|----------|-----------|
   | 1 | SQLi en login | Critical | Medio | Inmediata |
   | 2 | XSS en search | High | Bajo | Alta |
   | ... | ... | ... | ... | ... |

8. ANEXOS
   - Logs completos de herramientas
   - Lista completa de URLs testeadas
   - Configuracion de herramientas utilizadas
   - Glosario de terminos
```

---

## Plantilla de Hallazgo Individual

```markdown
## [ID] - [Titulo Descriptivo]

**Severidad:** Critical | High | Medium | Low | Info
**CVSS 4.0:** X.X (CVSS:4.0/AV:N/AC:L/AT:N/PR:N/UI:N/VC:H/VI:H/VA:H/SC:N/SI:N/SA:N)
**OWASP:** A0X:2025 - [Categoria]
**CWE:** CWE-XXX - [Nombre]

### Descripcion
[Parrafo explicando la vulnerabilidad en contexto]

### Ubicacion
- **URL:** `https://target.com/vulnerable/endpoint`
- **Parametro:** `id` (GET)
- **Componente:** API de usuarios

### Reproduccion

**Request:**
```http
GET /api/users?id=1'+OR+1=1-- HTTP/1.1
Host: target.com
Cookie: session=abc123
```

**Response:**
```http
HTTP/1.1 200 OK
Content-Type: application/json

{"users": [{"id":1,"name":"admin","email":"admin@target.com"}, ...]}
```

### Origen del Hallazgo
- [ ] Descubierto manualmente
- [ ] Descubierto por scanner → Reproducido manualmente: Sí / No
- **Scanner:** [ZAP/Nuclei/Nikto/otro]
- **Verificación manual:** [Comando curl o request usado para confirmar]

### Evidencia
[Screenshot 1: Descripcion]
[Screenshot 2: Descripcion]

### Impacto
Un atacante sin autenticacion puede extraer la base de datos completa
de usuarios, incluyendo emails y hashes de passwords. Esto permite
credential stuffing contra otros servicios de los usuarios afectados.

### Remediacion
1. Usar consultas parametrizadas (prepared statements)
2. Implementar validacion de input con whitelist
3. Aplicar principio de minimo privilegio en la cuenta de BD
4. Implementar WAF como capa adicional de defensa

**Referencia:** https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html
```

---

## Entrega y Presentacion

```
PROCESO DE ENTREGA:

1. Entrega del borrador
   - Enviar reporte cifrado (GPG/PGP o password en canal separado)
   - Dar plazo para revision y preguntas

2. Presentacion
   - Sesion con equipo tecnico: hallazgos detallados y demos
   - Sesion con management: resumen ejecutivo y riesgo de negocio
   - Resolver dudas y ajustar prioridades

3. Reporte final
   - Incorporar feedback del cliente
   - Version definitiva firmada

4. Retest (si acordado)
   - Periodo definido post-remediacion (tipico: 30-90 dias)
   - Verificar que las correcciones son efectivas
   - Reporte de retest con estado de cada hallazgo
```
