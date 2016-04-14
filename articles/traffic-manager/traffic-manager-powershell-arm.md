<properties
   pageTitle="Compatibilidad del Administrador de recursos de Azure para la vista previa del Administrador de tráfico | Microsoft Azure"
   description="Uso de PowerShell para el Administrador de tráfico con el Administrador de recursos de Azure (ARM) en vista previa"
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="joaoma" />

# Compatibilidad del Administrador de recursos de Azure con la vista previa del Administrador de tráfico de Azure
El Administrador de recursos de Azure (ARM) es el nuevo marco de administración de servicios en Azure. Los perfiles del Administrador de tráfico de Azure ahora pueden administrarse mediante las herramientas y las API basadas en el Administrador de recursos de Azure. Para obtener más información sobre el Administrador de recursos de Azure, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-preview-portal-using-resource-groups.md).

## Modelo de recursos

El Administrador de tráfico de Azure se configura con una colección de valores denominada perfil del Administrador de tráfico. Contiene la configuración de DNS, de enrutamiento de tráfico, de supervisión de extremos y la lista de extremos de servicio a la que se enrutará el tráfico.

En ARM, cada perfil del Administrador de tráfico se representa mediante un recurso ARM, de tipo 'TrafficManagerProfiles', administrado por el proveedor de recursos 'Microsoft.Network'. En el nivel de la API de REST, el URI de cada perfil es el siguiente:

	https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## Comparación con la API de administración del servicio del Administrador de tráfico de Azure

El uso de ARM para configurar perfiles de Administrador de tráfico proporciona acceso al mismo conjunto de características de Administrador de tráfico que la API de administración de servicios de Azure (ASM), excepto las limitaciones de vista previa que aparecen a continuación.

Sin embargo, mientras que las características siguen siendo las mismas, parte de la terminología ha cambiado:

- Se cambió el nombre del 'método de equilibrio de carga', que determina cómo elige el Administrador de tráfico a qué extremo debe enrutar el tráfico al responder a una solicitud concreta de DNS, por el de 'método de enrutamiento de tráfico'.

- Se cambió el nombre del tráfico de 'round robin' por el de 'ponderado'.

- Se cambió el nombre del tráfico de 'conmutación por error' por el de 'prioridad'.

## Limitaciones
Actualmente hay unas pocas limitaciones de compatibilidad de ARM con el Administrador de tráfico de Azure:

- Los perfiles de Administrador de tráfico creados con la API de administración de servicios (ASM), las herramientas y el Portal "clásico" no están disponibles a través de ARM y viceversa. Actualmente no se admite la migración de perfiles de las API de ASM a las de ARM, excepto eliminar el perfil y volverlo a crear.

- Se admiten puntos de conexión "Anidados" a través de la API de ARM, PowerShell de ARM y la CLI de Azure de modo ARM. Actualmente, no se admiten en el portal de Azure (que también utiliza la API de ARM).

- Los puntos de conexión del Administrador de tráfico del tipo “AzureEndpoints”, al hacer referencia a una aplicación web, solo pueden referirse a la [ranura de aplicación web](../app-service-web/web-sites-staged-publishing.md) predeterminada (producción). Todavía no se admiten las ranuras personalizadas. Como alternativa, se pueden configurar ranuras personalizadas mediante el tipo “ExternalEndpoints”.

## Configuración de Azure PowerShell

Estas instrucciones utilizan Microsoft Azure PowerShell, que debe configurarse con los siguientes pasos.

Para los usuarios que no usan PowerShell ni Windows, se pueden ejecutar operaciones análogas a través de la CLI de Azure. Todas las operaciones, excepto la administración de perfiles de Administrador de tráfico "anidados", también están disponibles en el Portal de Azure.

### Paso 1
Instalación del PowerShell Azure más reciente, disponible en la página de descargas de Azure.

### Paso 2
Inicio de sesión en la cuenta de Azure

	PS C:\> Login-AzureRmAccount

Se le pedirá que se autentique con sus credenciales.

### Paso 3
Elección de la suscripción de Azure que se va a usar.

	PS C:\> Set-AzureRmContext -SubscriptionName "MySubscription"

Para ver una lista de las suscripciones disponibles, use el cmdlet 'Get-AzureRmSubscription'.

### Paso 4

El proveedor de recursos Microsoft.Network administra el servicio del Administrador de tráfico. Su suscripción de Azure debe estar registrada para utilizar este proveedor de recursos si desea poder usar el Administrador de tráfico a través de ARM. Se trata de una operación única para cada suscripción.

	PS C:\> Register-AzureRmResourceProvider –ProviderNamespace Microsoft.Network

### Paso 5
Creación de un grupo de recursos (omitir este paso si se utiliza un grupo de recursos existente)

	PS C:\> New-AzureRmResourceGroup -Name MyRG -Location "West US"

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Sin embargo, puesto que todos los recursos del perfil del Administrador de tráfico son globales y no regionales, la elección de la ubicación del grupo de recursos no tiene efecto en el Administrador de tráfico de Azure.

## Creación de un perfil del Administrador de tráfico

Para crear un perfil de Administrador de tráfico, use el cmdlet New-AzureRmTrafficManagerProfile:

	PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Los parámetros son los siguientes:

- Name: el nombre del recurso de ARM para el recurso del perfil del Administrador de tráfico. Los perfiles del mismo grupo de recursos deben tener nombres únicos. Este nombre es independiente del nombre DNS que se utiliza para las consultas de DNS.

- ResourceGroupName: el nombre del grupo de recursos de ARM que contiene el recurso del perfil.

- TrafficRoutingMethod: especifica el método de enrutamiento de tráfico que se utiliza para determinar qué extremo es el que se devuelve en respuesta a las consultas de DNS entrantes. Los valores posibles son ‘Performance’, ‘Weighted’ o ‘Priority’.

- RelativeDnsName: especifica el nombre DNS relativo proporcionado por este perfil del Administrador de tráfico. Este valor se combina con el nombre de dominio DNS utilizado por el Administrador de tráfico de Azure para formar el nombre de dominio completo (FQDN) del perfil. Por ejemplo, el valor ‘contoso’ proporcionará un perfil de Administrador de tráfico con el nombre completo de 'contoso.trafficmanager.net'.

- TTL: especifica el período de vida (TTL) de DNS, en segundos. Esto informa a las resoluciones DNS locales y a los clientes DNS cuánto tiempo se guardan en la memoria caché las respuestas DNS proporcionadas por este perfil del Administrador de tráfico.

- MonitorProtocol: especifica el protocolo que se va a utilizar para supervisar el estado del extremo. Los valores posibles son ‘HTTP’ y ‘HTTPS’.

- MonitorPort: especifica el puerto TCP utilizado para supervisar el estado del extremo.

- MonitorPath: especifica la ruta de acceso en relación con el nombre de dominio de extremo utilizado para sondear el estado del extremo.

El cmdlet crea un perfil del Administrador de tráfico en el Administrador de tráfico de Azure y devuelve un objeto de perfil correspondiente. En este momento, el perfil no contiene ningún punto de conexión, consulte [Adición de puntos de conexión de Administrador de tráfico](#adding-traffic-manager-endpoints) para más información sobre cómo agregar puntos de conexión a un perfil de Administrador de tráfico.

## Obtención de un perfil del Administrador de tráfico

Para recuperar un objeto del perfil de Administrador de tráfico existente, use el cmdlet Get-AzureRmTrafficManagerProfle:

	PS C:\> $profile = Get-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG

Este cmdlet devuelve un objeto del perfil del Administrador de tráfico.

## Actualización de un perfil del Administrador de tráfico [](#update-traffic-manager-profile)

Para modificar perfiles del Administrador de tráfico, por ejemplo, para agregar o quitar extremos o modificar la configuración del perfil, se sigue un proceso de tres pasos:

1.	Recuperar el perfil mediante Get-AzureRmTrafficManagerProfile (o usar el perfil devuelto por New-AzureRmTrafficManagerProfile).

2.	Modificar el perfil, agregando extremos, quitando extremos, cambiando parámetros de extremo o cambiando parámetros de perfil. Estos cambios se realizan sin conexión, solo se cambia el objeto local que representa el perfil.

3.	Confirmar los cambios mediante el cmdlet Set-AzureRmTrafficManagerProfile. Esto reemplaza el perfil existente en el Administrador de tráfico de Azure por el perfil proporcionado.

Todas las propiedades del perfil se pueden cambiar, con la excepción de que el elemento RelativeDnsName del perfil no se puede cambiar una vez creado este último (para cambiar este valor, hay que eliminar el perfil y volverlo a crear).

Por ejemplo, para cambiar el perfil de TTL:

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG
	PS C:\> $profile.Ttl = 300
	PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

## Agregue puntos de conexión de Administrador de tráfico
Existen tres tipos de puntos de conexión de Administrador de tráfico:

1. Puntos de conexión de Azure: representan los servicios hospedados en Azure.<BR>
2. Puntos de conexión externos: representan los servicios hospedados fuera de Azure.<BR>
3. Puntos de conexión anidados: se usan para construir jerarquías anidadas de perfiles de Administrador de tráfico, para habilitar configuraciones avanzadas de enrutamiento de tráfico para las aplicaciones más complejas. Todavía no se admiten a través de la API de ARM.<BR>

En los tres casos, se pueden agregar puntos de conexión de dos maneras:<BR>

1. Mediante un proceso de tres pasos similar al descrito en [Actualización de un perfil de Administrador de tráfico](#update-traffic-manager-profile): obtener el objeto de perfil mediante Get-AzureRmTrafficManagerProfile; actualizarlo sin conexión para agregar un punto de conexión, mediante Add-AzureRmTrafficManagerEndpointConfig; cargar los cambios en el Administrador de tráfico de Azure mediante Set-AzureRmTrafficManagerProfile. La ventaja de este método es que se puede realizar una serie de cambios del punto de conexión en una sola actualización.<BR>

2. Con el cmdlet New-AzureRmTrafficManagerEndpoint. Se agrega un punto de conexión a un perfil de Administrador de tráfico existente en una sola operación.

### Adición de puntos de conexión de Azure

Los puntos de conexión de Azure hacen referencia a los servicios hospedados en Azure. Actualmente, se admiten tres tipos de puntos de conexión de Azure:<BR> 1. Aplicaciones web de Azure <BR> 2. Servicios en la nube "clásicos" (que pueden contener un servicio PaaS o máquinas virtuales IaaS)<BR> 3. Recursos de Microsoft.Network/publicIpAddress de ARM (que se pueden agregar a un equilibrador de carga o a una NIC de máquina virtual). Tenga en cuenta que publicIpAddress debe tener un nombre DNS asignado para usarse en el Administrador de tráfico.

En cada caso: - El servicio se especifica mediante el parámetro 'targetResourceId' de Add-AzureRmTrafficManagerEndpointConfig o New-AzureRmTrafficManagerEndpoint.<BR> - El valor de 'Target' y 'EndpointLocation' no debe especificarse, está implícito en el valor de TargetResourceId especificado antes.<BR> - La especificación del valor de 'Weight' es opcional. Los pesos solo se usan si el perfil se configura para usar el método de enrutamiento de tráfico "ponderado". Si se especifica, deben encontrarse entre 1 y 1000. El valor predeterminado es '1'.<BR> - Especificar el valor de 'Priority' es opcional. Las prioridades solo se usan si el perfil se configura para usar el método de enrutamiento de tráfico de 'Priority'. Los valores válidos van de 1 a 1000 (los valores más bajos son de mayor prioridad). Si se especifica para un punto de conexión, se debe especificar para todos los puntos de conexión. Si se omite, se aplican los valores predeterminados desde 1, 2, 3, etc., en el orden en que se proporcionan los puntos de conexión.

#### Ejemplo 1: Adición de puntos de conexión de la aplicación web mediante Add-AzureRmTrafficManagerEndpointConfig
En este ejemplo, se crea un nuevo perfil de Administrador de tráfico y se agregan dos puntos de conexión de la aplicación web mediante el cmdlet Add-AzureRmTrafficManagerEndpointConfig, después se confirma el perfil actualizado en Administrador de tráfico de Azure mediante el cmdlet Set-AzureRmTrafficManagerProfile.

	PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> $webapp1 = Get-AzureRMWebApp -Name webapp1
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName webapp1ep –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled
	PS C:\> $webapp2 = Get-AzureRMWebApp -Name webapp2
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName webapp2ep –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile  

#### Ejemplo 2: Adición de un punto de conexión del servicio en la nube "clásico" mediante New-AzureRmTrafficManagerEndpoint
En este ejemplo, se agrega un punto de conexión del servicio en la nube "clásico" a un perfil de Administrador de tráfico. Tenga en cuenta que en este caso, elegimos especificar el perfil con el nombre de perfil y el nombre del grupo de recursos, en lugar de pasar un objeto de perfil (se admiten ambos enfoques).

	PS C:\> $cloudService = Get-AzureRmResource -ResourceName MyCloudService -ResourceType "Microsoft.ClassicCompute/domainNames" -ResourceGroupName MyCloudService
	PS C:\> New-AzureRmTrafficManagerEndpoint –Name MyCloudServiceEndpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type AzureEndpoints -TargetResourceId $cloudService.Id –EndpointStatus Enabled

#### Ejemplo 3: Adición de un punto de conexión de publicIpAddress mediante New-AzureRmTrafficManagerEndpoint
En este ejemplo, se agrega un recurso de dirección IP público de ARM al perfil de Administrador de tráfico. La dirección IP pública debe tener un nombre DNS configurado y puede enlazarse a la NIC de una máquina virtual o a un equilibrador de carga.

	PS C:\> $ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
	PS C:\> New-AzureRmTrafficManagerEndpoint –Name MyIpEndpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type AzureEndpoints -TargetResourceId $ip.Id –EndpointStatus Enabled

### Adición de puntos de conexión externos
Administrador de tráfico usa los puntos de conexión externos para dirigir el tráfico a los servicios hospedados fuera de Azure. Al igual que con los puntos de conexión de Azure, los puntos de conexión externos pueden agregarse mediante el cmdlet Add-AzureRmTrafficManagerEndpointConfig seguido de Set-AzureRmTrafficManagerProfile o el cmdlet New-AzureRMTrafficManagerEndpoint.

Al especificar los puntos de conexión externos: - Debe especificarse el nombre de dominio del punto de conexión con el parámetro 'Target'.<BR> - Se requiere el parámetro 'EndpointLocation' si se usa el método de enrutamiento de tráfico 'Performance'; de lo contrario, es opcional. El valor debe ser un [nombre de región de Azure válido](https://azure.microsoft.com/regions/).<BR> - Los parámetros 'Weight' y 'Priority' son opcionales, en cuanto a los puntos de conexión de Azure.<BR>
 

#### Ejemplo 1: Adición de puntos de conexión externos mediante los cmdlets Add-AzureRmTrafficManagerEndpointConfig y Set-AzureRmTrafficManagerProfile
En este ejemplo, se crea un nuevo perfil de Administrador de tráfico, se agregan dos puntos de conexión externos y se confirman los cambios.

	PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName eu-endpoint –TrafficManagerProfile $profile –Type ExternalEndpoints -Target app-eu.contoso.com –EndpointStatus Enabled
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName us-endpoint –TrafficManagerProfile $profile –Type ExternalEndpoints -Target app-us.contoso.com –EndpointStatus Enabled
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile  

#### Ejemplo 2: Adición de puntos de conexión externos mediante el cmdlet New-AzureRmTrafficManagerEndpoint
En este ejemplo, se agrega un punto de conexión externo a un perfil existente, especificado con el nombre del perfil y el nombre del grupo de recursos.

	PS C:\> New-AzureRmTrafficManagerEndpoint –Name eu-endpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type ExternalEndpoints -Target app-eu.contoso.com –EndpointStatus Enabled

### Adición de puntos de conexión "Anidados"

Administrador de tráfico le permite configurar un perfil de Administrador de tráfico (lo llamaremos un perfil "secundario") como un punto de conexión dentro de otro perfil de Administrador de tráfico (al que llamaremos el perfil "primario").

Anidar Administrador de tráfico le permite crear unos esquemas de enrutamiento de tráfico y de conmutación por error más flexibles y potentes para satisfacer las necesidades de implementaciones más grandes y complejas. [Esta entrada de blogs](https://azure.microsoft.com/blog/new-azure-traffic-manager-nested-profiles/) ofrece varios ejemplos.

Los puntos de conexión anidados se configuran en el perfil primario, utilizando un tipo de punto de conexión específico: 'NestedEndpoints'. Al especificar un punto de conexión, este (por ejemplo, el perfil secundario) debe especificarse con el parámetro 'targetResourceId'.<BR> - Así mismo, se requiere el parámetro 'EndpointLocation' si se usa el método de enrutamiento de tráfico 'Performance'; de lo contrario, es opcional. El valor debe ser un [nombre de región de Azure válido](http://azure.microsoft.com/regions/).<BR> - Los parámetros 'Weight' y 'Priority' son opcionales, en cuanto a los puntos de conexión de Azure.<BR> - Además, el parámetro 'MinChildEndpoints' es opcional, valor predeterminado '1'. Si el número de puntos de conexión disponibles en el perfil secundario cae por debajo de este umbral, el perfil primario tendrá en cuenta que dicho perfil está "degradado" y desviará el tráfico a los otros puntos de conexión del perfil primario.<BR>


#### Ejemplo 1: adición de puntos de conexión anidados mediante Add-AzureRmTrafficManagerEndpointConfig y Set-AzureRmTrafficManagerProfile.

En este ejemplo, se crearán los perfiles secundario y primario de Administrador de tráfico, se agregará el secundario como un punto de conexión anidado en el primario y se confirmarán los cambios. (Para mayor brevedad, no agregaremos otros puntos de conexión al perfil secundario o al principal, aunque normalmente también serían necesarios).<BR>

	PS C:\> $child = New-AzureRmTrafficManagerProfile –Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> $parent = New-AzureRmTrafficManagerProfile –Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName child-endpoint –TrafficManagerProfile $parent –Type NestedEndpoints -TargetResourceId $child.Id –EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile

#### Ejemplo 2: adición de puntos de conexión anidados mediante New-AzureRmTrafficManagerEndpoint.

En este ejemplo, se agregará un perfil secundario existente como punto de conexión anidado a un perfil primario existente, especificado con el nombre del perfil y el nombre del grupo de recursos.

	PS C:\> $child = Get-AzureRmTrafficManagerEndpoint –Name child -ResourceGroupName MyRG
	PS C:\> New-AzureRmTrafficManagerEndpoint –Name child-endpoint –ProfileName parent -ResourceGroupName MyRG –Type NestedEndpoints -TargetResourceId $child.Id –EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2


## Actualización de un punto de conexión de Administrador de tráfico
Hay dos maneras de actualizar un punto de conexión de Administrador de tráfico existente:<BR>

1. Obtener el perfil de Administrador de tráfico mediante el cmdlet Get-AzureRmTrafficManagerProfile, actualizar las propiedades del punto de conexión en el perfil y confirmar los cambios mediante Set-AzureRmTrafficManagerProfile. Este método tiene la ventaja de poder actualizar más de un punto de conexión en una sola operación.<BR>
2. Obtener el punto de conexión de Administrador de tráfico mediante el cmdlet Get-AzureRmTrafficManagerEndpoint, actualizar las propiedades del punto de conexión y confirmar los cambios mediante Set-AzureRmTrafficManagerEndpoint. Este método es más sencillo, ya que no requiere la indexación de la matriz de puntos de conexión en el perfil.<BR>

#### Ejemplo 1: Actualización mediante Get-AzureRmTrafficManagerProfile y Set-AzureRmTrafficManagerProfile
En este ejemplo, se va a modificar la prioridad de los dos puntos de conexión de un perfil existente.

	PS C:\> $profile = Get-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName MyRG
	PS C:\> $profile.Endpoints[0].Priority = 2
	PS C:\> $profile.Endpoints[1].Priority = 1
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile

#### Ejemplo 2: Actualización de un punto de conexión con Get-AzureRmTrafficManagerEndpoint y Set-AzureRmTrafficManagerEndpoint
En este ejemplo, se va a modificar el peso de un punto de conexión de un perfil existente.

	PS C:\> $endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
	PS C:\> $endpoint.Weight = 20
	PS C:\> Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint

## Habilitación y deshabilitación de puntos de conexión y perfiles
Administrador de tráfico permite que se habiliten y deshabiliten puntos de conexión individuales, además de permitir la habilitación y deshabilitación de perfiles completos. Estos cambios pueden realizarse mediante la obtención, actualización o configuración los recursos del perfil o del punto de conexión. Para facilitar estas operaciones comunes, también se admiten a través de cmdlets dedicados.

#### Ejemplo 1: Habilitación y deshabilitación de un perfil de Administrador de tráfico
Para habilitar un perfil de Administrador de tráfico, use el cmdlet Enable-AzureRmTrafficManagerProfile. El perfil se puede especificar mediante un objeto de perfil (pasado a través de la canalización o usando el parámetro '-TrafficManagerProfile') o bien especificando el nombre del perfil y el nombre del grupo de recursos directamente, como en este ejemplo.

	PS C:\> Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup

De forma similar, para deshabilitar un perfil de Administrador de tráfico:

	PS C:\> Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup

El cmdlet Disable-AzureRmTrafficManagerProfile le pedirá confirmación, este aviso se puede suprimir con el parámetro '-Force'.

#### Ejemplo 2: Habilitación y deshabilitación de un punto de conexión de Administrador de tráfico
Para habilitar un punto de conexión de Administrador de tráfico, use el cmdlet Enable-AzureRmTrafficManagerEndpoint. El punto de conexión se puede especificar mediante un objeto TrafficManagerEndpoint (pasado a través de la canalización o mediante el parámetro '-TrafficManagerEndpoint'), o con el nombre de punto de conexión, tipo de punto de conexión, nombre del perfil y el nombre del grupo de recursos:

	PS C:\> Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG

De forma similar, para deshabilitar un punto de conexión de Administrador de tráfico:

 	PS C:\> Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force

Al igual que con Disable-AzureRmTrafficManagerProfile, el cmdlet Disable-AzureRmTrafficManagerEndpoint incluye un aviso de confirmación que se puede suprimir con el parámetro '-Force'.

## Eliminación de un punto de conexión de Administrador de tráfico
Es una manera de eliminar un punto de conexión de Administrador de tráfico recuperar el objeto de perfil (mediante Get-AzureRmTrafficManagerProfile), actualizar la lista de puntos de conexión en el objeto de perfil local y confirmar los cambios (mediante el conjunto AzureRmTrafficManagerProfile). Este método permite que varios cambios en el punto de conexión se confirmen juntos.

Otra manera de quitar los puntos de conexión individuales es mediante el cmdlet Remove-AzureRmTrafficManagerEndpoint:

	PS C:\> Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
	
Este cmdlet le pedirá confirmación, a menos que el se use el parámetro '-Force' para suprimir el aviso.

## Eliminación de un perfil del Administrador de tráfico
Para eliminar un perfil de Administrador de tráfico, use el cmdlet Remove-AzureRmTrafficManagerProfile, especificando el nombre del perfil y el nombre del grupo de recursos:

	PS C:\> Remove-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG [-Force]

Este cmdlet le pedirá confirmación. Se puede utilizar el conmutador '-Force' opcional para suprimir este mensaje. El perfil que se va a eliminar también se puede especificar con un objeto de perfil:

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG
	PS C:\> Remove-AzureTrafficManagerProfile –TrafficManagerProfile $profile [-Force]

Se puede canalizar igualmente esta secuencia:

	PS C:\> Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG | Remove-AzureTrafficManagerProfile [-Force]

## Pasos siguientes

[Supervisión del Administrador de tráfico](traffic-manager-monitoring.md)

[Consideraciones de rendimiento sobre el Administrador de tráfico](traffic-manager-performance-considerations.md)
 

<!---HONumber=AcomDC_0218_2016-->