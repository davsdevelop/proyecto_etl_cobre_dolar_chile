Proyecto: Modelamiento Económico Cobre-Dólar-IPSA
Estado Actual: Fase 1 - Ingesta, validación y transformación en notebook único
Objetivo del Proyecto
Modelar la relación económica entre el precio del cobre, el tipo de cambio y el IPSA en Chile.

Arquitectura y Decisiones Técnicas

1. Gestión de Entorno y Rutas
- Notebook único: src/data/ETL_IPSA.ipynb.
- Uso de pathlib y detección dinámica de PROJECT_ROOT a partir de .git o pyproject.toml.
- Variables de entorno cargadas desde .env con python-dotenv.

2. Ingesta de Datos
- Descarga automática desde Hugging Face usando huggingface_hub (repo DVidelaS/historico_ipsa_chile, repo_type="dataset").
- La URL se configura mediante IPSA_DATASET_URL y la revisión opcional mediante IPSA_DATASET_REVISION.
- Escritura atómica en data/raw: descarga a un archivo temporal y reemplazo final.
- Si el archivo ya existe, se reutiliza salvo que FORCE_DOWNLOAD se establezca en True.

3. Procesamiento y Transformación (ETL en notebook)
- La lógica vive directamente en las celdas del notebook, sin módulos .py externos.
- Análisis inicial del dataset crudo con pandas.
- Transformación: normalización de nombres, parseo de números en formato español, conversión de fechas y variables derivadas (dia, mes, anio, dia_semana).
- Ordenamiento cronológico y eliminación de la columna vacía Vol.
- El flujo conserva el estilo de análisis exploratorio definido originalmente en el notebook.

4. Persistencia
- Salida en formato Parquet: data/processed/ipsa_clean.parquet.
- Parquet preserva los tipos de datos (datetime64, float64, int32 y string), evitando la pérdida de tipos del CSV.
- Escritura atómica mediante archivo temporal y reemplazo final.
- El CSV de salida anterior fue reemplazado por Parquet.

5. Flujo de datos
data/raw (CSV original) -> validación de esquema -> transformación -> validación de calidad -> data/processed/ipsa_clean.parquet

6. Limitaciones actuales y siguientes pasos
- El dataset solo contiene el IPSA; aún faltan las series de cobre y tipo de cambio para el modelamiento.
- No hay tests automatizados ni pipeline CI.
- Las dependencias no están fijadas con lockfile.
- Próximos pasos: tests con pytest, contratos con pandera, CI en GitHub Actions y versionado de datos con DVC o revisiones fijas de Hugging Face.
