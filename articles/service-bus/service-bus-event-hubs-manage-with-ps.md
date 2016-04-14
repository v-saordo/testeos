<properties
   pageTitle="Usar PowerShell para administrar recursos de Service Bus y centros de eventos"
   description="Usar PowerShell crear y administrar recursos de Service Bus y centros de eventos"
   services="service-bus"
   documentationCenter=".NET"
   authors="sethmanheim"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/09/2015"
   ms.author="sethm"/>

# Usar PowerShell para administrar recursos de Service Bus y centros de eventos

Microsoft Azure PowerShell es un entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus servicios de Azure. En este artículo se describe cómo usar PowerShell para aprovisionar y administrar entidades de Service Bus, como espacios de nombres, colas y centros de eventos, mediante una consola local de Azure PowerShell.

## Requisitos previos

Antes de comenzar, necesitará lo siguiente:

- Una suscripción de Azure. Azure es una plataforma basada en suscripción. Para obtener más información acerca de cómo obtener una suscripción, consulte [Opciones de compra][], [Ofertas para miembros][] o [Prueba gratuita][].

- Un equipo con Azure PowerShell. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][].

- Conocimientos generales sobre los scripts de PowerShell, paquetes de NuGet y .NET Framework.

## Incluir una referencia al ensamblado .NET para Service Bus

Hay un número limitado de cmdlets de PowerShell disponibles para la administración de Service Bus. Para aprovisionar las entidades que no se exponen a través de los cmdlets existentes, puede usar el cliente de .NET para Service Bus dentro de PowerShell haciendo referencia al [paquete NuGet del Service Bus].

En primer lugar, asegúrese de que el script puede encontrar el ensamblado **Microsoft.ServiceBus.dll**, que se instala con el paquete NuGet. Para ser flexible, el script llevará a cabo estos pasos:

1. Determina la ruta de acceso desde la que se invocó.
2. Atraviesa la ruta de acceso hasta que encuentra una carpeta denominada `packages`. Esta carpeta se crea al instalar paquetes de NuGet.
3. Se busca de forma recursiva en la carpeta `packages` un ensamblado denominado **Microsoft.ServiceBus.dll**.
4. Hace referencia al ensamblado de modo que los tipos estarán disponibles para su uso posterior.

Aquí se explica cómo se implementan estos pasos en un script de PowerShell:

```powershell

try
{
    # IMPORTANT: Make sure to reference the latest version of Microsoft.ServiceBus.dll
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

Al trabajar con espacios de nombres de Service Bus, hay dos cmdlets que puede usar en lugar del SDK de .NET: [Get-AzureSBNamespace] y [New-AzureSBNamespace].

En este ejemplo se crean algunas variables locales en el script: `$Namespace` y `$Location`.

- `$Namespace` es el nombre del espacio de nombres de Service Bus con el que queremos trabajar.
- `$Location` identifica el centro de datos en el que aprovisionaremos el espacio de nombres.
- `$CurrentNamespace` almacena el espacio de nombres de referencia que vamos a recuperar (o crear).

En un script real, `$Namespace` y `$Location` se pueden pasar como parámetros.

Esta parte del script hace lo siguiente:

1. Intenta recuperar un espacio de nombres de Service Bus con el nombre proporcionado.
2. Si se encuentra el espacio de nombres, informa de lo que ha encontrado.
3. Si no se encuentra el espacio de nombres, lo crea y luego recupera el espacio de nombres recién creado.

	``` powershell

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
	    New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace false -NamespaceType Messaging
	    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
	    Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
	}
	```
Para aprovisionar otras entidades de Service Bus, cree una instancia del objeto `NamespaceManager` a partir del SDK. Puede usar el cmdlet [Get-AzureSBAuthorizationRule] para recuperar una regla de autorización que se usa para proporcionar una cadena de conexión. En este ejemplo se almacena una referencia a la instancia `NamespaceManager` de la variable `$NamespaceManager`. Más adelante el script usa `$NamespaceManager` para aprovisionar otras entidades.

	``` powershell
	$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
	# Create the NamespaceManager object to create the Event Hub
	Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
	$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
	Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
	```

## Aprovisionamiento de otras entidades de Service Bus

Para aprovisionar otras entidades, como colas, temas y centros de eventos, podemos usar la [API .NET para Service Bus]. Al final de este artículo se hace referencia a ejemplos más detallados, incluidas otras entidades.

### Creación de un Centro de eventos

En esta parte del script se crean cuatro variables locales adicionales. Estas variables se usan para crear una instancia de un objeto `EventHubDescription`. El script hace lo siguiente:

1. Mediante el objeto `NamespaceManager`, compruebe si existe el centro de eventos que se identifica mediante `$Path`.
2. Si no existe, creamos un `EventHubDescription` y lo pasamos al método `CreateEventHubIfNotExists` de la clase `NamespaceManager`.
3. Cuando sepamos que el centro de eventos está disponible, cree un grupo de consumidores mediante `ConsumerGroupDescription` y `NamespaceManager`.

	``` powershell

	$Path  = "MyEventHub"
	$PartitionCount = 12
	$MessageRetentionInDays = 7
	$UserMetadata = $null
	$ConsumerGroupName = "MyConsumerGroup"

	# Check if the Event Hub already exists
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

### Creación de una cola

Para crear una cola o un tema, realice una comprobación de espacio de nombres como en la sección anterior.

	if ($NamespaceManager.QueueExists($Path))
	{
	    Write-Output "The [$Path] queue already exists in the [$Namespace] namespace."
	}
	else
	{
	    Write-Output "Creating the [$Path] queue in the [$Namespace] namespace..."
	    $QueueDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.QueueDescription -ArgumentList $Path
	    if ($AutoDeleteOnIdle -ge 5)
	    {
	        $QueueDescription.AutoDeleteOnIdle = [System.TimeSpan]::FromMinutes($AutoDeleteOnIdle)
	    }
	    if ($DefaultMessageTimeToLive -gt 0)
	    {
	        $QueueDescription.DefaultMessageTimeToLive = [System.TimeSpan]::FromMinutes($DefaultMessageTimeToLive)
	    }
	    if ($DuplicateDetectionHistoryTimeWindow -gt 0)
	    {
	        $QueueDescription.DuplicateDetectionHistoryTimeWindow = [System.TimeSpan]::FromMinutes($DuplicateDetectionHistoryTimeWindow)
	    }
	    $QueueDescription.EnableBatchedOperations = $EnableBatchedOperations
	    $QueueDescription.EnableDeadLetteringOnMessageExpiration = $EnableDeadLetteringOnMessageExpiration
	    $QueueDescription.EnableExpress = $EnableExpress
	    $QueueDescription.EnablePartitioning = $EnablePartitioning
	    $QueueDescription.ForwardDeadLetteredMessagesTo = $ForwardDeadLetteredMessagesTo
	    $QueueDescription.ForwardTo = $ForwardTo
	    $QueueDescription.IsAnonymousAccessible = $IsAnonymousAccessible
	    if ($LockDuration -gt 0)
	    {
	        $QueueDescription.LockDuration = [System.TimeSpan]::FromSeconds($LockDuration)
	    }
	    $QueueDescription.MaxDeliveryCount = $MaxDeliveryCount
	    $QueueDescription.MaxSizeInMegabytes = $MaxSizeInMegabytes
	    $QueueDescription.RequiresDuplicateDetection = $RequiresDuplicateDetection
	    $QueueDescription.RequiresSession = $RequiresSession
	    if ($EnablePartitioning)
	    {
	        $QueueDescription.SupportOrdering = $False
	    }
	    else
	    {
	        $QueueDescription.SupportOrdering = $SupportOrdering
	    }
	    $QueueDescription.UserMetadata = $UserMetadata
	    $NamespaceManager.CreateQueue($QueueDescription);
	}

### de un tema

	if ($NamespaceManager.TopicExists($Path))
	{
	    Write-Output "The [$Path] topic already exists in the [$Namespace] namespace."
	}
	else
	{
	    Write-Output "Creating the [$Path] topic in the [$Namespace] namespace..."
	    $TopicDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.TopicDescription -ArgumentList $Path
	    if ($AutoDeleteOnIdle -ge 5)
	    {
	        $TopicDescription.AutoDeleteOnIdle = [System.TimeSpan]::FromMinutes($AutoDeleteOnIdle)
	    }
	    if ($DefaultMessageTimeToLive -gt 0)
	    {
	        $TopicDescription.DefaultMessageTimeToLive = [System.TimeSpan]::FromMinutes($DefaultMessageTimeToLive)
	    }
	    if ($DuplicateDetectionHistoryTimeWindow -gt 0)
	    {
	        $TopicDescription.DuplicateDetectionHistoryTimeWindow = [System.TimeSpan]::FromMinutes($DuplicateDetectionHistoryTimeWindow)
	    }
	    $TopicDescription.EnableBatchedOperations = $EnableBatchedOperations
	    $TopicDescription.EnableExpress = $EnableExpress
	    $TopicDescription.EnableFilteringMessagesBeforePublishing = $EnableFilteringMessagesBeforePublishing
	    $TopicDescription.EnablePartitioning = $EnablePartitioning
	    $TopicDescription.IsAnonymousAccessible = $IsAnonymousAccessible
	    $TopicDescription.MaxSizeInMegabytes = $MaxSizeInMegabytes
	    $TopicDescription.RequiresDuplicateDetection = $RequiresDuplicateDetection
	    if ($EnablePartitioning)
	    {
	        $TopicDescription.SupportOrdering = $False
	    }
	    else
	    {
	        $TopicDescription.SupportOrdering = $SupportOrdering
	    }
	    $TopicDescription.UserMetadata = $UserMetadata
	    $NamespaceManager.CreateTopic($TopicDescription);
	}

## Pasos siguientes

En este artículo se ofrece una descripción básica para el aprovisionamiento de entidades del Bus de servicio mediante PowerShell. Aunque hay un número limitado de cmdlets de PowerShell disponibles para la administración de entidades de mensajería de Service Bus, al hacer referencia al ensamblado Microsoft.ServiceBus.dll, prácticamente cualquier operación que pueda realizar con las bibliotecas de cliente .NET se podrán realizar también en un script de PowerShell.

Hay ejemplos más detallados disponibles en estas publicaciones de blog:

- [Cómo crear colas, temas y suscripciones de Service Bus con un script de PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Cómo crear un espacio de nombres de Service Bus y un centro de eventos mediante un script de PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Además, puede descargar algunos scripts listos para usar:

- [Scripts de PowerShell de Service Bus](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[Opciones de compra]: http://azure.microsoft.com/pricing/purchase-options/
[Ofertas para miembros]: http://azure.microsoft.com/pricing/member-offers/
[Prueba gratuita]: http://azure.microsoft.com/pricing/free-trial/
[paquete NuGet del Service Bus]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[API .NET para Service Bus]: https://msdn.microsoft.com/es-ES/library/azure/mt419900.aspx
[Instalación y configuración de Azure PowerShell]: ../install-configure-powershell.md

<!---HONumber=AcomDC_1217_2015-->