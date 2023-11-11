
<h1 align='center'>
 <b>PROYECTO </b>
</h1>
 
# <h1 align="center">**`Sistema de recomendación de videojuegos para usuarios`**</h1>

![videojuegos](imagen_videojuego.jpg)

## 1- **Introducción**

El desafío planteado para este proyecto implica el desarrollo de un proceso de MLOps que abarque varias etapas, comenzando con la Ingeniería de Datos, que incluye la Extracción, Transformación y Carga (ETL) de datos. Luego, se avanza hacia el Machine Learning, donde se realiza un Análisis Exploratorio de Datos (EDA) y se exploran y entrenan modelos. Finalmente, se culmina con la implementación tanto del modelo como de los datos procesados en la etapa ETL.

## 2- **Desarrollo del Proyecto**
### **ETL/Ingenieria de Datos:**

En el rol de Data Engineer, encaramos el proceso de ETL. El detalle del proceso puede ser encontrado en el archivo [etl.ipynb](etl.ipynb), con comentarios en Markdown paso a paso, pero algunos de los pasos destacados son (en orden, auque algunos pasos se repiten luego de su primera aparición):

- Importación de librerías y lectura del archivo original.
- Conversión a dataframe de pandas, ejecución de info() para una primera observación general.
- Observación del formato de los datos en el dataset, al mismo tiempo se observa el diccionario de datos proporcionado.
- Búsqueda y observación de filas sin datos útiles (vacíos, datos inespecíficos, duplicados).
-Eliminación de columnas que no se utilizarán en aplicaciones posteriores, como el ID.
- Conversión de los tipos de datos asignados por defecto a tipos adecuados.
- Exploración de columnas con similitudes y sustitución de datos ausentes usando similitudes.
- Eliminación de columnas redundantes (columnas que aportan datos casi idénticos).
- Sustitución de valores string por valores float o NaN donde sea posible, y reasignación del tipo de datos de la/s columnas que lo permitan.
- Reemplazo de valores sin relación con la columna donde se encuentran por NA.
- Reinicio del índice y guardado del dataset como archivo de parquet (elegido por almacenar data types y su bajo peso de archivo en comparación con alternativas como .json y .csv) para posterior uso en la aplicación FAST API, para sus primeras 6 funciones.


## 3- **EDA**

Nuevamente, el detalle de este proceso puede seguirse paso a paso en el Jupyter Notebook eda.ipynb. Algunos puntos a destacar son:

- Partida desde el archivo parquet elaborado en la etapa ETL.
- Descarte de filas que no contienen precios (ya que el precio será la variable a predecir por el modelo).
- Scatter plot y observación de outliers, puntos a destacar.
  - Si la edad de release es mayor a aproximadamente 180 meses (15 años), los precios de los juegos están centrados por debajo de 25.
  - Si la edad del release es menor, la dispersión de precio es notable. La mayoría aún se concentra entre 0 y 25, pero hay muchos puntos por encima de ese rango también.
- Columnas calculadas de cantidad de juegos por desarrollador y el precio promedio por desarrollador.
Nueva matriz de correlación para ver la fuerza de la edad del release y los precios promedio.
- Exploración y decisión sobre columnas numéricas existentes con baja correlación al precio.
- Exploración de Géneros y Sentiments con técnica one-hot encoding, decisión respecto a las mismas.
- Eliminación de filas que carecen de valores en las columnas seleccionadas para explorar modelos.
-Reinicio del índice y guardado del dataframe en archivo parquet, para ser consumido por el modelo.

## 4- **Experimentación y Entrenamiento con Modelos**
1- Exploración de Modelos.

Modelos Explorados y Entrenados:

- Modelo de Regresión Lineal
- Modelo de Regresión Lineal con características polinomiales
- Modelo de Árbol (Tree)
- Modelo de K-vecinos
- Modelo de Bosque Aleatorio (Random Forest)
- Modelo de Regresor de Vectores de Soporte
  - Kernel lineal
  - Kernel Polinomial
  - Kernel de función de base radial
- Modelo de Ensamble: Stacking con 3 modelos de mejor rendimiento.

Pasos Detallados en [experimentación](experimentacion_entrenamiento.ipynb):

- Usando como origen el archivo generado en la etapa EDA, definimos X e y, dividimos en conjuntos de prueba y entrenamiento, y escalamos los datos.
- Para cada Modelo:
  - Pre procesamiento adicional en caso de ser necesario (características polinómicas).
  - Instanciamos el modelo y ajustamos el conjunto de entrenamiento.
  - Realizamos predicciones.
  - Observamos MSE, RMSE, R2.
  - Generamos gráficos adecuados al modelo.
- En los modelos con menos error:
  - Observamos la validación cruzada.
- Curva de aprendizaje.
  - Aplicación de técnicas de aprendizaje para varios modelos que permiten hiperparámetros (algunos fueron muy exigentes en procesamiento y no fue posible).

2- Pruebas de Predicciones Individuales y Procesamiento de Entrada:

- Simulamos la entrada de usuario en nuestra función API de predicción.
- Realizamos cálculos para campos calculados.
- Formateamos y escalamos adecuadamente para cada modelo.
- Predecimos con los modelos de mejor rendimiento/menor error.
- Guardamos los preprocesamientos en un archivo de pickle para uso en la aplicación.
- Guardamos el RMSE del modelo lineal en pruebas en un archivo.
- Guardamos el modelo elegido en un archivo de pickle.
  - Todos los modelos con base lineal tuvieron rendimientos similares, por encima de los modelos no basados en lo lineal. Por simplicidad del modelo y un menor peso de archivo, elegimos el modelo lineal clásico.
- Probamos los archivos.

## 5- **Desarrollo de la aplicación de FAST API**

La misma consta de 3 funciones de validacion de datos, 1 ruta raiz, 6 rutas de consulta de datos del dataset (endpoits), mas la ruta de prediccion de precio de juegos usando el modelo de Machine Learning.


## 6- **Fuentes de datos**
El repositorio no contiene los datos originales provistos para el proyecto, los mismos puedenser encontrados en las siguientes ubicaciones:
- [Dataset:](https://drive.google.com/drive/folders/1HqBG2-sUkz_R3h1dZU5F2uAzpRn7BSpj?usp=drive_link) Carpeta con el archivo de origen en formato .json(steam_games.json)
- [Diccionario de datos:](https://docs.google.com/spreadsheets/d/1-t9HLzLHIGXvliq56UE_gMaWBVTPfrlTf2D9uAtLGrk/edit?usp=sharing) Diccionario con algunas descripciones de las columnas disponibles en el dataset.

