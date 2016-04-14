<properties 
	pageTitle="Vinculación de recursos en el Administrador de recursos de Azure" 
	description="Cree un vínculo entre los recursos en distintos grupos de recursos en el Administrador de recursos de Azure." 
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
	ms.date="01/26/2016" 
	ms.author="tomfitz"/>

# Vinculación de recursos en el Administrador de recursos de Azure

Después de la implementación es posible que desee consultar las relaciones o vínculos entre recursos. Las dependencias informan a la implementación, pero el ciclo de vida finaliza en la implementación. Una vez completada la implementación, no hay ninguna relación identificada entre los recursos dependientes.

En su lugar, el Administrador de recursos de Azure proporciona una nueva característica denominada vinculación de recursos para establecer y consultar las relaciones entre los recursos. Puede determinar qué recursos están vinculados a un recurso o qué recursos están vinculados a partir de un recurso.

El ámbito de un vínculo de recurso puede ser una suscripción, un grupo de recursos o un recurso específico. Esto significa que los vínculos de recursos pueden documentar las relaciones que abarcan los grupos de recursos. Cuando comienza a descomponer la solución en varias plantillas y varios grupos de recursos, resulta útil contar con un mecanismo para identificar estos vínculos de recursos. Por ejemplo, es frecuente que una base de datos con su propio ciclo de vida resida en un grupo de recursos, y una aplicación con un ciclo de vida diferente en un grupo de recursos distinto. La aplicación se conecta a la base de datos, por lo que hay un vínculo entre los recursos en distintos grupos de recursos.

Todos los recursos vinculados deben pertenecer a la misma suscripción. Cada recurso puede vincularse con otros 50. Si uno de los recursos vinculados se elimina o modifica, el propietario del vínculo debe borrar el vínculo restante.

## Vinculación de plantillas

Para definir un vínculo entre los recursos de una plantilla, consulte [Vínculos de recursos: esquema de plantillas](resource-manager-template-links.md).

## Vinculación con la API de REST

Para definir un vínculo entre recursos implementados, ejecute:

    PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/providers/Microsoft.Resources/links/{link-name}?api-version={api-version}

Reemplace {subscription-id} por el identificador de suscripción. Reemplace {resource-group}, {provider-namespace, {resource-type} y {resource-name} por los valores que identifican el primer recurso en el vínculo. Reemplace {link-name} por el nombre del vínculo que crear. Use 2015-01-01 para la versión de la API.

En la solicitud, incluya un objeto que defina al segundo recurso del vínculo:

    {
        "name": "{new-link-name}",
        "properties": {
            "targetId": "subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/",
            "notes": "{link-description}",
        }
    }

El elemento de propiedades contiene el identificador para el segundo recurso.

Para obtener más ejemplos, incluido cómo recuperar información acerca de los vínculos, consulte [Recursos vinculados](https://msdn.microsoft.com/library/azure/mt238499.aspx).

## Pasos siguientes

- También puede organizar los recursos con etiquetas. Para obtener información sobre el etiquetado de recursos, consulte [Uso de etiquetas para organizar los recursos](resource-group-using-tags.md).
- Para obtener una descripción sobre cómo crear plantillas y definir los recursos que se van a implementar, consulte [Creación de plantillas](resource-group-authoring-templates.md).

<!---HONumber=AcomDC_0128_2016-->