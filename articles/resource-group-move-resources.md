<properties 
	pageTitle="Traslado de recursos al nuevo grupo de recursos" 
	description="Use Azure PowerShell o la API de REST para trasladar los recursos a un nuevo grupo de recursos para el Administrador de recursos de Azure." 
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
	ms.date="01/28/2016" 
	ms.author="tomfitz"/>

# Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción

En este tema se muestra cómo trasladar recursos de un grupo de recursos a otro. También puede trasladar recursos a una nueva suscripción. Es posible que necesite trasladar recursos cuando decida que:

1. Para fines de facturación, un recurso debe residir en una suscripción diferente.
2. Un recurso ya no comparte el mismo ciclo de vida que los recursos con el que estaba agrupado anteriormente. Desea trasladarlo a un nuevo grupo de recursos para administrar ese recurso independientemente de los otros recursos.
3. Ahora, el recurso comparte el mismo ciclo de vida que los otros recursos de un grupo de recursos distinto. Desea trasladarlo al grupo de recursos con los otros recursos para administrarlos de forma conjunta.

Hay algunas consideraciones importantes cuando se mueve un recurso:

1. No puede cambiar la ubicación del recurso. Si se mueve un recurso, solo se mueve a un nuevo grupo de recursos. El nuevo grupo de recursos puede tener una ubicación diferente, pero no cambia la ubicación del recurso.
2. El grupo de recursos de destino debe contener únicamente los recursos que comparten el mismo ciclo de vida de aplicación que los recursos que se van a mover.
3. Si usa Azure PowerShell o la CLI de Azure, asegúrese de que está usando la versión más reciente. Para actualizar su versión, ejecute el Instalador de plataforma web de Microsoft y compruebe si hay disponible una nueva versión. Para más información, vea [Instalación y configuración de Azure PowerShell](powershell-install-configure.md) e [Instalación de la CLI de Azure](xplat-cli-install.md).
4. La operación de traslado puede tardar en completarse y durante ese tiempo el símbolo del sistema esperará hasta que se haya completado la operación.
5. Al mover los recursos, el grupo de origen y el grupo de destino se bloquean durante la operación. Las operaciones de escritura y eliminación están bloqueadas en los grupos hasta que se completa el movimiento.

## Servicios admitidos

No todos los servicios admiten actualmente la capacidad de traslado de recursos.

Por ahora, los servicios que admiten el traslado a un nuevo grupo de recursos y a una nueva suscripción son:

- Administración de API
- Automatización
- Lote
- Factoría de datos
- DocumentDB
- Clústeres de HDInsight
- Almacén de claves
- Aplicaciones lógicas
- Mobile Engagement
- Centros de notificaciones
- Visión operativa
- Caché en Redis
- Search
- Servidor de Base de datos SQL (al mover un servidor se mueven también todas sus bases de datos. Las bases de datos no se pueden mover por separado del servidor).
- Aplicaciones web (se aplican algunas [limitaciones](app-service-web/app-service-move-resources.md))

Los servicios que admiten el traslado a un nuevo grupo de recursos, pero no una nueva suscripción son:

- Máquinas virtuales (clásicas)
- Almacenamiento (clásico)
- Redes virtuales
- Servicios en la nube

Los servicios que actualmente no permiten trasladar un recurso son:

- Máquinas virtuales
- Almacenamiento
- ExpressRoute

Al trabajar con aplicaciones web, no se puede mover solo un plan del Servicio de aplicaciones. Para mover las aplicaciones web, las opciones son:

- Mover todos los recursos de un grupo de recursos a otro grupo de recursos, si el grupo de recursos de destino no tiene ya recursos Microsoft.Web.
- Mover las aplicaciones web a un grupo de recursos distinto, pero mantener el plan del Servicio de aplicaciones del grupo de recursos original.

No puede mover una base de datos SQL por separado del servidor. La base de datos y el servidor deben residir en el mismo grupo de recursos. Cuando se mueve un servidor SQL Server, se mueven también todas sus bases de datos.

## Uso de PowerShell para trasladar recursos

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

Para mover recursos existentes a otro grupo de recursos o a otra suscripción, use el comando **Move-AzureRmResource**.

El primer ejemplo muestra cómo trasladar un recurso a un nuevo grupo de recursos.

    PS C:\> $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    PS C:\> Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

El segundo ejemplo muestra cómo trasladar varios recursos a un nuevo grupo de recursos.

    PS C:\> $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    PS C:\> $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    PS C:\> Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId

Para moverlos a una nueva suscripción, especifique un valor para el parámetro **DestinationSubscriptionId**.

## Uso de CLI de Azure para mover recursos

Para mover recursos existentes a otro grupo de recursos o a otra suscripción, use el comando **azure resource move**. En el siguiente ejemplo se muestra cómo trasladar una Caché de Redis a un nuevo grupo de recursos. En el parámetro **-i**, ofrezca una lista separada por comas del id. de recurso que se va a mover.

    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"
    info:    Executing command resource move
    Move selected resources in OldRG to NewRG? [y/n] y
    + Moving selected resources to NewRG
    info:    resource move command OK

## Uso de la API de REST para trasladar recursos

Para trasladar recursos existentes a otro grupo de recursos o a una suscripción, ejecute:

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

En el cuerpo de la solicitud, especifique el grupo de recursos de destino y los recursos a mover. Para obtener más información acerca de la operación REST de movimiento, consulte [Mover recursos](https://msdn.microsoft.com/library/azure/mt218710.aspx).

## Pasos siguientes
- [Uso de Azure PowerShell con el Administrador de recursos](./powershell-azure-resource-manager.md)
- [Uso de la CLI de Azure para Mac, Linux y Windows con administración de recursos de Azure](./xplat-cli-azure-resource-manager.md)
- [Uso del Portal de Azure para administrar los recursos de Azure](azure-portal/resource-group-portal.md)
- [Uso de etiquetas para organizar los recursos de Azure](./resource-group-using-tags.md)

<!---HONumber=AcomDC_0224_2016-->