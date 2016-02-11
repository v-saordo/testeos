<properties 
	pageTitle="Desplazamiento y procesamiento de archivos de registro mediante la factoría de datos de Azure (Portal de Azure clásico)" 
	description="En este tutorial avanzado se describe un escenario casi real y se implementa el escenario con el servicio Factoría de datos de Azure y Editor de Factoría de datos en el Portal de Azure clásico." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/12/2015" 
	ms.author="spelluru"/>

# Tutorial: Medición de la eficacia de una campaña de marketing  
Contoso es una empresa de juegos que crea juegos para varias plataformas: consolas de juegos, dispositivos portátiles y PC. Estos juegos generan muchos registros y el objetivo de Contoso es recopilar y analizar estos registros para obtener información sobre las preferencias del cliente, los datos demográficos, el comportamiento de uso, etc. para identificar oportunidades de venta mejorada y venta cruzada, desarrollar características nuevas y atractivas para impulsar el crecimiento empresarial y ofrecer una experiencia mejor a los clientes.

En este tutorial, creará canalizaciones de Factoría de datos para evaluar la eficacia de una campaña de marketing que Contoso ha lanzado recientemente; para ello, se recopilan registros de ejemplo, se procesan y se enriquecen estos registros con datos de referencia y se transforman los datos. El tutorial incluye las tres canalizaciones siguientes:

1.	**PartitionGameLogsPipeline** lee los eventos de juegos sin procesar de un almacenamiento de blobs y crea particiones basadas en el año, el mes y el día.
2.	**EnrichGameLogsPipeline** combina eventos de juegos con particiones con datos de referencia de código geográfico y enriquece los datos mediante la asignación de direcciones IP a las ubicaciones geográficas correspondientes.
3.	La canalización **AnalyzeMarketingCampaignPipeline** aprovecha los datos enriquecidos y los procesa con los datos de publicidad para crear el resultado final que contiene la eficacia de la campaña de marketing.

## Preparación para el tutorial
1.	Lea [Introducción a Factoría de datos de Azure][adfintroduction] para obtener información general sobre Factoría de datos de Azure y conocer los conceptos de nivel superior.
2.	Debe tener una suscripción de Azure para realizar este tutorial. Para obtener información acerca de cómo obtener una suscripción, consulte [Opciones de compra](https://azure.microsoft.com/pricing/purchase-options/), [Ofertas para miembros](https://azure.microsoft.com/pricing/member-offers/) o [Prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).
3.	Debe descargar e instalar [Azure PowerShell][download-azure-powershell] en el equipo. Deberá ejecutar cmdlets de Factoría de datos para cargar datos de ejemplo y scripts de Pig y Hive en el almacenamiento de blobs. 
2.	**(recomendado)** Revise y practique el tutorial del artículo [Introducción a Factoría de datos de Azure][adfgetstarted] para obtener un tutorial sencillo para familiarizarse con el portal y los cmdlets.
3.	**(recomendado)** Revise y practique el tutorial del artículo [Uso de Pig y Hive con Factoría de datos de Azure][usepigandhive] para obtener un tutorial sobre cómo crear una canalización para desplazar datos desde un origen de datos local a un almacenamiento de blobs de Azure.
4.	Descargue los archivos [ ADFWalkthrough][adfwalkthrough-download] en la carpeta **C:\\ADFWalkthrough** y **conserve la estructura de carpetas**:
	- **Pipelines:** incluye archivos JSON que contienen la definición de las canalizaciones.
	- **Tables:** incluye archivos JSON que contienen la definición de las tablas.
	- **LinkedServices:** incluye archivos JSON que contienen la definición de su clúster de almacenamiento y proceso (HDInsight). 
	- **Scripts:** incluye los scripts Hive y Pig que se usan para procesar los datos y se invocan desde las canalizaciones.
	- **SampleData:** incluye datos de ejemplo para este tutorial.
	- **OnPremises:** incluye archivos JSON y scripts que se usan para demostrar el acceso a los datos locales.
	- **uploadSampleDataAndScripts.ps1:** este script carga los datos de ejemplo y los scripts en Azure.
5. Asegúrese de haber creado los siguientes recursos de Azure:			
	- Cuenta de almacenamiento de Azure.
	- Base de datos SQL de Azure
	- Clúster de HDInsight de Azure de la versión 3.1 o superior (o use un clúster de HDInsight a petición que el servicio Factoría de datos creará automáticamente).	
7. Una vez creados los recursos de Azure, asegúrese de tener la información necesaria para conectarse a cada uno de estos recursos.
 	- **Cuenta de almacenamiento de Azure**: nombre y clave de la cuenta.  
	- **Base de datos SQL de Azure**: servidor, base de datos, nombre de usuario y contraseña.
	- **Clúster de HDInsight de Azure**: nombre del clúster de HDInsight, nombre de usuario, contraseña y nombre y clave de la cuenta para el almacenamiento de Azure asociado a este clúster. Si quiere usar un clúster de HDInsight a petición en lugar de su propio clúster de HDInsight, omita este paso.  
8. Inicie **Azure PowerShell** y ejecute los comandos siguientes: Mantenga abierto Azure PowerShell. Si lo cierra y vuelve a abrirlo, deberá ejecutar los comandos de nuevo.
	- Ejecute **Add-AzureAccount** y escriba el mismo nombre de usuario y contraseña que usó para iniciar sesión en el Portal de Azure.  
	- Ejecute **Get-AzureSubscription** para ver todas las suscripciones para esta cuenta.
	- Ejecute **Select-AzureSubscription** para seleccionar la suscripción con la que quiere trabajar. Esta suscripción debe ser la misma que usó en el Portal de Azure.	

## Información general
El flujo de trabajo completo se muestra a continuación:

![Tutorial Flujo completo][image-data-factory-tutorial-end-to-end-flow]

1. **PartitionGameLogsPipeline** lee los eventos de juegos sin procesar de un almacenamiento de blobs (RawGameEventsTable) y crea particiones basadas en el año, el mes y el día (PartitionedGameEventsTable).
2. **EnrichGameLogsPipeline** combina eventos de juegos con particiones (tabla PartitionedGameEvents, que es un resultado de PartitionGameLogsPipeline) con código geográfico (RefGetoCodeDictionaryTable) y enriquece los datos asignando una dirección IP a la ubicación geográfica correspondiente (EnrichedGameEventsTable).
3. La canalización **AnalyzeMarketingCampaignPipeline** aprovecha los datos enriquecidos (EnrichedGameEventTable generada por EnrichGameLogsPipeline) y los procesa con los datos de publicidad (RefMarketingCampaignnTable) para crear el resultado final de la eficacia de la campaña de marketing, que se copia en la base de datos SQL de Azure (MarketingCampainEffectivensessSQLTable) y en un almacenamiento de blobs de Azure (MarketingCampaignEffectivenessBlobTable) para su análisis.
    
## Tutorial: Creación, implementación y supervisión de flujos de trabajo
1. [Paso 1: Carga de scripts y datos de ejemplo](#MainStep1). En este paso, cargará todos los datos de ejemplo (incluidos todos los registros y datos de referencia) y los flujos de trabajo ejecutarán los scripts Hive/Pig. Los scripts que ejecuta también crean una base de datos SQL de Azure (denominada MarketingCampaigns), tablas, tipos definidos por el usuario y procedimientos almacenados.
2. [Paso 2: Creación de una factoría de datos de Azure](#MainStep2). En este paso, creará una factoría de datos de Azure denominada LogProcessingFactory.
3. [Paso 3: Creación de servicios vinculados](#MainStep3). En este paso, creará los siguientes servicios vinculados: 
	
	- 	**StorageLinkedService**. Vincula la ubicación de almacenamiento de Azure que contiene eventos de juegos sin procesar, eventos de juegos con particiones, eventos de juegos enriquecidos, información efectiva de campañas de marketing, datos de código geográfico de referencia y datos de campaña de marketing de referencia a LogProcessingFactory   
	- 	**AzureSqlLinkedService**. Vincula una base de datos SQL de Azure que contiene información de eficacia de campaña de marketing. 
	- 	**HDInsightStorageLinkedService**. Vincula un almacenamiento de blobs de Azure asociado al clúster de HDInsight al que hace referencia HDInsightLinkedService. 
	- 	**HDInsightLinkedService**. Vincula un clúster de Azure HDInsight a LogProcessingFactory. Este clúster se usa para realizar el procesamiento pig y hive en los datos. 
 		
4. [Paso 4: Creación de tablas](#MainStep4). En este paso, creará las tablas siguientes:
	
	- **RawGameEventsTable**. Esta tabla especifica la ubicación de los datos de eventos de juego sin procesar en el almacenamiento de blobs de Azure definido por StorageLinkedService (adfwalkthrough/logs/rawgameevents/). 
	- **PartitionedGameEventsTable**. Esta tabla especifica la ubicación de los datos de eventos de juego con particiones en el almacenamiento de blobs de Azure definido por StorageLinkedService (adfwalkthrough/logs/partitionedgameevents/). 
	- **RefGeoCodeDictionaryTable**. Esta tabla especifica la ubicación de los datos de código geográfico de referencia en el almacenamiento de blobs de Azure definido por StorageLinkedService (adfwalkthrough/refdata/refgeocodedictionary/).
	- **RefMarketingCampaignTable**. Esta tabla especifica la ubicación de los datos de campaña de marketing de referencia en el almacenamiento de blobs de Azure definido por StorageLinkedService (adfwalkthrough/refdata/refmarketingcampaign/).
	- **EnrichedGameEventsTable**. Esta tabla especifica la ubicación de los datos de eventos de juego enriquecidos en el almacenamiento de blobs de Azure definido por StorageLinkedService (adfwalkthrough/logs/enrichedgameevents/).
	- **MarketingCampaignEffectivenessSQLTable**. Esta tabla especifica la tabla SQL (MarketingCampaignEffectiveness) en la base de datos SQL de Azure definida por el elemento AzureSqlLinkedService que contiene los datos de eficacia de la campaña de marketing. 
	- **MarketingCampaignEffectivenessBlobTable**. Esta tabla especifica la ubicación de los datos de eficacia de la campaña de marketing en el almacenamiento de blobs de Azure definido por StorageLinkedService (adfwalkthrough/marketingcampaigneffectiveness/). 

	
5. [Paso 5: Creación y programación de canalizaciones](#MainStep5). En este paso, creará los siguientes procesos:
	- **PartitionGameLogsPipeline**. Este proceso lee los eventos de juegos sin procesar de un almacenamiento de blobs (RawGameEventsTable) y crea particiones basadas en el año, el mes y el día (PartitionedGameEventsTable). 


		![Canalización PartitionGamesLogs][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**. Este proceso combina eventos de juegos con particiones (tabla PartitionedGameEvents, que es un resultado de PartitionGameLogsPipeline) con código geográfico (RefGetoCodeDictionaryTable) y enriquece los datos mediante la asignación de una dirección IP a la ubicación geográfica correspondiente (EnrichedGameEventsTable)

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**. Este proceso aprovecha los datos de eventos de juego enriquecidos (EnrichedGameEventTable producido por EnrichGameLogsPipeline) y lo procesa con los datos de publicidad (RefMarketingCampaignnTable) para crear el resultado final de la eficacia de la campaña de marketing, que se copian en la base de datos SQL de Azure (MarketingCampainEffectivensessSQLTable) y un almacenamiento de blobs de Azure (MarketingCampaignEffectivenessBlobTable) para el análisis.


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [Paso 6: Supervisión de las canalizaciones y los segmentos de datos](#MainStep6) En este paso, supervisará los procesos, las tablas y los segmentos de datos mediante el Portal de Azure clásico.

## <a name="MainStep1"></a> Paso 1: Carga de scripts y datos de ejemplo
En este paso, cargará todos los datos de ejemplo (incluidos todos los registros y datos de referencia) y los scripts Hive y Pig, que se invocan mediante los flujos de trabajo. Los scripts que ejecuta también crean una base de datos SQL de Azure llamada **MarketingCampaigns**, tablas, tipos definidos por el usuario y procedimientos almacenados.

Las tablas, los tipos definidos por el usuario y procedimientos almacenados se utilizan al mover los resultados de la eficacia de la campaña de marketing desde el almacenamiento de blobs de Azure para la base de datos SQL de Azure.

1. Abra **uploadSampleDataAndScripts.ps1** desde la carpeta **C:\\ADFWalkthrough** (o la carpeta que contenga los archivos extraídos) en su editor favorito, reemplace el texto resaltado por la información de su clúster y guarde el archivo.


		$storageAccount = <storage account name>
		$storageKey = <storage account key>
		$azuresqlServer = <sql azure server>.database.windows.net
		$azuresqlUser = <sql azure user>@<sql azure server>
		$azuresqlPassword = <sql azure password>

 
	Este script requiere tener instalada en el equipo la utilidad sqlcmd. Si tiene SQL Server instalado, ya la tiene. De lo contrario, [descargue][sqlcmd-install] e instale la utilidad.
	
	Si lo prefiere, puede utilizar los archivos de la carpeta: C:\\ADFWalkthrough\\Scripts cargar los scripts pig y hive y los archivos de ejemplo en el contenedor adfwalkthrough, en el almacenamiento de blobs, y crear la tabla MarketingCampaignEffectiveness en la base de datos SQL de Azure MarketingCamapaigns.
   
2. Confirme que el equipo local tiene acceso a la base de datos SQL de Azure. Para permitir el acceso, use el [Portal de Azure clásico](http://manage.windowsazure.com) o **sp\_set\_firewall\_rule** en la base de datos maestra para crear una regla de firewall para la dirección IP del equipo. Puede tardar hasta cinco minutos que este cambio surta efecto. Consulte [Definición de reglas de firewall para SQL Azure][azure-sql-firewall].
4. En Azure PowerShell, vaya a la ubicación donde ha extraído los ejemplos (por ejemplo, **C:\\ADFWalkthrough**).
5. Ejecute **uploadSampleDataAndScripts.ps1** 
6. Una vez que el script se ejecute correctamente, verá lo siguiente:

		$storageAccount = <storage account name>
		PS C:\ ADFWalkthrough> & '.\uploadSampleDataAndScripts.ps1'

		Name			PublicAccess		LastModified
		-----			--------		------
		ADFWalkthrough		off			6/6/2014 6:53:34 PM +00:00
	
		Uploading sample data and script files to the storage container [adfwalkthrough]

		Container Uri: https://<yourblobstorage>.blob.core.windows.net/adfwalkthrough

		Name                        BlobType   Length   ContentType               LastModified                        
		----                        --------   ------   -----------               ------------                        
		logs/rawgameevents/raw1.csv  BlockBlob  12308   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw2.csv  BlockBlob  16119   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw3.csv  BlockBlob  16062   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw4.csv  BlockBlob  16245   application/octet-stream  6/6/2014 6:54:35 PM 
		refdata/refgeocodedictiona.. BlockBlob  18647   application/octet-stream  6/6/2014 6:54:36 PM 
		refdata/refmarketingcampai.. BlockBlob  8808    application/octet-stream  6/6/2014 6:54:36 PM
		scripts/partitionlogs.hql    BlockBlob  2449    application/octet-stream  6/6/2014 6:54:36 PM 
		scripts/enrichlogs.pig       BlockBlob  1631    application/octet-stream  6/6/2014 6:54:36 PM
		scripts/transformdata.hql    BlockBlob  1753    application/octet-stream  6/6/2014 6:54:36 PM

		6/6/2014 11:54:36 AM Summary
		6/6/2014 11:54:36 AM 1. Uploaded Sample Data Files to blob container.
		6/6/2014 11:54:36 AM 2. Uploaded Sample Script Files to blob container.
		6/6/2014 11:54:36 AM 3. Created ‘MarketingCampaigns’ Azure SQL database and tables.
		6/6/2014 11:54:36 AM You are ready to deploy Linked Services, Tables and Pipelines. 

## <a name="MainStep2"></a> Paso 2: Creación de una factoría de datos de Azure
En este paso, creará una factoría de datos de Azure llamada **LogProcessingFactory**.

1.	Tras iniciar sesión en el [Portal de Azure][azure-portal], haga clic en **NUEVO** en la esquina inferior izquierda, seleccione **Análisis de datos** en la hoja **Crear** y haga clic en **Factoría de datos**, en la hoja **Análisis de datos**. 

	![New->DataFactory][image-data-factory-new-datafactory-menu]

5. En la hoja **Nueva factoría de datos**, escriba **LogProcessingFactory** como **Nombre**.

	![Hoja de Factoría de datos][image-data-factory-tutorial-new-datafactory-blade]

6. Si aún no ha creado un grupo de recursos de Azure denominado**ADF**, haga lo siguiente:
	1. Haga clic en **NOMBRE DEL GRUPO DE RECURSOS** y en **Crear un nuevo grupo de recursos**.
	
		![Hoja Grupos de recursos][image-data-factory-tutorial-resourcegroup-blade]
	2. En la hoja **Crear grupo de recursos**, escriba **ADF** como nombre del grupo de recursos y haga clic en **Aceptar**.
	
		![Crear grupo de recursos][image-data-factory-tutorial-create-resourcegroup]
7. Seleccione **ADF** como **NOMBRE DEL GRUPO DE RECURSOS**.  
8.	En la hoja **Nueva factoría de datos**, observe que **Agregar al Panel de inicio** está seleccionado de forma predeterminada. De esta forma agregará un vínculo a la factoría de datos del panel de inicio (lo que verá cuando inicie sesión en el Portal de Azure).

	![Hoja Crear factoría de datos][image-data-factory-tutorial-create-datafactory]

9.	En la hoja **Nueva factoría de datos**, haga clic en **Crear** para crear la factoría de datos.
10.	Después de crear la factoría de datos, debería ver la hoja **FACTORÍA DE DATOS** denominada **LogProcessingFactory**.

	![Página principal de Factoría de datos][image-data-factory-tutorial-datafactory-homepage]

	
	Si no la ve, realice una de las acciones siguientes:

	- Haga clic en **LogProcessingFactory** en el **Panel de inicio** (página principal).
	- Haga clic en **EXAMINAR** en el lado izquierdo, haga clic en **Todo**, elija **Factorías de datos** y seleccione la factoría de datos.
 
	El nombre del generador de datos de Azure debe ser único global. Si recibe el error: **El nombre de la factoría de datos "LogProcessingFactory" no está disponible**, cambie el nombre (por ejemplo, yournameLogProcessingFactory). Use este nombre en lugar de LogProcessingFactory mientras sigue los pasos de este tutorial.
 
## <a name="MainStep3"></a> Paso 3: Creación de servicios vinculados

> [AZURE.NOTE] En este artículo se usa el Portal de Azure clásico, concretamente el Editor de la Factoría de datos, para crear servicios vinculados, tablas y canalizaciones. Consulte el [tutorial Uso de Azure PowerShell][adftutorial-using-powershell] si desea realizar este tutorial con Azure PowerShell.

En este paso, creará los siguientes servicios vinculados:

- StorageLinkedService
- AzureSqlLinkedService
- HDInsightStorageLinkedService
- HDInsightLinkedService. 

### Creación de StorageLinkedService y HDInsightStorageLinkedService

1.	En la hoja **FACTORÍA DE DATOS**, haga clic en la ventana **Crear e implementar** para iniciar el **Editor** para la factoría de datos.

	![Mosaico Crear e implementar][image-author-deploy-tile]

2.  En el **Editor**, haga clic en el botón **Nuevo almacén de datos** de la barra de herramientas y seleccione **Almacenamiento de Azure** en el menú desplegable. Debería ver la plantilla JSON para crear un servicio vinculado de almacenamiento de Azure en el panel derecho.
	
	![Botón Nuevo almacén de datos del Editor][image-editor-newdatastore-button]

3. Reemplace **accountname** y **accountkey** por los valores de clave de cuenta y nombre de cuenta para la cuenta de almacenamiento de Azure.

	![JSON de almacenamiento de blobs del Editor][image-editor-blob-storage-json]
	
	Para obtener más información sobre las propiedades JSON, vea [Referencia de scripting JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

4. Haga clic en **Implementar** en la barra de herramientas para implementar StorageLinkedService. Confirme que aparece el mensaje **SERVICIO VINCULADO CREADO CORRECTAMENTE** en la barra de título.

	![Implementación de almacenamiento de blobs del Editor][image-editor-blob-storage-deploy]

5. Repita los pasos para crear otro servicio vinculado de Almacenamiento de Azure denominado: **HDInsightStorageLinkedService** para el almacenamiento asociado a su clúster de HDInsight. En el script JSON del servicio vinculado, cambie el valor de la propiedad **name** a **HDInsightStorageLinkedService**.

### Creación de AzureSqlLinkedService
1. En el **Editor de Factoría de datos**, haga clic en el botón **Nuevo almacén de datos** de la barra de herramientas y seleccione **Base de datos SQL de Azure** en el menú desplegable. Verá la plantilla JSON para crear un servicio vinculado SQL de Azure en el panel derecho.
2. Reemplace **servername**, ****username@servername** y **password** por los nombres de su servidor SQL de Azure, cuenta de usuario y contraseña.
3. Reemplace **databasename** por **MarketingCampaigns**. Se trata de la base de datos SQL de Azure creada por los scripts que ejecutó en el paso 1. Debe confirmar que los scripts crearon realmente esta base de datos (si se producen errores). 
3. Haga clic en **Implementar** en la barra de herramientas para crear e implementar AzureSqlLinkedService.

### Creación de HDInsightLinkedService
El servicio Factoría de datos de Azure admite la creación de un clúster a petición y usarlo para procesar la entrada para generar datos de salida. También puede utilizar su propio clúster para realizar la misma tarea. Cuando se utiliza el clúster de HDInsight a petición, se crea un clúster para cada sector. Mientras que, si utiliza su propio clúster de HDInsight, el clúster está preparado para procesar el sector inmediatamente. Por lo tanto, cuando utilice el clúster a petición, es posible que no vea los datos de salida tan rápido como cuando utilice su propio clúster. Para los fines de este ejemplo, vamos a usar un clúster a petición.

#### Para utilizar un clúster de HDInsight a petición
1. Haga clic en **Nuevo proceso** en la barra de comandos y seleccione **Clúster de HDInsight a petición** en el menú.
2. En el script JSON, haga lo siguiente: 
	1. En la propiedad **clusterSize**, especifique el tamaño del clúster de HDInsight.
	3. En la propiedad **timeToLive**, especifique cuánto tiempo el clúster puede estar inactivo antes de que se elimine. 
	4. En la propiedad **version**, especifique la versión de HDInsight que quiere usar. Si excluye esta propiedad, se usa la versión más reciente.  
	5. En **linkedServiceName**, especifique el elemento **StorageLinkedService** que creó en el tutorial Introducción. 

			{
			    "name": "HDInsightOnDemandLinkedService",
			    "properties": {
			        "type": "HDInsightOnDemand",
			        "description": "",
			        "typeProperties": {
			            "clusterSize": "4",
			            "timeToLive": "00:30:00",
			            "version": "3.2",
			            "linkedServiceName": "StorageLinkedService"
			        }
			    }
			}

		Tenga en cuenta que el **tipo** de servicio vinculado se establece en **HDInsightOnDemand**.

2. Haga clic en **Implementar** en la barra de comandos para implementar el servicio vinculado.
   
   
#### Para utilizar su propio clúster de HDInsight: 

1. Haga clic en **Nuevo proceso** en la barra de comandos y seleccione **Clúster de HDInsight** en el menú.
2. En el script JSON, haga lo siguiente: 
	1. En la propiedad **clusterUri**, especifique la dirección URL para su HDInsight. Por ejemplo, https://<clustername>.azurehdinsight.net/.     
	2. En la propiedad **UserName**, escriba el nombre del usuario que tiene acceso al clúster de HDInsight.
	3. En la propiedad **Password**, especifique la contraseña del usuario. 
	4. En la propiedad **LinkedServiceName**, escriba **StorageLinkedService**. Este es el servicio vinculado que creó en el tutorial Introducción. 

	Tenga en cuenta que el **tipo** de servicio vinculado se establece en **HDInsightBYOCLinkedService** (BYOC significa Bring Your Own Cluster).

2. Haga clic en **Implementar** en la barra de comandos para implementar el servicio vinculado.


## <a name="MainStep4"></a> Paso 4: Creación de tablas
 
En este paso creará las tablas de Factoría de datos siguientes:

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Tutorial Flujo completo][image-data-factory-tutorial-end-to-end-flow]
 
La imagen anterior muestra los procesos en la fila central y las tablas de las filas superior e inferior.

### Para crear las tablas
	
1. En el **Editor** de Factoría de datos, haga clic en el botón **Nuevo conjunto de datos** en la barra de herramientas y haga clic en **Almacenamiento de blobs de Azure** en el menú desplegable. 
2. Reemplace el script JSON del panel derecho por el script JSON del archivo **RawGameEventsTable.json** en la carpeta **C:\\ADFWalkthrough\\Tables**.
3. Haga clic en **Implementar** en la barra de herramientas para crear e implementar la canalización. Confirme que aparece el mensaje **TABLA CREADA CORRECTAMENTE** en la barra de título del Editor.
4. Repita los pasos del 1 al 3 con el contenido de los archivos siguientes: 
	1. PartitionedGameEventsTable.json
	2. RefGeoCodeDictionaryTable.json
	3. RefMarketingCampaignTable.json
	4. EnrichedGameEventsTable.json
	5. MarketingCampaignEffectivenessBlobTable.json 
5. Repita los pasos del 1 al 3 con el contenido del archivo siguiente: PERO seleccione **SQL Azure** después de hacer clic en **Nuevo conjunto de datos**.
	1. MarketingCampaignEffectivenessSQLTable.json
	

## <a name="MainStep5"></a> Paso 5: Creación y programación de canalizaciones
En este paso, creará los siguientes procesos:

- PartitionGameLogsPipeline
- EnrichGameLogsPipeline
- AnalyzeMarketingCampaignPipeline

### Creación de canalizaciones

1. En el **Editor de la Factoría de datos**, haga clic en **Nueva canalización** en la barra de herramientas. Haga clic en **... (puntos suspensivos)** en la barra de herramientas si no ve el botón. O bien, haga clic con el botón secundario **Canalizaciones** en la vista de árbol y elija **Nueva canalización**.
2. Reemplace el script JSON del panel derecho por el script JSON del archivo **PartitionGameLogsPipeline.json** en la carpeta **C:\\ADFWalkthrough\\Pipelines**.
3. Agregue una **coma (',')** al final del **corchete de cierre ('] ')** en el archivo JSON y, a continuación, agregue las tres líneas siguientes después del corchete de cierre. 

        "start": "2014-05-01T00:00:00Z",
        "end": "2014-05-05T00:00:00Z",
        "isPaused": false

	Tenga en cuenta que las horas de inicio y finalización están establecidas en 01/05/2014 y 05/05/2014, dado que los datos de ejemplo de este tutorial van del 01/05/2014 al 05/05/2014.
 	
	Si está utilizando el servicio vinculado de HDInsight a petición, establezca la propiedad **linkedServiceName** en **HDInsightOnDemandLinkedService**.
3. Haga clic en **Implementar** en la barra de herramientas para implementar la canalización. Confirme que aparece el mensaje **LA CANALIZACIÓN SE HA CREADO CORRECTAMENTE** en la barra de título del Editor.
4. Repita los pasos del 1 al 3 con el contenido de los archivos siguientes: 
	1. EnrichGameLogsPipeline.json
	2. AnalyzeMarketingCampaignPipeline.json
4. Cierre las hojas de la Factoría de datos presionando **X** (esquina superior derecha) para ver la página principal (hoja **FACTORÍA DE DATOS**) de la Factoría de datos.

### Vista de diagrama

1. En la hoja **FACTORÍA DE DATOS** para **LogProcessingFactory**, haga clic en **Diagrama**. 

	![Vínculo de diagrama][image-data-factory-tutorial-diagram-link]

2. Puede reorganizar el diagrama que ve; aquí se muestra un diagrama reorganizado en el que se muestran entradas directas en la parte superior y las salidas en la parte inferior. Puede ver que la salida de **PartitionGameLogsPipeline** se pasa como entrada a EnrichGameLogsPipeline y la salida de **EnrichGameLogsPipeline** se pasa a **AnalyzeMarketingCampaignPipeline**. Haga doble clic en un título para ver detalles sobre el artefacto que representa la hoja.

	![Vista de diagrama][image-data-factory-tutorial-diagram-view]

3. Haga clic en **AnalyzeMarketingCampaignPipeline** y en **Abrir canalización**. Debería ver las actividades en la canalización, junto con los conjuntos de datos de entrada y salida para las actividades.
 
	![Abrir canalización](./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline-OpenPipeline.png)

	Haga clic en **Factoría de datos** en la ruta de exploración de la esquina superior izquierda para volver a la vista de diagrama con todas las canalizaciones.


**¡Enhorabuena!** Ha creado la factoría de datos de Azure, servicios vinculados, procesos y tablas, y ha iniciado el flujo de trabajo.


## <a name="MainStep6"></a> Paso 6: Supervisión de las canalizaciones y los segmentos de datos 

1.	Si no tiene la hoja **FACTORÍA DE DATOS** para el **LogProcessingFactory** abierto, puede hacer lo siguiente:
	1.	Haga clic en **LogProcessingFactory** en el **Panel de inicio**. Al crear la factoría de datos, se selecciona automáticamente la opción **Agregar al Panel de inicio**.

		![Supervisión del Panel de Inicio][image-data-factory-monitoring-startboard]

	2. Haga clic en **EXAMINAR**, en la hoja **Examinar**, seleccione **Factorías de datos** y, en la hoja **Factorías de datos**, seleccione **LogProcessingFactory**.

		![Supervisión de Examinar factorías de datos][image-data-factory-monitoring-browse-datafactories]
2. Puede supervisar su factoría de datos de varias maneras. Puede iniciar con procesos o conjuntos de datos. Vamos a comenzar con los procesos e iremos profundizando. 
3.	Haga clic en **Canalizaciones** en la hoja **FACTORÍA DE DATOS**. 
4.	Haga clic en **PartitionGameLogsPipeline** en la hoja Canalizaciones. 
5.	En la hoja **CANALIZACIÓN** para **PartitionGameLogsPipeline**, verá que la canalización consume el conjunto de datos **RawGameEventsTable**. Haga clic en **RawGameEventsTable**.

	![Canalización consumida y producida][image-data-factory-monitoring-pipeline-consumed-produced]

6. En la hoja TABLA de **RawGameEventsTable**, verá todos los segmentos. En la siguiente captura de pantalla, todos los segmentos están en estado **Listo** y no hay segmentos con problemas. Significa que los datos están listos para ser procesados.

	![Hoja TABLA RawGameEventsTable][image-data-factory-monitoring-raw-game-events-table]

	Las listas **Segmentos actualizados recientemente** y **Segmentos erróneos recientes** se ordenan por la **HORA DE LA ÚLTIMA ACTUALIZACIÓN**. En las situaciones siguientes, se cambia la hora de actualización de un segmento.

	-  El estado del segmento se actualiza manualmente, por ejemplo, con **Set-AzureRmDataFactorySliceStatus** (o) al hacer clic en **EJECUTAR** en la hoja **SEGMENTO** para el segmento.
	-  El segmento cambia de estado debido a una ejecución (por ejemplo, una ejecución se inició, una ejecución finalizó y produjo un error, una ejecución finalizó correctamente, etc.).
 
	Haga clic en el título de las listas o **... (puntos suspensivos)** para ver la lista más amplia de segmentos. Haga clic en **Filtro** en la barra de herramientas para filtrar los segmentos.
	
	Para ver los segmentos de datos ordenados por las horas de inicio y finalización, haga clic en la ventana **Segmentos de datos (por hora del segmento)**.

	![Segmentos de datos por intervalo de tiempo][DataSlicesBySliceTime]
 
7. Ahora, en la hoja **CANALIZACIÓN** para **PartiionGameLogsPipeline**, haga clic en **Producida**.
8. Debería ver la lista de conjuntos de datos que genera este proceso: 
9. Haga clic en la tabla **PartitionedGameEvents** en la hoja **Conjuntos de datos producidos**. 
10.	Confirme que el **estado** de todos los segmentos se establece en **Listo**. 
11.	Haga clic en uno de los segmentos que está **Listo** para ver la hoja **SEGMENTO DE DATOS** para ese segmento.

	![Hoja SEGMENTO DE DATOS RawGameEventsTable][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Si se produjo un error, verá el estado **Error** aquí. Puede que también vea los dos segmentos con el estado **Listo** o con el estado **Validación pendiente**, según la rapidez con la que se procesen.

	Si el segmento no está en el estado **Listo**, puede ver los segmentos ascendentes que no están en estado Listo y bloquean la ejecución del segmento actual en la lista **Segmentos ascendentes que no están listos**.
 
	Consulte [Referencia para desarrolladores de Factoría de datos de Azure][developer-reference] para obtener una descripción de todos los estados posibles de los segmentos.

12.	En la hoja **SEGMENTO DE DATOS**, haga clic en la ejecución de la lista **Ejecuciones de actividades**. Debería ver la hoja Ejecución de actividad para ese segmento. Debería ver la siguiente hoja **DETALLES DE EJECUCIÓN DE ACTIVIDAD**.

	![Hoja Detalles de ejecución de actividad][image-data-factory-monitoring-activity-run-details]

13.	Haga clic en **Descargar** para descargar los archivos. Esta pantalla es especialmente útil al solucionar errores de procesamiento de HDInsight.
	 
	
Cuando se complete la ejecución de toda la canalización, puede buscar en **MarketingCampaignEffectivenessTable** en Base de datos SQL de Azure **MarketingCampaigns** para ver los resultados.

**¡Enhorabuena!** Ahora puede supervisar y solucionar problemas de los flujos de trabajo. Ha aprendido a utilizar la factoría de datos de Azure para procesar los datos y obtener análisis.

## Ampliación del tutorial para usar datos locales
En el último paso del escenario de procesamiento de registro del tutorial de este artículo, la salida de la eficacia de la campaña de marketing se copió en una Base de datos SQL de Azure. También puede mover estos datos a un SQL Server local para realizar análisis dentro de su organización.
 
Para copiar los datos de eficacia de la campaña de marketing desde Blob de Azure a un SQL Server local, debe crear el servicio vinculado local, la tabla y la canalización que se presentan en el tutorial de este artículo.

Practique el [tutorial Uso de orígenes de datos locales][tutorial-onpremises] para aprender a crear una canalización para copiar datos de eficacia de campaña de marketing en una base de datos de SQL Server local.


[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[adfsamples]: data-factory-samples.md
[adfgetstarted]: data-factory-get-started.md
[adftutorial-using-powershell]: data-factory-tutorial-using-powershell.md
[adfintroduction]: data-factory-introduction.md
[usepigandhive]: data-factory-data-transformation-activities.md
[tutorial-onpremises]: data-factory-tutorial-extend-onpremises.md
[download-azure-powershell]: ../powershell-install-configure.md

[azure-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: https://azure.microsoft.com/documentation/articles/sql-database-configure-firewall-settings/


[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[DataSlicesBySliceTime]: ./media/data-factory-tutorial/DataSlicesBySliceTime.png
[image-author-deploy-tile]: ./media/data-factory-tutorial/author-deploy-tile.png
[image-editor-newdatastore-button]: ./media/data-factory-tutorial/editor-newdatastore-button.png
[image-editor-blob-storage-json]: ./media/data-factory-tutorial/editor-blob-storage-json.png
[image-editor-blob-storage-deploy]: ./media/data-factory-tutorial/editor-blob-storage-deploy.png

[image-data-factory-tutorial-end-to-end-flow]: ./media/data-factory-tutorial/EndToEndWorkflow.png

[image-data-factory-tutorial-partition-game-logs-pipeline]: ./media/data-factory-tutorial/PartitionGameLogsPipeline.png

[image-data-factory-tutorial-enrich-game-logs-pipeline]: ./media/data-factory-tutorial/EnrichGameLogsPipeline.png

[image-data-factory-tutorial-analyze-marketing-campaign-pipeline]: ./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline.png


[image-data-factory-tutorial-new-datafactory-blade]: ./media/data-factory-tutorial/NewDataFactoryBlade.png

[image-data-factory-tutorial-resourcegroup-blade]: ./media/data-factory-tutorial/ResourceGroupBlade.png

[image-data-factory-tutorial-create-resourcegroup]: ./media/data-factory-tutorial/CreateResourceGroup.png

[image-data-factory-tutorial-datafactory-homepage]: ./media/data-factory-tutorial/DataFactoryHomePage.png

[image-data-factory-tutorial-create-datafactory]: ./media/data-factory-tutorial/CreateDataFactory.png

[image-data-factory-tutorial-diagram-link]: ./media/data-factory-tutorial/DataFactoryDiagramLink.png

[image-data-factory-tutorial-diagram-view]: ./media/data-factory-tutorial/DiagramView.png

[image-data-factory-monitoring-startboard]: ./media/data-factory-tutorial/MonitoringStartBoard.png

[image-data-factory-monitoring-browse-datafactories]: ./media/data-factory-tutorial/MonitoringBrowseDataFactories.png

[image-data-factory-monitoring-pipeline-consumed-produced]: ./media/data-factory-tutorial/MonitoringPipelineConsumedProduced.png

[image-data-factory-monitoring-raw-game-events-table]: ./media/data-factory-tutorial/MonitoringRawGameEventsTable.png

[image-data-factory-monitoring-raw-game-events-table-dataslice-blade]: ./media/data-factory-tutorial/MonitoringPartitionGameEventsTableDataSliceBlade.png

[image-data-factory-monitoring-activity-run-details]: ./media/data-factory-tutorial/MonitoringActivityRunDetails.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial/NewDataFactoryMenu.png

<!---HONumber=AcomDC_0128_2016-->