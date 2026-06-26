# BI_Datamart_Accidentes — Análisis de Accidentes de Tránsito en EE.UU.

**Integrantes:** Diego Saldaña, Diego Rodríguez, Sebastián Vinces y Mario Auqui

---

## 1. Marco Teórico

### Business Intelligence

Business Intelligence (BI) es el conjunto de estrategias, procesos, tecnologías y herramientas orientadas a la recopilación, integración, análisis y presentación de información empresarial con el fin de apoyar la toma de decisiones (Negash, 2004). A diferencia de los sistemas transaccionales, los sistemas de BI están diseñados para consolidar grandes volúmenes de datos históricos y transformarlos en conocimiento accionable (Işık et al., 2013). En el contexto de seguridad vial, el BI permite convertir millones de registros de accidentes en patrones identificables, KPIs significativos y visualizaciones que faciliten la acción preventiva.

### Data Warehouse y Datamart

Un Data Warehouse es un repositorio centralizado que integra datos provenientes de múltiples fuentes operacionales, transformados y estructurados para el análisis. Su diseño privilegia la velocidad de consulta y la consistencia histórica sobre la eficiencia transaccional (Chaudhuri & Dayal, 1997). Un datamart es un subconjunto temático orientado a un área funcional específica —en este caso, la accidentalidad vial— que permite obtener respuestas rápidas a preguntas de negocio concretas. El presente proyecto implementa un datamart independiente sobre el dataset US-Accidents, habilitando consultas multidimensionales sobre severidad, clima, geografía y tiempo.

### Modelamiento Dimensional

El modelamiento dimensional estructura los datos en tablas de hechos y dimensiones con el objetivo de hacer las consultas analíticas intuitivas y eficientes (Chaudhuri & Dayal, 1997). Se basa en dos tipos de tablas fundamentales:

- **Tabla de hechos (Fact Table):** almacena las métricas cuantitativas del proceso de negocio —en este caso, FactAccidente— junto con las claves foráneas que apuntan a las dimensiones. Las medidas incluyen duración del accidente, distancia del tramo afectado, temperatura, humedad, visibilidad y precipitación.

- **Tablas de dimensiones (Dimension Tables):** describen el contexto de cada hecho. En este proyecto se modelaron doce dimensiones: DimSeverity, DimFecha, DimUbicacionGeo, DimLugar, DimClima, DimCrossing, DimJunction, DimStation, DimStop, DimTraffic y DimCivilTwilight.

### Esquema Estrella

El esquema estrella (star schema) es la implementación más común del modelamiento dimensional, caracterizada por una tabla de hechos central conectada directamente a múltiples tablas de dimensión sin normalización adicional (Chaudhuri & Dayal, 1997). Esta estructura permite que los motores de consulta ejecuten operaciones de agregación y filtrado de manera altamente eficiente, dado que las uniones se realizan siempre entre la tabla de hechos y una sola dimensión a la vez.

En el presente proyecto, la tabla FactAccidente actúa como el centro del esquema, conectándose a once dimensiones que permiten analizar cada accidente desde perspectivas temporales, geográficas, climáticas e infraestructurales.

### Proceso ETL (Extract, Transform, Load)

El proceso ETL comprende las etapas de extracción de datos desde las fuentes originales, transformación para limpiar y estandarizar la información, y carga en el modelo dimensional destino (Vassiliadis, 2009). La calidad del dato en destino depende directamente de la rigurosidad de esta etapa, que en proyectos de Data Warehouse suele representar la mayor parte del esfuerzo de implementación. En este proyecto, el dataset US-Accidents provee los datos crudos que deben ser procesados para derivar campos calculados como "duracion_minutos", separar atributos en sus dimensiones correspondientes y garantizar la integridad referencial.

### KPIs y Análisis Multidimensional

Los Indicadores Clave de Rendimiento (KPIs) son métricas que permiten evaluar el desempeño de un proceso respecto a objetivos definidos. El análisis multidimensional OLAP (Online Analytical Processing) permite navegar estos indicadores con distintos niveles de granularidad (Chaudhuri & Dayal, 1997), aplicando operaciones como drill-down (desagregar de estado a ciudad), roll-up (agregar de hora a franja del día) y slice and dice (filtrar por condición climática o tipo de infraestructura). Esta capacidad analítica es la que distingue a un datamart de una base de datos operacional simple.

### Dashboards e Inteligencia Operacional

Los dashboards interactivos son la capa de presentación del sistema de BI, permitiendo a los usuarios explorar los datos sin necesidad de conocimientos técnicos avanzados (Yigitbasioglu & Velcu, 2012). En proyectos de seguridad vial, los dashboards facilitan la identificación visual de zonas de alto riesgo, patrones horarios y correlaciones entre condiciones ambientales y severidad de accidentes, traduciendo el análisis técnico en información directamente utilizable para la toma de decisiones estratégicas.

---

## 2. Descripción de la Fuente de Datos

El presente proyecto utiliza el dataset público US-Accidents, desarrollado por investigadores de The Ohio State University con el objetivo de recopilar y analizar información relacionada con accidentes de tránsito ocurridos en Estados Unidos. El dataset fue presentado originalmente en 2019 y surgió debido a la necesidad de contar con información masiva y enriquecida que permitiera estudiar patrones espaciales, temporales y ambientales asociados a la ocurrencia de accidentes de tránsito (Moosavi et al., 2019).

Los accidentes de tránsito representan una problemática importante debido a su impacto en la seguridad vial, la movilidad urbana y los costos económicos derivados de congestión vehicular, daños materiales y emergencias. Además, la gran cantidad de accidentes registrados diariamente dificulta la identificación de patrones relevantes y factores asociados a su ocurrencia, especialmente cuando intervienen múltiples variables como clima, ubicación geográfica, visibilidad, horario y características de la infraestructura vial.

El dataset US-Accidents fue construido mediante la integración de múltiples fuentes de información en tiempo real. La principal fuente utilizada para la recolección de accidentes fueron las APIs de MapQuest Traffic y Microsoft Bing Traffic, las cuales obtenían eventos reportados por sensores viales, cámaras de tránsito, departamentos de transporte y agencias de seguridad vial (Moosavi et al., 2019). Posteriormente, los registros fueron sometidos a procesos de integración y depuración para eliminar duplicados mediante comparaciones espaciales y temporales. La versión inicial del dataset descrita en el paper contenía aproximadamente 2.25 millones de registros de accidentes ocurridos entre febrero de 2016 y marzo de 2019 (Moosavi et al., 2019). Sin embargo, la versión actualmente disponible en Kaggle corresponde a una actualización posterior del dataset, ampliando la cobertura temporal hasta marzo de 2023 y alcanzando aproximadamente 7.7 millones de registros de accidentes (Moosavi, 2023).

Con el objetivo de enriquecer la información, los autores incorporaron datos meteorológicos obtenidos mediante la API de Weather Underground, incluyendo variables como temperatura, humedad, presión atmosférica, velocidad del viento, precipitación, lluvia, nieve, niebla y visibilidad. Asimismo, utilizaron herramientas geoespaciales como OpenStreetMap (OSM) y Nominatim Reverse Geocoding para complementar la información geográfica de cada accidente, permitiendo identificar calles, ciudades, intersecciones, junctions, señales de tránsito y distintos puntos de interés cercanos a cada incidente (Moosavi et al., 2019).

Adicionalmente, se incorporó información temporal mediante la API de TimeAndDate, permitiendo clasificar los accidentes según periodos del día y condiciones de iluminación, como sunrise/sunset y distintos niveles de twilight (Moosavi et al., 2019). Gracias a este proceso de enriquecimiento, el dataset permite analizar los accidentes desde distintas perspectivas y facilita la identificación de tendencias relacionadas con factores climáticos, temporales y geográficos.

---

## 3. Problemática

Los accidentes de tránsito representan una problemática importante por su impacto en la seguridad vial, la movilidad urbana y los costos económicos derivados de congestión vehicular, daños materiales y emergencias.

La gran cantidad de datos generados diariamente dificulta convertir dicha información en conocimiento útil para la toma de decisiones estratégicas. Aunque existen millones de registros disponibles, muchas organizaciones presentan limitaciones para:

- Analizar patrones de accidentes de manera eficiente.
- Identificar zonas de alto riesgo.
- Generar reportes estratégicos para prevención de accidentes.
- Relacionar variables como clima, horario, ubicación y severidad del accidente.

> La información suele encontrarse **dispersa y poco estructurada** para procesos analíticos avanzados, dificultando la implementación de estrategias preventivas y la optimización de recursos destinados a seguridad vial.

---

## 4. Solución Propuesta

El presente proyecto propone el desarrollo de una solución de **Business Intelligence** que permita consolidar y analizar la información mediante:

- Modelos dimensionales (esquema estrella)
- Dashboards interactivos

Con ello se busca mejorar la capacidad de análisis de accidentes de tránsito y facilitar la **toma de decisiones basada en datos**.

### 4.1 Objetivo del Proyecto

Construir un datamart que habilite consultas rápidas, agregaciones multidimensionales y la generación de KPIs relevantes para la gestión de la seguridad vial, facilitando la identificación de patrones de accidentalidad y la toma de decisiones preventivas basadas en datos.

### 4.2 Preguntas de Negocio y Decisiones Esperadas

- ¿En qué horarios y días de la semana ocurren más accidentes de alta severidad?
- ¿Qué estados y ciudades concentran la mayor cantidad de accidentes graves?
- ¿Qué condiciones climáticas están más asociadas a accidentes de severidad 3 y 4?
- ¿Qué tipo de infraestructura vial (cruces, semáforos, intersecciones) está más presente en accidentes graves (severidad 3 y 4)?

### 4.3 KPIs Propuestos para el Datamart

- **Tasa de severidad alta:** proporción de accidentes con severidad 3 o 4 sobre el total de registros.
- **Duración promedio del accidente:** media de `duracion_minutos` por dimensión (severidad, clima, lugar, franja horaria).
- **Accidentes por zona geográfica:** conteo y densidad de accidentes agrupados por estado, ciudad y condado.
- **Accidentalidad por condición climática:** distribución de accidentes y severidad promedio según `Weather_Condition`.
- **Accidentes por franja horaria:** distribución de accidentes según hora del día, día de la semana y condición de luz (DimCivilTwilight).

Estos KPIs se calcularán sobre la tabla de hechos `FactAccidente` con dimensiones conformadas (fecha, lugar, clima, severidad) para permitir cortes consistentes y comparables.

---

## 5. Modelamiento de Data Dimensional

![Modelo Multidimensional](Modelo_multidimensional.PNG)

---

## 6. Diccionario de Datos

### 6.1 FactAccidente

| Columna | Tipo | Descripción |
|---|---|---|
| id | INT (PK) | Identificador interno del registro en la tabla de hechos. |
| severityID | INT (FK) | Llave foránea hacia DimSeverity. |
| fechaID | INT (FK) | Llave foránea hacia DimFecha. |
| ubicacionGeoID | INT (FK) | Llave foránea hacia DimUbicacionGeo. |
| lugarID | INT (FK) | Llave foránea hacia DimLugar. |
| climaID | INT (FK) | Llave foránea hacia DimClima. |
| crossingID | INT (FK) | Llave foránea hacia DimCrossing. |
| junctionID | INT (FK) | Llave foránea hacia DimJunction. |
| stationID | INT (FK) | Llave foránea hacia DimStation. |
| stopID | INT (FK) | Llave foránea hacia DimStop. |
| trafficID | INT (FK) | Llave foránea hacia DimTraffic. |
| civilTwilightID | INT (FK) | Llave foránea hacia DimCivilTwilight. |
| duracion_minutos | INT | Duración del accidente en minutos, calculada como diferencia entre `End_Time` y `Start_Time`. |
| distance | FLOAT | Longitud del tramo vial afectado por el accidente, en millas (`Distance(mi)`). |
| temperature | FLOAT | Temperatura ambiente al momento del accidente, en grados Fahrenheit (`Temperature(F)`). |
| wind_chill | FLOAT | Sensación térmica por viento al momento del accidente, en grados Fahrenheit (`Wind_Chill(F)`). |
| humidity | FLOAT | Porcentaje de humedad relativa al momento del accidente (`Humidity(%)`). |
| pressure | FLOAT | Presión atmosférica al momento del accidente, en pulgadas de mercurio (`Pressure(in)`). |
| visibility | FLOAT | Visibilidad en millas al momento del accidente (`Visibility(mi)`). |
| wind_speed | FLOAT | Velocidad del viento al momento del accidente, en millas por hora (`Wind_Speed(mph)`). |
| precipitation | FLOAT | Nivel de precipitación al momento del accidente, en pulgadas (`Precipitation(in)`). |

### 6.2 DimSeverity

| Columna | Tipo | Descripción |
|---|---|---|
| id | INT (PK) | Identificador de severidad. |
| severity | INT | Nivel de impacto del accidente sobre el tráfico circundante. Escala del 1 al 4, donde 1 es el menor impacto y 4 el mayor (`Severity`). |

### 6.3 DimFecha

| Columna | Tipo | Descripción |
|---|---|---|
| id | INT (PK) | Identificador de la fecha. |
| start_time | DATETIME | Fecha y hora de inicio del accidente en zona horaria local (`Start_Time`). |
| anio | INT | Año de ocurrencia del accidente. |
| mes | INT | Mes de ocurrencia del accidente (1–12). |
| dia | INT | Día del mes de ocurrencia del accidente (1–31). |
| hora | INT | Hora de inicio del accidente (0–23). |
| dia_semana | VARCHAR(20) | Día de la semana |
| es_fin_semana | BIT | Indicador de fin de semana (1 = sábado o domingo, 0 = día hábil). |

### 6.4 DimUbicacionGeo

| Columna | Tipo | Descripción |
|---|---|---|
| id | INT (PK) | Identificador de ubicación geográfica. |
| start_lat | FLOAT | Latitud del punto GPS donde inició el accidente (`Start_Lat`). |
| start_lng | FLOAT | Longitud del punto GPS donde inició el accidente (`Start_Lng`). |

### 6.5 DimLugar

| Columna | Tipo | Descripción |
|---|---|---|
| id | INT (PK) | Identificador del lugar. |
| street | NVARCHAR(255) | Nombre de la calle donde ocurrió el accidente (`Street`). |
| city | NVARCHAR(100) | Nombre de la ciudad donde ocurrió el accidente (`City`). |
| county | NVARCHAR(100) | Nombre del condado donde ocurrió el accidente (`County`). |
| state | NVARCHAR(50) | Código del estado donde ocurrió el accidente (`State`). |
| zipcode | NVARCHAR(20) | Código postal de la zona del accidente (`Zipcode`). |
| timezone | NVARCHAR(50) | Zona horaria donde ocurrió el accidente (`Timezone`). |

### 6.6 DimClima

| Columna | Tipo | Descripción |
|---|---|---|
| id | INT (PK) | Identificador de condición climática. |
| weather_condition | NVARCHAR(100) | Descripción de las condiciones meteorológicas al momento del accidente (ej. Clear, Rain, Snow, Fog) (`Weather_Condition`). |

### 6.7 DimCrossing

| Columna | Tipo | Descripción |
|---|---|---|
| id | INT (PK) | Identificador de cruce peatonal. |
| crossing | NVARCHAR(10) | Indica si el accidente ocurrió cerca de un cruce peatonal (True/False) (`Crossing`). |

### 6.8 DimJunction

| Columna | Tipo | Descripción |
|---|---|---|
| id | INT (PK) | Identificador de intersección vial. |
| junction | NVARCHAR(10) | Indica si el accidente ocurrió cerca de una intersección vial (True/False) (`Junction`). |

### 6.9 DimStation

| Columna | Tipo | Descripción |
|---|---|---|
| id | INT (PK) | Identificador de estación. |
| station | NVARCHAR(10) | Indica si el accidente ocurrió cerca de una estación de transporte público (True/False) (`Station`). |

### 6.10 DimStop

| Columna | Tipo | Descripción |
|---|---|---|
| id | INT (PK) | Identificador de parada. |
| stop | NVARCHAR(10) | Indica si el accidente ocurrió cerca de una señal de stop o parada de tránsito (True/False) (`Stop`). |

### 6.11 DimTraffic

| Columna | Tipo | Descripción |
|---|---|---|
| id | INT (PK) | Identificador de señal de tráfico. |
| traffic_signal | NVARCHAR(10) | Indica si el accidente ocurrió cerca de un semáforo (True/False) (`Traffic_Signal`). |

### 6.12 DimCivilTwilight

| Columna | Tipo | Descripción |
|---|---|---|
| id | INT (PK) | Identificador de condición de luz civil. |
| civil_twilight | NVARCHAR(20) | Indica si el accidente ocurrió durante el día o la noche según el crepúsculo civil (Day/Night) (`Civil_Twilight`). |

---

## 7. Interpretación del Dashboard (Período 2016–2023)

Para obtener una visión integral de la accidentalidad vial en Estados Unidos, el análisis se realizó sobre el período comprendido entre 2016 y 2023, considerando un total de 7,533,737 accidentes registrados. A partir del datamart construido se definieron indicadores clave que permitieron analizar patrones temporales, geográficos, climáticos y de infraestructura vial mediante visualizaciones interactivas. Los KPIs muestran que durante el período analizado se registraron 1,463,560 accidentes de alta severidad, equivalentes al 19.4 % del total, con una duración promedio de 421.1 minutos, una distancia promedio afectada de 0.56 millas y siendo 2022 el año con mayor número de accidentes.

### Patrones temporales

La distribución horaria evidencia una marcada concentración de accidentes entre las 06:00 y las 18:00 horas, con dos picos claramente definidos alrededor de las 07:00 y 16:00 horas, coincidiendo con los horarios de mayor movilidad diaria. Este comportamiento refleja el incremento del flujo vehicular durante las jornadas laborales y confirma que los periodos de mayor exposición al tránsito concentran la mayor cantidad de incidentes.

### Distribución geográfica y severidad

El análisis territorial muestra que California (CA) concentra el mayor número de accidentes registrados durante el período de estudio, seguida por Florida (FL) y Texas (TX). En un segundo nivel aparecen estados como Carolina del Sur (SC), Nueva York (NY) y Carolina del Norte (NC), evidenciando que la accidentalidad se concentra principalmente en estados con alta densidad poblacional y elevado flujo vehicular. A nivel urbano, Los Ángeles, Dallas y Atlanta lideran el número de accidentes de alta severidad, consolidándose como las ciudades con mayor concentración de eventos críticos dentro del conjunto analizado.

### Condiciones climáticas asociadas a la accidentalidad

Las condiciones climáticas clasificadas como Fair, Mostly Cloudy y Cloudy concentran la mayor cantidad absoluta de accidentes, debido a que corresponden a los escenarios meteorológicos más frecuentes durante la circulación diaria. No obstante, el análisis relativo evidencia que fenómenos como fuertes lluvias acompañadas de tormentas eléctricas, lluvias ligeras con tormenta y otras condiciones meteorológicas adversas presentan una mayor tasa de gravedad, lo que indica que, aunque ocurren con menor frecuencia, incrementan significativamente el riesgo de que un accidente tenga consecuencias más severas.

### Infraestructura vial y duración de los accidentes

Entre los distintos elementos de infraestructura vial, los semáforos concentran el mayor número absoluto de accidentes, con 1.12 millones registros, debido a su amplia presencia dentro de la red vial urbana. Sin embargo, las intersecciones presentan la mayor proporción relativa de accidentes de alta severidad (26.0 %), lo que las convierte en uno de los elementos con mayor riesgo relativo.

### Distribución general por severidad

La distribución por niveles de severidad muestra que la mayor parte de los accidentes corresponde a la severidad 2, con aproximadamente 6.0 millones de registros, equivalentes al 79.7 % del total analizado. Por su parte, los accidentes clasificados como severidad 4 representan 195,222 registros, equivalentes al 2.59 % del total. Aunque estos últimos constituyen una proporción relativamente pequeña, representan los eventos con mayor impacto operativo y social, razón por la cual constituyen el principal foco de análisis dentro de los indicadores estratégicos definidos para el datamart.

---
## 8. Conclusiones

La construcción del datamart permitió estructurar más de 7.5 millones de registros correspondientes al período 2016–2023 bajo un esquema estrella conformado por una tabla de hechos y doce dimensiones, facilitando consultas multidimensionales eficientes sobre severidad, clima, ubicación geográfica e infraestructura vial.

El proceso ETL desarrollado en Python garantizó la correcta integración y transformación de la información, preservando la integridad referencial del modelo dimensional y asegurando que los indicadores obtenidos a partir de la tabla de hechos representen información consistente y confiable para el análisis.

Los KPIs implementados permitieron identificar patrones relevantes que no son evidentes mediante un simple conteo de accidentes. El análisis conjunto de variables temporales, geográficas, climáticas y de infraestructura permitió reconocer horarios críticos de circulación, estados con mayor concentración de accidentes, ciudades con elevada incidencia de eventos graves y condiciones climáticas que incrementan el riesgo relativo de severidad.

El dashboard desarrollado proporcionó una herramienta interactiva para responder las preguntas de negocio planteadas, facilitando el análisis de la accidentalidad desde diferentes perspectivas mediante filtros dinámicos y visualizaciones orientadas a la toma de decisiones. La integración de KPIs y gráficos permitió transformar grandes volúmenes de datos en información comprensible y útil para la gestión de la seguridad vial.

Finalmente, el proyecto demuestra que la aplicación de técnicas de Business Intelligence, apoyadas en un modelo dimensional y herramientas de visualización interactivas, constituye una estrategia efectiva para convertir datos masivos en conocimiento accionable, contribuyendo al diseño de políticas de prevención, la planificación urbana y la asignación eficiente de recursos destinados a la reducción de la siniestralidad vial.

## 9. Referencias

Chaudhuri, S., & Dayal, U. (1997). An overview of data warehousing and OLAP technology. ACM SIGMOD Record. https://dl.acm.org/doi/10.1145/248603.248616

Işık, Ö., Jones, M. C., & Sidorova, A. (2013). Business intelligence success: The roles of BI capabilities and decision environments. Information & Management. https://www.sciencedirect.com/science/article/pii/S0378720612000560

Moosavi, S., et al. (2019). A countrywide traffic accident dataset. arXiv. https://arxiv.org/abs/1906.05409

Moosavi, S. (2023). US-Accidents: A countrywide traffic accident dataset (2016–2023) [Dataset]. Kaggle. https://www.kaggle.com/datasets/sobhanmoosavi/us-accidents

Negash, S. (2004). Business intelligence. Communications of the AIS. https://aisel.aisnet.org/cais/vol13/iss1/15/

Vassiliadis, P. (2009). A survey of extract–transform–load technology. IJDWM. https://www.igi-global.com/article/survey-extract-transform-load-technology/37174

Yigitbasioglu, O. M., & Velcu, O. (2012). A review of dashboards in performance management. The British Accounting Review. https://www.sciencedirect.com/science/article/pii/S0748575111000930
