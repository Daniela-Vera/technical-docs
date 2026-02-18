\# Runbook: Gestión de Cambios en Sistemas



\## Información del Documento



| Campo | Valor |
|-------|-------|
| \*\*Versión\*\* | 1.0 |
| \*\*Fecha última actualización\*\* | 2024-02-18 |
| \*\*Propietario\*\* | Daniela Vera |
| \*\*Aprobado por\*\* | \[Nombre del aprobador] |
| \*\*Nivel de criticidad\*\* | Alto |

---



\## Objetivo



Este runbook establece el procedimiento estándar para la implementación controlada de cambios en sistemas de producción, minimizando riesgos y garantizando trazabilidad completa.



---



\## Alcance



\- Cambios en aplicaciones y servicios

\- Actualizaciones de software

\- Modificaciones de configuración

\- Deployment de nuevas funcionalidades

\- Rollback de cambios fallidos

\- Parches de seguridad críticos



---



\## Clasificación de Cambios



| Tipo | Descripción | Aprobación Requerida | Ventana |
|------|-------------|----------------------|---------|
| \*\*Estándar\*\* | Cambios pre-aprobados, bajo riesgo | Change Manager | Horario laboral |
| \*\*Normal\*\* | Cambios planificados, impacto moderado | CAB (Change Advisory Board) | Fuera de horario |
| \*\*Emergencia\*\* | Parches críticos de seguridad, outages | CTO + Change Manager | Inmediato |



---



\## Prerrequisitos Generales



\- \[ ] Ticket de cambio aprobado en sistema ITSM

\- \[ ] Análisis de impacto completado

\- \[ ] Plan de rollback documentado

\- \[ ] Backups verificados (< 24 horas)

\- \[ ] Comunicación a stakeholders enviada

\- \[ ] Recursos necesarios disponibles (personal, accesos)

\- \[ ] Ventana de mantenimiento programada



---



\## Procedimiento de Cambio



\### Fase 1: Planificación



\#### 1.1 Documentación del Cambio

Crear ticket en ITSM con:

\- Descripción detallada del cambio

\- Justificación de negocio

\- Sistemas afectados

\- Riesgos identificados

\- Plan de implementación

\- Plan de rollback

\- Criterios de éxito



\#### 1.2 Análisis de Impacto

```bash

\# Identificar servicios dependientes

systemctl list-dependencies \[servicio]



\# Verificar usuarios conectados

who

w



\# Revisar procesos críticos

ps aux | grep \[aplicacion]

```



\#### 1.3 Preparación del Entorno



\*\*Para aplicaciones web:\*\*

```bash

\# Clonar código actual

git clone https://repo.company.com/app.git

cd app

git checkout production



\# Crear rama para el cambio

git checkout -b change-\[ticket-id]

```



\*\*Para bases de datos:\*\*

```sql

-- Backup de tablas afectadas

CREATE TABLE tabla\_backup AS SELECT \* FROM tabla\_produccion;



-- Verificar espacio disponible

SELECT table\_schema, 

&nbsp;      SUM(data\_length + index\_length) / 1024 / 1024 AS "Size (MB)"

FROM information\_schema.tables 

GROUP BY table\_schema;

```



---



\### Fase 2: Ejecución



\#### 2.1 Pre-Deployment



\*\*Verificaciones pre-cambio:\*\*

```bash

\# Estado del servicio

systemctl status \[servicio]



\# Conectividad

ping -c 3 \[servidor]

curl -I https://aplicacion.com/health



\# Recursos del sistema

df -h

free -h

top -bn1 | head -20

```



\*\*Modo mantenimiento (si aplica):\*\*

```bash

\# Crear página de mantenimiento

touch /var/www/html/maintenance.html



\# Redirigir tráfico

\# (actualizar load balancer o nginx)

```



\#### 2.2 Implementación del Cambio



\*\*Deployment de aplicación:\*\*

```bash

\# Detener servicio actual

sudo systemctl stop \[aplicacion]



\# Backup de archivos actuales

sudo cp -r /opt/app /opt/app\_backup\_$(date +%Y%m%d\_%H%M%S)



\# Deploy nueva versión

sudo cp -r /tmp/new\_release/\* /opt/app/



\# Ajustar permisos

sudo chown -R appuser:appgroup /opt/app



\# Iniciar servicio

sudo systemctl start \[aplicacion]

```



\*\*Cambios en base de datos:\*\*

```sql

-- Ejecutar script de cambio

SOURCE /path/to/change\_script.sql;



-- Verificar cambios

DESCRIBE tabla\_modificada;

SELECT COUNT(\*) FROM tabla\_afectada;

```



\*\*Cambios de configuración:\*\*

```bash

\# Backup de configuración actual

sudo cp /etc/app/config.yml /etc/app/config.yml.bak



\# Aplicar nueva configuración

sudo nano /etc/app/config.yml



\# Validar sintaxis

\[comando\_validacion] /etc/app/config.yml



\# Recargar configuración

sudo systemctl reload \[servicio]

```



\#### 2.3 Verificación Post-Deployment



\*\*Pruebas funcionales:\*\*

```bash

\# Health check

curl https://app.com/health

\# Esperado: HTTP 200 OK



\# Verificar logs

tail -f /var/log/app/application.log | grep ERROR



\# Test de conectividad

nc -zv app.com 443



\# Verificar respuesta de aplicación

curl -X POST https://app.com/api/test -d '{"test":"data"}'

```



\*\*Monitoreo de métricas:\*\*

\- CPU y memoria del servidor

\- Tiempo de respuesta de aplicación

\- Tasa de errores

\- Conexiones activas



---



\### Fase 3: Validación



\#### 3.1 Pruebas de Aceptación

\- \[ ] Funcionalidad principal operativa

\- \[ ] Integraciones funcionando

\- \[ ] Performance dentro de SLAs

\- \[ ] Sin errores críticos en logs

\- \[ ] Confirmación de usuarios clave



\#### 3.2 Monitoreo Extendido

Monitorear por al menos 2 horas post-implementación:

```bash

\# Watch logs en tiempo real

tail -f /var/log/app/\*.log



\# Monitoreo de recursos

watch -n 5 'free -h \&\& df -h'

```



---



\### Fase 4: Documentación y Cierre



\#### 4.1 Registro de Actividades

Documentar en ticket:

\- Hora de inicio y fin del cambio

\- Pasos ejecutados

\- Resultados de pruebas

\- Incidencias encontradas (si las hubo)

\- Acciones correctivas tomadas



\#### 4.2 Notificaciones

\- Confirmar éxito del cambio a stakeholders

\- Actualizar status en sistema de monitoreo

\- Notificar a equipo de soporte



\#### 4.3 Actualizar Documentación

\- Diagramas de arquitectura

\- Manuales de operación

\- Base de conocimiento

\- Este runbook (si aplica)



---



\## Procedimiento de Rollback



\### Cuándo Ejecutar Rollback

\- Errores críticos en producción

\- Performance degradada significativamente

\- Funcionalidad principal no operativa

\- No se cumplen criterios de éxito



\### Ejecución de Rollback



\*\*Para aplicaciones:\*\*

```bash

\# Detener servicio

sudo systemctl stop \[aplicacion]



\# Restaurar versión anterior

sudo rm -rf /opt/app

sudo cp -r /opt/app\_backup\_\[fecha] /opt/app



\# Restaurar permisos

sudo chown -R appuser:appgroup /opt/app



\# Iniciar servicio

sudo systemctl start \[aplicacion]



\# Verificar

systemctl status \[aplicacion]

curl https://app.com/health

```



\*\*Para base de datos:\*\*

```sql

-- Restaurar desde backup

DROP TABLE tabla\_actual;

CREATE TABLE tabla\_actual AS SELECT \* FROM tabla\_backup;



-- Verificar integridad

SELECT COUNT(\*) FROM tabla\_actual;

```



\*\*Para configuración:\*\*

```bash

\# Restaurar archivo de configuración

sudo cp /etc/app/config.yml.bak /etc/app/config.yml



\# Recargar

sudo systemctl reload \[servicio]

```



\### Post-Rollback

\- Documentar causa raíz del fallo

\- Notificar a stakeholders

\- Crear plan de corrección

\- Reagendar cambio



---



\## Control de Versiones (Git Workflow)



\### Estrategia de Branching

```

main (producción)

&nbsp; ↑

develop (desarrollo)

&nbsp; ↑

feature/TICKET-123 (cambios individuales)

```



\### Comandos Esenciales

```bash

\# Crear rama para cambio

git checkout -b feature/TICKET-123



\# Hacer cambios y commit

git add .

git commit -m "TICKET-123: Descripción del cambio"



\# Subir rama

git push origin feature/TICKET-123



\# Merge a develop (después de code review)

git checkout develop

git merge feature/TICKET-123



\# Tag de versión para producción

git tag -a v1.2.3 -m "Release v1.2.3"

git push origin v1.2.3



\# Deploy a producción

git checkout main

git merge develop

git push origin main

```



---



\## Checklist de Cambio



\*\*Pre-Cambio:\*\*

\- \[ ] Ticket aprobado

\- \[ ] Backup completado

\- \[ ] Plan de rollback listo

\- \[ ] Notificaciones enviadas

\- \[ ] Equipo de soporte alertado



\*\*Durante Cambio:\*\*

\- \[ ] Modo mantenimiento activado (si aplica)

\- \[ ] Cambio implementado según plan

\- \[ ] Verificaciones técnicas exitosas

\- \[ ] Logs revisados



\*\*Post-Cambio:\*\*

\- \[ ] Pruebas de aceptación pasadas

\- \[ ] Modo mantenimiento desactivado

\- \[ ] Monitoreo estable

\- \[ ] Stakeholders notificados

\- \[ ] Documentación actualizada

\- \[ ] Ticket cerrado



---



\## Escalamiento



| Situación | Contacto | Tiempo |
|-----------|----------|--------|
| Cambio normal con problemas menores | Team Lead | Inmediato |
| Cambio con impacto a usuarios | Service Manager | < 15 min |
| Rollback requerido | Change Manager + CTO | Inmediato |
| Outage en producción | Incident Commander | Inmediato |


---



\## Métricas y KPIs



\- Tasa de éxito de cambios: > 95%

\- Tiempo promedio de implementación

\- Cantidad de rollbacks: < 5%

\- Incidentes causados por cambios: 0

\- Adherencia a ventanas de mantenimiento: 100%



---



\## Registro de Cambios



| Versión | Fecha | Autor | Descripción |
|---------|-------|-------|-------------|
| 1.0 | 2024-02-18 | Daniela Vera | Versión inicial |



---



\## Referencias



\- ITIL Change Management Best Practices

\- Política de Gestión de Cambios de la Organización

\- Procedimientos de Backup y Restore

\- Matriz de Escalamiento de TI

