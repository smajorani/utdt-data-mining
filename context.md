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
- **Cambio de criterio (2026-07-03):** el TP se desarrolla como **un notebook por clase** (`tp_spotify_claseN_<tema>.ipynb`), no un único archivo que se pisa — así queda un registro de cada clase para estudiar después. Ajustado el mismo día tras la primera prueba: cada notebook **no** es "el anterior completo + lo nuevo" (eso haría crecer el archivo sin límite — EDA, gráficos y comparaciones de todas las clases previas acumulándose; a la Clase 6 sería enorme). Cada notebook nuevo lleva solo la **base mínima indispensable** heredada (carga + target + features/decisiones ya cerradas, sin repetir EDA ni comparaciones intermedias — esas quedan en el notebook de la clase que las hizo) y el cuerpo principal es el desarrollo de esa clase. Ojo: la consigna pide como entregable de código un **script/notebook único** que corra de punta a punta — falta decidir, cerca de la entrega, cómo se consolida (¿se manda el último/más completo? ¿se arma uno final aparte?). Pendiente, no bloqueante ahora.
- Formato de trabajo: notebook `.ipynb` en VSCode (extensión Jupyter/Python), no Colab.
- El profesor autoriza explícitamente el uso de LLMs/Copilot/Codex para el TP ("la vara ahora es alta" porque ya no hay excusa para que el código no funcione). Sin restricciones de librerías: scikit-learn es la librería de referencia del curso.

## Estado actual (2026-07-03)

**Clase 1 cerrada y aprobada por el usuario**, notebook renombrado a `tp_spotify_clase1_knn.ipynb` (corre de punta a punta con el kernel `python311`). Qué contiene y qué dio, en una línea cada uno:

- Carga eficiente (`usecols`/`dtype`), EDA con 2 figuras, features derivadas: `content_type`, `hora`/`dia_semana` (UTC; AR=UTC−3), `artista_top` (top-50 de train, anti-leakage).
- Comparación de 3 conjuntos de features → ganó C (todo incluido), AUC holdout aleatorio 0.6903 con K=101.
- Chequeo temporal (75% viejo / 25% nuevo): AUC cae a 0.6477 → el split aleatorio infla ~4 puntos y el K óptimo temporal es más grande.
- Modelo final: muestra 100k, K=701 revalidado temporal (AUC 0.6396) → `submission_clase1_knn_v2.csv` (v1 se conserva). Expectativa realista en Kaggle: ~0.63-0.64.

Datos verificados: train 2013-10 → 2024-08-31; test 2024-09-01 → 2024-12-31; en train no hay audiobooks (907.211 tracks, 4.133 episodes).

**Clase 2 cerrada.** `tp_spotify_clase2_arboles.ipynb` (24 celdas, ~176 KB — base mínima de Clase 1 + cuerpo de Clase 2 completo), corre de punta a punta con el kernel `python311`. Qué contiene y qué dio:

- Esquema de validación formal **train/validation/test** (70/15/15, cronológico, sobre el dataset completo — no una muestra) reemplazando el holdout único de la Clase 1; justificado con lo que dice la clase (con cientos de miles de filas, holdout simple > K-Fold; hay que evitar "overfitting the validation set" al probar muchas combinaciones/algoritmos).
- Árbol de decisión (`DecisionTreeClassifier`): pipeline sin escalado (no lo necesita), barrido `max_depth`×`min_samples_leaf` contra `validation` → ganador `max_depth=14, min_samples_leaf=100` (AUC validation 0.6690).
- Comparación final honesta en `test_interno` (tocado una única vez): **árbol 0.6420 vs. KNN 0.6307** (K=1501 sobre muestra de 100k reconstruida sin fuga desde `train`) → gana el árbol por ~1.1 puntos de AUC. Confirmado con el árbol sobre el `test_interno` completo (136.703 filas): 0.6394.
- `submission_clase2_arbol.csv` generado (51.570 filas, modelo reentrenado con el 100% de train). Falta subir a Kaggle.

Detalle honesto para recordar: la curva de validación del árbol salió más ruidosa que la ideal de libro (esperable con holdout único en vez de K-Fold); `test_interno` (termina el 2024-08-31, justo donde termina el train real de la competencia) tiene tasa de abandono más baja (0.230) que train/validation (~0.287) — cambio real de comportamiento en el tiempo, visible solo porque el split es cronológico y no aleatorio.

**Post-cierre Clase 2 (2026-07-03, sesión de estudio):** `user_id` (10 usuarios reales, los mismos en train y test — verificado en los archivos) probado como feature y **rechazado** por el esquema cronológico: mejor AUC validation 0.6478 vs. 0.6690 sin ella. Causa verificada: las tasas por usuario derivan entre épocas (caso extremo: 8%→44% de abandono y 4× de actividad). Documentado en la sección final del notebook; material de informe (un split aleatorio lo habría aprobado falsamente). En curso: **ronda de mejoras dentro del temario**, protocolo una-decisión-un-número contra `validation` con adopción secuencial — A: `hora` numérica en vez de 24 dummies; B: poda por α (`min_impurity_decrease`, "lo más común en la práctica" según la clase); C: top de artistas 50→200/500 (calculado solo con `df_train`, higiene anti-fuga interna). `test_interno` sigue intacto; submission v1 vigente; si la ronda mejora la receta, se genera `submission_clase2_arbol_v2.csv` conservando la v1.

## Clase 1 — ya vista (resumen para no releer)

Fundamentos: qué es data mining, tipos de aprendizaje (supervisado/no supervisado/refuerzo), regresión vs. clasificación, estructura train/test. Primer algoritmo: **KNN** (vecinos más cercanos) — simple, no paramétrico, sirve para clasif. y regresión (cambia el paso 2: proporción de clases vs. promedio). Ideas clave: K es hiperparámetro que regula flexibilidad (K=1 sobreajusta, K=N tiende al prior/media); distancia euclídea sensible a escala → estandarizar (z-score), pero "escalar siempre ayuda" es falso, depende de si la variable de alta varianza es genuinamente predictiva; maldición de la dimensionalidad (más variables → todo se aleja); one-hot encoding de categóricas de alta cardinalidad no agrava tanto la dimensionalidad como parece (cada fila solo aporta como máximo distancia 2, sin importar cuántas columnas nuevas se creen); KNN es carísimo computacionalmente (hay que calcular distancia contra todo el training set) y caro de almacenar/transmitir (el "modelo" es el dataset entero) — por eso no se usa mucho en producción, pero es la mejor forma de introducir overfitting/underfitting, flexibilidad/rigidez y la necesidad de medir performance en datos no vistos (no alcanza con que algo "parezca metodológicamente sano").

## Clase 2 — ya vista (resumen para no releer)

Dos temas. **(1) Estrategias de validación (model selection):** el objetivo es estimar performance en datos no vistos para poder comparar decisiones de modelado. *Holdout* (validation set): simple, pero ruidoso (varía según el split al azar) y "regala" datos de entrenamiento — mitigable repitiendo/promediando, o reentrenando la receta final con el 100% de los datos una vez decidida (no siempre vale la pena: si la curva de aprendizaje ya convergió, o si reentrenar es carísimo). *Leave-one-out CV*: N modelos dejando 1 obs. afuera cada vez, determinístico y cada modelo usa casi todos los datos, pero carísimo computacionalmente y con alta varianza estadística — el profesor lo presenta "por completitud", no es su preferido. *K-Fold CV*: punto intermedio (K carpetas, cada obs. valida exactamente 1 vez); K=N es LOOCV, K=2 se acerca al holdout; valores típicos 3/5/10, sin regla de dedo formal. Regla práctica explícita del profesor: **con datasets grandes (cientos de miles de filas, nuestro caso) alcanza con holdout — K-Fold es exceso de cómputo sin beneficio real; K-Fold rinde con datasets medianos (miles-decenas de miles).** Al probar muchas combinaciones (hiperparámetros, conjuntos de features, varios algoritmos) el validation set se "sobreajusta" de tanto mirarlo (sesgo optimista, *"overfitting the validation set"* — paper de Andrew Ng) → por eso conviene partir en **train/validation/test**, con el test tocado una única vez al final. Todo esto asume observaciones i.i.d.; con series de tiempo (nuestro caso — el profesor confirmó que el TP "tiene componente temporal", los gustos cambian) hay que respetar la cronología: entrenar con el pasado, validar con el futuro, nunca mezclar épocas entre conjuntos.

**(2) Árboles de decisión:** parten el espacio de atributos con cortes binarios recursivos (`variable < umbral`, probando todas las combinaciones variable×corte y quedándose con la de mayor "ganancia" = mayor reducción de costo), buscando en cada paso minimizar una función de costo (suma de errores al cuadrado en regresión; Gini o entropía —impureza— en clasificación). En clasificación cada hoja predice la **proporción de clases** de las observaciones de train que caen ahí (probabilidad directa, ideal para ROC-AUC). Hiperparámetros que controlan flexibilidad (se eligen con validación, "nunca hay regla de dedo, la regla es probar"): `max_depth`, `min_samples_split`, `min_samples_leaf`, y alternativamente poda por costo-complejidad (parámetro α, penaliza cada partición nueva — más común en la práctica que podar un árbol ya crecido). Permiten calcular **importancia de atributos** (suma de la "ganancia" que generó cada variable en todos sus splits, normalizada 0-100) — útil también como selector de variables para otros algoritmos. Manejan categóricas de forma elegante en teoría (una rama por categoría) pero scikit-learn no lo tiene implementado — se sigue usando one-hot, "no es tan dramático" (ni XGBoost lo tuvo durante años). Son **eager** (todo el costo en `.fit()`, predecir es barato — recorrer ifs), al revés que KNN (**lazy**, todo el costo en `.predict()`) — por eso no hace falta subsamplear como con KNN, y por eso los modelos eager se reparten/escalan mejor en producción. Contras explícitos de la clase: un árbol solo "no predice tan bien" — son los ladrillos de modelos más competitivos (el profesor adelantó que **XGBoost es el modelo que se usa en el TP**); estructura inestable ante pequeños cambios en los datos.

## Clase 3
Todavía no la vimos (pisar esto cuando se llene el comentario de esa clase en este file). Recordame cuando te pida que leas el contexto, que te pase el copy and paste de la charla que tengo con ChatGPT sobre esta clase, que tiene un montón de insights interesantes.

## Entorno

Python 3.11.5. Instalado: `pandas`, `numpy`, y a partir de la Clase 1 también `scikit-learn`, `jupyter`/`ipykernel`, `matplotlib`. Falta instalar a demanda: `seaborn`, y más adelante `lightgbm`/`xgboost`/`catboost` según lo que pida cada clase.

## Repo

GitHub: https://github.com/smajorani/utdt-data-mining. `competition_data/` está excluida por `.gitignore` (GitHub rechaza archivos >100 MB; train pesa 211 MB) — los datos se bajan de Kaggle.

## Pendiente

- Nombre del equipo en Kaggle (para el mail de entrega del 19/7).
- Subir a Kaggle y anotar los scores públicos en las "Notas honestas" de cada notebook: v1/v2 de Clase 1 (KNN) y `submission_clase2_arbol.csv` de Clase 2 (árbol).
- Próximo: `clases/MD - Clase 3.txt`.
- Antes de la entrega final (19/7): decidir cómo se consolida en el notebook único que pide la consigna (ver nota en "Cómo trabajamos").
