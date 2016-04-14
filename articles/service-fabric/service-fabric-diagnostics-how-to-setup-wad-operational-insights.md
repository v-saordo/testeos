<properties
   pageTitle="Recopilación de registros con Diagnósticos y Visión operativa de Azure| Microsoft Azure"
   description="En este artículo se describe cómo configurar Diagnósticos y Visión operativa de Azure para recopilar registros de un clúster de Service Fabric que se ejecute en Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/12/2016"
   ms.author="toddabel"/>


# Recopilación de registros desde un clúster de Service Fabric mediante Diagnósticos y Visión operativa de Azure

Cuando se ejecuta un clúster de Azure Service Fabric, es conveniente recopilar los registros de todos los nodos en una ubicación central. La presencia de los registros en una ubicación central facilita el análisis y la solución de los problemas del clúster o de las aplicaciones y los servicios que se ejecutan en ese clúster. Uno de los métodos para cargar y recopilar registros es usar la extensión de Diagnósticos de Azure que carga los registros en Almacenamiento de Azure.

Visión operativa de Azure (parte de Microsoft Operations Management Suite) es una solución SaaS que facilita el análisis y la búsqueda de registros. Los pasos siguientes describen cómo puede configurar la extensión de Diagnósticos de Azure en las máquinas virtuales de un clúster para cargar registros a un almacén central y después configurar Visión operativa para extraerlos y poder verlos en el portal de Visión operativa.

Visión operativa identifica los orígenes de los diferentes tipos de registros que se cargan desde un clúster de Service Fabric por los nombres de las tablas de almacenamiento en las que se encuentran. Esto significa que la extensión de diagnósticos de Azure tiene que estar configurada para cargar los registros en tablas de almacenamiento con nombres que coincidan con lo que Visión operativa va a buscar. Los ejemplos de valores de configuración en este documento muestran los nombres que deben tener las tablas de almacenamiento.

## Lectura recomendada
* [Diagnósticos de Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) (relacionado con los Servicios en la nube de Azure, aunque con información y ejemplos adecuados)
* [Visión operativa](https://azure.microsoft.com/services/operational-insights/)
* [Administrador de recursos de Azure](https://azure.microsoft.com/resource-group-overview/)

## Requisitos previos
Estas herramientas se usarán para realizar algunas de las operaciones en este documento: * [Azure PowerShell](https://azure.microsoft.com/powershell-install-configure/) * [Cliente del Administrador de recursos de Azure](https://github.com/projectkudu/ARMClient)

## Diferentes orígenes de registros que podría recopilar
1. **Registros de Service Fabric:** emitidos por la plataforma a los canales ETW y EventSource estándar. Los registros pueden ser de uno de los varios tipos que hay:
  - Eventos operativos: se trata de registros para las operaciones realizadas por la plataforma Service Fabric. Algunos ejemplos son la creación de aplicaciones y servicios, los cambios de estado de nodo y la información sobre la actualización.
  - [Eventos del modelo de programación de actor](service-fabric-reliable-actors-diagnostics.md)
  - [Eventos del modelo de programación de Reliable Services](service-fabric-reliable-services-diagnostics.md)
2. **Eventos de aplicaciones:** son los eventos que se emiten desde el código de los servicios y que se escriben mediante la clase auxiliar EventSource proporcionada en las plantillas de Visual Studio. Para obtener más información sobre cómo escribir registros de la aplicación, consulte [este artículo sobre la supervisión y diagnóstico de servicios en una configuración de equipo local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).


## Implementación de la extensión de Diagnósticos en un clúster de Service Fabric para recopilar y cargar registros
El primer paso para recopilar registros será implementar la extensión WAD en cada una de las máquinas virtuales del clúster de Service Fabric. La extensión de Diagnósticos recopila registros en cada máquina virtual y los carga a la cuenta de almacenamiento que especifique. Los pasos varían ligeramente en función de si se usa el Portal de Azure o el Administrador de recursos de Azure, y de si se realiza la implementación como parte de la creación del clúster o para un clúster que ya existe. Echemos un vistazo a los pasos para cada escenario.

### Implementación de la extensión de Diagnósticos como parte de la creación del clúster a través del portal
Para implementar Diagnósticos en las máquinas virtuales del clúster como parte de la creación del mismo, se usa la configuración de Diagnósticos mostrada en la imagen siguiente. Está **activada** de forma predeterminada. ![Configuración de Diagnósticos en el portal para crear un clúster](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/portal-cluster-creation-diagnostics-setting.png)

### Implementación de la extensión de Diagnósticos como parte de la creación del clúster a través del Administrador de recursos de Azure
Para crear un clúster mediante el Administrador de recursos, tiene que agregar el JSON de la configuración de Diagnósticos a la plantilla del Administrador de recursos del clúster completo antes de crear el clúster. Dentro de los ejemplos de plantillas del Administrador de recursos, proporcionamos una plantilla de ejemplo del Administrador de recursos de clúster de cinco máquinas virtuales con la configuración de Diagnósticos añadida. Puede verlo en: [Ejemplo de plantilla de clúster de cinco nodos con el Administrador de recursos de Diagnósticos](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-1-nodetype-wad) en la galería de ejemplos de Azure.

Para ver la configuración de Diagnósticos en la plantilla del Administrador de recursos, busque **WadCfg.** Para crear un clúster con esta plantilla, basta con presionar el botón **Implementar en Azure** disponible en el vínculo anterior. También puede descargar el ejemplo del Administrador de recursos, modificarlo y crear un clúster con la plantilla modificada mediante el comando `New-AzureResourceGroupDeployment` en una ventana de Azure PowerShell. Consulte la información a continuación para los parámetros que necesitará pasar al comando.

Además, antes de llamar a este comando de implementación, es posible que tenga que llevar a cabo alguna tarea de configuración, como agregar su cuenta de Azure (`Add-AzureAccount`), elegir una suscripción (`Select-AzureSubscription`), cambiar al modo del Administrador de recursos (`Switch-AzureMode AzureResourceManager`) y crear el grupo de recursos si aún no lo hizo (`New-AzureResourceGroup`).

```powershell

New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploywadarm"></a>Implementación de la extensión de Diagnósticos en un clúster existente
Si tiene un clúster existente que no tiene Diagnósticos implementado, puede agregarlo siguiendo estos pasos. Cree dos archivos WadConfigUpdate.json y WadConfigUpdateParams.json con el siguiente JSON.

##### WadConfigUpdate.json

```json

{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",

  "parameters": {
        "vmNamePrefix": {
      "type": "string"
    },
        "applicationDiagnosticsStorageAccountName": {
      "type": "string"
    },
        "vmCount": {
            "type": "int"
    }
  },

  "resources": [
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(parameters('vmNamePrefix'),copyIndex(0),'/Microsoft.Insights.VMDiagnosticsSettings')]",
            "location": "[resourceGroup().location]",
      "properties": {
        "type": "IaaSDiagnostics",
                "typeHandlerVersion": "1.5",
        "publisher": "Microsoft.Azure.Diagnostics",
                "autoUpgradeMinorVersion": true,
        "settings": {
                    "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                            "overallQuotaInMB": "50000",
                "EtwProviders": {
                    "EtwEventSourceProviderConfiguration": [
                        {
                            "provider": "Microsoft-ServiceFabric-Actors",
                            "scheduledTransferKeywordFilter": "1",
                                        "scheduledTransferPeriod": "PT5M",
                            "DefaultEvents": { "eventDestination": "ServiceFabricReliableActorEventTable" }
                        },
                        {
                            "provider": "Microsoft-ServiceFabric-Services",
                            "DefaultEvents": { "eventDestination": "ServiceFabricReliableServiceEventTable" },
                                        "scheduledTransferPeriod": "PT5M"
                        }
                    ],
                    "EtwManifestProviderConfiguration": [
                        {
                            "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                            "scheduledTransferKeywordFilter": "4611686018427387904",
                                        "scheduledTransferLogLevelFilter": "Information",
                                        "scheduledTransferPeriod": "PT5M",
                            "DefaultEvents": { "eventDestination": "ServiceFabricSystemEventTable" }
                        }
                    ]
                }
            }
    },
                    "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountNamee')]"
                },
                "protectedSettings": {
                    "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                    "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                    "storageAccountEndPoint": "https://core.windows.net/"
                }
            },
            "copy": {
                "name": "vmExtensionLoop",
                "count": "[parameters('vmCount')]"
            }
        }
    ],

    "outputs": {
    }
}
```

##### WadConfigUpdateParams.json
Reemplace vmNamePrefix con el prefijo que eligió para los nombres de máquina virtual al crear el clúster. A continuación, modifique vmStorageAccountName para que sea la cuenta de almacenamiento en la que desea cargar los registros de las máquinas virtuales.

```json

{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmNamePrefix": {
            "value": "VM"
        },
        "applicationDiagnosticsStorageAccountName": {
            "value": "testdiagacc"
        },
        "vmCount": {
            "value": 5
        }
    }
}
```

Después de crear los archivos JSON como se describió anteriormente, cámbielos por los específicos de su entorno. A continuación, llame al comando siguiente, pasando el nombre del grupo de recursos para el clúster de Service Fabric. Una vez que se ejecuta este comando correctamente, se implementará Diagnósticos en todas las máquinas virtuales y se iniciará la carga de los registros desde el clúster a las tablas de la cuenta de Almacenamiento de Azure especificada.

Además, antes de llamar a este comando de implementación, es posible que tenga que llevar a cabo alguna tarea de configuración, como agregar su cuenta de Azure (`Add-AzureAccount`), elegir la suscripción correcta (`Select-AzureSubscription`) y cambiar al modo del Administrador de recursos (`Switch-AzureMode AzureResourceManager`).

```ps

New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToWADConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

## Configuración de Visión operativa para ver registros de clúster y buscar en ellos
Una vez que Diagnósticos está configurado en el clúster y está cargando los registros a una cuenta de almacenamiento, el paso siguiente consiste en configurar Visión operativa para poder ver, buscar y consultar todos los registros de clúster mediante el portal de Visión operativa.

### Creación de un área de trabajo de Visión operativa
Para ver los pasos para crear un área de trabajo de Visión operativa, consulte el siguiente artículo. Tenga en cuenta que describe dos formas diferentes de crear un área de trabajo. Elija el Portal de Azure y el enfoque basado en suscripción. Necesitará el nombre del área de trabajo de Visión operativa en las secciones posteriores de este documento. Cree el área de trabajo de Visión operativa con la misma suscripción que usó para crear todos los recursos del clúster, incluidas las cuentas de almacenamiento.

[Incorporación a Visión operativa](https://technet.microsoft.com/library/mt484118.aspx)

### Configuración de un área de trabajo de Visión operativa para mostrar los registros de clúster
Una vez creada el área de trabajo de Visión operativa como se describió antes, el siguiente paso consiste en configurar el área de trabajo para que extraiga los registros de las tablas de Almacenamiento de Azure donde la extensión de Diagnósticos los carga desde el clúster. Actualmente, esta configuración no se puede realizar a través del portal de Visión operativa y solo es posible realizarla con comandos de Powershell. Ejecute el siguiente script de PowerShell:

```powershell

    <#
    This script will configure an Operations Management Suite workspace (aka Operational Insights workspace) to read Diagnostics from an Azure Storage account.

    It will enable all supported data types (currently Windows Event Logs, Syslog, Service Fabric Events, ETW Events and IIS Logs).

    It supports both classic and Resource Manager storage accounts.

    If you have more than one OMS workspace you will be prompted for the workspace to configure.

    If you have more than one storage account you will be prompted for which storage account to configure.
    #>

Add-AzureAccount

Switch-AzureMode -Name AzureResourceManager

$validTables = "WADServiceFabric*EventTable", "WADETWEventTable"

function Select-Workspace {

    $workspace = ""

    $allWorkspaces = Get-AzureOperationalInsightsWorkspace  

    switch ($allWorkspaces.Count) {
        0 {Write-Error "No Operations Management Suite workspaces found"}
        1 {return $allWorkspaces}
        default {
            $uiPrompt = "Enter the number corresponding to the workspace you want to configure.`n"

            $count = 1
            foreach ($workspace in $allWorkspaces) {
                $uiPrompt += "$count. " + $workspace.Name + " (" + $workspace.CustomerId + ")`n"
                $count++
            }
            $answer = (Read-Host -Prompt $uiPrompt) - 1
            $workspace = $allWorkspaces[$answer]
        }  
    }
    return $workspace
}

function Select-StorageAccount {

    $storage = ""

    $allStorageAccounts = (get-azureresource -ResourceType Microsoft.ClassicStorage/storageAccounts) + (get-azureresource -ResourceType Microsoft.Storage/storageAccounts)

    switch ($allStorageAccounts.Count) {
        0 {Write-Error "No storage accounts found"}
        1 {$storage = $allStorageAccounts}
        default {

            $uiPrompt = "Enter the number corresponding to the storage account with diagnostics.`n"

            $count = 1
            foreach ($storageAccount in $allStorageAccounts) {
                $uiPrompt += "$count. " + $storageAccount.Name + " (" + $storageAccount.Location + ")`n"
                $count++
            }
            $answer = (Read-Host -Prompt $uiPrompt)

            $storage = $allStorageAccounts[$answer - 1]
        }
    }
    return $storage
}

$workspace = Select-Workspace
$storageAccount = Select-StorageAccount

$insightsName = $storageAccount.Name + $workspace.Name

$existingConfig = ""

try
{
    $existingConfig = Get-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -ErrorAction Stop
}
catch [Hyak.Common.CloudException]
{
    # HTTP Not Found is returned if the storage insight doesn't exist
}

if ($existingConfig) {
    Set-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -Tables $validTables -Containers $validContainers

} else {
    if ($storageAccount.ResourceType -eq "Microsoft.ClassicStorage/storageAccounts") {
        Switch-AzureMode -Name AzureServiceManagement
        $key = (Get-AzureStorageKey -StorageAccountName $storageAccount.Name).Primary
        Switch-AzureMode -Name AzureResourceManager
    } else {
        $key = (Get-AzureStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.Name).Key1
    }
    New-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -StorageAccountResourceId $storageAccount.ResourceId -StorageAccountKey $key -Tables $validTables -Containers $validContainers
}
```

Una vez haya configurado el área de trabajo de Visión operativa para leer desde las tablas de Azure en su cuenta de almacenamiento, inicie sesión en el portal y vaya a la pestaña **almacenamiento** del recurso de Visión operativa. Debería tener un aspecto similar al siguiente: ![Configuración de almacenamiento de Visión operativa en el Portal de Azure](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/oi-connected-tables-list.png)

### Búsqueda y visualización de registros en Visión operativa
Después de configurar el área de trabajo de Visión operativa para que lea los registros desde la cuenta de almacenamiento especificada, pueden pasar unos 10 minutos hasta que los registros aparezcan en la interfaz de usuario de Visión operativa. Para asegurarse de que haya nuevos registros generados, es conveniente implementar una aplicación de Service Fabric en el clúster ya que esto generará eventos operativos desde la plataforma Service Fabric.

1. Para ver los registros, seleccione **Búsqueda de registro** en la página principal del portal de Visión operativa. ![Opción de búsqueda de registros de Visión operativa](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/log-search-button-oi.png)

2. En la página de búsqueda de registros, use la consulta **Type=ServiceFabricOperationalEvent**; debería ver los registros operativos del clúster tal como se muestra a continuación. Para ver todos lo registros del modelo de programación de actor, realice una consulta **Type=ServiceFabricReliableActorEvent**. Para ver todos los registros desde el modelo de programación de Reliable Services, escriba **Type=ServiceFabricReliableServiceEven**. ![Consulta y visualización de registros en Visión operativa](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/view-logs-oi.png)

### Consultas de ejemplo para ayudar a solucionar problemas
Estos son ejemplos de algunos escenarios y las consultas que puede usar para solucionarlos:

1. **Para averiguar si Service Fabric llamá a RunAsync para un servicio concreto:** puede hacer esto si necesita descartar que un servicio se esté bloqueando al iniciar. Para ello, busque con una consulta parecida a la siguiente, pero reemplazando el nombre de la aplicación y el servicio para que coincidan con los que implementó y compruebe si se devuelven resultados.

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateless1" AND "RunAsync has been invoked"
    ```

2. **Si está ejecutando un servicio con estado y quiere ver si hay alguna excepción producida por él que Service Fabric haya marcado como error:** para encontrará esos eventos use una consulta similar a esta:

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateful1" AND TaskName=StatefulRunAsyncFailure
    ```

3. **Para buscar eventos correspondientes a las excepciones producidas por los métodos de actor en todas las aplicaciones y los servicios implementados:** puede usar una consulta como esta:

    ```
    Type=ServiceFabricReliableActorEvent AND TaskName=ActorMethodThrewException
    ```

## Actualización de Diagnósticos para recopilar y cargar registros desde nuevos canales EventSource
Para actualizar Diagnósticos de forma que recopile registros desde canales EventSource nuevos que representen una nueva aplicación que vaya a implementar, basta con seguir los mismos pasos que en la [sección anterior](#deploywadarm), que describe la configuración de Diagnósticos para un clúster existente.

Tendrá que actualizar la sección EtwEventSourceProviderConfiguration en WadConfigUpdate.json para agregar entradas para el nuevo EventSources antes de aplicar la actualización de la configuración mediante el comando del Administrador de recursos. La tabla que se carga será la misma (ETWEventTable), ya que es la que está configurada como aquella desde la que Visión operativa lee eventos ETW de aplicación.


## Pasos siguientes
Revise los eventos de diagnóstico emitidos para [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) y [Reliable Services](service-fabric-reliable-services-diagnostics.md) para comprender más a fondo qué eventos debería examinar durante la solución de problemas.

<!---HONumber=AcomDC_0224_2016-->