<properties
	pageTitle="API de REST de administración de Búsqueda de Azure versión 2015-02-28 | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="API de REST de administración de Búsqueda de Azure: versión 2015-02-28"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="02/04/2016"
	ms.author="heidist" />

# API de administración: versión 2015-02-28

Búsqueda de Azure es un servicio de búsqueda hospedado en la nube en Microsoft Azure. En este documento se describe la versión **2015-02-28* de la API de REST de administración de Búsqueda de Azure. Desde entonces se reemplazó por versiones más recientes. Para la versión más reciente, vea [API de REST de administración de Búsqueda de Azure 2015-08-19](https://msdn.microsoft.com/library/dn832684.aspx) en MSDN.

##Operaciones de administración de servicio

La API de REST de administración del Servicio Búsqueda de Azure proporciona acceso mediante programación a gran parte de las funciones disponibles a través del portal, permitiendo así que los administradores automaticen las operaciones siguientes:

- Crear o eliminar un servicio Búsqueda de Azure.
- Cree, regenere o modifique `api-keys` para automatizar cambios periódicos a las claves administrativas que se usan para autenticar operaciones de datos de búsqueda. 
- Ajustar la escala de un servicio Búsqueda de Azure en respuesta a cambios en los requisitos de almacenamiento o de volumen de la consulta.

Para administrar completamente el servicio mediante programación, necesitará dos API: la API de REST de administración de Búsqueda de Azure y la [API de REST del Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx) común. La API del Administrador de recursos se utiliza para operaciones generales que no son específicas del servicio, como consultar datos de suscripción, enumerar ubicaciones geográficas, etcétera. Para crear y administrar los servicios Búsqueda de Azure de su suscripción, asegúrese de que la solicitud HTTP incluye el extremo del Administrador de recursos, el identificador de suscripción, el proveedor (en este caso, Búsqueda de Azure) y la operación específica del servicio de búsqueda.

[Introducción a la API de REST de administración de Búsqueda de Azure](http://go.microsoft.com/fwlink/p/?linkID=516968) es un tutorial con código de ejemplo que muestra las operaciones de configuración de la aplicación y administración del servicio. La aplicación de ejemplo envía solicitudes a la API del Administrador de recursos de Azure y a la API de administración del servicio Búsqueda de Azure; así podrá hacerse una idea de cómo estructurar una aplicación coherente que se base en ambas API.

### Extremo ###

El extremo para las operaciones de administración del servicio es la dirección URL del Administrador de recursos de Azure, `https://management.azure.com`.

Tenga en cuenta que todas las llamadas a la API de administración deben incluir el identificador de suscripción y una versión de API.

### Versiones ###

La versión actual de la API de REST de administración de Búsqueda de Azure es `api-version=2015-02-28`. La versión anterior, `api-version=2014-07-31-Preview`, está en desuso. Aunque continuará funcionando durante los próximos meses, le animamos a que cambie a la nueva versión lo más pronto posible.

### Autenticación y control de acceso###

La API de REST de administración de Búsqueda de Azure es una extensión del Administrador de recursos de Azure y comparte sus dependencias. Por lo tanto, Active Directory es un requisito previo para la administración del servicio Búsqueda de Azure. Todas las solicitudes administrativas desde el código de cliente deben autenticarse con Azure Active Directory antes de que la solicitud llegue al administrador de recursos.

Tenga en cuenta que, si el código de la aplicación controla tanto las *operaciones de administración de servicios* como las *operaciones de datos* en índices o documentos, utilizará dos métodos de autenticación para cada una de las API de Búsqueda de Azure:

- Debido a la dependencia del Administrador de recursos, la administración del servicio y de las claves se basa en Active Directory para la autenticación.
- Las solicitudes de datos al extremo del servicio Búsqueda de Azure, como Create Index o Search Documents, usan una `api-key` en el encabezado de solicitud. Consulte [API de REST del Servicio Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx) para obtener información sobre la autenticación de una solicitud de datos.

La aplicación de ejemplo que se documenta en [Introducción a la API de REST de administración de Búsqueda de Azure](http://go.microsoft.com/fwlink/p/?linkID=516968) muestra las técnicas de autenticación para cada tipo de operación. La introducción incluye instrucciones para configurar una aplicación cliente de forma que use utilizar Active Directory.

El control de acceso para el Administrador de recursos de Azure usa los roles integrados de lector, colaborador y propietario. De forma predeterminada, todos los administradores de servicios son miembros del rol de propietario. Para obtener más información, vea [Control de acceso basado en roles en el Portal de Azure clásico](../active-directory/role-based-access-control-configure.md).


### Resumen de API ##

Las operaciones incluyen las siguientes API:

- <a name="CreateService">Creación de servicio de búsqueda</a>

    `PUT	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28`

- <a name="GetService">Obtención de servicio de búsqueda</a>

    `GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28`

- <a name="ListService">Enumeración de servicios de búsqueda</a>

    `GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices?api-version=2015-02-28`

- <a name="DeleteService">Eliminación de servicio de búsqueda</a>

    `DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28`

- <a name="UpdateService">Actualización de servicio de búsqueda</a>

    `PATCH https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28`

- <a name="ListAdminKey">Enumeración de claves de administración</a>

    `POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listAdminKeys?api-version=2015-02-28`

- <a name="RegenAdminKey">Regeneración de clave de administración</a>

    `POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/regenerateAdminKey/[keyKind]?api-version=2015-02-28`

- <a name="CreateQueryKey">Creación de clave de consulta</a>

    `POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/createQueryKey/[name]?api-version=2015-02-28`

- <a name="ListQueryKey">Enumeración de claves de consulta</a>

    `GET	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listQueryKeys?api-version=2015-02-28`

- <a name="DeleteQueryKey">Eliminación de claves de consulta</a>

    `DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/deleteQueryKey/[key]?api-version=2015-02-28`

<a name="ServiceOps"></a>
##Operaciones del servicio

Puede aprovisionar o desaprovisionar los servicios Búsqueda de Azure enviando solicitudes HTTP según su suscripción de Azure. Entre los escenarios que posibilitan estas operaciones están la creación de herramientas de administración personalizadas y la implementación de un entorno de producción o desarrollo completo (desde la creación de servicio hasta la alimentación de un índice). Del mismo modo, los proveedores de soluciones que diseñan y venden soluciones de nube pueden desear un enfoque automatizado y repetible para aprovisionar servicios a cada cliente nuevo.

**Operaciones en un servicio**

Las opciones relativas a los servicios incluyen las siguientes API:

- <a name="CreateService">Creación de servicio de búsqueda</a>
- <a name="GetService">Obtención de servicio de búsqueda</a>
- <a name="ListService">Enumeración de servicios de búsqueda</a>
- <a name="DeleteService">Eliminación de servicio de búsqueda</a>
- <a name="UpdateService">Actualización de servicio de búsqueda</a>


<a name="CreateService"></a>
###Creación de servicio de búsqueda

La operación **Creación de servicio de búsqueda** aprovisiona un nuevo servicio de búsqueda con los parámetros especificados. Esta API puede utilizarse también para actualizar una definición de servicio existente.

    PUT	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

#### Parámetros de URI de solicitud

`subscriptionId`: obligatorio. El `subscriptionID` del usuario de Azure. Puede obtener este valor en la API del Administrador de recursos o el portal de Azure.

`resourceGroupName`: obligatorio. El nombre del grupo de recursos dentro de la suscripción del usuario. Puede obtener este valor en la API del Administrador de recursos o el portal de Azure.

`serviceName`: obligatorio. El nombre del servicio de búsqueda dentro del grupo de recursos especificado. Los nombres de los servicios solo pueden contener letras minúsculas, números o guiones, no pueden contener un guion como primero, segundo o último carácter, no pueden contener guiones consecutivos y deben tener entre 2 y 15 caracteres de longitud. Puesto que todos los nombres terminan siendo <name>.search.windows.net, los nombres de servicio deben ser únicos globalmente. No puede haber dos servicios en todas las suscripciones y grupos de recursos que tengan el mismo nombre. El nombre del servicio no se puede cambiar después de crearlo.

`api-version`: obligatorio. Especifica la versión del protocolo utilizada para esta solicitud. La versión actual es `2015-02-28`.


#### Encabezados de solicitud

`Content-Type`: obligatorio. Defina este encabezado como application/json.

`x-ms-client-request-id`: opcional. Un valor GUID generado por el cliente que identifica esta solicitud. Si se especifica, se incluirá en la información de respuesta como una manera de asignar la solicitud.


#### Cuerpo de la solicitud

{ "location": "ubicación del servicio de búsqueda", "tags": { "clave": "valor", ... }, "properties": { "sku": { "nombre": "free | standard | standard2 " },"replicaCount": 1 | 2 | 3 | 4 | 5 | 6, "partitionCount": 1 | 2 | 3 | 4 | 6 | 12 } }

#### Parámetros del cuerpo de la solicitud#

`location`: obligatorio. Una de las regiones geográficas de Azure admitidas y registradas; por ejemplo, West US (oeste de EE. UU.), East US (este de EE. UU.), Southeast Asia (sudeste de Asia), etc. Tenga en cuenta que no se puede cambiar la ubicación de un recurso después de crearlo.

`tags`: opcional. Una lista de pares de clave y valor que describen el recurso. Estas etiquetas pueden utilizarse para visualizar y agrupar un recurso a través de grupos de recursos. Puede proporcionar un máximo de 10 etiquetas para cada recurso. Cada etiqueta debe tener una clave no superior a 128 caracteres y un valor no superior a 256 caracteres.

`sku`: obligatorio. Los valores válidos son `free` y `standard`. `standard2` también es válido, pero solo se puede usar si el soporte técnico de Microsoft lo ha habilitado para su suscripción de Azure. `free` aprovisiona el servicio en clústeres compartidos. `standard` aprovisiona el servicio en clústeres dedicados. En el nivel de precios gratuito solo se puede crear un servicio de búsqueda. Los servicios adicionales se deben crear en el nivel de precios estándar. De forma predeterminada, se crea un servicio con una partición y una réplica. Las réplicas y particiones adicionales tienen un precio basado en unidades de búsqueda. Consulte [Límites y restricciones](search-limits-quotas-capacity.md) para obtener detalles. El `sku` no se puede cambiar después de crear el servicio.

`replicaCount`: opcional. El valor predeterminado es 1. Los valores válidos son de 1 a 6. Válido solamente cuando `sku` es `standard`.

`partitionCount`: opcional. El valor predeterminado es 1. Los valores válidos son 1, 2, 3, 4, 6 o 12. Válido solamente cuando `sku` es `standard`.


### Respuesta

Cuando se actualiza una definición de servicio, se devuelve HTTP 200 (OK). Cuando se crea un nuevo servicio, se devuelve HTTP 201 (Created).


#### Encabezados de respuesta

`Content-Type`: este encabezado siempre está definido como application/json.

`x-ms-request-id`: identificador único para la operación actual, generado por el servicio.


#### Cuerpo de respuesta 

Para HTTP 200 y 201, el cuerpo de la respuesta contiene la definición del servicio.
    
    { 
      "id": "/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]", 
    
      "name": "service name", 
      "location": "location of search service", 
    "type": " Microsoft.Search/searchServices", 
      "tags": {
        "key": "value",
        ...
      },  
    "properties": { 
    	"sku": { 
          "name": "free | standard | standard2" 
       		 }, 
        "replicaCount": 1 | 2 | 3 | 4 | 5 | 6, 
        "partitionCount": 1 | 2 | 3 | 4 | 6 | 12, 
        "status": "running | provisioning | deleting | degraded | disabled | error", 
        "statusDetails": "details of the status", 
        "provisioningState": "succeeded | provisioning | failed" 
      } 
    } 


#### Elementos del cuerpo de respuesta

`id`: el identificador es la dirección URL (sin incluir la combinación de nombre de host) de este servicio de búsqueda.

`name`: el nombre del servicio de búsqueda.

`location`: una de las regiones geográficas de Azure admitidas y registradas; por ejemplo, West US (oeste de EE. UU.), East US (este de EE. UU.), Southeast Asia (sudeste de Asia), etc.

`tags`: una lista de pares de clave y valor que describen el recurso, utilizada para ver y agrupar recursos a través de grupos de recursos.

`sku`: indica el nivel de precios. Los valores válidos son:

- `free`: clúster compartido.
- `standard`: clúster dedicado.
- `standard2`: utilice solo por indicación del servicio de soporte técnico de Microsoft. 

`replicaCount`: indica cuántas réplicas tiene el servicio. Los valores válidos son de 1 a 6.

`partitionCount`: indica cuántas particiones tiene el servicio. Los valores válidos son 1, 2, 3, 4, 6 o 12.

`status`: estado del servicio de búsqueda en el momento en que se llamó a la operación. Los valores válidos son:

- `running`: se crea el servicio de búsqueda.
- `provisioning`: se está aprovisionando el servicio de búsqueda.
- `deleting`: se está eliminando el servicio de búsqueda.
- `degraded`: el servicio de búsqueda está degradado. Esto puede ocurrir cuando el clúster encuentra un error que podría impedir que el servicio funcionara correctamente.
- `disabled`: la Búsqueda está deshabilitada. En este estado, el servicio rechazará todas las solicitudes de API.
- `error`: el servicio de búsqueda está en estado de error. 

**Nota**: Si el servicio está en el estado `degraded`, `disabled` o `error`, significa que el equipo de Búsqueda de Azure está investigando activamente el problema subyacente. En estos estados, los servicios dedicados son todavía facturables en función del número de unidades de búsqueda aprovisionado.

`statusDetails`: detalles del estado.

`provisioningState`: indica el estado actual de la provisión de servicio. Los valores válidos son:

- `succeeded`: el aprovisionamiento se ha realizado correctamente.
- `provisioning`: se está aprovisionando el servicio.
- `failed`: no se está aprovisionando el servicio. 

El aprovisionamiento es un estado intermedio que se produce cuando se está estableciendo la capacidad de servicio. Cuando se configura la capacidad, `provisioningState` cambia a "succeeded" (correcto) o "failed" (error). Las aplicaciones cliente pueden usar la operación **Obtención de servicio de búsqueda** para sondear el estado de aprovisionamiento y ver cuando se completa una operación (el intervalo recomendado de sondeo es de 30 segundos y hasta un minuto). Si utiliza el servicio gratuito, este valor suele aparecer como "succeeded" directamente en la llamada para crear el servicio. Esto ocurre porque el servicio gratuito usa una capacidad que ya está configurada.

<a name="GetService"></a>
### Obtención de servicio de búsqueda

La operación **Obtención de servicio de búsqueda** devuelve las propiedades del servicio de búsqueda especificado. Tenga en cuenta que no devuelve las claves de administración. Utilice la operación **Obtención de claves de administración** para recuperar las claves de administración.

    GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

#### URI de solicitud

`subscriptionId`: obligatorio. Identificador de suscripción para el usuario de Azure. Puede obtener este valor en la API del Administrador de recursos o el portal de Azure.

`resourceGroupName`: obligatorio. El nombre del grupo de recursos dentro de la suscripción del usuario. Puede obtener este valor en la API del Administrador de recursos o el portal de Azure.

`serviceName`: obligatorio. El nombre del servicio de búsqueda dentro del grupo de recursos especificado. Si no conoce el nombre del servicio, puede obtener una lista con Enumerar servicios de búsqueda (API de Búsqueda de Azure).

`api-version`: obligatorio. Especifica la versión del protocolo utilizada para esta solicitud. La versión actual es `2015-02-28`.

#### Encabezados de solicitud

`x-ms-client-request-id`: opcional. Un valor GUID generado por el cliente que identifica esta solicitud. Si se especifica, se incluirá en la información de respuesta como una manera de asignar la solicitud.


#### Cuerpo de la solicitud

Ninguno.


#### Código de estado de respuesta

HTTP 200 (OK) si es correcto.


#### Encabezados de respuesta

`Content-Type`: este encabezado siempre está definido como application/json.

`x-ms-request-id`: identificador único para la operación actual, generado por el servicio.

#### Cuerpo de respuesta

    {
      "id": "/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]",
    
      "name": "service name",
      "location": "location of search service",
    "type": " Microsoft.Search/searchServices",
      "tags": {
        "key": "value",
        ... 
      }, 
    "properties": {
    	"sku": {
          "name": "free | standard | standard2"
    	},
        "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
        "partitionCount": 1 | 2 | 3 | 4 | 6 | 12,
        "status": "running | provisioning | deleting | degraded | disabled | error",
        "statusDetails": "details of the status",
        "provisioningState": "succeeded | provisioning | failed"
      }
    }

#### Elementos del cuerpo de respuesta

`id`: el identificador es la dirección URL (sin incluir la combinación de nombre de host) de este servicio de búsqueda.

`name`: el nombre del servicio de búsqueda.

`location`: la ubicación del recurso. Será una de las regiones geográficas de Azure admitidas y registradas; por ejemplo, West US (oeste de EE. UU.), East US (este de EE. UU.), Southeast Asia (sudeste de Asia), etc.

`tags`: las etiquetas son una lista de pares de clave y valor que describen el recurso. Estas etiquetas pueden utilizarse para visualizar y agrupar un recurso a través de grupos de recursos.

`sku`: indica el nivel de precios. Los valores válidos son:

- `free`: clúster compartido.
- `standard`: clúster dedicado.
- `standard2`: utilice solo por indicación del servicio de soporte técnico de Microsoft.

`replicaCount`: indica cuántas réplicas tiene el servicio. Los valores válidos son de 1 a 6.

`partitionCount`: indica cuántas particiones tiene el servicio. Los valores válidos son 1, 2, 3, 4, 6 o 12.

`status`: estado del servicio de búsqueda en el momento en que se llamó a la operación. Los valores válidos son:

- `running`: se crea el servicio de búsqueda.
- `provisioning`: se está aprovisionando el servicio de búsqueda.
- `deleting`: se está eliminando el servicio de búsqueda.
- `degraded`: el servicio de búsqueda está degradado. Esto puede ocurrir cuando el clúster encuentra un error que podría impedir que el servicio funcionara correctamente.
- `disabled`: la Búsqueda está deshabilitada. En este estado, el servicio rechazará todas las solicitudes de API.
- `error`: el servicio de búsqueda está en estado de error. 
 
**Nota**: Si el servicio está en el estado `degraded`, `disabled` o `error`, significa que el equipo de Búsqueda de Azure está investigando activamente el problema subyacente. En estos estados, los servicios dedicados son todavía facturables en función del número de unidades de búsqueda aprovisionado.
 
`statusDetails`: detalles del estado.

`provisioningState`: los valores válidos son:

- `succeeded`: el aprovisionamiento se ha realizado correctamente.
- `provisioning`: se está aprovisionando el servicio.
- `failed`: error de aprovisionamiento.


<a name="ListService"></a>
### Enumeración de servicios de búsqueda

La operación **Enumeración de servicios** devuelve una lista de todos los servicios Búsqueda de la suscripción correspondientes a un grupo de recursos específico. Esta operación devuelve definiciones de servicio, sin las claves de API de administración. Utilice la operación **Obtención de claves de administración** para recuperar las claves de administración.

    GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices?api-version=2015-02-28
    
#### Parámetros de URI de solicitud

`subscriptionId`: obligatorio. El `subscriptionID` del usuario de Azure. Puede obtener este valor en la API del Administrador de recursos o el portal de Azure.

`resourceGroupName`: obligatorio. El nombre del grupo de recursos dentro de la suscripción del usuario. Puede obtener este valor en la API del Administrador de recursos o el portal de Azure.

`api-version`: obligatorio. Especifica la versión del protocolo utilizada para esta solicitud. La versión actual es `2015-02-28`.

#### Encabezados de solicitud

`x-ms-client-request-id`: opcional. Un valor GUID generado por el cliente que identifica esta solicitud. Si se especifica, se incluirá en la información de respuesta como una manera de asignar la solicitud.

####Cuerpo de la solicitud

Ninguno.

####Respuesta

El código de estado es HTTP 200 (OK) si se realiza correctamente.

### Encabezados de respuesta ###

`Content-Type`: este encabezado siempre está definido como application/json.

`x-ms-request-id`: identificador único para la operación actual, generado por el servicio.


####Cuerpo de respuesta 

El cuerpo de respuesta es una lista de servicios, que se devuelve como una matriz JSON en la que cada servicio sigue el formato de la operación **Obtención de servicio de búsqueda**.

Tenga en cuenta que el campo `nextLink` siempre tiene el valor null porque la versión actual no admite la paginación. Se devuelve con un valor vacío para mantener la compatibilidad con versiones futuras.

    {
      "value": [
     	{
          "id": "id of service 1",
          "name": "service name",
          "location": "location of search service 1",
    	"type": " Microsoft.Search/searchServices",
          "tags": {
            "key": "value",
            ...
          }, 
    	"properties": {
            "sku": {
              "name": "free | standard | standard2"
            },
            "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
            "partitionCount": 1 | 2 | 3 | 4 | 6 | 12,
            "status": "running | provisioning | deleting | degraded | disabled | error",
            "statusDetails": "details of the status",
            "provisioningState": "succeeded | provisioning | failed"
    	}
      },   
      {
          "id": "id of service 2",
          "name": "service name 2",
          "location": "location of search service 2",
   	 	"type": " Microsoft.Search/searchServices",
          "tags": {
            "key": "value",
            ...
          },
    	"properties": {
            "sku": { 
              "name": "free | standard | standard2"
            },
            "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
            "partitionCount": 1 | 2 | 3 | 4 | 6 | 12,
            "status": "running | provisioning | deleting | degraded | disabled | error",
            "statusDetails": "details of the status",
            "provisioningState": "succeeded | provisioning | failed"
    	}
      }
    ],
    "nextLink": null
    }


<a name="DeleteService"></a>
### Eliminar servicio

La operación **Eliminar servicio** elimina el servicio de búsqueda y los datos de la búsqueda, incluidos todos los índices y documentos.
    
    DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

**Nota:** Los administradores y desarrolladores están acostumbrados a realizar copias de seguridad de los datos de aplicación antes de eliminarlos de un servidor de producción. En Búsqueda de Azure no hay ninguna operación de copia de seguridad. Si está utilizando el índice como almacenamiento principal de la aplicación, debe utilizar una operación de Búsqueda para devolver todos los datos del índice, que se puede almacenar externamente.

###Parámetros de URI de solicitud###

`subscriptionId`: obligatorio. Identificador de suscripción para el usuario de Azure. Puede obtener este valor en la API del Administrador de recursos o el portal de Azure.

`resourceGroupName`: obligatorio. El nombre del grupo de recursos dentro de la suscripción del usuario. Puede obtener este valor en la API del Administrador de recursos o el portal de Azure.

`serviceName`: obligatorio. El nombre del servicio de búsqueda dentro del grupo de recursos especificado. Si no conoce el nombre del servicio, puede obtener una lista con Enumerar servicios de búsqueda (API de Búsqueda de Azure).

`api-version`: obligatorio. Especifica la versión del protocolo utilizada para esta solicitud. La versión actual es `2015-02-28`.

###Encabezados de solicitud###

`x-ms-client-request-id`: opcional. Un valor GUID generado por el cliente que identifica esta solicitud. Si se especifica, se incluirá en la información de respuesta como una manera de asignar la solicitud.

###Cuerpo de la solicitud###

Ninguno.

###Respuesta###

Con HTTP 200, el cuerpo de respuesta estará vacío. HTTP 200 (OK) es la respuesta correcta si el recurso no existe.

Puede usar **Obtención de API del servicio de búsqueda** para sondear el estado del servicio de eliminación. Se recomienda utilizar intervalos de sondeo de 30 segundos a un minuto.

###Encabezados de respuesta###

`x-ms-request-id`: identificador único para la operación actual, generado por el servicio.

###Cuerpo de respuesta###

Ninguno.

<a name="UpdateService"></a>
### Actualización de servicio ##

La operación **Actualización de servicio** modifica la configuración del servicio de búsqueda. Son cambios válidos los cambios de etiquetas, de cantidad de particiones y de cantidad de réplicas, que agregarán o eliminarán unidades de búsqueda al servicio y afectarán a la facturación. Si intenta reducir las particiones por debajo de la cantidad necesaria para almacenar el corpus de búsqueda existente, se producirá un error que bloqueará la operación. Los cambios realizados en la topología del servicio pueden tardar un rato. Se necesita tiempo para reubicar los datos, así como para configurar o destruir clústeres en el centro de datos.

Tenga en cuenta que no puede cambiar el nombre, la ubicación ni el sku. Para cambiar cualquiera de estas propiedades se requiere la creación de un nuevo servicio.

    PATCH https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

Como alternativa, puede usar PUT.

    PUT https://management.azure.com/subscriptions/[subscriptionId]/resourcegroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

###Parámetros de URI de solicitud###

`subscriptionId`: obligatorio. El `subscriptionID` del usuario de Azure. Puede obtener este valor en la API del Administrador de recursos o el portal de Azure.

`resourceGroupName`: obligatorio. El nombre del grupo de recursos dentro de la suscripción del usuario. Puede obtener este valor en la API del Administrador de recursos o el portal de Azure.

`serviceName`: obligatorio. El nombre del servicio de búsqueda dentro del grupo de recursos especificado. Si no conoce el nombre del servicio, puede obtener una lista con Enumerar servicios de búsqueda (API de Búsqueda de Azure).

`api-version`: obligatorio. Especifica la versión del protocolo utilizada para esta solicitud. La versión actual es `2015-02-28`.

###Encabezados de solicitud###

`Content-Type`: obligatorio. Defina este encabezado como application/json.

`x-ms-client-request-id`: opcional. Un valor GUID generado por el cliente que identifica esta solicitud. Si se especifica, se incluirá en la información de respuesta como una manera de asignar la solicitud.

###Cuerpo de la solicitud###

    {
      "tags": {
        "key": "value",
        ...
      }, 
    	"properties": {
        "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
        "partitionCount": 1 | 2 | 3 | 4 | 6 | 12
     	}
    }

###Parámetros del cuerpo de la solicitud###

`tags`: opcional. Una lista de pares de clave y valor que describen el recurso. Estas etiquetas pueden utilizarse para visualizar y agrupar este recurso a través de grupos de recursos. Puede proporcionar un máximo de 10 etiquetas para cada recurso. Cada etiqueta debe tener una clave no superior a 128 caracteres y un valor no superior a 256 caracteres.

`replicaCount`: opcional. El valor predeterminado es 1. Los valores válidos son de 1 a 6. Válido solamente cuando `sku` es `standard`.

`partitionCount`: opcional. El valor predeterminado es 1. Los valores válidos son 1, 2, 3, 4, 6 o 12. Válido solamente cuando `sku` es `standard`.

###Respuesta###

Si la operación se realiza correctamente, se devuelve HTTP 200 (OK). Puede usar **Obtención de API del servicio de búsqueda** para sondear el estado del servicio de actualización. Se recomienda utilizar intervalos de sondeo de 30 segundos a un minuto.


### Encabezados de respuesta ###

`Content-Type`: este encabezado siempre está definido como application/json.

`x-ms-request-id`: identificador único para la operación actual, generado por el servicio.

### Cuerpo de respuesta ###

El cuerpo de la respuesta contiene la definición del servicio actualizado. Para ver un ejemplo, consulte **Obtención de API del servicio de búsqueda**.


<a name="KeyOps"></a>
## Operaciones de claves

La autenticación en un servicio Búsqueda de Azure requiere dos fragmentos de información: una dirección URL del servicio de búsqueda y una clave de API. Las claves de API se generan cuando se crea el servicio y se pueden volver a generar a petición después de aprovisionar el servicio. Existen dos tipos de clave de API:

- Clave de administración: concede acceso a todas las operaciones (máximo de 2 por servicio).
- Clave de consulta: autentica solamente las solicitudes de consulta (máximo de 50 por servicio).

La capacidad para administrar mediante programación las claves de administración y consulta de su servicio Búsqueda de Azure proporciona los medios para crear herramientas personalizadas, sustituir periódicamente las claves como rutina recomendada de seguridad, sustituir las claves cuando un empleado deja la empresa, o generar y adquirir claves durante el aprovisionamiento de servicio cuando se implementa la solución mediante programación o con scripts.

Las claves de la consulta se pueden adquirir, crear y eliminar. Las operaciones de clave de administración están restringidas a adquirir y volver a generar los valores de clave existentes. Eliminar una clave de administración podría impedir el acceso al servicio permanentemente, por lo que la operación no está disponible.

Las claves son cadenas que se componen de una combinación aleatoria de números y letras mayúsculas. Una clave de API solo puede utilizarse con el servicio para el que se creó y se puede cambiar periódicamente (si adopta una estrategia de sustitución de claves como práctica recomendada de seguridad).

Asegúrese de tratar las claves de API, especialmente las claves de administración, como datos confidenciales. Cualquier persona que adquiera su clave de administración tendrá la capacidad de eliminar o leer datos de los índices.

**Operaciones con claves**

Las operaciones relacionadas con las claves incluyen las siguientes API:

- <a name="ListAdminKey">Enumeración de claves de administración</a>
- <a name="RegenAdminKey">Regeneración de clave de administración</a>
- <a name="CreateQueryKey">Creación de clave de consulta</a>
- <a name="ListQueryKey">Enumeración de claves de consulta</a>
- <a name="DeleteQueryKey">Eliminación de claves de consulta</a>


<a name="ListAdminKey"></a>
## Enumeración de claves de administración ##

La operación **Enumeración de claves de administración** devuelve las claves de administración principal y secundaria para el servicio de búsqueda especificado. Dado que esta acción devuelve claves de lectura y escritura, se utiliza el método POST.

    POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listAdminKeys?api-version=2015-02-28

Las claves de administración se crean con el servicio. Siempre son dos claves, principal y secundaria. Puede volver a generar estas claves, pero no puede eliminarlas.

###Parámetros de URI de solicitud###

`subscriptionId`: obligatorio. Identificador de suscripción para el usuario de Azure. Puede obtener este valor en la API del Administrador de recursos o el portal de Azure.

`resourceGroupName`: obligatorio. El nombre del grupo de recursos dentro de la suscripción del usuario. Puede obtener este valor en la API del Administrador de recursos o el portal de Azure.

`serviceName`: obligatorio. El nombre del servicio de búsqueda dentro del grupo de recursos especificado. Si no conoce el nombre del servicio, puede obtener una lista con Enumerar servicios de búsqueda (API de Búsqueda de Azure).

`api-version`: obligatorio. Especifica la versión del protocolo utilizada para esta solicitud. La versión actual es `2015-02-28`.

`listAdminKeys`: obligatorio. Esta acción recupera las claves principal y secundaria de administración para el servicio de búsqueda.

###Encabezados de solicitud###

`x-ms-client-request-id`: opcional. Un valor GUID generado por el cliente que identifica esta solicitud. Si se especifica, se incluirá en la información de respuesta como una manera de asignar la solicitud.

###Cuerpo de la solicitud###

Ninguno.

###Respuesta###

Si la operación se realiza correctamente, se devuelve HTTP 200 (OK).

### Encabezados de respuesta ###

`Content-Type`: este encabezado siempre está definido como application/json.

`x-ms-request-id`: identificador único para la operación actual, generado por el servicio.

###Cuerpo de respuesta###

    {
      "primaryKey": "api key",
      "secondaryKey": "api key"
    }
    

<a name="RegenAdminKey"></a>
## Regeneración de claves de administración ##

La operación **Regeneración de claves de administración** elimina y vuelve a generar la clave principal o secundaria. Solo puede volver a generar una clave en cada ocasión. Al volver a generar las claves, piense cómo mantendrá el acceso al servicio. Existe una clave secundaria para que tenga una clave disponible durante la sustitución de la clave principal. Todos los servicios tienen siempre las dos claves. Puede volver a generar las claves, pero no puede eliminarlas ni ejecutar un servicio sin ellas.
 
    POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/regenerateAdminKey/[keyKind]?api-version=2015-02-28

###Parámetros de URI de solicitud###

`subscriptionId`: obligatorio. Identificador de suscripción para el usuario de Azure. Puede obtener este valor en la API del Administrador de recursos o el portal de Azure.

`resourceGroupName`: obligatorio. El nombre del grupo de recursos dentro de la suscripción del usuario. Puede obtener este valor en la API del Administrador de recursos o el portal de Azure.

`serviceName`: obligatorio. El nombre del servicio de búsqueda dentro del grupo de recursos especificado. Si no conoce el nombre del servicio, puede obtener una lista con Enumerar servicios de búsqueda (API de Búsqueda de Azure).

`api-version`: obligatorio. Especifica la versión del protocolo utilizada para esta solicitud. La versión actual es `2015-02-28`.
	
`regenerateKey`: obligatorio. Esta acción especifica que se volverá a generar la clave de administración principal o secundaria.

`keyKind`: obligatorio. Especifica la clave que desea volver a generar. Los valores válidos son:

- `primary`
- `secondary`

###Encabezados de solicitud###

`Content-Type`: obligatorio. Defina este encabezado como application/json.

`x-ms-client-request-id`: opcional. Un valor GUID generado por el cliente que identifica esta solicitud. Si se especifica, se incluirá en la información de respuesta como una manera de asignar la solicitud.

###Cuerpo de la solicitud###

Ninguno.

###Respuesta###

Si la operación se realiza correctamente, se devuelve HTTP 200 (OK).

### Encabezados de respuesta ###

`Content-Type`: este encabezado siempre está definido como application/json.

`x-ms-request-id`: identificador único para la operación actual, generado por el servicio.

###Cuerpo de respuesta###

    {
      "primaryKey": "api key",
      "secondaryKey": "api key"
    }
    
###Elementos del cuerpo de respuesta###

`primaryKey`: clave de administración principal, si se vuelve a generar.

`secondaryKey`: clave de administración secundaria, si se vuelve a generar.



<a name="CreateQueryKey"></a>
## Creación de clave de consulta ##

La operación **Creación de clave de consulta** genera una nueva clave de consulta al servicio de búsqueda. Puede crear hasta 50 claves de consulta por servicio.

    POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/createQueryKey/[name]?api-version=2015-02-28

###Parámetros de URI de solicitud###

`subscriptionId`: obligatorio. Identificador de suscripción para el usuario de Azure. Puede obtener este valor en la API del Administrador de recursos o el portal de Azure.

`resourceGroupName`: obligatorio. El nombre del grupo de recursos dentro de la suscripción del usuario. Puede obtener este valor en la API del Administrador de recursos o el portal de Azure.

`serviceName`: obligatorio. El nombre del servicio de búsqueda dentro del grupo de recursos especificado. Si no conoce el nombre del servicio, puede obtener una lista con Enumerar servicios de búsqueda (API de Búsqueda de Azure).

`api-version`: obligatorio. Especifica la versión del protocolo utilizada para esta solicitud. La versión actual es `2015-02-28`.

`createQueryKey`: obligatorio. Esta acción crea una clave de consulta al servicio de búsqueda.

`name`: obligatorio. El nombre de la clave nueva.

###Encabezados de solicitud###

`x-ms-client-request-id`: opcional. Un valor GUID generado por el cliente que identifica esta solicitud. Si se especifica, se incluirá en la información de respuesta como una manera de asignar la solicitud.

###Cuerpo de la solicitud###

Ninguno.

###Respuesta###

Código de estado de respuesta es HTTP 200 (correcto) si la operación se realiza correctamente.

### Encabezados de respuesta ###

`Content-Type`: este encabezado siempre está definido como application/json.

`x-ms-request-id`: identificador único para la operación actual, generado por el servicio.

###Cuerpo de respuesta###

    {
      "name": "name of key",
      "key": "api key"
    }


###Elementos del cuerpo de respuesta###

`name`: nombre de la clave de consulta.

`key`: valor de la clave de consulta.

<a name="ListQueryKey"></a>
## Enumeración de claves de consulta ##


La operación **Enumeración de claves de consulta** devuelve las claves de consulta para el servicio de búsqueda especificado. Las claves de consulta se utilizan para enviar llamadas a la API de consulta (sólo lectura) a un servicio de búsqueda. Puede haber hasta 50 claves de consulta por servicio.

    GET	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listQueryKeys?api-version=2015-02-28

###Parámetros de URI de solicitud###

`subscriptionId`: obligatorio. Identificador de suscripción para el usuario de Azure. Puede obtener este valor en la API del Administrador de recursos o el portal de Azure.

`resourceGroupName`: obligatorio. El nombre del grupo de recursos dentro de la suscripción del usuario. Puede obtener este valor en la API del Administrador de recursos o el portal de Azure.

`serviceName`: obligatorio. El nombre del servicio de búsqueda dentro del grupo de recursos especificado. Si no conoce el nombre del servicio, puede obtener una lista con Enumerar servicios de búsqueda (API de Búsqueda de Azure).

`api-version`: obligatorio. Especifica la versión del protocolo utilizada para esta solicitud. La versión actual es `2015-02-28`.
	
`listQueryKeys`: obligatorio. Esta acción recupera las claves de consulta del servicio de búsqueda.

###Encabezados de solicitud###

`x-ms-client-request-id`: opcional. Un valor GUID generado por el cliente que identifica esta solicitud. Si se especifica, se incluirá en la información de respuesta como una manera de asignar la solicitud.

###Cuerpo de la solicitud###

Ninguno.

###Respuesta###

El código de estado de respuesta es HTTP 200 (OK) si la operación se realiza correctamente.

### Encabezados de respuesta ###

`Content-Type`: este encabezado siempre está definido como application/json.

`x-ms-request-id`: identificador único para la operación actual, generado por el servicio.

###Cuerpo de respuesta###

    {
      "value": [
    	{
          "name": "name of key 1",
          "key": "key 1"
      	},   
      	{   
          "name": "name of key 2",
          "key": "key 2"
      	}
      ],
    "nextLink": null
    }

###Elementos del cuerpo de respuesta###

`name`: nombre de la clave de consulta.

`key`: valor de la clave de consulta.


<a name="DeleteQueryKey"></a>
## Eliminación de claves de consulta ##

La operación **Eliminación de claves de consulta** operación elimina la clave de consulta especificada. Las claves de consulta son opcionales y se utilizan para las consultas de solo lectura.

    DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/deleteQueryKey/[key]?api-version=2015-02-28

A diferencia de las claves de administración, las claves de consulta no se regeneran. El proceso para volver a generar la clave de consulta consiste en eliminarla y volver a crearla.

###Parámetros de URI de solicitud###

`subscriptionId`: obligatorio. Identificador de suscripción para el usuario de Azure. Puede obtener este valor en la API del Administrador de recursos o el portal de Azure.

`resourceGroupName`: obligatorio. El nombre del grupo de recursos dentro de la suscripción del usuario. Puede obtener este valor en la API del Administrador de recursos o el portal de Azure.

`serviceName`: obligatorio. El nombre del servicio de búsqueda dentro del grupo de recursos especificado. Si no conoce el nombre del servicio, puede obtener una lista con Enumerar servicios de búsqueda (API de Búsqueda de Azure).

`api-version`: obligatorio. Especifica la versión del protocolo utilizada para esta solicitud. La versión actual es `2015-02-28`.

`deleteQueryKey`: obligatorio. Esta acción elimina una clave de consulta al servicio de búsqueda.

`key`: obligatorio. La clave que se va a eliminar.

###Encabezados de solicitud###

`x-ms-client-request-id`: opcional. Un valor GUID generado por el cliente que identifica esta solicitud. Si se especifica, se incluirá en la información de respuesta como una manera de asignar la solicitud.

###Cuerpo de la solicitud###

Ninguno.

###Respuesta###

El código de estado de respuesta es HTTP 200 (OK) si se realiza correctamente.

### Encabezados de respuesta ###

`Content-Type`: este encabezado siempre está definido como application/json.

`x-ms-request-id`: identificador único para la operación actual, generado por el servicio.

###Cuerpo de respuesta###

Ninguno.

<!---HONumber=AcomDC_0224_2016-->