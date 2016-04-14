<properties 
	pageTitle="Bloqueo de recursos con el Administrador de recursos | Microsoft Azure" 
	description="Impida que los usuarios actualicen o eliminen determinados recursos al aplicar una restricción a todos los usuarios y roles." 
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
	ms.date="01/21/2016" 
	ms.author="tomfitz"/>

# Bloqueo de recursos con el Administrador de recursos de Azure

Como administrador, existen escenarios en los que deseará bloquear una suscripción, un grupo de recursos o un recurso para impedir que otros usuarios de su organización eliminen accidentalmente un recurso esencial.

El Administrador de recursos de Azure proporciona la capacidad para restringir las operaciones en los recursos a través de bloqueos de administración de recursos. Los bloqueos son directivas que aplican un nivel de bloqueo en un ámbito determinado. El ámbito puede ser una suscripción, un grupo de recursos o un recurso. El nivel de bloqueo identifica el tipo de cumplimiento para la directiva, que actualmente puede establecerse en **CanNotDelete**. **CanNotDelete** significa que los usuarios autorizados todavía pueden leer y modificar recursos, pero no pueden eliminar ninguno de los recursos restringidos.

Los bloqueos son diferentes del uso del control de acceso basado en rol para asignar permisos de usuario para realizar determinadas acciones. Para información sobre cómo establecer permisos para usuarios y roles, vea [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md). Al contrario que con el control de acceso basado en rol, se usan los bloqueos de administración para aplicar una restricción a todos los usuarios y roles, y normalmente se aplican los bloqueos solo durante un tiempo limitado.

Cuando se aplica un bloqueo en un ámbito primario, todos los recursos secundarios heredan el mismo bloqueo.

## Escenarios comunes

Un escenario común es cuando tiene un grupo de recursos con algunos recursos que se usa en un patrón de activación y desactivación. Los recursos de máquinas virtuales se activan periódicamente para procesar los datos para un intervalo de tiempo determinado y, luego, se desactivan. En este escenario, querrá habilitar el apagado de las máquinas virtuales, pero es fundamental que no se elimine una cuenta de almacenamiento. En este escenario, se utilizaría un bloqueo de recurso con un nivel de bloqueo **CanNotDelete** en la cuenta de almacenamiento.

## Quién puede crear o eliminar bloqueos en su organización

Para crear o eliminar bloqueos de administración, debe tener acceso a las acciones **Microsoft.Authorization/*** o **Microsoft.Authorization/locks/***. Entre los roles integrados, solamente se conceden esas acciones a **Propietario** y **Administrador de acceso de usuarios**. Para más información sobre la asignación del control de acceso, vea [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md).

## Creación de un bloqueo en una plantilla

El ejemplo siguiente muestra una plantilla que crea un bloqueo en una cuenta de almacenamiento. La cuenta de almacenamiento en la que se va a aplicar el bloqueo se proporciona como un parámetro que se utiliza junto con la función concat(). El resultado es el nombre del recurso con "Microsoft.Authorization" anexado, seguido del nombre del bloqueo, en este caso **myLock**.

El tipo proporcionado es específico del tipo de recurso. Para el almacenamiento, este tipo es "Microsoft.Storage/storageaccounts/providers/locks".

El nivel de ámbito se establece mediante la propiedad **level** del recurso. Como el ejemplo se concentra en evitar la eliminación accidental, el nivel está establecido en **CannotDelete**.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "lockedResource": {
                "type": "string"
            }
        },
        "resources": [
            {
                "name": "[concat(parameters('lockedResource'), '/Microsoft.Authorization/myLock')]",
                "type": "Microsoft.Storage/storageAccounts/providers/locks",
                "apiVersion": "2015-01-01",
                "properties": {
	                "level": "CannotDelete"
                }
            }
        ]
    }

## Creación de un bloqueo con la API de REST

Puede bloquear los recursos implementados con la [API de REST para bloqueos de administración](https://msdn.microsoft.com/library/azure/mt204563.aspx). La API de REST le permite crear y eliminar bloqueos, y recuperar información acerca de los bloqueos existentes.

Para crear un bloqueo, ejecute:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

El ámbito puede ser una suscripción, un grupo de recursos o un recurso. El nombre del bloqueo es el nombre con el que desee llamar al bloqueo. Como versión de la API, use **2015-01-01**.

En la solicitud, incluya un objeto JSON que especifique las propiedades para el bloqueo.

    {
        "properties": {
            "level": {lock-level},
            "notes": "Optional text notes."
        }
    } 

Para el nivel de bloqueo, especifique **CanNotDelete**.

Para ejemplos, vea la [API de REST para bloqueos de administración](https://msdn.microsoft.com/library/azure/mt204563.aspx).

## Creación de un bloqueo con Azure PowerShell

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

Puede bloquear los recursos implementados con Azure PowerShell mediante **New-AzureRmResourceLock**, como se muestra a continuación. Mediante PowerShell, solo puede establecer **LockLevel** en **CanNotDelete**.

    PS C:\> New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites

Azure PowerShell ofrece otros comandos para bloqueos de trabajo, como **Set-AzureRmResourceLock** para actualizar un bloqueo y **Remove-AzureRmResourceLock** para eliminarlo.

## Pasos siguientes

- Para obtener más información sobre cómo trabajar con bloqueos de recursos, consulte [Bloqueo de los recursos de Azure](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)
- Para aprender a organizar de manera lógica los recursos, vea [Uso de etiquetas para organizar sus recursos](resource-group-using-tags.md).
- Para cambiar el grupo de recursos en que reside un recurso, vea [Traslado de los recursos a un nuevo grupo de recursos](resource-group-move-resources.md).
- Puede aplicar restricciones y convenciones a través de su suscripción con directivas personalizadas. Para más información, vea [Uso de directivas para administrar los recursos y controlar el acceso](resource-manager-policy.md).

<!---HONumber=AcomDC_0128_2016-->