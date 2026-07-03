# TP Minería de Datos — Predicción de Abandono Temprano en Spotify (UTDT, MiM+AI)

Competencia Kaggle: predecir la probabilidad de que una reproducción de Spotify sea un "abandono temprano" (menos de 30 segundos escuchados). Métrica: ROC-AUC.

## Contenido

- `tp_spotify_claseN_<tema>.ipynb` — un notebook por clase (cada uno copia el anterior y le suma el contenido nuevo), como registro de estudio de cada clase. Cerca de la entrega se consolida en el notebook único que pide la consigna.
- `context.md` — estado vivo del proyecto (decisiones, resultados, pendientes).
- `submission_clase*.csv` — submissions generados por cada notebook.
- `clases/` — desgrabaciones de las clases teóricas que guían cada iteración.
- `overview.txt`, `rules.txt`, `data_context.txt`, `tp_description.txt` — consigna y reglas de la competencia.

## Cómo correrlo

1. Descargar los datos de la competencia desde Kaggle y descomprimirlos en `competition_data/` (no se versionan: `train_data.txt` pesa 211 MB y supera el límite de GitHub).
2. Instalar dependencias con Python 3.11: `pip install -r requirements.txt`.
3. Abrir el notebook de la clase más reciente (`tp_spotify_claseN_<tema>.ipynb`) y ejecutarlo de punta a punta (Run All). La celda final de cada sección de modelo escribe el submission listo para subir a Kaggle.
