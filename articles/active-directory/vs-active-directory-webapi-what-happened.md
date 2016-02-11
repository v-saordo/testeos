<properties
	pageTitle="Qué ha ocurrido a mi proyecto WebApi (servicio conectado a Azure Active Directory de Visual Studio) | Microsoft Azure"
	description="Describe lo que sucede a la WebApi de su proyecto de MVC cuando se conecta a Azure AD mediante Visual Studio."
  services="active-directory"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/18/2015"
	ms.author="tarcher"/>

# ¿Qué ha ocurrido a mi proyecto de WebApi (servicio conectado de Visual Studio Azure Active Directory)

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-webapi-getting-started.md)
> - [What Happened](vs-active-directory-webapi-what-happened.md)

##Se han agregado referencias

###Referencias de paquetes de NuGet

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

###Referencias de .NET

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

##Cambios de código

###Se han agregado archivos de código a su proyecto

Se ha agregado una clase de inicio de autenticación, **App\_Start/Startup.Auth.cs**, a su proyecto que contiene la lógica de inicio para la autenticación de Azure AD.

###Se ha agregado código de inicio a su proyecto.

Si ya tenía una clase de inicio en su proyecto, el método **Configuration** se ha actualizado para incluir una llamada a `ConfigureAuth(app)`. Si no era así, se ha agregado una clase de inicio a su proyecto.


###Su archivo app.config o web.config tiene nuevos valores de configuración.

Se han agregado las siguientes entradas de configuración. ```
	`<appSettings>
    		<add key="ida:ClientId" value="ClientId from the new Azure AD App" />
    		<add key="ida:Tenant" value="Your selected Azure AD Tenant" />
    		<add key="ida:Audience" value="The App ID Uri from the wizard" />
	</appSettings>`
```

###Se ha creado una aplicación de Azure AD.

Se ha creado una aplicación de Azure AD en el directorio que seleccionó en el asistente.

[Más información acerca de Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

##Si he activado la *deshabilitación de la autenticación de cuentas de usuario individuales*, ¿qué otros cambios se realizaron en mi proyecto?
Se han quitado las referencias al paquete NuGet y se han quitado los archivos y se ha realizado una copia de seguridad de los mismos. Según el estado del proyecto, tendrá que quitar referencias o archivos adicionales manualmente o modificar el código, según corresponda.

###Referencias al paquete NuGet quitadas (si existen)

- `Microsoft.AspNet.Identity.Core`
- `Microsoft.AspNet.Identity.EntityFramework`
- `Microsoft.AspNet.Identity.Owin`

###Se ha realizado una copia de seguridad de los archivos y se han eliminado (si existen)

Se ha realizado una copia de seguridad de cada uno de los siguientes archivos y se han eliminado del proyecto. Los archivos de copia de seguridad se encuentran en una carpeta 'Backup' en la raíz del directorio del proyecto.

- `App_Start\IdentityConfig.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Providers\ApplicationOAuthProvider.cs`

###Copia de seguridad de los archivos de código (si existen)

Se realizó una copia de seguridad de cada uno de los siguientes archivos antes de ser reemplazados. Los archivos de copia de seguridad se encuentran en una carpeta 'Backup' en la raíz del directorio del proyecto.

- `Startup.cs`
- `App_Start\Startup.Auth.cs`

##Si activé *Leer datos de directorio*, ¿qué otros cambios se realizaron en mi proyecto?

###Se realizaron cambios adicionales en app.config o web.config

Se han agregado las siguientes entradas de configuración adicionales.

```
	`<appSettings>
	    <add key="ida:Password" value="Your Azure AD App's new password" />
	</appSettings>`
```

###Se ha actualizado la aplicación Azure Active Directory
La aplicación Azure Active Directory se actualizó para incluir el permiso *Leer datos de directorio* y se creó una clave adicional que luego se usó como *ida:Password* en el archivo `web.config`.

[Más información acerca de Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

<!---HONumber=AcomDC_0128_2016-->