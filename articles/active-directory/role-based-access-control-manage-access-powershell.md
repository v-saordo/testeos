<properties
	pageTitle="Administración del control de acceso basado en rol (RBAC) con Azure PowerShell | Microsoft Azure"
	description="Cómo administrar RBAC con Azure PowerShell, incluida la enumeración y asignación de roles y la eliminación de asignaciones de roles."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="01/22/2016"
	ms.author="kgremban"/>

# Administración del control de acceso basado en rol con Azure PowerShell
> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)

## Enumeración de roles de control de acceso basado en rol (RBAC)
### Lista de todos los roles disponibles
Para enumerar los roles RBAC disponibles para asignación y para inspeccionar las operaciones a las que conceden acceso, use:

		Get-AzureRmRoleDefinition

![RBAC PowerShell - Get-AzureRmRoleDefinition - captura de pantalla](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### Lista de las acciones de un rol
Para enumerar las acciones de un rol específico, use:

    Get-AzureRmRoleDefinition <role name>

![RBAC PowerShell - Get-AzureRmRoleDefinition para un rol específico - captura de pantalla](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## Lista de acceso
### Lista de todas las asignaciones de rol en la suscripción seleccionada
Para enumerar las asignaciones de acceso RBAC vigentes en la suscripción, el recurso o el grupo de recursos especificado, use:

    Get-AzureRmRoleAssignment

###	Lista de las asignaciones de rol vigentes en un grupo de recursos
Para enumerar las asignaciones de acceso para un grupo de recursos, use:

    Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>

![RBAC PowerShell - Get-AzureRmRoleAssignment para un grupo de recursos - captura de pantalla](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### Lista de asignaciones de rol para un usuario, incluidas las asignadas a grupos de usuarios
Para enumerar las asignaciones de acceso para el usuario especificado, así como para los grupos de los que el usuario es miembro, use:

    Get-AzureRmRoleAssignment -ExpandPrincipalGroups

![RBAC PowerShell - Get-AzureRmRoleAssignment para un usuario - captura de pantalla](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### Lista de asignaciones de roles de administrador y coadministrador de servicio clásico
Para enumerar las asignaciones de acceso para el administrador y los coadministradores de suscripción clásica, use:

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## Conceder acceso
### Búsqueda de identificadores de objetos
Para usar las siguientes secuencias de comandos, primero debe encontrar los identificadores de los objetos. Se presume que ya conoce el identificador de la suscripción con la que trabaja; si no es así, consulte [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx) en MSDN.

#### Búsqueda del identificador del objeto de un grupo de Azure AD
Para obtener el identificador del objeto de un grupo de Azure AD, use:

    Get-AzureRmADGroup -SearchString <group name in quotes>

#### Búsqueda del identificador del objeto de una entidad de servicio de Azure AD
Para obtener el identificador del objeto de una entidad de servicio de Azure AD, use:

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### Asignación de un rol a grupo en el ámbito de la suscripción
Para conceder acceso a un grupo en el ámbito de la suscripción, use:

    New-AzureRmRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![RBAC PowerShell - New-AzureRmRoleAssignment - captura de pantalla](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment1.png)

### Asignación de un rol a aplicación en el ámbito de la suscripción
Para conceder acceso a una aplicación en el ámbito de la suscripción, use:

    New-AzureRmRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![RBAC PowerShell - New-AzureRmRoleAssignment - captura de pantalla](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### Asignación de un rol a usuario en el ámbito del grupo de recursos
Para conceder acceso a un usuario en el ámbito del grupo de recursos, use:

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - captura de pantalla](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### Asignación de un rol a grupo en el ámbito del recurso
Para conceder acceso a un grupo en el ámbito del recurso, use:

    New-AzureRmRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - captura de pantalla](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## Quitar acceso
Para quitar el acceso de usuarios, grupos y aplicaciones, use:

    Remove-AzureRmRoleAssignment -ObjId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription/subscription id>

![RBAC PowerShell - Remove-AzureRmRoleAssignment - captura de pantalla](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## Creación de un rol personalizado
Para crear un rol personalizado, use el comando `New-AzureRmRoleDefinition`.

En el ejemplo siguiente se crea un rol personalizado denominado *Operador de máquina virtual* que concede acceso a todas las operaciones de lectura de los proveedores de recursos *Microsoft.Compute*, *Microsoft.Storage* y *Microsoft.Network* y concede acceso para iniciar, reiniciar y supervisar las máquinas virtuales. El rol personalizado se puede usar en dos suscripciones.

![RBAC PowerShell - Get-AzureRmRoleDefinition - captura de pantalla](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

## Modificación de un rol personalizado
Para modificar un rol personalizado, use en primer lugar el comando `Get-AzureRmRoleDefinition` para recuperar la definición de rol. Seguidamente, realice los cambios que quiera en la definición del rol. Por último, use el comando `Set-AzureRmRoleDefinition` para guardar la definición de rol modificada.

En el ejemplo siguiente se agrega la operación `Microsoft.Insights/diagnosticSettings/*` al rol personalizado *Operador de máquina virtual*.

![RBAC PowerShell - Set-AzureRmRoleDefinition - captura de pantalla](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

En el ejemplo siguiente se agrega una suscripción de Azure a los ámbitos asignables del rol personalizado Operador de máquina virtual.

![RBAC PowerShell - Set-AzureRmRoleDefinition - captura de pantalla](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

## Eliminación de un rol personalizado

Para eliminar un rol personalizado, use el comando `Remove-AzureRmRoleDefinition`.

En el ejemplo siguiente se quita el rol personalizado *Operador de máquina virtual*.

![RBAC PowerShell - Remove-AzureRmRoleDefinition - captura de pantalla](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## Lista de roles personalizados
Para enumerar los roles que están disponibles para la asignación en un ámbito, use el comando `Get-AzureRmRoleDefinition`.

En el ejemplo siguiente se enumeran todos los roles disponibles para la asignación en la suscripción seleccionada.

![RBAC PowerShell - Get-AzureRmRoleDefinition - captura de pantalla](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

En el ejemplo siguiente, el rol personalizado *Operador de máquina virtual* no está disponible en la suscripción *Production4* debido a que la suscripción no se encuentra entre los **AssignableScopes** del rol.

![RBAC PowerShell - Get-AzureRmRoleDefinition - captura de pantalla](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## Temas de RBAC
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0128_2016-->