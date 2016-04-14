<properties 
   pageTitle="Control de acceso basado en rol en Automatización de Azure | Microsoft Azure"
   description="El control de acceso basado en rol (RBAC) permite la administración del acceso en los recursos de Azure. En este artículo se describe cómo configurar RBAC en Automatización de Azure."
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/11/2016"
   ms.author="sngun"/>

# Control de acceso basado en rol en Automatización de Azure

## Control de acceso basado en rol

El control de acceso basado en rol (RBAC) permite la administración del acceso en los recursos de Azure. Con [RBAC](../active-directory/role-based-access-control-configure.md), se pueden separar los deberes del equipo y conceder a los usuarios, grupos y aplicaciones únicamente el acceso que necesitan para realizar sus trabajos. El acceso basado en rol se puede conceder a los usuarios que utilicen el Portal de Azure, las herramientas de la línea de comandos de Azure o las API de administración de Azure.

## RBAC en cuentas de Automatización de Azure

En Automatización de Azure, el acceso se concede mediante la asignación de rol de RBAC adecuado a los usuarios, grupos y aplicaciones en el ámbito de las cuentas de Automatización. Estos son los roles integrados compatibles que admiten las cuentas de Automatización:

|**Rol** | **Descripción** |
|:--- |:---|
| Propietario | El rol Propietario permite el acceso a todos los recursos y acciones de una Cuenta de Automatización, lo que incluye proporcionar acceso a otros usuarios, grupos y aplicaciones para que administren la Cuenta de Automatización. |
| Colaborador | El rol Colaborador permite administrar todo, excepto la modificación de los permisos de acceso de otros usuarios a una Cuenta de Automatización. |
| Lector | El rol Lector permite ver todos los recursos de una Cuenta de Automatización, pero no permite realizar cambios. |
| Operador de Automatización | El rol Operador de Automatización permite realizar tareas operativas, como iniciar, detener, suspender, reanudar y programar trabajos. Este rol es útil si se desea proteger los recursos de la Cuenta de Automatización, como credenciales, activos y Runbooks, para que no puedan verse ni modificarse, pero permitir a los miembros de la organización ejecutar los Runbooks. [Acciones de operador de automatización](../active-directory/role-based-access-built-in-roles.md#automation-operator) enumera las acciones que admite el rol Operador de automatización en la Cuenta de Automatización y sus recursos. |
| Administrador de acceso de usuarios | El rol Administrador de acceso de usuarios permite administrar el acceso de los usuarios a las cuentas de Automatización de Azure. |

En este artículo se explicará cómo configurar RBAC en Automatización de Azure.

## Configuración de RBAC para una Cuenta de Automatización mediante el Portal de Azure

1.	Inicie sesión en el [Portal de Azure](https://portal.azure.com/) y abra su Cuenta de Automatización de la hoja Cuentas de Automatización.  

2.	Haga clic en el control **Acceso** de la esquina superior derecha. Se abrirá la hoja **Usuarios**, donde puede agregar nuevos usuarios, grupos y aplicaciones para administrar la Cuenta de Automatización y ver los roles existentes que se pueden configurar para la Cuenta de Automatización.

    ![Botón de acceso](media/automation-role-based-access-control/automation-01-access-button.png)

>[AZURE.NOTE]  **Administradores de suscripción** ya existe como usuario predeterminado. El grupo de Active Directory Administradores de suscripción incluye los administradores y coadministradores de servicios de su suscripción de Azure. El administrador del servicio es el propietario de la suscripción de Azure y sus recursos, y también heredará el rol de propietario de las cuentas de Automatización. Esto significa que el acceso es **Heredado** en el caso de los **administradores y coadministradores de servicios** de una suscripción, mientras que es **Asignado** en el caso de los restantes usuarios. Haga clic en **Administradores de suscripción** para ver más detalles acerca de sus permisos.

### Adición de usuarios nuevos y asignación de roles

1.	En la hoja Usuarios, haga clic en **Agregar** para abrir la **hoja Agregar acceso**, donde puede agregar un usuario, grupo o aplicación y asignarles un rol.  

    ![Agregar usuario](media/automation-role-based-access-control/automation-02-add-user.png)

2.	Seleccione en rol en la lista de roles disponibles. Aquí se elegirá el rol **Lector**, pero usted puede elegir cualquiera de los roles integrados disponibles que una Cuenta de Automatización admite o cualquier rol personalizado que puada haber definido.

    ![Seleccionar rol](media/automation-role-based-access-control/automation-03-select-role.png)

3.	Haga clic en **Agregar usuarios** para abrir la hoja **Agregar usuarios**. Si ha agregado los usuarios, grupos o aplicaciones para administrar su suscripción, se enumerarán dichos usuarios y podrá seleccionarlos para agregar acceso. Si no hay usuarios en la lista o si el usuario que le interesa agregar no está enumerado, haga clic en **Invitar** para abrir la hoja **Invitar a un invitado**, donde puede invitar a un usuario con una dirección de correo electrónico válida de la cuenta Microsoft como Outlook.com, OneDrive o Xbox Live ID. Una vez que haya escrito la dirección de correo electrónico del usuario, haga clic en **Seleccionar** para agregar el usuario y, después, haga clic en **Aceptar**.

    ![Agregar usuarios](media/automation-role-based-access-control/automation-04-add-users.png)
 
Lo normal es que el usuario se haya agregado a la hoja **Usuarios** y tenga el rol **Lector** asignado.

![Enumerar usuarios](media/automation-role-based-access-control/automation-05-list-users.png)

También se pueden asignar roles al usuario desde la hoja **Roles**. Haga clic en **Roles** en la hoja Usuarios y se abrirá la **hoja Roles**. En dicha hoja, puede ver el nombre del rol, el número de usuarios y los grupos asignados a dicho rol.

![Asignar rol en la hoja de usuarios](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)
   
>[AZURE.NOTE] El control de acceso basado en roles solo se puede establecer en el nivel de Cuenta de Automatización, no en los recursos que estén debajo de Cuenta de Automatización.

Es posible asignar más de un rol a un usuario, grupo o aplicación. Por ejemplo, si se agrega el rol **Operador de Automatización** junto con el rol **Lector** al usuario, este podrá ver todos los recursos de Automatización y ejecutar los trabajos de Runbook. Puede expandir la lista desplegable para ver una lista de los roles asignados al usuario.

![Ver varios roles](media/automation-role-based-access-control/automation-07-view-multiple-roles.png)
 
### Eliminación de un usuario

Puede quitar el permiso de usuario de cualquier usuario que no administre la Cuenta de Automatización o que haya dejado de trabajar en la organización. Estos son los pasos que deben seguirse para eliminar un usuario:

1.	En la hoja **Usuarios**, seleccione la asignación de rol que desea quitar.

2.	Haga clic en el icono **Quitar** de la hoja de detalles de asignación.

3.	Haga clic en **Sí** para confirmar la eliminación.

    ![Quitar usuarios](media/automation-role-based-access-control/automation-08-remove-users.png)

## Usuario con rol asignado

Cuando un usuario al que se ha asignado un rol inicia sesión en su Cuenta de Automatización puede ver la cuenta del propietario que se enumera en la lista de **Directorios predeterminados**. Para ver la Cuenta de Automatización al que se ha agregado, es preciso que cambie el directorio predeterminado al directorio predeterminado del propietario.

![Directorio predeterminado](media/automation-role-based-access-control/automation-09-default-directory-in-role-assigned-user.png)

### Experiencia del usuario en el rol Operador de automatización

Cuando un usuario, que se asigna a las vistas del rol Operador de automatización ve la Cuenta de Automatización a la que está asignado, solo puede ver la lista de Runbooks, los trabajos de Runbook y las programaciones creados en la Cuenta de Automatización, pero no pueden ver su definición. Puede iniciar, detener, suspender, reanudar o programar el trabajo de Runbook. El usuario no tendrá acceso a otros recursos de Automatización como configuraciones, grupos de Hybrid Worker o nodos de DSC.

![Sin acceso a los recursos](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

Cuando el usuario hace clic en el Runbook, los comandos para ver el origen o editar el Runbook no se proporcionan, ya que el rol de Operador de Automatización no permite acceder a ellos.

![Sin acceso para editar Runbook](media/automation-role-based-access-control/automation-11-no-access-to-edit-runbook.png)

El usuario tendrá acceso para ver y crear programaciones, pero no tendrá acceso a otros tipos de activos.

![Sin acceso a los activos](media/automation-role-based-access-control/automation-12-no-access-to-assets.png)

Este usuario tampoco tiene acceso para ver los Webhooks asociados a un Runbook

![Sin acceso a Webhooks](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)

## Configuración de RBAC para una Cuenta de Automatización mediante Azure PowerShell

El acceso basado en roles también se puede configurar en una Cuenta de Automatización mediante los siguientes [cmdlets de Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md).

• [Get AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) enumera todos los roles disponibles en el RBAC de Azure Active Directory. Este comando se puede usar con la propiedad **Name** para enumerar todos los usuarios con un rol concreto. **Ejemplo:** ![Obtener definición de rol](media/automation-role-based-access-control/automation-14-get-azurerm-role-definition.png)

• [Get AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) enumera las asignaciones de roles de RBAC de Azure en el ámbito especificado. Sin parámetros, este comando devuelve todas las asignaciones de roles realizadas en la suscripción. Use el parámetro **ExpandPrincipalGroups** para enumerar las asignaciones de acceso del usuario especificado, así como a los grupos de los que el usuario es miembro. **Ejemplo:** utilice el siguiente comando para enumerar todos los usuarios de una Cuenta de Automatización y sus roles.

    Get-AzureRMRoleAssignment -scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>” 

![Obtener asignación de rol](media/automation-role-based-access-control/automation-15-get-azurerm-role-assignment.png)

• [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) para conceder acceso a usuarios, grupos y aplicaciones en un ámbito determinado. **Ejemplo:** utilice el siguiente comando para crear un nuevo rol "Operador de Automatización" para un usuario en el ámbito de la Cuenta de Automatización.

    New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName "Automation operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”  

![Nueva asignación de rol](media/automation-role-based-access-control/automation-16-new-azurerm-role-assignment.png)

• Use [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) para quitar el acceso al acceso, grupo o aplicación en un ámbito determinado. **Ejemplo:** utilice el siguiente comando para crear un nuevo rol "Operador de Automatización" para un usuario en el ámbito de la Cuenta de Automatización.

    Remove-AzureRmRoleAssignment -SignInName "<sign-in Id of a user you wish to remove>" -RoleDefinitionName "Automation Operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”

En los cmdlets anteriores, reemplace el nombre de inicio de sesión, el identificador de suscripción, el nombre del grupo de recursos y nombre de la Cuenta de Automatización por los detalles de su cuenta. Elija **Sí** cuando se le pregunte si desea seguir y eliminar la asignación de roles.


## Pasos siguientes
-  Para más información sobre las diferentes formas de configurar RBAC en Automatización de Azure, consulte [Administración del control de acceso basado en rol (RBAC) con Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md).
- Para más información sobre las distintas maneras de iniciar un Runbook, consulte [Inicio de un runbook en Automatización de Azure](automation-starting-a-runbook.md).
- Para más información acerca de distintos tipos, consulte [Tipos de runbooks de Automatización de Azure](automation-runbook-types.md)

<!---HONumber=AcomDC_0218_2016-->