<properties 
	pageTitle="Implementación y administración de Centros de notificaciones mediante PowerShell" 
	description="Creación y administración de Centros de notificaciones mediante PowerShell con vistas a la automatización" 
	services="notification-hubs" 
	documentationCenter="" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/10/2015" 
	ms.author="wesmc"/>

# Implementación y administración de Centros de notificaciones mediante PowerShell

##Información general

Este artículo muestra cómo crear y administrar Centros de notificaciones de Azure mediante PowerShell. En este tema se muestran las siguientes tareas de automatización más comunes.

+ Creación de un centro de notificaciones
+ Definición de credenciales

Si también necesita crear un nuevo espacio de nombres de Bus de servicio para sus centros de notificaciones, consulte [Administración del Bus de servicio con PowerShell](../service-bus/service-bus-powershell-how-to-provision.md).

No se admite la administración de Centros de notificaciones directamente mediante los cmdlets incluidos con Azure PowerShell. El mejor enfoque en PowerShell es hacer referencia al ensamblado Microsoft.Azure.NotificationHubs.dll. El ensamblado se distribuye con el [paquete NuGet de los Centros de notificaciones de Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).


## Requisitos previos

Antes de empezar este artículo, debe tener lo siguiente:

- Una suscripción de Azure. Azure es una plataforma basada en suscripción. Para obtener más información acerca de cómo obtener una suscripción, consulte [Opciones de compra], [Ofertas para miembros] o [Prueba gratuita].

- Un equipo con Azure PowerShell. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell].

- Conocimientos generales sobre los scripts de PowerShell, paquetes de NuGet y .NET Framework.


## Incluir una referencia al ensamblado .NET para Service Bus

La administración de los Centros de notificaciones de Azure no está incluida aún con los cmdlets de Azure PowerShell. Para aprovisionar los centros de notificaciones, puede usar el cliente de .NET ofrecido en el [paquete NuGet de los Centros de notificaciones de Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

En primer lugar, asegúrese de que el script puede encontrar el ensamblado **Microsoft.Azure.NotificationHubs.dll**, que se instala como un paquete NuGet en un proyecto de Visual Studio. Para ser flexible, el script llevará a cabo estos pasos:

1. Determina la ruta de acceso en la que se invocó.
2. Atraviesa la ruta de acceso hasta que encuentra una carpeta denominada `packages`. Esta carpeta se crea al instalar paquetes NuGet para proyectos de Visual Studio.
3. Se busca de forma recursiva en la carpeta `packages` un ensamblado denominado **Microsoft.Azure.NotificationHubs.dll**.
4. Hace referencia al ensamblado de modo que los tipos estarán disponibles para su uso posterior.

Aquí se explica cómo se implementan estos pasos en un script de PowerShell:

``` powershell

try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## Creación de la clase NamespaceManager

Para aprovisionar los Centros de notificaciones, cree una instancia de la clase [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.namespacemanager.aspx) desde el SDK.

Puede usar el cmdlet [Get-AzureSBAuthorizationRule] que se incluye con Azure PowerShell para recuperar una regla de autorización que se usa para proporcionar una cadena de conexión. Almacenaremos una referencia a la instancia de `NamespaceManager` en la variable `$NamespaceManager`. Usaremos `$NamespaceManager` para aprovisionar un centro de notificaciones.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```


## Aprovisionamiento de un nuevo centro de notificaciones 

Para aprovisionar un nuevo centro de notificaciones, use la [API de .NET para Centros de notificaciones].

En esta parte del script, configuramos cuatro variables locales.

1. `$Namespace`: establezca esta variable en el nombre del espacio de nombres donde quiera crear un centro de notificaciones.
2. `$Path`: establezca esta ruta de acceso en el nombre del nuevo centro de notificaciones. Por ejemplo, "MyHub".    
3. `$WnsPackageSid`: establezca esta variable en el SID del paquete para la aplicación de Windows desde el [Centro de desarrollo de Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).
4. `$WnsSecretkey`: establezca este valor en la clave secreta para la aplicación de Windows desde el [Centro de desarrollo de Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).

Estas variables se usan para conectar con el espacio de nombres y crear un nuevo centro de notificaciones configurado para controlar las notificaciones de los servicios de notificación de Windows (WNS) con las credenciales de WNS en una aplicación de Windows. Para información sobre cómo obtener el SID del paquete y la clave secreta, vea el tutorial [Introducción a los Centros de notificaciones](notification-hubs-windows-store-dotnet-get-started.md).

+ El fragmento de código de script usa el objeto `NamespaceManager` para comprobar si existe el Centro de notificaciones identificado por `$Path`.

+ Si no existe, el script creará una `NotificationHubDescription` con las credenciales de WNS y la pasará al método `CreateNotificationHub` de la clase `NamespaceManager`.

``` powershell

$Namespace = "<Enter your namespace>
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```




## Recursos adicionales

- [Administración de Service Bus con PowerShell](../service-bus/service-bus-powershell-how-to-provision.md)
- [Cómo crear colas, temas y suscripciones de Service Bus con un script de PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Cómo crear un espacio de nombres de Service Bus y un centro de eventos mediante un script de PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Además, puede descargar algunos scripts listos para usar: - [Scripts de PowerShell del Bus de servicio](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)
 

[Opciones de compra]: http://azure.microsoft.com/pricing/purchase-options/
[Ofertas para miembros]: http://azure.microsoft.com/pricing/member-offers/
[Prueba gratuita]: http://azure.microsoft.com/pricing/free-trial/
[Instalación y configuración de Azure PowerShell]: ../install-configure-powershell.md
[API de .NET para Centros de notificaciones]: https://msdn.microsoft.com/library/azure/mt414893.aspx
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
 

<!---HONumber=AcomDC_1217_2015-->