
# Evaluacion landmarks

## Modelo de Red Neuronal para Identificación de Emociones

---

## Tipo de Red Neuronal

**Red Neuronal Feedforward (MLP)**  
Yo utilizaria un **Multi-Layer Perceptron (MLP)** ya que nos permitirua trabajar mejor con datos mas estructurados y numéricos, como las coordenadas de los landmarks.

---

## Estructura de la Red

### a) Capa de Entrada
- **Definición:** Recibe como input los valores extraídos de los landmarks.
- **Número máximo de entradas:**  
  - Si se usan 468 landmarks con las tres coordenadas (x, y, z), se tienen **468 × 3 = 1404 entradas**.
  - Si se utilizan solo dos coordenadas (x, y), el máximo sería **468 × 2 = 936 entradas**.
  
*La elección depende de la relevancia de cada dimensión para el reconocimiento de emociones.*

### b) Capas Ocultas
- **Definición:** Varias capas densas que aprenden representaciones de alto nivel a partir de los datos de entrada.
- **Función de activación:**  
  - Se recomienda **ReLU (Rectified Linear Unit)**, que introduce no linealidad y ayuda a evitar problemas como el desvanecimiento del gradiente.

### c) Capa de Salida
- **Definición:** Capa final encargada de generar la predicción de la emoción.
- **Patrón de salida:**  
  - Se utiliza para clasificación, donde cada componente del vector representa la probabilidad de una emoción específica (por ejemplo: felicidad, tristeza, sorpresa, enojo, miedo, asco).
- **Función de activación:**  
  - Se utiliza **softmax** para normalizar los valores de salida y obtener una distribución de probabilidad (valores entre 0 y 1 que suman 1).

---

## Patrones a Utilizar

- **Patrones de Entrada:**  
  - Cada patrón es un conjunto de coordenadas de landmarks normalizadas para hacer la red insensible a la escala y posición absoluta.
  - Es necesario contar con un conjunto de datos etiquetado, donde cada muestra (vector de landmarks) esté asociado a una emoción determinada.
  
- **Patrones de Salida:**  
  - Cada patrón de salida es un vector *one-hot* que indica la emoción correspondiente.  

---

## Función de Activación Necesaria

- **Capas Ocultas:**  
  - **ReLU (Rectified Linear Unit):** Favorece la convergencia rápida y ayuda a mitigar el problema del gradiente desvanecido.
  
- **Capa de Salida:**  
  - **Softmax:** Convierte los logits en probabilidades para cada clase, ideal para problemas de clasificación múltiple.

---

## Número Máximo de Entradas

El número máximo de entradas depende de la cantidad y dimensionalidad de los landmarks utilizados:
- **468 landmarks × 3 coordenadas (x, y, z):** 1404 entradas.
- **468 landmarks × 2 coordenadas (x, y):** 936 entradas.

---

## Valores Esperados a la Salida de la Red

- **Salida Final (Softmax):**  
  - Se espera obtener un vector de probabilidades, donde cada componente representa la probabilidad de que la entrada corresponda a una emoción específica.

- **Interpretación de Resultados:**  
  - El valor máximo en el vector de salida (idealmente cercano a 1) identifica la emoción predominante, mientras que los demás valores cercanos a 0 indican baja probabilidad para las otras clases.

---

## Valores Máximos del Bias

- **Definición del Bias:**  
  - Los bias son parámetros ajustables que se suman a las entradas ponderadas, permitiendo desplazar la función de activación.

- **Rango Inicial y Límites:**
  - **Inicialización:**  
    - Se suelen inicializar en valores pequeños (por ejemplo, en un rango de `[-0.1, 0.1]`) para evitar la saturación de las neuronas.
  - **Durante el Entrenamiento:**  
    - No existe un límite teórico máximo para los bias, ya que se actualizan en función del error del modelo. Su valor final dependerá del proceso de optimización y la magnitud de las activaciones.

---


