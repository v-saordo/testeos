<properties 
	pageTitle="Operaciones de auditoría con el Administrador de recursos | Microsoft Azure" 
	description="Use el registro de auditoría en el Administrador de recursos para revisar los errores y las acciones del usuario. Muestra PowerShell, CLI de Azure y REST." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="tomfitz"/>

# Operaciones de auditoría con el Administrador de recursos

Cuando se produce un problema durante la implementación o mientras la solución esté vigente, necesita descubrir qué salió mal. El Administrador de recursos ofrece dos maneras de averiguar qué ocurrió y por qué. Puede usar comandos de implementación para recuperar información sobre operaciones e implementaciones concretas. O bien, puede usar los registros de auditoría para recuperar información sobre las implementaciones y otras acciones llevadas a cabo durante la vigencia de la solución. Este tema se centra en los registros de auditoría.

El registro de auditoría contiene todas las acciones realizadas en los recursos. Por tanto, si un usuario de su organización modifica un recurso, podrá identificar la acción, el tiempo y el usuario.

Existen dos limitaciones importantes a tener en cuenta al trabajar con registros de auditoría:

1. Los registros de auditoría solo se conservan durante 90 días.
2. Solo se pueden consultar para un intervalo de 15 días o menos.

Puede recuperar información de los registros de auditoría a través de Azure PowerShell, CLI de Azure, API de REST o el Portal de Azure.

## PowerShell

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

Para recuperar las entradas del registro, ejecute el comando **Get-AzureRmLog** (o **Get-AzureResourceGroupLog** para versiones de PowerShell anteriores a la 1.0). Ofrezca parámetros adicionales para filtrar la lista de entradas.

En el ejemplo siguiente se muestra cómo usar el registro de auditoría para investigar acciones llevadas a cabo durante el ciclo de vida de la solución. Puede ver cuándo se produjo la acción y quién la solicitó. Las fechas inicial y final se especifican en un formato de fecha.

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00

También puede usar funciones de fecha para especificar el intervalo de fechas, como los últimos 15 días.

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-15)

En función de la hora de inicio que especifique, los comandos anteriores pueden devolver una lista larga de acciones para ese grupo de recursos. Puede filtrar los resultados para lo que busca ofreciendo criterios de búsqueda. Por ejemplo, si intenta investigar cómo se detuvo una aplicación web, podría ejecutar el siguiente comando y ver que someone@example.com realizó una acción de detención.

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-15) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action

    Authorization     :
                        Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
                        Action    : Microsoft.Web/sites/stop/action
                        Role      : Subscription Admin
                        Condition :
    Caller            : someone@example.com
    CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
    EventSource       : Administrative
    EventTimestamp    : 8/28/2015 4:08:18 PM
    OperationName     : Microsoft.Web/sites/stop/action
    ResourceGroupName : ExampleGroup
    ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
    Status            : Succeeded
    SubscriptionId    : xxxxx
    SubStatus         : OK

En el siguiente ejemplo, solo buscaremos acciones erróneas después de la hora de inicio especificada. También incluiremos el parámetro **DetailedOutput** para ver los mensajes de error.

    PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-15) -Status Failed –DetailedOutput
    
Si este comando devuelve demasiadas entradas y propiedades, puede centrarse en sus esfuerzos de auditoría mediante la recuperación de la propiedad **properties**.

    PS C:\> (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties

    Content
    -------
    {}
    {[statusCode, Conflict], [statusMessage, {"Code":"Conflict","Message":"Website with given name mysite already exists...
    {[statusCode, Conflict], [serviceRequestId, ], [statusMessage, {"Code":"Conflict","Message":"Website with given name...

Además, puede restringir los resultados examinando el mensaje de estado.

    PS C:\> (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json

    Code       : Conflict
    Message    : Website with given name mysite already exists.
    Target     :
    Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
    Innererror :


## Azure CLI

Para recuperar las entradas del registro, ejecute el comando **azure group log show**.

    azure group log show ExampleGroup

Puede filtrar los resultados con una utilidad de JSON como [jq](http://stedolan.github.io/jq/download/). En el ejemplo siguiente se muestra cómo buscar operaciones que implicaban la actualización de un archivo de configuración web.

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.localizedValue == "Update web sites config")"

Puede agregar el parámetro **–-last-deployment** para limitar las entradas devueltas solo a las operaciones desde la última implementación.

    azure group log show ExampleGroup --last-deployment

Si la lista de operaciones desde la última implementación es demasiado larga, puede filtrar los resultados solo para las operaciones con error.

    azure group log show tfCopyGroup --last-deployment --json | jq ".[] | select(.status.value == "Failed")"

                                   /Microsoft.Web/Sites/ExampleSite
    data:    SubscriptionId:       <guid>
    data:    EventTimestamp (UTC): Thu Aug 27 2015 13:03:27 GMT-0700 (Pacific Daylight Time)
    data:    OperationName:        Update website
    data:    OperationId:          cb772193-b52c-4134-9013-673afe6a5604
    data:    Status:               Failed
    data:    SubStatus:            Conflict (HTTP Status Code: 409)
    data:    Caller:               someone@example.com
    data:    CorrelationId:        a8c7a2b4-5678-4b1b-a50a-c17230427b1e
    data:    Description:
    data:    HttpRequest:          clientRequestId: <guid>
                                   clientIpAddress: 000.000.000.000
                                   method:          PUT

    data:    Level:                Error
    data:    ResourceGroup:        ExampleGroup
    data:    ResourceProvider:     Azure Web Sites
    data:    EventSource:          Administrative
    data:    Properties:           statusCode:       Conflict
                                   serviceRequestId:
                                   statusMessage:    {"Code":"Conflict","Message":"Website with given name
                                   ExampleSite already exists.","Target":null,"
                                   Details
                                   ":[{"Message":"Website with given name ExampleSite already exists."},
                                   {"Code":"Conflict"},
                                   {"ErrorEntity":{"Code":"Conflict","Message":"Website with given
                                   name ExampleSite already exists.","ExtendedCode
                                   ":"
                                   54001","MessageTemplate":"Website with given name {0} already exists.",
                                   "Parameters":["ExampleSite"],"
                                   InnerErrors":null}}],"Innererror":null}



## API de REST

Las operaciones REST para trabajar con el registro de auditoría forman parte de la [API de REST de Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx). Para recuperar los eventos de registro de auditoría, vea [Lista de los eventos de administración de una suscripción](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## Portal

También puede ver las operaciones registradas a través del portal. Solo tiene que seleccionar la hoja de registros de auditoría.

![seleccionar registros de auditoría](./media/resource-group-audit/select-audit.png)

Y ver la lista de las operaciones más recientes.

![mostrar acciones](./media/resource-group-audit/show-actions.png)

Puede seleccionar cualquier operación para obtener más detalles sobre ella.

## Pasos siguientes

- Para información sobre cómo establecer directivas de seguridad, vea [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md).
- Para obtener información sobre la concesión del acceso a una entidad de seguridad de servicio, vea [Autenticación de una entidad de seguridad de servicio con el Administrador de recursos de Azure](resource-group-authenticate-service-principal.md).
- Para aprender a realizar acciones en un recurso para todos los usuarios, vea [Bloqueo de recursos con el Administrador de recursos de Azure](resource-group-lock-resources.md).

<!---HONumber=AcomDC_0128_2016-->