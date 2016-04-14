<properties
	pageTitle="Administración de Caché en Redis de Azure con Azure PowerShell | Microsoft Azure"
	description="Aprenda a realizar tareas administrativas para Caché en Redis de Azure usando Azure PowerShell."
	services="redis-cache"
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2016" 
	ms.author="sdanie"/>

# Administración de Caché en Redis de Azure con Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](cache-howto-manage-redis-cache-powershell.md)
- [Azure CLI](cache-manage-cli.md)

Este tema muestra cómo realizar tareas comunes, como crear, actualizar y escalar las instancias de caché en Redis de Azure, cómo volver a generar las claves de acceso y cómo ver información acerca de las memorias caché. Para obtener una lista completa de cmdlets de PowerShell de caché en Redis de Azure, consulte [Azure Redis Cache cmdlets](https://msdn.microsoft.com/library/azure/mt634513.aspx).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](#clásico) se describe más adelante en este artículo.

## Requisitos previos

Si ya ha instalado Azure PowerShell, debe tener la versión 1.0.0 (o posterior) de Azure PowerShell. Puede comprobar la versión de Azure PowerShell que ha instalado con este comando en el símbolo del sistema de Azure PowerShell.

	Get-Module azure | format-table version

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

En primer lugar, debe iniciar sesión en Azure con este comando.

	Login-AzureRmAccount

Especifique la dirección de correo electrónico de la cuenta de Azure y su contraseña en el cuadro de diálogo de inicio de sesión de Microsoft Azure.

A continuación, si tiene varias suscripciones de Azure, deberá establecer la suscripción de Azure. Para ver una lista de las suscripciones actuales, ejecute este comando.

	Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Para especificar la suscripción, ejecute el siguiente comando. En el ejemplo siguiente, el nombre de la suscripción es `ContosoSubscription`.

	Select-AzureRmSubscription -SubscriptionName ContosoSubscription

Para poder usar Windows PowerShell con el Administrador de recursos de Azure, necesita lo siguiente:

- Windows PowerShell, versión 3.0 o 4.0. Para buscar la versión de Windows PowerShell, escriba:`$PSVersionTable` y compruebe que el valor de `PSVersion` es 3.0 o 4.0. Para instalar una versión compatible, consulte [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Para obtener ayuda detallada con cualquier cmdlet que aparezca en este tutorial, use el cmdlet Get-Help.

	Get-Help <cmdlet-name> -Detailed

Por ejemplo, para obtener ayuda para el cmdlet `New-AzureRmRedisCache`, escriba:

	Get-Help New-AzureRmRedisCache -Detailed

## Conexión a la nube de Azure Government o a la nube de China de Azure

De forma predeterminada, el entorno de Azure es `AzureCloud`, que representa la instancia de nube de Azure global. Para conectarse a una instancia diferente, utilice el comando `Add-AzureRmAccount` con el conmutador de línea de comandos `-Environment` o -`EnvironmentName` con el nombre de entorno o el entorno deseado.

Para ver la lista de entornos disponibles, ejecute el cmdlet `Get-AzureRmEnvironment`.

### Conexión a la nube de Azure Government

Para conectarse a la nube de Azure Government, utilice uno de los siguientes comandos.

	Add-AzureRMAccount -EnvironmentName AzureUSGovernment

o

	Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureUSGovernment)

Para crear una memoria caché en la nube de Azure Government, utilice una de las siguientes ubicaciones.

-	USGov Virginia
-	USGov Iowa

Para obtener más información acerca de la nube de Azure Government, consulte [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) y la [Guía para desarrolladores de Microsoft Azure Government](azure-government-developer-guide.md).

### Para conectarse a la nube de China de Azure

Para conectarse a la nube de China de Azure, use uno de los siguientes comandos.

	Add-AzureRMAccount -EnvironmentName AzureChinaCloud

o

	Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureChinaCloud)

Para crear una caché en la nube de China de Azure, use una de las siguientes ubicaciones.

-	Este de China
-	Norte de China

Para obtener más información acerca de la nube de China de Azure, consulte [AzureChinaCloud for Azure operated by 21Vianet in China (Nube de China de Azure operada por 21Vianet en China)](http://www.windowsazure.cn/).

## Propiedades utilizadas para Caché en Redis de Azure con PowerShell

La tabla siguiente contiene las propiedades y las descripciones de los parámetros normalmente utilizados al crear y administrar las instancias de Caché en Redis de Azure mediante Azure PowerShell.

| Parámetro | Descripción | Valor predeterminado |
|--------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|
| Nombre | Nombre de la memoria caché | |
| Ubicación | Ubicación de la memoria caché | |
| ResourceGroupName | Nombre del grupo de recursos en el que se va a crear la memoria caché | |
| Tamaño | El tamaño de la memoria caché. Los valores válidos son: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250 MB, 1 GB, 2,5 GB, 6 GB, 13 GB, 26 GB, 53 GB | 1 GB |
| ShardCount | El número de particiones para crear durante la creación de una memoria caché premium con clúster habilitado. Los valores válidos son: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU | Especifica la SKU de la memoria caché. Los valores válidos son: Básico, Estándar y Premium | Standard |
| RedisConfiguration | Especifica la configuración de Redis para maxmemory-delta, maxmemory-policy, y notify-keyspace-events. Tenga en cuenta que maxmemory-delta y notify-keyspace-events solo están disponibles para memorias caché estándar y premium. | |
| EnableNonSslPort | Indica si el puerto no SSL está habilitado. | False |
| MaxMemoryPolicy | Este parámetro está en desuso; utilice RedisConfiguration en su lugar. | |
| StaticIP | Si hospeda la memoria caché en una red virtual, especifica una dirección IP única en la subred de la memoria caché. Si no se ofrece, elija una para usted en la subred. | |
| Subred | Si hospeda la memoria caché en una red virtual, especifica el nombre de la subred en la que se va a implementar la memoria caché. | |
| VirtualNetwork | Si hospeda la memoria caché en una red virtual, especifica el identificador de recurso de la red virtual en la que se va a implementar la memoria caché. | |
| KeyType | Especifica la clave de acceso que hay que volver a generar cuando se renueven las claves de acceso. Los valores válidos son: primario, secundario | | | |


## Creación de una memoria caché en Redis

Se crean nuevas instancias de caché en Redis de Azure mediante el cmdlet [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx).

>[AZURE.IMPORTANT] La primera vez que crea una caché en Redis en una suscripción mediante el portal de Azure, el portal registra el espacio de nombres `Microsoft.Cache` para esa suscripción. Si intenta crear la primera caché en Redis en una suscripción mediante PowerShell, primero debe registrar ese espacio de nombres mediante el comando siguiente; en caso contrario los cmdlets como `New-AzureRmRedisCache` y `Get-AzureRmRedisCache` producirán un error.
>
>`Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Cache"`

Para ver una lista de parámetros disponibles y sus descripciones para `New-AzureRmRedisCache`, ejecute el siguiente comando.

	PS C:\> Get-Help New-AzureRmRedisCache -detailed
	
	NAME
	    New-AzureRmRedisCache
	
	SYNOPSIS
	    Creates a new redis cache.
	
	SYNTAX
	    New-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
	    [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
	    <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
	    [<CommonParameters>]
	
	DESCRIPTION
	    The New-AzureRmRedisCache cmdlet creates a new redis cache.
	
	PARAMETERS
	    -Name <String>
	        Name of the redis cache to create.
	
	    -ResourceGroupName <String>
	        Name of resource group in which to create the redis cache.
	
	    -Location <String>
	        Location in which to create the redis cache.
	
	    -RedisVersion <String>
	        RedisVersion is deprecated and will be removed in future release.
	
	    -Size <String>
	        Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
	        C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.
	
	    -Sku <String>
	        Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.
	
	    -MaxMemoryPolicy <String>
	        The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
	        MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}
	
	    -RedisConfiguration <Hashtable>
	        All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
	        rdb-backup-frequency, maxmemory-delta, maxmemory-policy, notify-keyspace-events, maxmemory-samples,
	        slowlog-log-slower-than, slowlog-max-len, list-max-ziplist-entries, list-max-ziplist-value,
	        hash-max-ziplist-entries, hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries,
	        zset-max-ziplist-value etc.
	
	    -EnableNonSslPort <Boolean>
	        EnableNonSslPort is used by Azure Redis Cache. If no value is provided, the default value is false and the
	        non-SSL port will be disabled. Possible values are true and false.

	    -ShardCount <Integer>
	        The number of shards to create on a Premium Cluster Cache.
	
	    -VirtualNetwork <String>
	        The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format:
	        /subscriptions/{subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}
	
	    -Subnet <String>
	        Required when deploying a redis cache inside an existing Azure Virtual Network.
	
	    -StaticIP <String>
	        Required when deploying a redis cache inside an existing Azure Virtual Network.
	
	    <CommonParameters>
	        This cmdlet supports the common parameters: Verbose, Debug,
	        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
	        OutBuffer, PipelineVariable, and OutVariable. For more information, see
	        about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Para crear una memoria caché con parámetros predeterminados, ejecute el siguiente comando.

	New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name`, y `Location` son parámetros necesarios, pero el resto son opcionales y tienen valores predeterminados. Ejecutar el comando anterior crea una instancia de caché en Redis de Azure de SKU estándar con el nombre, ubicación y grupo de recursos especificados, que es de 1 GB de tamaño con el puerto no SSL deshabilitado.

Para crear una memoria caché premium, especifique un tamaño de P1 (6 GB - 60 GB), P2 (13 GB - 130 GB), P3 (26 GB - 260 GB) o P4 (53 GB - 530 GB). Para habilitar la agrupación en clústeres, especifique un número de particiones mediante el parámetro `ShardCount`. En el ejemplo siguiente se crea una memoria caché premium P1 con 3 particiones. Una memoria caché premium P1 tiene 6 GB de tamaño y, puesto que especificamos tres particiones el tamaño total es de 18 GB (3 x 6 GB).

	New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Para especificar valores para el parámetro `RedisConfiuration`, incluya los valores dentro de `{}` como pares de valor o de clave como en `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`. En el ejemplo siguiente se crea una memoria caché estándar de 1 GB con `allkeys-random` maxmemory-policy y notificaciones de keyspace configuradas con `KEA`. Para más información, consulte [Notificaciones de Keyspace (configuración avanzada)](cache-configure.md#keyspace-notifications-advanced-settings) y [Maxmemory-policy y maxmemory-reserved](cache-configure.md#maxmemory-policy-and-maxmemory-reserved).

	New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

## Actualización de una caché en Redis

Las instancias de caché en Redis de Azure se actualizan mediante el cmdlet [Set-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx).

Para ver una lista de parámetros disponibles y sus descripciones para `Set-AzureRmRedisCache`, ejecute el siguiente comando.

	PS C:\> Get-Help Set-AzureRmRedisCache -detailed
	
	NAME
	    Set-AzureRmRedisCache
	
	SYNOPSIS
	    Set redis cache updatable parameters.
	
	SYNTAX
	    Set-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
	    [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
	    <Integer>] [<CommonParameters>]
	
	DESCRIPTION
	    The Set-AzureRmRedisCache cmdlet sets redis cache parameters.
	
	PARAMETERS
	    -Name <String>
	        Name of the redis cache to update.
	
	    -ResourceGroupName <String>
	        Name of the resource group for the cache.
	
	    -Size <String>
	        Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
	        C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.
	
	    -Sku <String>
	        Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.
	
	    -MaxMemoryPolicy <String>
	        The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
	        MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}
	
	    -RedisConfiguration <Hashtable>
	        All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
	        rdb-backup-frequency, maxmemory-delta, maxmemory-policy, notify-keyspace-events, maxmemory-samples,
	        slowlog-log-slower-than, slowlog-max-len, list-max-ziplist-entries, list-max-ziplist-value,
	        hash-max-ziplist-entries, hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries,
	        zset-max-ziplist-value etc.
	
	    -EnableNonSslPort <Boolean>
	        EnableNonSslPort is used by Azure Redis Cache. The default value is null and no change will be made to the
	        currently configured value. Possible values are true and false.
	
	    -ShardCount <Integer>
	        The number of shards to create on a Premium Cluster Cache.
	
	    <CommonParameters>
	        This cmdlet supports the common parameters: Verbose, Debug,
	        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
	        OutBuffer, PipelineVariable, and OutVariable. For more information, see
	        about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

El cmdlet `Set-AzureRmRedisCache` puede utilizarse para actualizar propiedades tales como `Size`, `Sku`, `EnableNonSslPort` y los valores de `RedisConfiguration`.

El comando siguiente actualiza el parámetro maxmemory-policy para la caché en Redis denominada myCache.

	Set-AzureRmRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>
## Para escalar una caché en Redis

Se puede usar `Set-AzureRmRedisCache` para escalar una instancia de caché en Redis de Azure si se modifican las propiedades `Size`, `Sku`, o `ShardCount`.

>[AZURE.NOTE]El escalado de una caché con PowerShell está sujeto a los mismos límites y directrices que el escalado de una caché desde el portal de Azure. Puede escalar a un nivel de precios diferente con las siguientes restricciones.
>
>-	No puede escalar a una memoria caché de nivel **Premium** o desde esta.
>-	No puede escalar desde una memoria caché **Estándar** a una **Básica**.
>-	Puede escalar desde una memoria caché **Basic** a una memoria caché **Standard**, pero no puede cambiar el tamaño al mismo tiempo. Si necesita un tamaño distinto, puede realizar una operación de escalado posterior hasta el tamaño deseado.
>-	No puede escalar desde un tamaño mayor hasta el tamaño **C0 (250 MB)**.
>
>Para más información, vea [Escalado de caché en Redis de Azure](cache-how-to-scale.md).

En el ejemplo siguiente se muestra cómo escalar una memoria caché denominada `myCache` a una caché de 2,5 GB. Tenga en cuenta que este comando funcionará con una memoria caché básica o una estándar.

	Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Después de emitir este comando, el estado de la memoria caché se devuelve (de forma similar a una llamada a `Get-AzureRmRedisCache`). Tenga en cuenta que `ProvisioningState` es `Scaling`.

	PS C:\> Set-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB
	
	
	Name               : mycache
	Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
	                     crosoft.Cache/Redis/mycache
	Location           : South Central US
	Type               : Microsoft.Cache/Redis
	HostName           : mycache.redis.cache.windows.net
	Port               : 6379
	ProvisioningState  : Scaling
	SslPort            : 6380
	RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
	                     [maxmemory-delta, 150]...}
	EnableNonSslPort   : False
	RedisVersion       : 3.0
	Size               : 1GB
	Sku                : Standard
	ResourceGroupName  : mygroup
	PrimaryKey         : ....
	SecondaryKey       : ....
	VirtualNetwork     :
	Subnet             :
	StaticIP           :
	TenantSettings     : {}
	ShardCount         :

Cuando se complete la operación de escalado, `ProvisioningState` cambiará a `Succeeded`. Si necesita realizar una operación de escalado subsiguiente, como cambiar de básica a estándar y, a continuación, cambiar el tamaño, debe esperar hasta que se complete la operación anterior o recibirá un error similar al siguiente.

	Set-AzureRmRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## Obtención de información acerca de una caché de Redis

Puede recuperar información sobre una caché con el cmdlet [Get-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634514.aspx).

Para ver una lista de parámetros disponibles y sus descripciones para `Get-AzureRmRedisCache`, ejecute el siguiente comando.

	PS C:\> Get-Help Get-AzureRmRedisCache -detailed
	
	NAME
	    Get-AzureRmRedisCache
	
	SYNOPSIS
	    Gets details about a single cache or all caches in the specified resource group or all caches in the current
	    subscription.
	
	SYNTAX
	    Get-AzureRmRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]
	
	DESCRIPTION
	    The Get-AzureRmRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
	    ResourceGroupName and Name parameters are provided then Get-AzureRmRedisCache will return details about the
	    specific cache name provided.
	
	    If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.
	
	    If no parameters are given than it will return details about all caches the current subscription.
	
	PARAMETERS
	    -Name <String>
	        The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzureRmRedisCache
	        returns the details for the cache.
	
	    -ResourceGroupName <String>
	        The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
	        then Get-AzureRmRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
	        parameter is provided, then details for all caches in the resource group are returned.
	
	    <CommonParameters>
	        This cmdlet supports the common parameters: Verbose, Debug,
	        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
	        OutBuffer, PipelineVariable, and OutVariable. For more information, see
	        about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Para devolver información sobre todas las cachés de la suscripción actual, ejecute `Get-AzureRmRedisCache` sin ningún parámetro.

	Get-AzureRmRedisCache

Para devolver información sobre todas las cachés de un grupo de recursos específico, ejecute `Get-AzureRmRedisCache` con el parámetro `ResourceGroupName`.

	Get-AzureRmRedisCache -ResourceGroupName myGroup

Para devolver información sobre una caché específica, ejecute `Get-AzureRmRedisCache` con el parámetro `Name` que contiene el nombre de la caché y el parámetro `ResourceGroupName` con el grupo de recursos que contiene esa caché.

	PS C:\> Get-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup
	
	Name               : mycache
	Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
	                     crosoft.Cache/Redis/mycache
	Location           : South Central US
	Type               : Microsoft.Cache/Redis
	HostName           : mycache.redis.cache.windows.net
	Port               : 6379
	ProvisioningState  : Succeeded
	SslPort            : 6380
	RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
	                     [maxclients, 1000]...}
	EnableNonSslPort   : False
	RedisVersion       : 3.0
	Size               : 1GB
	Sku                : Standard
	ResourceGroupName  : myGroup
	VirtualNetwork     :
	Subnet             :
	StaticIP           :
	TenantSettings     : {}
	ShardCount         :

## Recuperación de las claves de acceso de una caché en Redis

Para recuperar las claves de acceso de la caché, puede usar el cmdlet [Get-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634516.aspx).

Para ver una lista de parámetros disponibles y sus descripciones para `Get-AzureRmRedisCacheKey`, ejecute el siguiente comando.

	PS C:\> Get-Help Get-AzureRmRedisCacheKey -detailed
	
	NAME
	    Get-AzureRmRedisCacheKey
	
	SYNOPSIS
	    Gets the accesskeys for the specified redis cache.
	
	
	SYNTAX
	    Get-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]
	
	DESCRIPTION
	    The Get-AzureRmRedisCacheKey cmdlet gets the access keys for the specified cache.
	
	PARAMETERS
	    -Name <String>
	        Name of the redis cache.
	
	    -ResourceGroupName <String>
	        Name of the resource group for the cache.
	
	    <CommonParameters>
	        This cmdlet supports the common parameters: Verbose, Debug,
	        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
	        OutBuffer, PipelineVariable, and OutVariable. For more information, see
	        about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Para recuperar las claves de la caché, llame al cmdlet `Get-AzureRmRedisCacheKey` y pase el nombre de la memoria caché y el nombre del grupo de recursos que contiene la memoria caché.

	PS C:\> Get-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup
	
	PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
	SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## Regeneración de las claves de acceso de la caché en Redis

Para regenerar las claves de acceso de la caché, puede usar el cmdlet [New-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634512.aspx).

Para ver una lista de parámetros disponibles y sus descripciones para `New-AzureRmRedisCacheKey`, ejecute el siguiente comando.

	PS C:\> Get-Help New-AzureRmRedisCacheKey -detailed
	
	NAME
	    New-AzureRmRedisCacheKey
	
	SYNOPSIS
	    Regenerates the access key of a redis cache.
	
	SYNTAX
	    New-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]
	
	DESCRIPTION
	    The New-AzureRmRedisCacheKey cmdlet regenerate the access key of a redis cache.
	
	PARAMETERS
	    -Name <String>
	        Name of the redis cache.
	
	    -ResourceGroupName <String>
	        Name of the resource group for the cache.
	
	    -KeyType <String>
	        Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.
	
	    -Force
	        When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.
	
	    <CommonParameters>
	        This cmdlet supports the common parameters: Verbose, Debug,
	        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
	        OutBuffer, PipelineVariable, and OutVariable. For more information, see
	        about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
	
Para regenerar la clave principal o secundaria de la caché, llame al cmdlet `New-AzureRmRedisCacheKey` y pase el nombre, el grupo de recursos y especifique `Primary` o `Secondary` para el parámetro `KeyType`. En el ejemplo siguiente, se regenera la clave de acceso secundaria de una memoria caché.

	PS C:\> New-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary
	
	Confirm
	Are you sure you want to regenerate Secondary key for redis cache 'myCache'?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
	
	
	PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
	SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## Eliminación de una caché en Redis

Para eliminar una caché en Redis, use el cmdlet [Remove-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634515.aspx).

Para ver una lista de parámetros disponibles y sus descripciones para `Remove-AzureRmRedisCache`, ejecute el siguiente comando.

	PS C:\> Get-Help Remove-AzureRmRedisCache -detailed
	
	NAME
	    Remove-AzureRmRedisCache
	
	SYNOPSIS
	    Remove redis cache if exists.
	
	SYNTAX
	    Remove-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>
	
	DESCRIPTION
	    The Remove-AzureRmRedisCache cmdlet removes a redis cache if it exists.
	
	PARAMETERS
	    -Name <String>
	        Name of the redis cache to remove.
	
	    -ResourceGroupName <String>
	        Name of the resource group of the cache to remove.
	
	    -Force
	        When the Force parameter is provided, the cache is removed without any confirmation prompts.
	
	    -PassThru
	        By default Remove-AzureRmRedisCache removes the cache and does not return any value. If the PassThru par
	        is provided then Remove-AzureRmRedisCache returns a boolean value indicating the success of the operatio
	
	    <CommonParameters>
	        This cmdlet supports the common parameters: Verbose, Debug,
	        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
	        OutBuffer, PipelineVariable, and OutVariable. For more information, see
	        about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

En el ejemplo siguiente, se quita la memoria caché denominada `myCache`.

	PS C:\> Remove-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup
	
	Confirm
	Are you sure you want to remove redis cache 'myCache'?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

<a name="classic"></a>
## Administración de instancias de caché en Redis de Azure con el modelo de implementación clásica de PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](cache-howto-manage-redis-cache-powershell.md) descrito al principio de este artículo.

El siguiente script muestra la creación, actualización y eliminación de una caché en Redis de Azure mediante el modelo de implementación clásica.
		
		$VerbosePreference = "Continue"

    	# Create a new cache with date string to make name unique.
		$cacheName = "MovieCache" + $(Get-Date -Format ('ddhhmm'))
		$location = "West US"
		$resourceGroupName = "Default-Web-WestUS"
		
		$movieCache = New-AzureRedisCache -Location $location -Name $cacheName  -ResourceGroupName $resourceGroupName -Size 250MB -Sku Basic
		
		# Wait until the Cache service is provisioned.
		
		for ($i = 0; $i -le 60; $i++)
		{
		    Start-Sleep -s 30
		    $cacheGet = Get-AzureRedisCache -ResourceGroupName $resourceGroupName -Name $cacheName
		    if ([string]::Compare("succeeded", $cacheGet[0].ProvisioningState, $True) -eq 0)
		    {
		        break
		    }
		    If($i -eq 60)
		    {
		        exit
		    }
		}
		
		# Update the access keys.
		
		Write-Verbose "PrimaryKey: $($movieCache.PrimaryKey)"
		New-AzureRedisCacheKey -KeyType "Primary" -Name $cacheName  -ResourceGroupName $resourceGroupName -Force
		$cacheKeys = Get-AzureRedisCacheKey -ResourceGroupName $resourceGroupName  -Name $cacheName
		Write-Verbose "PrimaryKey: $($cacheKeys.PrimaryKey)"
		
		# Use Set-AzureRedisCache to set Redis cache updatable parameters.
		# Set the memory policy to Least Recently Used.
		
		Set-AzureRedisCache -Name $cacheName -ResourceGroupName $resourceGroupName -RedisConfiguration @{"maxmemory-policy" = "AllKeys-LRU"}
		
		# Delete the cache.
		
		Remove-AzureRedisCache -Name $movieCache.Name -ResourceGroupName $movieCache.ResourceGroupName  -Force

## Pasos siguientes

Para obtener más información acerca de Windows PowerShell con Azure, consulte los siguientes recursos:

- [Documentación de cmdlet de caché en Redis de Azure en MSDN](https://msdn.microsoft.com/library/azure/mt634513.aspx)
- [Cmdlets de Administrador de recursos de Azure](http://go.microsoft.com/fwlink/?LinkID=394765): obtenga información acerca del uso de los cmdlets en el módulo AzureResourceManager.
- [Uso de grupos de recursos para administrar los recursos de Azure](../azure-portal/resource-group-portal.md): obtenga información sobre la creación y administración de grupos de recursos en el portal de Azure.
- [Blog de Azure](http://blogs.msdn.com/windowsazure): obtenga información acerca de las nuevas características de Azure.
- [Blog de Windows PowerShell](http://blogs.msdn.com/powershell): obtenga información acerca de las nuevas características de Windows PowerShell.
- [Blog ¡Hola, chicos del scripting!](http://blogs.technet.com/b/heyscriptingguy/): Obtenga sugerencias y trucos del mundo real de la comunidad de Windows PowerShell.

<!---HONumber=AcomDC_0211_2016-->