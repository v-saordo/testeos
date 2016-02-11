<properties
	pageTitle="Uso del control de acceso (.NET) - Microsoft Azure"
	description="Aprenda a usar el servicio de control de acceso (ACS) en la aplicación de Azure para autenticar a los usuarios cuando intentan obtener acceso a una aplicación web."
	services="active-directory"
	documentationCenter=".net"
	authors="msmbaldwin"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/05/2015" 
	ms.author="mbaldwin"/>




# Autenticación de usuarios web con el control de acceso de Azure Active Directory


En esta guía se indica cómo usar el control de acceso de Azure Active Directory (también conocido como servicio de control de acceso o ACS) para autenticar usuarios con proveedores de identidades como Microsoft, Google, Yahoo y Facebook cuando intentan obtener acceso a una aplicación web.


## ¿Qué es ACS?

La mayoría de los desarrolladores ni son expertos en identidad ni quieren dedicar demasiado tiempo a desarrollar mecanismos de autenticación y autorización para sus aplicaciones y servicios. ACS es un servicio de Azure que ofrece un sencillo método de autenticación para que los usuarios tenga acceso a sus servicios y aplicaciones sin tener que agregar una lógica de autenticación compleja a su código.

En ACS están disponibles las características siguientes:

-   Integración con Windows Identity Foundation (WIF).
-   Compatibilidad con los proveedores de identidades (IP) habituales, entre los que se incluyen las cuentas de Microsoft (anteriormente conocidas como Windows Live ID), Google, Yahoo y Facebook.
-   Compatibilidad con los servicios de federación de Active Directory (AD FS) 2.0.
-   Un servicio de administración basado en un protocolo de datos abierto (OData) que proporciona acceso programático a la configuración de ACS.
-   Un portal de administración que concede acceso administrativo a la configuración de ACS.

Para obtener más información acerca de ACS, consulte [Access Control Service 2.0][].

## Conceptos

ACS se ha creado sobre los principios de la identidad basada en notificaciones, un enfoque coherente para crear mecanismos de autenticación para las aplicaciones que se ejecutan en el entorno local o en la nube. La identidad basada en notificaciones proporciona un método común para que las aplicaciones y los servicios obtengan la información necesaria acerca de la identidad de los usuarios de la organización, de otras organizaciones y en Internet.

Para completar las tareas de esta guía, debe comprender los términos y los conceptos siguientes que en ella se usan:


**Cliente**: explorador que intenta obtener acceso a su aplicación web.

**Aplicación de usuarios de confianza (RP)**: su aplicación web. Una aplicación RP es un sitio web o un servicio que externaliza la autenticación a una autoridad diferente. En la jerga de la identidad, se dice que los RP confían en dicha autoridad. En esta guía se explica cómo configurar su aplicación para que confíe en ACS.

**Token**: el usuario accede a una aplicación RP presentando un token válido emitido por una autoridad en la confía la aplicación RP. Cuando el usuario se autentica, se envía una colección de datos de seguridad. Esta contiene un conjunto de notificaciones, que son atributos del usuario autenticado, como su nombre o su edad, o bien un identificador para el rol del usuario. Se firma un token digitalmente para poder identificar a quien lo emite y no se pueda cambiar su contenido.

**Proveedor de identidades (IP)**: autoridad que autentica las identidades de los usuarios y emite los tokens de seguridad, como una cuenta de Microsoft (Windows Live ID), Facebook, Google, Twitter y Active Directory. Cuando ACS se ha configurado para confiar en un IP, acepta y valida los tokens que emite dicho IP. Como ACS puede confiar en varios IP a la vez, cuando su aplicación confía en ACS, puede ofrecer a los usuarios la opción de autenticarse con otro IP en el que confíe ACS.

**Proveedor de federación (FP)**: los proveedores de identidades (IP) conocen directamente a los usuarios, los autentican usando sus credenciales y emiten notificaciones sobre ellos. Otro tipo de autoridad es el proveedor de federación (FP). En lugar de autenticar a los usuarios directamente, el FP negocia su autenticación; es decir, actúa como intermediario entre la aplicación de un usuario de confianza y uno o varios IP. ACS es un proveedor de federación (FP).

**Motor de reglas de ACS**: las reglas de transformación de notificaciones convierten las notificaciones en tokens de IP de confianza para que un RP las pueda usar. ACS incluye un motor de reglas que aplica las reglas de transformación de notificaciones que usted especifique para su RP.

**Espacio de nombres de control de acceso**: proporciona un ámbito único para dirigir los recursos de ACS a su aplicación. El espacio de nombres contiene su configuración, como los IP en los que confía, las aplicaciones RP que desea servir, las reglas que aplica a los tokens entrantes y muestra los extremos que la aplicación y el desarrollador usan para comunicarse con ACS.

La siguiente ilustración muestra cómo funciona la autenticación de ACS con una aplicación web:

![][0]

1.  El cliente (en este caso, un explorador) solicita una página del RP.
2.  Como la solicitud no se ha autenticado todavía, el RP redirige al usuario a la autoridad en la que confía, es decir, ACS. El ACS presenta al usuario la selección de IP especificados para este RP. El usuario selecciona el IP adecuado.
3.  El cliente examina la página de autenticación del IP y pide al usuario que inicie sesión.
4.  Cuando el cliente se haya autenticado (por ejemplo, cuando haya escrito las credenciales de identidad), el IP emitirá un token de seguridad.
5.  Después de haber emitido un token de seguridad, el IP ordena al cliente que envíe a ACS el token de seguridad emitido por el IP.
6.  ACS valida el token de seguridad emitido por el IP, introduce las notificaciones de identidad de este token en el motor de reglas de ACS, calcula las notificaciones de identidad resultantes y emite un nuevo token de seguridad que contenga estas notificaciones.
7.  ACS ordena al cliente que envíe al RP el token de seguridad emitido por ACS. El RP valida la firma del token de seguridad, extrae las notificaciones que debe usar la lógica de negocios de la aplicación y devuelve la página que se solicitó originalmente.

## Requisitos previos


Necesitará lo siguiente para completar las tareas de esta guía:

-	Suscripción de Azure
-	Microsoft Visual Studio 2012
-	Herramienta de identidades y acceso para Visual Studio 2012 (para descargarla, consulte [Herramientas de identidades y acceso][])


## Creación de un espacio de nombres de control de acceso

Para usar el control de acceso de Active Directory en Azure, cree un espacio de nombres de control de acceso. El espacio de nombres proporciona un ámbito único para dirigir los recursos de ACS a su aplicación.

1.  Inicie sesión en el [Portal de administración de Azure][] (https://manage.WindowsAzure.com).

2.  Haga clic en **Active Directory**.

	![][1]

3.  Para crear un nuevo espacio de nombres de control de acceso, haga clic en **Nuevo**. **Servicios de aplicaciones** y **Control de acceso** se seleccionarán. Haga clic en **Creación rápida**.

	![][2]

4.  Escriba un nombre para el espacio de nombres. Azure verificará si el nombre es único.

5.  Seleccione la región en la que se usa el espacio de nombres. Para conseguir el mejor rendimiento, use la región en la que esté implementando su aplicación y, a continuación, haga clic en **Crear**.

Azure crea y activa el espacio de nombres.

## Crear una aplicación ASP.NET MVC

En este paso, creará una aplicación ASP.NET MVC. En los pasos siguientes, integraremos esta sencilla aplicación de formularios web con ACS.

1.	Inicie Visual Studio 2012 o Visual Studio Express 2012 para Web (las versiones anteriores de Visual Studio no funcionan con este tutorial).
1.	Haga clic en **Archivo** y, a continuación, en **Nuevo proyecto**.
1.	Seleccione la plantilla Visual C#/Web y, a continuación, **Aplicación Web ASP.NET MVC 4**.

	En esta guía usaremos una aplicación de MVC, pero puede utilizar cualquier tipo de aplicación web para esta tarea.

	![][3]

1. En **Nombre**, escriba **MvcACS** y, a continuación, haga clic en **Aceptar**.
1. En el diálogo siguiente, seleccione **Aplicación de Internet** y, a continuación, haga clic en **Aceptar**.
1. Edite el archivo *Views\\Shared\_LoginPartial.cshtml* y sustituya el contenido por el código siguiente:

        @if (Request.IsAuthenticated)
        {
            string name = "Null ID";
            if (!String.IsNullOrEmpty(User.Identity.Name))
            {
                name = User.Identity.Name;
            }
            <text>
            Hello, @Html.ActionLink(name, "Manage", "Account", routeValues: null, htmlAttributes: new { @class = "username", title = "Manage" })!
                    @using (Html.BeginForm("LogOff", "Account", FormMethod.Post, new { id = "logoutForm" }))
                    {
                        @Html.AntiForgeryToken()
                        <a href="javascript:document.getElementById('logoutForm').submit()">Log off</a>
                    }
            </text>
        }
        else
        {
            <ul>
                <li>@Html.ActionLink("Register", "Register", "Account", routeValues: null, htmlAttributes: new { id = "registerLink" })</li>
                <li>@Html.ActionLink("Log in", "Login", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
            </ul>
        }

Actualmente, ACS no configura User.Identity.Name, por lo que hay que realizar el cambio anterior.

1. Presione F5 para ejecutar la aplicación. La aplicación ASP.NET MVC predeterminada aparece en su explorador web.

## Integración de su aplicación web con ACS

En esta tarea, integrará su aplicación web ASP.NET con ACS.

1.	En el Explorador de soluciones, haga clic con el botón secundario en el proyecto MvcACS y, a continuación, seleccione **Identidades y acceso**.

	Si la opción**Identidades y acceso** no aparece en el menú contextual, instale la herramienta de identidades y acceso. Para obtener información, consulte [Herramienta de identidades y acceso].

	![][4]

2.	En la pestaña **Proveedores**, seleccione **Usar Azure Access Control Service **.

    ![][44]

3.  Haga clic en el vínculo **Configurar**.

    ![][444]

	Visual Studio solicita información acerca del espacio de nombres de control de acceso. Escriba el nombre del espacio de nombres que ha creado anteriormente (pruebe en las imágenes de arriba, aunque tendrá un espacio de nombres diferente). Vuelva al portal de administración de Azure para obtener la clave simétrica.

	![][17]

4.  En el Portal de administración de Azure, haga clic en el espacio de nombres de control de acceso y, a continuación, en **Administrar**.

	![][8]

5.	Haga clic en **Servicio de administración** y, a continuación, en **Cliente de administración**.

	![][18]

6.	Haga clic en **Clave simétrica**, después en **Mostrar clave** y copie el valor de la clave. A continuación, haga clic en **Cancelar** para salir de la página de edición del cliente de administración para salir sin realizar cambios.

	![][19]

7.  En Visual Studio, pegue la clave en el campo **Especificar la clave de administración para el espacio de nombres** y haga clic en **Guardar clave de administración** y, a continuación, haga clic en **Aceptar**.

	![][20]

	Visual Studio utiliza la información del espacio de nombres para conectarse al portal de administración de ACS y obtener la configuración de su espacio de nombres, incluyendo los proveedores de identidades, el dominio y la URL de retorno.

8.	Seleccione **Windows Live ID** (cuenta de Microsoft) y haga clic en Aceptar.

	![][5]

## Prueba de la integración con ACS

En esta tarea se explica cómo probar la integración de su aplicación RP con ACS.

-	Presione F5 en Visual Studio para ejecutar la aplicación.

Cuando la aplicación se integre con ACS y haya seleccionado Windows Live ID (cuenta de Microsoft), en lugar de abrir la aplicación de formularios web ASP.NET predeterminada, el navegador se redirigirá a la página de inicio de sesión para la cuenta de Microsoft. Cuando inicie sesión con un nombre de usuario y una contraseña válidos, entonces se le redirigirá a la aplicación MvcACS.

![][6]

¡Enhorabuena! Ha integrado correctamente ACS con la aplicación web ASP.NET. ACS ahora se encarga de la autenticación de los usuarios que utilizan sus credenciales de la cuenta de Microsoft.

## Visualización de las notificaciones enviadas por ACS

En esta sección se modificará la aplicación para ver las notificaciones enviadas por ACS. La herramienta de identidades y acceso ha creado un grupo de reglas que pasa por todas las notificaciones que emite el IP a su aplicación. Tenga en cuenta que cada proveedor de identidades envía notificaciones diferentes.

1. Abra el archivo *Controllers\\HomeController.cs*. Agregue una instrucción **using** a **System.Threading**:

 	using System.Threading;

1. En la clase HomeController, agregue el método *Claims*:

    public ActionResult Claims() { ViewBag.Message = "Your claims page.";

        ViewBag.ClaimsIdentity = Thread.CurrentPrincipal.Identity;

        return View();
    }

1. Haga clic con el botón derecho en el método *Claims* y seleccione **Agregar vista**.

![][66]

1. Haga clic en **Agregar**.

1. Reemplace el contenido del archivo *Views\\Home\\Claims.cshtml* por el código siguiente:

        @{
            ViewBag.Title = "Claims";
        }
        <hgroup class="title">
            <h1>@ViewBag.Title.</h1>
            <h2>@ViewBag.Message</h2>
        </hgroup>
        <h3>Values from Identity</h3>
        <table>
            <tr>
                <td>
                    IsAuthenticated:
                </td>
                <td>
                    @ViewBag.ClaimsIdentity.IsAuthenticated
                </td>
            </tr>
            <tr>
                <td>
                    Name:
                </td>
                <td>
                    @ViewBag.ClaimsIdentity.Name
                </td>
            </tr>
        </table>
        <h3>Claims from ClaimsIdentity</h3>
        <table>
            <tr>
                <th>
                    Claim Type
                </th>
                <th>
                    Claim Value
                </th>
            </tr>
                @foreach (System.Security.Claims.Claim claim in ViewBag.ClaimsIdentity.Claims ) {
            <tr>
                <td>
                    @claim.Type
                </td>
                <td>
                    @claim.Value
                </td>
            </tr>
        }
        </table>

1. Ejecute la aplicación y desplácese hasta el método*Claims*:

![][666]

Para obtener más información acerca de cómo usar las notificaciones de su aplicación, consulte la [biblioteca de Windows Identity Foundation](http://msdn.microsoft.com/library/hh377151.aspx).

## Visualización de la aplicación en el portal de administración de ACS

La herramienta de identidades y acceso de Visual Studio integra automáticamente su aplicación con ACS.

Cuando selecciona la opción Use Azure Access Control y, a continuación, ejecuta su aplicación, la herramienta de identidades y acceso agrega su aplicación como usuario de confianza, la configura para usar los proveedores de identidades seleccionados y, finalmente, genera y selecciona las reglas de transformación de notificaciones predeterminadas para la aplicación.

Puede revisar y cambiar estas opciones de configuración en el portal de administración de ACS. Siga estos pasos para revisar los cambios del portal.

1.	Inicie sesión en el [Portal de administración de Azure](http://manage.WindowsAzure.com).

2.	Haga clic en **Active Directory**.

	![][8]

3.	Seleccione un espacio de nombres de Access Control y luego haga clic en **Administrar**. Esta acción abre el portal de administración de ACS.

	![][9]


4.	Haga clic en **Aplicaciones de usuarios de confianza**.

	Aparece la nueva aplicación MvcACS en la lista de aplicaciones de usuarios de confianza. El dominio se establece automáticamente en la página principal de la aplicación.

	![][10]


5.	Haga clic en **MvcACS**.

	La página de edición de las aplicaciones de los usuarios de confianza contiene la configuración para la aplicación web MvcACS. Cuando realice cambios en la configuración de esta página y los guarde, estos se reflejarán inmediatamente en la aplicación.

	![][11]

6.	Desplácese por la página para ver las opciones de configuración restantes de la aplicación MvcACS, incluyendo los proveedores de identidades y las reglas de transformación de notificaciones.

	![][12]

En la sección siguiente, utilizaremos las características del portal de administración de ACS para realizar un cambio en la aplicación web, simplemente para mostrar lo fácil que es hacerlo.

## Incorporación de un proveedor de identidades

Utilizaremos el portal de administración de ACS para cambiar la autenticación de nuestra aplicación MvcACS. En este ejemplo, incorporaremos Google como proveedor de identidades para MvcACS.

1.	Haga clic en **Proveedores de identidades** (en el menú de navegación) y, a continuación, haga clic en **Agregar**.

	![][13]

2.	Haga clic en **Google** y, a continuación, en **Siguiente**. La casilla de la aplicación MvcACS está activa de forma predeterminada.

	![][14]

3. Haga clic en Guardar.

	![][15]


¡Listo! Si vuelve a Visual Studio, abra el proyecto para la aplicación MvcACS y haga clic en **Identidades y acceso**, la herramienta muestra los proveedores de identidades Windows Live ID y Google.

![][16]

Y cuando ejecute la aplicación, verá el efecto. Cuando una aplicación admite más de un proveedor de identidades, el explorador se dirige en primer lugar a la página hospedada por ACS y pide al usuario que elija un proveedor de identidades.

![][7]

Una vez que el usuario seleccione un proveedor de identidades, el explorador irá a la página de inicio de sesión de este.

## Pasos siguientes

Ha creado una aplicación web que está integrada con ACS. Sin embargo, esto es solo el principio. Puede expandir este escenario.

Por ejemplo, puede agregar más proveedores de identidades para este RP o permitir que los usuarios registrados en los directorios empresariales, como los servicios de dominio de Active Directory, inicien sesión en la aplicación web.

También puede agregar reglas a su espacio de nombres que determinen qué notificaciones se envían a una aplicación para que se procese en su lógica de negocios.

Para seguir explorando la funcionalidad de ACS y experimentar con más escenarios, consulte [Access Control Service 2.0].



  [What is ACS?]: #what-is
  [Concepts]: #concepts
  [Prerequisites]: #pre
  [Create an ASP.NET MVC Application]: #create-web-app
  [Create an Access Control Namespace]: #create-namespace
  [Integrate your Web Application with ACS]: #Identity-Access
  [Test the Integration with ACS]: #Test-ACS
  [View the Application in the ACS Management Portal]: acs-portal
  [Add an Identity Provider]: #add-IP
  [What's Next]: #whats-next
  [vcsb]: #bkmk_viewClaims
  [vpp]: #bkmk_VP

  [Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
  [Herramienta de identidades y acceso]: http://go.microsoft.com/fwlink/?LinkID=245849
  [Herramientas de identidades y acceso]: http://go.microsoft.com/fwlink/?LinkID=245849
  [Portal de administración de Azure]: http://manage.WindowsAzure.com

  [0]: ./media/active-directory-dotnet-how-to-use-access-control/acs-01.png
  [1]: ./media/active-directory-dotnet-how-to-use-access-control/acsCreateNamespace.png
  [2]: ./media/active-directory-dotnet-how-to-use-access-control/acsQuickCreate.png
  [3]: ./media/active-directory-dotnet-how-to-use-access-control/rzMvc.png
  [4]: ./media/active-directory-dotnet-how-to-use-access-control/rzIA.png
[44]: ./media/active-directory-dotnet-how-to-use-access-control/rzPT.png
 [444]: ./media/active-directory-dotnet-how-to-use-access-control/rzC.png
  [5]: ./media/active-directory-dotnet-how-to-use-access-control/acsIdAndAccess1.png
  [6]: ./media/active-directory-dotnet-how-to-use-access-control/acsMSFTAcct.png
  [66]: ./media/active-directory-dotnet-how-to-use-access-control/rzAv.png
  [666]: ./media/active-directory-dotnet-how-to-use-access-control/rzCl.png
  [7]: ./media/active-directory-dotnet-how-to-use-access-control/acsSignIn.png
  [8]: ./media/active-directory-dotnet-how-to-use-access-control/acsClickManage.png
  [9]: ./media/active-directory-dotnet-how-to-use-access-control/acsACSPortal.png
  [10]: ./media/active-directory-dotnet-how-to-use-access-control/acsRPPage.png
  [11]: ./media/active-directory-dotnet-how-to-use-access-control/acsEdit-RP.png
  [12]: ./media/active-directory-dotnet-how-to-use-access-control/acsEdit-RP2.png
  [13]: ./media/active-directory-dotnet-how-to-use-access-control/acsAdd-Idp.png
  [14]: ./media/active-directory-dotnet-how-to-use-access-control/acsAdd-Google.png
  [15]: ./media/active-directory-dotnet-how-to-use-access-control/acsSave-Google.png
  [16]: ./media/active-directory-dotnet-how-to-use-access-control/acsIdAndA-after.png
  [17]: ./media/active-directory-dotnet-how-to-use-access-control/acsConfigAcsNamespace.png
  [18]: ./media/active-directory-dotnet-how-to-use-access-control/acsManagementService.png
  [19]: ./media/active-directory-dotnet-how-to-use-access-control/acsShowKey.png
  [20]: ./media/active-directory-dotnet-how-to-use-access-control/acsConfigAcsNamespace2.png

<!---HONumber=AcomDC_1210_2015-->