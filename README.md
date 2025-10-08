# TP Individual Machine Learning - Marcos Gurruchaga (K5551)

## Descripción

Este proyecto implementa el procesamiento completo de datos bancarios para crear una **Analytic Base Table (ABT)** destinada al entrenamiento de modelos de Machine Learning. El objetivo es predecir qué clientes comprarán un paquete de servicios bancarios.

## Estructura del Proyecto

```
tp-individual-ml/
├── data.csv                    # Dataset original
├── tp_individual.ipynb         # Notebook principal con todo el análisis
├── analytic_base_table.csv     # ABT generada (se crea al ejecutar)
└── README.md                   # Este archivo
```

## Casos de Uso Implementados

### 1. Filtrado de Datos
- **Caso 1:** Selección de clientes con exactamente 9 meses de datos
- **Caso 2:** Filtrado de clientes sin paquete activo Y sin CoBranding en enero 2019

### 2. Ventanas Temporales
- **Caso 3:** Definición de ventanas de tiempo
  - **Prediction Window:** Marzo-Abril 2019 (2 meses)
  - **Lead Window:** Febrero 2019 (1 mes)
  - **Historical/Training Window:** Agosto 2018 - Enero 2019 (6 meses)

### 3. Análisis del Target
- **Caso 4:** Análisis de balance/desbalance del target
- **Caso 5:** Demostración de técnicas de balanceo (undersampling/oversampling)

### 4. Feature Engineering

#### Identificación de Tipos de Datos (Caso 6)
- Variables de fecha (3)
- Variables de texto/ID (1)
- Variables categóricas (24)
- Variables numéricas (49)

#### Identity Features (Caso 7)
Características estáticas del cliente del último mes disponible (enero 2019):
- Productos activos
- Seguros contratados
- Información demográfica

#### Transform Features (Caso 7)
Variables derivadas calculadas:
- **Operaciones:** Total de operaciones, operaciones digitales, porcentaje digital
- **Tarjeta de Crédito:** Ratio de utilización, porcentaje en cuotas, uso de revolving
- **Cuenta de Ahorros:** Ratio de uso, ratio de créditos, variación de balance, ticket promedio
- **Productos:** Total de productos activos, total de seguros, indicador multiproducto

#### Tratamiento de Valores Faltantes (Caso 8)
- Identificación de columnas con nulos
- Imputación lógica basada en reglas de negocio

#### Análisis de Outliers (Casos 9-10)
- **SavingAccount_Balance_Average (Caso 9):** Análisis completo con boxplot, skewness, tratamiento
- **CreditCard_Total_Spending (Caso 10):** Comparación de 3 métodos (percentiles 1%-99%, 5%-95%, 3-sigma)

#### Aggregate Features (Caso 11)
Agregaciones temporales sobre variables clave:
- **Ventanas temporales:** 6 meses completos, últimos 3 meses, primeros 3 meses
- **Funciones:** sum, max, min, mean, median, std
- **Especiales:** Variación primer-último mes, conteo de meses con actividad

### 5. Analytic Base Table (Caso 12)
Tabla final que integra:
- Identity Features
- Transform Features  
- Aggregate Features
- Target (TGT)

**Resultado esperado:** 23,191 clientes con ~150 features

---

## Resumen de Casos de Uso

| # | Caso de Uso | Descripción |
|---|-------------|-------------|
| 1 | Clientes con 9 meses | Filtrar clientes con historial completo |
| 2 | Sin paquete y sin CoBranding | Filtrar en enero 2019 |
| 3 | Ventanas de tiempo | Definir Training/Lead/Prediction windows |
| 4 | Balance del target | Analizar desbalance |
| 5 | Balanceo 50/50 | Demostración (no aplicado) |
| 6 | Tipos de datos | Clasificar variables |
| 7 | Identity & Transform | Crear features base y derivadas |
| 8 | Valores faltantes | Detectar y rellenar |
| 9 | Análisis outliers 1 | SavingAccount_Balance_Average |
| 10 | Análisis outliers 2 | CreditCard_Total_Spending |
| 11 | Aggregate Features | Agregaciones temporales |
| 12 | Analytic Base Table | Unir todas las features |

---

## Requisitos

```bash
pip install pandas numpy matplotlib seaborn scipy
```

## Cómo Ejecutar

1. **Abrir el notebook:**
   ```bash
   jupyter notebook tp_individual.ipynb
   ```

2. **Ejecutar todas las celdas:**
   - Menú: Cell → Run All
   - O presionar Shift+Enter en cada celda

3. **Verificar el resultado:**
   - La ejecución debe llegar a 23,191 clientes
   - Se generará `analytic_base_table.csv` con la tabla final

## Filtros Clave para 23,191 Registros

Los filtros aplicados son críticos para alcanzar el número exacto:

1. **9 meses de datos:** Garantiza historial completo para todos los clientes
2. **Sin paquete activo en enero 2019:** Filtra en el mes previo a la predicción porque necesitamos clientes que NO tengan el paquete y sean candidatos a comprarlo
3. **Sin CoBranding en enero 2019:** Filtra clientes que no tienen tarjeta de crédito CoBranding, enfocando el análisis en un segmento específico

**¿Por qué enero 2019?**
- Es el último mes de la ventana de entrenamiento
- Precede inmediatamente a la ventana de predicción (marzo-abril 2019)
- Permite verificar el estado actual del cliente antes de predecir su comportamiento futuro

**¿Por qué sin CoBranding?**
- Segmenta la población a clientes sin este tipo de tarjeta
- Permite un análisis más homogéneo del comportamiento
- Es parte de la estrategia de negocio definida por el profesor

## Features Generadas

### Categorías de Features

1. **Identity Features (~24):** Características del cliente en enero 2019
2. **Transform Features (~15):** Ratios y variables derivadas
3. **Aggregate Features (78):** 
   - 13 variables × 6 funciones = 78 features
   - Sobre 3 ventanas temporales diferentes
4. **Variation Features (~13):** Cambios entre primer y último mes
5. **Non-Zero Count Features (~13):** Meses con actividad

**Total aproximado:** ~140-150 features + 1 target

## Análisis de Outliers

### SavingAccount_Balance_Average
- Análisis de skewness para determinar tipo de distribución
- Boxplot para identificación visual
- Tratamiento mediante winsorización al 1%-99%

### CreditCard_Total_Spending  
Comparación de 3 métodos:
- **Método 1:** Percentil 1%-99% (menos restrictivo) ✓ ELEGIDO
- **Método 2:** Percentil 5%-95% (más restrictivo)
- **Método 3:** 3 desviaciones estándar (3-sigma)

**Conclusión:** Se eligió el Método 1 por preservar más información

## Próximos Pasos

Con la Analytic Base Table generada, los siguientes pasos serían:

1. **Feature Selection:** Identificar las features más relevantes
2. **Modelado:** Entrenar algoritmos (Random Forest, XGBoost, etc.)
3. **Validación:** Cross-validation y optimización de hiperparámetros
4. **Evaluación:** Métricas de performance (Precision, Recall, F1, AUC-ROC)
5. **Deployment:** Implementación del modelo en producción

## Notas Importantes

- **No se aplica balanceo:** Aunque se demuestra cómo hacerlo, el dataset final mantiene su distribución natural
- **Transform features originales:** Se crearon ratios y porcentajes diferentes a los del código del profesor
- **Múltiples ventanas temporales:** Se agregó información de diferentes períodos para capturar tendencias
- **Comentarios en español:** Todo el código está documentado en español como solicitado

## Autor

**Marcos Gurruchaga**  
Curso K5551 - Machine Learning  
TP Individual

---

*El notebook está completamente documentado con comentarios explicativos en cada paso del proceso.*

