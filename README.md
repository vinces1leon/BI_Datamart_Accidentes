# PROYECTO_BI

**Integrantes:** Diego Saldaña, Diego Rodríguez, Sebastián Vinces y Mario Auqui

### 1. Marco Teórico



### 2. Descripción de la Fuente de Datos

El presente proyecto utiliza el dataset público US-Accidents, desarrollado por investigadores de The Ohio State University con el objetivo de recopilar y analizar información relacionada con accidentes de tránsito ocurridos en Estados Unidos. El dataset fue presentado originalmente en 2019 y surgió debido a la necesidad de contar con información masiva y enriquecida que permitiera estudiar patrones espaciales, temporales y ambientales asociados a la ocurrencia de accidentes de tránsito (Moosavi et al., 2019).

Los accidentes de tránsito representan una problemática importante debido a su impacto en la seguridad vial, la movilidad urbana y los costos económicos derivados de congestión vehicular, daños materiales y emergencias. Además, la gran cantidad de accidentes registrados diariamente dificulta la identificación de patrones relevantes y factores asociados a su ocurrencia, especialmente cuando intervienen múltiples variables como clima, ubicación geográfica, visibilidad, horario y características de la infraestructura vial.

El dataset US-Accidents fue construido mediante la integración de múltiples fuentes de información en tiempo real. La principal fuente utilizada para la recolección de accidentes fueron las APIs de MapQuest Traffic y Microsoft Bing Traffic, las cuales obtenían eventos reportados por sensores viales, cámaras de tránsito, departamentos de transporte y agencias de seguridad vial (Moosavi et al., 2019). Posteriormente, los registros fueron sometidos a procesos de integración y depuración para eliminar duplicados mediante comparaciones espaciales y temporales. La versión inicial del dataset descrita en el paper contenía aproximadamente 2.25 millones de registros de accidentes ocurridos entre febrero de 2016 y marzo de 2019 (Moosavi et al., 2019). Sin embargo, la versión actualmente disponible en Kaggle corresponde a una actualización posterior del dataset, ampliando la cobertura temporal hasta marzo de 2023 y alcanzando aproximadamente 7.7 millones de registros de accidentes (Moosavi, 2023).

Con el objetivo de enriquecer la información, los autores incorporaron datos meteorológicos obtenidos mediante la API de Weather Underground, incluyendo variables como temperatura, humedad, presión atmosférica, velocidad del viento, precipitación, lluvia, nieve, niebla y visibilidad. Asimismo, utilizaron herramientas geoespaciales como OpenStreetMap (OSM) y Nominatim Reverse Geocoding para complementar la información geográfica de cada accidente, permitiendo identificar calles, ciudades, intersecciones, junctions, señales de tránsito y distintos puntos de interés cercanos a cada incidente (Moosavi et al., 2019).

Adicionalmente, se incorporó información temporal mediante la API de TimeAndDate, permitiendo clasificar los accidentes según periodos del día y condiciones de iluminación, como sunrise/sunset y distintos niveles de twilight (Moosavi et al., 2019). Gracias a este proceso de enriquecimiento, el dataset permite analizar los accidentes desde distintas perspectivas y facilita la identificación de tendencias relacionadas con factores climáticos, temporales y geográficos.

## Problemática

Los accidentes de tránsito representan una problemática importante por su impacto en la seguridad vial, la movilidad urbana y los costos económicos derivados de congestión vehicular, daños materiales y emergencias.

La gran cantidad de datos generados diariamente dificulta convertir dicha información en conocimiento útil para la toma de decisiones estratégicas. Aunque existen millones de registros disponibles, muchas organizaciones presentan limitaciones para:

- Analizar patrones de accidentes de manera eficiente.
- Identificar zonas de alto riesgo.
- Generar reportes estratégicos para prevención de accidentes.
- Relacionar variables como clima, horario, ubicación y severidad del accidente.

> La información suele encontrarse **dispersa y poco estructurada** para procesos analíticos avanzados, dificultando la implementación de estrategias preventivas y la optimización de recursos destinados a seguridad vial.

## Solución Propuesta

El presente proyecto propone el desarrollo de una solución de **Business Intelligence** que permita consolidar y analizar la información mediante:

- Modelos dimensionales (esquema estrella)
- Dashboards interactivos

Con ello se busca mejorar la capacidad de análisis de accidentes de tránsito y facilitar la **toma de decisiones basada en datos**.

### Objetivo del Proyecto

Construir un datamart que habilite consultas rápidas, agregaciones multidimensionales y la generación de KPIs relevantes para la gestión de la seguridad vial, facilitando la identificación de patrones de accidentalidad y la toma de decisiones preventivas basadas en datos.

### Preguntas de Negocio y Decisiones Esperadas

- ¿En qué horarios y días de la semana ocurren más accidentes de alta severidad?
- ¿Qué estados y ciudades concentran la mayor cantidad de accidentes graves?
- ¿Qué condiciones climáticas están más asociadas a accidentes de severidad 3 y 4?
- ¿Qué tipo de infraestructura vial (cruces, semáforos, intersecciones) está más presente en accidentes graves?
- ¿Cuál es la duración promedio de los accidentes según severidad, clima y zona geográfica?


### KPIs propuestos para el Datamart

- **Tasa de severidad alta:** proporción de accidentes con severidad 3 o 4 sobre el total de registros.
- **Duración promedio del accidente:** media de `duracion_minutos` por dimensión (severidad, clima, lugar, franja horaria).
- **Accidentes por zona geográfica:** conteo y densidad de accidentes agrupados por estado, ciudad y condado.
- **Accidentalidad por condición climática:** distribución de accidentes y severidad promedio según `Weather_Condition`.
- **Índice de infraestructura crítica:** proporción de accidentes graves ocurridos cerca de cruces, semáforos o intersecciones.
- **Accidentes por franja horaria:** distribución de accidentes según hora del día, día de la semana y condición de luz (DimCivilTwilight).

Estos KPIs se calcularán sobre la tabla de hechos `FactAccidente` con dimensiones conformadas (fecha, lugar, clima, severidad) para permitir cortes consistentes y comparables.

## Modelamiento de Data Dimensional

![Modelo Multidimensional](Modelo_multidimensional.PNG)

## Diccionario de Datos

### FactAccidente

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
| duracion_minutos | FLOAT | Duración del accidente en minutos, calculada como diferencia entre `End_Time` y `Start_Time`. |
| distance | FLOAT | Longitud del tramo vial afectado por el accidente, en millas (`Distance(mi)`). |
| temperature | FLOAT | Temperatura ambiente al momento del accidente, en grados Fahrenheit (`Temperature(F)`). |
| wind_chill | FLOAT | Sensación térmica por viento al momento del accidente, en grados Fahrenheit (`Wind_Chill(F)`). |
| humidity | FLOAT | Porcentaje de humedad relativa al momento del accidente (`Humidity(%)`). |
| pressure | FLOAT | Presión atmosférica al momento del accidente, en pulgadas de mercurio (`Pressure(in)`). |
| visibility | FLOAT | Visibilidad en millas al momento del accidente (`Visibility(mi)`). |
| wind_speed | FLOAT | Velocidad del viento al momento del accidente, en millas por hora (`Wind_Speed(mph)`). |
| precipitation | FLOAT | Nivel de precipitación al momento del accidente, en pulgadas (`Precipitation(in)`). |

---

### DimSeverity

| Columna | Tipo | Descripción |
|---|---|---|
| id | INT (PK) | Identificador de severidad. |
| severity | INT | Nivel de impacto del accidente sobre el tráfico circundante. Escala del 1 al 4, donde 1 es el menor impacto y 4 el mayor (`Severity`). |

---

### DimFecha

| Columna | Tipo | Descripción |
|---|---|---|
| id | INT (PK) | Identificador de la fecha. |
| start_time | DATETIME | Fecha y hora de inicio del accidente en zona horaria local (`Start_Time`). |
| anio | INT | Año de ocurrencia del accidente. |
| mes | INT | Mes de ocurrencia del accidente (1–12). |
| dia | INT | Día del mes de ocurrencia del accidente (1–31). |
| hora | INT | Hora de inicio del accidente (0–23). |
| dia_semana | INT | Día de la semana (1 = lunes, 7 = domingo). |
| es_fin_semana | BIT | Indicador de fin de semana (1 = sábado o domingo, 0 = día hábil). |

---

### DimUbicacionGeo

| Columna | Tipo | Descripción |
|---|---|---|
| id | INT (PK) | Identificador de ubicación geográfica. |
| start_lat | FLOAT | Latitud del punto GPS donde inició el accidente (`Start_Lat`). |
| start_lng | FLOAT | Longitud del punto GPS donde inició el accidente (`Start_Lng`). |

---

### DimLugar

| Columna | Tipo | Descripción |
|---|---|---|
| id | INT (PK) | Identificador del lugar. |
| street | NVARCHAR(200) | Nombre de la calle donde ocurrió el accidente (`Street`). |
| city | NVARCHAR(100) | Nombre de la ciudad donde ocurrió el accidente (`City`). |
| county | NVARCHAR(100) | Nombre del condado donde ocurrió el accidente (`County`). |
| state | NVARCHAR(10) | Código del estado donde ocurrió el accidente (`State`). |
| zipcode | NVARCHAR(20) | Código postal de la zona del accidente (`Zipcode`). |
| timezone | NVARCHAR(50) | Zona horaria donde ocurrió el accidente (`Timezone`). |

---

### DimClima

| Columna | Tipo | Descripción |
|---|---|---|
| id | INT (PK) | Identificador de condición climática. |
| weather_condition | NVARCHAR(100) | Descripción de las condiciones meteorológicas al momento del accidente (ej. Clear, Rain, Snow, Fog) (`Weather_Condition`). |

---

### DimCrossing

| Columna | Tipo | Descripción |
|---|---|---|
| id | INT (PK) | Identificador de cruce peatonal. |
| crossing | BIT | Indica si el accidente ocurrió cerca de un cruce peatonal (True/False) (`Crossing`). |

---

### DimJunction

| Columna | Tipo | Descripción |
|---|---|---|
| id | INT (PK) | Identificador de intersección vial. |
| junction | BIT | Indica si el accidente ocurrió cerca de una intersección vial (True/False) (`Junction`). |

---

### DimStation

| Columna | Tipo | Descripción |
|---|---|---|
| id | INT (PK) | Identificador de estación. |
| station | BIT | Indica si el accidente ocurrió cerca de una estación de transporte público (True/False) (`Station`). |

---

### DimStop

| Columna | Tipo | Descripción |
|---|---|---|
| id | INT (PK) | Identificador de parada. |
| stop | BIT | Indica si el accidente ocurrió cerca de una señal de stop o parada de tránsito (True/False) (`Stop`). |

---

### DimTraffic

| Columna | Tipo | Descripción |
|---|---|---|
| id | INT (PK) | Identificador de señal de tráfico. |
| traffic_signal | BIT | Indica si el accidente ocurrió cerca de un semáforo (True/False) (`Traffic_Signal`). |

---

### DimCivilTwilight

| Columna | Tipo | Descripción |
|---|---|---|
| id | INT (PK) | Identificador de condición de luz civil. |
| civil_twilight | NVARCHAR(10) | Indica si el accidente ocurrió durante el día o la noche según el crepúsculo civil (Day/Night) (`Civil_Twilight`). |

---

## Referencias

Moosavi, S., Samavatian, M. H., Parthasarathy, S., & Ramnath, R. (2019). A countrywide traffic accident dataset. arXiv. https://arxiv.org/abs/1906.05409

Moosavi, S., Samavatian, M. H., Parthasarathy, S., & Ramnath, R. (2023). US-Accidents: A countrywide traffic accident dataset (2016–2023) [Dataset]. Kaggle. Kaggle US-Accidents Dataset https://www.kaggle.com/datasets/sobhanmoosavi/us-accidents



