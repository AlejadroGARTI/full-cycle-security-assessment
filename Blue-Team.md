# 1. TRIAGE INICIAL Y CAZA DE AMENAZAS

## 1.1 Como se llama el host que se está analizando?

Se usa la query (*) para buscar todos los resultados, en donde podemos osbsevar que el host es brains. Identificar el nombre del host analizado es fundamental para contextualizar la investigación y garantizar que los eventos corresponden al sistema correcto. Esto permite relacionar los registros con un activo específico de la infraestructura y facilita el seguimiento de incidentes, anomalías y actividades sospechosas.

![](Evidencias_Visuales/BlueTeam/host)

## 1.2 ¿Cuántos sourcetype de los logs se están analizando?

Conocer el número de sourcetypes ayuda a comprender la diversidad de fuentes de datos disponibles para el análisis. Cuantas más fuentes se integren, mayor será la visibilidad sobre la actividad del sistema, permitiendo correlacionar eventos y obtener una visión más completa de la seguridad del entorno.

![](Evidencias_Visuales/BlueTeam/sourcetype2)

## 1.3 ¿Cuantos eventos generó el sourcetype que más eventos tiene?

Identificar la fuente que genera más eventos permite determinar qué sistemas o aplicaciones tienen mayor actividad y cuáles aportan más información al análisis. Además, ayuda a priorizar la revisión de registros y detectar comportamientos anómalos o patrones de actividad inusuales.

![](Evidencias_Visuales/BlueTeam/sourcetype)

## 1.4 ¿En que año se generaron más eventos?

Determinar el año con mayor volumen de eventos permite identificar períodos de mayor actividad operativa o de seguridad. Esta información es útil para analizar tendencias, evaluar el crecimiento de la infraestructura y detectar posibles incrementos en la actividad de usuarios o sistemas.

![](Evidencias_Visuales/BlueTeam/año)

## 1.5 ¿Cuántos eventos se generaron ese año?

Conocer la cantidad exacta de eventos registrados durante el período de mayor actividad permite medir la magnitud de la información disponible para el análisis. Este dato es importante para evaluar la carga de trabajo de los sistemas de monitorización, realizar análisis históricos y establecer comparativas con otros períodos temporales.

![](Evidencias_Visuales/BlueTeam/eventosaño)

---

# 2. ANÁLISIS PROFUNDO Y LINEA DE TIEMPO
