<properties
	pageTitle="SDK de .NET de administración para Análisis de transmisiones | Microsoft Azure"
	description="Introducción al uso del SDK de .NET de administración de Análisis de transmisiones Obtenga información acerca de cómo configurar y ejecutar trabajos de análisis: crear un proyecto, entradas, salidas y transformaciones."
	keywords="SDK de .NET, API de análisis"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="02/16/2016"
	ms.author="jeffstok"/>


# SDK de .NET de administración: Configuración y ejecución de trabajos de análisis con la API de Análisis de transmisiones de Azure para .NET

Aprenda a configurar y ejecutar trabajos de análisis con la API de Análisis de transmisiones para .NET mediante el SDK de .NET de administración. Configure un proyecto, cree orígenes de entrada y salida, transformaciones, e inicie y detenga trabajos. En los trabajos de análisis puede transmitir datos desde el almacenamiento de blobs o desde un centro de eventos.

Consulte la [documentación de referencia de administración de la API de Análisis de transmisiones para .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Análisis de transmisiones de Azure es un servicio totalmente administrado que proporciona un procesamiento completo de eventos de baja latencia, alta disponibilidad y escalable a través de la transmisión de datos en la nube. Análisis de transmisiones permite a los clientes configurar trabajos de streaming para analizar flujos de datos y realizar análisis casi en tiempo real.


## Requisitos previos
Antes de empezar este artículo, debe tener lo siguiente:

- Instale Visual Studio 2012 o 2013.
- Descargue e instale el [SDK de .NET de Azure](https://azure.microsoft.com/downloads/).
- Cree un grupo de recursos de Azure en su suscripción. A continuación se muestra un ejemplo de script de Azure PowerShell. Para obtener información sobre Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).  


		# Log in to your Azure account
		Add-AzureAccount

		# Select the Azure subscription you want to use to create the resource group
		Select-AzureSubscription -SubscriptionName <subscription name>

			# If Stream Analytics has not been registered to the subscription, remove the remark symbol (#) to run the Register-AzureRMProvider cmdlet to register the provider namespace
			#Register-AzureRMProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

		# Create an Azure resource group
		New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>


-	Configure un origen de entrada y un destino de salida para usar. Para obtener más instrucciones, consulte [Agregar entradas](stream-analytics-add-inputs.md) para configurar una entrada de ejemplo, y [Agregar salidas](stream-analytics-add-outputs.md) para configurar una salida de ejemplo.


## Configuración de un proyecto

Para crear un trabajo de análisis que use la API de Análisis de transmisiones para. NET, configure primero el proyecto.

1. Cree una aplicación de consola .NET de Visual Studio C#.
2. En la consola del administrador de paquetes, ejecute los siguientes comandos para instalar los paquetes NuGet. El primero es el SDK de .NET de administración de Análisis de transmisiones de Azure. El segundo es el cliente de Azure Active Directory que se usará para autenticación.

		Install-Package Microsoft.Azure.Management.StreamAnalytics
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

4. Agregue la siguiente sección **appSettings** al archivo App.config:

		<appSettings>
		  <!--CSM Prod related values-->
		  <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		  <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		  <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
		  <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
		  <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
		  <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION" />
		  <add key="ActiveDirectoryTenantId" value="YOU TENANT ID" />
		</appSettings>


	Reemplace los valores para **SubscriptionId** y **ActiveDirectoryTenantId** por sus identificadores de inquilino y de suscripción de Azure. Para obtener estos valores, ejecute el siguiente cmdlet de Azure PowerShell:

		Get-AzureAccount

5. Agregue las siguientes instrucciones **using** al archivo de origen (Program.cs) en el proyecto:

		using System;
		using System.Configuration;
		using System.Threading;
		using Microsoft.Azure;
		using Microsoft.Azure.Management.StreamAnalytics;
		using Microsoft.Azure.Management.StreamAnalytics.Models;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;

6.	Agregue un método de autenticación auxiliar:

		public static string GetAuthorizationHeader()
		{
		    AuthenticationResult result = null;
		    var thread = new Thread(() =>
		    {
		        try
		        {
		            var context = new AuthenticationContext(
						ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
						ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

		            result = context.AcquireToken(
		                resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
		                clientId: ConfigurationManager.AppSettings["AsaClientId"],
		                redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
		                promptBehavior: PromptBehavior.Always);
		        }
		        catch (Exception threadEx)
		        {
		            Console.WriteLine(threadEx.Message);
		        }
		    });

		    thread.SetApartmentState(ApartmentState.STA);
		    thread.Name = "AcquireTokenThread";
		    thread.Start();
		    thread.Join();

		    if (result != null)
		    {
		        return result.AccessToken;
		    }

		    throw new InvalidOperationException("Failed to acquire token");
		}  


## Cree un cliente de administración de Análisis de transmisiones

Un objeto **StreamAnalyticsManagementClient** le permite administrar el trabajo y los componentes del trabajo, como la entrada, la salida y la transformación.

Agregue el siguiente código al comienzo del método **Main**:

	string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
	string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
	string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
	string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
	string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";

	// Get authentication token
	TokenCloudCredentials aadTokenCredentials =
	    new TokenCloudCredentials(
	        ConfigurationManager.AppSettings["SubscriptionId"],
	        GetAuthorizationHeader());

	// Create Stream Analytics management client
	StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);

El valor de la variable **resourceGroupName** debe ser el mismo que el nombre del grupo de recursos que creó o eligió en los pasos de requisitos previos.

Para automatizar el aspecto de la presentación de credenciales de creación del trabajo, consulte [Autenticación de una entidad de servicio con el Administrador de recursos de Azure](../resource-group-authenticate-service-principal.md).

Las secciones restantes de este artículo suponen que este código se encuentra al comienzo del método **Main**.

## Creación de un trabajo de Análisis de transmisiones

El siguiente código crea un trabajo de Análisis de transmisiones bajo el grupo de recursos que ha definido. Agregará una entrada, salida y transformación al trabajo más adelante.

	// Create a Stream Analytics job
	JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
	{
	    Job = new Job()
	    {
	        Name = streamAnalyticsJobName,
	        Location = "<LOCATION>",
	        Properties = new JobProperties()
	        {
	            EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Adjust,
	            Sku = new Sku()
	            {
	                Name = "Standard"
	            }
	        }
	    }
	};

	JobCreateOrUpdateResponse jobCreateResponse = client.StreamingJobs.CreateOrUpdate(resourceGroupName, jobCreateParameters);


## Creación de un origen de entrada de Análisis de transmisiones

El código siguiente crea un origen de entrada de Análisis de transmisiones con el tipo de origen de entrada de blob y la serialización de CSV. Para crear un origen de entrada de centro de eventos, use **EventHubStreamInputDataSource** en lugar de **BlobStreamInputDataSource**. De manera similar, puede personalizar el tipo de serialización del origen de entrada.

	// Create a Stream Analytics input source
	InputCreateOrUpdateParameters jobInputCreateParameters = new InputCreateOrUpdateParameters()
	{
	    Input = new Input()
	    {
	        Name = streamAnalyticsInputName,
	        Properties = new StreamInputProperties()
	        {
	            Serialization = new CsvSerialization
	            {
	                Properties = new CsvSerializationProperties
	                {
	                    Encoding = "UTF8",
	                    FieldDelimiter = ","
	                }
	            },
	            DataSource = new BlobStreamInputDataSource
	            {
	                Properties = new BlobStreamInputDataSourceProperties
	                {
	                    StorageAccounts = new StorageAccount[]
	                    {
	                        new StorageAccount()
	                        {
	                            AccountName = "<YOUR STORAGE ACCOUNT NAME>",
	                            AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
	                        }
	                    },
	                    Container = "samples",
	                    PathPattern = ""
	                }
	            }
	        }
	    }
	};

	InputCreateOrUpdateResponse inputCreateResponse =
		client.Inputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobInputCreateParameters);

Los orígenes de entrada, ya sean desde el almacenamiento de blobs o un centro de eventos, están vinculados a un trabajo específico. Para usar el mismo origen de entrada para distintos trabajos, debe llamar nuevamente al método y especificar un nombre de trabajo distinto.


## Prueba del origen de entrada de Análisis de transmisiones

El método **TestConnection** prueba si el trabajo de Análisis de transmisiones puede conectarse al origen de entrada así como otros aspectos específicos para el tipo de origen de entrada. Por ejemplo, en el origen de entrada de blob que creó en un paso anterior, el método comprobará que el par de claves y el nombre de cuenta de almacenamiento se pueden usar para conectarse a la cuenta de almacenamiento, así como para comprobar que existe el contenedor especificado.

	// Test input source connection
	DataSourceTestConnectionResponse inputTestResponse =
		client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## Creación de un destino de salida de Análisis de transmisiones

La creación de un destino de salida es muy similar a crear un origen de entrada de Análisis de transmisiones. Al igual que los orígenes de entrada, los destinos de salida están vinculados a un trabajo específico. Para usar el mismo destino de salida para distintos trabajos, debe llamar nuevamente al método y especificar un nombre de trabajo distinto.

El siguiente código crea un destino de salida (Base de datos SQL de Azure). Puede personalizar el tipo de datos y/o el tipo de serialización del destino de salida.

	// Create a Stream Analytics output target
	OutputCreateOrUpdateParameters jobOutputCreateParameters = new OutputCreateOrUpdateParameters()
	{
	    Output = new Output()
	    {
	        Name = streamAnalyticsOutputName,
	        Properties = new OutputProperties()
	        {
	            DataSource = new SqlAzureOutputDataSource()
	            {
	                Properties = new SqlAzureOutputDataSourceProperties()
	                {
	                    Server = "<YOUR DATABASE SERVER NAME>",
	                    Database = "<YOUR DATABASE NAME>",
	                    User = "<YOUR DATABASE LOGIN>",
	                    Password = "<YOUR DATABASE LOGIN PASSWORD>",
	                    Table = "<YOUR DATABASE TABLE NAME>"
	                }
	            }
	        }
	    }
	};

	OutputCreateOrUpdateResponse outputCreateResponse =
		client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);

## Prueba de un destino de salida de Análisis de transmisiones

Un destino de salida de Análisis de transmisiones también tiene el método **TestConnection** para probar conexiones.

	// Test output target connection
	DataSourceTestConnectionResponse outputTestResponse =
		client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## Creación de una transformación de Análisis de transmisiones

El siguiente código crea una transformación de Análisis de transmisiones con la consulta "select * from Input" y especifica para asignar una unidad de streaming para el trabajo de Análisis de transmisiones. Para obtener más información sobre el ajuste de las unidades de streaming, consulte [Escalación de trabajos de Análisis de transmisiones](stream-analytics-scale-jobs.md).


	// Create a Stream Analytics transformation
	TransformationCreateOrUpdateParameters transformationCreateParameters = new TransformationCreateOrUpdateParameters()
	{
	    Transformation = new Transformation()
	    {
	        Name = streamAnalyticsTransformationName,
	        Properties = new TransformationProperties()
	        {
	            StreamingUnits = 1,
	            Query = "select * from Input"
	        }
	    }
	};

	var transformationCreateResp =
		client.Transformations.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, transformationCreateParameters);

Al igual que la entrada y la salida, una transformación también está vinculada al trabajo de Análisis de transmisiones específico bajo el que se creó.

## Inicio de un trabajo de Análisis de transmisiones
Después de crear un trabajo de Análisis de transmisiones y sus entradas, salidas y transformaciones, puede iniciar el trabajo si llama al método **Start**.

El siguiente código de ejemplo inicia un trabajo de Análisis de transmisiones con una hora de inicio de salida personalizada definida para el 12 de diciembre de 2012, 12:12:12 UTC:

	// Start a Stream Analytics job
	JobStartParameters jobStartParameters = new JobStartParameters
	{
	    OutputStartMode = OutputStartMode.CustomTime,
	    OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
	};

	LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);



## Detención de un trabajo de Análisis de transmisiones
Puede detener un trabajo de Análisis de transmisiones en ejecución si llama al método **Stop**.

	// Stop a Stream Analytics job
	LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## Eliminación de un trabajo de Análisis de transmisiones
El método **Delete** eliminará el trabajo, además de los subrecursos subyacentes, incluidas las entradas, salidas y transformaciones del trabajo.

	// Delete a Stream Analytics job
	LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);


## Obtención de soporte técnico
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=AzureStreamAnalytics).


## Pasos siguientes

Ha aprendido los conceptos básicos del uso de un SDK de .NET para crear y ejecutar trabajos de análisis. Para obtener más información, consulte:

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [SDK de .NET de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn889315.aspx)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!---HONumber=AcomDC_0218_2016-->