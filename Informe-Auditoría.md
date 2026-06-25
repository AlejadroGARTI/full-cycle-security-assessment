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
| Ref. | Hallazgo | Activo Afectado | Código Amenaza | Justificación |
|------|----------|----------------|----------------|---------------|
| AM-01 | Versión de TeamCity obsoleta y con vulnerabilidades críticas conocidas (2023.11.3) | WB-1 (App Web, TeamCity) | S.21 | La versión 2023.11.3 de TeamCity presenta vulnerabilidades críticas documentadas (CVE-2024-27198 y CVE-2024-27199) que permiten ejecución remota de código sin autenticación, con un base score de 9.8. Durante el pentesting se explotó con éxito esta vulnerabilidad, creando un usuario administrador no autorizado (AGARTI) y obteniendo una shell inversa. La falta de un proceso formal de actualización ha dejado el software obsoleto y expuesto a exploits públicos disponibles en Metasploit. |
| AM-02 | Versión de OpenSSH obsoleta con vulnerabilidades sin parchear | MD-1 (Middleware, OpenSSH) | S.21 | OpenSSH 8.2p1 es una versión anterior a la 8.8, que incluye parches para vulnerabilidades críticas como CVE-2021-41617 (fallo en la validación de certificados) y otros problemas de seguridad documentados. Al no estar actualizado, el servicio de administración remota queda expuesto a ataques de denegación de servicio, ejecución de comandos y acceso no autorizado. La ausencia de un ciclo de actualización planificado para componentes críticos agrava el riesgo. |
| AM-03 | Versión de Apache obsoleta | MD-2 (Middleware, Apache httpd) | S.21 | Apache 2.4.41 es una versión que ha quedado obsoleta frente a las versiones estables actuales (2.4.68+). Presenta vulnerabilidades conocidas como desbordamientos de búfer, divulgación de información y posibles ejecuciones remotas de código (CVE-2021-40438, CVE-2021-44224, entre otras). La falta de monitoreo activo de actualizaciones de seguridad y la posible ausencia de un gestor de paquetes actualizado han permitido que el servidor web permanezca en una versión insegura. |
| AM-04 | Versión del sistema operativo Linux desactualizada | SO-1 (Servidor Ubuntu) | S.21 | Ubuntu 20.04.6 LTS (Focal Fossa) ya no presenta actualizaciones de seguridad, lo que implica una reducción en la disponibilidad de actualizaciones de seguridad críticas para el kernel y los paquetes del sistema. Esto incrementa la superficie de ataque del servidor, dejándolo expuesto a vulnerabilidades del sistema operativo que ya han sido parcheadas en versiones posteriores. El ciclo de actualización del SO no está alineado con el calendario de lanzamientos LTS de Ubuntu. |
| AM-05 | Configuración o implementación incorrecta del sistema de firewall | COM-1 y COM-2 (Comunicaciones) | F.14 | La ausencia o mala configuración del firewall permite que los puertos de comunicación (SSH en COM-1 y HTTP/HTTPS en COM-2) estén accesibles desde redes externas sin restricciones. Esto facilita el acceso no autorizado y la explotación de vulnerabilidades en los servicios expuestos. La evidencia en el SIEM muestra conexiones desde la IP maliciosa 10.11.75.247 y la implantación de un plugin no autorizado (AyzzbuXY), confirmando que el perímetro no está protegiendo adecuadamente la infraestructura. |

En donde los códigos de las amenazas están dados por: 
- **[S.21] Vulnerabilidades de los programas:** Uso de software obsoleto, sin actualizar o que ya no recibe parches de seguridad de sus creadores.
- **[S.22] Errores de configuración / Faltas funcionales:** Ausencia de componentes, librerías o módulos necesarios para que el sistema funcione correctamente y de forma segura.
- **[S.24] Deficiencias de mantenimiento:** Dejar instalados componentes, plugins o temas que no se usan. Esto aumenta la "superficie de ataque".
- **[A.11] Degradación del servicio:** Fallos o configuraciones pobres que hacen que el sistema vaya muy lento o pueda colapsar si recibe muchas visitas de golpe
- **[F.14] Fallo en los controles de acceso perimetral:** Ausencia o mala configuración del firewall que permite el acceso no autorizado desde redes externas y control de las conexiones desde las redes internas al exterior.

#### 🔴 Evaluación de Riesgos
##### 🟢 Cálculo de Probabilidad

|Probabilidad| Muy baja | Baja | Media | Alta | Crítica |
|---|---|---|---|---|---|
| Frecuencia | 0 - 1,9 | 2 - 3,9 | 4 - 5,9 | 6 - 7,9 | 8 - 10 |
|ARO |0 - 1,9| 2 - 3,9| 4 - 5,9 |6 - 7,9| 8 - 10
| Estadística | > 1 año | Cada año | Cada mes | Cada semana | Cada día |


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
| AM-03 | MD-2 (Middleware, Apache httpd) | C       | 10 | 10%  | 1.0 |
| AM-04 | SO-1 (Servidor Ubuntu)          | C y Au  | 10 | 90%  | 9.0 |
| AM-05 | COM-1 y COM-2 (Comunicaciones)  | C y Au  | 10 | 95% | 9.5 |

##### 🟢 Cálculo de Riesgo
| Prioridad   | Ref.  | Activo Afectado              | Dimensión Principal Afectada | Degradación | Impacto | Probabilidad | Riesgo Potencial | Riesgo Cualitativo |
|-------------|-------|------------------------------|------------------------------|-------------|----------|--------------|------------------|--------------------|
| 1º Mayor    | AM-01 | WB-1 (App Web, TeamCity)       | C y Au                     | 100%        | 10.0     | 9.7          | 97.0             | Muy Alto / Crítico |
| 2º          | AM-05 | COM-1 y COM-2 (Comunicaciones) | C y Au                     | 95%         | 9.5      | 8.7          | 82.7             | Muy Alto / Crítico |
| 3º          | AM-04 | SO-1 (Servidor Ubuntu)         | C y Au                     | 90%         | 9.0      | 6.7          | 60.3             | Alto               |
| 5º          | AM-02 | MD-1 (Middleware, OpenSSH)     | C y Au                     | 85%         | 8.5      | 8.7          | 73.9             | Alto               |
| 6º          | AM-03 | MD-2 (Middleware, Apache httpd)| C                          | 10%         | 1.0      | 8.0          | 8.0              | Bajo               |          
#### 🔴 Selección de Salvaguardas
##### 🟢 Plan de Tratamiento del Riesgo
| Ref. | Hallazgo                          | Código ENS | Acción Técnica Recomendada | ¿Qué reduce? |
|------|----------------------------------|------------|----------------------------|--------------|
| AM-01 | Versión obsoleta de PHP         | [op.exp.8] Gestión de vulnerabilidades y actualizaciones     | Actualizar a versión soportada y aplicar parches de seguridad | Probabilidad |
| AM-05 | Servidor SQL obsoleto           | [op.exp.8] Gestión de vulnerabilidades y actualizaciones      | Actualizar motor de base de datos a versión soportada y parcheada | Probabilidad |
| AM-02 | Actualización de WP disponible  | [op.exp.8] Gestión de vulnerabilidades y actualizaciones      | Actualizar WordPress a la última versión estable | Probabilidad |
| AM-03 | Temas inactivos instalados      | [op.exp.2] Configuración de seguridad      | Eliminar temas no utilizados y mantener solo los necesarios | Probabilidad |
| AM-04 | Módulos recomendados faltantes   | [op.pl.1] Arquitectura de seguridad      | Instalar plugins o módulos de seguridad recomendados | Probabilidad |
##### 🟢 Plan de Tratamiento del Riesgo Residual
| Ref. | Hallazgo                          | Acción Técnica Recomendada | Eficacia (e) | Riesgo Residual  | Riesgo Residual Cualitativo |
|------|----------------------------------|----------------------------|--------------|-----------------|-----------------------------|
| AM-01 | Versión obsoleta de PHP         | Actualizar a versión soportada y aplicar parches de seguridad                      | 80%          | 16.2           | Bajo       |
| AM-05 | Servidor SQL obsoleto           | Actualizar motor de base de datos a versión soportada y parcheada                     | 80%          | 16.0           | Bajo    |
| AM-04 | Actualización de WP disponible   | Actualizar WordPress a la última versión estable                      | 70%          | 21.6           | Medio          |
| AM-02 | Temas inactivos instalados      | Eliminar temas no utilizados y mantener solo los necesarios                      | 90%          | 3.15           | Bajo      |
| AM-03 | Faltan módulos recomendados     | Instalar plugins o módulos de seguridad recomendados                      | 100%         | 0          | Bajo                    |


---
---
# Tablas externas

## Selección de medidas de seguridad del ENS

| Medidas de Seguridad | Por categoría o dimensión(es) | Categoría de seguridad del sistema: BAJO | CSS: MEDIO | CSS: ALTO |
| :--- | :--- | :--- | :--- | :--- |
| **org** | **Marco organizativo** | :--- | :--- | :--- |
| **org.1** | Política de seguridad | aplica | aplica | aplica |
| **org.2** | Normativa de seguridad | aplica | aplica | aplica |
| **org.3** | Procedimientos de seguridad | aplica | aplica | aplica |
| **org.4** | Proceso de autorización | aplica | aplica | aplica |
| **op** | **Marco operacional** | :--- | :--- | :--- |
| op.pl | Planificación | :--- | :--- | :--- |
| **op.pl.1** | Análisis de riesgos | aplica | + R1 | + R2 |
| **op.pl.2** | Arquitectura de Seguridad | aplica | + R1 | + R1 + R2 + R3 |
| **op.pl.3** | Adquisición de nuevos componentes | aplica | aplica | aplica |
| **op.pl.4** | Dimensionamiento/gestión de la capacidad | aplica | + R1 | + R1 |
| **op.pl.5** | Componentes certificados | n.a. | aplica | aplica |
| op.acc | Control de acceso | :--- | :--- | :--- |
| **op.acc.1** | Identificación | aplica | + R1 | + R1 |
| **op.acc.2** | Requisitos de acceso | aplica | aplica | + R1 |
| **op.acc.3** | Segregación de funciones y tareas | n.a. | aplica | + R1 |
| **op.acc.4** | Proceso de gestión de derechos de acceso | aplica | aplica | aplica |
| **op.acc.5** | Mecanismo de autenticación (usuarios externos) | + [R1 o R2 o R3 o R4] | + [R2 o R3 o R4] + R5 | + [R2 o R3 o R4] + R5 |
| **op.acc.6** | Mecanismo de autenticación (usuarios de la organización) | + [R1 o R2 o R3 o R4] + R8 + R9 | + [R1 o R2 o R3 o R4] + R5 + R8 + R9 | + [R1 o R2 o R3 o R4] + R5 + R6 + R7 + R8 + R9 |
| op.exp | Explotación | :--- | :--- | :--- |
| **op.exp.1** | Inventario de activos | aplica | aplica | aplica |
| **op.exp.2** | Configuración de seguridad | aplica | aplica | aplica |
| **op.exp.3** | Gestión de la configuración de seguridad | aplica | + R1 | + R1 + R2 + R3 |
| **op.exp.4** | Mantenimiento y actualizaciones de seguridad | aplica | + R1 | + R1 + R2 |
| **op.exp.5** | Gestión de cambios | n.a. | aplica | + R1 |
| **op.exp.6** | Protección frente a código dañino | aplica | + R1 + R2 | + R1 + R2 + R3 + R4 |
| **op.exp.7** | Gestión de incidentes | aplica | + R1 + R2 | + R1 + R2 + R3 |
| **op.exp.8** | Registro de la actividad | aplica | + R1 + R2 + R3 + R4 | + R1 + R2 + R3 + R4 + R5 |
| **op.exp.9** | Registro de la gestión de incidentes | aplica | aplica | aplica |
| **op.exp.10** | Protección de claves criptográficas | aplica | + R1 | + R1 |
| op.ext | Recursos externos | :--- | :--- | :--- |
| **op.ext.1** | Contratación y acuerdos de nivel de servicio | n.a. | aplica | aplica |
| **op.ext.2** | Gestión diaria | n.a. | aplica | aplica |
| **op.ext.3** | Protección de la cadena de suministro | n.a. | n.a. | aplica |
| **op.ext.4** | Interconexión de sistemas | n.a. | aplica | + R1 |
| op.nub | Servicios en la nube | :--- | :--- | :--- |
| **op.nub.1** | Protección de servicios en la nube | aplica | + R1 | + R1 + R2 |
| op.cont | Continuidad del servicio | :--- | :--- | :--- |
| **op.cont.1** | Análisis de impacto | n.a. | aplica | aplica |
| **op.cont.2** | Plan de continuidad | n.a. | n.a. | aplica |
| **op.cont.3** | Pruebas periódicas | n.a. | n.a. | aplica |
| **op.cont.4** | Medios alternativos | n.a. | n.a. | aplica |
| op.mon | Monitorización del sistema | :--- | :--- | :--- |
| **op.mon.1** | Detección de intrusión | aplica | + R1 | + R1 + R2 |
| **op.mon.2** | Sistema de métricas | aplica | + R1 + R2 | + R1 + R2 |
| **op.mon.3** | Vigilancia | aplica | + R1 + R2 | + R1 + R2 + R3 + R4 + R5 + R6 |
| **mp** | **Medidas de protección** | :--- | :--- | :--- |
| mp.if | Protección de las instalaciones e infraestructuras | :--- | :--- | :--- |
| **mp.if.1** | Áreas separadas y con control de acceso | aplica | aplica | aplica |
| **mp.if.2** | Identificación de las personas | aplica | aplica | aplica |
| **mp.if.3** | Acondicionamiento de los locales | aplica | aplica | aplica |
| **mp.if.4** | Energía eléctrica | aplica | + R1 | + R1 |
| **mp.if.5** | Protección frente a incendios | aplica | aplica | aplica |
| **mp.if.6** | Protección frente a inundaciones | n.a. | aplica | aplica |
| **mp.if.7** | Registro de entrada y salida de equipamiento | aplica | aplica | aplica |
| mp.per | Gestión del personal | :--- | :--- | :--- |
| **mp.per.1** | Caracterización del puesto de trabajo | n.a. | aplica | aplica |
| **mp.per.2** | Deberes y obligaciones | aplica | + R1 | + R1 |
| **mp.per.3** | Concienciación | aplica | aplica | aplica |
| **mp.per.4** | Formación | aplica | aplica | aplica |
| mp.eq | 	Protección de los equipos | :--- | :--- | :--- |
| **mp.eq.1** | Puesto de trabajo despejado | aplica | + R1 | + R1 |
| **mp.eq.2** | Bloqueo de puesto de trabajo | n.a. | aplica | + R1 |
| **mp.eq.3** | Protección de dispositivos portátiles | aplica | aplica | + R1 + R2 |
| **mp.eq.4** | Otros dispositivos conectados a la red | aplica | + R1 | + R1 |
| mp.com | Protección de las comunicaciones | :--- | :--- | :--- 
| **mp.com.1** | Perímetro seguro | aplica | aplica | aplica |
| **mp.com.2** | Protección de la confidencialidad | aplica | + R1 | + R1 + R2 + R3 |
| **mp.com.3** | Protección de la integridad y de la autenticidad | aplica | + R1 + R2 | + R1 + R2 + R3 + R4 |
| **mp.com.4** | Separación de flujos de información en la red | n.a. | + [R1 o R2 o R3] | + [R2 o R3] + R4 |
| mp.si | 	Protección de los soportes de información | :--- | :--- | :--- 
| **mp.si.1** | Marcado de soportes | n.a. | aplica | aplica |
| **mp.si.2** | Criptografía | n.a. | aplica | + R1 + R2 |
| **mp.si.3** | Custodia | aplica | aplica | aplica |
| **mp.si.4** | Transporte | aplica | aplica | aplica |
| **mp.si.5** | Borrado y destrucción | aplica | + R1 | + R1 |
| mp.sw | Protección de las aplicaciones informáticas | :--- | :--- | :--- |
| **mp.sw.1** | Desarrollo de aplicaciones | n.a. | + R1 + R2 + R3 + R4 | + R1 + R2 + R3 + R4 |
| **mp.sw.2** | Aceptación y puesta en servicio | aplica | + R1 | + R1 |
| mp.info | Protección de la información | :--- | :--- | :--- |
| **mp.info.1** | Datos personales | aplica | aplica | aplica |
| **mp.info.2** | Calificación de la información | n.a. | aplica | aplica |
| **mp.info.3** | Firma electrónica | aplica | + R1 + R2 + R3 | + R1 + R2 + R3 + R4 |
| **mp.info.4** | Sellos de tiempo | n.a. | n.a. | aplica |
| **mp.info.5** | Limpieza de documentos | aplica | aplica | aplica |
| **mp.info.6** | Copias de seguridad | aplica | + R1 | + R1 + R2 |
| mp.s | Protección de los servicios | :--- | :--- | :--- |
| **mp.s.1** | Protección del correo electrónico | aplica | aplica | aplica |
| **mp.s.2** | Protección de servicios y aplicaciones web | + [R1 o R2] | + [R1 o R2] | + R2 + R3 |
| **mp.s.3** | Protección de la navegación web | aplica | aplica | + R1 |
| **mp.s.4** | Protección frente a denegación de servicio | n.a. | aplica | + R1 |

Para obtener más información se puede consultar: https[:]//www.boe.es/buscar/act.php?id=BOE-A-2022-7191

## Matriz de Riesgo

| Efectos negativos producidos | Criticidad de los recursos: Muy baja | Criticidad: Baja | Criticidad: Media | Criticidad: Alta | Criticidad: Muy Alta |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Muy Bajo** | MUY BAJO | MUY BAJO | MUY BAJO | BAJO | BAJO |
| **Bajo** | MUY BAJO | BAJO | BAJO | MEDIO | MEDIO |
| **Medio** | BAJO | MEDIO | MEDIO | ALTO | ALTO |
| **Alto** | MEDIO | ALTO | ALTO | CRÍTICO | CRÍTICO |
| **Muy Alto** | ALTO | CRÍTICO | CRÍTICO | CRÍTICO | CRÍTICO |
