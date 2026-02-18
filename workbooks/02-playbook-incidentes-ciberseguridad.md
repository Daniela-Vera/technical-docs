\# Playbook: Respuesta a Incidentes de Ciberseguridad



\## Información del Documento



| Campo | Valor |

|-------|-------|

| \*\*Versión\*\* | 1.0 |

| \*\*Fecha última actualización\*\* | 2024-02-18 |

| \*\*Propietario\*\* | Daniela Vera |

| \*\*Clasificación\*\* | Confidencial |

| \*\*Nivel de criticidad\*\* | Crítico |



---



\## Objetivo



Este playbook establece el procedimiento de respuesta ante incidentes de seguridad informática para garantizar una respuesta coordinada, eficiente y documentada que minimice el impacto al negocio.



---



\## Alcance



\- Brechas de seguridad

\- Malware y ransomware

\- Accesos no autorizados

\- Fuga de información

\- Ataques DDoS

\- Phishing y ingeniería social

\- Vulnerabilidades críticas explotadas



---



\## Clasificación de Incidentes



| Severidad | Descripción | Tiempo de Respuesta |

|-----------|-------------|---------------------|

| \*\*Crítica\*\* | Compromiso de sistemas core, exfiltración masiva de datos | Inmediato (< 15 min) |

| \*\*Alta\*\* | Acceso no autorizado a sistemas sensibles, malware detectado | < 1 hora |

| \*\*Media\*\* | Intento de acceso no autorizado, vulnerabilidad explotable | < 4 horas |

| \*\*Baja\*\* | Anomalías menores, intentos fallidos de ataque | < 24 horas |



---



\## Fases de Respuesta



\### Fase 1: Detección e Identificación



\#### 1.1 Detección del Incidente

\- Alertas de SIEM/SOC

\- Reportes de usuarios

\- Escaneos de vulnerabilidades

\- Monitoreo de red anómalo



\#### 1.2 Análisis Inicial

\*\*Preguntas clave a responder:\*\*

\- ¿Qué sistema(s) están afectados?

\- ¿Cuándo se detectó el incidente?

\- ¿Cuál es el vector de ataque?

\- ¿Hay evidencia de exfiltración de datos?

\- ¿Cuántos usuarios/sistemas están comprometidos?



\#### 1.3 Clasificación

Asignar severidad según tabla de clasificación y activar el equipo correspondiente.



---



\### Fase 2: Contención



\#### 2.1 Contención Inmediata (Short-term)



\*\*Para accesos no autorizados:\*\*

```bash

\# Bloquear IP atacante en firewall

iptables -A INPUT -s \[IP\_ATACANTE] -j DROP



\# Deshabilitar cuenta comprometida

net user \[usuario] /active:no

```



\*\*Para malware/ransomware:\*\*

\- Aislar host de la red inmediatamente

\- Deshabilitar acceso remoto

\- NO apagar el equipo (preservar memoria RAM para forenses)



\*\*Para phishing:\*\*

\- Bloquear dominio malicioso en gateway

\- Eliminar correos relacionados de buzones

\- Resetear credenciales de usuarios afectados



\#### 2.2 Contención a Largo Plazo

\- Implementar parches de seguridad

\- Cambiar credenciales de sistemas afectados

\- Revisar logs para identificar alcance completo

\- Establecer monitoreo adicional



---



\### Fase 3: Erradicación



\#### 3.1 Eliminación de la Amenaza



\*\*Malware:\*\*

```bash

\# Escaneo completo con antivirus actualizado

/opt/antivirus/scan --full --quarantine



\# Verificar procesos sospechosos

ps aux | grep -E "unusual|suspicious"

netstat -antp | grep ESTABLISHED

```



\*\*Backdoors:\*\*

\- Revisar tareas programadas

\- Verificar cuentas de usuario no autorizadas

\- Analizar modificaciones a archivos de sistema

\- Revisar claves de registro (Windows)



\#### 3.2 Validación

\- Escaneo de vulnerabilidades post-limpieza

\- Revisión de logs de seguridad

\- Confirmación con equipo de seguridad



---



\### Fase 4: Recuperación



\#### 4.1 Restauración de Servicios

\- Restaurar desde backups verificados (si es necesario)

\- Aplicar configuraciones de seguridad reforzadas

\- Reintegrar sistemas a producción gradualmente

\- Monitorear comportamiento post-recuperación



\#### 4.2 Validación de Funcionalidad

\- Pruebas de conectividad

\- Verificación de aplicaciones críticas

\- Confirmación con usuarios finales



---



\### Fase 5: Lecciones Aprendidas



\#### 5.1 Análisis Post-Incidente (dentro de 72 horas)



\*\*Preguntas a responder:\*\*

\- ¿Cómo ocurrió el incidente?

\- ¿Fue efectiva la respuesta?

\- ¿Qué se puede mejorar?

\- ¿Se requieren controles adicionales?

\- ¿Qué medidas preventivas implementar?



\#### 5.2 Documentación

\- Timeline completo del incidente

\- Acciones tomadas y resultados

\- Evidencia forense recopilada

\- Recomendaciones de mejora

\- Actualización de este playbook



---



\## Equipo de Respuesta



| Rol | Responsabilidades | Contacto |

|-----|-------------------|----------|

| \*\*Incident Commander\*\* | Coordinar respuesta, tomar decisiones críticas | \[Nombre] - ext. 5001 |

| \*\*Security Analyst\*\* | Análisis técnico, forense, contención | \[Nombre] - ext. 5002 |

| \*\*IT Operations\*\* | Soporte técnico, restauración de sistemas | \[Nombre] - ext. 5003 |

| \*\*Legal/Compliance\*\* | Aspectos legales, notificaciones regulatorias | \[Nombre] - ext. 5004 |

| \*\*Communications\*\* | Comunicación interna y externa | \[Nombre] - ext. 5005 |



---



\## Herramientas y Recursos



\### Software

\- SIEM: Splunk / ELK Stack

\- EDR: CrowdStrike / Carbon Black

\- Análisis forense: FTK, EnCase

\- Análisis de malware: VirusTotal, ANY.RUN



\### Documentos de Referencia

\- Plan de Continuidad de Negocio

\- Políticas de Seguridad de la Información

\- Procedimientos de Backup y Restore

\- Matriz de Escalamiento



---



\## Comunicación



\### Notificaciones Internas

\- \*\*Crítico/Alto:\*\* Notificar a CIO, CISO, Legal inmediatamente

\- \*\*Medio:\*\* Notificar a gerencia de IT dentro de 4 horas

\- \*\*Bajo:\*\* Reporte diario consolidado



\### Notificaciones Externas

\- \*\*Clientes afectados:\*\* Según lo determine Legal/Compliance

\- \*\*Autoridades:\*\* Si hay obligación regulatoria (GDPR, HIPAA, etc.)

\- \*\*Proveedores:\*\* Si sus sistemas están involucrados



---



\## Checklist de Respuesta Rápida



\*\*Primeros 15 minutos:\*\*

\- \[ ] Confirmar el incidente

\- \[ ] Clasificar severidad

\- \[ ] Activar equipo de respuesta

\- \[ ] Iniciar documentación (hora, hallazgos iniciales)

\- \[ ] Aislar sistemas comprometidos



\*\*Primera hora:\*\*

\- \[ ] Análisis de alcance

\- \[ ] Contención activa

\- \[ ] Notificaciones a stakeholders

\- \[ ] Preservar evidencia



\*\*Primeras 24 horas:\*\*

\- \[ ] Erradicación de amenaza

\- \[ ] Inicio de recuperación

\- \[ ] Actualización a management

\- \[ ] Monitoreo continuo



---



\## Registro de Cambios



| Versión | Fecha | Autor | Descripción |

|---------|-------|-------|-------------|

| 1.0 | 2024-02-18 | Daniela Vera | Versión inicial |



---



\## Notas de Cumplimiento



Este playbook debe revisarse y actualizarse:

\- Cada 6 meses

\- Después de cada incidente significativo

\- Cuando cambien regulaciones aplicables

\- Al implementar nuevas tecnologías de seguridad

