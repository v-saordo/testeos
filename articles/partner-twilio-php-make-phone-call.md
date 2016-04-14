<properties 
	pageTitle="Realización de una llamada telefónica desde Twilio (PHP) | Microsoft Azure" 
	description="Aprenda a realizar llamadas telefónicas y a enviar mensajes SMS con el servicio de la API de Twilio en Azure. Ejemplos para la aplicación PHP." 
	documentationCenter="php" 
	services="" 
	authors="devinrader" 
	manager="twilio" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="microsofthelp@twilio.com"/>

# Realización de una llamada telefónica con Twilio en una aplicación PHP en Azure 

El siguiente ejemplo muestra cómo se puede usar Twilio para realizar una llamada desde una página web PHP hospedada en Azure. La aplicación resultante le pedirá al usuario los valores de una llamada telefónica, como se muestra en la siguiente captura de pantalla.

![Formulario de llamada de Azure con Twilio y PHP][twilio_php]

Tendrá que hacer lo siguiente para utilizar el código de este tema:

1. Adquiera una cuenta de Twilio y un token de autenticación. Para empezar con Twilio, evalúe los precios en [http://www.twilio.com/pricing][twilio_pricing]. Puede registrarse para obtener una cuenta de prueba en [https://www.twilio.com/try-twilio][try_twilio]. Para obtener información acerca de la API proporcionada por Twilio, consulte [http://www.twilio.com/api][twilio_api].
2. Obtenga la biblioteca de Twilio para PHP. Puede descargarla desde Github ([https://github.com/twilio/twilio-php][twilio_php_github]) o instalarla como paquete PEAR. Para obtener más información, consulte [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].
3. Instale el SDK de Azure para PHP. Para obtener información general sobre el SDK e instrucciones sobre cómo instalarlo, consulte [Set up the Azure SDK for PHP][setup_php_sdk] (Configuración del SDK de Azure para PHP).

## Creación de un formulario web para hacer una llamada

El código HTML siguiente muestra cómo crear una página web (**callform.html**) que recupera datos de usuarios para realizar una llamada:

    <html>
	<head>
		<title>Automated call form</title>
	</head>
	<body>
	<h1>Automated Call Form</h1>
 	<p>Fill in all fields and click <b>Make this call</b>.</p>
  	<form action="makecall.php" method="post">
   	<table>
     	<tr>
       		<td>To:</td>
       		<td><input type="text" size=50 name="callTo" value=""></td>
     	</tr>
     	<tr>
       		<td>From:</td>
       		<td><input type="text" size=50 name="callFrom" value=""></td>
     	</tr>
     	<tr>
       		<td>Call message:</td>
       		<td><input type="text" size=100 name="callText" value="Hello. This is the call text. Good bye." /></td>
     	</tr>
     	<tr>
       		<td colspan=2><input type="submit" value="Make this call"></td>
     	</tr>
   	</table>
 	</form>
 	<br/>
	</body>
	</html>

## Creación del código para realizar la llamada
El código siguiente muestra cómo crear una página web (**makecall.php**) a la que se llama cuando el usuario envía el formulario mostrado por **callform.html**. El código que se muestra a continuación crea el mensaje de llamada y genera la llamada. (Use su cuenta Twilio y token de autenticación en lugar de los valores de marcador de posición asignados a **$sid** y **$token** en el código siguiente).

    <html>
	<head><title>Making call...</title></head>
	<body>
	<p>Your call is being made.</p>

	<?php
	require_once 'Services/Twilio.php';

	$sid = "your_account_sid";
	$token = "your_authentication_token";

	$from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
	$to_number = $_POST['callTo'];
	$message = $_POST['callText'];

	$client = new Services_Twilio($sid, $token, "2010-04-01");

	$call = $client->account->calls->create(
		$from_number, 
		$to_number,
  		'http://twimlets.com/message?Message='.urlencode($message)
	);

	echo "Call status: ".$call->status."<br />";
	echo "URI resource: ".$call->uri."<br />";
	?>
	</body>
	</html>

Además de realizar la llamada, **makecall.php** muestra algunos metadatos de llamada (en la captura de pantalla siguiente se muestra un ejemplo). Para obtener información acerca de los metadatos de llamada, consulte [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties].

![Respuesta de llamada de Azure con Twilio y PHP][twilio_php_response]

## Ejecución de la aplicación
El paso siguiente consiste en implementar la aplicación en Sitios web Azure. Los artículos siguientes contienen la información necesaria para crear un sitio web e implementar el código con Git, FTP o WebMatrix (si bien no toda la información de cada uno de los artículos es relevante):

* [Creación de un sitio web PHP-MySQL en el Servicio de aplicaciones de Azure e implementación mediante Git][website-git]
* [Creación de un sitio web PHP-MySQL en el Servicio de aplicaciones de Azure e implementación mediante FTP][website-ftp]
* [Creación e implementación de un sitio web PHP-MySQL en el Servicio de aplicaciones de Azure mediante WebMatrix][website-webmatrix]

## Pasos siguientes
Este código se ha proporcionado para mostrar la funcionalidad básica usando Twilio en PHP en Azure. Antes de implementarlo en Azure en producción, es posible que desee agregar más controles de errores u otras características. Por ejemplo:

* En lugar de usar un formulario web, puede usar blobs de almacenamiento de Azure o una base de datos SQL para almacenar los números de teléfono y el texto de llamada. Para obtener información sobre el uso de blobs de almacenamiento de Azure en PHP, consulte [Uso del almacenamiento de blobs de PHP][howto_blob_storage_php]. Para obtener información sobre el uso de bases de datos SQL en PHP, consulte [Acceso a una base de datos SQL de Azure desde PHP][howto_sql_azure_php].
* El código de **makecall.php** usa una dirección URL ([http://twimlets.com/message][twimlet_message_url]) de Twilio para proporcionar una respuesta de Lenguaje de marcado de Twilio (TwiML) que indica a Twilio cómo proceder con la llamada. Por ejemplo, la TwiML que se devuelve puede contener un verbo `<Say>` que se traduce en el texto que se está hablando con el destinatario de la llamada. En lugar de usar la dirección URL proporcionada por Twilio, podría construir su propio servicio para responder a la solicitud de Twilio; para obtener más información, consulte [Uso de Twilio para capacidades de voz y SMS en PHP][howto_twilio_voice_sms_php]. Puede encontrar más información sobre TwiML en [http://www.twilio.com/docs/api/twiml][twiml], y más información sobre `<Say>` y otros verbos de Twilio en [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Lea las directrices de seguridad de Twilio en [https://www.twilio.com/docs/security][twilio_docs_security].

Para obtener información adicional acerca de Twilio, consulte [https://www.twilio.com/docs][twilio_docs].

## Otras referencias
* [Uso de Twilio para capacidades de voz y SMS en PHP](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_php]: https://github.com/twilio/twilio-php
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[setup_php_sdk]: http://azurephp.interoperabilitybridges.com/articles/setup-the-windows-azure-sdk-for-php
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: http://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: http://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[website-git]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-git/
[website-ftp]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-ftp/
[website-webmatrix]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-webmatrix/
[twilio_php_github]: https://github.com/twilio/twilio-php

<!---HONumber=Oct15_HO3-->