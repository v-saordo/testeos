<properties
	pageTitle="Creación de un centro de IoT mediante una plantilla del Administrador de recursos de Azure y PowerShell | Microsoft Azure"
	description="Siga este tutorial para empezar a usar las plantillas del Administrador de recursos para crear un Centro de IoT con PowerShell."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="02/12/2016"
     ms.author="dobett"/>

# Creación de un Centro de IoT con Powershell

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## Introducción

Puede usar el Administrador de recursos de Azure para crear y administrar Centros de IoT de Azure mediante programación. En este tutorial se muestra cómo usar una plantilla del administrador de recursos para crear un centro de IoT desde un programa de C#.

> [AZURE.NOTE] Azure tiene dos modelos de implementación diferentes para crear y trabajar con recursos: [el Administrador de recursos y el clásico](../resource-manager-deployment-model.md). Este artículo trata sobre el uso del modelo de implementación del Administrador de recursos.

Para completar este tutorial, necesitará lo siguiente:

- Una cuenta de Azure activa. <br/>En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][lnk-free-trial].
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] o versiones posteriores.

> [AZURE.TIP] El artículo [Uso de Azure PowerShell con Administrador de recursos de Azure][lnk-powershell-arm] proporciona más información acerca de cómo usar scripts de PowerShell y plantillas del Administrador de recursos de Azure para crear recursos de Azure.

## Conectarse a su suscripción de Azure

En un símbolo del sistema de PowerShell, escriba el siguiente comando para iniciar sesión en su suscripción de Azure:

```
Login-AzureRmAccount
```

Puede usar los comandos siguientes para conocer donde puede implementar un Centro de IoT y las versiones de API admitidas actualmente:

```
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

Cree un grupo de recursos para que contenga su Centro de IoT mediante el siguiente comando en una de las ubicaciones compatibles para el Centro de IoT. En este ejemplo se crea un grupo de recursos denominado **MyIoTRG1**:

```
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## Enviar una plantilla para crear un centro de IoT

Use una plantilla de JSON para crear un nuevo centro de IoT en el grupo de recursos. También puede usar la plantilla para realizar cambios en un centro de IoT existente.

1. Utilice un editor de texto para crear una plantilla del Administrador de recursos de Azure llamada **template.json** con la siguiente definición de recursos a fin de crear un nuevo Centro de IoT estándar. En este ejemplo se agrega el Centro de IoT a la región **Este de EE. UU.** y se usa la versión de API **2016-02-03**. En esta plantilla se espera también que pase el nombre del Centro de IoT como un parámetro denominado **hubName**.

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

2. Guarde el archivo de plantilla en el equipo local. En este ejemplo se da por supuesto que lo guarda en una carpeta denominada **c:\\templates**.

3. Ejecute el comando siguiente para implementar el nuevo Centro de IoT, pasando el nombre de su Centro de IoT como un parámetro. En este ejemplo, el nombre del centro de IoT es **myiothub**:

    ```
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName myiothub
    ```

4. El resultado muestra las claves para el Centro de IoT que ha creado.

5. Para comprobar que su aplicación ha agregado el nuevo centro de IoT, visite el [portal][lnk-azure-portal] y vea la lista de recursos o use el cmdlet de PowerShell **Get-AzureRmResource**.

> [AZURE.NOTE] Esta aplicación de ejemplo agrega un centro de IoT estándar S1 por el que se le cobrará. Puede eliminar el centro de IoT a través del [portal][lnk-azure-portal] o mediante el cmdlet **Remove-AzureRmResource** de PowerShell cuando haya terminado.

## Pasos siguientes

Ahora que ha implementado un Centro de IoT mediante una plantilla del Administrador de recursos de Azure con PowerShell, quizá desee seguir explorando:

- Lea sobre las capacidades de la API de REST en [IoT Hub Resource Provider REST API][lnk-rest-api] (API de REST del proveedor de recursos del centro de IoT).
- Lea la [Información general de Administrador de recursos de Azure][lnk-azure-rm-overview] para más información sobre las capacidades del Administrador de recursos de Azure.

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md
[lnk-powershell-arm]: ../powershell-azure-resource-manager.md

<!---HONumber=AcomDC_0218_2016-->