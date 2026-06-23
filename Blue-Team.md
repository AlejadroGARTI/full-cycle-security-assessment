# 1. TRIAGE INICIAL Y CAZA DE AMENAZAS

## 1.1 Host analizado (Brains)

Se usa la query (*) para buscar todos los resultados, en donde podemos osbsevar que el host es brains. Identificar el nombre del host analizado es fundamental para contextualizar la investigación y garantizar que los eventos corresponden al sistema correcto. Esto permite relacionar los registros con un activo específico de la infraestructura y facilita el seguimiento de incidentes, anomalías y actividades sospechosas.

![](Evidencias_Visuales/BlueTeam/host)

## 1.2 Cantidad de sourcetypes analizados (3)

Conocer el número de sourcetypes ayuda a comprender la diversidad de fuentes de datos disponibles para el análisis. Cuantas más fuentes se integren, mayor será la visibilidad sobre la actividad del sistema, permitiendo correlacionar eventos y obtener una visión más completa de la seguridad del entorno.

![](Evidencias_Visuales/BlueTeam/sourcetype2)

## 1.3 Sourcetype con mayor número de eventos (3.816 eventos)

Identificar la fuente que genera más eventos permite determinar qué sistemas o aplicaciones tienen mayor actividad y cuáles aportan más información al análisis. Además, ayuda a priorizar la revisión de registros y detectar comportamientos anómalos o patrones de actividad inusuales.

![](Evidencias_Visuales/BlueTeam/sourcetype)

## 1.4 Año con mayor generación de eventos (2024)

Determinar el año con mayor volumen de eventos permite identificar períodos de mayor actividad operativa o de seguridad. Esta información es útil para analizar tendencias, evaluar el crecimiento de la infraestructura y detectar posibles incrementos en la actividad de usuarios o sistemas.

![](Evidencias_Visuales/BlueTeam/año)

## 1.5 Número de eventos generados en ese año (4109)

Conocer la cantidad exacta de eventos registrados durante el período de mayor actividad permite medir la magnitud de la información disponible para el análisis. Este dato es importante para evaluar la carga de trabajo de los sistemas de monitorización, realizar análisis históricos y establecer comparativas con otros períodos temporales.

![](Evidencias_Visuales/BlueTeam/eventosaño)

---

# 2. ANÁLISIS PROFUNDO Y LINEA DE TIEMPO

## 2.1 Usuario creado durante la explotación (eviluser)

La identificación del usuario creado por el atacante es una evidencia clave de compromiso del sistema. La creación de cuentas no autorizadas suele utilizarse para mantener acceso persistente al servidor incluso después de finalizar la intrusión inicial, permitiendo al atacante volver a conectarse en cualquier momento.

## 2.2 Fecha y hora de creación del usuario (Jul 4 22:32:37)

Determinar el momento exacto en que se creó la cuenta maliciosa permite reconstruir la línea temporal del incidente. Este dato ayuda a correlacionar otros eventos registrados en los logs y a comprender la secuencia de acciones realizadas durante la explotación.

## 2.3 Punct del usuario (___::__[]:__:_=,_=,_=,_=//,_=//,_=/// )

El campo punct proporciona información sobre la configuración interna de la cuenta creada. Analizar este valor puede ayudar a verificar la legitimidad del usuario, identificar patrones anómalos y detectar modificaciones realizadas por el atacante en la configuración del sistema.

## 2.4 Tipo de shell asignada (/bin/bash)

Conocer la shell asociada al usuario es importante porque define las capacidades de interacción con el sistema. Una shell como /bin/bash proporciona acceso completo a comandos y scripts, permitiendo al atacante ejecutar acciones avanzadas, automatizar tareas y mantener el control del servidor.

![](Evidencias_Visuales/BlueTeam/usr)

## 2.5 Paquete malicioso instalado (datacollector)

La identificación del paquete malicioso permite determinar una de las herramientas utilizadas por el atacante durante la intrusión. Conocer su nombre facilita la investigación de sus funcionalidades, el alcance de la amenaza y la búsqueda de indicadores de compromiso relacionados.

## 2.6 Versión del paquete (amd64 1.0)

La versión del software malicioso ayuda a identificar exactamente qué variante fue desplegada en el sistema por lo que esta información es útil para comparar el incidente con campañas conocidas, analizar vulnerabilidades explotadas y aplicar medidas de mitigación específicas.

![](Evidencias_Visuales/BlueTeam/paquetemalicioso)

## 2.7 Hora de inicio de la instalación del paquete (22:58:23)

Registrar la hora exacta de instalación permite ubicar este evento dentro de la línea temporal del ataque. Además, facilita la correlación con otros registros para determinar qué acciones precedieron y siguieron a la implantación del software malicioso.

![](Evidencias_Visuales/BlueTeam/installationuser)

## 2.8 Nombre del plugin instalado (AyzzbuXY)

La detección de plugins no autorizados es fundamental en entornos como TeamCity, ya que estos pueden utilizarse para ejecutar código arbitrario, obtener persistencia o exfiltrar información. Identificar el plugin permite analizar su comportamiento y evaluar su impacto en el sistema, tal como ocurre con el CVE-2024-27198 identificado durante la fase de RED-TEAM, en donde, una vez dentro del sistema se pueden instalar estos plugins de fuentes maliciosas.

## 2.9 Source del evento del plugin (/opt/teamcity/TeamCity/logs/teamcity-activities.log)

Conocer la fuente exacta del evento permite validar la evidencia obtenida y localizar registros adicionales relacionados con la actividad maliciosa. También facilita futuras investigaciones forenses y la búsqueda de otros eventos asociados al incidente.

![](Evidencias_Visuales/BlueTeam/sourceplugin)

## 2.10 Dirección IP del atacante (10.11.75.247)

La IP de origen constituye uno de los principales indicadores de compromiso (IoC). Este dato permite rastrear el origen de la conexión, identificar movimientos laterales dentro de la red y aplicar medidas de bloqueo o monitorización sobre direcciones sospechosas.

## 2.11 Clave pública utilizada para el acceso (ecXxIHdpi9cpIPbjewybKqpDqrM1bw/OlKeuDT6rmzc)

La identificación de la clave pública utilizada por el atacante es esencial para comprender el mecanismo de autenticación empleado ya que este dato permite detectar accesos no autorizados mediante SSH, eliminar credenciales comprometidas y reforzar los controles de acceso del sistema.

![](Evidencias_Visuales/BlueTeam/publickey)
