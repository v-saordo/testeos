<properties
	pageTitle="Introducción a Azure AD AngularJS | Microsoft Azure"
	description="Creación de una aplicación de una sola página AngularJS que se integra con Azure AD para el inicio de sesión y llama a las API protegidas de Azure AD mediante OAuth."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="dastrock"/>


# Seguridad de las aplicaciones de una sola página AngularJS con Azure AD

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Con Azure AD, es sencillo y directo agregar llamadas de inicio y cierre de sesión, así como llamadas seguras de API de OAuth a las aplicaciones una sola página (SPA). Permite que la aplicación autentique a los usuarios con sus cuentas de Active Directory, además de consumir cualquier web API protegida por Azure AD, como las API de Office 365 o la API de Azure.

Para las aplicaciones de Javascript que se ejecuta en un explorador, Azure AD proporciona la biblioteca de autenticación de Active Directory (o adal.js). El único propósito de adal.js es facilitar a su aplicación la obtención de tokens de acceso. Para demostrar lo fácil que es, crearemos aquí una aplicación de lista de tareas pendientes (ToDo) de AngularJS que permita realizar las siguientes acciones:

- Iniciar sesión del usuario en la aplicación con Azure AD como proveedor de identidades
- Mostrar información sobre el usuario
- Realizar una llamada con seguridad a la API de la lista de tareas pendientes de la aplicación mediante tokens de portador de AAD
- Cerrar la sesión del usuario en la aplicación

Para crear la aplicación de trabajo completa, deberá realizar estas acciones:

2. Registrar la aplicación con Azure AD.
3. Instalar ADAL y configurar SPA
5. Usar ADAL para proteger las páginas de la SPA

Para empezar, [descargue el esquema de la aplicación](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) o [descargue el ejemplo finalizado](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip). También necesitará a un inquilino de Azure AD en el que pueda crear usuarios y registrar una aplicación. Si aún no tiene un inquilino, [descubra cómo conseguir uno](active-directory-howto-tenant.md).

## *1. Registro de la aplicación DirectorySearcher*
Para habilitar la aplicación a fin de autenticar a los usuarios y obtener tokens, primero deberá registrarla en el inquilino de Azure AD:

-	Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com).
-	En el panel de navegación izquierdo, haga clic en **Active Directory**.
-	Seleccione el inquilino en el que va a registrar la aplicación.
-	Haga clic en la pestaña **Aplicaciones** y en **Agregar** en el cajón inferior.
-	Siga las indicaciones y cree una nueva **Aplicación web y/o API web**.
    -	El **nombre** de la aplicación permite proporcionar una descripción de la aplicación a los usuarios finales.
    -	El **URI de redirección** es la ubicación a la que AAD devolverá los tokens. La ubicación predeterminada para este ejemplo es `https://localhost:44326/`
-	Una vez que haya completado el registro, AAD asignará a su aplicación un **identificador de cliente único**. Necesitará este valor en las secciones siguientes, de modo que cópielo desde la pestaña **Configurar**.
- Adal.js utiliza el flujo implícito de OAuth para comunicarse con Azure AD. Debe habilitar el flujo implícito para la aplicación; para ello, siga estos pasos:
    - Descargue el manifiesto de la aplicación haciendo clic en **Administrar manifiesto**.
    - Abra el manifiesto y busque la propiedad `oauth2AllowImplicitFlow`. Establezca su valor en `true`.
    - Guarde y cargue el manifiesto de la aplicación haciendo clic en **Administrar manifiesto** de nuevo.

## *2. Instalación de ADAL y configuración de SPA*
Ahora que tiene una aplicación en Azure AD, puede instalar adal.js y escribir el código relacionado con la identidad.

-	Comience agregando adal.js al proyecto TodoSPA mediante la consola del administrador de paquetes:
  - Descargue [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) y agréguelo al directorio del proyecto `App/Scripts/`.
  - Descargue [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) y agréguelo al directorio del proyecto `App/Scripts/`.
  - Cargue cada script antes del fin de `</body>` en `index.html`:

```js
...
<script src="App/Scripts/adal.js"></script>
<script src="App/Scripts/adal-angular.js"></script>
...
```

-	Para que la API de tareas pendientes del back-end de SPA acepte tokens del explorador, el back-end necesita información de configuración acerca del registro de la aplicación. En el proyecto TodoSPA, abra `web.config`. Reemplace los valores de los elementos de la sesión `<appSettings>` para que reflejen los valores especificados en el Portal de Azure. El código hará referencia a estos valores cada vez que use ADAL.
    -	`ida:Tenant` es el dominio del inquilino de Azure AD, por ejemplo, contoso.onmicrosoft.com.
    -	`ida:Audience` es el **identificador de cliente** de la aplicación que copió del portal.

## *3. Uso de ADAL para proteger las páginas de la SPA*
Adal.js se ha creado para integrarse con los proveedores de http y ruta AngularJS, lo que permite proteger las vistas individuales en la SPA.

- En `App/Scripts/app.js`, importe el módulo adal.js:

```js
angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {
...
```
- Ahora se puede inicializar `adalProvider` con los valores de configuración del registro de su aplicación, también en `App/Scripts/app.js`:

```js
adalProvider.init(
  {
      instance: 'https://login.microsoftonline.com/',
      tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
      clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
      extraQueryParameter: 'nux=1',
      //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
  },
  $httpProvider
);
```
- Ahora, para proteger la vista `TodoList` en la aplicación, solo se necesita una línea de código: `requireADLogin`.

```js
...
}).when("/TodoList", {
        controller: "todoListCtrl",
        templateUrl: "/App/Views/TodoList.html",
        requireADLogin: true,
...
```

Ahora tiene una aplicación de una sola página segura con capacidad para iniciar la sesión de los usuarios y emitir solicitudes protegidas de token de portador a su API de back-end. Cuando un usuario hace clic en el vínculo `TodoList`, adal.js le redireccionará automáticamente a Azure AD para iniciar sesión si es necesario. Además, adal.js adjuntará automáticamente un access\_token a cualquier solicitud de ajax que se envíe al servidor back-end de la aplicación. El ejemplo anterior es el mínimo necesario para crear una SPA con adal.js, pero hay otras características que son útiles en SPA:

- Para emitir explícitamente solicitudes de inicio y cierre de sesión, puede definir funciones en sus controladores que invocan adal.js. En `App/Scripts/homeCtrl.js`:

```js
...
$scope.login = function () {
    adalService.login();
};
$scope.logout = function () {
    adalService.logOut();
};
...
```
- Puede que desee presentar información de usuario en la interfaz de usuario de la aplicación. El servicio ADAL ya se ha agregado al controlador `userDataCtrl`, por lo que puede tener acceso al objeto `userInfo` en la vista asociada, `App/Views/UserData.html`:

```js
<p>{{userInfo.userName}}</p>
<p>aud:{{userInfo.profile.aud}}</p>
<p>iss:{{userInfo.profile.iss}}</p>
...
```

- También puede darse el caso de que sea necesario saber si el usuario ha iniciado sesión o no. También puede utilizar el objeto `userInfo` para recopilar esta información. Por ejemplo, en `index.html` puede mostrar el botón "Inicio de sesión" o "Cierre de sesión" en función del estado de autenticación:

```js
<li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
<li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
```

¡Enhorabuena! La aplicación de una sola página integrada de Azure AD está completa ahora. Permite autenticar a los usuarios, efectuar llamadas seguras a back-end mediante OAuth 2.0 y obtener información básica sobre el usuario. Si todavía no lo ha hecho, ahora es el momento de completar el inquilino con algunos usuarios. Ejecute la SPA de la lista de tareas pendientes e inicie sesión con uno de esos usuarios. Agregue tareas a la lista de tareas pendientes de los usuarios, cierre la sesión y vuelva a iniciarla.

Adal.js facilita la incorporación de todas estas características comunes de identidad a la aplicación. Se encarga de todo el trabajo duro: administración de la caché, compatibilidad con el protocolo OAuth, presentación del usuario con una interfaz de usuario de inicio de sesión, actualización de los tokens caducados, etc.

Como referencia, [aquí](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip) puede ver el ejemplo finalizado (sin sus valores de configuración). Ahora puede pasar a otros escenarios. También puede probar lo siguiente:

[Llamar a una API web CORS desde una SPA >>](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0128_2016-->