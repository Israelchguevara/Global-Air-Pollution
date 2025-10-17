# Global-Air-Pollution
Proyecto de análisis y visualización de calidad del aire a nivel global. A partir de un dataset con AQI (Air Quality Index) y subíndices por contaminante
el cuaderno explora patrones por país y ciudad, compara categorías de calidad y genera visualizaciones para comunicar hallazgos de forma clara.

🔎 Dataset

Archivo: global_air_pollution_data.csv
Filas/columnas (aprox.): ~23k × 12
Columnas principales:

country_name, city_name

aqi_value, aqi_category

co_aqi_value, co_aqi_category

ozone_aqi_value, ozone_aqi_category

no2_aqi_value, no2_aqi_category

pm2.5_aqi_value, pm2.5_aqi_category

Nota de limpieza: el campo co_aqi_value puede venir con un carácter de tabulación (co_aqi_value\t). En el notebook se normaliza el nombre para trabajar sin problemas.

🎯 Objetivos del proyecto

Limpiar y normalizar nombres de columnas/valores.

Analizar la distribución de aqi_value y de los subíndices por contaminante (CO, O₃, NO₂, PM2.5).

Comparar categorías de calidad (aqi_category: Good, Moderate, etc.) por país y ciudad.

Identificar ciudades y países extremos (top/bottom) por AQI.

Explorar correlaciones entre subíndices (ej. NO₂ vs. PM2.5).

Construir visualizaciones avanzadas y resúmenes ejecutivos (tablas y gráficos).

🗂️ Estructura del repositorio
├─ Visualización Avanzada - Global Air Pollution (1) (1).ipynb  # notebook principal
├─ data/
│  └─ global_air_pollution_data.csv                              # datos
├─ figures/                                                      # export de gráficos (opcional)
└─ README.md

🧭 Flujo de trabajo en el notebook

Carga & validación

Lectura del CSV, inspección de forma, tipos y nulos.

Limpieza & normalización

Estandarización de nombres (snake_case, reemplazo de pm2.5→pm25 para operaciones), corrección de co_aqi_value\t.

Análisis descriptivo

Medidas de tendencia/disp/percentiles, distribución de aqi_value y comparación por aqi_category.

Comparativas por país/ciudad

Rankings de AQI, medias por país, dispersión entre ciudades de un mismo país.

Relaciones entre contaminantes

Matriz de correlación y pair plots/bivariados (ej. PM2.5 vs NO₂).

Visualización avanzada

Barras horizontales (Top N/Bottom N), pequeños múltiplos por contaminante, heatmaps (país × categoría), boxplots por país/continente (si aplica).

Export

Guardado de gráficos clave a figures/ y tablas de resumen a CSV (opcional).

🧩 Fragmentos útiles de código

Normalización de columnas:

import pandas as pd, re

df = pd.read_csv("data/global_air_pollution_data.csv")

# limpiar nombres (snake_case, sin espacios/puntos)
def clean_col(c):
    c = c.strip().lower()
    c = c.replace(" ", "_").replace(".", "")
    c = c.replace("\t", "")  # elimina tabulaciones sueltas
    c = c.replace("pm2_5", "pm25")  # opcional si haces replace anterior
    return c

df.columns = [clean_col(c) for c in df.columns]


Resumen por país (media de AQI y conteo de ciudades):

country_summary = (df
    .groupby("country_name", as_index=False)
    .agg(mean_aqi=("aqi_value","mean"),
         cities=("city_name","nunique"),
         records=("aqi_value","size"))
    .sort_values("mean_aqi", ascending=False)
)


Top N ciudades con peor AQI (ej. top 15):

top_cities = (df[["country_name","city_name","aqi_value"]]
    .sort_values("aqi_value", ascending=False)
    .head(15)
)


Matriz de correlación entre subíndices (ej. CO, O₃, NO₂, PM2.5):

import seaborn as sns, matplotlib.pyplot as plt

cols = ["co_aqi_value","ozone_aqi_value","no2_aqi_value","pm25_aqi_value"]
corr = df[cols].corr(numeric_only=True)
sns.heatmap(corr, annot=True, fmt=".2f")
plt.title("Correlaciones entre subíndices de AQI")
plt.tight_layout()

🧰 Requisitos

Python 3.9+

pandas, numpy, matplotlib, seaborn
(opcional) plotly, scipy

Instalación rápida:

pip install pandas numpy matplotlib seaborn

▶️ Cómo ejecutar

Coloca el CSV en ./data/global_air_pollution_data.csv.

Abre el notebook Visualización Avanzada - Global Air Pollution (1) (1).ipynb.

Ejecuta todas las celdas.

(Opcional) Exporta gráficos/tablas a figures/ y data/.

📌 Resultados esperados

Tablas de resumen por país/ciudad y categoría AQI.

Gráficos: barras (Top/Bottom), heatmaps de categorías, pair plots y boxplots por grupo.

Insights: países/ciudades más y menos expuestos, relación entre contaminantes, cambios de categoría y distribución general de AQI.

🚀 Próximos pasos

Enriquecer con geocódigos para mapas (choropleth).

Añadir series temporales si se incorporan fechas.

Publicar un dashboard (Streamlit/Plotly Dash) con filtros interactivos.
