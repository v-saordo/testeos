<properties
	pageTitle="Control de acceso basado en roles de Azure Active Directory | Microsoft Azure"
	description="En este artículo se describe el control de acceso basado en roles de Azure."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="02/10/2016"
	ms.author="kgremban"/>

# Control de acceso basado en roles de Azure

## Control de acceso basado en roles
El control de acceso basado en roles (RBAC) de Azure permite realizar una administración detallada del acceso para Azure. Gracias a RBAC puede dividir las tareas entre el equipo de DevOps, y conceder a los usuarios únicamente el nivel de acceso que necesitan para realizar su trabajo.

### Aspectos básicos de la administración de acceso en Azure
Cada suscripción de Azure está asociada a un directorio Azure Active Directory. Solo se puede conceder acceso a los usuarios, los grupos y las aplicaciones de ese directorio para que administren los recursos en la suscripción de Azure, mediante el Portal de Azure, las herramientas de línea de comandos de Azure y las API de administración de Azure.

El acceso se concede mediante la asignación de rol RBAC adecuado a los usuarios, grupos y aplicaciones en el ámbito correcto. Para conceder acceso a toda la suscripción, asigne un rol en el ámbito de la suscripción. Para conceder acceso a un grupo de recursos específico dentro de una suscripción, asigne un rol en el ámbito de grupo de recursos. También puede asignar roles a recursos específicos, como sitios web, máquinas virtuales y subredes para conceder acceso a un único recurso.

![Recursos de Azure Active Directory y herramientas de administración de recursos (diagrama)](./media/role-based-access-control-configure/overview.png)

El rol RBAC que asigna a los usuarios, los grupos y las aplicaciones determina qué recursos se pueden administrar dentro de ese ámbito.

### Roles integrados en Azure RBAC
Azure RBAC cuenta con tres roles básicos que se aplican a todos los tipos de recurso: propietario, colaborador y lector. El propietario tiene acceso completo a todos los recursos y cuenta con el derecho a delegar acceso a otros. El colaborador puede crear y administrar todos los tipos de recursos de Azure pero no puede conceder acceso a otros. El lector solo puede ver los recursos existentes de Azure. El resto de los roles RBAC de Azure permiten la administración de recursos específicos de Azure. Por ejemplo, el rol de colaborador de máquina virtual permite crear y administrar máquinas virtuales, pero no permite administrar la red virtual ni la subred a las que se conecta la máquina virtual.

[Roles RBAC integrados](role-based-access-built-in-roles.md) muestra los roles RBAC integrados disponibles en Azure. En cada rol se especifican las operaciones a las que el rol integrado da acceso.

### Jerarquía de recursos de Azure y herencia de acceso
Cada suscripción de Azure pertenece a un único directorio, cada grupo de recursos pertenece a una única suscripción y cada recursos pertenece a un único grupo de recursos. El acceso que se concede en el nivel principal se hereda en los ámbitos secundarios. Si se concede el rol de lector a un grupo de Azure AD en el ámbito de suscripción, los miembros de dicho grupo podrán ver todos los grupos de recursos y todos los recursos de la suscripción. Si se concede el rol de colaborador a una aplicación en el ámbito de grupo de recursos, podrá administrar los recursos de todos los tipos de ese grupo de recursos, pero no de otros grupos de recursos de la suscripción.

### Azure RBAC frente al administrador y los coadministradores de la suscripción clásica
El administrador y los coadministradores de la suscripción clásica tienen acceso competo a la suscripción de Azure. Pueden administrar los recursos mediante el [Portal de Azure](https://portal.azure.com), las API del Administrador de recursos de Azure, o el [Portal de Azure clásico](https://manage.windowsazure.com) y con las API de la Administración de servicios de Azure. En el modelo RBAC, a los administradores clásicos se les asigna el rol de propietario en el ámbito de suscripción.

El modelo de autorización detallado (Azure RBAC) solo es compatible con el Portal de Azure y con las nuevas API del Administrador de recursos de Azure. Los usuarios y las aplicaciones a los que se asignan roles RBAC (en el ámbito de suscripción/grupo de recursos/recurso) no pueden usar el portal de administración clásico ni las API de Administración de servicios de Azure.

### Autorización para administración frente a operaciones de datos
El modelo de autorización detallado (Azure RBAC) solo es compatible con operaciones de administración de los recursos de Azure del Portal de Azure y las API de Administrador de recursos de Azure. No todas las operaciones de nivel de datos para recursos de Azure se pueden autorizar mediante RBAC. Por ejemplo, la creación, la lectura, la actualización o la eliminación de cuentas de almacenamiento se puede controlar mediante RBAC; pero la creación, la lectura, la actualización o la eliminación de blobs o tablas de la cuenta de almacenamiento aún no se puede controlar mediante RBAC. Del mismo modo, la creación, la lectura, la actualización o la eliminación de una base de datos de SQL se puede controlar mediante RBAC; pero la creación, la lectura, la actualización o la eliminación tablas de base de datos de SQL aún no se puede controlar mediante RBAC.

## Administrar el acceso con el portal de Azure
### Vista de acceso
Seleccione la configuración de acceso en la sección de Aspectos básicos de la hoja de **grupo de recursos**. La hoja **Usuarios** muestra todos los usuarios, los grupos y las aplicaciones a los que se ha concedido acceso al grupo de recursos. El acceso se asigna en el grupo de recursos o se hereda de una asignación en la suscripción principal.

![Hoja de usuarios: acceso heredado frente a asignado (captura de pantalla)](./media/role-based-access-control-configure/view-access.png)

> [AZURE.NOTE] Los administradores y coadministradores de la suscripción clásica son propietarios de la suscripción en el nuevo modelo RBAC.


### Agregación de acceso
1. Haga clic en el icono **Agregar** de la hoja **Usuarios**. ![Hoja Agregar acceso: seleccionar un rol (captura de pantalla)](./media/role-based-access-control-configure/grant-access1.png)
2. Seleccione el rol que desea asignar.
3. Busque y seleccione el usuario, el grupo o la aplicación al que desea conceder acceso.
4. Busque en el directorio usuarios, grupos y aplicaciones usando los nombres para mostrar direcciones de correo electrónico e identificadores de objeto.![Hoja Agregar usuarios: búsqueda (captura de pantalla)](./media/role-based-access-control-configure/grant-access2.png)

### Eliminación de acceso
1. En la hoja **Usuarios**, seleccione la asignación de rol que desea quitar.
2. Haga clic en el icono **Quitar** de la hoja de detalles de asignación.
3. Haga clic en **Sí** para confirmar la eliminación.

![Hoja de usuarios: quitar del rol (captura de pantalla)](./media/role-based-access-control-configure/remove-access1.png)


> [AZURE.NOTE] Las asignaciones heredadas no se pueden quitar de los ámbitos secundarios. Navegue al ámbito principal y quite allí las asignaciones.

![Hoja de usuario: acceso heredado deshabilita el botón de eliminación (captura de pantalla)](./media/role-based-access-control-configure/remove-access2.png)

## Administración del acceso con Azure Powershell
El acceso se puede administrar mediante el uso de comandos de Azure RBAC en las herramientas de Azure PowerShell.

-	Use `Get-AzureRmRoleDefinition` para mostrar los roles RBAC disponibles para su asignación y para inspeccionar las operaciones a las que conceden acceso.

-	Use `Get-AzureRmRoleAssignment` para mostrar las asignaciones de acceso RBAC vigentes en la suscripción, el grupo de recursos o el recurso especificado. Use el parámetro `ExpandPrincipalGroups` para mostrar las asignaciones de acceso al usuario especificado, así como a los grupos de los que el usuario es miembro. Use el parámetro `IncludeClassicAdministrators` para mostrar también el administrador y los coadministradores de la suscripción clásica.

-	Use `New-AzureRmRoleAssignment` para conceder acceso a usuarios, grupos y aplicaciones.

-	Use `Remove-AzureRmRoleAssignment` para quitar el acceso.

Consulte [Administración de acceso con Azure PowerShell](role-based-access-control-manage-access-powershell.md) para ver ejemplos más detallados de la administración de acceso con Azure PowerShell.

## Administración del acceso con la interfaz de la línea de comandos de Azure
El acceso se puede administrar mediante el uso de comandos de Azure RBAC en la interfaz de la línea de comandos de Azure.

-	Use `azure role list` para mostrar los roles RBAC disponibles para asignación. Use `azure role show` para inspeccionar las operaciones a las que conceden acceso.

-	Use `azure role assignment list` para mostrar las asignaciones de acceso RBAC vigentes en la suscripción, el grupo de recursos o el recurso especificado. Use la opción `expandPrincipalGroups` para mostrar las asignaciones de acceso al usuario especificado, así como a los grupos de los que el usuario es miembro. Use el parámetro `includeClassicAdministrators` para mostrar también el administrador y los coadministradores de la suscripción clásica.

-	Use `azure role assignment create` para conceder acceso a usuarios, grupos y aplicaciones.

-	Use `azure role assignment delete` para quitar el acceso.

Consulte [Administración de acceso con la CLI de Azure](role-based-access-control-manage-access-azure-cli.md) para ver ejemplos más detallados de la administración de acceso con la CLI de Azure.

## Administración del acceso mediante la API de REST
Consulte [Administración del control de acceso basado en rol con la API de REST](role-based-access-control-manage-access-rest.md) para obtener más ejemplos de la administración del acceso mediante la API de REST.

## Uso del informe de historial de cambios de acceso
Todos los cambios de acceso que tienen lugar en las suscripciones de Azure se registran en eventos de Azure.

### Creación de un informe con Azure PowerShell
Para crear un informe sobre quién concedió/revocó qué tipo de acceso a quién en qué ámbito dentro de las suscripciones de Azure, use el siguiente comando de PowerShell:

    `Get-AzureRMAuthorizationChangeLog`

### Creación de un informe con la CLI de Azure
Para crear un informe sobre quién concedió/revocó qué tipo de acceso a quién en qué ámbito dentro de las suscripciones de Azure, use el siguiente comando de la interfaz de la línea de comandos (CLI) de Azure:

    `azure authorization changelog`

> [AZURE.NOTE] Se pueden consultar los cambios de acceso de los últimos 90 días (en lotes de 15 días).

A continuación se muestran todos los cambios de acceso de la suscripción que tuvieron lugar en los últimos 7 días.

![PowerShell Get:AzureRMAuthorizationChangeLog (captura de pantalla)](./media/role-based-access-control-configure/access-change-history.png)

### Exportación de cambios de acceso a una hoja de cálculo
Es conveniente exportar los cambios de acceso a una hoja de cálculo para su revisión.

![Changelog visto como una hoja de cálculo ( captura de pantalla)](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## Roles personalizados en RBAC de Azure
Cree un rol personalizado en Azure RBAC si ninguno de los roles integrados satisface sus necesidades de acceso específicas. Los roles personalizados se pueden crear con las herramientas de la línea de comandos de RBAC en Azure PowerShell y con la interfaz de la línea de comandos de Azure. Igual que los roles integrados, los roles personalizados pueden asignarse a usuarios, grupos y aplicaciones en un ámbito de suscripciones, grupos de recursos y recursos.

A continuación encontrará un ejemplo de una definición de rol personalizado que permite la supervisión y el reinicio de máquinas virtuales:

```
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
### Acciones
La propiedad Actions de un rol personalizado especifica las operaciones de Azure para las que el rol concede acceso. Se trata de una colección de cadenas de operación que identifican a las operaciones protegibles de proveedores de recursos de Azure. Las cadenas de operación que contienen caracteres comodín (*) conceden acceso a todas las operaciones que coinciden con la cadena de la operación. Por ejemplo:

-	`*/read` concede acceso a las operaciones de lectura a todos los tipos de recursos de todos los proveedores de recursos de Azure.
-	`Microsoft.Network/*/read` concede acceso a las operaciones de lectura a todos los tipos de recursos del proveedor de recursos Microsoft.Network de Azure.
-	`Microsoft.Compute/virtualMachines/*` concede acceso a todas las operaciones de las máquinas virtuales y a sus tipos de recursos secundarios.
-	`Microsoft.Web/sites/restart/Action` concede acceso para reiniciar sitios web.

Use los comandos `Get-AzureRmProviderOperation` o `azure provider operations show` para mostrar una lista de operaciones de los proveedores de recursos de Azure. También puede usar estos comandos para comprobar que una cadena de operación es válida y para expandir las cadenas de operación con comodín.

![PowerShell Get: AzureRMProviderOperation (captura de pantalla)](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

![Muestra de operaciones de proveedor de Azure de CLI de Azure (captura de pantalla)](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

### No acciones
Si el conjunto de operaciones que desea permitir se expresa más fácilmente excluyendo operaciones específicas, en lugar de incluir todas las operaciones (excepto las que quiere excluir), use la propiedad **NotActions** de un rol personalizado. El acceso efectivo concedido por un rol personalizado se calcula mediante la exclusión de las operaciones **NotActions** de las operaciones **Actions**.

> [AZURE.NOTE] Si un usuario tiene asignado un rol que excluye una operación en **NotActions** y se le asigna un segundo rol que sí concede acceso a la misma operación, el usuario podrá realizar dicha operación. **NotActions** no es una regla de denegación, es simplemente una manera cómoda de crear un conjunto de operaciones permitidas cuando es necesario excluir operaciones específicas.

### Ámbitos asignables
La propiedad **AssignableScopes** del rol personalizado especifica los ámbitos (suscripciones, grupos de recursos o recursos) dentro de los que dicho rol personalizado está disponible para su asignación a usuarios, grupos y aplicaciones. Con el uso de **AssignableScopes** puede disponer del rol personalizado para su asignación solamente a las suscripciones o los grupos de recursos que lo requieran, sin necesidad de abarrotar la experiencia de usuario del resto de las suscripciones o grupos de recursos.

> [AZURE.NOTE] Tiene que utilizar al menos una suscripción, grupo de recursos o identificador de recurso.

**AssignableScopes** de un rol personalizado también controla quién puede ver, actualizar y eliminar el rol. A continuación se muestran algunos ámbitos asignables válidos:

-	“/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e”, “/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624”: permite la disponibilidad del rol para su asignación en dos suscripciones.
-	“/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e”: permite la disponibilidad del rol para su asignación en una sola suscripción.
-	“/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network”: permite la disponibilidad del rol para su asignación solamente en el grupo de recursos de Network.

### Control de acceso de los roles personalizados
La propiedad **AssignableScopes** del rol personalizado prescribe quién puede ver, modificar y eliminar el rol.

**¿Quién puede crear un rol personalizado?** La creación de roles personalizados se puede llevar a cabo solamente si el usuario que la realiza tiene permiso para crear un rol personalizado para todos los ámbitos especificados por **AssignableScopes**. La creación de roles personalizados se puede llevar a cabo solamente si el usuario que la realiza puede ejecutar `Microsoft.Authorization/roleDefinition/write operation` en todos los ámbitos **AssignableScopes** del rol. Por lo tanto, los propietarios (y administradores de acceso de usuario) de las suscripciones, grupos de recursos y recursos pueden crear roles personalizados para su uso en esos ámbitos.

**¿Quién puede modificar un rol personalizado?** Los usuarios que pueden actualizar roles personalizados para todos los ámbitos **AssignableScopes** de un rol, pueden modificar dicho rol personalizado. Los usuarios que pueden realizar la operación `Microsoft.Authorization/roleDefinition/write` en todos los ámbitos **AssignableScopes** de un rol personalizado, pueden modificar dicho rol personalizado. Por ejemplo, si un rol personalizado es asignable en dos suscripciones de Azure (es decir, tiene dos suscripciones en su propiedad **AssignableScopes**), un usuario tiene que ser el propietario (o administrador de acceso de usuario) de ambas suscripciones para poder modificar el rol personalizado.

**¿Quién puede ver los roles personalizados que están disponibles para su asignación en un ámbito?** Los usuarios que pueden realizar la operación `Microsoft.Authorization/roleDefinition/read` en un ámbito, pueden ver los roles RBAC que están disponibles para su asignación en ese ámbito. Todos los roles integrados de RBAC de Azure permiten ver los roles que están disponibles para la asignación.

<!---HONumber=AcomDC_0211_2016-->