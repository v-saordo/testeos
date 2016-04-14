<properties
   pageTitle="Uso de Aprendizaje automático de Azure con Almacenamiento de datos SQL | Microsoft Azure"
   description="Tutorial para usar Aprendizaje automático de Azure con Almacenamiento de datos SQL de Azure para el desarrollo de soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="sahajs;barbkess;sonyama"/>

# Uso de Aprendizaje automático de Azure con Almacenamiento de datos SQL

Aprendizaje automático de Azure es un servicio de análisis predictivo que puede usar para crear modelos predictivos con sus datos en Almacenamiento de datos SQL y publicarlos como servicios web listos para su consumo. Para aprender los conceptos básicos del análisis predictivo y el aprendizaje automático, lea [Introducción a Aprendizaje automático en Azure][]. Puede aprender a crear, entrenar, puntuar y probar un modelo de aprendizaje automático con el [Tutorial para crear un experimento][].

En este artículo, aprenderá cómo hacer lo siguiente utilizando [Estudio de aprendizaje automático de Azure][]\:

- Leer datos de la base de datos para crear, entrenar y puntuar un modelo predictivo
- Escribir datos en la base de datos 


## Lectura de datos desde Almacenamiento de datos SQL

Leeremos datos desde la tabla Producto en la base de datos AdventureWorksDW.

### Paso 1

Inicie un experimento nuevo haciendo clic en +NUEVO en la parte inferior de la ventana de Estudio de aprendizaje automático, seleccione EXPERIMENTO y luego Experimento en blanco. Seleccione el nombre del experimento predeterminado en la parte superior del lienzo y cámbielo por uno significativo, por ejemplo, Predicción del precio de bicicletas.

### Paso 2

Busque el módulo Lector en la paleta de conjuntos de datos y módulos que aparece a la izquierda del lienzo de experimentos. Arrastre el módulo al lienzo de experimentos. ![][drag_reader]

### Paso 3

Seleccione el módulo Lector y rellene el panel de propiedades.

1. Seleccione Base de datos SQL de Azure como el origen de datos.
2. Nombre del servidor de base de datos: escriba el nombre del servidor. Para encontrarlo, puede usar el [Portal de Azure clásico][].

![][server_name]

3. Nombre de la base de datos: escriba el nombre de la base de datos en el servidor que acaba de especificar. 
4. El nombre de la cuenta de usuario de servidor: escriba el nombre de usuario de una cuenta que tiene permisos de acceso para la base de datos. 
5. Contraseña de la cuenta de usuario de servidor: proporcione la contraseña de la cuenta de usuario especificada.
6. Aceptar cualquier certificado de servidor: use esta opción (menos segura) si desea omitir la revisión del certificado de sitio antes de leer los datos.
7. Consulta de base de datos: escriba una instrucción SQL que describa los datos que desea leer. En este caso, leeremos datos desde la tabla Producto con la siguiente consulta.


```
SELECT ProductKey, EnglishProductName, StandardCost,
        ListPrice, Size, Weight, DaysToManufacture,
        Class, Style, Color
FROM dbo.DimProduct;
```

![][reader_properties]

### Paso 4

1. Ejecute el experimento; para ello, haga clic en Ejecutar bajo el lienzo de experimentos.
2. Cuando el experimento finalice, el módulo Lector tendrá una marca de verificación verde para indicar que se completó correctamente. Observe también el estado Ejecución finalizada en la esquina superior derecha.

![][run]

3. Para ver los datos importados, haga clic en el puerto de salida en la parte inferior del conjunto de datos de automóvil y seleccione Visualizar.


## Creación, entrenamiento y puntuación de un modelo

Ahora puede utilizar este conjunto de datos para:

- Crear un modelo: procesar datos y definir características
- Entrenar el modelo: elegir y aplicar un algoritmo de aprendizaje
- Puntuar y probar el modelo: predecir el nuevo precio de bicicleta
 

![][model]

Puede aprender a crear, entrenar, puntuar y probar un modelo de aprendizaje automático con el [Tutorial para crear un experimento][].

## Escritura de datos en Almacenamiento de datos SQL de Azure

Escribiremos el conjunto de resultados en la tabla ProductPriceForecast de la base de datos AdventureWorksDW.

### Paso 1

Busque el módulo Redactor en la paleta de conjuntos de datos y módulos que aparece a la izquierda del lienzo de experimentos. Arrastre el módulo al lienzo de experimentos.

![][drag_writer]

### Paso 2

Seleccione el módulo Redactor y rellene el panel de propiedades.

1. Seleccione Base de datos SQL de Azure como el destino de los datos.
2. Nombre del servidor de base de datos: escriba el nombre del servidor. Para encontrarlo, puede usar el [Portal de Azure clásico][]. 
3. Nombre de la base de datos: escriba el nombre de la base de datos en el servidor que acaba de especificar. 
4. El nombre de la cuenta de usuario de servidor: escriba el nombre de usuario de una cuenta que tiene permisos de escritura para la base de datos. 
5. Contraseña de la cuenta de usuario de servidor: proporcione la contraseña de la cuenta de usuario especificada.
6. Aceptar cualquier certificado de servidor (no seguro): seleccione esta opción si no desea ver el certificado.
7. Lista separada por comas de las columnas que se guardarán: proporcione una lista de las columnas de resultados o conjuntos de datos que desea obtener.
8. Nombre de la tabla de datos: especifique el nombre de la tabla de datos.
9. Lista separada por comas de las columnas de la tabla de datos: especifique los nombres de las columnas que se usarán en la tabla nueva. Los nombres de las columnas pueden ser distintos a los nombres del conjunto de datos de origen, pero debe aquí debe indicar el mismo número de columnas que define en la tabla de salida.
10. Número de filas escritas por operación de SQL Azure: puede configurar el número de filas que se escriben en una base de datos SQL en una operación.

![][writer_properties]

### Paso 3

1. Ejecute el experimento; para ello, haga clic en Ejecutar bajo el lienzo de experimentos.
2. Cuando el experimento finalice, todos los módulos tendrán una marca de verificación verde para indicar que se han implementado correctamente. 

## Pasos siguientes

Para obtener información general sobre la integración, consulte [Información general de la integración de Almacenamiento de datos SQL][].

Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo de Almacenamiento de datos SQL][].

<!--Image references-->

[drag_reader]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-reader.png
[server_name]: ./media/sql-data-warehouse-integrate-azure-machine-learning/dw-server-name.png
[reader_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-reader-properties.png
[run]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-finished-running.png
[model]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-create-train-score-model.png
[drag_writer]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-writer.png
[writer_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-writer-properties.png

<!--Article references-->

[información general sobre desarrollo de Almacenamiento de datos SQL]: ./sql-data-warehouse-overview-develop/
[Información general de la integración de Almacenamiento de datos SQL]: ./sql-data-warehouse-overview-integration/
[Tutorial para crear un experimento]: https://azure.microsoft.com/es-ES/documentation/articles/machine-learning-create-experiment/
[Introducción a Aprendizaje automático en Azure]: https://azure.microsoft.com/es-ES/documentation/articles/machine-learning-what-is-machine-learning/
[Estudio de aprendizaje automático de Azure]: https://studio.azureml.net/Home
[Portal de Azure clásico]: https://portal.azure.com/

<!--MSDN references-->

<!--Other Web references-->

[Azure Machine Learning documentation]: http://azure.microsoft.com/documentation/services/machine-learning/

<!---HONumber=AcomDC_0114_2016-->