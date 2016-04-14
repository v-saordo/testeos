<properties
	pageTitle="Creación de un centro de IoT mediante una plantilla del Administrador de recursos de Azure y C# | Microsoft Azure"
	description="Siga este tutorial para empezar a usar las plantillas del Administrador de recursos para crear un Centro de IoT con un programa de C#."
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

# Creación de un Centro de IoT mediante un programa de C# con una plantilla del Administrador de recursos de Azure

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## Introducción

Puede usar el Administrador de recursos de Azure para crear y administrar Centros de IoT de Azure mediante programación. En este tutorial se muestra cómo usar una plantilla del administrador de recursos para crear un centro de IoT desde un programa de C#.

> [AZURE.NOTE] Azure tiene dos modelos de implementación diferentes para crear y trabajar con recursos: [el Administrador de recursos y el clásico](../resource-manager-deployment-model.md). Este artículo trata sobre el uso del modelo de implementación del Administrador de recursos.

Para completar este tutorial, necesitará lo siguiente:

- Microsoft Visual Studio 2015.
- Una cuenta de Azure activa. <br/>En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][lnk-free-trial].
- Una [cuenta de almacenamiento de Azure][lnk-storage-account] donde se puedan almacenar los archivos de plantilla.
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] o versiones posteriores.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## Preparar su proyecto de Visual Studio

1. En Visual Studio, cree un nuevo proyecto de Windows de Visual C# con la plantilla de proyecto **Aplicación de consola**. Asigne al proyecto el nombre **CreateIoTHub**.

2. En el Explorador de soluciones, haga clic con el botón secundario en su proyecto y luego haga clic en **Administrar paquetes de NuGet**.

3. En el Administrador de paquetes de NuGet, active **Incluir versión preliminar** y busque **Microsoft.Azure.Management.Resources**. Haga clic en **Instalar**, en **Revisar cambios**, haga clic en **Aceptar** y, luego, en **Acepto** para aceptar las licencias.

4. En el Administrador de paquetes de NuGet, busque **Microsoft.IdentityModel.Clients.ActiveDirectory**. Haga clic en **Instalar**, en **Revisar cambios**, haga clic en **Aceptar** y, luego, en **Acepto** para aceptar la licencia.

5. En el Administrador de paquetes de NuGet, busque **Microsoft.Azure.Common**. Haga clic en **Instalar**, en **Revisar cambios**, haga clic en **Aceptar** y, luego, en **Acepto** para aceptar las licencias.

6. En Program.cs, reemplace las instrucciones **using** existentes por las siguientes:

    ```
    using System;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
    
7. En Program.cs, agregue las siguientes variables estáticas reemplazando los valores de marcador de posición. Realizó una nota de **ApplicationId**, **SubscriptionId**, **TenantId** y **Password** anteriormente en este tutorial. **El nombre de la cuenta de almacenamiento** es el nombre de la cuenta de almacenamiento de Azure donde almacenará los archivos de la plantilla. El **Nombre de grupo de recursos** es el nombre del grupo de recursos que usará al crear el Centro de IoT, puede ser un grupo de recursos existente u otro nuevo. El **Nombre de la implementación** es un nombre para la implementación, como **Deployment\_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## Enviar una plantilla para crear un centro de IoT

Use una plantilla de JSON y un archivo de parámetro para crear un nuevo Centro de IoT en el grupo de recursos. También puede usar la plantilla para realizar cambios en un centro de IoT existente.

1. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto, haga clic en **Agregar** y luego en **Nuevo elemento**. Agregue un nuevo archivo JSON denominado **template.json** a su proyecto.

2. Reemplace el contenido de **template.json** por la siguiente definición de recursos para agregar un nuevo centro de IoT estándar a la región **Este de EE.UU.**:

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto, haga clic en **Agregar** y luego en **Nuevo elemento**. Agregue un nuevo archivo JSON denominado **parameters.json** a su proyecto.

4. Reemplace el contenido de **parameters.json** por la siguiente información de parámetro que establece el nombre del nuevo Centro de IoT en **mynewiothub**:

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```

5. En el **Explorador de servidores**, conéctese a su suscripción de Azure y, en su cuenta de almacenamiento, cree un contenedor nuevo denominado **plantillas**. En el panel **Propiedades**, establezca los permisos de **Acceso de lectura público** para el contenedor **plantillas** en **Blob**.

6. En el **Explorador de servidores**, haga clic con el botón derecho en el contenedor **plantillas** y luego haga clic en **Ver contenedor de blob**. Haga clic en el botón **Cargar blob**, seleccione los dos archivos, **parameters.json** y **templates.json**, y luego haga clic en **Abrir** para cargar los archivos JSON en el contenedor **plantillas**. Las direcciones URL de los blobs que contienen los datos de JSON son:

    ```
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.windows.net/templates/template.json
    ```

7. Agregue el método siguiente a Program.cs:
    
    ```
    static void CreateIoTHub(ResourceManagementClient client)
    {
        
    }
    ```

5. Agregue el código siguiente al método **CreateIoTHub** para enviar los archivos de plantilla y parámetro al Administrador de recursos de Azure:

    ```
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

6. Agregue el código siguiente al método **CreateIoTHub** que muestra el estado y las claves del nuevo Centro de IoT:

    ```
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## Completar y ejecutar la aplicación

Ahora puede completar la aplicación llamando al método **CreateIoTHub** antes de compilarla y ejecutarla.

1. Agregue el siguiente código al final del método **Main**:

    ```
    CreateIoTHub(client);
    Console.ReadLine();
    ```
    
2. Haga clic en **Compilar** y luego en **Compilar solución**. Corrija los errores.

3. Haga clic en **Depurar** y luego en **Iniciar depuración** para ejecutar la aplicación. La ejecución de la implementación puede tardar varios minutos en completarse.

4. Para comprobar que su aplicación ha agregado el nuevo centro de IoT, visite el [portal][lnk-azure-portal] y vea la lista de recursos o use el cmdlet de PowerShell **Get-AzureRmResource**.

> [AZURE.NOTE] Esta aplicación de ejemplo agrega un centro de IoT estándar S1 por el que se le cobrará. Puede eliminar el centro de IoT a través del [portal][lnk-azure-portal] o mediante el cmdlet **Remove-AzureRmResource** de PowerShell cuando haya terminado.

## Pasos siguientes

Ahora que ha implementado un Centro de IoT mediante una plantilla del Administrador de recursos de Azure con un programa de C#, quizá desee seguir explorando:

- Lea sobre las capacidades de la API de REST en [IoT Hub Resource Provider REST API][lnk-rest-api] (API de REST del proveedor de recursos del centro de IoT).
- Lea la [Información general de Administrador de recursos de Azure][lnk-azure-rm-overview] para más información sobre las capacidades del Administrador de recursos de Azure.

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md
[lnk-storage-account]: ../storage/storage-create-storage-account.md

<!---HONumber=AcomDC_0218_2016-->