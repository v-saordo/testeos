<properties 
	pageTitle="Desplazamiento y procesamiento de archivos de registro mediante la factoría de datos de Azure (Azure PowerShell)" 
	description="En este tutorial avanzado se describe un escenario casi real y se implementa el escenario con el servicio Factoría de datos de Azure y Azure PowerShell." 
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
	ms.date="10/29/2015" 
	ms.author="spelluru"/>

# Tutorial: mover y procesar archivos de registro con Factoría de datos [PowerShell]
En este artículo se ofrece un completo tutorial de un escenario canónico de procesamiento de registros utilizando la factoría de datos de Azure para transformar los datos de archivos de registro en perspectivas.

## Escenario
Contoso es una empresa de juegos que crea juegos para varias plataformas: consolas de juegos, dispositivos portátiles y PC. Cada uno de estos juegos produce miles de registros. El objetivo de Contoso es recopilar y analizar los registros generados por estos juegos para obtener información de uso, identificar las oportunidades de venta y venta cruzada, desarrollar nuevas características atractivas, etc. para mejorar el negocio y ofrecer la mejor experiencia a los clientes.
 
En este tutorial, recopilamos registros de ejemplo, los procesamos y enriquecemos con datos de referencia, y transformamos los datos para evaluar la eficacia de una campaña de marketing que Contoso ha lanzado recientemente.

## Preparación para el tutorial
1.	Lea [Introducción a Factoría de datos de Azure][adfintroduction] para obtener información general sobre Factoría de datos de Azure y conocer los conceptos de nivel superior.
2.	Debe tener una suscripción de Azure para realizar este tutorial. Para obtener información acerca de cómo obtener una suscripción, consulte [Opciones de compra][azure-purchase-options], [Ofertas para miembros][azure-member-offers] o [Prueba gratuita][azure-free-trial].
3.	Debe descargar e instalar [Azure PowerShell][download-azure-powershell] en el equipo.

	Este artículo no abarca todos los cmdlets de Factoría de datos. Vea [Referencia de cmdlets de factoría de datos](https://msdn.microsoft.com/library/dn820234.aspx) para obtener la documentación completa sobre los cmdlets de la factoría de datos.
    
	Si usa Azure PowerShell de una **versión inferior a 1.0**, deberá usar los cmdlets que se documentan [aquí][old-cmdlet-reference]. También debe ejecutar los comandos siguientes antes de usar los cmdlets de Factoría de datos:

	1. Ejecute **Add-AzureAccount** y escriba el mismo nombre de usuario y la contraseña que usó para iniciar sesión en el Portal de Azure.
	2. Ejecute **Get-AzureSubscription** para ver todas las suscripciones para esta cuenta.
	3. Ejecute **Select-AzureSubscription** para seleccionar la suscripción con la que quiere trabajar. Esta suscripción debe ser la misma que la usada en el Portal de Azure.
	
	Mantenga Azure PowerShell abierto hasta el final de este tutorial. Si lo cierra y vuelve a abrirlo, deberá ejecutar los comandos de nuevo.

2. Cambie al modo AzureResourceManager a medida que los cmdlets de Factoría de datos de Azure están disponibles en este modo: **Switch-AzureMode AzureResourceManager**.
 
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
	- Ejecute **Add-AzureRmAccount** y escriba el mismo nombre de usuario y contraseña que usó para iniciar sesión en el Portal de Azure.  
	- Ejecute **Get-AzureSubscription** para ver todas las suscripciones para esta cuenta.
	- Ejecute **Select-AzureSubscription** para seleccionar la suscripción con la que quiere trabajar. Esta suscripción debe ser la misma que usó en el Portal de Azure.
	

## Información general
El flujo de trabajo completo se muestra a continuación: ![Tutorial Flujo completo][image-data-factory-tutorial-end-to-end-flow]

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

1. Cambie a **Azure PowerShell** si ya lo tiene abierto (o) inicie **Azure PowerShell**. Si había cerrado y vuelto a abrir Azure, debe ejecutar los siguientes comandos: 
	- Ejecute **Add-AzureRmAccount** y escriba el mismo nombre de usuario y contraseña que usó para iniciar sesión en el Portal de Azure.  
	- Ejecute **Get-AzureSubscription** para ver todas las suscripciones para esta cuenta.
	- Ejecute **Select-AzureSubscription** para seleccionar la suscripción con la que quiere trabajar. Esta suscripción debe ser la misma que usó en el Portal de Azure. 

2. Cree un grupo de recursos de Azure con el nombre: **ADFTutorialResourceGroup** (si no lo ha creado todavía) ejecutando el siguiente comando.

		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	En algunos de los pasos de este tutorial se supone que se usa el grupo de recursos denominado ADFTutorialResourceGroup. Si usa un grupo de recursos diferentes, deberá usarlo en lugar de ADFTutorialResourceGroup en este tutorial.
4. Ejecute el cmdlet **New-AzureRmDataFactory** para crear una factoría de datos con el nombre DataFactoryMyFirstPipelinePSH.  

		New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name LogProcessingFactory –Location "West US"

	> [AZURE.IMPORTANT]El nombre de la Factoría de datos de Azure debe ser único de forma global. Si recibe el error **El nombre de la factoría de datos "LogProcessingFactory" no está disponible**, cambie el nombre (por ejemplo, yournameLogProcessingFactory). Use este nombre en lugar de LogProcessingFactory mientras sigue los pasos de este tutorial. Consulte el tema [Factoría de datos: reglas de nomenclatura](data-factory-naming-rules.md) para las reglas de nomenclatura para los artefactos de Factoría de datos.
	> 
	> El nombre de la factoría de datos se puede registrar como un nombre DNS en el futuro y, por lo tanto, hacerse públicamente visible.

 
## <a name="MainStep3"></a> Paso 3: Creación de servicios vinculados

> [AZURE.NOTE]En este artículo se usa Azure PowerShell para crear servicios vinculados, tablas y canalizaciones. Consulte el [tutorial Uso del Editor de Factoría de datos][adftutorial-using-editor] si quiere realizar este tutorial con el Portal de Azure, concretamente el Editor de Factoría de datos.

En este paso, creará los servicios vinculados siguientes: StorageLinkedService, AzureSqlLinkedService, HDInsightStorageLinkedService y HDInsightLinkedService.

16. En Azure PowerShell, vaya a la subcarpeta **LinkedServices** en **C:\\ADFWalkthrough** (o) desde la carpeta de la ubicación donde extrajo los archivos.
17. Utilice el siguiente comando para establecer la variable $df en el nombre de la factoría de datos.

		$df = “LogProcessingFactory”
17. Abra **StorageLinkedService.json** en su editor favorito, escriba los valores del **nombre de la cuenta** y de la **clave de cuenta**, y guarde el archivo.
17. Use el cmdlet **New-AzureRmDataFactoryLinkedService** para crear un servicio vinculado de la manera siguiente. 

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\StorageLinkedService.json
	
18. Abra **StorageLinkedService.json** en su editor favorito, escriba los valores del **nombre de la cuenta** y de la **clave de cuenta**, y guarde el archivo.
19. Cree **HDInsightStorageLinkedService**.

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightStorageLinkedService.json
 
19. Abra **AzureSqlLinkedService.json** en su editor favorito, escriba el nombre del **servidor sql de azure**, del **nombre de usuario** y de la **contraseña**, y guarde el archivo.
19. Use el cmdlet **New-AzureRmDataFactoryLinkedService** para crear un servicio vinculado de la manera siguiente. 

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\AzureSqlLinkedService.json
19. Abra **HDInsightLinkedService.json** en su editor favorito y observe que el tipo está establecido en **HDInsightOnDemandLinkedService**.

	El servicio Factoría de datos de Azure admite la creación de un clúster a petición y usarlo para procesar la entrada para generar datos de salida. También puede utilizar su propio clúster para realizar la misma tarea. Cuando se utiliza el clúster de HDInsight a petición, se crea un clúster para cada sector. Mientras que, si utiliza su propio clúster de HDInsight, el clúster está preparado para procesar el sector inmediatamente. Por lo tanto, cuando utilice el clúster a petición, es posible que no vea los datos de salida tan rápido como cuando utilice su propio clúster. Para los fines de este ejemplo, vamos a usar un clúster a petición.
	
	HDInsightLinkedService vincula un clúster de HDInsight a petición con la factoría de datos. Para usar su propio clúster de HDInsight, actualice la sección Propiedades del archivo HDInsightLinkedService.json tal como se muestra a continuación (reemplace el nombre de clúster, el usuario y la contraseña por los valores adecuados):
	
		"Properties": 
		{
    		"Type": "HDInsightBYOCLinkedService",
        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
	    	"UserName": "<username>",
	    	"Password": "<password>",
	    	"LinkedServiceName": "HDInsightStorageLinkedService"
		}
		


19. Use el cmdlet **New-AzureRmDataFactoryLinkedService** para crear un servicio vinculado de la manera siguiente. Comience con la cuenta de almacenamiento:

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
	Si está utilizando un nombre diferente para ResourceGroupName, DataFactoryName o LinkedService, indíquelo en el cmdlet anterior. Además, proporcione la ruta de acceso completa del archivo JSON del servicio vinculado si no se encuentra el archivo.

 

## <a name="MainStep4"></a> Paso 4: Creación de tablas 
En este paso, creará las tablas siguientes:

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Tutorial Flujo completo][image-data-factory-tutorial-end-to-end-flow]
 
La imagen anterior muestra los procesos en la fila central y las tablas de las filas superior e inferior.

El Portal de Azure clásico no admite crear conjuntos de datos y tablas, por lo que deberá usar Azure PowerShell para crear tablas en esta versión.

### Para crear las tablas

1.	En Azure PowerShell, vaya a la carpeta **Tablas** (**C:\\ADFWalkthrough\\Tables**) desde la ubicación donde extrajo los ejemplos.
2.	Use el cmdlet **New-AzureRmDataFactoryDataset** para crear los conjuntos de datos para **RawGameEventsTable**.json de la manera siguiente.	


		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\RawGameEventsTable.json

	Si está utilizando otro nombre para ResourceGroupName y DataFactoryName, indíquelo en el cmdlet anterior. Además, proporcione la ruta de acceso completa del archivo JSON de la tabla si no se encuentra el archivo mediante el cmdlet.

3. Repita el paso anterior para crear las tablas siguientes:
		
		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionedGameEventsTable.json
		
		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\RefGeoCodeDictionaryTable.json
			
		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\RefMarketingCampaignTable.json
			
		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichedGameEventsTable.json
			
		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessSQLTable.json
			
		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessBlobTable.json



4. En el **Portal de Azure**, haga clic en **Conjuntos de datos** en la hoja **FACTORÍA DE DATOS** para **LogProcessingFactory** y confirme que ve todos los conjuntos de datos (las tablas son conjuntos de datos rectangulares).

	![Todos los conjuntos de datos][image-data-factory-tutorial-datasets-all]

	También puede utilizar el siguiente comando de Azure PowerShell:
			
		Get-AzureRmDataFactoryDataset –ResourceGroupName ADF –DataFactoryName $df

	


## <a name="MainStep5"></a> Paso 5: Creación y programación de canalizaciones
En este paso, creará las canalizaciones siguientes: PartitionGameLogsPipeline, EnrichGameLogsPipeline y AnalyzeMarketingCampaignPipeline.

1. En el **Explorador de Windows**, vaya a la subcarpeta **Canalizaciones** en la carpeta **C:\\ADFWalkthrough** (o desde la ubicación donde extrajo los ejemplos).
2.	Abra **PartitionGameLogsPipeline.json** en su editor favorito, reemplace el texto resaltado por su cuenta de almacenamiento para la información de la cuenta de almacenamiento de datos y guarde el archivo.
			
		"RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
		"PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. Repita el paso para crear los siguientes procesos:
	1. **EnrichGameLogsPipeline**.json (3 apariciones)
	2. **AnalyzeMarketingCampaignPipeline**.json (3 apariciones)

	**IMPORTANTE:** confirme que ha reemplazado todas las apariciones de <storageaccountname> por el nombre de la cuenta de almacenamiento.
 
4.  En **Azure PowerShell**, vaya a la subcarpeta **Canalizaciones** en la carpeta **C:\\ADFWalkthrough** (o desde la ubicación donde extrajo los ejemplos).
5.  Use el cmdlet **New-AzureRmDataFactoryPipeline** para crear los canalizaciones para **PartitionGameLogspeline**.json de la manera siguiente.	 
			
		New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionGameLogsPipeline.json

	Si está utilizando un nombre diferente para ResourceGroupName, DataFactoryName o Pipeline, indíquelo en el cmdlet anterior. Además, proporcione la ruta de acceso completa del archivo JSON de proceso.
6. Repita el paso anterior para crear los siguientes procesos:
	1. **EnrichGameLogsPipeline**
			
			New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichGameLogsPipeline.json

	2. **AnalyzeMarketingCampaignPipeline**
				
			New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\AnalyzeMarketingCampaignPipeline.json

7. Use el cmdlet **Get-AzureRmDataFactoryPipeline** para la lista de las canalizaciones.
			
		Get-AzureRmDataFactoryPipeline –ResourceGroupName ADF –DataFactoryName $df

8. Una vez creados los procesos, puede especificar la duración en la que se producirá el procesamiento de datos. Al especificar el período activo de un proceso, está definiendo el tiempo en el que se procesarán los segmentos de datos basándose en las propiedades de disponibilidad que se han definido para cada tabla ADF.

Para especificar el período activo para el proceso, puede usar el cmdlet Set-AzureRmDataFactoryPipelineActivePeriod. En este tutorial, los datos de ejemplo son del 05/01 al 05/05. Use 2014-05-01 como StartDateTime. EndDateTime es opcional.
			
		Set-AzureRmDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name PartitionGameLogsPipeline
  
9. Confirme para establecer el período activo del proceso.
			
			Confirm
			Are you sure you want to set pipeline 'PartitionGameLogsPipeline' active period from '05/01/2014 00:00:00' to '05/05/2014 00:00:00'?
			[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): n

10. Repita los dos pasos anteriores para establecer el período activo para los siguientes procesos.
	1. **EnrichGameLogsPipeline**
			
			Set-AzureRmDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z –EndDateTime 2014-05-05Z –Name EnrichGameLogsPipeline

	2. **AnalyzeMarketingCampaignPipeline**
			
			Set-AzureRmDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name AnalyzeMarketingCampaignPipeline

11. En el **Portal de Azure**, haga clic en el icono **Canalizaciones** (no en los nombres de las canalizaciones) en la hoja **FACTORÍA DE DATOS** para **LogProcessingFactory**, debería ver las canalizaciones que ha creado.

	![Todas las canalizaciones][image-data-factory-tutorial-pipelines-all]

12. En la hoja **FACTORÍA DE DATOS** para **LogProcessingFactory**, haga clic en **Diagrama**.

	![Vínculo de diagrama][image-data-factory-tutorial-diagram-link]

13. Puede reorganizar el diagrama que ve; aquí se muestra un diagrama reorganizado en el que se muestran entradas directas en la parte superior y las salidas en la parte inferior. Puede ver que la salida de **PartitionGameLogsPipeline** se pasa como entrada a EnrichGameLogsPipeline y la salida de **EnrichGameLogsPipeline** se pasa a **AnalyzeMarketingCampaignPipeline**. Haga doble clic en un título para ver detalles sobre el artefacto que representa la hoja.

	![Vista de diagrama][image-data-factory-tutorial-diagram-view]

	**¡Enhorabuena!** Ha creado la factoría de datos de Azure, servicios vinculados, procesos y tablas, y ha iniciado el flujo de trabajo.


## <a name="MainStep6"></a> Paso 6: Supervisión de las canalizaciones y los segmentos de datos 

1.	Si no tiene la hoja FACTORÍA DE DATOS para abrir LogProcessingFactory, puede hacer lo siguiente:
	1.	Haga clic en **LogProcessingFactory** en el **Panel de inicio**. Al crear la factoría de datos, se selecciona automáticamente la opción **Agregar al Panel de inicio**.

		![Supervisión del Panel de Inicio][image-data-factory-monitoring-startboard]

	2. Haga clic en el centro **EXAMINAR** y en **Todo**.
	 	
		![Supervisión de Todo en centro][image-data-factory-monitoring-hub-everything]

		En la hoja **Examinar**, seleccione **Factorías de datos** y elija **LogProcessingFactory** en la hoja **Factorías de datos**.

		![Supervisión de Examinar factorías de datos][image-data-factory-monitoring-browse-datafactories]
2. Puede supervisar su factoría de datos de varias maneras. Puede iniciar con procesos o conjuntos de datos. Vamos a comenzar con los procesos e iremos profundizando. 
3.	Haga clic en **Canalizaciones** en la hoja **FACTORÍA DE DATOS**. 
4.	Haga clic en **PartitionGameLogsPipeline** en la hoja Canalizaciones. 
5.	En la hoja **CANALIZACIÓN** para **PartitionGameLogsPipeline**, verá que la canalización consume el conjunto de datos **RawGameEventsTable**. Haga clic en **RawGameEventsTable**.

	![Canalización consumida y producida][image-data-factory-monitoring-pipeline-consumed-produced]

6. En la hoja TABLA de **RawGameEventsTable**, verá todos los segmentos. En la siguiente captura de pantalla, todos los segmentos están en estado **Listo** y no hay segmentos con problemas. Significa que los datos están listos para ser procesados.

	![Hoja TABLA RawGameEventsTable][image-data-factory-monitoring-raw-game-events-table]
 
7. Ahora, en la hoja **CANALIZACIÓN** para **PartiionGameLogsPipeline**, haga clic en **Producida**.
8. Debería ver la lista de conjuntos de datos que genera este proceso: 
9. Haga clic en la tabla **PartitionedGameEvents** en la hoja **Conjuntos de datos producidos**. 
10.	Confirme que el **estado** de todos los segmentos se establece en **Listo**. 
11.	Haga clic en uno de los segmentos que está **Listo** para ver la hoja **SEGMENTO DE DATOS** para ese segmento.

	![Hoja SEGMENTO DE DATOS RawGameEventsTable][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Si se produjo un error, verá el estado **Error** aquí. Puede que también vea los dos segmentos con el estado **Listo** o con el estado **Validación pendiente**, según la rapidez con la que se procesen.
 
	Consulte [Referencia para desarrolladores de Factoría de datos de Azure][developer-reference] para obtener una descripción de todos los estados posibles de los segmentos.

12.	En la hoja **SEGMENTO DE DATOS**, haga clic en la ejecución de la lista **Ejecuciones de actividades**. Debería ver la hoja Ejecución de actividad para ese segmento. Debería ver la siguiente hoja **DETALLES DE EJECUCIÓN DE ACTIVIDAD**.

	![Hoja Detalles de ejecución de actividad][image-data-factory-monitoring-activity-run-details]

13.	Haga clic en **Descargar** para descargar los archivos. Esta pantalla es especialmente útil al solucionar errores de procesamiento de HDInsight.
	 
	
Cuando se complete la ejecución de toda la canalización, puede buscar en **MarketingCampaignEffectivenessTable** en Base de datos SQL de Azure **MarketingCampaigns** para ver los resultados.

**¡Enhorabuena!** Ahora puede supervisar y solucionar problemas de los flujos de trabajo. Ha aprendido a utilizar la factoría de datos de Azure para procesar los datos y obtener análisis.

## Ampliación del tutorial para usar datos locales
En el último paso del escenario de procesamiento de registro del tutorial de este artículo, la salida de la eficacia de la campaña de marketing se copió en una Base de datos SQL de Azure. También puede mover estos datos a un SQL Server local para realizar análisis dentro de su organización.
 
Para copiar los datos de eficacia de la campaña de marketing desde Blob de Azure a un SQL Server local, debe crear el servicio vinculado local, la tabla y la canalización que se presentan en el tutorial de este artículo.

Practique el [tutorial Uso de orígenes de datos locales][tutorial-onpremises-using-powershell] para aprender a crear una canalización para copiar datos de eficacia de campaña de marketing en una base de datos de SQL Server local.
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[adftutorial-using-editor]: data-factory-tutorial.md

[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[usepigandhive]: data-factory-data-transformation-activities.md
[tutorial-onpremises-using-powershell]: data-factory-tutorial-extend-onpremises-using-powershell.md
[download-azure-powershell]: ../powershell-install-configure.md

[azure-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx


[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx


[image-data-factory-tutorial-end-to-end-flow]: ./media/data-factory-tutorial-using-powershell/EndToEndWorkflow.png

[image-data-factory-tutorial-partition-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/PartitionGameLogsPipeline.png

[image-data-factory-tutorial-enrich-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/EnrichGameLogsPipeline.png

[image-data-factory-tutorial-analyze-marketing-campaign-pipeline]: ./media/data-factory-tutorial-using-powershell/AnalyzeMarketingCampaignPipeline.png


[image-data-factory-tutorial-new-datafactory-blade]: ./media/data-factory-tutorial-using-powershell/NewDataFactoryBlade.png

[image-data-factory-tutorial-resourcegroup-blade]: ./media/data-factory-tutorial-using-powershell/ResourceGroupBlade.png

[image-data-factory-tutorial-create-resourcegroup]: ./media/data-factory-tutorial-using-powershell/CreateResourceGroup.png

[image-data-factory-tutorial-datafactory-homepage]: ./media/data-factory-tutorial-using-powershell/DataFactoryHomePage.png

[image-data-factory-tutorial-create-datafactory]: ./media/data-factory-tutorial-using-powershell/CreateDataFactory.png

[image-data-factory-tutorial-linkedservice-tile]: ./media/data-factory-tutorial-using-powershell/LinkedServiceTile.png

[image-data-factory-tutorial-linkedservices-add-datstore]: ./media/data-factory-tutorial-using-powershell/LinkedServicesAddDataStore.png

[image-data-factory-tutorial-datastoretype-azurestorage]: ./media/data-factory-tutorial-using-powershell/DataStoreTypeAzureStorageAccount.png

[image-data-factory-tutorial-azurestorage-settings]: ./media/data-factory-tutorial-using-powershell/AzureStorageSettings.png

[image-data-factory-tutorial-storage-key]: ./media/data-factory-tutorial-using-powershell/StorageKeyFromAzurePortal.png

[image-data-factory-tutorial-linkedservices-blade-storage]: ./media/data-factory-tutorial-using-powershell/LinkedServicesBladeWithAzureStorage.png

[image-data-factory-tutorial-azuresql-settings]: ./media/data-factory-tutorial-using-powershell/AzureSQLDatabaseSettings.png

[image-data-factory-tutorial-azuresql-database-connection-string]: ./media/data-factory-tutorial-using-powershell/DatabaseConnectionString.png

[image-data-factory-tutorial-linkedservices-all]: ./media/data-factory-tutorial-using-powershell/LinkedServicesAll.png

[image-data-factory-tutorial-datasets-all]: ./media/data-factory-tutorial-using-powershell/DataSetsAllTables.png

[image-data-factory-tutorial-pipelines-all]: ./media/data-factory-tutorial-using-powershell/AllPipelines.png

[image-data-factory-tutorial-diagram-link]: ./media/data-factory-tutorial-using-powershell/DataFactoryDiagramLink.png

[image-data-factory-tutorial-diagram-view]: ./media/data-factory-tutorial-using-powershell/DiagramView.png

[image-data-factory-monitoring-startboard]: ./media/data-factory-tutorial-using-powershell/MonitoringStartBoard.png

[image-data-factory-monitoring-hub-everything]: ./media/data-factory-tutorial-using-powershell/MonitoringHubEverything.png

[image-data-factory-monitoring-browse-datafactories]: ./media/data-factory-tutorial-using-powershell/MonitoringBrowseDataFactories.png

[image-data-factory-monitoring-pipeline-consumed-produced]: ./media/data-factory-tutorial-using-powershell/MonitoringPipelineConsumedProduced.png

[image-data-factory-monitoring-raw-game-events-table]: ./media/data-factory-tutorial-using-powershell/MonitoringRawGameEventsTable.png

[image-data-factory-monitoring-raw-game-events-table-dataslice-blade]: ./media/data-factory-tutorial-using-powershell/MonitoringPartitionGameEventsTableDataSliceBlade.png

[image-data-factory-monitoring-activity-run-details]: ./media/data-factory-tutorial-using-powershell/MonitoringActivityRunDetails.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial-using-powershell/NewDataFactoryMenu.png

<!---HONumber=AcomDC_0107_2016-->