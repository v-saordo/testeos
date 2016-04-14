<properties
	pageTitle="Guía técnica de la plantilla de solución de Cortana Analytics para mantenimiento predictivo en empresas aeroespaciales y de otros tipos | Microsoft Azure"
	description="Una guía técnica para la plantilla de solución con Microsoft Cortana Analytics para mantenimiento predictivo en los sectores aeroespacial, de servicios públicos y de transporte."
	services="cortana-analytics"
	documentationCenter=""
	authors="fboylu"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cortana-analytics"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/29/2016"
	ms.author="fboylu" />

# Guía técnica de la plantilla de solución de Cortana Analytics para mantenimiento predictivo en empresas aeroespaciales y de otros tipos

## **Agradecimientos**
Los autores de este artículo son los científicos de datos Yan Zhang, Gauher Shaheen, Fidan Boylu Uz y el ingeniero de software Dan Grecoe de Microsoft.

## **Información general**

Las plantillas de soluciones están diseñadas para acelerar el proceso de creación de una demostración E2E basada en Cortana Analytics Suite. Una plantilla implementada aprovisionará la suscripción con los componentes necesarios de Cortana Analytics y creará las relaciones entre ellos. También propaga la canalización de datos con datos de ejemplo generados a partir de una aplicación de generador de datos que descargará e instalará en el equipo local después de implementar la plantilla de la solución. Los datos generados a partir del generador hidratarán la canalización de datos e iniciarán la generación de predicciones de aprendizaje automático que podrán verse a continuación en el panel de Power BI. El proceso de implementación le guiará a través de varios pasos para configurar las credenciales de la solución. Asegúrese de que registra estas credenciales: nombre de la solución, nombre de usuario y contraseña que proporcionó durante la implementación.

El objetivo de este documento es explicar la arquitectura de referencia y los diferentes componentes aprovisionados en su suscripción como parte de esta plantilla de solución. El documento también trata sobre cómo reemplazar los datos de ejemplo con datos reales para poder ver recomendaciones y predicciones a partir de sus propios datos. Además, el documento explica las partes de la plantilla de solución que deberán modificarse si desea personalizar la solución con sus propios datos. Se proporcionan instrucciones sobre cómo crear el panel de Power BI para esta plantilla de solución al final.

>[AZURE.TIP] Puede descargar e imprimir una [versión en PDF de este documento](http://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf).

## **Idea general**

![](media/cortana-analytics-technical-guide-predictive-maintenance\predictive-maintenance-architecture.png)

Cuando se implementa la solución, se activan varios servicios de Azure dentro de Cortana Analytics Suite (*por ejemplo,* Centro de eventos, Análisis de transmisiones, HDInsight, Factoría de datos, Aprendizaje automático, *etc.*). El diagrama de arquitectura anterior muestra, a nivel general, cómo se construye el mantenimiento predictivo para la plantilla de solución aeroespacial de un extremo a otro. Podrá investigar estos servicios en el Portal de Azure haciendo clic en ellos en el diagrama de la plantilla de solución creada con la implementación de la solución, con la excepción de HDInsight, ya que este servicio se aprovisiona a petición cuando las actividades de canalización relacionadas se requieren para la ejecución y se eliminan a continuación. Puede descargar una [versión en tamaño completo del diagrama](http://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

Las secciones siguientes describen cada parte.

## **Origen e ingesta de datos**

### Origen de datos sintéticos

Para esta plantilla, el origen de datos utilizado se genera desde una aplicación de escritorio que se descarga y ejecuta localmente tras una implementación correcta. Encontrará las instrucciones para descargar e instalar esta aplicación en la barra de propiedades al seleccionar el primer nodo denominado Predictive Maintenance Data Generator en el diagrama de la plantilla de solución. Esta aplicación alimenta al servicio [Centro de eventos de Azure](#azure-event-hub) con puntos de datos o eventos, que se utilizarán en el flujo de solución restante. Este origen de datos se forma o deriva a partir de datos disponibles públicamente en el [repositorio de datos de la NASA](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/) utilizando el [Turbofan Engine Degradation Simulation Data Set](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan).

La aplicación de generación de eventos rellenará el Centro de eventos de Azure solo mientras se esté ejecutando en el equipo.

### Centro de eventos de Azure

El servicio [Centro de eventos de Azure](https://azure.microsoft.com/services/event-hubs/) es el destinatario de la entrada proporcionada por el origen de datos sintéticos descrito anteriormente.

## **Preparación y análisis de datos**


### Análisis de transmisiones de Azure

El servicio [Análisis de transmisiones de Azure](https://azure.microsoft.com/services/stream-analytics/) se utiliza para proporcionar análisis casi en tiempo real en el flujo de entrada del servicio [Centro de eventos de Azure](#azure-event-hub) y para publicar los resultados en un panel de [Power BI](https://powerbi.microsoft.com) y, al mismo tiempo, archivar todos los eventos de entrada sin procesar en el [Almacenamiento de Azure](https://azure.microsoft.com/services/storage/) para su posterior procesamiento mediante el servicio [Factoría de datos de Azure](https://azure.microsoft.com/documentation/services/data-factory/).

### Agregación personalizada de HD Insights

El servicio HD Insight de Azure se utiliza para ejecutar scripts de [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (organizados mediante Factoría de datos de Azure) para proporcionar las agregaciones en los eventos sin procesar que se almacenaron con el servicio Análisis de transmisiones de Azure.

### Aprendizaje automático de Azure

El servicio [Aprendizaje automático de Azure](https://azure.microsoft.com/services/machine-learning/) se utiliza (organizado por la Factoría de datos de Azure) para realizar predicciones sobre la vida útil restante (RUL) de un motor de avión determinado dadas las entradas que recibe.

## **Publicación de datos**


### Servicio Base de datos SQL de Azure.

El servicio [Base de datos SQL de Azure](https://azure.microsoft.com/services/sql-database/) se utiliza para almacenar (administradas por la Factoría de datos de Azure) las predicciones recibidas por el servicio de Aprendizaje automático de Azure que se utilizarán en el panel de [Power BI](https://powerbi.microsoft.com).

## **Consumo de datos**

### Power BI

El servicio [Power BI](https://powerbi.microsoft.com) se utiliza para mostrar un panel que contiene las agregaciones y alertas proporcionadas por el servicio [Análisis de transmisiones de Azure](https://azure.microsoft.com/services/stream-analytics/), así como las predicciones sobre la vida útil restante almacenadas en la [Base de datos SQL de Azure](https://azure.microsoft.com/services/sql-database/) que se hayan producido al utilizar el servicio [Aprendizaje automático de Azure](https://azure.microsoft.com/services/machine-learning/). Para instrucciones sobre cómo crear el panel de Power BI para esta plantilla de solución, consulte la sección siguiente.

## **Incorporación de sus propios datos**

Esta sección describe cómo traer sus propios datos a Azure y qué áreas necesitarían cambios para los datos que ha traído a esta arquitectura.

Es improbable que cualquier conjunto de datos que traiga coincida con el conjunto de datos utilizado por el [Turbofan Engine Degradation Simulation Data Set](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan) empleado en esta plantilla de solución. Entender bien los datos y los requisitos resultará vital a la hora de modificar esta plantilla para que funcione con sus propios datos. Si se trata del primer contacto con el servicio Aprendizaje automático de Azure, puede obtener una introducción al mismo mediante el ejemplo que puede encontrar en [Tutorial de Aprendizaje automático: Creación del primer experimento en Estudio de aprendizaje automático de Azure](machine-learning-create-experiment.md).

Los apartados siguientes describen las secciones de la plantilla que requieren modificaciones cuando se introduce un nuevo conjunto de datos.

### Centro de eventos de Azure

El servicio Centro de eventos de Azure es muy genérico, hasta tal punto que se pueden publicar datos en el centro en formato CSV o JSON. No se produce ningún procesamiento especial en el Centro de eventos de Azure, pero es importante que comprenda los datos que se introducen en él.

Este documento no describe cómo introducir los datos, pero puede enviar fácilmente eventos o datos a un Centro de eventos de Azure mediante la API del Centro de eventos.

### Análisis de transmisiones de Azure

El servicio Análisis de transmisiones de Azure se utiliza para proporcionar análisis casi en tiempo real mediante la lectura de flujos de datos y el envío de datos a cualquier número de orígenes.

Para el mantenimiento predictivo de la plantilla de solución aeroespacial, la consulta del servicio Análisis de transmisiones de Azure consta de cuatro subconsultas, y cada una de ellas consume eventos del servicio Centro de eventos de Azure y tiene salidas a cuatro ubicaciones distintas. Estas salidas constan de tres conjuntos de datos de Power BI y una ubicación de Almacenamiento de Azure.

La consulta de Análisis de transmisiones de Azure puede encontrarse al:

-   Iniciar sesión en el Portal de Azure.

-   Búsqueda de los trabajos de Análisis de transmisiones ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-stream-analytics.png) que se generaron cuando se implementó la solución (*por ejemplo,*, **maintenancesa02asapbi** y **maintenancesa02asablob** para la solución de mantenimiento predictivo)

-   Seleccionar

    -   ***INPUTS*** para ver la entrada de consulta

    -   ***QUERY*** para ver la consulta en sí

    -   ***OUTPUTS*** para ver las diferentes salidas

Puede encontrar información acerca de la construcción de consultas de Análisis de transmisiones de Azure en [Stream Analytics Query Reference (Referencia de consultas en Análisis de transmisiones)](https://msdn.microsoft.com/library/azure/dn834998.aspx) en MSDN.

En esta solución, las consultas envían tres conjuntos de datos con información de análisis casi en tiempo real acerca del flujo de datos entrante a un panel de Power BI que se proporciona como parte de esta plantilla de solución. Dado que hay un conocimiento implícito acerca del formato de datos entrantes, estas consultas deben modificarse según su formato de datos.

La consulta en el segundo trabajo de Análisis de transmisiones, **maintenancesa02asablob**, simplemente envía todos los eventos de [Centro de eventos](https://azure.microsoft.com/services/event-hubs/) al [Almacenamiento de Azure](https://azure.microsoft.com/services/storage/) y, por tanto, no necesita ninguna modificación, independientemente del formato de los datos, ya que toda la información del evento se transmitió al almacenamiento.

### Factoría de datos de Azure

El servicio [Factoría de datos de Azure](https://azure.microsoft.com/documentation/services/data-factory/) organiza el movimiento y procesamiento de los datos. En el mantenimiento predictivo para la plantilla de solución aeroespacial, la Factoría de datos se compone de tres [canalizaciones](../data-factory/data-factory-create-pipelines.md) que mueven y procesan los datos mediante varias tecnologías. Puede tener acceso a la Factoría de datos abriendo el nodo Factoría de datos en la parte inferior del diagrama de la plantilla de solución que se creó con la implementación de la solución. Esto le llevará a la Factoría de datos del Portal de Azure. Si ve errores en los conjuntos de datos, puede ignorarlos ya que se deben a que la Factoría de datos se implementó antes de iniciarse el generador de datos. Estos errores no impiden que la Factoría de datos funcione.

![](media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

Esta sección describe las [canalizaciones](../data-factory/data-factory-create-pipelines.md) y [actividades](../data-factory/data-factory-create-pipelines.md) necesarias contenidas en la [Factoría de datos de Azure](https://azure.microsoft.com/documentation/services/data-factory/). A continuación se muestra la vista de diagrama de la solución.

![](media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Dos de las canalizaciones de esta factoría contienen scripts de [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) que se utilizan para crear particiones y agregar los datos. Cuando los anote, los scripts se encontrarán en la cuenta de [Almacenamiento de Azure](https://azure.microsoft.com/services/storage/) creada durante la instalación. Su ubicación será: maintenancesascript\\\script\\\hive\\\ (o nombre de la solución de https://[Your].blob.core.windows.net/maintenancesascript).

Al igual que las consultas de [Análisis de transmisiones de Azure](#azure-stream-analytics-1), los scripts de [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) tienen un conocimiento implícito acerca del formato de los datos entrantes, por lo que estas consultas tendrían que modificarse en función del formato de los datos y de los requisitos de la [ingeniería de características](machine-learning-feature-selection-and-engineering.md).

#### *AggregateFlightInfoPipeline*

Esta [canalización](../data-factory/data-factory-create-pipelines.md) contiene una sola actividad: una actividad de [HDInsightHive](../data-factory/data-factory-hive-activity.md) que utiliza un servicio [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que ejecuta un script de [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para crear una partición de los datos colocados en el [Almacenamiento de Azure](https://azure.microsoft.com/services/storage/) durante el trabajo de [Análisis de transmisiones de Azure](https://azure.microsoft.com/services/stream-analytics/).

El script de [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para esta tarea de creación de particiones es ***AggregateFlightInfo.hql***.

#### *MLScoringPipeline*

Esta [canalización](../data-factory/data-factory-create-pipelines.md) contiene varias actividades cuyo resultado final son las predicciones puntuadas a partir del experimento de [Aprendizaje automático de Azure](https://azure.microsoft.com/services/machine-learning/) asociado a esta plantilla de solución.

Las actividades contenidas son:

-   Una actividad de [HDInsightHive](../data-factory/data-factory-hive-activity.md) que utiliza un servicio de [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que ejecuta un script de [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para realizar las agregaciones y la ingeniería de características necesarias para el experimento de [Aprendizaje automático de Azure](https://azure.microsoft.com/services/machine-learning/). El script de [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para esta tarea de creación de particiones es ***PrepareMLInput.hql***.

-   Una actividad de [copia](https://msdn.microsoft.com/library/azure/dn835035.aspx) que mueve los resultados de la actividad [HDInsightHive](../data-factory/data-factory-hive-activity.md) a un solo blob del [Almacenamiento de Azure](https://azure.microsoft.com/services/storage/) al que se puede acceder mediante la actividad [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx).

-   Una actividad [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) que llama al experimento de [Aprendizaje automático de Azure](https://azure.microsoft.com/services/machine-learning/) que hace que los resultados se coloquen en un único blob del [Almacenamiento de Azure](https://azure.microsoft.com/services/storage/).

#### *CopyScoredResultPipeline*

Esta [canalización](../data-factory/data-factory-create-pipelines.md) contiene una sola actividad: una actividad de [copia](https://msdn.microsoft.com/library/azure/dn835035.aspx) que mueve los resultados del experimento de [Aprendizaje automático de Azure](#azure-machine-learning) desde la canalización ***MLScoringPipeline*** a la [Base de datos SQL de Azure](https://azure.microsoft.com/services/sql-database/) que se aprovisionó como parte de la instalación de la plantilla de solución.

### Aprendizaje automático de Azure

El experimento de [Aprendizaje automático de Azure](https://azure.microsoft.com/services/machine-learning/) utilizado para esta plantilla de solución proporciona la vida útil restante (RUL) de un motor de avión. El experimento es específico para el conjunto de datos consumido y, por tanto, requerirá una modificación o reemplazo específicos para los datos que se han traído.

Para obtener más información acerca de cómo se creó el experimento de Aprendizaje automático de Azure, consulte [Predictive Maintenance: Step 1 of 3, data preparation and feature engineering (Mantenimiento predictivo: Paso 1 de 3, preparación de datos e ingeniería de características)](http://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2).

## **Supervisión de progreso**
 Una vez iniciado el Generador de datos, la canalización comienza a hidratarse y los distintos componentes de la solución empiezan a entrar en acción siguiendo los comandos emitidos por la Factoría de datos. Hay dos maneras de supervisar la canalización.

1. Uno de los trabajos de Análisis de transmisiones escribe los datos entrantes sin procesar en Almacenamiento de blobs. Si hace clic en el componente Almacenamiento de blobs de la solución desde la pantalla en la que implementó correctamente la solución y después hace clic en Abrir en el panel derecho, irá al [portal de administración](https://portal.azure.com/). Una vez allí, haga clic en Blobs. En el siguiente panel, verá una lista de contenedores. Haga clic en **maintenancesadata**. En el siguiente panel, verá la carpeta **rawdata**. Dentro de la carpeta rawdata, verá las carpetas con nombres como hour= 17, hour=18, etc. Si ve estas carpetas, significa que los datos sin procesar se están generando en el equipo y almacenando en Almacenamiento de blobs correctamente. Debería ver archivos csv que deben tener tamaños finitos en MB en esas carpetas.

2. El último paso de la canalización es escribir los datos (por ejemplo, predicciones del aprendizaje automático) en Base de datos SQL. Es posible que deba esperar un máximo de tres horas para los datos aparezcan en Base de datos SQL. Una manera de supervisar la cantidad de datos que están disponibles en Base de datos SQL es a través del [Portal de Azure](https://manage.windowsazure.com/). En el panel izquierdo, busque el icono BASES DE DATOS SQL ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-SQL-databases.png) y haga clic en él. Después, busque la base de datos **pmaintenancedb** y haga clic en ella. En la parte inferior de la página siguiente, haga clic en ADMINISTRAR.

	![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-manage.png).

	Aquí, puede hacer clic en Nueva consulta y consultar el número de filas (por ejemplo, select count(*) from PMResult). A medida que la base de datos crece, debe aumentar el número de filas de la tabla.


## **Panel de Power BI**

### Información general

En esta sección se describe cómo configurar el panel de Power BI para visualizar los datos en tiempo real del Análisis de transmisiones de Azure (ruta de acceso activa), así como los resultados de predicción por lotes del Aprendizaje automático de Azure (ruta de acceso inactiva).

### Configuración del panel de ruta de acceso inactiva

En la canalización de datos de la ruta de acceso inactiva, el objetivo fundamental es obtener la predicción de la RUL (vida útil restante) de cada motor del avión cuando finaliza un vuelo (ciclo). El resultado de la predicción se actualiza cada 3 horas para poder predecir la vida útil restante de los motores de los aviones que hayan terminado un vuelo durante las últimas 3 horas.

Power BI se conecta a una Base de datos SQL de Azure como su origen de datos, donde se almacenan los resultados de predicción. Observe que: 1) Una vez implementada la solución, aparecerá una predicción real en la base de datos dentro de 3 horas. El archivo pbix incluido con la descarga del generador, contiene algunos datos de inicialización para que pueda crear el panel de Power BI inmediatamente. 2) En este paso, el requisito previo es descargar e instalar gratuitamente el software [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Los siguientes pasos le guiarán a la hora de conectar el archivo pbix con Base de datos SQL que estaba en marcha en el momento de la implementación de la solución que contiene los datos (*por ejemplo,* resultados de predicción) para su visualización.

1.  Obtenga las credenciales de la base de datos.

    Antes de continuar con los pasos siguientes, necesitará el **nombre del servidor de la base de datos, el nombre de la base de datos, el nombre de usuario y la contraseña**. Estos son los pasos que le ayudarán a encontrarlos.

    -   Una vez que **"Base de datos SQL de Azure"** se torne verde en el diagrama de la plantilla de solución, haga clic sobre este icono y después haga clic en **"Abrir"**.

    -   Verá una nueva pestaña o ventana del explorador que muestra la página del Portal de Azure. Haga clic en **"Grupos de recursos"** en el panel izquierdo.

    -   Seleccione la suscripción que va a utilizar para implementar la solución y después seleccione **"YourSolutionName\_ResourceGroup"**.

    -   En el nuevo panel emergente, haga clic en el icono ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-sql.png) para acceder a la base de datos. El nombre de la base de datos se sitúa junto a este icono (*por ejemplo*, **'pmaintenancedb'**) y el **nombre del servidor de base de datos** aparece bajo la propiedad de nombre de servidor y se parece a **YourSoutionName.database.windows.net**.

	-   El **nombre de usuario** y la **contraseña** de la base de datos son los mismos que los registrados anteriormente durante la implementación de la solución.

2.  Actualice el origen de datos del archivo de informe de la ruta de acceso inactiva con Power BI Desktop.

    -   En la carpeta del equipo donde ha descargado y descomprimido el archivo del generador, haga doble clic en el archivo **PowerBI\\PredictiveMaintenanceAerospace.pbix**. Si ve algún mensaje de advertencia al abrir el archivo, ignórelo. En la parte superior del archivo, haga clic en **"Editar consultas"**.

	    ![](media\cortana-analytics-technical-guide-predictive-maintenance\edit-queries.png)

	-	Verá dos tablas, **RemainingUsefulLife** y **PMResult**. Seleccione la primera tabla y haga clic en ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-query-settings.png) junto a **"Origen"** en **"PASOS APLICADOS"** en el panel de la derecha **"Configuración de la consulta"**. Ignore los mensajes de advertencia que aparezcan.

    -   En la ventana emergente, reemplace **"Servidor"** y **"Base de datos"** por sus propios nombres de servidor y base de datos y después haga clic en **"Aceptar"**. Para el nombre del servidor, asegúrese de especificar el puerto 1433 (**YourSoutionName.database.windows.net, 1433**). Deje el campo Base de datos como **pmaintenancedb**. Ignore los mensajes de advertencia que aparezcan en la pantalla.

    -   En la siguiente ventana emergente, verá dos opciones en el panel izquierdo (**Windows** y **Base de datos**). Haga clic en **"Base de datos"**, escriba su **"Nombre de usuario"** y **"Contraseña"** (que es el nombre de usuario y la contraseña que escribió la primera vez que implementó la solución y creó una Base de datos SQL de Azure). En ***Seleccionar en qué nivel hay que aplicar estos valores***, active la opción de nivel de base de datos. A continuación, haga clic en **"Conectar"**.

    -   Haga clic en la segunda tabla **PMResult**, después haga clic en ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-navigation.png) junto a **"Origen"** en **"PASOS APLICADOS"** en el panel de la derecha **"Configuración de la consulta"**. Luego, actualice los nombres del servidor y de la base de datos como en los pasos anteriores y, por último, haga clic en Aceptar.

    -   Una vez que se le dirija a la página anterior, cierre la ventana. Aparecerá un mensaje. Haga clic en **Aplicar**. Finalmente, haga clic en el botón **Guardar** para guardar los cambios. El archivo de Power BI ha establecido ya la conexión con el servidor. Si las visualizaciones están vacías, asegúrese de borrar todas las selecciones para poder visualizar todos los datos haciendo clic en el icono de borrador de la esquina superior derecha de las leyendas. Utilice el botón Actualizar para reflejar los nuevos datos en las visualizaciones. Inicialmente, solo verá los datos de inicialización en las visualizaciones ya que la Factoría de datos está programada para actualizarse cada 3 horas. Después de 3 horas, verá nuevas predicciones reflejadas en las visualizaciones al actualizar los datos.

3.  (Opcional) Publique el panel de la ruta de acceso en frío en [Power BI en línea](http://www.powerbi.com/). Tenga en cuenta que este paso necesita una cuenta de Power BI (o la cuenta de Office 365).

    -   Haga clic en **"Publicar"** y algunos segundos más tarde aparecerá una ventana con un mensaje que indicará que la publicación en Power BI se ha realizado correctamente y con una marca de verificación verde. Haga clic en el vínculo "Abrir PredictiveMaintenanceAerospace.pbix en Power BI". Para obtener instrucciones detalladas, consulte [Publicar desde Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).

    -   Para crear un nuevo panel: haga clic en el signo **+** junto a la sección **Paneles** del panel izquierdo. Escriba el nombre "Demo de mantenimiento predictivo" para este nuevo panel.

    -   Una vez que abra el informe, haga clic en ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-pin.png) para anclar todas las visualizaciones al panel. Para obtener instrucciones detalladas, consulte [Anclaje de un icono a un panel de Power BI desde un informe](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report). Vaya a la página del panel y ajuste el tamaño y la ubicación de las visualizaciones y edite los títulos. Para obtener instrucciones detalladas sobre cómo editar los iconos, consulte [Edición de un icono: cambiar el tamaño, mover, cambiar el nombre, anclar, eliminar, agregar un hipervínculo](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Este es un panel de ejemplo con algunas visualizaciones de la ruta de acceso inactiva ancladas. Dependiendo de cuánto tiempo tarde en ejecutarse el generador de datos, los números en las visualizaciones pueden ser diferentes. <br/> ![](media\cortana-analytics-technical-guide-predictive-maintenance\final-view.png) <br/>
    -   Para programar la actualización de los datos, mantenga el puntero sobre el conjunto de datos **PredictiveMaintenanceAerospace**, haga clic en ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-elipsis.png) y después elija **Programar actualización**. <br/> **Nota:** Si ve un mensaje de advertencia, haga clic en **Editar credenciales** y asegúrese de que las credenciales de la base de datos son las mismas que las descritas en el paso 1. <br/> ![](media\cortana-analytics-technical-guide-predictive-maintenance\schedule-refresh.png) <br/>
    -   Expanda la sección **Programar actualización**. Active "Mantener los datos actualizados". <br/>
    -   Programe la actualización según sus necesidades. Para obtener más información, consulte [Actualizar datos en Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### Configuración del panel de ruta de acceso activa

Los siguientes pasos le guiarán por los pasos para visualizar la salida de datos en tiempo real de los trabajos de Análisis de transmisiones que se generaron en el momento de la implementación de la solución. Se necesita una cuenta de [Power BI en línea](http://www.powerbi.com/) para realizar los pasos siguientes. Si no tiene una cuenta, puede [crear una](https://powerbi.microsoft.com/pricing).

1.  Agregue una salida de Power BI en el Análisis de transmisiones de Azure (ASA).

    -  Debe seguir las instrucciones descritas en [Análisis de transmisiones y Power BI: panel de análisis en tiempo real de flujo de datos](stream-analytics-power-bi-dashboard.md) para configurar la salida del trabajo de Análisis de transmisiones de Azure como panel de Power BI.
	- La consulta ASA tiene tres salidas, que son **aircraftmonitor**, **aircraftalert** y **flightsbyhour**. Puede ver la consulta haciendo clic en la pestaña de consulta. Deberá agregar a ASA la salida correspondiente a cada una de estas tablas. Cuando se agrega la primera salida (*por ejemplo,* **aircraftmonitor**) asegúrese de que el **Alias de salida**, el **Nombre del conjunto de datos** y el **Nombre de la tabla** son los mismos (**aircraftmonitor**). Repita los pasos para agregar las salidas de **aircraftalert** y **flightsbyhour**. Una vez haya agregado las tres tablas de salida e iniciado el trabajo de ASA, recibirá un mensaje de confirmación (*por ejemplo*, "Se ha iniciado correctamente el trabajo de análisis de transmisiones maintenancesa02asapbi").

2. Inicie sesión en [Power BI en línea](http://www.powerbi.com).

    -   En el panel izquierdo de la sección Conjuntos de datos en Mi área de trabajo, deberían aparecer los nombres del ***CONJUNTO DE DATOS*** **aircraftmonitor**, **aircraftalert**, y **flightsbyhour**. Se trata de los datos de streaming que se insertan desde Análisis de transmisiones en el paso anterior. El conjunto de datos **flightsbyhour** pueden no aparecer al mismo tiempo que los otros dos conjuntos de datos debido a la naturaleza de la consulta de SQL subyacente. Sin embargo, deben aparecer después de una hora.
    -   Asegúrese de que el panel ***Visualizaciones*** está abierto y se muestra en el lado derecho de la pantalla.

3. Una vez que tenga los datos fluyendo en Power BI, puede comenzar a visualizar los datos de streaming. Este es un panel de ejemplo con algunas visualizaciones de la ruta de acceso en caliente ancladas. Puede crear otros iconos de panel basados en conjuntos de datos adecuados. Dependiendo de cuánto tiempo tarde en ejecutarse el generador de datos, los números en las visualizaciones pueden ser diferentes.


    ![](media\cortana-analytics-technical-guide-predictive-maintenance\dashboard-view.png)

4. Estos son algunos pasos para crear uno de los iconos anteriores: "Fleet View of Sensor 11 vs. Threshold 48.26":

    -   Haga clic en el conjunto de datos **aircraftmonitor** en el panel izquierdo de la sección Conjuntos de datos.

    -   Haga clic en el icono **Gráfico de líneas**.

    -   Haga clic en **Procesados** en el panel **Campos** para que aparezca en "Eje" en el panel de **Visualizaciones**.

    -   Haga clic en "s11" y "s11\_alert" para que ambos aparezcan en "Valores". Haga clic en la flecha pequeña situada junto a **s11** y **s11\_alert** y cambie "Suma" a "Media".

    -   Haga clic en **GUARDAR** en la parte superior y asigne el nombre "aircraftmonitor" al informe. El informe denominado "aircraftmonitor" se mostrará en la sección **Informes** del panel **Navegador** de la izquierda.

    -   Haga clic en el icono **Anclar visualización** situado en la esquina superior derecha de este gráfico de líneas. Puede aparecer una ventana llamada "Anclar en el panel" que le permitirá elegir un panel. Seleccione "Demostración de mantenimiento predictivo" y luego haga clic en "Anclar".

    -   Mantenga el mouse sobre este icono en el panel, haga clic en el icono "Editar" en la esquina superior derecha para cambiar el título a "Fleet View of Sensor 11 vs. Threshold 48.26" y el subtítulo a "Average across fleet over time".

## **Eliminación de la solución**
Asegúrese de detener el generador de datos cuando no se use activamente la solución, ya que de lo contrario incurrirá en costos elevados. Elimine la solución si no lo está usando. Al eliminar la solución se eliminarán todos los componentes aprovisionados en su suscripción al implementar la solución. Para eliminar la solución, haga clic en el nombre de la solución en el panel izquierdo de la plantilla de soluciones y haga clic en Eliminar.

## **Herramientas de estimación de costos**

Las dos herramientas siguientes están disponibles para ayudarle a comprender mejor los costos totales implicados en la ejecución del mantenimiento predictivo para la plantilla de solución aeroespacial en su suscripción:

-   [Herramienta de estimación de costos de Microsoft Azure (en línea)](https://azure.microsoft.com/pricing/calculator/)

-   [Herramienta de estimación de costos de Microsoft Azure (escritorio)](http://www.microsoft.com/download/details.aspx?id=43376)

<!---HONumber=AcomDC_0224_2016-->