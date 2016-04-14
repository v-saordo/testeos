<properties
   pageTitle="Análisis de datos con Aprendizaje automático de Azure | Microsoft Azure"
   description="Tutorial para usar Aprendizaje automático de Azure con Almacenamiento de datos SQL de Azure para el desarrollo de soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="sahajs;barbkess;sonyama"/>

# Análisis de datos con Aprendizaje automático de Azure
Este tutorial le enseñará a crear un modelo de aprendizaje automático predictivo con Aprendizaje automático de Azure con sus datos de Almacenamiento de datos SQL de Azure. En este tutorial, crearemos una campaña de marketing dirigida para Adventure Works, una tienda de bicicletas, mediante la predicción de la probabilidad que existe de que un cliente compre una bicicleta.

> [AZURE.VIDEO integrating-azure-machine-learning-with-azure-sql-data-warehouse]

## Requisitos previos
Para seguir paso a paso este tutorial, necesita

- Almacenamiento de datos SQL con la base de datos de ejemplo AdventureWorksDW.

[Creación de un Almacenamiento de datos SQL][] muestra cómo aprovisionar una base de datos con datos de ejemplo. Si ya tiene una base de datos de Almacenamiento de datos SQL pero no tiene datos de ejemplo, puede [cargar manualmente los datos de ejemplo][].


## Paso 1: Obtención de datos
Leeremos los datos de la vista dbo.vTargetMail en la base de datos AdventureWorksDW.

1. Inicie sesión en [Estudio de aprendizaje automático de Microsoft Azure][] y haga clic en mis experimentos.
2. Haga clic en **+NUEVO** y seleccione **Experimento en blanco**.
3. Escriba un nombre para el experimento: Targeted Marketing.
4. Arrastre el módulo **Lector** del panel de módulos al lienzo.
5. Especifique los detalles de la base de datos de Almacenamiento de datos SQL en el panel Propiedades.
6. Especifique la **consulta** de la base de datos para leer los datos de interés.

   ```
   SELECT [CustomerKey]
      ,[GeographyKey]
      ,[CustomerAlternateKey]
      ,[MaritalStatus]
      ,[Gender]
      ,cast ([YearlyIncome] as int) as SalaryYear
      ,[TotalChildren]
      ,[NumberChildrenAtHome]
      ,[EnglishEducation]
      ,[EnglishOccupation]
      ,[HouseOwnerFlag]
      ,[NumberCarsOwned]
      ,[CommuteDistance]
      ,[Region]
      ,[Age]
      ,[BikeBuyer]
  FROM [dbo].[vTargetMail]
   ```

Para ejecutar el experimento, haga clic en **Ejecutar** en el lienzo de experimentos. ![Ejecutar el experimento][1]


Cuando el experimento haya terminado de ejecutarse correctamente, haga clic en el puerto de salida en la parte inferior del módulo del lector y seleccione **Visualizar** para ver los datos importados. ![Ver los datos importados][3]





## Paso 2: Limpieza de datos
Se quitarán algunas columnas que no son relevantes para el modelo.

1. Arrastre el módulo **Columnas del proyecto** al lienzo.
2. Haga clic en **Iniciar selector de columnas** en el panel Propiedades para especificar las columnas que desea quitar. ![Columnas del proyecto][4]

3. Excluya dos columnas: CustomerAlternateKey y GeographyKey. ![Quitar las columnas innecesarias][5]




## Paso 3: Creación del modelo
Dividiremos los datos 80-20: 80% para entrenar un modelo de aprendizaje automático y un 20% para probar el modelo. Usaremos los algoritmos de "dos clases" para este problema de clasificación binaria.

1. Arrastre el módulo **Dividir** al lienzo.
2. Escriba 0,8 en Fracción de filas del primer conjunto de datos de salida en el panel Propiedades. ![Dividir los datos en conjunto de entrenamiento y prueba][6]
3. Arrastre el módulo **Árbol de decisión aumentado de dos clases** al lienzo.
4. Arrastre el módulo **Entrenar modelo** al lienzo y especifique las entradas. Luego, haga clic en **Iniciar el selector de columnas** en el panel Propiedades.
      - Primera entrada: el algoritmo de aprendizaje automático.
      - Segunda entrada: datos para entrenar el algoritmo. ![Conectar el módulo Entrenar modelo][7]
5. Seleccione la columna **BikeBuyer** como columna de predicción. ![Seleccionar columna de predicción][8]





## Paso 4: Modelo de puntuación
Ahora, probaremos cómo funciona el modelo con datos de prueba. Compararemos el algoritmo que elijamos con otro algoritmo para comprobar cuál funciona mejor.

1. Arrastre el módulo **Puntuar modelo** al lienzo. Primera entrada: modelo entrenado. Segunda entrada: datos de prueba ![Puntuación del modelo][9]
2. Arrastre **Máquina del punto de Bayes de dos clases** al lienzo del experimento. Compararemos cómo funciona este algoritmo en comparación con el Árbol de decisión aumentado de dos clases.
3. Copie y pegue los módulos Entrenar modelo y Puntuar modelo en el lienzo.
4. Arrastre el módulo **Evaluar modelo** al lienzo para comparar los dos algoritmos.
5. **Ejecute** el experimento. ![Ejecutar el experimento][10]
6. Haga clic en el puerto de salida en la parte inferior del módulo Evaluar modelo y haga clic en Visualizar. ![Visualizar los resultados de evaluación][11]



Las métricas proporcionadas son la curva ROC, el diagrama de retirada-precisión y la curva de elevación. Al mirar estas métricas, podemos ver que el primer modelo funciona mejor que el segundo. Para ver lo que ha predicho el primer modelo, haga clic en el puerto de salida de Puntuar modelo y haga clic en Visualizar. ![Visualizar los resultados de puntuación][12]

Verá dos columnas más agregadas al conjunto de datos de prueba.

- Probabilidades puntuadas: la probabilidad de que un cliente sea comprador de bicicletas.
- Etiquetas puntuadas: la clasificación realizada por el modelo – comprador de bicicletas (1) o no (0). Este umbral de probabilidad para etiquetar se establece en 50% y se puede ajustar.

Comparación de la columna BikeBuyer (real) con las etiquetas puntuadas (predicción), puede ver cómo ha funcionado el modelo. Como pasos siguientes, puede usar este modelo para realizar predicciones para los nuevos clientes y publicar este modelo como un servicio web o volver a escribir los resultados en Almacenamiento de datos SQL.

Para obtener más información sobre la creación de modelos de aprendizaje automático predictivo, consulte [Introducción al aprendizaje automático en Azure][].



<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1_reader.png
[2]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2_visualize.png
[3]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3_readerdata.png
[4]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4_projectcolumns.png
[5]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5_columnselector.png
[6]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6_split.png
[7]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7_train.png
[8]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8_traincolumnselector.png
[9]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9_score.png
[10]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10_evaluate.png
[11]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11_evalresults.png
[12]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12_scoreresults.png


<!--Article references-->
[Estudio de aprendizaje automático de Microsoft Azure]: https://studio.azureml.net/
[Introducción al aprendizaje automático en Azure]: https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[cargar manualmente los datos de ejemplo]: sql-data-warehouse-get-started-manually-load-samples.md
[Creación de un Almacenamiento de datos SQL]: sql-data-warehouse-get-started-provision.md

<!---HONumber=AcomDC_0309_2016-->