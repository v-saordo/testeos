<properties 
	pageTitle="Supervisión de trabajos de Análisis de transmisiones mediante programación | Microsoft Azure" 
	description="Obtenga información sobre cómo supervisar los trabajos de Análisis de transmisiones creados a través de las API de REST, el SDK de Azure o Powershell."
	keywords="supervisión de .net monitor, supervisión de trabajos, aplicación de supervisión"
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
	ms.date="02/04/2016" 
	ms.author="jeffstok"/>


# Creación de supervisión de trabajos de Análisis de transmisiones mediante programación
 En este artículo se demuestra cómo habilitar la supervisión de un trabajo de Análisis de transmisiones. Los trabajos de Análisis de transmisiones creados a través de las API de REST, el SDK de Azure o Powershell no tienen habilitada la supervisión de forma predeterminada. Puede habilitarla manualmente en el Portal de Azure navegando hasta la página Supervisión del trabajo y haciendo clic en Habilitar, o bien puede automatizar este proceso siguiendo los pasos que se describen en este artículo. Los datos de supervisión se mostrarán en la pestaña "Supervisión" en el Portal de Azure para el trabajo de Análisis de transmisiones.

![Pestaña Trabajos de Supervisión de trabajos](./media/stream-analytics-monitor-jobs/stream-analytics-monitor-jobs-tab.png)

## Requisitos previos
Antes de empezar este artículo, debe tener lo siguiente:

- Visual Studio 2012 o 2013
- Descargue e instale el [SDK de .NET de Azure](https://azure.microsoft.com/downloads/).
- Un trabajo de Análisis de transmisiones existente que requiera la habilitación de supervisión.

## Configuración de un proyecto

1.	Cree una aplicación de consola .NET de Visual Studio C#.
2.	En la consola del administrador de paquetes, ejecute los siguientes comandos para instalar los paquetes NuGet. El primero es el SDK de .NET de administración de Análisis de transmisiones de Azure. El segundo es el SDK de Azure Insights que se usará para habilitar la supervisión. El último es el cliente de Azure Active Directory que se usará para autenticación.

    ```
    Install-Package Microsoft.Azure.Management.StreamAnalytics
    Install-Package Microsoft.Azure.Insights -Pre
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

3.	Agregue la siguiente sección appSettings al archivo App.config:

    ```
    <appSettings>
    	<!--CSM Prod related values-->
    	<add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
    	<add key="JobName" value="YOUR JOB NAME" />
    	<add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
    	<add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
    	<add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
    	<add key="WindowsManagementUri" value="https://management.core.windows.net/" />
    	<add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
    	<add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
    	<add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
    	<add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
    </appSettings>
	```
Reemplace los valores para *SubscriptionId* y *ActiveDirectoryTenantId* por sus identificadores de inquilino y de suscripción de Azure. Para obtener estos valores, ejecute el siguiente cmdlet de PowerShell:

    ```
    Get-AzureAccount
    ```
4.	Agregue las siguientes instrucciones using al archivo de origen (Program.cs) en el proyecto. 

    ```
        using System;
        using System.Configuration;
        using System.Threading;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.Insights;
        using Microsoft.Azure.Management.Insights.Models;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```
5.	Agregue un método auxiliar de autenticación:

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

## Creación de clientes de administración
El código siguiente configurará las variables y los clientes de administración necesarios.

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
    	new TokenCloudCredentials(
    		ConfigurationManager.AppSettings["SubscriptionId"],
    		GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);

## Habilitación de supervisión para un trabajo de Análisis de transmisiones existente

El código siguiente habilitará la supervisión de un trabajo de Análisis de transmisiones **existente**. La primera parte del código realiza una solicitud GET en el servicio Análisis de transmisiones para recuperar información sobre el trabajo de Análisis de transmisiones en concreto. Usa la propiedad "Id" (recuperada de la solicitud GET) como parámetro del método Put en la segunda mitad del código que envía una solicitud PUT al servicio Insights para habilitar la supervisión para el trabajo de Análisis de transmisiones.

> [AZURE.WARNING]
Si previamente ha habilitado la supervisión de otro trabajo de Análisis de transmisiones, a través del Portal de Azure o mediante programación con el siguiente código, **es recomendable proporcionar el mismo nombre de cuenta de almacenamiento que indicó cuando habilitó anteriormente la supervisión.**
> 
> La cuenta de almacenamiento está vinculada a la región en la que se ha creado el trabajo de Análisis de transmisiones, no específicamente al trabajo.
> 
> Todos los trabajos de Análisis de transmisiones (y todos los demás recursos de Azure) de esa misma región comparten esta cuenta de almacenamiento para almacenar los datos de supervisión. Si proporciona otra cuenta de almacenamiento, puede provocar efectos secundarios no deseados en la supervisión de sus otros trabajos de Análisis de transmisiones y otros recursos de Azure.
> 
> El nombre de la cuenta de almacenamiento utilizado para reemplazar ```“<YOUR STORAGE ACCOUNT NAME>”``` a continuación debe ser una cuenta de almacenamiento que esté en la misma suscripción que el trabajo de Análisis de transmisiones para el que está habilitando la supervisión.

    // Get an existing Stream Analytics job
    JobGetParameters jobGetParameters = new JobGetParameters()
    {
    	PropertiesToExpand = "inputs,transformation,outputs"
    };
    JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);

    // Enable monitoring
    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
    {
    		Properties = new ServiceDiagnosticSettings()
    		{
        		StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
    		}
    };
    insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);



## Obtenga soporte técnico
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=AzureStreamAnalytics).


## Pasos siguientes

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=AcomDC_0204_2016-->