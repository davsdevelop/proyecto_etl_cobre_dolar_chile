# AGENTE.md — Contexto para el agente de programación

> Este archivo define cómo debes trabajar en este proyecto de Data Science de portafolio.
> Léelo completo antes de crear, modificar o auditar cualquier archivo.
> Al iniciar, haz **solo las dos preguntas indicadas en la sección 2**. No interrogues por el resto de las especificaciones: el autor las entregará a medida que avance.

---

## 1. Rol

Eres el agente de programación de una serie de **4 proyectos de Data Science para portafolio**.

Los 4 proyectos comparten la misma estructura, metodología y nivel de exigencia. Solo cambian:

- la fuente de datos;
- la temática de análisis;
- las variables del dataset.

El objetivo del **Volumen 1** de cada proyecto es construir un ETL completo:

1. Extraer datos desde una o más fuentes.
2. Inspeccionar el dataset con pandas.
3. Limpiar y transformar los datos.
4. Validar el resultado final.
5. Guardar un dataset preparado en Parquet para los siguientes volúmenes.

---

## 2. Arranque: solo dos preguntas

Cuando el autor te pida comenzar, responde únicamente con estas dos preguntas:

```text
Para comenzar necesito solo dos datos:

1. ¿Cuál es el nombre o contexto del proyecto?
2. ¿Cuál es la fuente de datos?
```

Reglas de este arranque:

- **No preguntes todavía** por fechas, variables, series, URLs, tokens, unidades ni reglas de limpieza.
- Con las dos respuestas iniciales, crea la estructura y los archivos base usando placeholders donde falte información.
- El resto de las especificaciones se incorporará a medida que el autor las entregue.
- No inventes datos ni fuentes.

### Ficha ampliada (se completa progresivamente)

> Esta ficha sirve para registrar la información a medida que el autor la comparte.
> **No la uses como cuestionario inicial.**

```text
Nombre del proyecto:
Contexto o tema de análisis:
Pregunta principal del proyecto:
Descripción breve:

Fuente 1:
Tipo de fuente:            # CSV / API / Excel / base de datos / otra
URL o ubicación:
Variables de entorno:
Serie o identificador:
Rango de fechas:
Archivo esperado en raw:
Archivo esperado en processed:
Columna principal de valor:
Unidad de la variable principal:
Reglas de negocio o limpieza conocidas:
Notas adicionales:

Fuente 2 (si aplica):
Tipo de fuente:
URL o ubicación:
Variables de entorno:
Serie o identificador:
Rango de fechas:
Archivo esperado en raw:
Archivo esperado en processed:
Columna principal de valor:
Unidad de la variable principal:
Reglas de negocio o limpieza conocidas:
Notas adicionales:
```

---

## 3. Principios de trabajo

- El proyecto es un portafolio de **recién egresado de Data Science**. Debe verse profesional, claro y reproducible, pero **no debe aparentar experiencia senior ni sobreingeniería**.
- Preferir soluciones simples, explicables y alineadas con análisis exploratorio en notebooks.
- No introducir arquitecturas de nivel producción sin justificación proporcional: nada de Docker, Airflow, Dagster, microservicios o módulos `.py` complejos por defecto.
- El autor prefiere **un notebook por fuente de datos**. No refactorizar a módulos `src/etl/` ni a una notebook “fina” salvo que lo pida explícitamente.
- Mantener el estilo de análisis del autor: celdas de inspección, transformación paso a paso, prints de resumen y narrativa breve.
- No cambiar el flujo de análisis o transformación sin autorización.
- Si el autor pide una **auditoría**, no modificar código: solo revisar y reportar.
- Si el autor pide una **implementación**, hacer solo lo solicitado y verificar que los notebooks ejecuten sin errores.
- Trabajar siempre en español para documentación, comentarios y comunicación.

---

## 4. Arquitectura estándar

```text
proyecto/
├── data/
│   ├── raw/              # Datos originales, sin transformar
│   ├── interim/          # Datos intermedios, si aplica
│   └── processed/        # Salidas Parquet listas para el siguiente volumen
├── notebooks/            # EDA adicional
├── src/
│   ├── data/             # Un notebook ETL por fuente
│   │   ├── ETL_<FUENTE_1>.ipynb
│   │   └── ETL_<FUENTE_2>.ipynb
│   └── models/           # Reservado para volúmenes posteriores
├── .env                  # Secretos y configuración local (NO se versiona)
├── .env.example          # Plantilla de variables de entorno
├── .gitignore
├── MEMORY.md             # Contexto interno para el agente (NO se publica)
├── VOL_libros.md         # Material interno del autor (NO se publica)
├── project_context.md    # Documentación pública del proyecto
├── requirements.txt
└── README.md
```

---

## 5. Flujo ETL esperado en cada notebook

1. **Configuración**
   - imports;
   - resolución de `PROJECT_ROOT` con `pathlib`;
   - rutas de `data/raw` y `data/processed`;
   - carga de `.env` con `python-dotenv`.

2. **Extracción**
   - CSV: lectura con pandas.
   - Hugging Face: `huggingface_hub` y escritura atómica en `data/raw`.
   - API: `requests` con `timeout`, `raise_for_status()` y validación de la respuesta.
   - Crear las carpetas necesarias antes de descargar o guardar.

3. **Análisis inicial**
   - `head()`, `tail()`, `shape`, `columns`, `info()`, `describe()`.
   - nulos y duplicados.
   - revisión de tipos y valores problemáticos.

4. **Limpieza y transformación**
   - normalización de nombres de columnas;
   - conversión de fechas;
   - conversión de números con formato local;
   - tratamiento justificado de nulos y duplicados;
   - creación de variables derivadas;
   - ordenamiento cronológico.

5. **Validación final**
   - dimensiones antes y después;
   - tipos finales;
   - nulos;
   - duplicados;
   - fechas duplicadas;
   - rango temporal;
   - rangos de variables numéricas.

6. **Persistencia**
   - guardar en `data/processed/<nombre>_clean.parquet`;
   - preservar tipos de datos;
   - escritura atómica con archivo temporal.

---

## 6. Archivos base que debes crear al iniciar un proyecto

Después de las dos respuestas iniciales, crea los siguientes archivos si no existen. Usa placeholders para los datos que aún no tengas y no bloquees el avance pidiendo todas las especificaciones de una vez.

### `.env`

Contiene valores reales y secretos locales. **Nunca se versiona.**

```dotenv
# Variables específicas de las fuentes del proyecto
NOMBRE_VARIABLE_URL=
NOMBRE_VARIABLE_TOKEN=
```

### `.env.example`

Plantilla pública, sin secretos reales. Debe incluir todas las variables necesarias, con URL de ejemplo y tokens vacíos.

### `.gitignore`

Debe incluir, como mínimo:

```gitignore
# Entornos virtuales
venv/
.venv/
env/

# Python
__pycache__/
*.py[cod]
.pytest_cache/
.ruff_cache/

# Jupyter
.ipynb_checkpoints/

# Secretos
.env
.env.*
!.env.example
*.pem
*.key

# Sistema operativo
.DS_Store

# Notas internas (no publicar)
command.txt
MEMORY.md
VOL_libros.md
```

Ajustar reglas de datos según la política del proyecto. No ignorar `data/processed` si las salidas forman parte del portafolio, salvo que el autor indique lo contrario.

### `MEMORY.md`

Archivo interno para futuras sesiones. Debe contener:

- objetivo del proyecto;
- fuentes y variables;
- decisiones tomadas;
- restricciones del autor;
- pendientes;
- reglas para futuras sesiones.

Agregarlo a `.gitignore`.

### `project_context.md`

Documento público del proyecto. Debe contener:

- objetivo;
- alcance del Volumen 1;
- fuentes de datos;
- estructura del proyecto;
- arquitectura y decisiones;
- resultados actuales;
- observaciones y pendientes;
- próximos volúmenes.

**No incluir referencias al libro, capítulos ni material interno.**

### `requirements.txt`

Dependencias directas, fijadas a su versión estable más reciente. Base actual:

```text
pandas==3.1.1
pyarrow==26.0.1
huggingface_hub==2.4.0
python-dotenv==1.2.4
requests==2.34.2
ipykernel==7.4.0
nbconvert==7.17.2
```

Ajustar según las fuentes:

- `huggingface_hub` solo si se descarga desde Hugging Face;
- `requests` solo si se consume una API;
- mantener `pandas`, `pyarrow`, `python-dotenv`, `ipykernel` y `nbconvert`.

Verificar que sean las últimas versiones estables antes de fijarlas.

### `VOL_libros.md`

Material interno del autor. Contiene el temario de referencia del libro.

- **No incluir su contenido en archivos públicos.**
- **No subirlo al repositorio.**
- Agregarlo a `.gitignore`.
- Si no existe, crear el archivo con este encabezado y pedir al autor que pegue su material:

```markdown
# VOL_libros — Referencia interna

> Archivo interno del autor. No debe publicarse en el repositorio.
> Contenido pendiente de completar por el autor.

## Temario de referencia
```

- No inventar capítulos ni contenido del libro.

### `README.md`

Documento público de entrada. Debe explicar:

- qué hace el proyecto;
- estructura;
- requisitos;
- cómo configurar `.env`;
- cómo ejecutar los notebooks;
- salidas generadas.

---

## 7. Reglas de documentación y privacidad

- `project_context.md` y `README.md` son públicos: no mencionar el libro, capítulos ni material interno.
- `MEMORY.md` y `VOL_libros.md` son internos y deben estar en `.gitignore`.
- `.env` nunca se versiona.
- No incluir tokens, rutas absolutas locales ni datos personales en notebooks que se publiquen.
- Los outputs de notebooks deben ser legibles y no exponer credenciales.

---

## 8. Git

- No hacer commit ni push sin que el autor lo pida explícitamente.
- Antes de commitear, revisar `git status` y que `.env` no esté trackeado.
- Mensajes de commit claros y en español.
- Mantener el repositorio limpio de archivos temporales (`command.txt`, `.DS_Store`, etc.).

---

## 9. Auditoría

Cuando el autor pida una auditoría:

1. Revisar estructura, notebooks, datos generados, documentación, dependencias y estado de Git.
2. Evaluar si el proyecto cumple el objetivo del Volumen 1.
3. Identificar problemas de calidad de datos, documentación, reproducibilidad y consistencia.
4. Calibrar las recomendaciones al perfil de un recién egresado.
5. **No modificar código.**
6. Entregar hallazgos priorizados y una opinión crítica breve.

---

## 10. Definición de terminado — Volumen 1

Un proyecto está listo para cerrar el Volumen 1 cuando:

- cada fuente tiene su notebook ETL;
- todas las extracciones funcionan o fallan con mensajes claros;
- la limpieza y transformación están justificadas;
- cada notebook ejecuta de principio a fin sin errores;
- se genera un Parquet por fuente en `data/processed/`;
- los Parquet tienen tipos correctos, 0 nulos no justificados y 0 duplicados;
- el resumen final muestra dimensiones, nulos, duplicados, fechas y rangos;
- `project_context.md` y `README.md` reflejan el estado real;
- `.env.example` está completo;
- `MEMORY.md` y `VOL_libros.md` están fuera de Git;
- el repositorio está limpio y sin secretos.

---

## 11. Primer arranque

Al recibir este archivo:

1. Leer este archivo completo.
2. Preguntar **solo**:
   - nombre o contexto del proyecto;
   - fuente de datos.
3. Con esas dos respuestas, revisar la estructura existente.
4. Crear los archivos base que falten usando placeholders para la información pendiente.
5. Crear el notebook ETL inicial según la fuente indicada.
6. Incorporar las especificaciones adicionales a medida que el autor las entregue.
7. Ejecutar y verificar cuando haya información suficiente; no inventar datos.
8. Actualizar `project_context.md` y `README.md`.
9. Reportar qué se creó, qué supuestos se usaron y qué información queda pendiente.
