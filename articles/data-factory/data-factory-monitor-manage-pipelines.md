<properties 
	pageTitle="Supervisión y administración de canalizaciones de la Factoría de datos de Azure" 
	description="Obtenga información sobre el uso del Portal de Azure clásico y Azure PowerShell para supervisar y administrar las factorías de datos y las canalizaciones de Azure que cree." 
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
	ms.date="01/04/2016" 
	ms.author="spelluru"/>

# Supervisión y administración de canalizaciones de la Factoría de datos de Azure
El servicio Factoría de datos proporciona una vista completa y confiable de los servicios de movimiento de datos, procesamiento y almacenamiento. Le ayuda a evaluar el estado de la canalización de datos de un extremo a otro rápidamente, a identificar problemas y a tomar medidas correctivas si es necesario. Visualmente, puede realizar el seguimiento del linaje de datos y las relaciones entre los datos a través de cualquiera de los orígenes y consultar una contabilización histórica completa de ejecución del trabajo, estado del sistema y dependencias desde un solo panel de supervisión.

En este artículo se describe cómo supervisar, administrar y depurar las canalizaciones. También se ofrece información sobre cómo crear alertas y recibir notificaciones cuando se produzcan errores.

## Descripción de las canalizaciones y los estados de actividad
Con el Portal de Azure, puede ver la factoría de datos como un diagrama, las actividades de una canalización, los conjuntos de datos de entrada y salida, etc. En esta sección se indica también cómo pasa un segmento de un estado a otro.

### Navegación hasta la factoría de datos
1.	Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2.	Haga clic en **Examinar todo** y seleccione **Factorías de datos**.
	
	![Examinar todo -> Factorías de datos](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)

	Debería ver todas las factorías de datos en la hoja **Factorías de datos**. 
4. En la hoja Factorías de datos, seleccione la factoría de datos que le interesa y debería ver la página principal (hoja **Factoría de datos**) de la factoría de datos.

	![Hoja Factoría de datos](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### Vista de diagrama de la factoría de datos
La Vista de diagrama de una factoría de datos ofrece un panel único para supervisar y administrar la factoría de datos y sus recursos.

Haga clic en **Diagrama** en la página de inicio de la fábrica de datos anterior para ver la vista de diagrama de la factoría de datos.

![Vista de diagrama](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Puede acercar, alejar, ajustar al tamaño, ajustar al 100%, bloquear el diseño del diagrama, colocar automáticamente canalizaciones y tablas, y mostrar información de linaje (resalta elementos ascendentes y descendentes de los elementos seleccionados).
 

### Actividades en una canalización 
1. Haga doble clic en la canalización y haga clic en **Abrir canalización** para ver todas las actividades de la canalización junto con los conjuntos de datos de entrada y salida para las actividades. Esto resulta útil cuando la canalización consta de más de una actividad y se quiere entender el linaje operativo de una sola canalización.

	![Menú Abrir canalización](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)	 
2. En el ejemplo siguiente, verá dos actividades en la canalización con sus entradas y salidas. En esta canalización de ejemplo se encuentran la actividad titulada **JoinData** del tipo de actividad de Hive de HDInsight y **EgressDataAzure** del tipo de actividad de copia. 
	
	![Actividades en una canalización](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png) 
3. Puede navegar de nuevo a la página de inicio de Factoría de datos haciendo clic en el vínculo de la factoría de datos situado en la ruta de navegación de la esquina superior izquierda.

	![Navegación hacia atrás a la factoría de datos](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### Estado de vista de cada actividad dentro de una canalización
Puede ver el estado actual de una actividad viendo el estado de cualquiera de los conjuntos de datos generados por la actividad.

Por ejemplo, en el siguiente caso, **BlobPartitionHiveActivity** se ejecutó correctamente y generó un conjunto de datos denominado **PartitionedProductsUsageTable** que tiene el estado **Ready**.

![Estado de canalización](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Al hacer doble clic en **PartitionedProductsUsageTable** en la vista de diagrama se presentan todos los segmentos generados por distintas ejecuciones de actividades dentro de una canalización. Puede ver que **BlobPartitionHiveActivity** se ejecutó correctamente cada mes durante los últimos ocho meses y generó los segmentos con el estado **Ready**.

Los segmentos de conjunto de datos en una factoría de datos pueden tener uno de los siguientes estados:

<table>
<tr>
	<th align="left">Estado</th><th align="left">Subestado</th><th align="left">Descripción</th>
</tr>
<tr>
	<td rowspan="8">En espera</td><td>ScheduleTime</td><td>No ha llegado la hora para que se ejecute el sector.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Las dependencias de nivel superior no están listas.</td>
</tr>
<tr>
<td>ComputeResources</td><td>No están disponibles los recursos de proceso.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Todas las instancias de actividad están ocupadas con la ejecución de otros sectores.</td>
</tr>
<tr>
<td>ActivityResume</td><td>La actividad está en pausa y no puede ejecutar los sectores hasta que se reanude.</td>
</tr>
<tr>
<td>Retry</td><td>Se volverá a intentar la ejecución de la actividad.</td>
</tr>
<tr>
<td>Validación</td><td>Aún no ha iniciado la validación.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>En espera de que se vuelva a intentar la validación.</td>
</tr>
<tr>
&lt;tr
<td rowspan="2">InProgress</td><td>Validating</td><td>Validación en curso.</td>
</tr>
<td></td>
<td>El segmento se está procesando.</td>
</tr>
<tr>
<td rowspan="4">Con error</td><td>TimedOut</td><td>La ejecución tardó más tiempo del permitido por la actividad.</td>
</tr>
<tr>
<td>Canceled</td><td>Cancelado por acción del usuario.</td>
</tr>
<tr>
<td>Validación</td><td>Error de validación.</td>
</tr>
<tr>
<td></td><td>No se pudo generar o validar el segmento.</td>
</tr>
<td>Ready</td><td></td><td>El segmento está listo para su uso.</td>
</tr>
<tr>
<td>Skipped</td><td></td><td>El segmento no se procesa.</td>
</tr>
<tr>
<td>None</td><td></td><td>Un segmento que existía con un estado distinto, pero se ha restablecido.</td>
</tr>
</table>



Puede ver los detalles sobre un segmento haciendo clic en la hoja **Segmentos actualizados recientemente**.

![Detalles de segmento](./media/data-factory-monitor-manage-pipelines/slice-details.png)
 
Si el segmento se ejecutó varias veces, verá varias filas en la lista **Ejecuciones de actividades**.

![Ejecuciones de actividad en un segmento](./media/data-factory-monitor-manage-pipelines/activity-runs-for-a-slice.png)

Para ver detalles sobre una ejecución de actividad, haga clic en la entrada de la ejecución en la lista **Ejecuciones de actividades**. Se presentarán todos los archivos de registro junto con un mensaje de error si existe alguno. Esto resulta muy útil para ver y depurar registros sin tener que salir de la factoría de datos.

![Detalles de ejecución de actividad](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Si el segmento no está en el estado **Listo**, puede ver los segmentos ascendentes que no están en estado Listo y bloquean la ejecución del segmento actual en la lista **Segmentos ascendentes que no están listos**. Esto resulta muy útil cuando el segmento tiene el estado **En espera** y se quiere saber cuáles son las dependencias ascendentes en las que el segmento está en espera.

![Segmentos ascendentes no listos](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### Diagrama de estado del conjunto de datos
Cuando se implementa una factoría de datos y las canalizaciones tienen un período activo válido, los segmentos del conjunto de datos pasan de un estado a otro. Actualmente, el estado del segmento se ajusta al siguiente diagrama de estado:

![Diagrama de estado](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

El flujo de transición de estado del conjunto de datos de la factoría de datos implica los siguientes estados: En espera -> En curso /En curso (Validando) -> Listo/En error.

Los segmentos se inician con el estado **En espera** de las condiciones previas que deben cumplirse antes de la ejecución. Seguidamente, la actividad comienza a ejecutarse y el segmento pasa al estado **En curso**. La ejecución de actividades puede ser correcta o producirse un error y, en función de esto, el segmento pasará al estado **Listo** o **En error**.

El usuario puede restablecer el segmento para que vuelva del estado **Listo** o **En error** al estado **En espera**. El usuario también puede marcar el estado del segmento como **Omitir**, lo que impide que la actividad se ejecute y no se procese el segmento.


## Administración de canalizaciones
Puede administrar las canalizaciones mediante Azure PowerShell. Por ejemplo, puede pausar y reanudar canalizaciones ejecutando cmdlets de Azure PowerShell.

### Pausa y reanudación de canalizaciones
Puede pausar o suspender canalizaciones con el cmdlet **Suspend-AzureRmDataFactoryPipeline** de Powershell. Esto resulta útil si se detecta un problema con los datos y no se quiere seguir ejecutando las canalizaciones para procesar datos hasta que se solucione el problema.

Por ejemplo: en la siguiente captura de pantalla, se identificó un problema con la canalización **PartitionProductsUsagePipeline** en la factoría de datos **productrecgamalbox1dev** y queremos suspender la canalización.

![Canalización que se suspende](./media/data-factory-monitor-manage-pipelines/pipeline-to-be-suspended.png)

Ejecute el siguiente comando de PowerShell para suspender la canalización **PartitionProductsUsagePipeline**.

	Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>

Por ejemplo:

	Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 

Tras solucionar el problema con **PartitionProductsUsagePipeline**, se puede reanudar la canalización suspendida mediante el siguiente comando de PowerShell.

	Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>

Por ejemplo:

	Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 


## Depuración de canalizaciones
Factoría de datos de Azure ofrece amplias capacidades a través del Portal de Azure clásico y Azure PowerShell para depurar y solucionar problemas de las canalizaciones.

### Búsqueda de errores en una canalización
Si falla la ejecución de actividad en una canalización, el conjunto de datos generado por la canalización tiene un estado de error debido al error. Puede depurar y solucionar los errores en la Factoría de datos de Azure con los mecanismos siguientes.

#### Uso del Portal de Azure clásico para depurar un error:

1.	Haga clic en **Con errores** en el icono **Conjuntos de datos** en la página principal de la factoría de datos.
	
	![Icono Conjuntos de datos con errores](./media/data-factory-monitor-manage-pipelines/datasets-tile-with-errors.png)
2.	En la hoja **Conjuntos de datos con errores**, haga clic en la tabla en la que está interesado.

	![Hoja Conjuntos de datos con errores](./media/data-factory-monitor-manage-pipelines/datasets-with-errors-blade.png)
3.	En la hoja **TABLA**, haga clic en el segmento problemático con el **ESTADO** establecido en **En error**.

	![Hoja Tabla con segmentos con problemas](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
4.	En la hoja **SEGMENTO DE DATOS**, haga clic en la ejecución de actividad que produjo el error.
	
	![Segmento de datos con un error](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
5.	En la hoja **DETALLES DE EJECUCIÓN DE ACTIVIDAD**, puede descargar los archivos asociados al procesamiento de HDInsight. Haga clic en Descargar correspondiente a Status/stderr para descargar el archivo de registro de errores que contiene detalles sobre el error.

	![Hoja Detalles de ejecución de actividad con errores](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)

#### Uso de PowerShell para depurar un error
1.	Inicie **Azure PowerShell**.
3.	Ejecute el comando **Get-AzureRmDataFactorySlice** para ver los segmentos y sus estados. Debería ver un segmento con el estado: **En error**.

		Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-TableName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
	
	Por ejemplo:


		Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	Reemplace **StartDateTime** por el valor de StartDateTime especificado para Set-AzureDataFactoryPipelineActivePeriod.
4. Ahora, ejecute el cmdlet **Get-AzureRmDataFactoryRun** para obtener detalles sobre la ejecución de actividad para el segmento.

		Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-
		DataFactoryName] <String> [-TableName] <String> [-StartDateTime] 
		<DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
	
	Por ejemplo:


		Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	El valor de StartDateTime es la hora de inicio del segmento con error o con problemas que anotó en el paso anterior. La fecha y hora debe ir encerrada entre comillas dobles.
5. 	Debería ver la salida con detalles sobre el error (similar a la siguiente):

		Id                  	: 841b77c9-d56c-48d1-99a3-8c16c3e77d39
		ResourceGroupName   	: ADF
		DataFactoryName     	: LogProcessingFactory3
		TableName           	: EnrichedGameEventsTable
		ProcessingStartTime 	: 10/10/2014 3:04:52 AM
		ProcessingEndTime   	: 10/10/2014 3:06:49 AM
		PercentComplete     	: 0
		DataSliceStart      	: 5/5/2014 12:00:00 AM
		DataSliceEnd        	: 5/6/2014 12:00:00 AM
		Status              	: FailedExecution
		Timestamp           	: 10/10/2014 3:04:52 AM
		RetryAttempt        	: 0
		Properties          	: {}
		ErrorMessage        	: Pig script failed with exit code '5'. See wasb://		adfjobs@spestore.blob.core.windows.net/PigQuery
		                        		Jobs/841b77c9-d56c-48d1-99a3-
					8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
					more details.
		ActivityName        	: PigEnrichLogs
		PipelineName        	: EnrichGameLogsPipeline
		Type                	:
	
	
6. 	Puede ejecutar el cmdlet **Save-AzureRmDataFactoryLog** con el valor de id. que ve en la salida anterior y descargar los archivos de registro mediante la opción **-DownloadLogsoption** del cmdlet.

	Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\\Test"


## Repetición de la ejecución de errores en una canalización

### Uso del portal de Azure clásico

Tras solucionar los problemas y depurar los errores de una canalización, para volver a ejecutar los errores vaya al segmento de error y haga clic en el botón **Ejecutar** de la barra de comandos.

![Repetición de ejecución de un segmento con errores](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

En caso de que el segmento no se valide debido a un error de directiva (por ejemplo: datos no disponibles), puede corregir el error y volver a validarlo haciendo clic en el botón **Validar** de la barra de comandos.![Corrección de errores y validación](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### Uso de Azure PowerShell

Puede volver a ejecutar errores mediante el cmdlet 'Set-AzureDataFactorySliceStatus'.

	Set-AzureRmDataFactorySliceStatus [-ResourceGroupName] <String> [-DataFactoryName] <String> [-TableName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Status] <String> [[-UpdateType] <String> ] [-Profile <AzureProfile> ] [ <CommonParameters>]

**Ejemplo:** En el caso siguiente se establece el estado de todos los segmentos de la tabla 'DAWikiAggregatedData' en 'PendingExecution' en la factoría de datos de Azure 'WikiADF'.

**Nota:** UpdateType se establece en UpstreamInPipeline, lo que significa que el estado de cada segmento de la tabla y todas las tablas dependientes (ascendentes) que se usan como tablas de entrada para las actividades de la canalización se establecen en "PendingExecution". Otro valor posible para este parámetro es "Individual".

	Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -TableName DAWikiAggregatedData -Status PendingExecution -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00


## Creación de alertas
Azure registra eventos del usuario cuando se crea, actualiza o elimina un recurso de Azure (por ejemplo, la Factoría de datos). Puede crear alertas en estos eventos. Factoría de datos permite capturar diversas métricas y crear alertas en las métricas. Se recomienda que use los eventos con fines de supervisión en tiempo real y las métricas con fines históricos.

### Alertas en eventos
Los eventos de Azure proporcionan información útil sobre lo que sucede en los recursos de Azure. Azure registra eventos del usuario cuando se crea, actualiza o elimina un recurso de Azure (por ejemplo, la Factoría de datos). Al usar la Factoría de datos de Azure, se generan eventos cuando:

- La Factoría de datos de Azure se crea/actualiza/elimina.
- Se inicia o finaliza el procesamiento de datos (denominado ejecución).
- Cuando se crea o se elimina un clúster de HDInsight a petición.

Puede crear alertas sobre estos eventos del usuario y configurarlas para enviar notificaciones por correo electrónico al administrador y a los coadministradores de la suscripción. Además, puede especificar direcciones de correo electrónico adicionales de los usuarios que necesiten recibir notificaciones por correo electrónico cuando se cumplan las condiciones. Esto resulta muy útil si se quiere recibir una notificación cuando se produzcan errores y no se desea supervisar continuamente la factoría de datos.

#### Especificación de una definición de alerta:
Para especificar una definición de alerta, cree un archivo JSON que describa las operaciones sobre las que desea recibir alertas. En el ejemplo siguiente, la alerta enviará una notificación por correo electrónico para la operación RunFinished. Para ser más específicos, se envía una notificación por correo electrónico cuando se ha completado una ejecución en la Factoría de datos y se ha producido un error (estado = FailedExecution).

	{
	    "contentVersion": "1.0.0.0",
	     "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
	    "parameters": {},
	    "resources": 
	    [
	        {
	            "name": "ADFAlertsSlice",
	            "type": "microsoft.insights/alertrules",
	            "apiVersion": "2014-04-01",
	            "location": "East US",
	            "properties": 
	            {
	                "name": "ADFAlertsSlice",
	                "description": "One or more of the data slices for the Azure Data Factory has failed processing.",
	                "isEnabled": true,
	                "condition": 
	                {
	                    "odata.type": "Microsoft.Azure.Management.Insights.Models.ManagementEventRuleCondition",
	                    "dataSource": 
	                    {
	                        "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleManagementEventDataSource",
	                        "operationName": "RunFinished",
	                        "status": "Failed",
	                        "subStatus": "FailedExecution"   
	                    }
	                },
	                "action": 
	                {
	                    "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
	                    "customEmails": [ "<your alias>@contoso.com" ]
	                }
	            }
	        }
	    ]
	}

En la definición anterior de JSON, **subStatus** se puede quitar si no desea recibir alertas sobre un error específico.

En el ejemplo anterior se configura la alerta para todas las factoría de datos de la suscripción. Si quiere que la alerta esté configurada para una factoría de datos concreta, puede especificar la factoría de datos **resourceUri** en el bloque **dataSource** de la siguiente manera:

	"resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>"

En la tabla siguiente se ofrece una lista de las operaciones y los estados (y subestados) disponibles.

Nombre de la operación | Estado | Subestado
-------------- | ------ | ----------
RunStarted | Started | Iniciando
RunFinished | Failed / Succeeded | <p>FailedResourceAllocation</p><p>Succeeded</p><p>FailedExecution</p><p>TimedOut</p><p><Canceled/p><p>FailedValidation</p><p>Abandoned</p>
OnDemandClusterCreateStarted | Started
OnDemandClusterCreateSuccessful | Succeeded
OnDemandClusterDeleted | Succeeded

Vea [Crear regla de alerta](https://msdn.microsoft.com/library/azure/dn510366.aspx) para más información sobre los elementos JSON usados en el ejemplo anterior.

#### Implementación de alertas 
Para implementar la alerta, use el cmdlet de Azure PowerShell **New-AzureRmResourceGroupDeployment**, como se muestra en el ejemplo siguiente:

	New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\ADFAlertFailedSlice.json  

Una vez completada correctamente la implementación del grupo de recursos, verá los siguientes mensajes:

	VERBOSE: 7:00:48 PM - Template is valid.
	WARNING: 7:00:48 PM - The StorageAccountName parameter is no longer used and will be removed in a future release.
	Please update scripts to remove this parameter.
	VERBOSE: 7:00:49 PM - Create template deployment 'ADFAlertFailedSlice'.
	VERBOSE: 7:00:57 PM - Resource microsoft.insights/alertrules 'ADFAlertsSlice' provisioning status is succeeded
	
	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :

#### Recuperación de la lista de implementaciones del grupo de recursos de Azure
Para recuperar la lista de implementaciones del grupo de recursos de Azure implementado, use el cmdlet **Get-AzureRmResourceGroupDeployment**, como se muestra en el ejemplo siguiente:

	Get-AzureRmResourceGroupDeployment -ResourceGroupName adf
	
	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :


#### Solución de problemas con eventos del usuario


- Aparecerán todos los eventos generados después de hacer clic en el icono **Operaciones** y se pueden configurar alertas en cualquiera de estas operaciones visibles en la hoja **Eventos**:

	![Operaciones](./media/data-factory-monitor-manage-pipelines/operations.png)


- Vea el artículo [Azure Insight Cmdlets](https://msdn.microsoft.com/library/mt282452.aspx) para ver los cmdlets de PowerShell que puede usar para agregar, obtener o quitar alertas. Estos son algunos ejemplos del uso del cmdlet **Get-AlertRule**:


		PS C:\> get-alertrule -res $resourceGroup -n ADFAlertsSlice -det
			
				Properties :
		        Action      : Microsoft.Azure.Management.Insights.Models.RuleEmailAction
		        Condition   :
				DataSource :
				EventName             :
				Category              :
				Level                 :
				OperationName         : RunFinished
				ResourceGroupName     :
				ResourceProviderName  :
				ResourceId            :
				Status                : Failed
				SubStatus             : FailedExecution
				Claims                : Microsoft.Azure.Management.Insights.Models.RuleManagementEventClaimsDataSource
		        Condition  	:
				Description : One or more of the data slices for the Azure Data Factory has failed processing.
				Status      : Enabled
				Name:       : ADFAlertsSlice
				Tags       :
				$type          : Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage
				Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/ADFAlertsSlice
				Location   : West US
				Name       : ADFAlertsSlice
		
		PS C:\> Get-AlertRule -res $resourceGroup
	
				Properties : Microsoft.Azure.Management.Insights.Models.Rule
				Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
				Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
				Location   : West US
				Name       : FailedExecutionRunsWest0
		
				Properties : Microsoft.Azure.Management.Insights.Models.Rule
				Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
				Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest3
				Location   : West US
				Name       : FailedExecutionRunsWest3
	
		PS C:\> Get-AlertRule -res $resourceGroup -Name FailedExecutionRunsWest0
		
				Properties : Microsoft.Azure.Management.Insights.Models.Rule
				Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
				Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
				Location   : West US
				Name       : FailedExecutionRunsWest0

	Ejecute los siguientes comandos get-help para ver detalles y ejemplos para el cmdlet Get-AlertRule.

		get-help Get-AlertRule -detailed 
		get-help Get-AlertRule -examples


- Si ve los eventos de generación de alertas en la hoja del portal, pero no recibe notificaciones de correo electrónico, compruebe si la dirección de correo electrónico especificada se estableció para recibir correos electrónicos de remitentes externos. Puede que la configuración de correo electrónico bloquease los mensajes de alertas.

### Alertas en métricas
Factoría de datos permite capturar diversas métricas y crear alertas en las métricas. Puede supervisar y crear alertas en las siguientes métricas para los segmentos de la factoría de datos.
 
- Ejecuciones con error
- Ejecuciones correctas

Estas métricas resultan muy útiles y permiten a los usuarios obtener información general de todas las ejecuciones correctas y con error en su factoría de datos. Las métricas se emiten cada vez que hay una ejecución del segmento. A la hora en punto, estas métricas se agregan y se insertan en la cuenta de almacenamiento. Por lo tanto, para habilitar las métricas, tendrá que configurar una cuenta de almacenamiento.

#### Habilitación de métricas:
Para habilitar las métricas, haga clic en la secuencia siguiente desde la hoja Factoría de datos:

**Supervisión** -> **Métrica** -> **Configuración de diagnóstico** -> **Diagnóstico**

En la hoja **Diagnóstico**, haga clic en **Activada**, seleccione la cuenta de almacenamiento y guarde los cambios.

![Habilitación de métricas](./media/data-factory-monitor-manage-pipelines/enable-metrics.png)

Una vez guardadas, las métricas pueden tardar hasta una hora en estar visibles en la hoja de supervisión, porque la agregación de métricas se realiza cada hora.


### Configuración de alerta en métricas:

Para configurar alertas en métricas, haga clic en la secuencia siguiente de la hoja Factoría de datos: **Supervisión** -> **Métrica** -> **Agregar alerta** -> **Agregar una regla de alerta**.

Rellene los detalles de la regla de alerta, especifique los mensajes de correo electrónico y haga clic en **Aceptar**.


![Configuración de alerta en métricas](./media/data-factory-monitor-manage-pipelines/setting-up-alerts-on-metrics.png)

Al terminar, debería ver una nueva regla de alerta habilitada en el icono Reglas de alertas de la manera siguiente:

![Reglas de alerta habilitadas](./media/data-factory-monitor-manage-pipelines/alert-rule-enabled.png)

¡Enhorabuena! Ya configuró la primera alerta en métricas. Ahora debe recibir notificaciones cada vez que la regla de alerta coincida en la ventana de tiempo especificada.

### Notificaciones de alerta:
Cuando la regla de configuración coincida con la condición, recibirá una alerta activada por correo electrónico. Cuando resuelva el problema y la condición de alerta ya no coincida más, recibirá un mensaje de correo de alerta resuelta.

Este comportamiento es diferente al de eventos en los que se envía una notificación en todos y cada uno de los errores en los que la regla de alerta se cumple.

### Implementación de alertas con PowerShell
Puede implementar alertas para las métricas de la misma manera que lo hace para los eventos.

**Definición de alertas:**

	{
	    "contentVersion" : "1.0.0.0",
	    "$schema" : "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
	    "parameters" : {},
	    "resources" : [
		{
	            "name" : "FailedRunsGreaterThan5",
	            "type" : "microsoft.insights/alertrules",
	            "apiVersion" : "2014-04-01",
	            "location" : "East US",
	            "properties" : {
	                "name" : "FailedRunsGreaterThan5",
	                "description" : "Failed Runs greater than 5",
	                "isEnabled" : true,
	                "condition" : {
	                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
	                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
	                    "dataSource" : {
	                        "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
	                        "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
	                        "resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName
	>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>",
	                        "metricName" : "FailedRuns"
	                    },
	                    "threshold" : 5.0,
	                    "windowSize" : "PT3H",
	                    "timeAggregation" : "Total"
	                },
	                "action" : {
	                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
	                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
	                    "customEmails" : ["abhinav.gpt@live.com"]
	                }
	            }
	        }
	    ]
	}
 
Reemplace subscriptionId, resourceGroupName y dataFactoryName en el ejemplo anterior con los valores adecuados.

*metricName* a partir de ahora admite dos valores:
- FailedRuns
- SuccessfulRuns.

**Implementación de alertas:**

Para implementar la alerta, use el cmdlet de Azure PowerShell **New-AzureRmResourceGroupDeployment**, como se muestra en el ejemplo siguiente:

	New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\FailedRunsGreaterThan5.json

Debería ver el siguiente mensaje después de la implementación correcta:

	VERBOSE: 12:52:47 PM - Template is valid.
	VERBOSE: 12:52:48 PM - Create template deployment 'FailedRunsGreaterThan5'.
	VERBOSE: 12:52:55 PM - Resource microsoft.insights/alertrules 'FailedRunsGreaterThan5' provisioning status is succeeded
	
	
	DeploymentName    : FailedRunsGreaterThan5
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 7/27/2015 7:52:56 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           


También puede usar el cmdlet **Add-AlertRule** para implementar una regla de alerta. Consulte el tema [Add-AlertRule](https://msdn.microsoft.com/library/mt282468.aspx) para obtener información detallada y ejemplos.

<!---HONumber=AcomDC_0128_2016-->