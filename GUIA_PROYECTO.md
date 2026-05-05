# Guía Completa del Proyecto: Análisis de Siniestros Viales — SUTRAN

> Esta guía complementa el notebook `proyecto_siniestros_viales.ipynb`. Aquí tienes
> (1) la estructura recomendada del informe escrito, y
> (2) la guía paso a paso para construir el dashboard en Power BI con todas las visualizaciones que pide la rúbrica.

---

## 📁 1. Estructura de carpetas del proyecto

Organiza tu proyecto así antes de empezar (esto ayuda mucho en la exposición):

```
proyecto_siniestros_viales/
│
├── 01_data/
│   ├── data_raw.csv                       ← descargado de la PNDA
│   └── data_clean.csv                     ← generado por el notebook (LOAD)
│
├── 02_notebook/
│   └── proyecto_siniestros_viales.ipynb   ← notebook principal
│
├── 03_powerbi/
│   └── dashboard_siniestros.pbix          ← Power BI Desktop
│
├── 04_informe/
│   └── informe_final.docx                 ← documento Word con narrativa
│
├── 05_presentacion/
│   └── exposicion.pptx                    ← 8-10 minutos
│
└── README.md                              ← descripción breve del proyecto
```

---

## 📄 2. Estructura del Informe Escrito (Word)

### Portada
- Universidad Nacional de Ingeniería — Facultad de Ciencias
- Curso: Big Data Analysis (CC531)
- Título: *Análisis Geoespacial y Temporal de la Siniestralidad Vial en la Red Vial Nacional del Perú (2020-2021) — Caso CGM-SUTRAN*
- Integrantes (3) y código de matrícula
- Docente, semestre y fecha

### 1. Introducción (½ página)
- Contexto: la seguridad vial como problema de salud pública.
- Objetivo del proyecto.
- Alcance: análisis exploratorio y descriptivo (no predictivo).

### 2. Descripción del Dataset (1 página)
> **Esto va en el informe tomado del Markdown del notebook (sección 1).**

Subsecciones:
- **2.1 Origen y rubro** → SUTRAN, Plataforma Nacional de Datos Abiertos.
- **2.2 Teoría detrás de los datos** → siniestralidad vial, monitoreo CGM, marco normativo (Reglamento Nacional de Tránsito).
- **2.3 Características** → 3,832 registros, 14 columnas originales, encoding latin-1, separador `;`, periodo 01-Ene-2020 al 30-Nov-2021.
- **2.4 Diccionario de campos** → tabla con las 14 columnas, tipos y dominios.
- **2.5 Reglas de los datos** → restricciones y dominios.
- **2.6 Información que se puede obtener** → 5-6 viñetas con preguntas de negocio.

### 3. Aplicación de ETL a la Base de Datos (1-2 páginas) ⭐
> **Esta subsección es de las más puntuadas. Sé muy explícito con cada fase.**

- **3.1 Extract** — proceso de descarga, particularidades (encoding, separador), código `pd.read_csv` con sus parámetros.
- **3.2 Transform** — listar las 6 transformaciones aplicadas:
  1. Conversión de `FECHA_ACC` y `FECHA_CORTE` (entero → datetime).
  2. Conversión de `HORA_ACC` a hora numérica.
  3. Imputación de nulos en `CODIGO_VIA` y `PROGRESIVA`.
  4. Filtrado geográfico (bounding box del Perú).
  5. Normalización de texto (mayúsculas, strip).
  6. Generación de columnas derivadas (FRANJA_HORARIA, GRAVEDAD, CATEGORIA_ACC, ANIO, MES, DIA_SEMANA).
- **3.3 Load** — guardado como `data_clean.csv` UTF-8 con BOM (`utf-8-sig`) compatible con Power BI y Excel.

Incluye **un diagrama de flujo simple** del proceso ETL (puedes hacerlo en draw.io o PowerPoint).

### 4. Consultas Analíticas en Python (4-5 páginas)
Por cada consulta, sigue **rigurosamente** este formato (es lo que pide la rúbrica):

> **Consulta N**  
> **Tipo de pregunta:** [agregación / correlación / tendencia / distribución / etc.]  
> **Pregunta sobre el dataset:** *"¿..."* (en lenguaje natural)  
> **Solución técnica:** breve descripción del groupby/método aplicado.  
> **[Insertar gráfico]**  
> **Interpretación:** 2-4 líneas explicando qué se observa y qué responde a la pregunta.

Estructura por subsecciones:
- **4.1** Gráfico 1 con groupby de 3+ columnas (count + sum)
- **4.2** Gráfico 2 con groupby de 4 columnas (heatmap)
- **4.3** Generación de 3 columnas derivadas (con uso futuro de cada una)
- **4.4** Matriz de correlación (≥6 columnas) con seaborn — **interpretar cada par fuerte**
- **4.5** Gráficos de barras (2)
- **4.6** Gráficos de líneas (2)
- **4.7** Tendencias acumulativas (3)
- **4.8** Gráficos circulares (2) con condicionales

### 5. Dashboard en Power BI (2-3 páginas)
> **Captura cada visualización con una imagen y descríbela usando el mismo formato Tipo/Pregunta/Solución/Interpretación.**

- **5.1** 2 gráficos de columnas apiladas (vertical y horizontal)
- **5.2** 2 gráficos de líneas
- **5.3** Gráfico de dispersión
- **5.4** Gráfico de mapa
- **5.5** Gráfico circular

### 6. Conclusiones (½ página)
5-6 viñetas con los hallazgos más importantes.

### 7. Referencias
- Plataforma Nacional de Datos Abiertos: https://www.datosabiertos.gob.pe/
- Página del dataset: https://www.datosabiertos.gob.pe/dataset/reportes-preliminares-de-accidentes-centro-de-gesti%C3%B3n-y-monitoreo
- Reglamento Nacional de Tránsito (DS 016-2009-MTC).
- Documentación SUTRAN, Centro de Gestión y Monitoreo.
- McKinney, W. (2017). *Python for Data Analysis*. O'Reilly.
- Documentación de pandas, seaborn, matplotlib y Power BI.

### Anexos
- Capturas de pantalla del notebook ejecutado.
- Capturas del dashboard Power BI a alta resolución.

---

## 📊 3. Guía paso a paso para Power BI

### 3.1 Cargar el dataset

1. Abre **Power BI Desktop**.
2. `Inicio` → `Obtener datos` → `Texto/CSV`.
3. Selecciona `data_clean.csv` (el limpio, no el crudo).
4. En la previsualización, verifica que el separador detectado sea **coma** y la codificación **65001: Unicode (UTF-8)** (porque guardamos con `utf-8-sig`).
5. Click en **Cargar**.

### 3.2 Verificar tipos de datos

En la vista de **Tabla**, asegúrate que:

| Campo | Tipo correcto |
|---|---|
| `FECHA_ACC` | Fecha |
| `LATITUD`, `LONGITUD` | Número decimal |
| `CANT_FALLECIDOS`, `CANT_HERIDOS`, `GRAVEDAD`, `HORA_NUM`, `ANIO`, `MES` | Número entero |
| El resto | Texto |

Si alguno está mal, click derecho sobre la columna → `Cambiar tipo`.

### 3.3 Categorización geográfica (CRÍTICO para el mapa)

1. Selecciona la columna `LATITUD` → en la pestaña `Modelado` → `Categoría de datos` → **Latitud**.
2. Repite con `LONGITUD` → **Longitud**.
3. Selecciona `DEPARTAMENTO` → `Categoría de datos` → **Estado o provincia**.

> Sin este paso, Power BI no entiende que las coordenadas son geográficas y el mapa puede fallar.

### 3.4 Las 5 visualizaciones que pide la rúbrica

#### 📊 Visual 1: Columnas Apiladas Verticales
- **Pregunta:** *"¿Cómo se distribuyen los accidentes por departamento, separados por modalidad?"*
- **Visual:** `Gráfico de columnas apiladas`
- **Eje X:** `DEPARTAMENTO`
- **Eje Y:** `Recuento de NRO_REPORTE`
- **Leyenda:** `MODALIDAD_ACC`
- **Filtro de visual:** Top N → 10 departamentos por recuento
- **Interpretación esperada:** Lima, Arequipa y Junín lideran; en cada departamento se ve la composición de modalidades.

#### 📊 Visual 2: Columnas Apiladas Horizontales (Barras)
- **Pregunta:** *"¿Cuáles son las rutas nacionales con más fallecidos según franja horaria?"*
- **Visual:** `Gráfico de barras apiladas`
- **Eje Y:** `CODIGO_VIA`
- **Eje X:** `Suma de CANT_FALLECIDOS`
- **Leyenda:** `FRANJA_HORARIA`
- **Filtro:** Top 10 códigos de vía por suma de fallecidos
- **Interpretación esperada:** PE-1N (Panamericana Norte) y PE-1S (Panamericana Sur) lideran en fallecidos.

#### 📈 Visual 3: Líneas (1)
- **Pregunta:** *"¿Cómo evoluciona mensualmente el número de accidentes en 2020 vs 2021?"*
- **Visual:** `Gráfico de líneas`
- **Eje X:** `MES`
- **Eje Y:** `Recuento de NRO_REPORTE`
- **Leyenda:** `ANIO`
- **Interpretación esperada:** se ve la caída de marzo-junio 2020 por confinamiento y la recuperación 2021.

#### 📈 Visual 4: Líneas (2)
- **Pregunta:** *"¿Cómo evolucionan los heridos por franja horaria a lo largo del periodo?"*
- **Visual:** `Gráfico de líneas`
- **Eje X:** `FECHA_ACC` (jerarquía Año → Trimestre → Mes)
- **Eje Y:** `Suma de CANT_HERIDOS`
- **Leyenda:** `FRANJA_HORARIA`

#### 🟢 Visual 5: Dispersión
- **Pregunta:** *"¿Existe relación entre la cantidad de fallecidos y heridos por accidente, según modalidad?"*
- **Visual:** `Gráfico de dispersión`
- **Eje X:** `CANT_FALLECIDOS` (no agregado: click derecho → "No resumir")
- **Eje Y:** `CANT_HERIDOS` (no agregado)
- **Leyenda:** `MODALIDAD_ACC`
- **Detalles:** `NRO_REPORTE` (hace cada punto único)
- **Tamaño:** `GRAVEDAD`
- **Interpretación esperada:** la mayoría de puntos se concentra en valores bajos (0-2 fallecidos, 0-3 heridos), con outliers visibles para volcaduras y choques masivos.

#### 🗺️ Visual 6: Mapa ⭐ (LO MÁS IMPORTANTE)
- **Pregunta:** *"¿Dónde se concentran geográficamente los siniestros viales en el Perú?"*
- **Visual:** `Mapa` (el básico de bolas, no el de relleno)
- **Latitud:** `LATITUD`
- **Longitud:** `LONGITUD`
- **Tamaño de burbuja:** `Suma de GRAVEDAD`
- **Leyenda:** `MODALIDAD_ACC`
- **Información sobre herramientas:** `DEPARTAMENTO`, `CANT_FALLECIDOS`, `CANT_HERIDOS`
- **Interpretación esperada:** se ven hotspots claros en el corredor de la Panamericana (costa) y la Carretera Central; puntos dispersos en la selva.

> Si el mapa no carga, ve a `Archivo` → `Opciones` → `Configuración global` → `Seguridad` → activa "Usar mapas globales" / "Usar Bing Maps".

#### 🥧 Visual 7: Circular
- **Pregunta:** *"¿Cuál es la distribución de modalidades en accidentes con al menos un fallecido?"*
- **Visual:** `Gráfico circular`
- **Leyenda:** `MODALIDAD_ACC`
- **Valores:** `Recuento de NRO_REPORTE`
- **Filtro de visual:** `CANT_FALLECIDOS` > 0  *(esto cumple con el "condicional" que pide la rúbrica)*

### 3.5 Segmentadores (slicers) recomendados

Agrega estos **3 segmentadores** en la parte superior del dashboard para que sea interactivo:

1. `ANIO` (segmentador desplegable o botones)
2. `DEPARTAMENTO` (lista con búsqueda)
3. `FECHA_ACC` (segmentador de rango de fechas)

Estos no cuentan como visualizaciones de la rúbrica, pero **mejoran muchísimo la presentación** y demuestran dominio de Power BI durante la exposición.

### 3.6 KPIs (tarjetas) — opcional pero recomendado

En la parte superior, agrega 4 tarjetas (Visual `Tarjeta`) con:

- Total de accidentes: `CONTAR(NRO_REPORTE)`
- Total fallecidos: `SUMA(CANT_FALLECIDOS)`
- Total heridos: `SUMA(CANT_HERIDOS)`
- % accidentes fatales: medida DAX:
   ```dax
   % Fatales = DIVIDE(
       CALCULATE(COUNTROWS(data_clean), data_clean[CATEGORIA_ACC]="FATAL"),
       COUNTROWS(data_clean)
   )
   ```

### 3.7 Layout sugerido del dashboard

```
┌──────────────────────────────────────────────────────────────┐
│  Análisis de Siniestros Viales en Perú (2020-2021) — SUTRAN │
├──────────┬──────────┬──────────┬─────────────────────────────┤
│ KPI: Acc │ KPI: Fal │ KPI: Her │  Segmentadores: Año / Dpto  │
├──────────┴──────────┴──────────┴─────────────────────────────┤
│                                  │                           │
│   🗺️ MAPA (visual destacado)     │   📊 Columnas apiladas    │
│       ocupando 60% × 40%         │       por departamento     │
│                                  │                           │
├──────────────────────┬───────────┴─────────┬─────────────────┤
│  📈 Líneas mensuales │  📊 Barras por ruta │  🥧 Circular    │
│      (2020 vs 2021)  │      con fallecidos │     fatales     │
├──────────────────────┴─────────────────────┴─────────────────┤
│  📈 Líneas franja horaria  │  🟢 Dispersión fall vs her       │
└────────────────────────────┴─────────────────────────────────┘
```

---

## 🎤 4. Estructura de la exposición (8-10 minutos)

| Tiempo | Quién | Qué |
|---|---|---|
| 0:00–1:00 | Integrante 1 | Introducción + descripción del dataset (rubro, fuente, tamaño) |
| 1:00–2:30 | Integrante 1 | Teoría de los datos + ETL (mostrar las 3 fases en el notebook) |
| 2:30–6:00 | Integrante 2 | Recorrido por las consultas Python (sin leerlas todas — escoger las 3-4 más impactantes: matriz de correlación, una tendencia acumulativa, un circular condicional, un heatmap) |
| 6:00–8:30 | Integrante 3 | Demo del dashboard Power BI **interactivo**: usar los segmentadores en vivo, mostrar el mapa con zoom a Lima, filtrar por modalidad |
| 8:30–9:30 | Todos | Conclusiones (uno habla, los otros apoyan en pantalla) |
| 9:30–10:00 | Todos | Preguntas |

**Tip clave para la exposición:** no leas el notebook ni el dashboard. **Cuenta una historia**: "El Perú tuvo X accidentes en 2 años, los choques son la primera causa, el confinamiento bajó la siniestralidad, el mapa muestra que las Panamericanas concentran los hotspots". Los datos son el respaldo de tu narrativa, no la narrativa misma.

---

## ✅ 5. Checklist final antes de la entrega

- [ ] Dataset crudo (`data_raw.csv`) y limpio (`data_clean.csv`) en la carpeta de datos.
- [ ] Notebook ejecutado completo, sin errores ni warnings.
- [ ] Cada celda de código tiene comentarios explicativos.
- [ ] Cada consulta tiene su markdown previo con Tipo + Pregunta + Solución + Interpretación.
- [ ] Dashboard `.pbix` con las 5 visualizaciones obligatorias + segmentadores.
- [ ] Informe Word con todas las secciones (incluyendo capturas).
- [ ] Presentación PPTX coordinada para 8-10 minutos.
- [ ] Los 3 integrantes saben explicar **cualquier** parte del proyecto (la rúbrica evalúa al equipo, no individualmente).
- [ ] Backup en la nube (Google Drive / OneDrive) por si falla el USB el día de la exposición.

---

## 🚨 6. Errores comunes a evitar

1. **Encoding incorrecto al leer el CSV** → usar `encoding='latin-1'` y `sep=';'`.
2. **Olvidar la categorización geográfica en Power BI** → el mapa no carga.
3. **Quedarse con `data_raw.csv` en Power BI** → no tendrás las columnas derivadas.
4. **Gráficos circulares con demasiadas categorías** → siempre filtra a Top 5-7 o agrupa el resto en "Otros".
5. **Matriz de correlación sin interpretar** → la rúbrica pide explícitamente "interpretar".
6. **No mencionar tipo de pregunta + consulta + interpretación** → es lo que más puntúa.
7. **Hacer el dashboard sin segmentadores** → se ve estático y poco profesional.
8. **No ejecutar el notebook antes de la presentación** → si pierdes conexión a internet, los gráficos generados quedan guardados; los que requieren librerías no.
