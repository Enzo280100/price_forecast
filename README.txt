================================================================
PRICE FORECAST - EAU DE PARFUM 50ML
Predicción de precios de fragancias usando análisis temporal 
y machine learning
================================================================

================================================================
TABLA DE CONTENIDOS
================================================================
1. Descripción
2. Estructura del Proyecto
3. Requisitos
4. Instalación
5. Uso
6. Flujo de Trabajo
7. Resultados Principales
8. Análisis Clave
9. Pasos Claves del Preprocesamiento
10. Librerías y Versiones
11. Troubleshooting
12. Notas Importantes
13. Próximos Pasos
14. Autor y Contacto

================================================================
1. DESCRIPCIÓN
================================================================

Este proyecto implementa un sistema de PREDICCIÓN DE PRECIOS para
Eau de Parfum de 50ml usando datos históricos de ventas 
(2022-2023).

OBJETIVO PRINCIPAL
──────────────────
- Anticipar la evolución de precios de fragancias existentes
- Identificar tendencias estacionales y factores impulsores
- Apoyar decisiones de inventario y planificación comercial

DIFERENCIAL
───────────
- Análisis bivariado por zona geográfica y marca
- Estabilidad temporal con validación out-of-time (OOT)
- Modelo LightGBM con forecast recursivo
- Interpretabilidad mediante feature importance y precisión

================================================================
2. ESTRUCTURA DEL PROYECTO
================================================================

price_forecast/
│
├── README.md                           # Este archivo (versión markdown)
├── README.txt                          # Documentación técnica detallada
│
├── notebooks/
│   └── 20250625_price_forecast.ipynb   # Notebook principal
│                                       # (análisis + modelado)
│
├── data/
│   ├── raw/
│   │   └── Dataset_recruiting.csv      # Dataset original (input)
│   │
│   └── processed/
│       ├── Dataset_Cleaned.csv         # Dataset limpio
│       ├── precio_por_region.csv       # Análisis bivariado región
│       ├── precio_por_top_brands.csv   # Análisis bivariado marca
│       └── Dataset_ModelReady.csv      # Dataset con features ML
│
├── outputs/
│   ├── analisis_bivariado_precio.png   # Gráficos bivariados
│   ├── estabilidad_temporal.png        # Series temporales
│   ├── precio_promedio_categorias.png  # Precio por categorías
│   ├── modelo_resultados.png           # Real vs predicho
│   ├── feature_importance.png          # Importancia de features
│   └── metricas_oot.csv                # Métricas validación OOT
│
└── .gitignore                          # Archivos a ignorar en git

================================================================
3. REQUISITOS
================================================================

PYTHON
──────
Versión: 3.9 o superior

DEPENDENCIAS PRINCIPALES
────────────────────────
- pandas: manipulación de datos
- numpy: cálculo numérico
- scikit-learn: ML utilities
- lightgbm: modelo predictivo
- matplotlib: visualización
- seaborn: visualización avanzada
- ptitprince: rain cloud plots
- re, os, warnings: librerías estándar

================================================================
4. INSTALACIÓN
================================================================

PASO 1: CLONAR EL REPOSITORIO
──────────────────────────────
$ git clone https://github.com/Enzo280100/price_forecast.git
$ cd price_forecast

PASO 2: CREAR ENTORNO VIRTUAL (RECOMENDADO)
─────────────────────────────────────────────
En Linux/Mac:
  $ python -m venv venv
  $ source venv/bin/activate

En Windows:
  $ python -m venv venv
  $ venv\Scripts\activate

PASO 3: INSTALAR DEPENDENCIAS
──────────────────────────────
Opción A - Usar requirements.txt:
  $ pip install -r requirements.txt

Opción B - Instalar manualmente:
  $ pip install pandas numpy scikit-learn matplotlib seaborn \
    ptitprince lightgbm jupyter

PASO 4: CREAR ESTRUCTURA DE CARPETAS
──────────────────────────────────────
$ mkdir -p data/raw data/processed outputs

PASO 5: COLOCAR DATASET
───────────────────────
Descarga Dataset_recruiting.csv y colócalo en:
  data/raw/Dataset_recruiting.csv

================================================================
5. USO
================================================================

EJECUTAR ANÁLISIS COMPLETO
───────────────────────────
$ jupyter notebook

Luego:
1. Navega a: notebooks/20250625_price_forecast.ipynb
2. Ejecuta: Kernel → Restart & Run All Cells

Tiempo estimado de ejecución: 2-3 minutos

EJECUTAR CELDAS ESPECÍFICAS
────────────────────────────
El notebook está organizado en secciones independientes:

1. Carga y EDA (Exploratory Data Analysis)
2. Preprocesamiento (limpieza, imputación, outliers)
3. Análisis Bivariado (precio por región, marca, etc.)
4. Feature Engineering (variables derivadas, lags, features)
5. Modelamiento (LightGBM con validación temporal)
6. Resultados y Conclusiones

Puedes ejecutar cada sección por separado según necesites.

================================================================
6. FLUJO DE TRABAJO
================================================================

FASE 1: EXPLORACIÓN Y LIMPIEZA
───────────────────────────────
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

FASE 2: ANÁLISIS DESCRIPTIVO
────────────────────────────
- Univariado: distribuciones, estadísticos
- Bivariado: precio vs región, precio vs marca, series temporales
- Multivariado: correlaciones Spearman, heatmaps

FASE 3: FEATURE ENGINEERING
─────────────────────────────
Feature                Descripción
──────────────────────────────────────────────────────────────
region                 País/región (USA, China, Hong Kong, etc.)
brand_category         Categoría (Luxury, Designer, Mid-Range, 
                       Mass Market)
week                   Semana del año (para capturar estacionalidad)
lag_1, lag_2, lag_3    Precio con 1, 2, 3 semanas de retraso
rolling_mean_4w        Media móvil de 4 semanas
sold_value_lag1        Unidades vendidas semana anterior
product_id_code        Identificador único del producto

FASE 4: MODELAMIENTO
─────────────────────
Estrategia:
- Split temporal: 20 semanas train / 5 semanas val / 4 semanas OOT
- Modelo: LightGBM (gradient boosting)
- Validación: Forecast recursivo (predicciones alimentan lags futuros)
- Métricas: MAE, RMSE, MAPE

================================================================
7. RESULTADOS PRINCIPALES
================================================================

DATASET
───────
Periodo:                2022-09-26 → 2023-04-10 (29 semanas)
Productos únicos:       71 (Eau de Parfum 40-60 ml)
Observaciones:          2,018
Variables objetivo:     price, units, sold_value
% Missing:              < 1%

RENDIMIENTO DEL MODELO (OOT)
────────────────────────────
Modelo                          MAPE    RMSE    MAE
──────────────────────────────────────────────────
Baseline Naive (lag-1)          7.35%   4.62    3.21
Rolling Mean (4 semanas)        7.17%   4.95    3.26
LightGBM (recursivo)            8.16%   5.40    3.87

Interpretación:
El modelo LightGBM ofrece mejor INTERPRETABILIDAD (feature 
importance, drivers), pero el Rolling Mean mejor performance en OOT.

Para horizonte corto (1-4 semanas), los precios son muy PERSISTENTES 
(precios rígidos en el corto plazo).

El modelo aporta valor en ESCENARIOS "QUÉ PASARÍA SI" y en detectar 
ANOMALÍAS.

DISTRIBUCIÓN DE CALIDAD POR PRODUCTO (OOT)
──────────────────────────────────────────
LightGBM:
  Alta (MAPE ≤5%)       : 24 productos (34%)
  Media (MAPE 5-20%)    : 47 productos (66%)
  Baja (MAPE 20-50%)    :  0 productos
  Muy baja (>50%)       :  0 productos

Rolling Mean:
  Alta (MAPE ≤5%)       : 25 productos (35%)
  Media (MAPE 5-20%)    : 42 productos (59%)
  Baja (MAPE 20-50%)    :  4 productos (5.6%)
  Muy baja (>50%)       :  0 productos

FEATURES MÁS IMPORTANTES
────────────────────────
1. Lag-1 y Lag-2 (precios semanas anteriores)
2. Rolling Mean 4w (tendencia reciente)
3. Lag-3 (patrón de 3 semanas)

================================================================
8. ANÁLISIS CLAVE
================================================================

POR REGIÓN (PRECIO MEDIANO)
─────────────────────────
Región              Precio      Observaciones
──────────────────────────────────────────────────────────────
Hong Kong           $51.99      Mayor precio, mercado premium
United States       $37.99      Mercado más grande, competitivo
China               $35.99      Precio bajo, volumen alto
Canada              $34.50      Menos datos, precio competitivo

POR CATEGORÍA DE MARCA
──────────────────────
Categoría           Precio Medio    Productos
────────────────────────────────────────────────────────────
Luxury              $65+            Tom Ford, Roja, Creed
Designer            $45-55          Gucci, Dior, Chanel
Mid-Range           $30-40          Armani, Paco Rabanne
Mass Market         $15-25          Unbranded, Victoria's Secret

ESTABILIDAD TEMPORAL
────────────────────
- Price: Relativamente estable (σ ≈ 2-3%)
- Units: Mayor volatilidad, picos estacionales en diciembre
- Sold Value: Estable alrededor de 94 unidades/semana promedio
- Available: Bajo (19 unidades promedio), refleja stock limitado

================================================================
9. PASOS CLAVES DEL PREPROCESAMIENTO
================================================================

PASO 1: EXTRACCIÓN DE UBICACIÓN Y MARCA
────────────────────────────────────────
# Clasificar región de itemLocation
region = clasificar_region(itemLocation)
# Resultado: 'Hong Kong', 'USA', 'China', etc.

# Categorizar marca
brand_category = clasificar_brand(brand)
# Resultado: 'Luxury', 'Designer', 'Mid-Range', 'Mass Market'

PASO 2: MANEJO DE VALORES FALTANTES
───────────────────────────────────
# Forward fill + backward fill por producto
df['price'] = df.groupby(
    ['brand', 'title', 'type', 'itemLocation']
)['price'].transform(lambda x: x.ffill().bfill())

PASO 3: ELIMINACIÓN DE OUTLIERS
────────────────────────────────
# Corte al percentil 99
q_price = df['price'].quantile(0.99)
df_clean = df[df['price'] <= q_price]
# Elimina ~1.98% del dataset

PASO 4: VALIDACIÓN OUT-OF-TIME
───────────────────────────────
# Split temporal estricto (sin shuffling)
train = df[df['fecha'] < '2023-03-13']
val = df[
    (df['fecha'] >= '2023-03-13') & 
    (df['fecha'] < '2023-04-03')
]
oot = df[df['fecha'] >= '2023-04-03']

================================================================
10. LIBRERÍAS Y VERSIONES
================================================================

VERSIONES RECOMENDADAS
───────────────────────
pandas==2.0+
numpy==1.24+
scikit-learn==1.3+
lightgbm==4.0+
matplotlib==3.7+
seaborn==0.12+
ptitprince==0.2.3+

VERIFICAR INSTALACIÓN
─────────────────────
$ pip freeze | grep -E "pandas|numpy|lightgbm|scikit-learn|matplotlib|seaborn"

================================================================
11. TROUBLESHOOTING
================================================================

PROBLEMA: ModuleNotFoundError
SOLUCIÓN:
  $ pip install -r requirements.txt

PROBLEMA: Ruta de datos no encontrada
SOLUCIÓN:
  Verificar que data/raw/Dataset_recruiting.csv existe

PROBLEMA: Notebook lento
SOLUCIÓN:
  Reducir muestras o ejecutar celdas por secciones

PROBLEMA: Gráficos no se muestran
SOLUCIÓN:
  Usar %matplotlib inline al inicio del notebook

================================================================
12. NOTAS IMPORTANTES
================================================================

✓ VALIDACIÓN 100% TEMPORAL
  Sin shuffling, sin data leakage

✓ FORECAST RECURSIVO
  Predicciones alimentan lags futuros

✓ TRATAMIENTO NATIVO DE CATEGÓRICAS
  LightGBM maneja product_id_code nativamente

✓ IMPUTACIÓN CONTEXTUAL
  ffill+bfill por producto, no global

⚠ HORIZONTE CORTO
  Modelo es mejor para explicabilidad que para mejora métrica puntual

================================================================
13. PRÓXIMOS PASOS (RECOMENDACIONES)
================================================================

1. INCLUIR EXTERNOS
   - Datos macroeconómicos
   - Tipo de cambio
   - Estacionalidad holidays

2. HORIZONTE MÁS LARGO
   - Evaluar modelo a 8-12 semanas

3. SEGMENTACIÓN
   - Modelos específicos por región
   - Modelos por categoría de marca

4. ENSEMBLE
   - Combinar con ARIMA
   - Combinar con Prophet

5. A/B TESTING
   - Validar recomendaciones en ambiente controlado

================================================================
14. AUTOR Y CONTACTO
================================================================

AUTOR
─────
Enzo Infantes Zúñiga

EMAIL
─────
enzo.infantes28@gmail.com

LINKEDIN
────────
https://www.linkedin.com/in/enzo-infantes/

GITHUB
──────
https://github.com/Enzo280100/price_forecast

SOPORTE
───────
Para preguntas o issues:
1. Revisar README.txt (documentación técnica detallada)
2. Abrir issue en GitHub
3. Contactar directamente al autor

================================================================
INFORMACIÓN DEL ARCHIVO
================================================================

Última actualización: Junio 2025
Versión: 1.0
Formato: Texto plano UTF-8
Línea de 64 caracteres ancho para legibilidad

================================================================
FIN DEL DOCUMENTO
================================================================
