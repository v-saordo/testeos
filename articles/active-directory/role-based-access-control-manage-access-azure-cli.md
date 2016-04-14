<properties
	pageTitle="Administración del control de acceso basado en rol (RBAC) con la CLI de Azure | Microsoft Azure"
	description="Aprenda a administrar el acceso basado en rol (RBAC) con la interfaz de la línea de comandos de Azure a través de la enumeración de roles y acciones de roles, la asignación de roles a los ámbitos de aplicación y suscripción."
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
	ms.date="02/29/2016"
	ms.author="kgremban"/>

# Administración del control de acceso basado en rol con la interfaz de la línea de comandos de Azure

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [CLI de Azure](role-based-access-control-manage-access-azure-cli.md)
- [API DE REST](role-based-access-control-manage-access-rest.md)

## Enumeración de roles de control de acceso basado en rol (RBAC)

>[AZURE.IMPORTANT] Para poder usar los cmdlets de este artículo, debe [instalar la CLI de Azure](../xplat-cli-install.md).

###	Lista de todos los roles disponibles
Para enumerar todos los roles disponibles, use:

		azure role list

El ejemplo siguiente muestra la lista de *todos los roles disponibles*.

![Línea de comandos de Azure RBAC: lista de roles de Azure (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

###	Lista de las acciones de un rol
Para enumerar las acciones de un rol, use:

    azure role show <role in quotes>

El ejemplo siguiente muestra las acciones del rol *Colaborador* y del rol *Colaborador de la máquina virtual*.

![Línea de comandos de Azure RBAC: vista de roles de Azure (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

##	Lista de acceso
###	Lista de las asignaciones de rol vigentes en un grupo de recursos
Para enumerar las asignaciones de rol vigentes en un grupo de recursos, use:

    azure role assignment list --resource-group <resource group name>

El ejemplo siguiente muestra las asignaciones de rol vigentes en el grupo *pharma-sales-projecforcast*.

![Línea de comandos de Azure RBAC: lista de asignación de roles de Azure por grupo (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

###	Lista de asignaciones de rol para un usuario, incluidas las asignadas a los grupos de un usuario

El ejemplo siguiente muestra las asignaciones de rol vigentes en el usuario **sameert@aaddemo.com*.

![Línea de comandos de Azure RBAC: lista de asignación de roles de Azure por usuario (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

##	Conceder acceso
Cuando haya identificado el rol que desea asignar, para conceder acceso, use:

    azure role assignment create

###	Asignación de un rol a grupo en el ámbito de la suscripción
Para asignar un rol a un grupo en el ámbito de la suscripción, use:

	azure role assignment create --objId  <group's object id> --role <name of role> --scope <subscription/subscription id>

En el ejemplo siguiente se asigna el rol *Lector* al *equipo de Christine Koch* en el ámbito de la *suscripción*.

![Línea de comandos de Azure RBAC: asignación de roles de Azure creada por grupo (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

###	Asignación de un rol a aplicación en el ámbito de la suscripción
Para asignar un rol a una aplicación en el ámbito de la suscripción, use:

    azure role assignment create --objId  <applications's object id> --role <name of role> --scope <subscription/subscription id>

En el ejemplo siguiente se concede el rol *Colaborador* a una aplicación de *Azure AD* en la suscripción seleccionada.

 ![Línea de comandos de Azure RBAC: asignación de roles de Azure creada por aplicación (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

###	Asignación de un rol a usuario en el ámbito del grupo de recursos
Para asignar un rol a un usuario en el ámbito del grupo de recursos, use:

	azure role assignment create --signInName  <user's email address> --roleName <name of role in quotes> --resourceGroup <resource group name>

En el ejemplo siguiente se concede el rol *Colaborador de la máquina virtual* al usuario **samert@aaddemo.com* en el ámbito del grupo de recursos *Pharma-Sales-ProjectForcast*.

![Línea de comandos de Azure RBAC: asignación de roles de Azure creada por usuario (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

###	Asignación de un rol a grupo en el ámbito del recurso
Para asignar un rol a un grupo en el ámbito del recurso, use:

    azure role assignment create --objId  <group id> --roleName <name of role in quotes> --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

El ejemplo siguiente concede el rol *Colaborador de la máquina virtual* a un grupo de *Azure AD* en una *subred*.

![Línea de comandos de Azure RBAC: asignación de roles de Azure creada por grupo (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

##	Quitar acceso
Para quitar una asignación de rol, use:

    azure role assignment delete --objId <object id to from which to remove role> --roleName <role name>

En el ejemplo siguiente se quita la asignación de rol *Colaborador de la máquina virtual* de **sammert@aaddemo.com* en el grupo de recursos *Pharma-Sales-ProjectForcast*. Luego, quita la asignación de rol de un grupo de la suscripción.

![Línea de comandos de Azure RBAC: asignación de roles de Azure eliminada (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## Crear un rol personalizado
Para crear un rol personalizado, use el comando `azure role create`.

En el ejemplo siguiente se crea un rol personalizado denominado *Operador de máquina virtual* que concede acceso a todas las operaciones de lectura de los proveedores de recursos *Microsoft.Compute*, *Microsoft.Storage* y *Microsoft.Network* y concede acceso para iniciar, reiniciar y supervisar las máquinas virtuales. El rol personalizado se puede usar en dos suscripciones. En este ejemplo se usa un archivo json como entrada.

![JSON: definición de roles personalizados (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-1.png)

![Línea de comandos de Azure RBAC: creación de roles de Azure (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-2.png)

## Modificación de un rol personalizado

Para modificar un rol personalizado, use en primer lugar el comando `azure role show` para recuperar la definición de rol. Seguidamente, realice los cambios que quiera en la definición del rol. Por último, use `azure role set` para guardar la definición de rol modificada.

En el ejemplo siguiente, se agrega la operación Microsoft.Insights/diagnosticSettings/* a **Acciones** y una suscripción de Azure al valor de **AssignableScopes** del rol personalizado de operador de máquina virtual.

![JSON: modificación de definición de roles personalizados (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set-1.png)

![Línea de comandos de Azure RBAC: establecimiento de roles de Azure (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set2.png)

## Eliminación de un rol personalizado

Para eliminar un rol personalizado, use primero el comando `azure role show` para determinar el **identificador** del rol. Luego, use el comando `azure role delete` para eliminar el rol especificando el **identificador**.

En el ejemplo siguiente se quita el rol personalizado *Operador de máquina virtual*.

![Línea de comandos de Azure RBAC: eliminación de roles de Azure (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-delete.png)

## Lista de roles personalizados

Para enumerar los roles que están disponibles para la asignación en un ámbito, use el comando `azure role list`.

En el ejemplo siguiente se enumeran todos los roles disponibles para la asignación en la suscripción seleccionada.

![Línea de comandos de Azure RBAC: lista de roles de Azure (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list1.png)

En el ejemplo siguiente, el rol personalizado *Operador de máquina virtual* no está disponible en la suscripción *Production4* debido a que la suscripción no se encuentra entre los **AssignableScopes** del rol.

![Línea de comandos de Azure RBAC: lista de roles de Azure para roles personalizados (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list2.png)





## Temas de RBAC
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0302_2016-->