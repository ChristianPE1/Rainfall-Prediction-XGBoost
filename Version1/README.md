# Predicción de Lluvias usando XGBoost con el conjunto de datos Australianos

Este proyecto reproduce y compara los resultados del paper  
**“An AI-Enabled ensemble method for rainfall forecasting using LSTM”**,  
usando únicamente **XGBoost** sobre el dataset público *Rain in Australia*.

---

## 💡 Definición del problema

*"Predecir la lluvia con precisión es un desafío en contextos de alta variabilidad climática como Australia. Este trabajo buscará abordar ese problema mediante el uso de algoritmos de aprendizaje automático aplicados a un conjunto de datos meteorológicos históricos de Australia."*


## 🎯 Objetivos
| Tipo | Descripción |
|------|-------------|
| **General** | Diseñar un modelo de predicción de lluvia basado en XGBoost. |
| **Específicos** | 1. Identificar variables relevantes.<br>2. Ajustar hiperparámetros y balanceo.<br>3. Evaluar con accuracy, precision, recall y F1.<br>4. Comparar con el estado del arte publicado. |

## ⌨️ Implementación
Predecir con precisión si lloverá al día siguiente en cualquiera de las 49 estaciones meteorológicas australianas registradas (2008-2017).


## ⚙️ Pipeline resumido
1. **Carga & limpieza**  
   - Conversión de fechas, estaciones, direcciones a grados.  
   - Imputación multivariante (`IterativeImputer`).  
2. **Preprocesado**  
   - One-Hot para categóricas, escalado z-score para numéricas.  
3. **Modelos XGBoost**  
   - `Modelo_A` optimiza precisión.<br>
   - `Modelo_B` optimiza recall.  
   - Ensamble lineal \((0.5\,A + 0.5\,B)\).  
4. **Selección de umbral** sobre el conjunto de validación.  
5. **Métricas en test** (con datos desbalanceados reales).  

## 📊 Resultados actuales

| Clase                 | Precision | Recall | F1-score | Support |
|----------------------|-----------|--------|----------|---------|
| **0 (No lloverá - Paper)**   | 0.89      | 0.96   | 0.93     | 18238   |
| **1 (Sí lloverá - Paper)**   | 0.96      | 0.89   | 0.92     | 18714   |  
|                      |           |        |          |         |
| **0 (No lloverá - Modelo propio)**   | 0.902     | 0.914  | 0.908    | 22064   | 
| **1 (Sí lloverá - Modelo propio)**   | 0.687     | 0.657  | 0.672    |  6375   |

| Métrica global       | Paper      | Nuestro modelo |
|----------------------|------------|-----------------|
| **Accuracy**         | 0.92       | 0.856           |
| **Macro Avg (F1)**   | 0.92       | 0.790           |
| **Weighted Avg (F1)**| 0.92       | 0.855           |

> *Nota:* El paper evalúa sobre un conjunto de prueba balanceado con SMOTE, lo que eleva artificialmente las métricas.  
> El test propio mantiene la distribución original para un escenario más realista.

## 🧪 Técnicas exploradas sin mejora significativa
Durante el proceso se implementaron varias estrategias con el objetivo de mejorar la predicción de la clase minoritaria ("sí lloverá"). Sin embargo, no presentaron resultados significantes comparados al tiempo que llevó su ejecución:

1. **GridSearchCV para optimización exhaustiva de hiperparámetros**
Se probaron múltiples combinaciones en dos clasificadores XGBoost con validación cruzada. La búsqueda tomó varias horas y solo se consiguió una leve mejora en F1-score (~0.5%).

2. **Modelo stacking (meta-modelo de regresión logística sobre dos clasificadores)**
Aunque se incrementó ligeramente el recall (~5%), se redujo la precision y el resultado global fue inferior en F1 y accuracy respecto al resultado incial base.

Esas pruebas fueron útiles para delimitar la complejidad necesaria y validar que el modelo actual logra un buen compromiso entre rendimiento y costo computacional.

## 📈 Próximos pasos
1. **Reevaluar el balanceo en el conjunto de prueba**  
   Aunque SMOTE se aplicó correctamente al conjunto de entrenamiento, el paper también balancea el test set, lo cual eleva artificialmente métricas como recall y F1.  
   → *Decidir si se replica este enfoque para comparación directa o mantener un test realista.*

2. **Optimizar el umbral de decisión para la clase "lluvia"**  
   Actualmente se usa un ensamble con ajuste de umbral óptimo según F1. Aún así, se puede:  
   - Explorar la **optimización multiobjetivo** (precisión *vs* recall).  
   - Incorporar métricas de costos asimétricos (por ejemplo, penalizar más los falsos negativos).

3. **Probar técnicas avanzadas de balanceo de clases**  
   - `SMOTE-Tomek`para una generación de muestras más robusta.  
   - `scale_pos_weight` dinámico durante el entrenamiento.  

4. **Reintroducir variables eliminadas (p.ej., `Cloud9am`, `Cloud3pm`)**  
   Estas columnas fueron descartadas por alto porcentaje de valores nulos. Sin embargo, la imputación múltiple (p. ej., `IterativeImputer`) puede permitir recuperar información útil para la predicción.

---
