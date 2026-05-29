# PROYECTO_BI

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

REFERENCIAS

Moosavi, S., Samavatian, M. H., Parthasarathy, S., & Ramnath, R. (2019). A countrywide traffic accident dataset. arXiv. https://arxiv.org/abs/1906.05409

Moosavi, S., Samavatian, M. H., Parthasarathy, S., & Ramnath, R. (2023). US-Accidents: A countrywide traffic accident dataset (2016–2023) [Dataset]. Kaggle. Kaggle US-Accidents Dataset

