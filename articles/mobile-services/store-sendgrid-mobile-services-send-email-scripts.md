<properties
	pageTitle="Envío de correo electrónico con SendGrid | Microsoft Azure"
	description="Obtenga información acerca de cómo usar el servicio de SendGrid para enviar correo electrónico desde su aplicación de Servicios móviles de Azure."
	services="mobile-services"
	documentationCenter=""
	authors="Erikre"
	manager="sendgrid"
	editor=""/>


<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/30/2015" 
	ms.author="glenga"/>


# Envío de correo electrónico desde servicios móviles con SendGrid

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


En este tema se describe cómo agregar la funcionalidad de correo electrónico al servicio móvil. En este tutorial se demuestra cómo agregar scripts del lado servidor para enviar correo electrónico mediante SendGrid. Cuando haya finalizado, el servicio móvil le enviará un correo electrónico cada vez que se inserte un registro.

SendGrid es un [servicio de correo electrónico basado en la nube] que proporciona un sistema fiable de [entrega de correos electrónicos transaccional], escalabilidad y análisis en tiempo real junto con API flexibles que facilitan la integración personalizada. Para obtener más información, consulte <http://sendgrid.com>.

Este tutorial le guiará a través de estos pasos básicos para habilitar la funcionalidad de correo electrónico:

1. [Creación de una cuenta de SendGrid]
2. [Incorporación de un script para enviar correo electrónico]
3. [Inserción de datos para recibir correo electrónico]

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, debe leer [Introducción a los Servicios móviles].

## <a name="sign-up"></a>Creación de una cuenta de SendGrid

[AZURE.INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## <a name="add-script"></a>Registro de un nuevo script que envía mensajes de correo

1. Inicie sesión en el [Portal de Azure clásico], haga clic en **Servicios móviles** y luego en su servicio móvil.

2. En el Portal de Azure clásico, haga clic en la pestaña **Datos** y luego en la tabla **TodoItem**.

	![][1]

3. En **todoitem**, haga clic en la pestaña **Script** y seleccione **Insertar**.

	![][2]

	Se muestra la función que se invoca cuando se produce una inserción en la tabla **TodoItem**.

4. Reemplace la función de inserción por el código siguiente:

        var SendGrid = require('sendgrid').SendGrid;

        function insert(item, user, request) {
            request.execute({
                success: function() {
                    // After the record has been inserted, send the response immediately to the client
                    request.respond();
                    // Send the email in the background
                    sendEmail(item);
                }
            });

            function sendEmail(item) {
                var sendgrid = new SendGrid('**username**', '**password**');

                sendgrid.send({
                    to: '**email-address**',
                    from: '**from-address**',
                    subject: 'New to-do item',
                    text: 'A new to-do was added: ' + item.text
                }, function(success, message) {
                    // If the email failed to send, log it as an error so we can investigate
                    if (!success) {
                        console.error(message);
                    }
                });
            }
        }

5. Reemplace los marcadores de posición del script anterior por los valores correctos:

	- **_username_ y _password_**: las credenciales de SendGrid que identificó en [Creación de una cuenta de SendGrid].

	- **_email-address_**: la dirección a la que se envía el correo electrónico. En una aplicación en tiempo real, puede usar tablas para almacenar y recuperar direcciones de correo electrónico. Al probar la aplicación, use su propia dirección de correo electrónico.

	- **_from-address_**: la dirección de la que procede el correo electrónico. Considere usar una dirección de dominio registrada que pertenezca a su organización.

     > [AZURE.NOTE] Si no tiene un dominio registrado, puede usar en su lugar el dominio del servicio móvil, con el formato **notifications@_your-mobile-service_.azure-mobile.net*. No obstante, se ignoran los mensajes enviados al dominio del servicio móvil.

6. Haga clic en el botón **Save** (Guardar). Ahora ha configurado un script para enviar un correo electrónico cada vez que se inserta un registro en la tabla **TodoItem**.

## <a name="insert-data"></a>Inserción de datos de prueba para recibir correo electrónico

1. En el proyecto de la aplicación cliente, ejecute la aplicación de inicio rápido.

	En este tema se muestra la versión de inicio rápido de la Tienda Windows.

2. En la aplicación, escriba un texto en **Insertar in TodoItem** y haga clic en **Guardar**.

	![][3]

3. Tenga en cuenta que recibirá un correo electrónico, como el que se muestra en la notificación siguiente.

	![][4]

	Enhorabuena, ha configurado correctamente el servicio móvil para enviar correo electrónico con SendGrid.

## <a name="nextsteps"> </a>Pasos siguientes

Ahora que ya sabe lo fácil que resulta usar el servicio de correo electrónico SendGrid con Servicios móviles, siga estos vínculos para obtener más información acerca de SendGrid.

-   Documentación sobre la API de SendGrid: <https://sendgrid.com/docs>
-   Oferta especial de SendGrid para clientes de Azure: <https://sendgrid.com/windowsazure.html>

<!-- Anchors. -->
[Creación de una cuenta de SendGrid]: #sign-up
[Incorporación de un script para enviar correo electrónico]: #add-script
[Inserción de datos para recibir correo electrónico]: #insert-data

<!-- Images. -->
[1]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-portal-data-tables.png
[2]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-insert-script-push2.png
[3]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-quickstart-push1.png
[4]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-receive-email.png

<!-- URLs. -->
[Introducción a los Servicios móviles]: /develop/mobile/tutorials/get-started
[sign up page]: https://sendgrid.com/windowsazure.html
[Multiple User Credentials page]: https://sendgrid.com/credentials
[Portal de Azure clásico]: https://manage.windowsazure.com/
[servicio de correo electrónico basado en la nube]: https://sendgrid.com/email-solutions
[entrega de correos electrónicos transaccional]: https://sendgrid.com/transactional-email

<!---HONumber=AcomDC_0211_2016-->