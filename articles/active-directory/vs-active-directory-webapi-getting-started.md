<properties 
	pageTitle="Introducción a Azure Active Directory y servicios conectados de Visual Studio (proyectos de WebApi) | Microsoft Azure" 
	description="Cómo empezar a usar Azure Active Directory en los proyectos de WebApi después de crear un Azure AD usando los servicios conectados de Visual Studio o de conectarse a él" 
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
	ms.date="12/18/2015" 
	ms.author="tarcher"/>

# Introducción a Azure Active Directory y servicios conectados de Visual Studio (proyectos de WebApi)

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-webapi-getting-started.md)
> - [What Happened](vs-active-directory-webapi-what-happened.md)

##Requerimiento de autenticación para obtener acceso a los controladores
 
Todos los controladores de su proyecto cuentan ahora con el atributo **Authorize**. Este atributo requerirá que el usuario se autentique antes de tener acceso a las API definidas por estos controladores. Para permitir el acceso anónimo al controlador, quite este atributo del controlador. Si desea establecer los permisos a un nivel más detallado, aplique el atributo a cada método que requiere autorización en vez de aplicarlo a la clase del controlador.

[Más información acerca de Azure Active Directory](https://azure.microsoft.com/services/active-directory/)
 

<!---HONumber=AcomDC_0128_2016-->