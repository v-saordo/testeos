<properties 
	pageTitle="Introducción a Azure Active Directory y servicios conectados de Visual Studio (proyectos de MVC) | Microsoft Azure" 
	description="Cómo empezar a usar Azure Active Directory en los proyectos de MVC después de crear un Azure AD usando los servicios conectados de Visual Studio o de conectarse a él" 
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

# Introducción a Azure Active Directory y servicios conectados de Visual Studio (proyectos de MVC)

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-dotnet-getting-started.md)
> - [What Happened](vs-active-directory-dotnet-what-happened.md)
 
##Requerimiento de autenticación para obtener acceso a los controladores 

Todos los controladores de su proyecto cuentan ahora con el atributo **Authorize**. Este atributo requerirá que el usuario se autentique antes de tener acceso a estos controladores. Para permitir el acceso anónimo al controlador, quite este atributo del controlador. Si desea establecer los permisos a un nivel más detallado, aplique el atributo a cada método que requiere autorización en vez de aplicarlo a la clase del controlador.
 
##Incorporación de controles SignIn / SignOut 

Para agregar controles SignIn/SignOut a su vista, utilice la vista parcial **\_LoginPartial.cshtml** para agregar esta funcionalidad a alguna de sus vistas. El siguiente es un ejemplo de la funcionalidad agregada a la vista estándar **\_Layout.cshtml**. Observe el último elemento de la sección div con la clase navbar-collapse:

<pre>
    &lt;!DOCTYPE html> 
     &lt;html> 
     &lt;head> 
         &lt;meta charset="utf-8" /> 
        &lt;meta name="viewport" content="width=device-width, initial-scale=1.0"> 
        &lt;title>@ViewBag.Title - My ASP.NET Application&lt;/title> 
        @Styles.Render("~/Content/css") 
        @Scripts.Render("~/bundles/modernizr") 
    &lt;/head> 
    &lt;body> 
        &lt;div class="navbar navbar-inverse navbar-fixed-top"> 
            &lt;div class="container"> 
                &lt;div class="navbar-header"> 
                    &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"> 
                        &lt;span class="icon-bar">&lt;/span> 
                        &lt;span class="icon-bar">&lt;/span> 
                        &lt;span class="icon-bar">&lt;/span> 
                    &lt;/button> 
                    @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" }) 
                &lt;/div> 
                &lt;div class="navbar-collapse collapse"> 
                    &lt;ul class="nav navbar-nav"> 
                        &lt;li>@Html.ActionLink("Home", "Index", "Home")&lt;/li> 
                        &lt;li>@Html.ActionLink("About", "About", "Home")&lt;/li> 
                        &lt;li>@Html.ActionLink("Contact", "Contact", "Home")&lt;/li> 
                    &lt;/ul> 
                    <span style="background-color:yellow">@Html.Partial("_LoginPartial")</span> 
                &lt;/div> 
            &lt;/div> 
        &lt;/div> 
        &lt;div class="container body-content"> 
            @RenderBody() 
            &lt;hr /> 
            &lt;footer> 
                &lt;p>&amp;copy; @DateTime.Now.Year - My ASP.NET Application&lt;/p> 
            &lt;/footer> 
        &lt;/div> 
        @Scripts.Render("~/bundles/jquery") 
        @Scripts.Render("~/bundles/bootstrap") 
        @RenderSection("scripts", required: false) 
    &lt;/body> 
    &lt;/html>
</pre>

[Más información acerca de Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

<!---HONumber=AcomDC_0128_2016-->