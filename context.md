# Contexto del proyecto — TP Minería de Datos (UTDT, MiM+AI)

Documento de referencia para mí (Claude), no es el informe de la materia. Se mantiene minimalista: cuando algo cambia, se reemplaza in-place, no se agrega como capa nueva.

## Qué es esto

Competencia Kaggle "Predicción de Abandono Temprano en Spotify - 2026", TP de Minería de Datos (MiM+AI, Di Tella), en grupos de 2. Objetivo: predecir probabilidad de que una reproducción sea "abandono temprano". Métrica: ROC-AUC. 30% de test = leaderboard público (guía), 70% = leaderboard privado (nota final). Máx. 3 submits/día.

Dato de color de la Clase 1: los datos son reales (no simulados), de ~10 usuarios (el profesor, amigos, ayudantes).

## Fechas

Todos los hitos intermedios (14/6, 21/6, 28/6) ya se cumplieron en tiempo y forma. Estamos a principios de julio, con casi un mes libre de presión para desarrollar en serio — ese es el propósito de este repo. Quedan dos fechas duras:

- Submits en Kaggle: hasta **jueves 16/7**.
- Informe (PDF) + código por mail a datamining.mim@gmail.com (todo el grupo en copia, un solo integrante envía): hasta **domingo 19/7**.

## Nota final

30% leaderboard privado + actividad en Kaggle · 25% código (script/notebook único, corre de punta a punta, legible) · 45% informe (máx. 6 carillas: EDA con ≥2 figuras, feature engineering probado y justificado, esquema de validación justificado y comparado contra Kaggle, algoritmos e hiperparámetros probados, reparto de tiempo dedicado).

## Datos (`competition_data/`)

- `train_data.txt`: 911.344 filas, TSV, incluye `ms_played`. `obs_id` 1–911.344.
- `test_data.txt`: 51.570 filas, mismo esquema sin `ms_played`. `obs_id` continúa desde 911.345.
- `submission_example.csv`: CSV, columnas `obs_id,target`.
- `spotify_api_data.zip` (155 MB, 53.789 JSON): metadata cruda de Spotify por track/episode (duración, popularidad, artistas, etc.). Uso opcional.
- Split train/test puramente temporal: train = antes de 2024-09-01, test = desde esa fecha.
- Columnas principales: `obs_id`, `user_id` (anon.), `ts` (redondeado a 30 min), `ms_played` (solo train), `platform_family`, `conn_country`, metadata de track/episode/audiobook, `shuffle`, `offline`, `offline_timestamp`, `incognito_mode`.

**Target (resuelto — fuente: Clase 1, no está escrito en ningún archivo del dataset):** `target = 1` si `ms_played < 30000` (escuchó menos de 30 segundos), si no `target = 0`. `ts` está redondeado a bloques de 30 min y faltan campos deliberadamente para que no se pueda inferir la duración real comparando reproducciones consecutivas (anti-trampa).

**A tener en cuenta:** volumen grande (211 MB train, 53.789 JSON sueltos) → pensar memoria/chunking cuando toque, no cargar todo ingenuamente. El profesor lo marca explícitamente como parte del desafío del TP.

## Cómo trabajamos

**REGLA DURA, releer antes de tocar código:** solo hago lo que el usuario pide explícitamente, un paso a la vez, a SU ritmo. Nada de adelantarme a construir/ejecutar/instalar/debuggear varias cosas encadenadas por mi cuenta aunque "tenga sentido" como siguiente paso lógico. Si algo requiere varios pasos (instalar algo, correr algo, armar una celda), lo digo y lo hago de a uno, chequeando con el usuario, no en una corrida larga autónoma. Ya se me fue una vez de mambo (2026-07-02: armé y ejecuté el notebook entero de la Clase 1 de punta a punta, incluyendo debug de instalación de Python/Jupyter, sin ir parando a explicar y consensuar cada paso) — no volver a hacerlo.

- Carpeta `clases/` cargada con 5 clases (`MD - Clase 1.txt` a `Clase 5.txt`). Se recorren **una por una, en orden**, sin adelantarme a leer las siguientes hasta que corresponda.
- El usuario está atrasado con la materia (no el TP) → ritmo lento, deliberado.
- Por cada clase: leo el `.txt` completo, identifico los conceptos que da el profesor, se los explico, y vamos construyendo el TP incrementalmente en código, **con el usuario, no para el usuario**.
- Pedido explícito y permanente: no alcanza con escribir código — siempre explicar en el chat el razonamiento detrás de cada decisión (qué dice la clase, por qué elegimos tal técnica/feature, qué alternativas había) y esperar antes de seguir.
- El TP se desarrolla como **un único notebook que se va pisando/mejorando clase a clase** (no un archivo nuevo por clase) — así queda como el entregable final de código.
- Formato de trabajo: notebook `.ipynb` en VSCode (extensión Jupyter/Python), no Colab.
- El profesor autoriza explícitamente el uso de LLMs/Copilot/Codex para el TP ("la vara ahora es alta" porque ya no hay excusa para que el código no funcione). Sin restricciones de librerías: scikit-learn es la librería de referencia del curso.

## Estado actual (2026-07-02, noche)

**Clase 1 cerrada y aprobada por el usuario.** `tp_abandono_spotify.ipynb` (2ª iteración) corre de punta a punta con el kernel `python311` y queda como base para la Clase 2. Qué contiene y qué dio, en una línea cada uno:

- Carga eficiente (`usecols`/`dtype`), EDA con 2 figuras, features derivadas: `content_type`, `hora`/`dia_semana` (UTC; AR=UTC−3), `artista_top` (top-50 de train, anti-leakage).
- Comparación de 3 conjuntos de features → ganó C (todo incluido), AUC holdout aleatorio 0.6903 con K=101.
- Chequeo temporal (75% viejo / 25% nuevo): AUC cae a 0.6477 → el split aleatorio infla ~4 puntos y el K óptimo temporal es más grande.
- Modelo final: muestra 100k, K=701 revalidado temporal (AUC 0.6396) → `submission_clase1_knn_v2.csv` (v1 se conserva). Expectativa realista en Kaggle: ~0.63-0.64.

Datos verificados: train 2013-10 → 2024-08-31; test 2024-09-01 → 2024-12-31; en train no hay audiobooks (907.211 tracks, 4.133 episodes).

## Clase 1 — ya vista (resumen para no releer)

Fundamentos: qué es data mining, tipos de aprendizaje (supervisado/no supervisado/refuerzo), regresión vs. clasificación, estructura train/test. Primer algoritmo: **KNN** (vecinos más cercanos) — simple, no paramétrico, sirve para clasif. y regresión (cambia el paso 2: proporción de clases vs. promedio). Ideas clave: K es hiperparámetro que regula flexibilidad (K=1 sobreajusta, K=N tiende al prior/media); distancia euclídea sensible a escala → estandarizar (z-score), pero "escalar siempre ayuda" es falso, depende de si la variable de alta varianza es genuinamente predictiva; maldición de la dimensionalidad (más variables → todo se aleja); one-hot encoding de categóricas de alta cardinalidad no agrava tanto la dimensionalidad como parece (cada fila solo aporta como máximo distancia 2, sin importar cuántas columnas nuevas se creen); KNN es carísimo computacionalmente (hay que calcular distancia contra todo el training set) y caro de almacenar/transmitir (el "modelo" es el dataset entero) — por eso no se usa mucho en producción, pero es la mejor forma de introducir overfitting/underfitting, flexibilidad/rigidez y la necesidad de medir performance en datos no vistos (no alcanza con que algo "parezca metodológicamente sano").

## Entorno

Python 3.11.5. Instalado: `pandas`, `numpy`, y a partir de la Clase 1 también `scikit-learn`, `jupyter`/`ipykernel`, `matplotlib`. Falta instalar a demanda: `seaborn`, y más adelante `lightgbm`/`xgboost`/`catboost` según lo que pida cada clase.

## Repo

GitHub: https://github.com/smajorani/utdt-data-mining. `competition_data/` está excluida por `.gitignore` (GitHub rechaza archivos >100 MB; train pesa 211 MB) — los datos se bajan de Kaggle.

## Pendiente

- Nombre del equipo en Kaggle (para el mail de entrega del 19/7).
- Subir v1 y v2 a Kaggle y anotar los scores en las "Notas honestas" del notebook.
- Mañana: empezar `clases/MD - Clase 2.txt`.
