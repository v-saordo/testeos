<properties 
	pageTitle="Incorporación de autenticación a la aplicación HTML/JavaScript | Microsoft Azure" 
	description="Obtenga información acerca de cómo utilizar Servicios móviles para autenticar usuarios de su aplicación HTML a través de una variedad de proveedores de identidad, incluidos Google, Facebook, Twitter y cuenta de Microsoft." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="11/30/2015" 
	ms.author="glenga"/>

# Incorporación de autenticación a la aplicación de Servicios móviles 

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

En este tema se muestra cómo autenticar usuarios en Servicios móviles de Azure desde una aplicación HTML, incluidas aplicaciones PhoneGap o Cordova. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que Servicios móviles haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles].

##<a name="register"></a>Registro de la aplicación para la autenticación y configuración de Servicios móviles

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]


3. En el directorio de aplicaciones, inicie uno de los archivos de comandos siguientes desde la subcarpeta **server**.

	+ **launch-windows** (equipos con Windows) 
	+ **launch-mac.command** (equipos con Mac OS X)
	+ **launch-linux.sh** (equipos con Linux)

	>[AZURE.NOTE]En un equipo con Windows, escriba `R` cuando PowerShell le pida que confirme que desea ejecutar el script. Su explorador web podría advertirle de no ejecutar el script porque se ha descargado de Internet. Cuando esto ocurra, debe solicitar que el explorador continúe con la carga del script.

	De este modo se inicia un servidor web en su equipo local para hospedar la nueva aplicación.

2. Abra la URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> en un explorador web para iniciar la aplicación.

	No se pueden cargar los datos. Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla _TodoItem_ requiere ahora autenticación.

3. (Opcional) Abra el depurador de scripts para su explorador web y vuelva a cargar la página. Compruebe que se produce un error de acceso denegado.

A continuación, actualizará la aplicación para permitir la autenticación antes de solicitar recursos del servicio móvil.

##<a name="add-authentication"></a>Incorporación de autenticación a la aplicación

>[AZURE.NOTE]Como el inicio de sesión se realiza en una ventana emergente, debería invocar el método **login** desde el evento Click del botón. Si no, muchos exploradores suprimirían la ventana de inicio de sesión.

1. Abra el archivo del proyecto index.html, localice el elemento H1 y agregue el siguiente fragmento de código debajo de él:

	    <div id="logged-in">
            You are logged in as <span id="login-name"></span>.
            <button id="log-out">Log out</button>
        </div>
        <div id="logged-out">
            You are not logged in.
            <button>Log in</button>
        </div>

	Esto le permite iniciar sesión en los Servicios móviles desde la página.

2. En el archivo page.js, localice la línea de código situada al final del archivo que llama a la función refreshTodoItems y reemplácela por el código siguiente:
	
		function refreshAuthDisplay() {
			var isLoggedIn = client.currentUser !== null;
			$("#logged-in").toggle(isLoggedIn);
			$("#logged-out").toggle(!isLoggedIn);

			if (isLoggedIn) {
				$("#login-name").text(client.currentUser.userId);
				refreshTodoItems();
			}
		}

		function logIn() {
			client.login("facebook").then(refreshAuthDisplay, function(error){
				alert(error);
			});
		}

		function logOut() {
			client.logout();
			refreshAuthDisplay();
			$('#summary').html('<strong>You must login to access data.</strong>');
		}

		// On page init, fetch the data and set up event handlers
		$(function () {
			refreshAuthDisplay();
			$('#summary').html('<strong>You must login to access data.</strong>');		    
			$("#logged-out button").click(logIn);
			$("#logged-in button").click(logOut);
		});

    De este modo se crea un conjunto de funciones para administrar el proceso de autenticación. El usuario se autentica mediante el inicio de sesión en Facebook. Si usa un proveedor de identidades que no sea Facebook, cambie el valor pasado al método **login** a uno de los siguientes: *microsoftaccount*, *facebook*, *twitter*, *google* o *aad*.

	>[AZURE.IMPORTANT]En una aplicación de PhoneGap, también debe agregar los siguientes complementos al proyecto: <ul><li><code>phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-device.git</code></li> <li><code>phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-inappbrowser.git</code></li></ul>

9. Vuelva al explorador en el que se ejecuta su aplicación y actualice la página.

	   Cuando haya iniciado sesión correctamente, la aplicación debe ejecutarse sin errores y debe poder consultar a Servicios móviles y realizar actualizaciones de datos.

	>[AZURE.NOTE]Si usa Internet Explorer, podría recibir el error siguiente después de iniciar sesión: <code>No se puede acceder al elemento de apertura de ventanas. Puede estar en una zona Internet Explorer diferente</code>. Esto sucede porque la ventana emergente se ejecuta en una zona de seguridad diferente (Internet) del localhost (intranet). Esto solo afecta a las aplicaciones durante el desarrollo que usan localhost. También puede abrir la pestaña **Seguridad** de **Opciones de Internet**, hacer clic en **Intranet local**, después en **Sitios** y deshabilitar **Detectar redes intranet automáticamente**. Acuérdese de volver a cambiar esta configuración cuando haya terminado la prueba.

## <a name="next-steps"> </a>Pasos siguientes

En el siguiente tutorial, [Autorización de usuarios con scripts], usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles. Encontrará más información acerca de cómo usar los [Servicios móviles con HTML/JavaScript en Referencia conceptual de Servicios móviles HTML/JavaScript]

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->

[4]: ./media/mobile-services-html-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-html-get-started-users/mobile-service-uri.png
[13]: ./media/mobile-services-html-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-html-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-html-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Introducción a los Servicios móviles]: mobile-services-html-get-started.md
[Autorización de usuarios con scripts]: mobile-services-javascript-backend-service-side-authorization.md
[Servicios móviles con HTML/JavaScript en Referencia conceptual de Servicios móviles HTML/JavaScript]: mobile-services-html-how-to-use-client-library.md
 

<!---HONumber=AcomDC_1210_2015-->