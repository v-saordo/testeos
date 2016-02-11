<properties
	pageTitle="Administrar el control de acceso basado en roles con Windows PowerShell"
	description="Administrar el control de acceso basado en roles con Windows PowerShell"
	services="active-directory"
	documentationCenter="na"
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="multiple"
	ms.tgt_pltfrm="powershell"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/25/2016"
	ms.author="kgremban"/>

# Administrar el control de acceso basado en roles con Windows PowerShell #

> [AZURE.SELECTOR]
- [Windows PowerShell](role-based-access-control-powershell.md)
- [Azure CLI](role-based-access-control-xplat-cli.md)

El control de acceso basado en roles (RBAC) del Portal de Azure y la API del Administrador de recursos de Azure le permite administrar el acceso a su suscripción en un nivel específico. Con esta característica, puede conceder acceso a usuarios, grupos o entidades de seguridad de servicio de Active Directory asignándoles roles en un ámbito determinado.

En este tutorial, aprenderá a usar Windows PowerShell para administrar RBAC. Le guía por el proceso de crear y comprobar asignaciones de roles.

**Tiempo estimado para completar el tutorial:** 15 minutos

## Requisitos previos

Para poder usar Windows PowerShell para administrar RBAC, necesita lo siguiente:

- Windows PowerShell, versión 3.0 o 4.0. Para buscar la versión de Windows PowerShell, escriba:`$PSVersionTable` y compruebe que el valor de `PSVersion` es 3.0 o 4.0. Para instalar una versión compatible, consulte [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

- La versión 0.8.8 o posterior de Azure PowerShell. Para instalar la última versión y asociarla a la suscripción de Azure, consulte [Instalación y configuración de Azure PowerShell](../install-configure-powershell.md).

Este tutorial está diseñado para los principiantes de Windows PowerShell, pero se asume que se conocen los conceptos básicos, como los módulos, los cmdlets y las sesiones. Para obtener más información acerca de Windows PowerShell, consulte [Introducción a Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Para obtener ayuda detallada con cualquier cmdlet que aparezca en este tutorial, use el cmdlet `Get-Help`.

	Get-Help <cmdlet-name> -Detailed

Por ejemplo, para obtener ayuda para el cmdlet `Add-AzureAccount`, escriba:

	Get-Help Add-AzureAccount -Detailed

Lea también los siguientes tutoriales para aprender a configurar y usar el Administrador de recursos de Azure en Windows PowerShell:

- [Instalación y configuración de Azure PowerShell](../install-configure-powershell.md)
- [Uso de Windows PowerShell con el Administrador de recursos](../powershell-azure-resource-manager.md)


## Conectarse a sus suscripciones

Como RBAC solo funciona con el Administrador de recursos de Azure, lo primero que hay que hacer es pasar al modo Administrador de recursos de Azure.

    PS C:\> Switch-AzureMode -Name AzureResourceManager

Para más información, consulte [Uso de Windows PowerShell con el Administrador de recursos](../powershell-azure-resource-manager.md).

Para conectarse a sus suscripciones de Azure, escriba:

    PS C:\> Add-AzureAccount

En el control emergente del explorador, escriba el nombre de usuario y la contraseña de su cuenta de Azure. PowerShell obtendrá todas las suscripciones que tenga con esta cuenta y se configurará para usar la primera como predeterminada. Con RBAC, solo podrá obtener las suscripciones donde tiene permisos por ser administrador adjunto o por tener una asignación de rol.

Si tiene varias suscripciones y quiere cambiar a otra, use los comandos siguientes:

    # This will show you the subscriptions under the account.
    PS C:\> Get-AzureSubscription
    # Use the subscription name to select the one you want to work on.
    PS C:\> Select-AzureSubscription -SubscriptionName <subscription name>

Para más información, consulte [Instalación y configuración de Azure PowerShell](../install-configure-powershell.md).

## Comprobar asignaciones de roles existentes

Ahora vamos a ver qué asignaciones de roles existen en la suscripción. Escriba:

    PS C:\> Get-AzureRoleAssignment

Esto devolverá todas las asignaciones de roles que haya en la suscripción. Cabe destacar dos cosas:

1. Necesita tener acceso de lectura en el nivel de la suscripción.
2. Si la suscripción tiene muchas asignaciones de roles, se puede tardar un rato en recuperarlas todas.

También puede comprobar las asignaciones de roles existentes para una definición de rol determinada, en un ámbito concreto y para un cierto usuario. Escriba:

    PS C:\> Get-AzureRoleAssignment -ResourceGroupName group1 -Mail <user email> -RoleDefinitionName Owner

Esto devolverá todas las asignaciones de roles para un usuario determinado de su inquilino de AD, que tiene una asignación de rol de "Owner" para el grupo de recursos "group1". La asignación de rol puede venir de dos sitios:

1. Una asignación de rol de "Owner" al usuario para el grupo de recursos.
2. Una asignación de rol de "Owner" al usuario para el elemento primario del grupo de recursos (en este caso, la suscripción) porque, si tiene permisos en un nivel primario, tendrá los mismos permisos en todos los niveles secundarios.

Todos los parámetros de este cmdlet son opcionales. Puede combinarlos para comprobar asignaciones de roles con distintos filtros.

## Crear una asignación de rol

Para crear una asignación de rol, tiene que pensar en lo siguiente:

A quién quiere asignar el rol: puede usar los siguientes cmdlets de Azure Active Directory para ver qué usuarios, grupos y entidades de seguridad de servicio tiene en su inquilino de AD.

    PS C:\> Get-AzureADUser
	PS C:\> Get-AzureADGroup
	PS C:\> Get-AzureADGroupMember
	PS C:\> Get-AzureADServicePrincipal

Qué rol quiere asignar: puede usar el cmdlet siguiente para ver las definiciones de rol compatibles.

    PS C:\> Get-AzureRoleDefinition

Qué ámbito quiere asignar: tiene tres niveles de ámbitos: - La suscripción actual: un grupo de recursos, para obtener una lista de grupos de recursos, escriba `PS C:\> Get-AzureResourceGroup` - Un recurso, para obtener una lista de recursos, escriba `PS C:\> Get-AzureResource`

Luego use `New-AzureRoleAssignment` para crear una asignación de rol. Por ejemplo:

	#This will create a role assignment at the current subscription level for a user as a reader.
	PS C:\> New-AzureRoleAssignment -Mail <user email> -RoleDefinitionName Reader

	#This will create a role assignment at a resource group level.
	PS C:\> New-AzureRoleAssignment -Mail <user email> -RoleDefinitionName Contributor -ResourceGroupName group1

	#This will create a role assignment for a group at a resource group level.
	PS C:\> New-AzureRoleAssignment -ObjectID <group object ID> -RoleDefinitionName Reader -ResourceGroupName group1

	#This will create a role assignment at a resource level.
	PS C:\> $resources = Get-AzureResource
    PS C:\> New-AzureRoleAssignment -Mail <user email> -RoleDefinitionName Owner -Scope $resources[0].ResourceId


## Comprobar los permisos

Después de comprobar que su cuenta tiene asignaciones de roles, puede ver los permisos que conceden estas asignaciones de roles ejecutando lo siguiente:

    PS C:\> Get-AzureResourceGroup
    PS C:\> Get-AzureResource

Esos dos cmdlets solo devolverán los grupos de recursos o los recursos donde tiene permiso de lectura. También le mostrarán los permisos que tiene.

Si intenta ejecutar otros cmdlets, como `New-AzureResourceGroup`, obtendrá un error de acceso denegado si no tiene el permiso.

## Pasos siguientes

Si quiere más información sobre cómo administrar el control de acceso basado en roles con Windows PowerShell y otros temas relacionados:

- [Control de acceso basado en rol de Azure](../role-based-access-control-configure.md)
- [Cmdlets de Administrador de recursos de Azure](http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409): obtenga información acerca del uso de los cmdlets en el módulo AzureResourceManager.
- [Uso de grupos de recursos para administrar los recursos de Azure](../azure-preview-portal-using-resource-groups.md): obtenga información acerca de la creación y administración de grupos de recursos en el Portal de administración de Azure.
- [Blog de Azure](http://blogs.msdn.com/windowsazure): obtenga información acerca de las nuevas características de Azure.
- [Blog de Windows PowerShell](http://blogs.msdn.com/powershell): obtenga información acerca de las nuevas características de Windows PowerShell.
- [Blog ¡Hola, chicos del scripting!](http://blogs.technet.com/b/heyscriptingguy/): Obtenga sugerencias y trucos del mundo real de la comunidad de Windows PowerShell.
- [Configuración del control de acceso basado en roles mediante CLI de Azure](role-based-access-control-xplat-cli-install.md)
- [Solución de problemas de control de acceso basado en roles](role-based-access-control-troubleshooting.md)

<!---HONumber=AcomDC_0128_2016-->