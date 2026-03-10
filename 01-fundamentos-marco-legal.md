# 01 - Fundamentos y Marco Legal

## Que es una Auditoria de Caja Negra

Una auditoria de caja negra (black box) es una evaluacion de seguridad donde el auditor no recibe informacion previa sobre la infraestructura, codigo fuente, credenciales ni arquitectura del objetivo. Simula la perspectiva de un atacante externo real.

### Diferencias entre Tipos de Auditoria

| Aspecto | Caja Negra | Caja Gris | Caja Blanca |
|---------|-----------|-----------|-------------|
| Informacion previa | Ninguna (solo URL/IP) | Parcial (credenciales, docs) | Completa (codigo fuente, arquitectura) |
| Simula | Atacante externo | Insider con acceso limitado | Revision de codigo/arquitectura |
| Tiempo requerido | Mayor | Medio | Menor para vuln especificas |
| Cobertura | Superficie externa | Media | Maxima |
| Costo | Alto (mas horas) | Medio | Variable |

### Ventajas de Caja Negra
- Simula escenario real de ataque externo
- Evalua la postura de seguridad desde la perspectiva del adversario
- Descubre vulnerabilidades expuestas sin sesgo de informacion interna
- Testea la efectividad de controles perimetrales y de deteccion

### Limitaciones
- No garantiza cobertura total del codigo
- Puede pasar por alto vulnerabilidades que requieren contexto interno
- Requiere mas tiempo para alcanzar profundidad equivalente a caja gris/blanca

---

## Marco Legal

### Autorizacion Escrita (Obligatoria)

**Nunca iniciar una auditoria sin autorizacion formal por escrito.** La autorizacion debe incluir:

1. **Identificacion de las partes**
   - Datos del cliente (propietario del sistema)
   - Datos de la empresa/auditor que ejecuta
   - Contactos de emergencia de ambas partes

2. **Alcance (Scope)**
   - URLs, dominios y subdominios autorizados
   - Direcciones IP incluidas y excluidas
   - Sistemas fuera de alcance explicitamente listados
   - Entornos: produccion, staging, desarrollo

3. **Ventana temporal**
   - Fechas de inicio y fin
   - Horarios permitidos para pruebas activas
   - Periodos de blackout (ej: eventos de negocio criticos)

4. **Tecnicas permitidas y prohibidas**
   - Tipos de pruebas autorizadas (escaneo, fuzzing, explotacion, DoS controlado)
   - Tecnicas explicitamente prohibidas
   - Ingenieria social: si/no y alcance
   - Exfiltracion de datos: limites

5. **Manejo de datos**
   - Tratamiento de datos sensibles descubiertos
   - Almacenamiento y cifrado de evidencias
   - Destruccion post-reporte
   - Cumplimiento GDPR/LOPD si aplica

---

### Reglas de Engagement (RoE)

Las RoE definen las condiciones operativas de la auditoria:

```
REGLAS DE ENGAGEMENT - TEMPLATE

1. COMUNICACION
   - Canal principal: [email/slack/telefono]
   - Frecuencia de reportes de estado: [diaria/semanal]
   - Protocolo de hallazgo critico: notificacion inmediata a [contacto]
   - Escalamiento: [cadena de contactos]

2. LIMITES TECNICOS
   - Rate limiting: maximo [X] requests/segundo
   - Escaneos automatizados: [horario permitido]
   - Cuentas de prueba: [si se proporcionan]
   - VPN/IP de origen: [IP del auditor para whitelisting]

3. CONDICIONES DE DETENCION
   - Hallazgo de vulnerabilidad critica en explotacion activa
   - Impacto no intencionado en disponibilidad
   - Descubrimiento de breach activo por terceros
   - Solicitud expresa del cliente

4. ENTREGABLES
   - Reporte ejecutivo
   - Reporte tecnico detallado
   - Evidencias y PoCs
   - Retest post-remediacion: [si/no, plazo]
```

---

### Legislacion Relevante

#### Internacional
- **Computer Fraud and Abuse Act (CFAA)** - EEUU: acceso no autorizado a sistemas es delito federal
- **Computer Misuse Act 1990** - Reino Unido
- **Budapest Convention on Cybercrime** - Marco europeo

#### Europa
- **GDPR (Reglamento General de Proteccion de Datos)**: aplica si se accede a datos personales durante la auditoria
- **Directiva NIS2**: requisitos de ciberseguridad para entidades esenciales e importantes

#### Espana
- **Codigo Penal Art. 197 bis**: acceso no autorizado a sistemas informaticos
- **Codigo Penal Art. 264**: danos informaticos
- **LOPD-GDD**: proteccion de datos personales
- **ENS (Esquema Nacional de Seguridad)**: marco para administraciones publicas

#### Latinoamerica
- Cada pais tiene legislacion especifica sobre delitos informaticos
- Convenio de Budapest aplica en paises adheridos
- Verificar legislacion local del pais donde residen los servidores

---

### Contrato de Servicios - Elementos Clave

```
ESTRUCTURA DEL CONTRATO

1. Objeto del contrato
2. Alcance y limitaciones
3. Autorizacion explicita de pruebas
4. Clausula de confidencialidad (NDA)
5. Limitacion de responsabilidad
6. Propiedad intelectual de los hallazgos
7. Tratamiento y destruccion de datos
8. Seguros de responsabilidad civil
9. Duracion y condiciones de renovacion
10. Jurisdiccion y resolucion de disputas
```

### Buenas Practicas Legales

- Mantener logs detallados de toda actividad realizada
- Documentar timestamp de inicio y fin de cada sesion
- Usar IP de origen conocida y comunicada al cliente
- No acceder a datos mas alla de lo necesario para demostrar la vulnerabilidad
- Cifrar toda evidencia almacenada
- Destruir datos del cliente al finalizar el periodo de retencion acordado
