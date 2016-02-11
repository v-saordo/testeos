<properties
	pageTitle="Clonación de aplicaciones web"
	description="Aprenda a clonar sus aplicaciones web en nuevas aplicaciones web."
	services="app-service\web"
	documentationCenter=""
	authors="ahmedelnably"
	manager="stefsch"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/13/2016"
	ms.author="ahmedelnably"/>

# Clonación de aplicaciones en Servicio de aplicaciones de Azure #

Con el lanzamiento de Microsoft Azure PowerShell versión 1.1.0, se ha agregado una nueva opción a New-AzureRMWebApp que podría proporcionar al usuario la capacidad para clonar una aplicación web existente en una aplicación recién creada de una región diferente o de la misma. Esto permitirá que los clientes implementen fácil y rápidamente varias aplicaciones en diferentes regiones.

La clonación de aplicaciones actualmente solo se admite para los planes de Servicio de aplicaciones de nivel Premium. La nueva característica cuenta con las mismas limitaciones que la característica de copia de seguridad de aplicaciones web; consulte [Hacer copia de seguridad de una aplicación web en el Servicio de aplicaciones de Azure](web-sites-backup.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]


## Clonación de una aplicación existente ##

Escenario: Una aplicación web existente en la región centro-sur de EE. UU.; el usuario desea clonar el contenido en una nueva aplicación web en la región centro-norte de EE. UU. Esto puede realizarse mediante la versión ARM del cmdlet de PowerShell para crear una nueva aplicación web con la opción -SourceWebApp.

Conociendo el nombre del grupo de recursos que contiene la aplicación web de origen, podemos usar el siguiente comando de PowerShell para obtener la información de la aplicación web de origen (en este caso, llamada source-webapp):

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Para crear un nuevo plan de Servicio de aplicaciones, podemos usar el comando New-AzureRmAppServicePlan como en el ejemplo siguiente:

	New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan

Con el comando New-AzureRmWebApp, podemos crear la nueva aplicación web en la región centro-norte de EE. UU. y vincularla a un plan de Servicio de aplicaciones de nivel Premium existente. Además, podemos usar el mismo grupo de recursos que la aplicación web de origen, o bien definir uno nuevo como se muestra a continuación:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp

Para clonar una aplicación web existente, incluidas todas las ranuras de implementación asociadas, el usuario deberá usar el parámetro IncludeSourceWebAppSlots; el siguiente comando de PowerShell muestra el uso de este parámetro con el comando New-AzureRmWebApp:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots $true

Para clonar una aplicación web existente en la misma región, el usuario deberá crear un nuevo grupo de recursos y un nuevo plan de Servicio de aplicaciones en la misma región y después usar el siguiente comando de PowerShell para clonar la aplicación web:

    $destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap

## Clonación de una aplicación existente en un entorno de Servicio de aplicaciones ##

Escenario: Una aplicación web existente en la región centro-sur de EE. UU.; el usuario desea clonar el contenido en una nueva aplicación web en un entorno de Servicio de aplicaciones (ASE) existente.

Conociendo el nombre del grupo de recursos que contiene la aplicación web de origen, podemos usar el siguiente comando de PowerShell para obtener la información de la aplicación web de origen (en este caso, llamada source-webapp):

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Conociendo el nombre del ASE y el nombre del grupo de recursos al que este pertenece, el usuario puede utilizar el comando New-AzureRmWebApp para crear la nueva aplicación web en el ASE existente, como se muestra a continuación:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp

El parámetro Location es necesario por un motivo heredado, pero se pasará por alto cuando se cree una aplicación en un ASE.

## Clonación de una ranura de aplicación existente ##

Escenario: El usuario desea clonar una ranura de aplicación web existente en una nueva aplicación web o una nueva ranura de aplicación web. La nueva aplicación web puede estar en la misma región que la ranura de aplicación web original o en una distinta.

Conociendo el nombre del grupo de recursos que contiene la aplicación web de origen, podemos usar el siguiente comando de PowerShell para obtener la información de la ranura de aplicación web de origen (en este caso, llamada source-webappslot) vinculada a la aplicación web source-webapp:

    $srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot

A continuación, se muestra la creación de un clon de la aplicación web de origen en una nueva aplicación web:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot

## Configuración del Administrador de tráfico durante la clonación de una aplicación ##

Cuando se crea una nueva aplicación web al tiempo que se clona a partir de una existente, tiene la opción de conectar ambas aplicaciones web a un nuevo perfil del Administrador de tráfico o a uno existente. Tenga en cuenta que solo se admite la versión ARM del Administrador de tráfico.

### Creación de un nuevo perfil del Administrador de tráfico durante la clonación de una aplicación ###

Escenario: El usuario desea clonar una aplicación web en otra región, al mismo tiempo que configura un perfil del Administrador de tráfico de ARM que incluya ambas aplicaciones web. Lo siguiente demuestra la creación de un clon de la aplicación web de origen en una nueva aplicación web al tiempo que se configura un nuevo perfil del Administrador de tráfico:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile

### Incorporación de una nueva aplicación web clonada a un perfil del Administrador de tráfico existente ###

Escenario: El usuario ya tiene un perfil del Administrador de tráfico de ARM al que le gustaría agregar ambas aplicaciones web como puntos de conexión. Para hacerlo, primero es necesario ensamblar el identificador del perfil del Administrador de tráfico existente; se necesitará el identificador de la suscripción, el nombre del grupo de recursos y el nombre del perfil del Administrador de tráfico existente.

    $TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"

Después de obtener el identificador del Administrador de tráfico, lo siguiente muestra cómo crear un clon de la aplicación web de origen en una nueva aplicación web al mismo tiempo que se agregan ambas a un perfil del Administrador de tráfico existente:

	$destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID

## Restricciones actuales ##

Esta característica se encuentra actualmente en versión preliminar y estamos trabajando para agregar nueva funcionalidad con el tiempo. En la siguiente lista, se incluyen las restricciones conocidas de la versión actual de la clonación de aplicaciones:

- No se clona la configuración de escalado automático.
- No se clona la configuración de programación de copia de seguridad.
- No se clona la configuración de red virtual.
- No se instala automáticamente App Insights en la aplicación web de destino.
- No se clona la configuración de Easy Auth.
- No se clona la extensión Kudu.
- No se clonan las reglas de TiP.


### Referencias ###
- [Hacer copia de seguridad de una aplicación web en el Servicio de aplicaciones de Azure](web-sites-backup.md)
- [Compatibilidad del Administrador de recursos de Azure con la vista previa del Administrador de tráfico de Azure](../../articles/traffic-manager/traffic-manager-powershell-arm.md)
- [Introducción al entorno del Servicio de aplicaciones](app-service-app-service-environment-intro.md)
- [Uso de Azure PowerShell con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md)

<!---HONumber=AcomDC_0121_2016-->