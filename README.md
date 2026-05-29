# PROYECTO_BI

Integrantes: Diego Saldaña, Diego Rodríguez, Sebastián Vinces y Mario Auqui

Descripción de la fuente de datos y problemática

El presente proyecto utiliza el dataset público US-Accidents, desarrollado por investigadores de The Ohio State University con el objetivo de recopilar y analizar información relacionada con accidentes de tránsito ocurridos en Estados Unidos. El dataset fue presentado originalmente en 2019 y surgió debido a la necesidad de contar con información masiva y enriquecida que permitiera estudiar patrones espaciales, temporales y ambientales asociados a la ocurrencia de accidentes de tránsito (Moosavi et al., 2019).

Los accidentes de tránsito representan una problemática importante debido a su impacto en la seguridad vial, la movilidad urbana y los costos económicos derivados de congestión vehicular, daños materiales y emergencias. Además, la gran cantidad de accidentes registrados diariamente dificulta la identificación de patrones relevantes y factores asociados a su ocurrencia, especialmente cuando intervienen múltiples variables como clima, ubicación geográfica, visibilidad, horario y características de la infraestructura vial.

El dataset US-Accidents fue construido mediante la integración de múltiples fuentes de información en tiempo real. La principal fuente utilizada para la recolección de accidentes fueron las APIs de MapQuest Traffic y Microsoft Bing Traffic, las cuales obtenían eventos reportados por sensores viales, cámaras de tránsito, departamentos de transporte y agencias de seguridad vial (Moosavi et al., 2019). Posteriormente, los registros fueron sometidos a procesos de integración y depuración para eliminar duplicados mediante comparaciones espaciales y temporales. La versión inicial del dataset descrita en el paper contenía aproximadamente 2.25 millones de registros de accidentes ocurridos entre febrero de 2016 y marzo de 2019 (Moosavi et al., 2019). Sin embargo, la versión actualmente disponible en Kaggle corresponde a una actualización posterior del dataset, ampliando la cobertura temporal hasta marzo de 2023 y alcanzando aproximadamente 7.7 millones de registros de accidentes (Moosavi, 2023).

Con el objetivo de enriquecer la información, los autores incorporaron datos meteorológicos obtenidos mediante la API de Weather Underground, incluyendo variables como temperatura, humedad, presión atmosférica, velocidad del viento, precipitación, lluvia, nieve, niebla y visibilidad. Asimismo, utilizaron herramientas geoespaciales como OpenStreetMap (OSM) y Nominatim Reverse Geocoding para complementar la información geográfica de cada accidente, permitiendo identificar calles, ciudades, intersecciones, junctions, señales de tránsito y distintos puntos de interés cercanos a cada incidente (Moosavi et al., 2019).

Adicionalmente, se incorporó información temporal mediante la API de TimeAndDate, permitiendo clasificar los accidentes según periodos del día y condiciones de iluminación, como sunrise/sunset y distintos niveles de twilight (Moosavi et al., 2019). Gracias a este proceso de enriquecimiento, el dataset permite analizar los accidentes desde distintas perspectivas y facilita la identificación de tendencias relacionadas con factores climáticos, temporales y geográficos.

La problemática identificada en este entorno empresarial se centra en la gran cantidad de datos generados diariamente y en la dificultad para convertir dicha información en conocimiento útil para la toma de decisiones estratégicas. Aunque existen millones de registros disponibles, muchas organizaciones presentan limitaciones para:
•	Analizar patrones de accidentes de manera eficiente. 
•	Identificar zonas de alto riesgo. 
•	Generar reportes estratégicos para prevención de accidentes. 
•	Relacionar variables como clima, horario, ubicación y severidad del accidente. 
Debido a ello, la información suele encontrarse dispersa y poco estructurada para procesos analíticos avanzados. Esto dificulta la implementación de estrategias preventivas y la optimización de recursos destinados a seguridad vial.

En ese contexto, el presente proyecto propone el desarrollo de una solución de Business Intelligence que permita consolidar y analizar la información mediante modelos dimensionales y dashboards interactivos. Con ello se busca mejorar la capacidad de análisis de accidentes de tránsito y facilitar la toma de decisiones basada en datos.

## Justificación del Modelo Dimensional

### Columnas incluidas y su dimensión

| Campo raw | Dimensión | Justificación |
|---|---|---|
| `Severity` | DimSeverity | Eje central del análisis: mide el impacto del accidente |
| `Start_Time` | DimFecha | Permite análisis temporal por hora, día, mes, año y fin de semana |
| `Start_Lat`, `Start_Lng` | DimUbicacionGeo | Coordenadas exactas para mapas y clustering geográfico |
| `Street`, `City`, `County`, `State`, `Zipcode`, `Timezone` | DimLugar | Agrupación geográfica administrativa para filtros en dashboards |
| `Weather_Condition` | DimClima | Variable clave para correlacionar clima con accidentes |
| `Crossing` | DimCrossing | Infraestructura vial con impacto directo en siniestralidad |
| `Junction` | DimJunction | Los cruces son puntos críticos de ocurrencia de accidentes |
| `Station` | DimStation | Las zonas de transporte incrementan el riesgo vial |
| `Stop` | DimStop | Las paradas generan puntos de conflicto en la vía |
| `Traffic_Signal` | DimTraffic | Indicador clave del nivel de control del tráfico |
| `Civil_Twilight` | DimCivilTwilight | Indica condición de luz natural, relevante para visibilidad |
| `End_Time`, `Distance(mi)`, métricas climáticas | FactAccidente | Son medidas cuantitativas únicas por accidente |

---

### Columnas excluidas y por qué

| Campo raw | Razón de exclusión |
|---|---|
| `Source` | Metadato administrativo sin valor analítico para BI |
| `End_Lat`, `End_Lng` | Redundante con `Start_Lat/Lng`; el punto de inicio es el relevante en accidentes |
| `Description` | Texto libre VARCHAR(MAX), no agrupable ni filtrable en dashboards |
| `Country` | El dataset es 100% EE.UU. — cardinalidad 1, no aporta segmentación |
| `Airport_Code` | Alta cardinalidad sin valor analítico directo; cubierto por `DimLugar` |
| `Weather_Timestamp` | Redundante con `Start_Time`; es la hora del reporte meteorológico, no del accidente |
| `Wind_Direction` | Baja relevancia analítica para BI de accidentes vs. el costo de una dimensión extra |
| `Amenity`, `Bump`, `Give_Way`, `No_Exit`, `Railway`, `Roundabout`, `Traffic_Calming`, `Turning_Loop` | Casi siempre `False` en el dataset — baja varianza, no aportan segmentación útil |
| `Sunrise_Sunset` | Cubierto con mayor granularidad por `Civil_Twilight` y `DimFecha.hora` |
| `Nautical_Twilight`, `Astronomical_Twilight` | Demasiado técnicos para dashboards ejecutivos; `Civil_Twilight` es suficiente |

---

### Criterios generales del diseño

El modelo fue diseñado para responder preguntas de negocio concretas:

- **¿En qué horarios y días ocurren más accidentes graves?** → `DimFecha` + `DimSeverity`
- **¿Qué condiciones climáticas están asociadas a mayor severidad?** → `DimClima`
- **¿Qué zonas geográficas concentran los accidentes?** → `DimLugar` + `DimUbicacionGeo`
- **¿Qué infraestructura vial está presente en accidentes graves?** → `DimCrossing`, `DimJunction`, `DimStation`, `DimStop`, `DimTraffic`

Se excluyeron campos que cumplían al menos uno de estos criterios:
- Cardinalidad de 1 (como `Country`)
- Texto libre no agregable (como `Description`)
- Baja varianza — casi siempre el mismo valor (como `Turning_Loop`, `Roundabout`)
- Redundancia con otro campo ya incluido (como `Sunrise_Sunset` vs. `Civil_Twilight`)

REFERENCIAS

Moosavi, S., Samavatian, M. H., Parthasarathy, S., & Ramnath, R. (2019). A countrywide traffic accident dataset. arXiv. https://arxiv.org/abs/1906.05409

Moosavi, S., Samavatian, M. H., Parthasarathy, S., & Ramnath, R. (2023). US-Accidents: A countrywide traffic accident dataset (2016–2023) [Dataset]. Kaggle. Kaggle US-Accidents Dataset https://www.kaggle.com/datasets/sobhanmoosavi/us-accidents

