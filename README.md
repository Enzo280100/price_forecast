================================================================
  PRUEBA PRÁCTICA NTT DATA — DATA SCIENTIST SENIOR
  Use Case: Forecasting de precios de Eau de Parfum 50 ml
================================================================

Candidato : Enzo Infantes Zúñiga
Correo    : enzo.infantes28@gmail.com
LinkedIn  : https://www.linkedin.com/in/enzo-infantes/
Fecha     : 25 de junio de 2026

----------------------------------------------------------------
1. OBJETIVO
----------------------------------------------------------------
Resolver el Use Case Senior planteado en la prueba: anticipar
cómo evolucionarán los precios de un Eau de Parfum de 50 ml
ya existente, identificando tendencias que ayuden a las áreas
de inventario y planificación comercial.

Adicionalmente, se proponen 5 Use Cases conceptuales sobre el
mismo dataset (sección 2 del notebook).

----------------------------------------------------------------
2. ESTRUCTURA DEL PROYECTO
----------------------------------------------------------------
.
├── 20260625_EnzoInfantes.ipynb     # Notebook principal (entregable)
├── README.txt                       # Este archivo
├── data/
│   ├── raw/
│   │   └── Dataset_recruiting.csv   # Dataset original (input)
│   └── processed/
│       └── Dataset_Cleaned.csv      # Generado por el notebook
└── output/                          # Para gráficos/exports opcionales

El notebook usa rutas relativas a `os.path.dirname(os.getcwd())`,
por lo que se asume que se ejecuta desde un subdirectorio
(p. ej. `notebooks/`).

----------------------------------------------------------------
3. ESTRUCTURA DEL NOTEBOOK
----------------------------------------------------------------
 1. Librerías
 2. Use Cases Conceptuales (Perfil Senior) — 5 UCs
 3. (renumerado a partir de 1)
 4. Carga de Datos
 5. EDA y Preprocesamiento
    5.1  Estructura del dataset (pivot a formato largo)
    5.2  Formato numérico de precio y unidades
    5.3  Variable available y availableText
    5.4  Distribución de variables numéricas
    5.5  Tratamiento de missing values
    5.6  Tratamiento de outliers (corte p99)
    5.7  Análisis univariado
    5.8  Análisis bivariado (raincloud, barras, series temporales)
    5.9  Análisis multivariado (correlación Spearman)
 6. Feature Engineering
    6.1  Variables derivadas del título (size_ml, flags)
    6.2  Región, calendario, product_id
    6.3  Filtro al universo del UC (EdP, 40–60 ml)
    6.4  Lags y rolling features
 7. Modelamiento
    7.1  Planteamiento del problema supervisado
    7.2  Split temporal + LightGBM + forecast recursivo
 8. Validación
    8.1  Métricas (MAE, RMSE, MAPE)
    8.2  Calidad por producto en OOT
    8.3  Feature importance
    8.4  Visualización real vs predicho
    8.5  Distribución de precios
 9. Conclusiones del Business Case
    9.1  Respuesta al Business Case
    9.2  Pasos seguidos y excluidos
    9.3  Próximos pasos

----------------------------------------------------------------
4. DEPENDENCIAS
----------------------------------------------------------------
Python 3.11+
pandas, numpy, scikit-learn, matplotlib, seaborn,
ptitprince, lightgbm, re, os, warnings

Instalación rápida:
    pip install pandas numpy scikit-learn matplotlib seaborn ptitprince lightgbm

----------------------------------------------------------------
5. CÓMO REPRODUCIR
----------------------------------------------------------------
1. Colocar Dataset_recruiting.csv en `data/raw/`.
2. Crear directorios `data/processed/` y `output/` si no existen.
3. Abrir el notebook desde un subdirectorio (por ejemplo `notebooks/`)
   o desde la raíz del proyecto si se ajusta `root_dir`.
4. Ejecutar todas las celdas en orden ("Run All").

Tiempo total de ejecución estimado: ~1-2 minutos.

----------------------------------------------------------------
6. RESULTADOS PRINCIPALES (RESUMEN EJECUTIVO)
----------------------------------------------------------------
- Universo modelado: 71 productos Eau de Parfum cercanos a 50 ml
  (2 018 observaciones, 29 semanas: 2022-09-26 → 2023-04-10).
- Split: 20 sem train / 5 sem val / 4 sem out-of-time (OOT).

Métricas en OOT (forecast recursivo a 4 semanas):
    Baseline Naive (último valor) : MAPE 7.35%   RMSE 4.62
    LightGBM (recursivo)          : MAPE 8.16%   RMSE 5.40

A 1 semana vista (val):
    Baseline Naive lag-1          : MAPE 8.57%
    LightGBM (1-step)             : MAPE 9.35%

Distribución de calidad por producto en OOT (LightGBM):
    Alta (≤5%)        : 21 productos (30%)
    Media (5-20%)     : 48 productos (68%)
    Baja (20-50%)     :  2 productos ( 3%)
    Muy baja (>50%)   :  0 productos

Hallazgo principal: los precios de EdP son altamente persistentes
en horizonte corto (alta autocorrelación lag-1). El baseline naive
es muy competitivo; el modelo ML aporta valor en interpretabilidad
(drivers, calendario) y en escenarios "qué pasaría si", pero no
mejora sustancialmente la métrica puntual en este horizonte.

----------------------------------------------------------------
7. NOTAS DE IMPLEMENTACIÓN
----------------------------------------------------------------
- Validación 100% temporal (sin shuffling, sin leakage).
- Forecast multi-step en OOT con propagación de predicciones
  (las predicciones del modelo alimentan los lags subsiguientes).
- Categóricas tratadas nativamente por LightGBM (product_id_code).
- Outliers tratados con corte al p99 (1.98% del dataset eliminado).
- Imputaciones contextuales (ffill+bfill por producto;
  mediana por brand×type).

================================================================