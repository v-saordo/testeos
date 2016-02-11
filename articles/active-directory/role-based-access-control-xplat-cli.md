<properties
	pageTitle="Administración del control de acceso basado en roles con la interfaz de la línea de comandos de Azure"
	description="Administración del control de acceso basado en roles con la interfaz de la línea de comandos de Azure"
	services="active-directory"
	documentationCenter="na"
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/25/2016"
	ms.author="kgremban"/>

# Administración del control de acceso basado en roles con la interfaz de la línea de comandos de Azure (CLI de Azure) #

> [AZURE.SELECTOR]
- [Windows PowerShell](role-based-access-control-powershell.md)
- [Azure CLI](/role-based-access-control-xplat-cli-install.md)

El control de acceso basado en rol (RBAC) del Portal de Azure y la API del Administrador de recursos de Azure le permite administrar el acceso a su suscripción y sus recursos en un nivel específico. Con esta característica, puede conceder acceso a usuarios, grupos o entidades de seguridad de servicio de Active Directory asignándoles roles en un ámbito determinado.

En este tutorial, aprenderá a usar la CLI de Azure para administrar RBAC. Le guía por el proceso de crear y comprobar asignaciones de roles.

**Tiempo estimado para completar el tutorial:** 15 minutos

## Requisitos previos ##

Para poder usar la CLI de Azure para administrar RBAC, necesita lo siguiente:

- CLI de Azure versión 0.8.8 o posterior. Para instalar la última versión y asociarla a la suscripción de Azure, consulte [Instalación y configuración de la interfaz de la línea de comandos de Azure](../xplat-cli-install.md).
- Lea también el siguiente tutorial para aprender a configurar y usar el Administrador de recursos de Azure en CLI de Azure: [Uso de la interfaz de la línea de comandos de Azure con Administrador de recursos](../xplat-cli-azure-resource-manager.md)

## Apartados de este tutorial ##

* [Conexión a las suscripciones](#connect)
* [Comprobar asignaciones de roles existentes](#check)
* [Crear una asignación de rol](#create)
* [Comprobar los permisos](#verify)
* [Pasos siguientes](#next)

## <a id="connect"></a>Conexión a las suscripciones ##

Como RBAC solo funciona con el Administrador de recursos de Azure, lo primero que hay que hacer es pasar al modo Administrador de recursos de Azure. Escriba:

    azure config mode arm

Para más información, consulte [Uso de la interfaz de la línea de comandos de Azure con Administrador de recursos](../xplat-cli-azure-resource-manager.md).

Para conectarse a sus suscripciones de Azure, escriba:

    azure login -u <username>

En el símbolo de la línea de comandos, escriba la contraseña de su cuenta de Azure (use solo una cuenta de organización). CLI de Azure obtendrá todas las suscripciones que tenga con esta cuenta y se configurará para usar la primera como predeterminada. Con el control de acceso basado en rol, solo podrá obtener las suscripciones donde tiene permisos por ser coadministrador o por tener una asignación de rol.

Si tiene varias suscripciones y quiere cambiar a otra, escriba:

    # This will show you the subscriptions under the account.
    azure account list
    # Use the subscription name to select the one you want to work on.
    azure account set <subscription name>

Para más información, consulte [Instalación y configuración de la interfaz de la línea de comandos de Azure](../xplat-cli-install.md).

## <a id="check"></a>Comprobar asignaciones de roles existentes ##

Ahora vamos a ver qué asignaciones de roles existen en la suscripción. Escriba:

    azure role assignment list

Esto devolverá todas las asignaciones de roles que haya en la suscripción. Cabe destacar dos cosas:

1. Necesitará acceso de lectura en el nivel de la suscripción.
2. Si la suscripción tiene muchas asignaciones de roles, se puede tardar un rato en recuperarlas todas.

También puede comprobar las asignaciones de roles existentes para una definición de rol determinada, en un ámbito concreto y para un cierto usuario. Escriba:

    azure role assignment list -g group1 --upn <user email> -o Owner

Esto devolverá toda las asignaciones de roles para un usuario determinado de su directorio de Azure AD, que tiene una asignación de rol de "Owner" para el grupo de recursos "group1". La asignación de rol puede venir de dos sitios:

1. Una asignación de rol de "Owner" al usuario para el grupo de recursos.
2. Una asignación de rol de "Owner" al usuario para el elemento primario del grupo de recursos (en este caso, la suscripción) porque, si tiene permisos en un recurso primario determinado, tendrá los mismos permisos en todos los recursos secundarios.

Todos los parámetros de este cmdlet son opcionales. Puede combinarlos para comprobar asignaciones de roles con distintos filtros.

## <a id="create"></a>Crear una asignación de rol ##

Para crear una asignación de rol, tiene que pensar en lo siguiente:

- A quién quiere asignar el rol: puede usar los siguientes cmdlets de Azure Active Directory para ver qué usuarios, grupos y entidades de seguridad de servicio tiene en su directorio.

    ```
    azure ad user list  
    azure ad user show  
    azure ad group list  
    azure ad group show  
    azure ad group member list  
    azure ad sp list  
    azure ad sp show  
    ```

- Qué rol quiere asignar: puede usar el cmdlet siguiente para ver las definiciones de rol compatibles.

    `azure role list`

- Qué ámbito quiere asignar: tiene tres niveles de ámbitos

    - La suscripción actual
    - Un grupo de recursos. Para obtener una lista de grupos de recursos, escriba `azure group list`
    - Un recurso. Para obtener una lista de recursos, escriba `azure resource list`

Luego use `azure role assignment create` para crear una asignación de rol. Por ejemplo:

 	#This will create a role assignment at the current subscription level for a user as a reader:
    `azure role assignment create --upn <user's email> -o Reader`

	#This will create a role assignment at a resource group level:
    `PS C:\> azure role assignment create --upn <user's email> -o Contributor -g group1`

	#This will create a role assignment at a resource level:
    `azure role assignment create --upn <user's email> -o Owner -g group1 -r Microsoft.Web/sites -u site1`

## <a id="verify"></a>Comprobar los permisos ##

Después de comprobar que su cuenta tiene asignaciones de roles, puede ver los permisos que conceden estas asignaciones de roles ejecutando lo siguiente:

    PS C:\> azure group list
    PS C:\> azure resource list

Esos dos cmdlets solo devolverán los grupos de recursos o los recursos donde tiene permiso de lectura. También le mostrarán los permisos que tiene.

Si intenta ejecutar otros cmdlets, como `azure group create`, obtendrá un error de acceso denegado si no tiene el permiso.

## <a id="next"></a>Pasos siguientes ##

Si quiere más información sobre cómo administrar el control de acceso basado en roles con CLI de Azure y otros temas relacionados:

- [Control de acceso basado en rol de Azure](../role-based-access-control-configure.md)
- [Instalación y configuración de la interfaz de la línea de comandos de Azure.](../xplat-cli-install.md)
- [Uso de la interfaz de la línea de comandos de Azure con Administrador de recursos](../xplat-cli-azure-resource-manager.md)
- [Uso de grupos de recursos para administrar los recursos de Azure](../azure-preview-portal-using-resource-groups.md): obtenga información acerca de la creación y administración de grupos de recursos en el Portal de administración de Azure.
- [Blog de Azure](http://blogs.msdn.com/windowsazure): obtenga información acerca de las nuevas características de Azure.
- [Configuración del control de acceso basado en roles usando Windows PowerShell](role-based-access-control-powershell.md)
- [Solución de problemas de control de acceso basado en roles](role-based-access-control-troubleshooting.md)

<!---HONumber=AcomDC_0128_2016-->