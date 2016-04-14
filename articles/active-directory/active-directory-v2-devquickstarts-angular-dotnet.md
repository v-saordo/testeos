<properties
	pageTitle="Introducción AngularJS de Azure AD v2.0 | Microsoft Azure"
	description="Cómo crear una aplicación de una página Angular JS que inicia la sesión de los usuarios tanto con cuentas de Microsoft personales como educativas o profesionales."
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
	ms.date="02/20/2016"
	ms.author="dastrock"/>


# Agregar inicio de sesión a una aplicación de una página AngularJS (.NET)

En este artículo vamos a agregar inicio de sesión con cuentas con tecnología de Microsoft a una aplicación AngularJS mediante el punto de conexión v2.0 de Azure Active Directory. El punto de conexión v2.0 le permite realizar una sola integración en su aplicación y autenticar a los usuarios con cuentas tanto personales como educativas o profesionales.

Este ejemplo es una sencilla aplicación de lista de tareas de una sola página que almacena las tareas en una API de REST, escrita con el marco .NET 4.5 MVC y protegida con tokens de portador OAuth de Azure AD. La aplicación AngularJS usará nuestra biblioteca de autenticación JavaScript de código abierto [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js) para gestionar el proceso completo de inicio de sesión y adquirir tokens para llamar a la API de REST. Se puede aplicar el mismo patrón para autenticarse en otras API de REST, como [Microsoft Graph](https://graph.microsoft.com).

> [AZURE.NOTE]
	No todas las características y escenarios de Azure Active Directory son compatibles con el punto de conexión v2.0. Para determinar si debe usar el punto de conexión v2.0, lea acerca de las [limitaciones de v2.0](active-directory-v2-limitations.md).

## Descargar

Para comenzar, necesitará descargar e instalar Visual Studio. Luego, puede clonar o [descargar](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) una aplicación de esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet.git
```

La aplicación de esqueleto incluye todo el código reutilizable para una aplicación sencilla AngularJS, pero faltan todas las partes relacionadas con la identidad. Si no desea continuar por este camino, en su lugar puede clonar o [descargar](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet/archive/complete.zip) el ejemplo finalizado.

```
git clone https://github.com/AzureADSamples/SinglePageApp-AngularJS-DotNet.git
```

## Registrar una aplicación

En primer lugar, cree una aplicación en el [Portal de registro de aplicaciones](https://apps.dev.microsoft.com), o siga estos [pasos detallados](active-directory-v2-app-registration.md). Asegúrese de que:

- Agrega la plataforma **web** para su aplicación.
- Escribe el **URI de redireccionamiento** correcto. El valor predeterminado en este ejemplo es `https://localhost:44326/`.
- Deja la casilla **Permitir flujo implícito** habilitada. 

Anota el valor de **Id. de aplicación** asignado a su aplicación; lo necesitará pronto.

## Instalación de adal.js
Para comenzar, vaya al proyecto que ha descargado e instale adal.js. Si tiene [bower](http://bower.io/) instalado, solo puede ejecutar este comando. Para los errores de coincidencia de la versión de dependencia, elija la versión superior. ```
bower install adal-angular#experimental
```

Como alternativa, puede descargar manualmente [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal.min.js) y [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal-angular.min.js). Agregue ambos archivos al directorio `app/lib/adal-angular-experimental/dist` del proyecto `TodoSPA`.

Ahora abra el proyecto en Visual Studio y cargue adal.js al final del cuerpo de la página principal:

```html
<!--index.html-->

...

<script src="App/bower_components/dist/adal.min.js"></script>
<script src="App/bower_components/dist/adal-angular.min.js"></script>

...
```

## Configuración de la API de REST

Mientras realizamos todas estas configuraciones, vamos a poner en funcionamiento la API de REST de back-end. En la raíz del proyecto, abra `web.config` y reemplace el valor `audience`. La API de REST usará este valor para validar los tokens que recibe de la aplicación Angular en las solicitudes AJAX.

```xml
<!--web.config-->

...

    <appSettings>
        <add key="ida:Audience" value="[Your-application-id]" />
    </appSettings>
    
...
```

Ese es todo el tiempo que vamos a dedicar a hablar de cómo funciona la API de REST. No dude en echar un vistazo al código, pero si desea aprender más sobre cómo proteger las API web con Azure AD, consulte [este artículo](active-directory-v2-devquickstarts-dotnet-api.md).

## Inicio de sesión de los usuarios
Es hora de escribir algún código de identidad. Es posible que haya notado que adal.js contiene un proveedor de AngularJS, que se reproduce perfectamente con los mecanismos de enrutamiento de Angular. Empiece por agregar el módulo adal a la aplicación:

```js
// app/scripts/app.js

angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {

...
```

Ahora puede inicializar la instancia de `adalProvider` con su id. de aplicación:

```js
// app/scripts/app.js

...

adalProvider.init({
        
        // Use this value for the public instance of Azure AD
        instance: 'https://login.microsoftonline.com/', 
        
        // The 'common' endpoint is used for multi-tenant applications like this one
        tenant: 'common',
        
        // Your application id from the registration portal
        clientId: '<Your-application-id>',
        
        // If you're using IE, uncommment this line - the default HTML5 sessionStorage does not work for localhost.
        //cacheLocation: 'localStorage',
         
    }, $httpProvider);
```

Muy bien, ahora adal.js tiene toda la información que necesita para proteger su aplicación e iniciar la sesión de los usuarios. Para forzar el inicio de sesión en una determinada ruta de la aplicación, basta con una línea de código:

```js
// app/scripts/app.js

...

}).when("/TodoList", {
    controller: "todoListCtrl",
    templateUrl: "/static/views/TodoList.html",
    requireADLogin: true, // Ensures that the user must be logged in to access the route
})

...
```

Ahora, cuando un usuario hace clic en el vínculo `TodoList`, adal.js le redireccionará automáticamente a Azure AD para iniciar sesión si es necesario. También puede enviar explícitamente solicitudes de inicio y cierre de sesión mediante la invocación de adal.js en sus controladores:

```js
// app/scripts/homeCtrl.js

angular.module('todoApp')
// Load adal.js the same way for use in controllers and views   
.controller('homeCtrl', ['$scope', 'adalAuthenticationService','$location', function ($scope, adalService, $location) {
    $scope.login = function () {
        
        // Redirect the user to sign in
        adalService.login();
        
    };
    $scope.logout = function () {
        
        // Redirect the user to log out    
        adalService.logOut();
    
    };
...
```

## Visualización de la información de usuario
Ahora que el usuario ha iniciado sesión, probablemente necesitará tener acceso a los datos de autenticación de dicho usuario en la aplicación. Adal.js expone esta información en el objeto `userInfo`. Para tener acceso a este objeto en una vista, primero agregue adal.js al ámbito raíz del controlador correspondiente:

```js
// app/scripts/userDataCtrl.js

angular.module('todoApp')
// Load ADAL for use in view
.controller('userDataCtrl', ['$scope', 'adalAuthenticationService', function ($scope, adalService) {}]);
```

A continuación, puede dirigir directamente el objeto `userInfo` en la vista:

```html
<!--app/views/UserData.html-->

...

    <!--Get the user's profile information from the ADAL userInfo object-->
    <tr ng-repeat="(key, value) in userInfo.profile">
        <td>{{key}}</td>
        <td>{{value}}</td>
    </tr>
...
```

También puede usar el objeto `userInfo` para determinar si el usuario ha iniciado sesión o no.

```html
<!--index.html-->

...

    <!--Use the ADAL userInfo object to show the right login/logout button-->
    <ul class="nav navbar-nav navbar-right">
        <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
        <li><a class="btn btn-link" ng-hide="userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    </ul>
...
```

## Llamada a la API de REST
Por último, es momento de obtener algunos tokens y llamar a la API de REST para crear, leer, actualizar y eliminar tareas. Bien, ¿sabe qué? No tiene que hacer *nada*. Adal.js se encarga automáticamente de obtener, almacenar en caché y actualizar los tokens. También se encarga de adjuntar esos tokens a las solicitudes AJAX salientes que se envían a la API de REST.

¿Cómo funciona esto exactamente? Pues todo gracias a la magia de los [interceptores de AngularJS](https://docs.angularjs.org/api/ng/service/$http), que permiten a adal.js transformar los mensajes http entrantes y salientes. Además, adal.js presupone que cualquier solicitud enviada al mismo dominio que la ventana debe usar los tokens previstos para el mismo id. de aplicación que la aplicación AngularJS. Por eso hemos usado el mismo id. de aplicación tanto en la aplicación Angular como en la API de REST de NodeJS. Por supuesto, puede invalidar este comportamiento y decir a adal.js que obtenga tokens para otras API de REST si es necesario, pero en este sencillo escenario estos son los valores predeterminados.

Este es un fragmento de código que muestra lo fácil que es enviar solicitudes con tokens de portador de Azure AD:

```js
// app/scripts/todoListSvc.js

...
return $http.get('/api/tasks');
...
```

¡Enhorabuena! La aplicación de una sola página integrada de Azure AD está terminada ahora. !Ya puede salir a recibir el aplauso del público! Con ella puede autenticar usuarios, llamar de forma segura a su API de REST de back-end mediante OpenID Connect y obtener información básica sobre el usuario. De forma predeterminada, es compatible con cualquier usuario con una cuenta de Microsoft personal, profesional o educativa desde Azure AD. Ejecute la aplicación, y en un explorador, vaya a `https://localhost:44326/`. Inicie sesión con una cuenta de Microsoft personal, profesional o educativa. Agregue tareas a la lista de tareas del usuario y cierre la sesión. Intente iniciar sesión con el otro tipo de cuenta. Si necesita un inquilino de Azure AD para crear usuarios de cuentas profesionales o educativas, [obtenga información sobre cómo conseguir uno aquí](active-directory-howto-tenant.md) (es gratuito).

Para obtener más información sobre el punto de conexión v2.0, regrese a nuestra [guía para desarrolladores de v2.0](active-directory-appmodel-v2-overview.md). Para obtener recursos adicionales, consulte:

- [Ejemplos de Azure en GitHub >>](https://github.com/Azure-Samples)
- [Azure AD en Stack Overflow >>](http://stackoverflow.com/questions/tagged/azure-active-directory)
- Documentación de Azure AD en [Azure.com >>](https://azure.microsoft.com/documentation/services/active-directory/)

<!---HONumber=AcomDC_0224_2016-->