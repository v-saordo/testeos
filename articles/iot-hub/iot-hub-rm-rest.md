<properties
	pageTitle="Crear un centro de IoT mediante la API de REST | Microsoft Azure"
	description="Siga este tutorial para empezar a usar la API de REST para crear un Centro de IoT."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="02/12/2016"
     ms.author="dobett"/>

# Tutorial: Crear un Centro de IoT con un programa de C# y la API de REST

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## Introducción

Puede usar la [API de REST del proveedor de recursos del Centro de IoT][lnk-rest-api] para crear y administrar los centros de IoT de Azure mediante programación. En este tutorial se muestra cómo usar la API de REST del proveedor de recursos para crear un centro de IoT desde un programa de C#.

> [AZURE.NOTE] Azure tiene dos modelos de implementación diferentes para crear y trabajar con recursos: [el Administrador de recursos y el clásico](../resource-manager-deployment-model.md). Este artículo trata sobre el uso del modelo de implementación del Administrador de recursos.

Para completar este tutorial, necesitará lo siguiente:

- Microsoft Visual Studio 2015.
- Una cuenta de Azure activa. <br/>En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][lnk-free-trial].
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] o versiones posteriores.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## Preparar su proyecto de Visual Studio

1. En Visual Studio, cree un nuevo proyecto de Windows de Visual C# con la plantilla de proyecto **Aplicación de consola**. Asigne al proyecto el nombre **CreateIoTHubREST**.

2. En el Explorador de soluciones, haga clic con el botón secundario en su proyecto y luego haga clic en **Administrar paquetes de NuGet**.

3. En el Administrador de paquetes de NuGet, active **Incluir versión preliminar** y busque **Microsoft.Azure.Management.Resources**. Haga clic en **Instalar**, en **Revisar cambios**, haga clic en **Aceptar** y, luego, en **Acepto** para aceptar las licencias.

4. En el Administrador de paquetes de NuGet, busque **Microsoft.IdentityModel.Clients.ActiveDirectory**. Haga clic en **Instalar**, en **Revisar cambios**, haga clic en **Aceptar** y, luego, en **Acepto** para aceptar la licencia.

6. En Program.cs, reemplace las instrucciones **using** existentes por las siguientes:

    ```
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    using Newtonsoft.Json;
    ```
    
7. En Program.cs, agregue las siguientes variables estáticas reemplazando los valores de marcador de posición. Realizó una nota de **ApplicationId**, **SubscriptionId**, **TenantId** y **Password** anteriormente en este tutorial. El **Nombre de grupo de recursos** es el nombre del grupo de recursos que usará al crear el centro de IoT, puede ser un grupo de recursos existente u otro nuevo. El **nombre del Centro de IoT** es el nombre del Centro de IoT que creará, como **MyIoTHub**. El **Nombre de la implementación** es un nombre para la implementación, como **Deployment\_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## Usar la API de REST para crear un centro de IoT

Use la [API de REST del Centro de IoT][lnk-rest-api] para crear un nuevo Centro de IoT en su grupo de recursos. También puede usar la API de REST para hacer cambios en un Centro de IoT existente.

1. Agregue el método siguiente a Program.cs:
    
    ```
    static void CreateIoTHub(string token)
    {
        
    }
    ```

2. Agregue el código siguiente al método **CreateIoTHub** para crear un objeto **HttpClient** con un token de autenticación en los encabezados:

    ```
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Agregue el siguiente código al método **CreateIoTHub** para describir el centro de IoT para crear y generar una representación JSON:

    ```
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };
    
    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. Agregue el código siguiente al método **CreateIoTHub** para enviar la solicitud de REST a Azure, comprobar la respuesta y recuperar la dirección URL con la que se puede supervisar el estado de la tarea de implementación:

    ```
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;
      
    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }
    
    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. Agregue el código siguiente al final del método **CreateIoTHub** para utilizar la dirección **asyncStatusUri** recuperada en el paso anterior y esperar a que la implementación se complete:

    ```
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{"Status":"Running"}");
    ```

6. Agregue el código siguiente al final del método **CreateIoTHub** para recuperar las claves del Centro de IoT que ha creado e imprimirlas en la consola:

    ```
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2015-08-15-preview", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;
    
    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```
    
## Completar y ejecutar la aplicación

Ahora puede completar la aplicación llamando al método **CreateIoTHub** antes de compilarla y ejecutarla.

1. Agregue el siguiente código al final del método **Main**:

    ```
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```
    
2. Haga clic en **Compilar** y luego en **Compilar solución**. Corrija los errores.

3. Haga clic en **Depurar** y luego en **Iniciar depuración** para ejecutar la aplicación. La ejecución de la implementación puede tardar varios minutos en completarse.

4. Para comprobar que su aplicación ha agregado el nuevo centro de IoT, visite el [portal][lnk-azure-portal] y vea la lista de recursos o use el cmdlet de PowerShell **Get-AzureRmResource**.

> [AZURE.NOTE] Esta aplicación de ejemplo agrega un centro de IoT estándar S1 por el que se le cobrará. Puede eliminar el centro de IoT a través del [portal][lnk-azure-portal] o mediante el cmdlet **Remove-AzureRmResource** de PowerShell cuando haya terminado.

## Pasos siguientes

Ahora que ha implementado un Centro de IoT mediante la API de REST, quizá desee seguir explorando:

- Explore las capacidades de la [API de REST del proveedor de recursos del centro de IoT][lnk-rest-api].
- Lea la [Información general de Administrador de recursos de Azure][lnk-azure-rm-overview] para más información sobre las capacidades del Administrador de recursos de Azure.

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md

<!---HONumber=AcomDC_0218_2016-->