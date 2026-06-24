# Informe de Auditoría de Seguridad

## Cliente

- **Empresa Auditora:** AGARTI Technologies
- **Cliente:** NEBULA Inc.
- **Proyecto:** Auditoría Integral - Operación Eclipse
- **Fecha de emisión:** 26/06/2026
- **Auditor Jefe:** Alejandro Gutiérrez Alañón
- **Clasificación del Documento:** CONFIDENCIAL

## Resumen Ejecutivo

## Alcance y Metodología

### Sistemas evaluados

### Marcos normativos aplicados
- Metodología MAGERIT v3
- Controles del Esquema Nacional de Seguridad (ENS)

### Herramientas utilizadas
- Inspección manual de configuración de WordPress (ver evidencias visuales)
- Análisis de cabeceras HTTP 

## Top 3 Hallazgos Críticos

### Hallazgo 1

- **Nombre del Hallazgo:** Versión de TeamCity obsoleta y vulnerable
- **Nivel de Riesgo:** Crítico
- **Condición (Lo que es):** (2023.11.3 (build 147512)).
- **Criterio (Lo que debería ser):**
- **Causa (Por qué ocurre):** 
- **Consecuencia e Impacto:** 
- **Plan de Acción (Recomendación):** 

### Hallazgo 2

- **Nombre del Hallazgo:** Versión de SSH obsoleta
- **Nivel de Riesgo:** 
- **Condición (Lo que es):** 8.2p1
- **Criterio (Lo que debería ser):**
- **Causa (Por qué ocurre):** 
- **Consecuencia e Impacto:** 
- **Plan de Acción (Recomendación):** 

### Hallazgo 3

- **Nombre del Hallazgo:** Versión de Apache obsoleta
- **Nivel de Riesgo:** 
- **Condición (Lo que es):** Apache/2.4.41 
- **Criterio (Lo que debería ser):**
- **Causa (Por qué ocurre):** 
- **Consecuencia e Impacto:** 
- **Plan de Acción (Recomendación):**

### Hallazgo 4

- **Nombre del Hallazgo:** Versión de Linux obsoleta
- **Nivel de Riesgo:** 
- **Condición (Lo que es):** Apache/2.4.41 
- **Criterio (Lo que debería ser):**
- **Causa (Por qué ocurre):** 
- **Consecuencia e Impacto:** 
- **Plan de Acción (Recomendación):** 


cat /etc/os-release
NAME="Ubuntu"
VERSION="20.04.6 LTS (Focal Fossa)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 20.04.6 LTS"
VERSION_ID="20.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=focal
UBUNTU_CODENAME=focal


## Anexos Técnicos

- [Informe de Pentesting](Red-Team.md)
- [Análisis en Splunk](Blue-Team.md)

#### 🔴 Identificación y Valoración de Activos
##### 🟢 Valor propio
| Ref.   | Activo              | C | I | A | Au | T |
|--------|---------------------|---|---|---|----|---|
| INF-1  | Datos de negocio    | 10 | 9 | 8 | 6  | 6 |
| SW-1   | Aplicación Web      | 3 | 8 | 7 | 5  | 4 |
| SW-2   | Middleware          | 4 | 7 | 7 | 4  | 4 |
| COM-1  | Comunicaciones      | 10 | 9 | 6 | 8  | 5 |
| HW-1   | Servidor Ubuntu     | 4 | 7 | 7 | 4  | 5 |

```bash
                                                ┌────────┐     ┌────────┐
                                                │ WEB 1  │     │ WEB 2  │
                                                │(Apache)│     │(Tomcat)│
                                                └───┬────┘     └───┬────┘ 
                                                    │              │
                                                    │              │
                                                ┌───▼────┐     ┌───▼────┐
                                                │ APACHE │     │ TOMCAT │     
                                ┌────▼────┐     └───┬────┘     └───┬────┘ 
                                │   SSH   │         │              │
                                └────┬────┘         └──────┬───────┘
                                     │                     │ 
                                ┌────▼────┐           ┌────▼────┐               
                                │ OpenSSH │           │  HTTP   │          
                                └─────────┘           └─────────┘      
                                     │                     │      
                                     └──────────┼──────────┘
                                                │
                                           ┌────▼────┐
                                           │ LINUX   │
                                           └─────────┘
```

HD - Linux
S2 - OpenSSH (middleware) 
S2 - Apache (middleware) - Web en mantenimiento por el puerto 80
S2 - TomCat (middleware) - Web TeamCity
Comunicaciones  tambien cada una diferente ver diagrama 

##### 🟢 Valor acumulado


#### 🔴 Identificación de Amenazas
| Ref.  | Hallazgo                                | Activo Afectado | Código Amenaza | Justificación |
|-------|-----------------------------------------|----------------|---------------|--------------|
| AM-01 | Versión obsoleta de PHP (7.0.33-0ubuntu0.16.04.16)        | SW-2 (Middleware)          | S.21         | Al ser una versión obsoleta del 2018 que ya no recibe parches de seguridad , se expone tanto al servidor, como a la página web a recibir ataques críticos, en donde desctacan vulnerabilidades como la inyección de Comando (CVE-2018-19518) en donde un fallo crítico en la función imap_open, permite a un atacante remoto ejecutar comandos arbitrarios en el servidor enviando un nombre de servidor IMAP manipulado. |
| AM-02 | Actualización de WordPress (7.0)        | SW-1 (Aplicación Web)          | S.21         | La versión actual de WordPress (6.5.8) posee múltiples vulnerabilidades, entre las que destacan una vulnerabilidad de tipo SSRF (Server-Side Request Forgery) que permite que un atacante obligue al servidor a hacer peticiones internas o externas o también una vulnerabilidad de tipo XSS (Cross-Site Scripting) que permite el secuestro de cuentas y el robo de cookies o sesiones.|
| AM-03 | Eliminación de temas inactivos          | SW-1 (Aplicación Web)         | S.24         | Los temas inactivos pueden tener vulnerabilidades de seguridad, y al estar fuera de uso, es más probable que no se actualicen y sus versiones presenten graves problemas de seguridad. |
| AM-04 | Módulos recomendados faltantes          | SW-1 (Aplicación Web)         | S.22         | La ausencia de los módulos recomendados, en si no representa un fallo crítico de seguridad, sin embargo, la configuración recomendada y la instalación de estos módulos permite reforzar la seguridad y el funcionamiento del sitio.  |
| AM-05 | Servidor SQL obsoleto                   | INF-1 (Datos de negocio)          | S.21         | Los datos del negocio se podrìan ver gravemente afectados ya que al tener una base de datos obsoleta, se podrían robar todos los datos que contiene.  |
| AM-06 | Falta de caché de página                | HW-1 (Servidor Ubuntu)         | A.11        | El servidor no cachea contenido, por lo que ante picos de tráfico deberá procesar cada petición desde cero, lo que puede agotar recursos y provocar caída del servicio. |

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
