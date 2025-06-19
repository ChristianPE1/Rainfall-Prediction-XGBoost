# 🌧️ # Predicción de Lluvias usando LSTM y XGBoost con el conjunto de datos Australianos

Este proyecto implementa y evalúa un modelo híbrido para la predicción de lluvias diarias en Australia, combinando redes neuronales LSTM con XGBoost. Se comparan además los resultados obtenidos con un modelo Transformer, para verificar si el enfoque secuencial clásico (LSTM) es más adecuado en este dominio.

📄 Basado en los papers:
**“An AI-Enabled ensemble method for rainfall forecasting using LSTM”**
**“Research on Rainfall Prediction Based on LSTM and Random Forest”**

---

## 💡 Motivación

Predecir la lluvia con precisión es clave en la planificación agrícola, prevención de desastres y manejo de recursos hídricos. Australia presenta condiciones climáticas altamente variables, lo que representa un desafío para los sistemas de predicción convencionales. Este trabajo busca abordar ese problema utilizando arquitecturas avanzadas de aprendizaje automático con enfoque secuencial.

---

## 🎯 Objetivos

| Tipo         | Descripción                                                                 |
|--------------|-----------------------------------------------------------------------------|
| **General**  | Diseñar un modelo híbrido LSTM + XGBoost para predecir lluvia diaria.       |
| **Específicos** | 1. Evaluar el impacto de combinar aprendizaje profundo (LSTM) con árboles (XGBoost).<br>2. Comparar contra arquitecturas Transformer.<br>3. Analizar el rendimiento de cada modelo usando métricas de clasificación. |

---

## 📁 Dataset

Se utilizó el conjunto de datos **Rain in Australia**, el cual contiene registros meteorológicos diarios de múltiples estaciones entre 2008 y 2017.

- Fuente: [Kaggle - Rain in Australia](https://www.kaggle.com/jsphyg/weather-dataset-rattle-package)
- Variable objetivo: `RainTomorrow` (binaria: *sí* / *no*)

---

## ⚙️ Pipeline de Implementación

1. **Carga y preprocesamiento**
   - Conversión de variables categóricas (direcciones de viento) a grados.
   - Limpieza y eliminación de columnas con demasiados valores nulos.
   - Imputación de valores faltantes eliminada para simplificación experimental.
2. **Balanceo**
   - Se aplica **SMOTE** sobre el conjunto de entrenamiento para balancear clases.
3. **Normalización**
   - Estándar (z-score) con `StandardScaler`.
4. **Modelado**
   - **LSTM**: Secuencia univariada con múltiples capas.
   - **XGBoost**: Dos modelos: uno como regresor (probabilidad), otro como clasificador.
   - **Fusión híbrida**: Predicciones promediadas y ponderadas para clasificación final.
5. **Comparación**
   - Se entrenó un modelo **Transformer** equivalente para establecer un nuevo benchmark.

---

## 📊 Resultados

### 🔁 Modelo Híbrido (LSTM + XGBoost)

```text
              precision    recall  f1-score   support

           0       0.85      0.98      0.91     17534
           1       0.98      0.83      0.90     17629

    accuracy                           0.91     35163
   macro avg       0.92      0.91      0.91     35163
weighted avg       0.92      0.91      0.91     35163
```

### 🧠 Transformer

```text
              precision    recall  f1-score   support

  No Lloverá       0.80      0.94      0.86     17522
  Sí Lloverá       0.93      0.76      0.83     17640

    accuracy                           0.85     35162
   macro avg       0.86      0.85      0.85     35162
weighted avg       0.86      0.85      0.85     35162
```

## 📌 Conclusiones

- El modelo **LSTM + XGBoost** obtiene la mejor combinación general de métricas:
  - `Accuracy` del **91%**
  - `F1-score` macro de **0.91**
  - `Recall` más alto para la clase *Sí lloverá*

- El **Transformer** demuestra un desempeño competitivo:
  - `Precision` para la clase *Sí lloverá* de **0.93**, incluso mayor que el modelo híbrido.
  - Sin embargo, su `recall` para esa clase cae a **0.76**, lo que indica más falsos negativos.

- Si se prioriza evitar **falsos positivos**, el Transformer puede ser una opción válida.

🔎 **Conclusión general**: El modelo **híbrido LSTM + XGBoost** ofrece un **mejor equilibrio** y **mayor confiabilidad** para tareas de predicción de lluvia diaria.
