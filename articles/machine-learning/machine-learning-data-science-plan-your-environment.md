<properties 
	pageTitle="Identificación de escenarios y planeamiento del procesamiento analítico avanzado de datos | Microsoft Azure" 
	description="Plan para análisis avanzado teniendo en cuenta una serie de cuestiones claves." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev"
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/08/2016" 
	ms.author="bradsev" />


# Identificación de escenarios y planeamiento del procesamiento analítico avanzado de datos

¿Qué recursos debe incluir al configurar un entorno para realizar un procesamiento de análisis avanzado en un conjunto de datos? Este artículo sugiere una serie de preguntas que le ayudarán a identificar las tareas y los recursos pertinentes para su escenario. El orden de los pasos de alto nivel para el análisis predictivo se describe en el documento [¿Qué es el proceso de análisis de Cortana (CAP)?](machine-learning-data-science-the-cortana-analytics-process.md) . Cada uno de estos pasos requiere recursos específicos para las tareas relevantes para su escenario concreto. La clave para identificar su escenario la encontrará en las cuestiones relacionadas con logística de datos, las características, la calidad de los conjuntos de datos y las herramientas y lenguajes que prefiera usar para el análisis.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Cuestiones de logística: movimiento y ubicaciones de los datos
Las cuestiones logísticas se refieren a la ubicación del **origen de datos**, el **destino** en Azure y los requisitos para mover los datos, incluida la programación, la cantidad y los recursos implicados. Es posible que datos tengan que moverse varias veces durante el proceso de análisis. Un escenario común es mover datos locales a algún tipo de almacenamiento en Azure y, a continuación, en el Estudio de aprendizaje automático.

1. **¿Cuál es el origen de datos?** ¿Es local o está la nube? Por ejemplo:
	- Los datos están disponibles públicamente en una dirección HTTP.
	- Los datos residen en una ubicación de archivos de red o local.
	- Los datos están en una base de datos de SQL Server.
	- Los datos se almacenan en un contenedor de almacenamiento de Azure.

2. **¿Cuál es el destino en Azure?** ¿Dónde tiene que estar para procesar o modelar? Por ejemplo:
	- Almacenamiento de blobs de Azure
	- Bases de datos SQL Azure
	- SQL Server en máquinas virtuales de Azure
	- HDInsight (Hadoop en Azure) o tablas de Hive
	- Aprendizaje automático de Azure
	- Discos duros virtuales de Azure que se pueden montar.

3. **¿Cómo va a mover los datos?** En los temas siguientes se describen los procedimientos y los recursos disponibles para introducir o cargar datos en una variedad de entornos de almacenamiento y procesamiento diferentes.

	-  [Carga de datos en entornos de almacenamiento para el análisis](machine-learning-data-science-ingest-data.md) 
	-  [Importación de datos de entrenamiento en Estudio de aprendizaje automático de Azure desde varios orígenes de datos](machine-learning-data-science-import-data,md)

4. **¿Los datos necesitan moverse siguiendo una programación regular o modificarse durante la migración?** Considere el uso de Factoría de datos de Azure (ADF) cuando los datos tengan que migrarse continuamente, especialmente en el caso de un escenario híbrido en el que se tenga acceso a recursos locales y de nube, o cuando se realice una transacción de datos o estos tengan que modificarse o tener lógica de negocios agregada mientras se migran. Para más información, consulte [Mover datos desde un servidor SQL Server local a SQL Azure con la Factoría de datos de Azure](machine-learning-data-science-move-sql-azure-adf.md).

5. **¿Qué cantidad de datos se va a mover a Azure?** Los conjuntos de datos muy grandes pueden superar la capacidad de almacenamiento de ciertos entornos. Para ver un ejemplo, consulte la explicación de los límites de tamaño para el Estudio de aprendizaje automático en la sección siguiente. En tales casos, puede usarse una muestra de los datos durante el análisis. Para más información sobre cómo reducir la muestra de un conjunto de datos en diversos entornos de Azure, consulte [Muestreo de datos del proceso de análisis de Cortana](machine-learning-data-science-sample-data.md).


## Cuestiones sobre las características de los datos: tipo, formato y tamaño
Estas cuestiones son clave para planear los entornos de almacenamiento y procesamiento, cada uno de los cuales es adecuado para diversos tipos de datos y cada uno cuenta con ciertas restricciones.

1. **¿Cuáles son los tipos de datos?** Por ejemplo: 
	- Numérico
	- Categorías
	- Cadenas
	- Binary

2. **¿Qué formato tienen los datos?** Por ejemplo:
    - Archivos sin formato separados por comas (CSV) o separados por tabulaciones (TSV)
    - Comprimidos o sin comprimir
	- Blobs de Azure
	- Tablas de Hadoop Hive
	- Tablas de SQL Server

2. **¿Qué tamaño tienen los datos?**
    - Pequeño: menos de 2 GB
    - Medio: más de 2 GB y menos de 10 GB
	- Grande: más de 10 GB

Veamos por ejemplo el Estudio de aprendizaje automático de Azure:

- Para obtener una lista de los formatos de datos y los tipos admitidos por Estudio de aprendizaje automático de Azure, consulte la sección [Tipos y formatos de datos admitidos](machine-learning-data-science-import-data.md#data-formats-and-data-types-supported).
- Para más información sobre las limitaciones en los datos para Estudio de aprendizaje automático de Azure, consulte la sección **¿Cómo de grande puede ser el conjunto de datos para mis módulos?** en [Importación y exportación de datos para Aprendizaje automático](machine-learning-faq.md#machine-learning-studio-questions)

Para más información sobre las limitaciones de otros servicios de Azure usados en el proceso de análisis, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](azure-subscription-service-limits.md).

## Cuestiones sobre la calidad de los datos: exploración y procesamiento previo

1. **¿Qué sabe acerca de los datos?** Explore los datos cuando tenga que tener un conocimiento de sus características básicas. ¿Qué patrones o tendencias muestran, qué valores atípicos tienen o cuántos valores faltan? Este paso es importante para determinar el alcance de procesamiento previo necesario, para la formulación de hipótesis que podrían sugerir las características o el tipo de análisis más apropiados y para elaborar planes de recopilación de datos adicionales. Calcular estadísticas descriptivas y trazar visualizaciones son técnicas útiles para la inspección de datos. Para más información sobre cómo explorar un conjunto de datos en diversos entornos de Azure, consulte [Exploración de los datos del proceso de análisis de Cortana](machine-learning-data-science-explore-data.md).

2. **¿Los datos requieren un procesamiento previo o una limpieza?** El preprocesamiento y la limpieza de datos son tareas importantes que normalmente se deben llevar a cabo para que el conjunto de datos se pueda usar de forma eficaz para el aprendizaje automático. Los datos sin procesar son a menudo ruidosos no confiables y es posible que les falten valores. El uso de estos datos para el modelado puede producir resultados engañosos. Para ver una descripción, consulte [Tareas para preparar los datos para el aprendizaje automático mejorado](machine-learning-data-science-prepare-data.md).

## Cuestiones sobre herramientas y lenguajes
Hay muchas opciones dependiendo de qué lenguajes y entornos de desarrollo o herramientas necesita o prefiere usar.
 
1. **¿Qué lenguajes prefiere usar para el análisis?**  
	- R
	- Python
	- SQL

2. **¿Qué herramientas debe usar para analizar los datos?**
	- [Microsoft Azure Powershell](powershell-install-configure.md): un lenguaje de script que se usa para administrar los recursos de Azure en un lenguaje de script.
	- [Estudio de aprendizaje automático de Azure](machine-learning-what-is-ml-studio/)
	- [Revolution Analytics](http://www.revolutionanalytics.com/revolution-r-open)
	- [RStudio](http://www.rstudio.com)
	- [Python Tools para Visual Studio](http://microsoft.github.io/PTVS/)
	- [Anaconda](https://www.continuum.io/why-anaconda)
	- [Blocs de notas de Júpiter](http://jupyter.org/)
	- [Microsoft Power BI](http://powerbi.microsoft.com) 


## Identificación del escenario de análisis avanzado
Cuando haya respondido a las preguntas de la sección anterior, estará listo para determinar qué escenario se adapta mejor a su caso. Los escenarios de ejemplo que se describen en [Escenarios para análisis avanzado en Aprendizaje automático de Azure](../machine-learning-data-science-plan-sample-scenarios.md).







 

<!---HONumber=AcomDC_0211_2016-->