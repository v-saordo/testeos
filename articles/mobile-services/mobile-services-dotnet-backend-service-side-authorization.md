<properties
	pageTitle="Autorización en el servicio de usuarios en un servicio móvil back-end de .NET | Microsoft Azure"
	description="Obtenga información sobre cómo restringir el acceso para autorizar a los usuarios en un servicio móvil de back-end de .NET"
	services="mobile-services"
	documentationCenter="windows"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.topic="article"
	ms.devlang="dotnet"
	ms.date="02/07/2016"
	ms.author="krisragh"/>

# Autorización en el servicio de usuarios en Servicios móviles

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Any | .NET)](mobile-services-dotnet-backend-service-side-authorization.md)
- [(Any | Javascript)](mobile-services-javascript-backend-service-side-authorization.md)

En este tema se muestra cómo usar la lógica del lado servidor para autorizar a los usuarios. En este tutorial, modificará los controladores de tabla, filtrará consultas según los identificadores de usuario y proporcionará a los usuarios acceso solamente a sus propios datos. Filtrar los resultados de consulta de un usuario por el identificador de usuario es la forma más básica de autorización. Según cada escenario específico, también conviene crear tablas de usuarios o de roles para realizar un seguimiento de la información de autorización de usuario más detallada, como los extremos a los que un usuario determinado tiene permiso de acceso.

Este tutorial se basa en el Inicio rápido de Servicios móviles y en el tutorial [Agregar autenticación a la aplicación de Servicios móviles existente]. Complete primero [Agregar autenticación a la aplicación de Servicios móviles existente].

## <a name="register-scripts"></a>Modificación de los métodos de acceso a datos

1. En Visual Studio, abra el proyecto móvil, expanda la carpeta DataObjects y abra **TodoItem.cs**. La clase **TodoItem** define el objeto de datos y tiene que agregar una propiedad **UserId** que usar para el filtrado. Agregue la nueva propiedad UserId siguiente a la clase **TodoItem**:

		public string UserId { get; set; }

	>[AZURE.NOTE] Para realizar este cambio en el modelo de datos y mantener los datos existentes en la base de datos, debe usar [Migraciones de Code First](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

2. En Visual Studio, expanda la carpeta Controladores, abra **TodoItemController.cs** y agregue la siguiente instrucción using:

		using Microsoft.WindowsAzure.Mobile.Service.Security;

3. Busque el método **PostTodoItem** y agregue el código siguiente al principio del método.

		// Get the logged in user
		var currentUser = User as ServiceUser;

		// Set the user ID on the item
		item.UserId = currentUser.Id;

	Este código agrega el identificador de usuario del usuario autenticado al elemento antes de que este se inserte en la tabla TodoItem.

3. Busque el método **GetAllTodoItems** y reemplace la instrucción **return** existente por la siguiente línea de código:

		// Get the logged in user
		var currentUser = User as ServiceUser;

		return Query().Where(todo => todo.UserId == currentUser.Id);

	Esta consulta filtra los objetos TodoItem devueltos de manera que cada usuario solo recibe los elementos que inserte.

4. Vuelva a publicar el proyecto de servicio móvil en Azure.


## <a name="test-app"></a>Prueba de la aplicación

1. Observe que cuando ejecute la aplicación del lado cliente, aunque ya existan elementos en la base de datos de tutoriales anteriores, no se devuelven elementos. Esto ocurre porque se insertaron elementos anteriores sin la columna Id. de usuario y ahora cuentan con valores nulos.

2. Si dispone de cuentas de inicio de sesión adicionales, compruebe que los usuarios puedan ver solamente sus propios datos si cierran y eliminan la aplicación, y luego la vuelven a ejecutar. Cuando aparezca el cuadro de diálogo de credenciales de inicio de sesión, escriba otro inicio de sesión y compruebe que los elementos especificados no se muestran en el inicio de sesión anterior.



<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]: #next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Agregar autenticación a la aplicación de Servicios móviles existente]: mobile-services-dotnet-backend-ios-get-started-users.md

<!---HONumber=AcomDC_0211_2016-->