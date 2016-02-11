<properties 
	pageTitle="Solución de problemas de la Factoría de datos de Azure" 
	description="Obtenga información acerca de la solución de problemas relacionados con la factoría de datos de Azure." 
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

# Solución de problemas de la factoría de datos
Puede solucionar los problemas de la Factoría de datos de Azure mediante el Portal de Azure clásico (o) cmdlets de Azure PowerShell. Este tema contiene tutoriales que muestran cómo usar el Portal de Azure clásico para solucionar rápidamente los errores que se producen con la factoría de datos.

## Problema: no se pueden ejecutar los cmdlets de Factoría de datos
Si está usando Azure PowerShell, versión < 1.0:
 
Para resolver este problema, cambie el modo de Azure a **AzureResourceManager**:

Inicie **Azure PowerShell** y ejecute el siguiente comando para cambiar al modo **AzureResourceManager**. Los cmdlets de Factoría de datos de Azure están disponibles en el modo **AzureResourceManager**.

         switch-azuremode AzureResourceManager

## Problema: error no autorizado al ejecutar un cmdlet de Factoría de datos
Probablemente no está usando la cuenta o suscripción de Azure correctas con Azure PowerShell. Use los cmdlets siguientes para seleccionar la cuenta y la suscripción de Azure correctas para usarlas con Azure PowerShell.

1. Add-AzureAccount: use el id. de usuario y la contraseña correctos.
2. Get-AzureSubscription: vea todas las suscripciones de la cuenta. 
3. Select-AzureSubscription <subscription name>: seleccione la suscripción correcta. Use la misma que usó para crear una factoría de datos en el Portal de Azure.

## Problema: no se pudo iniciar la configuración rápida de Data Gateway desde el Portal de Azure clásico.
La configuración rápida de Data Gateway requiere Internet Explorer o un explorador web compatible con Microsoft ClickOnce. Si se produce un error al iniciar la configuración rápida, puede

1. Cambiar a Internet Explorer si se produce un error con los demás exploradores. O
2. Usar los vínculos "Instalación manual" que aparecen en la misma hoja en el portal para realizar la instalación y, a continuación, copiar la clave que se proporciona en la pantalla y pegarla cuando la configuración de Data Management Gateway esté lista. Si no se inicia, compruebe el menú de inicio para "Microsoft Data Management Gateway" y pegue la clave cuando se inicie. 


## Problema: no se pudo iniciar el Administrador de credenciales desde el Portal de Azure clásico.
Al configurar o actualizar un servicio vinculado de SQL Server vinculado mediante el Portal de Azure clásico, el Administrador de credenciales se iniciará para garantizar la seguridad. Es necesario Internet Explorer o un explorador web compatible con Microsoft ClickOnce. Cambie a Internet Explorer si se produce un error con los demás exploradores.

## Problema: no se pudo conectar al servidor SQL Server local. 
Compruebe que el servidor SQL Server es accesible desde la máquina donde está instalada la puerta de enlace. En el equipo donde está instalada la puerta de enlace, puede

1. Hacer ping al equipo donde está instalado el servidor SQL Server. O
2. Intentar conectarse a la instancia de SQL Server mediante las credenciales especificadas en el Portal de Azure clásico con SQL Server Management Studio (SSMS).


## Problema: los segmentos de entrada están en el estado PendingExecution o PendingValidation de forma permanente.

Los segmentos pueden estar en el estado **PendingExecution** o **PendingValidation** por varias razones. Una de las más comunes es que la propiedad **external** no está establecida en **true**. Cualquier conjunto de datos que se produce fuera del ámbito de Factoría de datos de Azure debe marcarse con la propiedad **external**. Esto indica que los datos son externos y no están respaldados por ninguna canalización dentro de la factoría de datos. Los segmentos de datos se marcan con el estado **Listo** una vez que están disponibles en el almacén correspondiente.

Consulte el ejemplo siguiente para el uso de la propiedad **external**. Opcionalmente, puede especificar**externalData*** al establecer external en true.

Consulte el tema Tablas en [Referencia de scripting JSON][json-scripting-reference] para obtener más información sobre esta propiedad.
	
	{
	  "name": "CustomerTable",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "MyLinkedService",
	    "typeProperties": {
	      "folderPath": "MyContainer/MySubFolder/",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "dataDelay": "00:10:00",
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

 Para resolver el error, agregue la propiedad **external** y la sección **externalData** opcional a la definición de JSON de la tabla de entrada y vuelva a crear la tabla.

## Problema: la operación de copia híbrida produce un error.
Para obtener más información:

1. Inicie el Administrador de configuración de Data Management Gateway en el equipo donde se instaló la puerta de enlace. Compruebe que **Nombre de la puerta de enlace** se establece en el nombre lógico de la puerta de enlace en el **Portal de Azure clásico**, **Estado de clave de puerta de enlace** es **Registrado** y **Estado del servicio** es **Iniciado**. 
2. Inicie el **Visor de eventos**. Expanda **Registros de aplicaciones y servicios** y haga clic en **Data Management Gateway**. Vea si hay errores relacionados con Data Management Gateway. 

## Problema: el aprovisionamiento de HDInsight a petición provoca un error.

Al usar un servicio vinculado de tipo HDInsightOnDemandLinkedService, debe especificar un linkedServiceName que apunte a Almacenamiento de blobs de Azure. Esta cuenta de almacenamiento se usará para copiar todos los registros y archivos auxiliares para el clúster de HDInsight a petición. A veces la actividad que realiza el aprovisionamiento a petición en HDInsight puede provocar el error siguiente:

		Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

Este error suele indicar que la ubicación de la cuenta de almacenamiento especificada en linkedServiceName no está en la misma ubicación del centro de datos que donde se produce el aprovisionamiento de HDInsight. Por ejemplo, si la ubicación de Factoría de datos de Azure es Oeste de EE. UU. y el aprovisionamiento de HDInsight a petición ocurre en el oeste de EE. UU. pero la ubicación de la cuenta de almacenamiento de blobs de Azure está establecida en Este de EE. UU., el aprovisionamiento a petición provocará un error.

Además, hay una segunda propiedad de JSON additionalLinkedServiceNames, donde puede se especifiquen cuentas de almacenamiento adicionales en HDInsight a petición. Las cuentas de almacenamiento vinculado adicionales deben estar en la misma ubicación que el clúster de HDInsight; de lo contrario, se producirá el mismo error.



## Problema: la actividad personalizada produce un error.
Al usar una actividad personalizada en Factoría de datos de Azure (tipo de actividad de canalización CustomActivity), la aplicación personalizada se ejecuta en el servicio vinculado a HDInsight como un trabajo de MapReduce de streaming de solo asignación.

Cuando se ejecuta la actividad personalizada, Factoría de datos de Azure podrá capturar los resultados desde el clúster de HDInsight y los guardará en el contenedor de almacenamiento *adfjobs* de la cuenta de Almacenamiento de blobs de Azure. Si se produce un error, puede leer el texto del archivo de salida **stderr**. Se puede tener acceso a los archivos y leerlos desde el propio Portal de Azure clásico en el explorador web o con las herramientas del explorador de almacenamiento para tener acceso a los archivos que se conservan en el contenedor de almacenamiento en Almacenamiento de blobs de Azure directamente.

Para enumerar y leer los registros de una actividad personalizada concreta, siga uno de los tutoriales que se explican más adelante en esta página. En resumen:

1.  En el Portal de Azure clásico, elija **Examinar** para buscar su factoría de datos.
2.  Use el botón **Diagrama** para ver el diagrama de la factoría de datos y haga clic en la tabla **Conjunto de datos** que sigue la **Canalización** específica que tiene la actividad personalizada. 
3.  En la hoja **Tabla**, haga clic en el segmento que le interesa en **Segmentos con problemas** para el período de tiempo que se debe investigar.
4.  Aparecerá la hoja **Segmento de datos** detallada, que puede enumerar varias **ejecuciones de actividad** para el segmento. Haga clic en una **actividad** de la lista. 
5.  Aparecerá la hoja **Detalles de ejecución de actividad**. Incluirá una lista de **mensajes de error** en medio de la hoja y varios **archivos de registro** enumerados al final de la hoja asociada a la ejecución de actividad en cuestión.
	- Logs/system-0.log
	- Estado
	- Estado/exit
	- Estado/stderr
	- Estado/stdout

6. Haga clic en el primer elemento **Archivo de registro** de la lista y el registro se abrirá en una hoja nueva, donde se mostrará el texto completo para su lectura. Haga clic en cada archivo de registro para revisar el texto. Se abrirá la hoja del visor de texto. Puede hacer clic en el botón **Descargar** para descargar el archivo de texto para su visualización sin conexión opcional.

Un **error común** de una actividad personalizada es el error de ejecución del paquete con código de salida ''1''. Consulte '' wasb://adfjobs@storageaccount.blob.core.windows.net/PackageJobs/<guid>/<jobid>/Status/stderr'' para obtener más información.

Para ver más detalles de este tipo de error, abra el archivo **stderr**. Un error común que es que hay una condición de tiempo de espera como esta: INFO mapreduce.Job: Task Id : attempt\_1424212573646\_0168\_m\_000000\_0, Status : FAILED AttemptID:attempt\_1424212573646\_0168\_m\_000000\_0 Timed out after 600 secs.

Este mismo error puede aparecer varias veces si el trabajo se ha reintentado tres veces, por ejemplo, en el intervalo de 30 minutos o más.

Este error de tiempo de espera indica un que se ha producido un tiempo de espera de 600 segundos (10 minutos). Normalmente, esto significa que la aplicación de .NET personalizada no ha emitido ninguna actualización del estado durante 10 minutos. Si la aplicación se bloquea o detiene al esperar durante demasiado tiempo, el tiempo de espera de 10 minutos es un mecanismo de seguridad para evitar que espere indefinidamente y retrase la canalización de Factoría de datos de Azure.

Este tiempo de espera se origina en la configuración del clúster de HDInsight que está vinculada a la actividad personalizada. La configuración es **mapred.task.timeout**, cuyo valor predeterminado es 600 000 milisegundos, como se documenta en la configuración predeterminada de Apache aquí: http://hadoop.apache.org/docs/r2.4.0/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml

Puede reemplazar este valor predeterminado cambiando los valores predeterminados en el momento de aprovisionar el clúster de aprovisionamiento de HDInsight. Cuando se usa Factoría de datos de Azure y el servicio vinculado de **HDInsight a petición**, la propiedad JSON se puede agregar cerca de las propiedades HDInsightOnDemandLinkedService JSON. Por ejemplo, puede aumentar el valor a 20 minutos con la propiedad JSON.
		
		"mapReduceConfiguration" :
		{
			"mapreduce.task.timeout":"1200000"
		}
		

Para obtener más contexto y un ejemplo completo de JSON para editar las propiedades de configuración de MapReduce, consulte el ejemplo 3 de la documentación de MSDN aquí https://msdn.microsoft.com/library/azure/dn893526.aspx.

## Problema: la solicitud de PowerShell produce el error 400 Solicitud incorrecta "No se encontró ningún proveedor de recursos registrado..."

A partir del 10 de marzo de 2015, se interrumpirán las versiones de vista previa privadas anteriores de Powershell de Factoría de datos de Azure 2014-05-01-preview, 2014-07-01-preview y 2014-08-01-preview. Recomendamos usar la versión más reciente de los cmdlets de ADF, que ahora forman parte de la descarga de Azure PowerShell, como la descarga de esta dirección URL http://go.microsoft.com/?linkid=9811175&clcid=0x409.

Si usa las versiones interrumpidas del SDK de Azure PowerShell, puede que reciba los errores siguientes:

		HTTP/1.1 400 Bad Request
		Cache-Control: no-cache
		Pragma: no-cache
		Content-Type: application/json; charset=utf-8
		Expires: -1
		x-ms-request-id: e07181e4-e421-46be-8a08-1f71d5e90494
		x-ms-correlation-request-id: e07181e4-e421-46be-8a08-1f71d5e90494
		x-ms-routing-request-id: WESTUS:20150306T234829Z:e07181e4-e421-46be-8a08-1f71d5e90494
		Strict-Transport-Security: max-age=31536000; includeSubDomains
		Date: Fri, 06 Mar 2015 23:48:29 GMT
		Content-Length: 157
		{"error":{"code":"NoRegisteredProviderFound","message":"No registered resource provider found for location 'west US' and API version '2014-05-01-preview'."}}


## <a name="copywalkthrough"></a> Tutorial: Solución de un error al copiar datos
En este tutorial, presentará un error en el tutorial del artículo Introducción a la factoría de datos y aprenderá a usar el Portal de Azure clásico para solucionar el error.

### Requisitos previos
1. Complete el tutorial en el artículo [Introducción a Factoría de datos de Azure][adfgetstarted].
2. Confirme que **ADFTutorialDataFactory** genera datos de la tabla **emp** en Base de datos SQL de Azure.  
3. Ahora, elimine la tabla **emp** (**drop table emp**) de Base de datos SQL de Azure. Esto presentará un error.
4. Ejecute el siguiente comando en **Azure PowerShell** para actualizar el período activo de la canalización y que intente escribir datos en la tabla **emp** , que ya no existe.

         
		Set-AzureRmDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 –EndDateTime 2014-09-30 –Name ADFTutorialPipeline
	
	Reemplace el valor de **StartDateTime** por el día actual y el de **EndDateTime** por el día siguiente.


### Uso del Portal de Azure para solucionar el error

1.	Inicie sesión en el [Portal de Azure][azure-portal]. 
2.	Haga clic en **ADFTutorialDataFactory** en el **Panel de inicio**. Si no ve el vínculo de la factoría de datos en el **Panel de inicio**, haga clic en el centro **EXAMINAR** y, a continuación, haga clic en **Todo**. Haga clic en **Factorías de datos…** en la hoja **Examinar** y elija **ADFTutorialDataFactory**.
3.	Observe que aparece **Con errores** en el icono **Conjuntos de datos**. Haga clic en **Con errores**. Debería ver la hoja **Conjuntos de datos con errores**.

	![Vínculo Factoría de datos con errores][image-data-factory-troubleshoot-with-error-link]

4. En la hoja **Conjuntos de datos** con errores, haga clic en **EmpSQLTable** para ver la hoja **TABLA**.

	![Hoja Conjuntos de datos con errores][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. En la hoja **TABLA**, debería ver los segmentos con problemas, es decir, los segmentos con un error en la lista **Segmentos con problemas** en la parte inferior. También puede ver todos los segmentos recientes con errores en la lista **Segmentos recientes**. Haga clic en un segmento en la lista **Segmentos con problemas**.

	![Hoja Taba con segmentos con problemas][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	Si hace clic en **Segmentos con problemas** (no en un problema específico), verá la hoja **SEGMENTOS DE DATOS**. A continuación, haga clic en un **segmento con problema específico** para ver la hoja **SEGMENTO DE DATOS** para el segmento de datos seleccionado.

6. En la hoja **SEGMENTO DE DATOS** para **EmpSQLTable**, verá todas las **ejecuciones de actividad** para el segmento en la lista de la parte inferior. Haga clic en una **ejecución de actividad** en la lista que produjo el error.

	![Hoja Segmento de datos con ejecuciones activas][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. En la hoja **Detalles de ejecución de actividad** para la ejecución de actividad que ha seleccionado, debe ver los detalles sobre el error. En este escenario, verá: **Nombre de objeto ''emp'' no válido **.

	![Detalles de ejecución de actividad con error][image-data-factory-troubleshoot-activity-run-with-error]

Para resolver este problema, cree la tabla **emp** con el script SQL del artículo [Introducción a Factoría de datos][adfgetstarted].


### Uso de cmdlets de Azure PowerShell para solucionar el error
1.	Inicie **Azure PowerShell**. 
3. Ejecute el comando Get-AzureRmDataFactorySlice para ver los segmentos y sus estados. Debería ver un sector con el estado: error.	

         
		Get-AzureRmDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	Reemplace **StartDateTime** por el valor de StartDateTime especificado para **Set-AzureRmDataFactoryPipelineActivePeriod**.

		ResourceGroupName 		: ADFTutorialResourceGroup
		DataFactoryName   		: ADFTutorialDataFactory
		TableName         		: EmpSQLTable
		Start             		: 10/15/2014 4:00:00 PM
		End               		: 10/15/2014 5:00:00 PM
		RetryCount        		: 0
		Status            		: Failed
		LatencyStatus     		:
		LongRetryCount    		: 0

	Anote la hora de **Inicio** del segmento con problemas (el segmento con **Estado** establecido en **Error**) en la salida. 
4. Ahora, ejecute el cmdlet **Get-AzureRmDataFactoryRun** para obtener detalles sobre la ejecución de actividad para el segmento.
         
		Get-AzureRmDataFactoryRun -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime "10/15/2014 4:00:00 PM"

	El valor de **StartDateTime** es la hora de inicio del segmento con error o con problemas que anotó en el paso anterior. La fecha y hora debe ir encerrada entre comillas dobles.
5. Debería ver la salida con detalles sobre el error (similar a la siguiente):

		Id                  	: 2b19475a-c546-473f-8da1-95a9df2357bc
		ResourceGroupName   	: ADFTutorialResourceGroup
		DataFactoryName     	: ADFTutorialDataFactory
		TableName           	: EmpSQLTable
		ResumptionToken    		:
		ContinuationToken   	:
		ProcessingStartTime 	: 10/15/2014 11:13:39 PM
		ProcessingEndTime  	 	: 10/15/2014 11:16:59 PM
		PercentComplete     	: 0
		DataSliceStart     		: 10/15/2014 4:00:00 PM
		DataSliceEnd       		: 10/15/2014 5:00:00 PM
		Status              	: FailedExecution
		Timestamp           	: 10/15/2014 11:13:39 PM
		RetryAttempt       		: 0
		Properties          	: {}
		ErrorMessage        	: Unknown error in CopyActivity: System.Data.SqlClient.SqlException (0x80131904): **Invalid object name 'emp'.**
                         at System.Data.SqlClient.SqlConnection.OnError(SqlException exception, Boolean
                      breakConnection, Action`1 wrapCloseInAction)
                         at System.Data.SqlClient.TdsParser.ThrowExceptionAndWarning(TdsParserStateObject stateObj,

 

## <a name="pighivewalkthrough"></a> Tutorial: Solución de un error de procesamiento de Hive/Pig
Este tutorial proporciona pasos para solucionar un error con el procesamiento de Hive/Pig mediante el Portal de Azure y Azure PowerShell.


### Tutorial: Uso del Portal de Azure clásico para solucionar un error de procesamiento de Pig/Hive
En este escenario, el conjunto de datos está en estado de error debido a un error en el procesamiento de Hive en un clúster de HDInsight.

1. Haga clic en **Con errores** en el icono **Conjuntos de datos** en la página principal **FACTORÍA DE DATOS**.

	![Vínculo Con errores en el icono Conjuntos de datos][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. En la hoja **Conjuntos de datos con errores**, haga clic en la **tabla** en la que está interesado.

	![Hoja Conjuntos de datos con errores][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. En la hoja **TABLA**, haga clic en el **segmento con problemas** con **ESTADO** establecido en **Error**.

	![Taba con segmentos con problemas][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. En la hoja **SEGMENTO DE DATOS**, haga clic en la **ejecución de actividad** que produjo el error.

	![Segmento de datos con ejecuciones erróneas][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. En la hoja **DETALLES DE EJECUCIÓN DE ACTIVIDAD**, puede descargar los archivos asociados al procesamiento de HDInsight. Haga clic en **Descargar** correspondiente a **Status/stderr** para descargar el archivo de registro de errores que contiene detalles del error.

	![Detalles de ejecución de actividad con vínculo de descarga][image-data-factory-troubleshoot-activity-run-details]

    
### Tutorial: Uso de Azure PowerShell para solucionar un error de procesamiento de Pig/Hive
1.	Inicie **Azure PowerShell**. 
3. Ejecute el comando Get-AzureRmDataFactorySlice para ver los segmentos y sus estados. Debería ver un sector con el estado: error.	

         
		Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	Reemplace **StartDateTime** por el valor de StartDateTime especificado para **Set-AzureRmDataFactoryPipelineActivePeriod**.

		ResourceGroupName : ADF
		DataFactoryName   : LogProcessingFactory
		TableName         : EnrichedGameEventsTable
		Start             : 5/5/2014 12:00:00 AM
		End               : 5/6/2014 12:00:00 AM
		RetryCount        : 0
		Status            : Failed
		LatencyStatus     :
		LongRetryCount    : 0


	Anote la hora de **Inicio** del segmento con problemas (el segmento con **Estado** establecido en **Error**) en la salida. 
4. Ahora, ejecute el cmdlet **Get-AzureRmDataFactoryRun** para obtener detalles sobre la ejecución de actividad para el segmento.
         
		Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	El valor de **StartDateTime** es la hora de inicio del segmento con error o con problemas que anotó en el paso anterior. La fecha y hora debe ir encerrada entre comillas dobles.
5. Debería ver la salida con detalles sobre el error (similar a la siguiente):

		Id                  : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
		ResourceGroupName   : ADF
		DataFactoryName     : LogProcessingFactory3
		TableName           : EnrichedGameEventsTable
		ProcessingStartTime : 10/10/2014 3:04:52 AM
		ProcessingEndTime   : 10/10/2014 3:06:49 AM
		PercentComplete     : 0
		DataSliceStart      : 5/5/2014 12:00:00 AM
		DataSliceEnd        : 5/6/2014 12:00:00 AM
		Status              : FailedExecution
		Timestamp           : 10/10/2014 3:04:52 AM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        : Pig script failed with exit code '5'. See 'wasb://adfjobs@spestore.blob.core.windows.net/PigQuery
								Jobs/841b77c9-d56c-48d1-99a3-8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for more details.
		ActivityName        : PigEnrichLogs
		PipelineName        : EnrichGameLogsPipeline
		Type                :

6. Puede ejecutar el cmdlet **Save-AzureRmDataFactoryLog** con el valor de id. que ve en la salida anterior y descargar los archivos de registro mediante la opción **-DownloadLogs** para el cmdlet.



[adfgetstarted]: data-factory-get-started.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
 

<!---HONumber=AcomDC_1210_2015-->