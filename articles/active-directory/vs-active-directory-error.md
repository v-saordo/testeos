<properties 
	pageTitle="Error durante la detección de autenticación" 
	description="El asistente para conexión de Active Directory detectó un tipo de autenticación no compatible" 
	services="active-directory" 
	documentationCenter="" 
	authors="TomArcher" 
	manager="douge" 
	editor=""/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/10/2015" 
	ms.author="tarcher"/>

# Error durante la detección de autenticación

Al detectar el código de autenticación anterior, el asistente detectó un tipo de autenticación incompatible.

##¿Qué se está comprobando?

**Nota:** para detectar correctamente el código de autenticación anterior de un proyecto, este debe estar creado. Si se produjo este error y no tiene un código de autenticación anterior en el proyecto, vuelva a compilarlo e inténtelo de nuevo.

###Tipos de proyecto

El asistente comprueba el tipo de proyecto que esté desarrollando, por lo que puede insertar la lógica de autenticación correcta en el proyecto. Si no hay ningún controlador que derive de `ApiController` en el proyecto, el proyecto se considerará como un proyecto WebAPI. Si solo hay controladores que derivan de `MVC.Controller` en el proyecto, el proyecto se considerará como proyecto MVC. El asistente considera todo lo demás como no compatible. Actualmente no son compatibles los proyectos WebForms.

###Código de autenticación compatible

El asistente también comprueba la configuración de autenticación que se ha configurado previamente con el asistente o que es compatible con el asistente. Si todos los valores de configuración están presentes, se considera un caso reentrante y el asistente abrirá y mostrará la configuración. Si solo algunos valores de configuración están presentes, se considera un caso de error.

En un proyecto MVC, el asistente comprueba cualquiera de los siguientes valores de configuración, que se originan a partir de un uso anterior del asistente:

	<add key="ida:ClientId" value="" />
	<add key="ida:Tenant" value="" />
	<add key="ida:AADInstance" value="" />
	<add key="ida:PostLogoutRedirectUri" value="" />

Además, el asistente comprueba los siguientes valores de configuración en el proyecto Web API, que se originan a partir del uso anterior del asistente:

	<add key="ida:ClientId" value="" />
	<add key="ida:Tenant" value="" />
	<add key="ida:Audience" value="" />

###Código de autenticación incompatible

Finalmente, el asistente trata de detectar versiones de código de autenticación que se hayan configurado con versiones anteriores de Visual Studio. Si recibió este error, significa el proyecto contiene un tipo de autenticación incompatible. El asistente detecta los siguientes tipos de autenticación de las versiones anteriores de Visual Studio:

* Autenticación de Windows 
* Cuentas de usuario individuales 
* Cuentas organizativas 
 

Para detectar la autenticación de Windows en un proyecto MVC, el asistente busca el elemento `authentication` en el archivo **web.config**.

<pre>
	&lt;configuration>
	    &lt;system.web>
	        <span style="background-color: yellow">&lt;authentication mode="Windows" /></span>
	    &lt;/system.web>
	&lt;/configuration>
</pre>

Para detectar la autenticación de Windows en un proyecto Web API, el asistente busca el elemento `IISExpressWindowsAuthentication` en el archivo **.csproj** del proyecto:

<pre>
	&lt;Project>
	    &lt;PropertyGroup>
	        <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication>enabled&lt;/IISExpressWindowsAuthentication></span>
	    &lt;/PropertyGroup>
	&lt;/Project>
</pre>

Para detectar la autenticación de cuentas de usuario individuales, el asistente busca el elemento de paquete en el archivo **Packages.config**.

<pre>
	&lt;packages>
	    <span style="background-color: yellow">&lt;package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /></span>
	&lt;/packages>
</pre>

Para detectar una forma anterior de autenticación con la cuenta de una organización, el asistente busca el siguiente elemento en **web.config**:

<pre>
	&lt;configuration>
	    &lt;appSettings>
	        <span style="background-color: yellow">&lt;add key="ida:Realm" value="***" /></span>
	    &lt;/appSettings>
	&lt;/configuration>
</pre>

Para cambiar el tipo de autenticación, quite el tipo de autenticación incompatible y ejecute de nuevo el asistente.

Para obtener más información, consulte [Escenarios de autenticación en Azure AD](active-directory-authentication-scenarios.md).

<!---HONumber=AcomDC_1217_2015-->