# 🎬 Análisis exploratorio de películas — TMDB 5000

Análisis de datos sobre casi 5.000 películas, cruzando dos fuentes distintas del dataset
público **TMDB 5000 Movie Dataset**: información financiera/descriptiva y datos de reparto
y equipo de producción.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/MartinCarossino/analisis-peliculas-tmdb/blob/main/analisis_peliculas_tmdb.ipynb)

## Preguntas que guían el análisis

- ¿Qué tan relacionados están presupuesto, ingresos, popularidad y rating?
- ¿Qué géneros tienen mejor retorno de inversión (ROI) real, sin que lo distorsionen outliers?
- ¿Cambió la relación entre presupuesto e ingresos a lo largo de las décadas?

## Datos

| Archivo | Contenido | Filas |
|---|---|---|
| `tmdb_5000_movies.csv` | Presupuesto, ingresos, géneros, idioma, popularidad, fecha, rating | 4.803 |
| `tmdb_5000_credits.csv` | Reparto (cast) y equipo de producción (crew) | 4.803 |

Fuente original: [TMDB 5000 Movie Dataset (Kaggle)](https://www.kaggle.com/datasets/tmdb/tmdb-movie-metadata).
Ambos CSV están alojados en este repo para que el notebook sea 100% reproducible, sin
necesidad de descargar nada manualmente.

## Estructura del notebook

El análisis está dividido en 5 bloques, cada uno con su justificación en celdas de markdown:

1. **Ingesta, limpieza y merge** — Unión de las dos fuentes por clave real (`id` ↔ `movie_id`),
   parseo de columnas con formato JSON (`genres`, `cast`, `crew`), y tratamiento de
   `budget`/`revenue` en cero como datos faltantes (no como ceros reales).
2. **SQLite + SQLAlchemy** — Carga de las tablas a una base SQLite y verificación del merge
   reproduciéndolo con un `INNER JOIN` en SQL puro, comparando que ambos caminos (`pandas`
   y SQL) lleguen al mismo resultado. Incluye consultas analíticas directas en SQL.
3. **Encoding categórico** — `OneHotEncoder` para género (categoría sin orden natural) y
   `OrdinalEncoder` para rango de rating (categoría con jerarquía real: bajo < medio < alto).
4. **Estadística descriptiva y correlaciones** — Detección de asimetría (media vs mediana),
   matriz de correlación, y análisis de ROI por género usando la **mediana** en vez del
   promedio, para no dejar que outliers extremos distorsionen la conclusión.
5. **Visualización (Seaborn)** — `pairplot`, `lmplot` por década y boxplots de rating por
   género, cerrando con las conclusiones generales del análisis.

## Stack utilizado

`Python` · `Pandas` · `NumPy` · `SQLAlchemy` · `SQLite` · `scikit-learn` (encoders) ·
`Seaborn` · `Matplotlib`

## Principales hallazgos

- La media de presupuesto e ingresos es notablemente mayor que la mediana en ambos casos,
  confirmando que un grupo reducido de superproducciones distorsiona el promedio.
- El ROI **promedio** por género puede ser engañoso: algunos géneros muestran valores de
  miles de % por un puñado de películas de presupuesto casi nulo. La **mediana** da una
  lectura mucho más realista del retorno típico.
- La correlación más fuerte del dataset es entre cantidad de votos y popularidad (0.78);
  el rating en sí casi no correlaciona con el presupuesto (0.02) — gastar más no garantiza
  mejor recepción.
- El rating promedio desciende con el tiempo (de ~7.4 en películas de 1910 a ~5.9 en 2010),
  probablemente por sesgo de selección en el catálogo histórico disponible.

## Autor

Martín Carossino — [Portfolio](https://github.com/MartinCarossino)
