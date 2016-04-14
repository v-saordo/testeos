<properties
	pageTitle="Autorización en el servicio de usuarios en un servicio móvil back-end de JavaSscript | Microsoft Azure"
	description="Obtenga información acerca de cómo autorizar a los usuarios en el back-end de JavaScript de Servicios móviles de Azure"
	services="mobile-services"
	documentationCenter=""
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.topic="article"
	ms.devlang="javascript"
	ms.date="11/30/2015"
	ms.author="krisragh"/>

# Autorización en el servicio de usuarios en Servicios móviles

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Any | .NET)](mobile-services-dotnet-backend-service-side-authorization.md)
- [(Any | Javascript)](mobile-services-javascript-backend-service-side-authorization.md)

En este tema se muestra cómo usar scripts del lado servidor para autorizar a los usuarios. En este tutorial, registrará scripts con Servicios móviles de Azure, filtrará consultas según el identificador de usuario y proporcionará a los usuarios acceso solamente a sus propios datos. Filtrar los resultados de consulta de un usuario por el identificador de usuario es la forma más básica de autorización. Según cada escenario específico, también conviene crear tablas de usuarios o de roles para realizar un seguimiento de la información de autorización de usuario más detallada, como los extremos a los que un usuario determinado tiene permiso de acceso.

Este tutorial se basa en el Inicio rápido de Servicios móviles y en el tutorial [Agregar autenticación a la aplicación de Servicios móviles existente]. Complete primero [Agregar autenticación a la aplicación de Servicios móviles existente].

## <a name="register-scripts"></a>Registro de scripts

1. Inicie sesión en el [Portal de Azure clásico], haga clic en **Servicios móviles** y luego en su servicio móvil. Haga clic en la pestaña **Datos** y luego en la tabla **TodoItem**.

2. Haga clic en **Script**, seleccione la operación **Insertar**, sustituya el script existente por la siguiente función y haga clic en **Guardar**.

        function insert(item, user, request) {
          item.userId = user.userId;
          request.execute();
        }

	Este script agrega el identificador de usuario del usuario autenticado al elemento antes de la inserción.

    >[AZURE.NOTE]Asegúrese de que el [esquema dinámico](https://msdn.microsoft.com/library/azure/jj193175.aspx) esté habilitado. De lo contrario, la columna *userId* no se agrega automáticamente. Esta configuración está habilitada de forma predeterminada para un nuevo servicio móvil.

3. Del mismo modo, reemplace la operación **Read** existente con la siguiente función. Este script filtra los objetos TodoItem devueltos de manera que un usuario solo recibe los elementos que inserte.

        function read(query, user, request) {
           query.where({ userId: user.userId });
           request.execute();
        }

## <a name="test-app"></a>Prueba de la aplicación

1. Observe que cuando ejecute la aplicación del lado cliente, aunque ya existan elementos en la tabla _TodoItem_ de tutoriales anteriores, no se devuelven elementos. Esto ocurre porque se insertaron elementos anteriores sin la columna Id. de usuario y ahora cuentan con valores nulos. Compruebe que los elementos recién agregados tienen un valor userId asociado en la tabla _TodoItem_.

2. Si dispone de cuentas de inicio de sesión adicionales, compruebe que los usuarios puedan ver solamente sus propios datos si cierran y eliminan la aplicación, y luego la vuelven a ejecutar. Cuando aparezca el cuadro de diálogo de credenciales de inicio de sesión, escriba otro inicio de sesión y compruebe que los elementos especificados no se muestran en el inicio de sesión anterior.

<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->

[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/p/?LinkID=257677
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Agregar autenticación a la aplicación de Servicios móviles existente]: /develop/mobile/tutorials/get-started-with-users-ios

[Portal de Azure clásico]: https://manage.windowsazure.com/
 

<!---HONumber=AcomDC_1210_2015-->