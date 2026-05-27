# Price Forecast - Eau de Parfum 50ml

**Predicción de precios de fragancias usando análisis temporal y machine learning**

## 📋 Tabla de Contenidos

- [Descripción](#descripción)
- [Estructura del Proyecto](#estructura-del-proyecto)
- [Requisitos](#requisitos)
- [Instalación](#instalación)
- [Uso](#uso)
- [Flujo de Trabajo](#flujo-de-trabajo)
- [Resultados Principales](#resultados-principales)
- [Contacto](#contacto)

---

## 📌 Descripción

Este proyecto implementa un sistema de **predicción de precios** para Eau de Parfum de 50ml usando datos históricos de ventas (2022-2023). 

**Objetivo Principal:**
- Anticipar la evolución de precios de fragancias existentes
- Identificar tendencias estacionales y factores impulsores
- Apoyar decisiones de inventario y planificación comercial

**Diferencial:**
- Análisis bivariado por zona geográfica y marca
- Estabilidad temporal con validación out-of-time (OOT)
- Modelo LightGBM con forecast recursivo
- Interpretabilidad mediante feature importance y precisión.

---

## 🗂️ Estructura del Proyecto

```
price_forecast/
│
├── README.md                          # Este archivo
├── README.txt                         # Documentación técnica detallada
│
├── notebooks/
│   └── 20250625_price_forecast.ipynb  # Notebook principal (análisis + modelado)
│
├── data/
│   ├── raw/
│   │   └── Dataset_recruiting.csv     # Dataset original (input)
│   │
│   └── processed/
│       ├── Dataset_Cleaned.csv        # Dataset después de limpieza
│       ├── precio_por_region.csv      # Análisis bivariado por región
│       ├── precio_por_top_brands.csv  # Análisis bivariado por marca
│       └── Dataset_ModelReady.csv     # Dataset con features para modelado
│
├── outputs/
│   ├── analisis_bivariado_precio.png          # Gráficos de análisis bivariado
│   ├── estabilidad_temporal.png               # Series temporales de estabilidad
│   ├── precio_promedio_categorias.png         # Precio promedio por categorías
│   ├── modelo_resultados.png                  # Resultados del modelo (real vs pred)
│   ├── feature_importance.png                 # Importancia de features
│   └── metricas_oot.csv                       # Métricas de validación OOT
│
└── .gitignore                         # Archivos a ignorar en control de versiones
```

---

## ⚙️ Requisitos

- **Python:** 3.9+
- **Dependencias principales:**
  - `pandas` (manipulación de datos)
  - `numpy` (cálculo numérico)
  - `scikit-learn` (ML utilities)
  - `lightgbm` (modelo predictivo)
  - `matplotlib`, `seaborn` (visualización)
  - `ptitprince` (rain cloud plots)
  - `re`, `os`, `warnings` (librerías estándar)

---

## 📦 Instalación

### 1. Clonar el repositorio

```bash
git clone https://github.com/Enzo280100/price_forecast.git
cd price_forecast
```

### 2. Crear entorno virtual (recomendado)

```bash
python -m venv venv
source venv/bin/activate  # En Windows: venv\Scripts\activate
```

### 3. Instalar dependencias

```bash
pip install -r requirements.txt
```

O instalar manualmente:

```bash
pip install pandas numpy scikit-learn matplotlib seaborn ptitprince lightgbm
```

### 4. Estructura de carpetas

Asegúrate de que existan:

```bash
mkdir -p data/raw data/processed outputs
```

### 5. Colocar dataset

Descarga `Dataset_recruiting.csv` y colócalo en `data/raw/`

---

## 🚀 Uso

### Ejecutar el análisis completo

```bash
jupyter notebook notebooks/20250625_price_forecast.ipynb
```

Luego ejecuta **"Restart & Run All"** (Kernel → Restart & Run All Cells)

**Tiempo estimado:** 2-3 minutos

### O ejecutar celdas específicas

El notebook está organizado en secciones independientes:

1. **Carga y EDA** (exploratory data analysis)
2. **Preprocesamiento** (limpieza, imputación, outliers)
3. **Análisis Bivariado** (precio por región, marca, etc.)
4. **Feature Engineering** (variables derivadas, lags, features temporales)
5. **Modelamiento** (LightGBM con validación temporal)
6. **Resultados y Conclusiones**

---

## 📊 Flujo de Trabajo

### Fase 1: Exploración y Limpieza

```
Dataset bruto (4,400+ filas)
    ↓
Melt + Pivot (formato tidy)
    ↓
Normalizar formato numérico (comas → puntos)
    ↓
Llenar NaN (ffill + bfill por producto)
    ↓
Eliminar outliers (p99)
    ↓
Dataset limpio (2,018 observaciones)
```

### Fase 2: Análisis Descriptivo

- **Univariado:** distribuciones, estadísticos
- **Bivariado:** precio vs región, precio vs marca, series temporales
- **Multivariado:** correlaciones Spearman, heatmaps

### Fase 3: Feature Engineering

| Feature | Descripción |
|---------|------------|
| `region` | País/región (USA, China, Hong Kong, etc.) |
| `brand_category` | Categoría de marca (Luxury, Designer, Mid-Range, Mass Market) |
| `week` | Semana del año (para capturar estacionalidad) |
| `lag_1, lag_2, lag_3` | Precio con 1, 2, 3 semanas de retraso |
| `rolling_mean_4w` | Media móvil de 4 semanas |
| `sold_value_lag1` | Unidades vendidas semana anterior |
| `product_id_code` | Identificador único del producto |

### Fase 4: Modelamiento

**Estrategia:**
- **Split temporal:** 20 semanas train / 5 semanas val / 4 semanas OOT
- **Modelo:** LightGBM (gradient boosting)
- **Validación:** Forecast recursivo (las predicciones alimentan los lags futuros)
- **Métricas:** MAE, RMSE, MAPE

---

## 📈 Resultados Principales

### Dataset

| Métrica | Valor |
|---------|-------|
| Período | 2022-09-26 → 2023-04-10 (29 semanas) |
| Productos únicos | 71 (Eau de Parfum 40-60 ml) |
| Observaciones | 2,018 |
| Variables objetivo | price, units, sold_value |
| % Missing después limpieza | <1% |

### Rendimiento del Modelo (OOT)

| Modelo | MAPE | RMSE | MAE |
|--------|------|------|-----|
| Baseline Naive (lag-1) | 7.35% | 4.62 | 3.21 |
| Rolling Mean (4 semanas) | 7.17% | 4.95 | 3.26 |
| LightGBM (recursivo) | 8.16% | 5.40 | 3.87 |

**Interpretación:**
- El modelo LightGBM ofrece mejor **interpretabilidad** (feature importance, drivers), pero el Rolling Mean mejor performance en OOT.
- Para horizonte corto (1-4 semanas), los precios son muy **persistentes** (precios rígidos en el corto plazo).
- El modelo aporta valor en **escenarios "qué pasaría si"** y en detectar **anomalías**

### Distribución de Calidad por Producto (OOT)

```
**LightGBM**
Alta (MAPE ≤5%)      : 24 productos (34%)
Media (MAPE 5-20%)   : 47 productos (66%)
Baja (MAPE 20-50%)   :  0 productos 
Muy baja (>50%)      :  0 productos

**LightGBM**
Alta (MAPE ≤5%)      : 25 productos (35%)
Media (MAPE 5-20%)   : 42 productos (59%)
Baja (MAPE 20-50%)   :  4 productos (5.6%)
Muy baja (>50%)      :  0 productos
```

### Features Más Importantes

1. **Lag-1 y Lag-2** (precios de las semanas anterior)
2. **Rolling Mean 4w** (tendencia reciente)
3. **Lag-3** (patrón de 3 semanas)

---

## 🔍 Análisis Clave

### Por Región (Precio Mediano)

| Región | Precio | Observaciones |
|--------|--------|-----------------|
| Hong Kong | $51.99 | Mayor precio, mercado premium |
| United States | $37.99 | Mercado más grande, competitivo |
| China | $35.99 | Precio bajo, volumen alto |
| Canada | $34.50 | Menos datos, precio competitivo |

### Por Categoría de Marca

| Categoría | Precio Medio | Productos |
|-----------|--------------|-----------|
| Luxury | $65+ | Tom Ford, Roja, Creed |
| Designer | $45-55 | Gucci, Dior, Chanel |
| Mid-Range | $30-40 | Armani, Paco Rabanne |
| Mass Market | $15-25 | Unbranded, Victoria's Secret |

### Estabilidad Temporal

- **Price:** Relativamente estable (σ ≈ 2-3%)
- **Units:** Mayor volatilidad, picos estacionales en diciembre
- **Sold Value:** Estable alrededor de 94 unidades/semana promedio
- **Available:** Bajo (19 unidades promedio), refleja stock limitado

---

## 🛠️ Pasos Claves del Preprocesamiento

### 1. Extracción de Ubicación y Marca

```python
# Clasificar región de itemLocation
region = clasificar_region(itemLocation)
# Resultado: 'Hong Kong', 'USA', 'China', etc.

# Categorizar marca
brand_category = clasificar_brand(brand)
# Resultado: 'Luxury', 'Designer', 'Mid-Range', 'Mass Market'
```

### 2. Manejo de Valores Faltantes

```python
# Forward fill + backward fill por producto
df['price'] = df.groupby(['brand', 'title', 'type', 'itemLocation'])['price'].transform(
    lambda x: x.ffill().bfill()
)
```

### 3. Eliminación de Outliers

```python
# Corte al percentil 99
q_price = df['price'].quantile(0.99)
df_clean = df[df['price'] <= q_price]  # Elimina ~1.98% del dataset
```

### 4. Validación Out-of-Time

```python
# Split temporal estricto (sin shuffling)
train = df[df['fecha'] < '2023-03-13']
val = df[(df['fecha'] >= '2023-03-13') & (df['fecha'] < '2023-04-03')]
oot = df[df['fecha'] >= '2023-04-03']
```

---

## 📚 Librerías y Versiones

```
pandas==2.0+
numpy==1.24+
scikit-learn==1.3+
lightgbm==4.0+
matplotlib==3.7+
seaborn==0.12+
ptitprince==0.2.3+
```

Verificar con:
```bash
pip freeze | grep -E "pandas|numpy|lightgbm|scikit-learn|matplotlib|seaborn"
```

---

## 🚨 Troubleshooting

| Problema | Solución |
|----------|----------|
| `ModuleNotFoundError` | Ejecutar `pip install -r requirements.txt` |
| Ruta de datos no encontrada | Verificar que `data/raw/Dataset_recruiting.csv` existe |
| Notebook lento | Reducir muestras o ejecutar celdas por secciones |
| Gráficos no se muestran | Usar `%matplotlib inline` al inicio del notebook |

---

## 📝 Notas Importantes

- ✅ **Validación 100% temporal:** sin shuffling, sin data leakage
- ✅ **Forecast recursivo:** predicciones alimentan lags futuros
- ✅ **Tratamiento nativo de categóricas:** LightGBM maneja `product_id_code`
- ✅ **Imputación contextual:** ffill+bfill por producto, no por global
- ⚠️ **Horizonte corto:** modelo es mejor para explicabilidad que para mejora métrica puntual

---

## 🔮 Próximos Pasos (Recomendaciones)

1. **Incluir externos:** datos macroeconómicos, tipo de cambio, estacionalidad holidays
2. **Horizonte más largo:** evaluar modelo a 8-12 semanas
3. **Segmentación:** modelos específicos por región o por categoría de marca
4. **Ensemble:** combinar con modelos de series temporales (ARIMA, Prophet)
5. **A/B Testing:** validar recomendaciones en ambiente controlado

---

## 👤 Autor

**Enzo Infantes Zúñiga**

- Email: enzo.infantes28@gmail.com
- LinkedIn: [Enzo Infantes](https://www.linkedin.com/in/enzo-infantes/)
- GitHub: [price_forecast](https://github.com/Enzo280100/price_forecast)

---

## 📞 Soporte

Para preguntas o issues:
1. Revisar `README.txt` (documentación técnica detallada)
2. Abrir issue en GitHub
3. Contactar directamente al autor

---

**Última actualización:** Junio 2025  
**Versión:** 1.0
