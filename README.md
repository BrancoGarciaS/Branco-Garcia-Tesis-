# Tesis: Caracterización de perfiles clínicos, demográficos y hospitalarios de pacientes oncológicos en Chile mediante aprendizaje automático supervisado explicable con datos GRD

Este repositorio contiene todo el flujo de trabajo desarrollado bajo la metodología **CRISP-DM**, desde la extracción y preprocesamiento de datos de Grupos Relacionados de Diagnóstico (GRD) de FONASA, hasta el entrenamiento de modelos de Machine Learning y su respectiva explicabilidad (XAI) mediante valores SHAP.

> **Acceso Directo a los resultados**
> 
> Nota: Si se desea explorar los gráficos, tablas de importancias, reportes de evaluación y los modelos finales (archivos `.pkl`) **sin necesidad de ejecutar el código localmente**, se puede acceder al respaldo completo de la carpeta de *Resultados* a través del siguiente enlace de Google Drive:
> **[Ver resultados pre-calculados en Google Drive](https://drive.google.com/drive/folders/19NGut5praNVhTc209m7N1_OncmtUldxc?usp=sharing)**

---
## Estructura del Repositorio

La arquitectura del proyecto está organizada en tres directorios principales:

```text
Branco-Garcia-Tesis-/
│
├── Datos/                      # Datos originales, procesados y finales
│   ├── Datasets originales/
│   ├── Datos clasificados/
│   ├── Datos procesados/
│   └── Datasets finales/
│
├── Notebooks/                  # Scripts de ejecución (Metodología CRISP-DM)
│   ├── Etapa 1 y 2/               # Comprensión y preparación de datos
│   ├── Etapa 3 y 4/               # Modelado y evaluación
│   └── Etapa 5/                   # Explicabilidad (SHAP)
│
└── Resultados/                 # Salidas csv, gráficos, métricas y modelos (.pkl)
    ├── Resultados (etapa 1 y 2)/
    ├── Resultados (etapa 3 y 4)/
    └── Resultados (etapa 5)/

```

---

## 1. Datos

Esta carpeta gestiona el ciclo de vida completo de los datos utilizados en el estudio:

* **`Datasets originales`**: Datos GRD crudos extraídos desde los datos abiertos de FONASA.
* **`Datos clasificados`**: Clasificación basada en diagnósticos (CIE-10), dividiendo la cohorte en registros oncológicos (incluyendo la categoría del tumor) y no oncológicos.
* **`Datos procesados`**: Resultados de la limpieza, reducción de cardinalidad, generación de variables derivadas y discretización de la variable numérica de consumo de recursos.
* **`Datasets finales`**: Datos divididos de forma aleatoria en 4 archivos (dos para entrenamiento y dos para prueba). Cada conjunto contiene un archivo exclusivo de diagnósticos oncológicos y otro de control (sin cáncer).

---

## 2. Notebooks

Contiene los scripts de ejecución secuencial numerados lógicamente, divididos según las fases de la metodología CRISP-DM:

### Etapas 1 y 2: Comprensión y preparación de Datos

Cada notebook tiene un numero prefijo que indica el orden de ejecución de los scripts (se comienza desde la adquisición de datos hasta el análisis de correlación de variables y aplicación de la codificación OHE):

* `00_adquisicion_datos.ipynb`
* `01_analisis_inicial.ipynb`
* `02_filtrado_inicial.ipynb`
* `03_analisis_identificadores.ipynb`
* `04_analisis_fechas.ipynb`
* `05_analisis_categoricas.ipynb`
* `06_analisis_numericas.ipynb`
* `07_procesamiento_targets.ipynb`
* `08_procesamiento_categoricas.ipynb`
* `09_procesamiento_derivadas.ipynb`
* `10_analisis_final.ipynb`
* `11_analisis_correlacion_ohe.ipynb`

### Etapas 3 y 4: Modelado y evaluación

Esta subcarpeta contiene los notebooks asociados a la fase de entrenamiento y evaluación de los modelos, incluyendo la ejecución de bootstrapping, la evaluación de los modelos óptimos exclusivamente en el conjunto de prueba oncológico, la prueba de fuga de pacientes (agrupando los registros por sus identificadores) y el entrenamiento de los modelos estrictos de severidad y consumo de recursos (excluyendo las variables que utiliza el agrupador IR-GRD para su cálculo, como comorbilidades, procedimientos y estadía).

* `01_regresion_logistica.ipynb`
* `02_random_forest.ipynb`
* `03_xgboost.ipynb`
* `04_bootstrapping.ipynb` (Validación de estabilidad)
* `05_evaluacion_cohorte_oncologica.ipynb` (Prueba exclusiva en pacientes con cáncer)
* `06_robustez_fuga_pacientes.ipynb` (Agrupación de registros por ID para evitar fuga)
* `07_modelo_estricto_severidad_consumo_recursos.ipynb` (Exclusión de variables circulares del algoritmo IR-GRD).

### Etapa 5: Explicabilidad (SHAP)

* `01_SHAP_Mortalidad.ipynb` (Modelo Random Forest).
* `02_SHAP_Severidad_Consumo_recursos.ipynb` (Modelos XGBoost).
* `03_SHAP_Estratificado_Cancer.ipynb` (Análisis específico por tipo de tumor).
* `04_SHAP_Estricto.ipynb` (Aplicado a modelos clínicos estrictos de severidad y consumo de recursos, sin variables circulares).
* `05_Estabilidad_Pandemia.ipynb` (Análisis de sensibilidad excluyendo los datos de 2020 y 2021).

---

## 3. Resultados

Salidas generadas automáticamente al ejecutar los notebooks respetando el orden numérico:

* **`Resultados (etapa 1 y 2)`**: Contiene el Análisis Exploratorio de Datos (EDA). Incluye subcarpetas de fechas, variables numéricas, diagnósticos, hospitales, etc. Destacan los gráficos de barras por categoría de cáncer y proporciones circulares (2019-2026), respaldados por sus respectivos `.csv`.
* **`Resultados (etapa 3 y 4)`**: Salidas de los modelos predictivos (`Random_Forest`, `Regresion_Logistica`, `XGBoost`). Incluye:
* Modelos exportados en formato `.pkl`.
* Métricas de desempeño (F1-Score, Grid Search).
* Importancia de predictores (Feature Importances / Odds Ratios).
* `Bootstrapping`: Archivos de estabilidad de variables y métricas Out-Of-Bag (OOB).


* **`Resultados (etapa 5)`**: Todo el análisis de explicabilidad e interpretabilidad. Contiene subcarpetas por cada variable objetivo y enfoque (`GLOBAL` vs `ONCOLÓGICO`).
* **Estructura interna**: Valores SHAP crudos y porcentuales, rangos direccionales (numéricos y categóricos), Summary Plots y Dependence Plots (Top 20).
* **Estratificación**: Subcarpetas para cada categoría de cáncer (ej. `C15_C26`), con el Top 10 de variables más influyentes.
* **Sensibilidad Pandemia**: Comparativas y cálculo de Deltas entre los modelos originales y los modelos entrenados excluyendo la distorsión del COVID-19.



---

## Reproducibilidad

Con el objetivo de garantizar la rigurosidad y la completa reproducibilidad científica del estudio, se ha fijado globalmente la semilla de aleatoriedad **`random_state = 42`**.
Este parámetro rige de manera consistente en:

* La partición aleatoria de los conjuntos de entrenamiento y evaluación.
* Los muestreos de la población de control (cohortes sin cáncer).
* El entrenamiento de todos los algoritmos (Regresión Logística, Random Forest, XGBoost).
* Las iteraciones de Bootstrapping y la aproximación de valores SHAP.

---

## Tecnologías y Dependencias

Este proyecto fue desarrollado en **Python**. El modelado predictivo y el análisis de explicabilidad se construyeron utilizando las siguientes herramientas principales:

*   **Manipulación de datos:** `pandas`, `numpy`, `scipy`
*   **Machine Learning:** `scikit-learn`, `xgboost`
*   **Explicabilidad (XAI):** `shap`
*   **Visualización:** `matplotlib`, `seaborn`

### Instalación

Para reproducir este entorno y evitar conflictos de versiones, se incluye un archivo `requirements.txt`. Puedes instalar todas las dependencias ejecutando el siguiente comando en tu terminal:

```bash
pip install -r requirements.txt