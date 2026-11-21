Proyecto: Modelamiento Económico Cobre-Dólar-IPSA
Estado Actual: Volumen 1 completado — ETL de tres fuentes en notebooks

Objetivo del Proyecto
Modelar la relación económica entre el precio del cobre, el tipo de cambio dólar/CLP y el IPSA en Chile. El proyecto se organiza en volúmenes; el Volumen 1 cubre exclusivamente la construcción del dataset preparado mediante un ETL.

Alcance del Volumen 1
- Construir un ETL completo a partir de fuentes reales, aplicando análisis, limpieza y transformación con pandas.
- Extraer datos desde dos tipos de fuente: archivo CSV y API.
- Inspeccionar, limpiar y transformar los datos con pandas.
- Guardar un dataset preparado en formato Parquet para los siguientes volúmenes.
- Mantener un nivel de ingeniería coherente con un recién egresado: claro, reproducible y sin sobreingeniería.

Fuentes de Datos
1. IPSA: CSV histórico descargado desde Hugging Face (DVidelaS/historico_ipsa_chile) con huggingface_hub.
2. Dólar observado: API del Banco Central de Chile, serie F073.TCO.PRE.Z.D.
3. Cobre: API del Banco Central de Chile, serie F019.PPB.PRE.100.D.

Estructura
- src/data/ETL_IPSA.ipynb: ETL del IPSA.
- src/data/ETL_DOLAR_API.ipynb: ETL del dólar observado.
- src/data/ETL_COBRE_API.ipynb: ETL del cobre.
- data/raw/: CSV original del IPSA.
- data/processed/: ipsa_clean.parquet, dolar_clean.parquet y cobre_clean.parquet.
- src/models/: reservado para los siguientes volúmenes.
- notebooks/: reservado para análisis exploratorio adicional.
- .env: URL del dataset y credenciales de las APIs; .env.example documenta las variables.

Arquitectura y Decisiones
- Un notebook independiente por fuente, con estructura común: extracción, análisis inicial, transformación, limpieza y persistencia.
- Rutas resueltas con pathlib y detección de la raíz del proyecto.
- Variables de entorno con python-dotenv.
- Descarga del IPSA con huggingface_hub y escritura atómica en data/raw.
- Salidas en Parquet para preservar tipos de datos (datetime, float, int, string).
- Escritura atómica de los Parquet mediante archivo temporal.
- No se imputan valores faltantes: los NaN de dólar y cobre corresponden a fines de semana y feriados, y se eliminan con justificación documentada.
- La columna Vol. del IPSA se elimina por estar completamente vacía.

Resultados Actuales
- IPSA: 707 filas, 10 columnas, rango 2024-01-19 a 2026-11-20.
- Dólar: 721 filas, 6 columnas, rango 2024-01-02 a 2026-11-23.
- Cobre: 721 filas, 6 columnas, rango 2024-01-02 a 2026-11-23.
- Los tres datasets tienen 0 valores nulos y 0 duplicados en su versión final.
- Intersección exacta de fechas entre IPSA y las series de API: 707 fechas. Las APIs contienen 14 fechas adicionales al inicio del período.

Observaciones y Pendientes
- Los notebooks de API entregan fechas hasta 2026-11-23, posterior a la fecha de auditoría (2026-11-21). Verificar si la serie publica observaciones futuras o si corresponde a un desfase de la API antes de usar los datos en modelamiento.
- Definir en el Volumen 2 la política de alineación de fechas entre IPSA, dólar y cobre (intersección, días hábiles, feriados).
- Actualizar README para reflejar los tres notebooks y las tres salidas Parquet.
- Incorporar un diccionario de datos breve: unidades del cobre, del dólar y de porcentaje_variacion.
- Agregar una tabla de validación posterior a la limpieza con dimensiones, tipos, nulos, duplicados y rangos.
- Evaluar la duplicación entre ETL_DOLAR_API.ipynb y ETL_COBRE_API.ipynb; es aceptable en el Volumen 1 por claridad didáctica, pero puede consolidarse más adelante.
- No hay tests automatizados ni CI; para un proyecto de portafolio de nivel junior es una mejora recomendable, no un bloqueante del Volumen 1.

Siguientes Volúmenes
- Volumen 2: análisis exploratorio, alineación temporal y análisis de relaciones entre variables.
- Volumen 3: feature engineering y preparación para modelamiento.
- Volumen 4: modelamiento, evaluación e interpretación.
