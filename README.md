# TP Minería de Datos — Predicción de Abandono Temprano en Spotify (UTDT, MiM+AI)

Competencia Kaggle: predecir la probabilidad de que una reproducción de Spotify sea un "abandono temprano" (menos de 30 segundos escuchados). Métrica: ROC-AUC.

## Contenido

- `tp_abandono_spotify.ipynb` — notebook único del TP; se construye incrementalmente clase a clase y es el entregable final de código.
- `context.md` — estado vivo del proyecto (decisiones, resultados, pendientes).
- `submission_clase1_knn*.csv` — submissions generados (v1 baseline, v2 con más features).
- `clases/` — desgrabaciones de las clases teóricas que guían cada iteración.
- `overview.txt`, `rules.txt`, `data_context.txt`, `tp_description.txt` — consigna y reglas de la competencia.

## Cómo correrlo

1. Descargar los datos de la competencia desde Kaggle y descomprimirlos en `competition_data/` (no se versionan: `train_data.txt` pesa 211 MB y supera el límite de GitHub).
2. Instalar dependencias con Python 3.11: `pip install -r requirements.txt`.
3. Abrir `tp_abandono_spotify.ipynb` y ejecutarlo de punta a punta (Run All). La celda final escribe el submission listo para subir a Kaggle.
