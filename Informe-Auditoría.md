# Informe de Auditoría de Seguridad

## Cliente

- **Empresa Auditora:** AGARTI Technologies
- **Cliente:** NEBULA Inc.
- **Proyecto:** Auditoría Integral - Operación Eclipse
- **Fecha de emisión:** 26/06/2026
- **Auditor Jefe:** Alejandro Gutiérrez Alañón
- **Clasificación del Documento:** CONFIDENCIAL

![](Evidencias_Visuales/D.D)

---

## Resumen Ejecutivo
Tras realizar la auditoría de seguridad basada en pruebas de penetración y análisis de logs para reconstruir la línea de tiempo del incidente, se concluye que la postura de seguridad de la infraestructura actual es crítica, ya que se han identificado y evaluado un total de 4 hallazgos, de los cuales 1 presenta un nivel de riesgo crítico y 3 presentan un nivel de riesgo muy alto por lo que la organización sigue expuesta a que ocurra de nuevo un ciberataque con una exilftración masiva de datos, con una probabilidad muy elevada en el corto plazo.

El principal problema reside en el uso de software obsoleto y sin parches de seguridad, lo que convierte los sistemas críticos de la empresa en un blanco fácil para atacantes. Durante la fase de pentesting, se logró explotar con éxito la vulnerabilidad CVE-2024-27198 en TeamCity 2023.11.3, una vulnerabilidad crítica con un base score de 9.8, que permitió crear un usuario administrador no autorizado (AGARTI), inyectar tokens de acceso y obtener una shell inversa en el servidor, adquiriendo control completo sobre toda la infraestructura. Además, se identificaron versiones obsoletas de OpenSSH 8.2p1, Apache 2.4.41 y Ubuntu 20.04.6 LTS, que presentan vulnerabilidades conocidas y sin parchear.

En cuanto al impacto directo en el negocio, si la organización no soluciona estos riesgos de forma inmediata, las consecuencias podrían significar la pérdida completa del negocio, ya que a nivel financiero, un ataque exitoso podría paralizar la operativa durante días y no solamente la exilftración de los datos como ocurrio previamente, con la consiguiente pérdida de ingresos, costes de recuperación forense y posibles multas regulatorias. La evidencia mostrada en el SIEM también confirma que ya se han producido intrusiones reales, con la creación del usuario malicioso eviluser (el 4 de julio a las 22:32:37), la instalación del paquete malicioso datacollector (versión amd64 1.0) y la implantación del plugin no autorizado AyzzbuXY, así como el uso de una clave pública comprometida (ecXxIHdpi9cpIPbjewybKqpDqrM1bw/OlKeuDT6rmzc) procedente de la IP atacante 10.11.75.247, evidenciando que el sistema ya ha sido comprometido activamente utilizando, posiblemente, la misma falla de seguridad identificada.

## Alcance y Metodología

### Sistemas evaluados
Se ha auditado el servidor principal con sistema operativo Ubuntu 20.04.6 LTS (Focal Fossa), el servicio de integración continua TeamCity 2023.11.3 (build 147512), el servidor web Apache/2.4.41 y el servicio de administración remota OpenSSH 8.2p1. También se han revisado las configuraciones de seguridad, los parches aplicados, las comunicaciones internas y externas del entorno, así como los mecanismos de autenticación y control de acceso implementados en la infraestructura evaluada.

### Marcos normativos aplicados
- Metodología MAGERIT v3
- Controles del Esquema Nacional de Seguridad (ENS)

### Herramientas utilizadas
- Inspección manual de la configuración de TeamCity
- Splunk SIEM
- Metasploit

## Hallazgos Críticos

### Hallazgo 1

- **Nombre del Hallazgo:** Versión de TeamCity obsoleta y con vulnerabilidades críticas conocidas
- **Nivel de Riesgo:** Crítico
- **Condición:** Se detectó la versión 2023.11.3 (build 147512) en el entorno de producción.
- **Criterio:** La versión instalada debe ser la más reciente estable que incluya parches de seguridad y correcciones de fallos críticos.
- **Causa:** Falta de un proceso formal de actualización y mantenimiento continuo del software, lo que ha derivado en un desfase significativo respecto a las versiones seguras disponibles.
- **Consecuencia e Impacto:** Exposición a vulnerabilidades públicas documentadas (CVE-2024-27198) y (CVE-2024-27199) que permiten a un atacante ejecutar código remoto, escalar privilegios y tomar el control completo del servidor, comprometiendo la confidencialidad, integridad y disponibilidad de los activos asociados.
- **Plan de Acción:** Actualizar de manera inmediata a la versión 2026.1 o superior, previa validación en un entorno de pruebas, y establecer una política de actualizaciones periódicas programadas.

### Hallazgo 2

- **Nombre del Hallazgo:** Versión de OpenSSH obsoleta con vulnerabilidades sin parchear
- **Nivel de Riesgo:** Crítico
- **Condición:** Se encuentra instalada la versión 8.2p1 del servicio SSH.
- **Criterio:** La versión instalada debe ser la más reciente estable que incluya parches de seguridad y correcciones de fallos críticos.
- **Causa:** Ausencia de un ciclo de actualización planificado para componentes críticos de la infraestructura, y posible dependencia de versiones antiguas por compatibilidad no evaluada.
- **Consecuencia e Impacto:** Riesgo de explotación de fallos de seguridad documentados en versiones anteriores, lo que podría permitir ataques de denegación de servicio, ejecución de comandos o acceso no autorizado al sistema.
- **Plan de Acción:** Actualizar OpenSSH a la versión 10.3 o superior, asegurando la compatibilidad con los sistemas dependientes, y validar la configuración post-actualización para mantener los estándares de seguridad.

### Hallazgo 3

- **Nombre del Hallazgo:** Versión de Apache obsoleta
- **Nivel de Riesgo:** Muy Alto
- **Condición:** Se identificó la versión Apache/2.4.41 en el servidor web.
- **Criterio:** La versión instalada debe ser la más reciente estable que incluya parches de seguridad y correcciones de fallos críticos.
- **Causa:** Falta de monitoreo activo de las actualizaciones de seguridad del servidor web y posible ausencia de un gestor de paquetes actualizado en el sistema.
- **Consecuencia e Impacto:** Exposición a múltiples vulnerabilidades conocidas (como desbordamientos de búfer, divulgación de información y ejecución remota), que podrían comprometer la integridad del servidor y la información transmitida.
- **Plan de Acción:** Actualizar Apache HTTP Server a la versión 2.4.68 o a la más reciente estable, realizando pruebas de regresión en un entorno controlado antes del despliegue en producción.

### Hallazgo 4

- **Nombre del Hallazgo:** Versión del sistema operativo Linux desactualizada
- **Nivel de Riesgo:** Muy Alto
- **Condición:** El sistema opera con la distribución Ubuntu 20.04.6 LTS (Focal Fossa).
- **Criterio:** La versión instalada debe ser la más reciente estable que incluya parches de seguridad y correcciones de fallos críticos.
- **Causa:** Ciclo de actualización del sistema operativo no alineado con el calendario de lanzamientos LTS de Ubuntu, posiblemente por falta de recursos o planificación.
- **Consecuencia e Impacto:** Fin del soporte estándar y reducción en la disponibilidad de actualizaciones de seguridad críticas, lo que incrementa la superficie de ataque y dificulta el cumplimiento normativo.
- **Plan de Acción (Recomendación):** Planificar y ejecutar la migración a Ubuntu 26.04 LTS, incluyendo un análisis de compatibilidad de aplicaciones, respaldo completo de datos y un plan de contingencia ante posibles incidencias durante la actualización

## Anexos Técnicos

### Recopilación de evidencias

- [Informe de Pentesting](Red-Team.md)
- [Análisis en Splunk](Blue-Team.md)

### Diagrama de la arquitectura del sistema

```bash
                                                ┌────────┐     ┌────────┐
                                                │ WEB 1  │     │ WEB 2  │
                                       ((WB-1)) │ maint. │     │TeamCity│ ((WB-2))
                                                └───┬────┘     └───┬────┘ 
                                                    │              │
                                                ┌───▼────┐     ┌───▼────┐
                                       ((MD-2)) │ APACHE │     │ TOMCAT │ ((MD-3))  
                                ┌─────────┐     └───┬────┘     └───┬────┘ 
                       ((MD-1)) │ OpenSSH │         │ pt. 80       │ pt. 50000
                                └────┬────┘         └──────┬───────┘
                                     │ pt. 22              │ 
                                ┌────▼────┐           ┌────▼────┐               
                      ((COM-1)) │   SSH   │           │  HTTP   │ ((COM-2))          
                                └─────────┘           └─────────┘      
                                     │                     │      
                                     └──────────┼──────────┘
                                                │
                                        ┌───────▼───────┐
                         ((AC-1)) ──────│ Ubuntu Server │ ((SO-1)) 
                                        └───────────────┘
```

### Tablas de Auditoría

#### 🔴 Identificación y Valoración de Activos (Post-Explotación)
##### 🟢 Valor propio
###### MAGERIT 
| Ref.   | Activo              | C | I | A | Au | T |
|--------|---------------------|---|---|---|----|---|
| AC-1  | Archivos confidenciales    | 10 | 6 | 2 | 9 | 8 |
| WB-1  | App Web (TeamCity)         | 10 | 8 | 4 | 10 | 4 |
| WB-2  | App Web (Mantenimiento)    | 1 | 1 | 1 | 1  | 1 |
| MD-1  | Middleware (Open SSH)      | 10 | 9 | 4 | 10 | 5 |
| MD-2  | Middleware (Apache httpd)  | 4 | 2 | 1 | 2  | 3 |
| MD-3  | Middleware (Apache tomcat) | 10 | 8 | 4 | 9 | 4 |
| COM-1  | Comunicaciones (SSH)      | 10 | 9 | 4 | 10 | 4 |
| COM-2  | Comunicaciones (HTTP)     | 10 | 9 | 4 | 10 | 5 |
| SO-1   | Servidor Ubuntu           | 9 | 3 | 2 | 9  | 6 |
###### ENS
```bash
0 a 4: Nivel Bajo (B) - Un fallo aquí apenas afecta a la empresa.
5 a 7: Nivel Medio (M) - Un fallo aquí causa problemas notables, pero superables.
8 a 10: Nivel Alto (A) - Un fallo aquí es crítico (multas legales, pérdida total de negocio).
```
| Ref.   | Activo                       | C  | I  | A  | Au | T  |
|--------|------------------------------|----|----|----|----|----|
| AC-1   | Archivos confidenciales      | A  | M  | B  | A  | A  |
| WB-1   | App Web (TeamCity)           | A  | A  | B  | A  | B  |
| WB-2   | App Web (Mantenimiento)      | B  | B  | B  | B  | B  |
| MD-1   | Middleware (OpenSSH)         | A  | A  | B  | A  | M  |
| MD-2   | Middleware (Apache httpd)    | B  | B  | B  | B  | B  |
| MD-3   | Middleware (Apache Tomcat)   | A  | A  | B  | A  | B  |
| COM-1  | Comunicaciones (SSH)         | A  | A  | B  | A  | B  |
| COM-2  | Comunicaciones (HTTP)        | A  | A  | B  | A  | M  |
| SO-1   | Servidor Ubuntu              | A  | B  | B  | A  | M  |

##### 🟢 Valor acumulado
###### MAGERIT
| Ref.   | Activo              | C | I | A | Au | T |
|--------|---------------------|---|---|---|----|---|
| AC-1  | Archivos confidenciales    | 10 | 6 | 2 | 9 | 8 |
| WB-1  | App Web (TeamCity)         | 10 | 8 | 4 | 10 | 8 |
| WB-2  | App Web (Mantenimiento)    | 10 | 8 | 4 | 10  | 8 |
| MD-1  | Middleware (Open SSH)      | 10 | 9 | 4 | 10 | 8 |
| MD-2  | Middleware (Apache httpd)  | 10 | 9 | 4 | 10  | 8 |
| MD-3  | Middleware (Apache tomcat) | 10 | 9 | 4 | 10 | 8 |
| COM-1  | Comunicaciones (SSH)      | 10 | 9 | 4 | 10 | 8 |
| COM-2  | Comunicaciones (HTTP)     | 10 | 9 | 4 | 10 | 8 |
| SO-1   | Servidor Ubuntu           | 10 | 9 | 4 | 10  | 8 |

###### ENS
| Ref.   | Activo                       | C  | I  | A  | Au | T  |
|--------|------------------------------|----|----|----|----|----|
| AC-1   | Archivos confidenciales      | A  | M  | B  | A  | A  |
| WB-1   | App Web (TeamCity)           | A  | A  | B  | A  | A  |
| WB-2   | App Web (Mantenimiento)      | A  | A  | B  | A  | A  |
| MD-1   | Middleware (OpenSSH)         | A  | A  | B  | A  | A  |
| MD-2   | Middleware (Apache httpd)    | A  | A  | B  | A  | A  |
| MD-3   | Middleware (Apache Tomcat)   | A  | A  | B  | A  | A  |
| COM-1  | Comunicaciones (SSH)         | A  | A  | B  | A  | A  |
| COM-2  | Comunicaciones (HTTP)        | A  | A  | B  | A  | A  |
| SO-1   | Servidor Ubuntu              | A  | A  | B  | A  | A  |

#### 🔴 Identificación de Amenazas
| Ref.  | Hallazgo                                | Activo Afectado | Código Amenaza | Justificación |
|-------|-----------------------------------------|----------------|---------------|--------------|
| AM-01 | Versión obsoleta de PHP (7.0.33-0ubuntu0.16.04.16)        | SW-2 (Middleware)          | S.21         | Al ser una versión obsoleta del 2018 que ya no recibe parches de seguridad , se expone tanto al servidor, como a la página web a recibir ataques críticos, en donde desctacan vulnerabilidades como la inyección de Comando (CVE-2018-19518) en donde un fallo crítico en la función imap_open, permite a un atacante remoto ejecutar comandos arbitrarios en el servidor enviando un nombre de servidor IMAP manipulado. |
| AM-02 | Actualización de WordPress (7.0)        | SW-1 (Aplicación Web)          | S.21         | La versión actual de WordPress (6.5.8) posee múltiples vulnerabilidades, entre las que destacan una vulnerabilidad de tipo SSRF (Server-Side Request Forgery) que permite que un atacante obligue al servidor a hacer peticiones internas o externas o también una vulnerabilidad de tipo XSS (Cross-Site Scripting) que permite el secuestro de cuentas y el robo de cookies o sesiones.|
| AM-03 | Eliminación de temas inactivos          | SW-1 (Aplicación Web)         | S.24         | Los temas inactivos pueden tener vulnerabilidades de seguridad, y al estar fuera de uso, es más probable que no se actualicen y sus versiones presenten graves problemas de seguridad. |
| AM-04 | Módulos recomendados faltantes          | SW-1 (Aplicación Web)         | S.22         | La ausencia de los módulos recomendados, en si no representa un fallo crítico de seguridad, sin embargo, la configuración recomendada y la instalación de estos módulos permite reforzar la seguridad y el funcionamiento del sitio.  |

En donde los códigos de las amenazas están dados por: 
- **[S.21] Vulnerabilidades de los programas:** Uso de software obsoleto, sin actualizar o que ya no recibe parches de seguridad de sus creadores.
- **[S.22] Errores de configuración / Faltas funcionales:** Ausencia de componentes, librerías o módulos necesarios para que el sistema funcione correctamente y de forma segura.
- **[S.24] Deficiencias de mantenimiento:** Dejar instalados componentes, plugins o temas que no se usan. Esto aumenta la "superficie de ataque".
- **[A.11] Degradación del servicio:** Fallos o configuraciones pobres que hacen que el sistema vaya muy lento o pueda colapsar si recibe muchas visitas de golpe

#### 🔴 Evaluación de Riesgos
##### 🟢 Cálculo de Probabilidad
| Ref.  | Atracción | Facilidad | Accesibilidad | Probabilidad | P. Cualitativa |
|--------|-----------|------------|---------------|--------------|----------------|
| AM-01 | 8 | 9 | 10 | 9,0 | Crítica |
| AM-02 | 9 | 8 | 10 | 9,0 | Crítica |
| AM-03 | 6 | 7 | 8  | 7,0 | Alta |
| AM-04 | 2 | 2 | 5  | 3,0 | Baja |
| AM-05 | 9 | 8 | 7  | 8,0 | Crítica |
| AM-06 | 5 | 8 | 10 | 7,7 | Alta |
##### 🟢 Cálculo de Impacto
| Ref.  | Activo Afectado | Dimensión Principal Afectada | Valor Acumulado | Degradación | Impacto |
|--------|----------------|------------------------------|-----------------|-------------|---------|
| AM-01 | SW-2 (Middleware) | I | 9 | 100% | 9.0 |
| AM-02 | SW-1 (Aplicación Web) | C | 10 | 80%  | 8.0 |
| AM-03 | SW-1 (Aplicación Web) | I | 9 | 50%  | 4.5 |
| AM-04 | SW-1 (Aplicación Web) | I | 9 | 20%  | 1.8 |
| AM-05 | INF-1 (Datos de negocio) | C | 10 | 100% | 10.0 |
| AM-06 | HW-1 (Servidor Ubuntu) | A | 8 | 70%  | 5.6 | 

##### 🟢 Cálculo de Riesgo
| Prioridad   | Ref.  | Activo Afectado              | Dimensión Principal Afectada | Degradación | Impacto | Probabilidad | Riesgo Potencial | Riesgo Cualitativo |
|-------------|-------|------------------------------|------------------------------|-------------|----------|--------------|------------------|--------------------|
| 1º Mayor    | AM-01 | SW-2 (Middleware)            | I                            | 100%        | 9.0      | 9,0          | 81.0             | Muy Alto / Crítico |
| 2º          | AM-05 | INF-1 (Datos de negocio)     | C                            | 100%        | 10.0     | 8,0          | 80.0             | Alto               |
| 3º          | AM-02 | SW-1 (Aplicación Web)        | C                            | 80%         | 8.0      | 9,0          | 72.0             | Alto               |
| 4º          | AM-06 | HW-1 (Servidor Ubuntu)       | A                            | 70%         | 5.6      | 7,7          | 43.12            | Medio              |
| 5º          | AM-03 | SW-1 (Aplicación Web)        | I                            | 50%         | 4.5      | 7,0          | 31.5             | Medio              |
| 6º          | AM-04 | SW-1 (Aplicación Web)        | I                            | 20%         | 1.8      | 3,0          | 5.4              | Bajo               |
#### 🔴 Selección de Salvaguardas
##### 🟢 Plan de Tratamiento del Riesgo
| Ref. | Hallazgo                          | Código ENS | Acción Técnica Recomendada | ¿Qué reduce? |
|------|----------------------------------|------------|----------------------------|--------------|
| AM-01 | Versión obsoleta de PHP         | [op.exp.8] Gestión de vulnerabilidades y actualizaciones     | Actualizar a versión soportada y aplicar parches de seguridad | Probabilidad |
| AM-05 | Servidor SQL obsoleto           | [op.exp.8] Gestión de vulnerabilidades y actualizaciones      | Actualizar motor de base de datos a versión soportada y parcheada | Probabilidad |
| AM-02 | Actualización de WP disponible  | [op.exp.8] Gestión de vulnerabilidades y actualizaciones      | Actualizar WordPress a la última versión estable | Probabilidad |
| AM-06 | No se ha detectado caché        | [op.pl.1] Arquitectura de seguridad      | Implementar sistema de caché | Impacto |
| AM-03 | Temas inactivos instalados      | [op.exp.2] Configuración de seguridad      | Eliminar temas no utilizados y mantener solo los necesarios | Probabilidad |
| AM-04 | Módulos recomendados faltantes   | [op.pl.1] Arquitectura de seguridad      | Instalar plugins o módulos de seguridad recomendados | Probabilidad |
##### 🟢 Plan de Tratamiento del Riesgo Residual
| Ref. | Hallazgo                          | Acción Técnica Recomendada | Eficacia (e) | Riesgo Residual  | Riesgo Residual Cualitativo |
|------|----------------------------------|----------------------------|--------------|-----------------|-----------------------------|
| AM-01 | Versión obsoleta de PHP         | Actualizar a versión soportada y aplicar parches de seguridad                      | 80%          | 16.2           | Bajo                       |
| AM-05 | Servidor SQL obsoleto           | Actualizar motor de base de datos a versión soportada y parcheada                     | 80%          | 16.0           | Bajo                       |
| AM-02 | Actualización de WP disponible   | Actualizar WordPress a la última versión estable                      | 70%          | 21.6           | Medio                       |
| AM-06 | No se ha detectado caché        | Implementar sistema de caché                      | 50%          | 21.56           | Medio                       |
| AM-03 | Temas inactivos instalados      | Eliminar temas no utilizados y mantener solo los necesarios                      | 90%          | 3.15           | Bajo                      |
| AM-04 | Faltan módulos recomendados     | Instalar plugins o módulos de seguridad recomendados                      | 100%         | 0          | Bajo                      |
