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
Tras realizar la auditoría de seguridad basada en pruebas de penetración y análisis de logs para reconstruir la línea de tiempo del incidente, se concluye que la postura de seguridad de la infraestructura actual es crítica, ya que se han identificado y evaluado un total de 5 hallazgos, de los cuales 2 presentan un nivel de riesgo crítico y 3 presentan un nivel de riesgo muy alto por lo que la organización sigue expuesta a que ocurra de nuevo un ciberataque con una exilftración masiva de datos, con una probabilidad muy elevada en el corto plazo.

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

### Hallazgo 5

- **Nombre del Hallazgo:** Firewall UFW inoperante o instalado de manera incompleta
- **Nivel de Riesgo:** Crítico
- **Condición:** El servicio ufw.service se encuentra activo según systemctl, sin embargo, el binario ufw no está disponible en el sistema (ufw: not found). El comando sudo ufw status reporta Status: inactive, lo que indica una instalación inconsistente o corrupta del paquete UFW.
- **Criterio:** El sistema debe contar con un firewall funcional y correctamente configurado que gestione el tráfico entrante y saliente con políticas de denegación por defecto y reglas específicas para servicios autorizados.
- **Causa:** Instalación incompleta o dañada del paquete ufw, posiblemente por interrupciones durante la instalación, dependencias rotas o limpieza manual inadecuada de binarios del sistema.
- **Consecuencia e Impacto:** Ausencia de un firewall activo que filtre el tráfico de red, exponiendo todos los puertos y servicios del sistema a posibles ataques externos. Esto incrementa significativamente la superficie de ataque, facilita accesos no autorizados, y compromete la confidencialidad, integridad y disponibilidad de los datos y servicios alojados en el servidor.
- **Plan de Acción (Recomendación):** Una vez que se hayan actualizado los demás componentes, instalar o actualizar el firewall y de ser posible, contrarar alguna solución de firewall de compañias especializadas como PaloAlto Networks o Fortinet.

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
| Ref. | Hallazgo | Activo Afectado | Código Amenaza | Justificación |
|------|----------|----------------|----------------|---------------|
| AM-01 | Versión de TeamCity obsoleta y con vulnerabilidades críticas conocidas (2023.11.3) | WB-1 (App Web, TeamCity) | S.21 | La versión 2023.11.3 de TeamCity presenta vulnerabilidades críticas documentadas (CVE-2024-27198 y CVE-2024-27199) que permiten ejecución remota de código sin autenticación, con un base score de 9.8. Durante el pentesting se explotó con éxito esta vulnerabilidad, creando un usuario administrador no autorizado (AGARTI) y obteniendo una shell inversa. La falta de un proceso formal de actualización ha dejado el software obsoleto y expuesto a exploits públicos disponibles en Metasploit. |
| AM-02 | Versión de OpenSSH obsoleta con vulnerabilidades sin parchear | MD-1 (Middleware, OpenSSH) | S.21 | OpenSSH 8.2p1 es una versión anterior a la 8.8, que incluye parches para vulnerabilidades críticas como CVE-2021-41617 (fallo en la validación de certificados) y otros problemas de seguridad documentados. Al no estar actualizado, el servicio de administración remota queda expuesto a ataques de denegación de servicio, ejecución de comandos y acceso no autorizado. La ausencia de un ciclo de actualización planificado para componentes críticos agrava el riesgo. |
| AM-03 | Versión de Apache obsoleta | MD-2 (Middleware, Apache httpd) | S.21 | Apache 2.4.41 es una versión que ha quedado obsoleta frente a las versiones estables actuales (2.4.68+). Presenta vulnerabilidades conocidas como desbordamientos de búfer, divulgación de información y posibles ejecuciones remotas de código (CVE-2021-40438, CVE-2021-44224, entre otras). La falta de monitoreo activo de actualizaciones de seguridad y la posible ausencia de un gestor de paquetes actualizado han permitido que el servidor web permanezca en una versión insegura. |
| AM-04 | Versión del sistema operativo Linux desactualizada | SO-1 (Servidor Ubuntu) | S.21 | Ubuntu 20.04.6 LTS (Focal Fossa) ya no presenta actualizaciones de seguridad, lo que implica una reducción en la disponibilidad de actualizaciones de seguridad críticas para el kernel y los paquetes del sistema. Esto incrementa la superficie de ataque del servidor, dejándolo expuesto a vulnerabilidades del sistema operativo que ya han sido parcheadas en versiones posteriores. El ciclo de actualización del SO no está alineado con el calendario de lanzamientos LTS de Ubuntu. |
| AM-05 | Configuración o implementación incorrecta del sistema de firewall | COM-1 y COM-2 (Comunicaciones) | F.14 | La ausencia del firewall permite que los puertos de comunicación (SSH en COM-1 y HTTP/HTTPS en COM-2) estén accesibles desde redes externas sin restricciones. Esto facilita el acceso no autorizado y la explotación de vulnerabilidades en los servicios expuestos. La evidencia en el SIEM muestra conexiones desde la IP maliciosa 10.11.75.247 y la implantación de un plugin no autorizado (AyzzbuXY), confirmando que el perímetro no está protegiendo adecuadamente la infraestructura. |

En donde los códigos de las amenazas están dados por: 
- **[S.21] Vulnerabilidades de los programas:** Uso de software obsoleto, sin actualizar o que ya no recibe parches de seguridad de sus creadores.
- **[F.14] Fallo en los controles de acceso perimetral:** Ausencia o mala configuración del firewall que permite el acceso no autorizado desde redes externas y control de las conexiones desde las redes internas al exterior.

#### 🔴 Evaluación de Riesgos
##### 🟢 Cálculo de Probabilidad

| Ref.  | Atracción | Facilidad | Accesibilidad | Probabilidad | P. Cualitativa |
|--------|-----------|------------|---------------|--------------|----------------|
| AM-01 | 10 | 10 | 9 | 9,7 | Crítica |
| AM-02 | 9 | 8 | 9 | 8,7 | Crítica |
| AM-03 | 8 | 7 | 9 | 8,0 |  Crítica |
| AM-04 | 6 | 5 | 7  | 6,7 | Alta |
| AM-05 | 9 | 9 | 8  | 8,7 | Crítica |

##### 🟢 Cálculo de Impacto
| Ref.  | Activo Afectado | Dimensión Principal Afectada | Valor Acumulado | Degradación | Impacto |
|--------|----------------|------------------------------|-----------------|-------------|---------|
| AM-01 | WB-1 (App Web, TeamCity)        | C y Au  | 10 | 100% | 10.0 |
| AM-02 | MD-1 (Middleware, OpenSSH)      | C y Au  | 10 | 85%  | 8.5 |
| AM-03 | MD-2 (Middleware, Apache httpd) | C y Au  | 10 | 10%  | 1.0 |
| AM-04 | SO-1 (Servidor Ubuntu)          | C y Au  | 10 | 90%  | 9.0 |
| AM-05 | COM-1 y COM-2 (Comunicaciones)  | C y Au  | 10 | 95% | 9.5 |

##### 🟢 Cálculo de Riesgo
| Prioridad   | Ref.  | Activo Afectado              | Dimensión Principal Afectada | Degradación | Impacto | Probabilidad | Riesgo Potencial | Riesgo Cualitativo |
|-------------|-------|------------------------------|------------------------------|-------------|----------|--------------|------------------|--------------------|
| 1º Mayor    | AM-01 | WB-1 (App Web, TeamCity)       | C y Au                     | 100%        | 10.0     | 9.7          | 97.0             | Muy Alto / Crítico |
| 2º          | AM-05 | COM-1 y COM-2 (Comunicaciones) | C y Au                     | 95%         | 9.5      | 8.7          | 82.7             | Muy Alto / Crítico |
| 3º          | AM-04 | SO-1 (Servidor Ubuntu)         | C y Au                     | 90%         | 9.0      | 6.7          | 60.3             | Alto               |
| 5º          | AM-02 | MD-1 (Middleware, OpenSSH)     | C y Au                     | 85%         | 8.5      | 8.7          | 73.9             | Alto               |
| 6º          | AM-03 | MD-2 (Middleware, Apache httpd)| C y Au                     | 10%         | 1.0      | 8.0          | 8.0              | Bajo               |          
#### 🔴 Selección de Salvaguardas
##### 🟢 Plan de Tratamiento del Riesgo
| Ref. | Hallazgo                          | Código ENS | Acción Técnica Recomendada | ¿Qué reduce? |
|------|----------------------------------|------------|----------------------------|--------------|
| AM-01 | Versión de TeamCity obsoleta y con vulnerabilidades críticas conocidas (2023.11.3)         | [op.exp.4] Mantenimiento y actualizaciones de seguridad     | Actualizar TeamCity a la última versión estable que corrija las vulnerabilidades CVE-2024-27198 y CVE-2024-27199. Establecer un proceso formal y periódico de actualización de software que incluya la monitorización de vulnerabilidades críticas. | Probabilidad |
| AM-05 | Configuración o implementación incorrecta del sistema de firewall           | [op.exp.2] Configuración de seguridad      | Revisar y reconfigurar el firewall aplicando el principio de menor privilegio. Restringir el acceso a los puertos de administración (SSH) y aplicaciones (HTTP/HTTPS) únicamente a redes internas o IPs confiables. | Probabilidad e Impacto |
| AM-04 | Versión del sistema operativo Linux desactualizada  | [op.exp.4] Mantenimiento y actualizaciones de seguridad      | Migrar el sistema operativo a una versión de Ubuntu LTS con soporte activo. Configurar el repositorio de paquetes para recibir actualizaciones de seguridad de forma automática o programada. | Probabilidad |
| AM-02 | Versión de OpenSSH obsoleta con vulnerabilidades sin parchear      | [op.exp.4] Mantenimiento y actualizaciones de seguridad      | Actualizar OpenSSH a una versión superior a la 8.8 (preferiblemente la última estable). Implementar un cronograma de actualizaciones para el sistema operativo y sus componentes críticos. Revisar la configuración del servicio SSH para deshabilitar protocolos y cifrados obsoletos. | Probabilidad |
| AM-03 | Versión de Apache obsoleta   | [op.exp.4] Mantenimiento y actualizaciones de seguridad      | Actualizar Apache httpd a la versión estable más reciente (2.4.68+). Automatizar el proceso de parcheo de seguridad para el middleware. | Probabilidad |

Para obtener más información sobre la selección de medidas de seguridad del ENS consultar: https://boe.es/buscar/act.php?id=BOE-A-2022-7191 y seleccionar la versión del 2024 o posterior. Ver el ANEXO II: Medidas de Seguridad.

##### 🟢 Plan de Tratamiento del Riesgo Residual
| Ref. | Hallazgo                          | Acción Técnica Recomendada | Eficacia (e) | Riesgo Residual  | Riesgo Residual Cualitativo |
|------|----------------------------------|----------------------------|--------------|-----------------|-----------------------------|
| AM-01 | Versión de TeamCity obsoleta        | Actualizar TeamCity a la última versión estable que corrija las vulnerabilidades       | 85%          | 13.5           | Bajo       |
| AM-05 | Configuración o implementación incorrecta del sistema de firewall           | Revisar y reconfigurar el firewall aplicando el principio de menor privilegio. Restringir el acceso a puertos de administración y aplicaciones   | 90%          | 4.0           | Bajo    |
| AM-04 | Versión del sistema operativo Linux desactualizada   | Migrar el sistema operativo a una versión de Ubuntu LTS con soporte activo (22.04 LTS o 24.04 LTS).                   | 80%          | 21.6           | Medio          |
| AM-02 | Versión de OpenSSH obsoleta con vulnerabilidades sin parchear      | Actualizar OpenSSH a una versión superior a la 8.8.        | 85%          | 7.8           | Bajo      |
| AM-03 | Versión de Apache obsoleta     | Actualizar Apache httpd a la versión estable más reciente (2.4.68+). Automatizar el proceso de parcheo de seguridad para el middleware.                |80%         | 12.6          | Bajo                    |


---
---
# Tablas externas

## Cálculo del valor propio ENS
```bash
0 a 4: Nivel Bajo (B) - Poca afectación para la empresa.
5 a 7: Nivel Medio (M) - Causa problemas notables, pero superables.
8 a 10: Nivel Alto (A) - Fallo  crítico (multas legales, pérdida total de negocio).
```

## Cálculo de la probabilidad

|Probabilidad| Muy baja | Baja | Media | Alta | Crítica |
|---|---|---|---|---|---|
| Frecuencia | 0 - 1,9 | 2 - 3,9 | 4 - 5,9 | 6 - 7,9 | 8 - 10 |
|ARO |0 - 1,9| 2 - 3,9| 4 - 5,9 |6 - 7,9| 8 - 10
| Estadística | > 1 año | Cada año | Cada mes | Cada semana | Cada día |


## Matriz de Riesgo

| Efectos negativos producidos | Criticidad de los recursos: Muy baja | Criticidad: Baja | Criticidad: Media | Criticidad: Alta | Criticidad: Muy Alta |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Muy Bajo** | MUY BAJO | MUY BAJO | MUY BAJO | BAJO | BAJO |
| **Bajo** | MUY BAJO | BAJO | BAJO | MEDIO | MEDIO |
| **Medio** | BAJO | MEDIO | MEDIO | ALTO | ALTO |
| **Alto** | MEDIO | ALTO | ALTO | CRÍTICO | CRÍTICO |
| **Muy Alto** | ALTO | CRÍTICO | CRÍTICO | CRÍTICO | CRÍTICO |
