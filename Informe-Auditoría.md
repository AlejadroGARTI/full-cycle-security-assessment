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

## Anexos Técnicos

- [Informe de Pentesting](Red-Team.md)
- [Análisis en Splunk](Blue-Team.md)

| Ref.   | Activo              | C | I | A | Au | T |
|--------|---------------------|---|---|---|----|---|
| INF-1  | Datos de negocio    | 10 | 9 | 8 | 6  | 6 |
| SW-1   | Aplicación Web      | 3 | 8 | 7 | 5  | 4 |
| SW-2   | Middleware          | 4 | 7 | 7 | 4  | 4 |
| COM-1  | Comunicaciones      | 10 | 9 | 6 | 8  | 5 |
| HW-1   | Servidor Ubuntu     | 4 | 7 | 7 | 4  | 5 |

HD - Linux
S2 - OpenSSH (middleware) 
S2 - Apache (middleware) - Web en mantenimiento por el puerto 80
S2 - TomCat (middleware) - Web TeamCity
Comunicaciones  tambien cada una diferente ver diagrama 


