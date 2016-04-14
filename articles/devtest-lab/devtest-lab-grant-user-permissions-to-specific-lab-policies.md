    <properties
	pageTitle="Grant user permissions to specific DevTest Lab policies | Microsoft Azure"
	description="Learn how to grant user permissions to specific DevTest Lab policies based on each user's needs"
	services="devtest-lab,virtual-machines,visual-studio-online"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="tarcher"/>

# Concesión de permisos de usuario a directivas específicas de Laboratorio de desarrollo y pruebas

## Información general

Este artículo muestra cómo usar PowerShell para conceder permisos de usuario a una directiva concreta de Laboratorio de desarrollo y pruebas. De este modo, se pueden aplicar permisos en función de las necesidades de cada usuario. Por ejemplo, quizá prefiera conceder a un usuario determinado la posibilidad de cambiar la configuración de directiva de máquina virtual, pero no las directivas de costos.

## Directivas como recursos

Tal y como se describe en el artículo [Control de acceso basado en roles de Azure](/role-based-access-control-configure.md), RBAC habilita la administración de acceso específica de recursos para Azure. Gracias a RBAC puede dividir las tareas entre el equipo de DevOps, y conceder a los usuarios únicamente el nivel de acceso que necesitan para realizar su trabajo.

En el Laboratorio de desarrollo y pruebas, una directiva es un tipo de recurso que permite la acción de RBAC **Microsoft.DevTestLab/labs/policySets/policies/**. Cada directiva de Laboratorio de desarrollo y pruebas es un recurso en el tipo de recurso Directiva, y se puede asignar como ámbito a un rol RBAC.

Por ejemplo, para conceder permisos de lectura y escritura a los usuarios para la directiva **Tamaños permitidos de máquina virtual**, debe crear un rol personalizado que funcione con la acción **Microsoft.DevTestLab/labs/policySets/policies/*** y, a continuación, asignar los usuarios adecuados a este rol personalizado en el ámbito de **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Para obtener más información acerca de los roles personalizados en RBAC, consulte la sección [Roles personalizados en RBAC de Azure](/role-based-access-control-configure.md#custom-roles-in-azure-rbac) del artículo [Control de acceso basado en roles de Azure](/role-based-access-control-configure.md).

##Creación de un rol personalizado Laboratorio de desarrollo y pruebas con PowerShell
Para empezar, debe leer el artículo siguiente, donde se explica cómo instalar y configurar los cmdlets de Azure PowerShell: [https://azure.microsoft.com/blog/azps-1-0-pre](https://azure.microsoft.com/blog/azps-1-0-pre).

Una vez haya configurado los cmdlets de Azure PowerShell, podrá realizar las siguientes tareas:

- Enumerar todas las operaciones y acciones para un proveedor de recursos
- Enumerar las acciones de un rol determinado
- Crear un rol personalizado

El siguiente script de PowerShell muestra ejemplos de cómo realizar estas tareas:

    ‘List all the operations/actions for a resource provider.
    Get-AzureRmProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzureRmRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)

##Asignación de permisos a un usuario para una directiva determinada utilizando roles personalizados
Una vez haya definido los roles personalizados, puede asignárselos a los usuarios. A fin de asignar un rol personalizado a un usuario, primero debe obtener el **ObjectId** que representa a ese usuario. Para ello, utilice el cmdlet **Get-AzureRmADUser**.

En el ejemplo siguiente, el valor **ObjectId** del usuario *SomeUser* es 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzureRmADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Una vez que tenga el **ObjectId** para el usuario y un nombre de rol personalizado, puede asignar ese rol al usuario con el cmdlet **New-AzureRmRoleAssignment**:

    PS C:\>New-AzureRmRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/policies/AllowedVmSizesInLab

En el ejemplo anterior, se utiliza la directiva **AllowedVmSizesInLab**. Puede utilizar cualquiera de las directivas siguientes:

- MaxVmsAllowedPerUser
- MaxVmsAllowedPerLab
- AllowedVmSizesInLab
- LabVmsShutdown

## Pasos siguientes

Una vez que haya concedido permisos de usuario a las directivas específicas de Laboratorio de desarrollo y pruebas, estos son algunos de los siguientes pasos que debe considerar:

- [Acceso seguro a un laboratorio de desarrollo y pruebas](devtest-lab-add-devtest-user.md).

- [Definición de directivas de laboratorio](devtest-lab-set-lab-policy.md)

- [Creación de una plantilla de laboratorio](devtest-lab-create-template.md)

- [Creación de artefactos personalizados para máquinas virtuales](devtest-lab-artifact-author.md)

- [Incorporación de una máquina virtual con artefactos a un laboratorio de desarrollo y pruebas de Azure](devtest-lab-add-vm-with-artifacts.md)

<!---HONumber=AcomDC_0211_2016-->