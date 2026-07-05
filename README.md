# TP Minería de Datos — Predicción de Abandono Temprano en Spotify (UTDT, MiM+AI)

Competencia Kaggle: predecir la probabilidad de que una reproducción de Spotify sea un "abandono temprano" (menos de 30 segundos escuchados). Métrica: ROC-AUC.

## Contenido

- `notebooks/` — un notebook por clase (`tp_spotify_claseN_<tema>.ipynb`, cada uno hereda la base mínima del anterior y suma el contenido nuevo) más los notebooks post-clases (`tp_spotify_historial_fino.ipynb`, ...). Cerca de la entrega se consolida en el notebook único que pide la consigna.
- `submissions/` — submissions generadas por cada notebook (más `submission_example.csv`, el formato de ejemplo de la competencia).
- `logs/` — progreso de las corridas largas (una línea por evaluación, con timestamp; se puede seguir en vivo con un tail).
- `context.md` — estado vivo del proyecto (decisiones, resultados, pendientes).
- `clases/` — desgrabaciones de las clases teóricas que guían cada iteración.
- `overview.txt`, `rules.txt`, `data_context.txt`, `tp_description.txt` — consigna y reglas de la competencia.

## Cómo correrlo

1. Descargar los datos de la competencia desde Kaggle y descomprimirlos en `competition_data/` (no se versionan: `train_data.txt` pesa 211 MB y supera el límite de GitHub).
2. Instalar dependencias con Python 3.11: `pip install -r requirements.txt`.
3. Abrir el notebook más reciente de `notebooks/` y ejecutarlo de punta a punta (Run All). Las rutas son relativas a esa carpeta (datos en `../competition_data/`, logs en `../logs/`, salidas en `../submissions/`) — VSCode/Jupyter y nbconvert usan la carpeta del notebook como directorio de trabajo por default. La celda final escribe el submission listo para subir a Kaggle.
