# ETL Cobre / Dólar Chile

Proyecto de ingesta, análisis y transformación de datos históricos del IPSA, organizado siguiendo la metodología **Cookiecutter Data Science**. Toda la lógica ETL vive en un notebook único: `src/data/ETL_IPSA.ipynb`.

## Estructura del proyecto

```text
.
├── data/
│   ├── raw/          # CSV original descargado desde Hugging Face
│   ├── interim/      # Datos intermedios
│   └── processed/    # ipsa_clean.parquet listo para análisis
├── notebooks/        # Análisis exploratorio (EDA)
├── src/
│   ├── data/
│   │   └── ETL_IPSA.ipynb    # flujo ETL completo
│   └── models/       # Código modular de modelado
├── requirements.txt
└── README.md
```

## Uso

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
```

Abre `src/data/ETL_IPSA.ipynb` y ejecuta las celdas en orden, o usa **Restart & Run All**.

## Dependencias

`requirements.txt` declara únicamente las dependencias directas del proyecto, fijadas a su versión estable más reciente:

```text
pandas==3.1.1
pyarrow==26.0.1
huggingface_hub==2.4.0
python-dotenv==1.2.4
requests==2.34.2
ipykernel==7.4.0
nbconvert==7.17.2
```

Versión de Python recomendada: **3.12 o superior** (probado con 3.14.5).

## Flujo del notebook

1. **Extracción:** descarga automática del CSV desde Hugging Face con `huggingface_hub`, creando las carpetas necesarias y escribiendo de forma atómica en `data/raw/`. Si el archivo ya existe, se reutiliza; `FORCE_DOWNLOAD = True` fuerza una nueva descarga.
2. **Análisis inicial:** exploración del dataset crudo con pandas.
3. **Transformación:** normaliza nombres, convierte fechas y números en formato español, elimina `Vol.` y agrega variables derivadas.
4. **Persistencia:** guarda `data/processed/ipsa_clean.parquet`, preservando los tipos de datos.

## Configuración

`.env` define la fuente de datos:

```dotenv
IPSA_DATASET_URL=https://huggingface.co/datasets/DVidelaS/historico_ipsa_chile/resolve/main/datos_historicos_del_IPSA.csv
```

Opcionalmente, `IPSA_DATASET_REVISION` permite fijar un commit o tag del dataset para que la descarga sea reproducible.

## Salida

```text
data/processed/ipsa_clean.parquet
```

Parquet preserva los tipos (`datetime64`, `float64`, `int32`, `string`) y evita la pérdida de tipos que ocurre al releer un CSV.

## Exportar el notebook a PDF

```bash
jupyter nbconvert --to pdf src/data/ETL_IPSA.ipynb
```
