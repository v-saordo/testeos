<properties
   pageTitle="Servicios, regiones, esquemas y versiones compatibles con el Administrador de recursos | Microsoft Azure"
   description="Describe los proveedores de recursos que admiten el Administrador de recursos, sus esquemas y las versiones disponibles de API y las regiones que pueden hospedar los recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/01/2016"
   ms.author="tomfitz"/>

# Proveedores, regiones, versiones de API y esquemas del Administrador de recursos

El Administrador de recursos de Azure le proporciona una nueva manera de implementar y administrar los servicios que conforman sus aplicaciones. La mayoría de los servicios, aunque no todos, admiten el Administrador de recursos y algunos lo admiten solo parcialmente. Microsoft habilitará el Administrador de recursos para cada servicio que sea importante para las soluciones futuras, pero hasta que la compatibilidad sea coherente, debe conocer el estado actual de cada servicio. Este tema proporciona una lista de proveedores de recursos admitidos para el Administrador de recursos de Azure.

Al implementar sus recursos, también debe saber qué regiones admiten esos recursos y qué versiones de API están disponibles para los recursos. En la sección [Regiones admitidas](#supported-regions) se muestra cómo averiguar qué regiones funcionarán para su suscripción y recursos. En la sección [Versiones de API admitidas](#supported-api-versions) se muestra cómo determinar las versiones de API que puede usar.

Para ver los servicios que se admiten en el Portal de Azure y el Portal clásico, consulte el [gráfico de disponibilidad del Portal de Azure](https://azure.microsoft.com/features/azure-portal/availability/). Para ver los servicios que admiten el traslado de recursos, consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](resource-group-move-resources.md).

En las tablas siguientes se muestra qué servicios admiten la implementación y administración a través del Administrador de recursos y cuáles no. El vínculo de la columna **Plantillas de inicio rápido** envía una consulta al repositorio de plantillas de inicio rápido de Azure para el proveedor de recursos especificado. Las plantillas de inicio rápido se agregan y se actualizan con frecuencia. La presencia de un vínculo para un servicio concreto no significa necesariamente que la consulta devolverá las plantillas del repositorio.


## Proceso

| Servicio | Administrador de recursos habilitado | API de REST | Esquema | Plantillas de inicio rápido |
| ------- | ------------------------ |-------- | ------ | ------ |
| Lote | Sí | [REST de Lote](https://msdn.microsoft.com/library/azure/dn820158.aspx) | | [Microsoft.Batch](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Batch%22&type=Code) |
| Dynamics Lifecycle Services | Sí | | | [Microsoft.DynamicsLcs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DynamicsLcs%22&type=Code)
| Service Fabric (vista previa) | Sí | [Rest de Service Fabric](https://msdn.microsoft.com/library/azure/dn707692.aspx) | | [Microsoft.ServiceFabric](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceFabric%22&type=Code) |
| Máquinas virtuales | Sí | [VM REST](https://msdn.microsoft.com/library/azure/mt163647.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json) | [Microsoft.Compute](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Compute%22&type=Code) |
| Máquinas virtuales (clásicas) | Limitado | - | - | 
|Aplicación remota | No | - | - |
| Servicios en la nube (clásicos) | Limitado (ver a continuación) | - | - | - |

Máquinas virtuales (clásicas) hace referencia a recursos que se implementaron mediante el modelo de implementación clásica, en lugar de a través del modelo de implementación del Administrador de recursos. En general, estos recursos no admiten las operaciones del Administrador de recursos, pero hay algunas operaciones que se han habilitado. Para obtener más información sobre estos modelos de implementación, vea [Descripción de la implementación del Administrador de recursos y la implementación clásica](resource-manager-deployment-model.md).

Los Servicios en la nube (clásicos) se pueden usar con otros recursos clásicos. Sin embargo, los recursos clásicos no aprovechan todas las características del Administrador de recursos y no son una buena opción para las soluciones futuras. En su lugar, considere la posibilidad de cambiar la infraestructura de aplicaciones para usar recursos de los espacios de nombres Microsoft.Compute, Microsoft.Storage y Microsoft.Network.


## Redes

| Servicio | Administrador de recursos habilitado | API de REST | Esquema | Plantillas de inicio rápido |
| ------- | -------  | -------- | ------ | ------ |
| Puerta de enlace de aplicaciones | Sí | | | [applicationGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FapplicationGateways%22&type=Code) |
| DNS | Sí | [DNS REST](https://msdn.microsoft.com/library/azure/mt163862.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [dnsZones](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FdnsZones%22&type=Code) |
| ExpressRoute | Sí | [REST de ExpressRoute](https://msdn.microsoft.com/library/azure/mt586720.aspx) | | [expressRouteCircuits](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FexpressRouteCircuits%22&type=Code) |
| Equilibrador de carga | Sí | [REST del Equilibrador de carga](https://msdn.microsoft.com/library/azure/mt163651.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [loadBalancers](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Floadbalancers%22&type=Code) |
| Administrador de tráfico | Sí | [REST para Administrador de tráfico](https://msdn.microsoft.com/library/azure/mt163667.aspx) | [2015-11-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-11-01/Microsoft.Network.json) | [trafficmanagerprofiles](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Ftrafficmanagerprofiles%22&type=Code) |
| Redes virtuales | Sí| [REST para Red virtual](https://msdn.microsoft.com/es-ES/library/azure/mt163650.aspx) | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json) | [virtualNetworks](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworks%22&type=Code) |
| Puerta de enlace de VPN | Sí | [REST de puerta de enlace de red](https://msdn.microsoft.com/library/azure/mt163859.aspx) | | [virtualNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FvirtualNetworkGateways%22&type=Code) <br /> [localNetworkGateways](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2FlocalNetworkGateways%22&type=Code) <br />[connections](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Network%2Fconnections%22&type=Code) |



## Datos y almacenamiento

| Servicio | Administrador de recursos habilitado | API de REST | Esquema | Plantillas de inicio rápido |
| ------- | ------- | -------- | ------ | ------- | ------ |
| DocumentDB | Sí | [REST de DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) | [2015-04-08](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-08/Microsoft.DocumentDB.json) | [Microsoft.DocumentDB](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DocumentDb%22&type=Code) |
| Caché en Redis | Sí | | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Cache.json) | [Microsoft.Cache](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cache%22&type=Code) |
| Search | Sí | [REST de Búsqueda](https://msdn.microsoft.com/library/azure/dn798935.aspx) | | [Microsoft.Search](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Search%22&type=Code) |
| Almacenamiento | Sí | [API de Almacenamiento](https://msdn.microsoft.com/library/azure/mt163683.aspx) | [Cuenta de almacenamiento](resource-manager-template-storage.md) | [Microsoft.Storage](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Storage%22&type=Code) |
| Base de datos SQL | Sí | [REST de Base de datos SQL](https://msdn.microsoft.com/library/azure/mt163571.aspx) | [2014-04-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01-preview/Microsoft.Sql.json) | [Microsoft.Sql](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Sql%22&type=Code) |
| Almacenamiento de datos SQL | Sí | | |
| StorSimple | No | - | - | - |

## Web y móvil

| Servicio | Administrador de recursos habilitado | API de REST | Esquema | Plantillas de inicio rápido |
| ------- | ------- | -------- | ------ | ------ |
| Aplicaciones de API | Sí | | [2015-03-01-preview](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-03-01-preview/Microsoft.AppService.json) | [Aplicaciones de API](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22kind%22%3A+%22apiApp%22&type=Code) |
| Administración de API | Sí | [REST de Administración de API](https://msdn.microsoft.com/library/azure/dn776326.aspx) | | [Microsoft.ApiManagement](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ApiManagement%22&type=Code) | 
| Aplicaciones lógicas | Sí | | | [Microsoft.Logic](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Logic%22&type=Code) |
| Aplicaciones móviles | Sí | | | |
| Compromisos móviles | Sí | | | [Microsoft.MobileEngagements](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.MobileEngagement%22&type=Code) |
| Aplicaciones web | Sí | | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json) | [Microsoft.Web](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Web%22&type=Code) |

## Análisis

| Servicio | Administrador de recursos habilitado | API de REST | Esquema | Plantillas de inicio rápido |
| ------- | -------  | -------- | ------ | ------ |
| Factoría de datos | Sí | [REST de Factoría de datos](https://msdn.microsoft.com/library/azure/dn906738.aspx) | | [Microsoft.DataFactory](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DataFactory%22&type=Code) |
| Análisis de Data Lake | Sí | | | |
| Almacén de Data Lake | Sí | | | |
| HDInsights | Sí | [REST de HDInsights](https://msdn.microsoft.com/library/azure/mt622197.aspx) | | [Microsoft.HDInsight](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.HDInsight%22&type=Code) |
| Análisis de transmisiones | Sí | [REST de Análisis de transmisiones](https://msdn.microsoft.com/library/azure/dn835031.aspx) | | [Microsoft.StreamAnalytics](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.StreamAnalytics%22&type=Code) |
| Aprendizaje automático | No | - | - |
| Catálogo de datos | No | - | - |

## Internet de las cosas

| Servicio | Administrador de recursos habilitado | API de REST | Esquema | Plantillas de inicio rápido |
| ------- | ------- | -------- | ------ | ------ |
| Centro de eventos | Sí | [REST del Centro de eventos](https://msdn.microsoft.com/library/azure/dn790674.aspx) | | [Microsoft.EventHub](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.EventHub%22&type=Code) |
| IoTHubs | Sí | [REST de Centro de IoT](https://msdn.microsoft.com/library/azure/mt589014.aspx) | | [Microsoft.Devices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Devices%22&type=Code) |
| Centros de notificaciones | Sí | [REST de Centro de notificaciones](https://msdn.microsoft.com/library/azure/dn495827.aspx) | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json) | [Microsoft.NotificationHubs](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.NotificationHubs%22&type=Code) |

## Multimedia y CDN

| Servicio | Administrador de recursos habilitado | API de REST | Esquema | Plantillas de inicio rápido |
| ------- | ------- | -------- | ------ | ------ |
| Servicio CDN | Sí | [REST de CDN](https://msdn.microsoft.com/library/azure/mt634456.aspx) | | [Microsoft.Cdn](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Cdn%22&type=Code) |
| Servicio multimedia | No | | |


## Integración híbrida

| Servicio | Administrador de recursos habilitado | API de REST | Esquema | Plantillas de inicio rápido |
| ------- | ------- | -------- | ------ | ------ |
| Servicios de BizTalk | Sí | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json) | [Microsoft.BizTalkServices](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.BizTalkServices%22&type=Code) |
| Bus de servicio | Sí | | | [Microsoft.ServiceBus](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.ServiceBus%22&type=Code) |
| Copia de seguridad | No | - | - | 
| Recuperación del sitio | No | - | - |

## Administración de identidad y acceso 

Azure Active Directory funciona con el administrador de recursos para habilitar el control de acceso basado en roles de la suscripción. Para obtener más información sobre el uso del control de acceso basado en roles y Active Directory, consulte [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md).

## Servicios de desarrollador 

| Servicio | Administrador de recursos habilitado | API de REST | Esquema | Plantillas de inicio rápido |
| ------- | ------- | -------- | ------ | ------ |
| Application Insights | Sí | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json) | [Microsoft.insights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.insights%22&type=Code) |
| Mapas de Bing | Sí | | | [Microsoft.BingMaps](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.BingMaps%22&type=Code) |
| Laboratorios de DevTest (vista previa) | Sí | | [Vista previa 2015-05-21](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-05-21-preview/Microsoft.DevTestLab.json) | [Microsoft.DevTestLab](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.DevTestLab%22&type=Code) |
| Cuenta de Visual Studio | Sí | | [2014-02-26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json) | [Microsoft.VisualStudio](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.VisualStudio%22&type=Code) |

## Administración y seguridad

| Servicio | Administrador de recursos habilitado | API de REST | Esquema | Plantillas de inicio rápido |
| ------- | ------- | -------- | ------ | ------ |
| Automatización | Sí | | | [Microsoft.Automation](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Automation%22&type=Code) |
| Almacén de claves | Sí | [REST de Almacén de claves](https://msdn.microsoft.com/library/azure/dn903609.aspx) | [Almacén de claves](resource-manager-template-keyvault.md)<br />[Secreto del almacén de claves](resource-manager-template-keyvault-secret.md) | [Microsoft.KeyVault](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.KeyVault%22&type=Code) |
| Visión operativa | Sí | | | [Microsoft.OperationalInsights](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.OperationalInsights%22&type=Code) |
| Programador | Sí | [REST de Programador](https://msdn.microsoft.com/library/azure/mt629143.aspx) | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-08-01/Microsoft.Scheduler.json) | [Microsoft.Scheduler](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Scheduler%22&type=Code) |
| Seguridad (vista previa) | Sí | | | [Microsoft.Security](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Security%22&type=Code) |

## Resource Manager

| Característica | Administrador de recursos habilitado | API de REST | Esquema | Plantillas de inicio rápido |
| ------- | ------- | -------------- | -------- | ------ | ------ |
| Autorización | Sí | [Bloqueos de administración](https://msdn.microsoft.com/library/azure/mt204563.aspx)<br >[Control de acceso basado en roles](https://msdn.microsoft.com/library/azure/dn906885.aspx) | [Bloqueo de recurso](resource-manager-template-lock.md)<br />[Asignaciones de roles](resource-manager-template-role.md) | [Microsoft.Authorization](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Authorization%22&type=Code) |
| Recursos | Sí | [Linked Resources](https://msdn.microsoft.com/library/azure/mt238499.aspx) | [Vínculos de recursos](resource-manager-template-links.md) | [Microsoft.Resources](https://github.com/Azure/azure-quickstart-templates/search?utf8=%E2%9C%93&q=%22Microsoft.Resources%22&type=Code) |


## Tipos y proveedores de recursos

Al implementar los recursos, con frecuencia necesitará recuperar información sobre los tipos y proveedores de recursos. Puede recuperar esta información a través de la API de REST, Azure PowerShell o CLI de Azure.

Para trabajar con un proveedor de recursos, dicho proveedor debe estar registrado con su cuenta. De forma predeterminada, muchos proveedores de recursos se registran automáticamente. Sin embargo, debe registrar manualmente algunos de ellos. Los ejemplos siguientes muestran cómo obtener el estado de registro de un proveedor de recursos y cómo registrarlo, si es necesario.

### API de REST

Para obtener todos los proveedores de recursos disponibles, incluidos sus tipos, ubicaciones, versiones de API y estado de registro, use la operación [Enumerar todos los proveedores de recursos](https://msdn.microsoft.com/library/azure/dn790524.aspx). Si necesita registrar un proveedor de recursos, consulte [Registro de una suscripción con un proveedor de recursos](https://msdn.microsoft.com/library/azure/dn790548.aspx).

### PowerShell

En el ejemplo siguiente se muestra cómo obtener todos los proveedores de recursos disponibles.

    PS C:\> Get-AzureRmResourceProvider -ListAvailable
    
La salida debe ser similar a:

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    ...

En el ejemplo siguiente se muestra cómo obtener los tipos de recursos para un proveedor de recursos determinado.

    PS C:\> (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes
    
La salida debe ser similar a:

    ResourceTypeName                Locations                                         ApiVersions
    ----------------                ---------                                         ------
    sites/extensions                {Brazil South, East Asia, East US, Japan East...} {20...
    sites/slots/extensions          {Brazil South, East Asia, East US, Japan East...} {20...
    ...
    
Para registrar un proveedor de recursos, proporcione el espacio de nombres:

    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ApiManagement

### Azure CLI

En el ejemplo siguiente se muestra cómo obtener todos los proveedores de recursos disponibles.

    azure provider list
    
La salida debe ser similar a:

    info:    Executing command provider list
    + Getting ARM registered providers
    data:    Namespace                        Registered
    data:    -------------------------------  -------------
    data:    Microsoft.ApiManagement          Unregistered
    data:    Microsoft.AppService             Registered
    data:    Microsoft.Authorization          Registered
    ...

Puede guardar la información para un proveedor de recursos particular en un archivo con el siguiente comando.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Para registrar un proveedor de recursos, proporcione el espacio de nombres:

    azure provider register -n Microsoft.ServiceBus

## Regiones admitidas

Al implementar recursos, normalmente debe especificar una región para los recursos. El Administrador de recursos se admite en todas las regiones, pero puede que los recursos que implementa no se admitan en todas las regiones. Además, puede haber limitaciones en su suscripción que le impidan utilizar algunas regiones que admiten el recurso. Estas limitaciones pueden estar relacionadas con problemas fiscales de su país de origen o ser el resultado de una directiva creada por su administrador de suscripción para su uso solo en determinadas regiones.

Para obtener una lista completa de todas las regiones admitidas para todos los servicios de Azure, consulte [Servicios por región](https://azure.microsoft.com/regions/#services); sin embargo, esta lista puede incluir las regiones no compatibles con su suscripción. Puede determinar las regiones para un tipo de recurso concreto que admite su suscripción mediante la ejecución de uno de los comandos siguientes.

### API de REST

Para detectar qué regiones están disponibles para un tipo de recurso determinado en su suscripción, use la operación [Enumerar todos los proveedores de recursos](https://msdn.microsoft.com/library/azure/dn790524.aspx).

### PowerShell

En el ejemplo siguiente se muestra cómo obtener las regiones admitidas para sitios web.

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
La salida debe ser similar a:

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

### Azure CLI

El ejemplo siguiente devuelve todas las ubicaciones admitidas para cada tipo de recurso.

    azure location list

También puede filtrar los resultados de ubicación con una herramienta como **jq**. Para obtener información sobre herramientas como jq, consulte [Herramientas útiles para interactuar con Azure](/virtual-machines/resource-group-deploy-debug/#useful-tools-to-interact-with-azure).

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

Que devuelve:

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

## Versiones de API compatibles

Cuando implemente una plantilla, debe especificar una versión de API que se usará para crear cada recurso. La versión de API se corresponde a una versión de operaciones de API de REST que se publican por el proveedor de recursos. Conforme un proveedor de recursos habilite nuevas características, publicará una nueva versión de la API de REST. Por lo tanto, la versión de la API que especifica en la plantilla afecta a qué propiedades puede especificar en la plantilla. En general, deseará seleccionar la versión de la API más reciente al crear nuevas plantillas. Para las plantillas existentes, puede decidir si quiere continuar usando una versión anterior de la API o actualizar la plantilla para que la versión más reciente aproveche las nuevas características.

### API de REST

Para detectar las versiones de la API que están disponibles para los tipos de recursos, use la operación [Enumerar todos los proveedores de recursos](https://msdn.microsoft.com/library/azure/dn790524.aspx).

### PowerShell

En el ejemplo siguiente se muestra cómo obtener las versiones de API disponibles para un tipo de recurso concreto.

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    
La salida debe ser similar a:
    
    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

### Azure CLI

Puede guardar la información (incluidas las versiones disponibles de la API) para un proveedor de recursos en un archivo con el siguiente comando.

    azure provider show Microsoft.Web -vv --json > c:\temp.json

Puede abrir el archivo y buscar el elemento **apiVersions**.

## Pasos siguientes

- Para obtener más información sobre la creación de plantillas del Administrador de recursos, consulte [Creación de plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md).
- Para obtener más información sobre la implementación de recursos, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](resource-group-template-deploy.md).

<!-----HONumber=AcomDC_0302_2016-->