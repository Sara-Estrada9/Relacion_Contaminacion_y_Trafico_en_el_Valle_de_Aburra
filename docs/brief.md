# BRIEF y contexto: Movilidad Urbana y Contaminación Atmosférica en el Valle de Aburrá

## 1. Pregunta

**Pregunta principal:** ¿Qué relación existe entre la intensidad de tráfico y las concentraciones de contaminantes atmosféricos (PM2.5) en el Valle de Aburrá?

Preguntas derivadas:

- ¿Con qué diferencia temporal (en horas o bloque de horas) el tráfico se asocia con cambios en PM2.5?
- ¿Cuánto de esa relación persiste teniendo en la meteorología (viento, lluvia, temperatura, humedad) e incluso geografía?
- ¿Es distinta la relación en estaciones de tráfico frente a estaciones de representatividad poblacional?

## 2. Quién decide

- **Decisión que informa:** priorizar medidas de gestión del tráfico dentro de las acciones para enfrentar episodios de contaminación (el AMVA opera el Plan Operacional para enfrentar Episodios de Contaminación Atmosférica, POECA).
- **Quién la tomaría:** Área Metropolitana del Valle de Aburrá (AMVA) / SIATA y la Secretaría de Movilidad de Medellín.
- **Mi rol:** analista. En esta fase es un ejercicio académico.

## 3. Fuente

| Rol | Fuente | Enlace | Granularidad | Actualización | Licencia | Estado |
|---|---|---|---|---|---|---|
| Tráfico (X) | Velocidad e intensidad vehicular en Medellín (sistema SIMM) | https://www.datos.gov.co/dataset/Velocidad-e-intensidad-vehicular-en-Medell-n/4q5i-ub4e | Horaria por carril | Mensual | CC BY-SA 4.0 | Verificada |
| Contaminante (Y) | Red de calidad del aire SIATA / AMVA, PM2.5 | https://siata.gov.co · https://datosabiertos.metropol.gov.co/ (análisis en proceso)
| Meteorología | Open-Meteo Historical Weather (ERA5 / ERA5-Land) | https://open-meteo.com/en/docs | Horaria | Diaria, con retraso de días | CC BY 4.0 (atribución obligatoria) | Variables en proceso de prueba |

**Fuente descartada para contaminantes:** Open-Meteo Air Quality (recomendada en clase). Es un modelo (CAMS) de resolución gruesa, no una medición, y un valle estrecho queda mal representado.

## 4. Alcance

- **Contaminante:** PM2.5.
- **Geografía:** estaciones SIATA en Medellín con corredores de tráfico cercanos (radios de 500 m, 1 km y 2 km, comparados).
- **Periodo:** ventana de solape de las tres fuentes, a definir tras la exploración. El tráfico abierto parte de marzo de 2017.
- **Granularidad:** una fila por hora y estación (llaves).
- **KPIs:** (quiero aplicar principalmente análisis de resultados con estadística descriptiva con datos históricos como indicadores iniciales)
1. Correlación de Spearman entre intensidad horaria y PM2.5, por estación.
2. Rezago óptimo (horas) de máxima correlación.
3. Pendiente ajustada: µg/m³ de PM2.5 por cada +1.000 veh/h, controlando meteorología.
4. R² incremental del tráfico sobre un modelo solo con meteorología.
5. Diferencia mediana de PM2.5 entre hora pico y hora valle.
6. Cobertura: % de horas con datos válidos pareados.
- **Frecuencia:** ejecución mensual y verificación semanal de esquema y disponibilidad. (depende principalmente de la vinculación y que la carga de data tenga una temporalidad o una frecuencia de carga que coincida para el modelo... revisar)
- **Destino del resultado:** este repositorio (documentación, tabla de KPIs, informes). 
- **Terminado cuando:**
  - un clon limpio reproduce la tabla horaria integrada con un solo comando;
  - la validación de esquema y calidad pasa;
  - un KPI recalculado a mano para una estación y un mes coincide con el del pipeline;
  - el informe responde la pregunta problematizadora.

## 5. Qué NO incluye

- Inferencia causal: se mide asociación, no causalidad.
- Modelos predictivos de Machine Learning.
- Tablero en tiempo real ni API propia.
- Modelos de emisiones por tipo de vehículo.
- Sensores de bajo costo de la red ciudadana.
- Otros municipios del valle en la parte de tráfico (la fuente cubre idealmente Medellín).
- Otras fuentes de emisión (industria, quema de biomasa).

## 6. Riesgos de calidad

| Riesgo | Por qué importa | Mitigación |
|---|---|---|
| Coordenadas ambiguas en tráfico: el diccionario las describe como coordenadas X e Y en Magna Sirgas, no como grados | El cruce espacial daría distancias erróneas | Revisar rangos reales; convertir todo a un sistema métrico común |
| Metadatos inconsistentes en el portal (fecha de modificación interna vs. última actualización) | No se sabe hasta cuándo llegan los datos | Usar el máximo de `fecha_trafico` |
| Cobertura incompleta: la fuente marca como falso el día con menos de 80 % de registros | Promedios sesgados | Filtrar por `identificador_fv` y reportar el % descartado |
| Acceso a PM2.5 horario sin verificar | Todo depende de esta fuente | Primera tarea: bajar una muestra real |
| Estaciones de tráfico con área de influencia pequeña | No representan todo el valle | Analizar por tipo de estación |
| Meteorología modelada (~10 km), no medida | Puede perder efectos locales | Declararlo como limitación |
| Zonas horarias y desfases | Arruinan el cálculo de rezagos | Todo en America/Bogota; pedir `timezone` a Open-Meteo |
| Posible cambio de fuente en la serie de intensidad (por confirmar las fuentes, aún investigando) | Rompe la continuidad | Buscar saltos de nivel por corredor y fecha |
| Variables omitidas (incendios, inversiones térmicas) | La asociación puede ser carente de autenticidad | Meteorología como control y discusión en el informe |
