# Modelo analítico de patrones de error humano en operaciones aeronáuticas (ASRS)

Repositorio de la tesis de Maestría en Ciencia de Datos y Analítica (UNAD)
**Cristhian Alexander Rodríguez Santanilla**, con la dirección de Andrés Felipe Solis Pino.

- Plataforma analítica (versión 2.0): https://asrs-analysis.vercel.app/ (archivo `index.html`)
- Datos: `data/ASRS_DBOnline.csv`, 1.003 reportes públicos y desidentificados del NASA ASRS (julio de 2012 a enero de 2025)

## Instalación y verificación

```bash
python -m venv .venv && source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
pytest -q          # 7 pruebas; incluyen las cifras base del documento (119/240 desenlaces, cortes 26,7/38,1/47,9)
cd notebooks && jupyter notebook
```

Las semillas están en `src/asrs_config.py` (semilla global 42; semillas de UMAP 42, 7, 123, 2024 y 31415).
Las versiones de `requirements.txt` son las usadas para generar las cifras de la tesis.

## Qué notebook reproduce cada tabla y figura

| Notebook | Contenido | Tablas | Figuras |
|---|---|---|---|
| 01_preparacion_y_muestra | Integración, depuración, muestra, hueco 2013–2015 | 2, 3, 4 | 1 |
| 02_eda_campos_multivaluados | Anomalías, detector, resultado, factores humanos, fases y uniones | 5, 6, 7, 8 | 2 |
| 03_tfidf_lsa_k | TF-IDF, bigramas, LSA 50 frente a 100, métricas por K | 9, 10, 12, 13 | 3 |
| 04_bertopic | Embeddings por fragmentos, % truncado, BERTopic con 5 semillas, coherencia, tópicos × factores humanos | 11 | — |
| 05_ablacion_y_perfiles | Ablación (incluye sin anomalías y sin factores humanos), perfiles finales, contingencia texto–fusión, riesgo por perfil | 14, 15, 16, 17, 18 | 4, 5 |
| 06_indice_y_asociaciones | Índice compuesto, AUC con bootstrap, negación, asociaciones, Fisher, Firth, potencia, valores de Result | 19, 20, 21, 22, 23, E1 | 6, 7, 8 |
| 07_panel_expertos | Límites de la V de Aiken, V de Aiken por ítem y criterio, W de Kendall con corrección por empates | 28 y resultados del panel | — |

Las Tablas 1 y A1–D1 y las Figuras 9 y 10 son descriptivas: tablas de diseño metodológico, plataforma y parámetros, y diagramas de arquitectura.

## Decisiones metodológicas

- Separador de valores múltiples: `;`. La `/` forma parte del nombre de la categoría.
- Unidad de conteo: el reporte.
- Corpus: narrativas de los reportantes 1 y 2, sin sinopsis ni callback y sin marcadores de desidentificación.
- Campos reservados como criterio externo, nunca usados como insumo: `Primary Problem` y `Result`.
- Desenlace severo: definición estricta y amplia en `src/asrs_config.py`. La clasificación de cada valor está en la Tabla E1.
- El índice compuesto se interpreta como **Índice de Complejidad del Reporte** (AUC = 0,57 frente al desenlace severo).

## Notebook 04: modelo de embeddings

Con internet, se usa `sentence-transformers/all-mpnet-base-v2` (opción `USE_ST = True`).
Sin internet, `src/embeddings/mpnet_np.py` implementa la inferencia del mismo modelo en NumPy. Para esa opción se necesita:
- `models/pytorch_model.bin`, el archivo de pesos del modelo publicado en Hugging Face;
- `models/bert-base-uncased-vocab.txt`, que ya está incluido.

## Panel de expertos

Transcriba las respuestas a `results/panel_expertos_respuestas.csv` con las columnas
`experto_id, item_id, tipo, criterio, puntaje`, y ejecute el notebook 07.
