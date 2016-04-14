<properties
	pageTitle="Administración de Bus de servicio con PowerShell | Microsoft Azure"
	description="Administración de Service Bus con scripts de PowerShell en lugar de .NET"
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/08/2016"
	ms.author="sethm"/>

# Administración de Service Bus con PowerShell

## Información general

Microsoft Azure PowerShell es un eficaz entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. En este artículo se describe cómo usar PowerShell para aprovisionar y administrar entidades de Service Bus, como espacios de nombres, colas y centros de eventos, mediante una consola local de Azure PowerShell.

## Requisitos previos

Antes de empezar este artículo, debe tener lo siguiente:

- Una suscripción de Azure. Azure es una plataforma basada en suscripción. Para obtener más información acerca de cómo obtener una suscripción, consulte [Opciones de compra][], [Ofertas para miembros][] o [Prueba gratuita][].

- Un equipo con Azure PowerShell. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][].

- Conocimientos generales sobre los scripts de PowerShell, paquetes de NuGet y .NET Framework.

## Incluir una referencia al ensamblado .NET para Service Bus

Hay un número limitado de cmdlets de PowerShell disponibles para la administración de Service Bus. Para aprovisionar las entidades que no se exponen a través de los cmdlets existentes, puede usar el cliente de .NET para el Bus de servicio en el [paquete NuGet del Bus de servicio].

En primer lugar, asegúrese de que el script puede encontrar el ensamblado **Microsoft.ServiceBus.dll**, que se instala con el paquete NuGet. Para ser flexible, el script llevará a cabo estos pasos:

1. Determina la ruta de acceso en la que se invocó.
2. Atraviesa la ruta de acceso hasta que encuentra una carpeta denominada `packages`. Esta carpeta se crea al instalar paquetes de NuGet.
3. Se busca de forma recursiva en la carpeta `packages` un ensamblado denominado **Microsoft.ServiceBus.dll**.
4. Hace referencia al ensamblado de modo que los tipos estarán disponibles para su uso posterior.

Aquí se explica cómo se implementan estos pasos en un script de PowerShell:

```
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Output "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## Aprovisionar un espacio de nombres de Service Bus

Dos de los cmdlets de PowerShell admiten operaciones de espacio de nombres del Bus de servicio. En lugar de las API .NET SDK, puede usar [Get-AzureSBNamespace][] y [New-AzureSBNamespace][].

En este ejemplo se crean algunas variables locales en el script: `$Namespace` y `$Location`.

- `$Namespace` es el nombre del espacio de nombres de Service Bus con el que queremos trabajar.
- `$Location` identifica el centro de datos en el que el script aprovisiona el espacio de nombres.
- `$CurrentNamespace` almacena el espacio de nombres de referencia que el script recupera (o crea).

En un script real, `$Namespace` y `$Location` se pueden pasar como parámetros.

Esta parte del script hace lo siguiente:

1. Intenta recuperar un espacio de nombres de Service Bus con el nombre proporcionado.
2. Si se encuentra el espacio de nombres, informa de lo que ha encontrado.
3. Si no se encuentra el espacio de nombres, lo crea y luego recupera el espacio de nombres recién creado.

	```
	$Namespace = "MyServiceBusNS"
	$Location = "West US"
	
	# Query to see if the namespace currently exists
	$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
	
	# Check if the namespace already exists or needs to be created
	if ($CurrentNamespace)
	{
	    Write-Output "The namespace [$Namespace] already exists in the [$($CurrentNamespace.Region)] region."
	}
	else
	{
	    Write-Host "The [$Namespace] namespace does not exist."
	    Write-Output "Creating the [$Namespace] namespace in the [$Location] region..."
	    New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace $false -NamespaceType Messaging
	    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
	    Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
	}
	```

Para aprovisionar otras entidades del Bus de servicio, cree una instancia de la clase [NamespaceManager][] a partir del SDK. Puede usar el cmdlet [Get-AzureSBAuthorizationRule][] para recuperar una regla de autorización que se usa para proporcionar una cadena de conexión. Almacenaremos una referencia a la instancia de `NamespaceManager` en la variable `$NamespaceManager`. Usaremos `$NamespaceManager` más adelante en el script para aprovisionar otras entidades.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## Aprovisionamiento de otras entidades de Service Bus

Para aprovisionar otras entidades, como colas, temas y Centros de eventos, podemos usar la [API de .NET para el Bus de servicio][]. En este artículo se tratan solo los centros de eventos, pero los pasos para otras entidades son similares. Además, al final de este artículo se hace referencia a ejemplos más detallados, incluidas otras entidades.

En esta parte del script se crean cuatro variables locales adicionales. Estas variables se usan para crear una instancia de un objeto `EventHubDescription`. El script hace lo siguiente:

1. Mediante el objeto `NamespaceManager`, compruebe si existe el centro de eventos que se identifica mediante `$Path`.
2. Si no existe, creamos un `EventHubDescription` y lo pasamos al método `CreateEventHubIfNotExists` de la clase `NamespaceManager`.
3. Cuando sepamos que el centro de eventos está disponible, cree un grupo de consumidores mediante `ConsumerGroupDescription` y `NamespaceManager`.

	```
	$Path  = "MyEventHub"
	$PartitionCount = 12
	$MessageRetentionInDays = 7
	$UserMetadata = $null
	$ConsumerGroupName = "MyConsumerGroup"
		
	# Check to see if the Event Hub already exists
	if ($NamespaceManager.EventHubExists($Path))
	{
	    Write-Output "The [$Path] event hub already exists in the [$Namespace] namespace."  
	}
	else
	{
	    Write-Output "Creating the [$Path] event hub in the [$Namespace] namespace: PartitionCount=[$PartitionCount] MessageRetentionInDays=[$MessageRetentionInDays]..."
	    $EventHubDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.EventHubDescription -ArgumentList $Path
	    $EventHubDescription.PartitionCount = $PartitionCount
	    $EventHubDescription.MessageRetentionInDays = $MessageRetentionInDays
	    $EventHubDescription.UserMetadata = $UserMetadata
	    $EventHubDescription.Path = $Path
	    $NamespaceManager.CreateEventHubIfNotExists($EventHubDescription);
	    Write-Output "The [$Path] event hub in the [$Namespace] namespace has been successfully created."
	}
		
	# Create the consumer group if it doesn't exist
	Write-Output "Creating the consumer group [$ConsumerGroupName] for the [$Path] event hub..."
	$ConsumerGroupDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.ConsumerGroupDescription -ArgumentList $Path, $ConsumerGroupName
	$ConsumerGroupDescription.UserMetadata = $ConsumerGroupUserMetadata
	$NamespaceManager.CreateConsumerGroupIfNotExists($ConsumerGroupDescription);
	Write-Output "The consumer group [$ConsumerGroupName] for the [$Path] event hub has been successfully created."
	```

## Pasos siguientes

En este artículo se proporciona una descripción básica para el aprovisionamiento de entidades del Bus de servicio mediante PowerShell. Todo lo que puede hacer con las bibliotecas de cliente .NET puede hacer en un script de PowerShell.

Hay ejemplos más detallados en los siguientes artículos de blog:

- [Cómo crear colas, temas y suscripciones de Service Bus con un script de PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Cómo crear un espacio de nombres de Service Bus y un centro de eventos mediante un script de PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Además, puede descargar algunos scripts listos para usar: - [Scripts de PowerShell del Bus de servicio](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Link references-->
[Opciones de compra]: http://azure.microsoft.com/pricing/purchase-options/
[Ofertas para miembros]: http://azure.microsoft.com/pricing/member-offers/
[Prueba gratuita]: http://azure.microsoft.com/pricing/free-trial/
[Instalación y configuración de Azure PowerShell]: ../powershell-install-configure.md
[paquete NuGet del Bus de servicio]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[API de .NET para el Bus de servicio]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.aspx
[NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx

<!---HONumber=AcomDC_0211_2016-->