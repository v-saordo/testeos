<properties 
	pageTitle="Uso de Twilio para voz y SMS (PHP) | Microsoft Azure" 
	description="Aprenda a realizar llamadas telefónicas y a enviar mensajes SMS con el servicio de la API de Twilio en Azure. Ejemplos de código escritos en PHP." 
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

# Uso de Twilio para capacidades de voz y SMS en PHP
Esta guía describe cómo realizar tareas comunes de programación con el servicio de API de Twilio en Azure. Entre los escenarios descritos se incluyen realizar una llamada telefónica y enviar un mensaje de servicio de mensajes cortos (SMS). Para obtener más información sobre Twilio y el uso de voz y mensajes SMS en sus aplicaciones, consulte la sección [Pasos siguientes](#NextSteps).

## <a id="WhatIs"></a>¿Qué es Twilio?
Twilio está potenciando el futuro de las comunicaciones empresariales, al permitir que los desarrolladores inserten voz, VoIP y mensajería en las aplicaciones. Pueden virtualizar toda la infraestructura necesaria en un entorno global basado en la nube y exponerlo a través de la plataforma API de comunicaciones de Twilio. Las aplicaciones son sencillas de compilar y pueden escalarse. Disfrute de la flexibilidad de pagar por lo que utiliza y aproveche la confiabilidad de la nube.

**Twilio Voice** permite que sus aplicaciones realicen y reciban llamadas. **Twilio SMS** permite que su aplicación envíe y reciba mensajes de texto. **Twilio Client** permite realizar llamadas VoIP desde cualquier teléfono, tableta o explorador, y es compatible con WebRTC.

## <a id="Pricing"></a>Precios de Twilio y ofertas especiales

Los clientes de Azure reciben una [oferta especial](http://www.twilio.com/azure): 10 $ de regalo en crédito Twilio al actualizar su cuenta Twilio. Este crédito Twilio se puede aplicar a cualquier uso de Twilio (10 $ de crédito equivalen al envío de aproximadamente 1.000 mensajes SMS o recibir hasta 1.000 minutos de voz entrante, según la ubicación del número de teléfono y el destino del mensaje o de la llamada). Canjee este crédito Twilio y comience en [http://ahoy.twilio.com/azure](http://ahoy.twilio.com/azure).

Twilio es un servicio de pago por uso. No hay comisiones establecidas y puede cerrar su cuenta en cualquier momento. Puede encontrar más detalles en [Precios de Twilio][twilio_pricing].

## <a id="Concepts"></a>Conceptos
La API de Twilio es una API de RESTful que proporciona funciones de voz y SMS para las aplicaciones. Las bibliotecas de cliente están disponibles en varios idiomas; para ver una lista, consulte las [bibliotecas API de Twilio][twilio_libraries].

Aspectos fundamentales de la API de Twilio son los verbos de Twilio y el lenguaje de marcado de Twilio (TwiML).

### <a id="Verbs"></a>Verbos de Twilio
La API usa los verbos de Twilio; por ejemplo, el verbo **&lt;Say&gt;** indica a Twilio que entregue por voz un mensaje en una llamada.

A continuación se presenta una lista de verbos de Twilio. Obtenga información sobre los demás verbos y las capacidades de Twilio a través de [Documentación de lenguaje de marcado de Twilio](http://www.twilio.com/docs/api/twiml).

* **&lt;Dial&gt;**: conecta la persona que llama con otro teléfono.
* **&lt;Gather&gt;**: recopila los dígitos numéricos especificados en el teclado del teléfono.
* **&lt;Hangup&gt;**: finaliza una llamada.
* **&lt;Play&gt;**: reproduce un archivo de audio.
* **&lt;Pause&gt;**: espera en silencio una cantidad de segundos especificada.
* **&lt;Record&gt;**: graba la voz de la persona que llama y devuelve la dirección URL de un archivo que contiene la grabación.
* **&lt;Redirect&gt;**: transfiere el control de una llamada o SMS al TwiML en una URL distinta.
* **&lt;Reject&gt;**: rechaza una llamada entrante a su número de Twilio sin cobrarle.
* **&lt;Say&gt;**: convierte texto en voz para hacer una llamada.
* **&lt;Sms&gt;**: envía un mensaje SMS.

### <a id="TwiML"></a>TwiML
TwiML es un conjunto de instrucciones basadas en XML y en los verbos de Twilio que informan a Twilio sobre cómo procesar una llamada o un SMS.

Como ejemplo, el siguiente TwiML convierte el texto **Hello World** en voz.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Cuando su aplicación llama a la API de Twilio, uno de los parámetros de API es la URL que devuelve la respuesta de TwiML. Con fines de desarrollo, puede usar las URL que ofrece Twilio para proporcionar las respuestas de TwiML que usaron sus aplicaciones. También puede hospedar sus propias URL para producir las repuestas de TwiML; otra opción es usar el objeto **TwiMLResponse**.

Para obtener más información sobre los verbos de Twilio, sus atributos y TwiML, consulte [TwiML][twiml]. Para obtener información adicional sobre la API de Twilio, consulte la [API de Twilio][twilio_api].

## <a id="CreateAccount"></a>Creación de una cuenta de Twilio
Cuando esté preparado para obtener una cuenta de Twilio, regístrese en la [página de evaluación de Twilio][try_twilio]. Puede empezar con una cuenta gratuita y, posteriormente, actualizarla.

Cuando se registre para obtener una cuenta de Twilio, recibirá un identificador de cuenta y un token de autenticación. Necesitará ambos para realizar llamadas a la API de Twilio. Para evitar el acceso no autorizado a su cuenta, mantenga protegido el token de autenticación. Puede ver el identificador de cuenta y el token de autenticación en la [página de la cuenta de Twilio][twilio_account], en los campos etiquetados como **ACCOUNT SID** y **AUTH TOKEN**, respectivamente.


## <a id="create_app"></a>Creación de una aplicación PHP
Una aplicación PHP que usa el servicio Twilio y que se ejecuta en Azure no es distinta a otra aplicación PHP que use el servicio Twilio. Mientras que los servicios Twilio se basan en REST y pueden llamarse desde PHP de varias formas, este artículo se centrará en cómo usar los servicios Twilio con la [biblioteca de Twilio para PHP desde Github][twilio_php] (en inglés). Para obtener más información sobre el uso de la biblioteca de Twilio para PHP, vea [http://readthedocs.org/docs/twilio-php/en/latest/index.html][twilio_lib_docs].

Puede conseguir instrucciones detalladas para crear e implementar una aplicación de Twilio o PHP en Azure, en [Realización de una llamada telefónica con Twilio en una aplicación PHP en Azure][howto_phonecall_php].

## <a id="configure_app"></a>Configuración de la aplicación para usar bibliotecas de Twilio
Puede configurar la aplicación para que use la biblioteca de Twilio para PHP de dos formas:

1. Descargue la biblioteca de Twilio para PHP desde Github ([https://github.com/twilio/twilio-php][twilio_php]) y agregue el directorio **Services** a la aplicación.

	O

2. Instale la biblioteca de Twilio para PHP como paquete PEAR. Puede instalarse con los siguientes comandos:

		$ pear channel-discover twilio.github.com/pear
		$ pear install twilio/Services_Twilio

Una vez que haya instalado la biblioteca de Twilio para PHP, puede agregar a continuación una instrucción **require\_once** en la parte superior de los archivos PHP para hacer referencia a la biblioteca:

    	require_once 'Services/Twilio.php';

Para obtener más información, consulte [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

## <a id="howto_make_call"></a>Realización de una llamada saliente
A continuación se indica cómo realizar una llamada saliente usando la clase **Services\_Twilio**. Este código también utiliza un sitio que proporciona Twilio para devolver la respuesta de lenguaje de marcado de Twilio (TwiML). Sustituya los valores de los números de teléfono **From** y **To** y asegúrese de comprobar el número de teléfono **From** de su cuenta de Twilio antes de ejecutar el código.

	// Include the Twilio PHP library.
	require_once 'Services/Twilio.php';

	// Library version.
	$version = "2010-04-01";

	// Set your account ID and authentication token.
	$sid = "your_twilio_account_sid";
	$token = "your_twilio_authentication_token";

	// The number of the phone initiating the the call.
	$from_number = "NNNNNNNNNNN";

	// The number of the phone receiving call.
	$to_number = "NNNNNNNNNNN";

	// Use the Twilio-provided site for the TwiML response.
    $url = "http://twimlets.com/message";
	
	// The phone message text.
	$message = "Hello world.";

	// Create the call client.
	$client = new Services_Twilio($sid, $token, $version);

	//Make the call.
	try
	{
		$call = $client->account->calls->create(
			$from_number, 
			$to_number,
  			$url.'?Message='.urlencode($message)
		);
	}
	catch (Exception $e) 
	{
		echo 'Error: ' . $e->getMessage();
	}

Como se mencionó, este código usa el sitio proporcionado por Twilio para devolver la respuesta de TwiML. A parte, también puede usar su propio sitio web para proporcionar la respuesta de TwiML; si desea obtener más información, consulte [Procedimientos: Suministro de respuestas de TwiML desde su propio sitio web](#howto_provide_twiml_responses).


- **Nota:** para solucionar los errores de validación de certificados SSL, consulte [http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 


## <a id="howto_send_sms"></a>Envío de un mensaje SMS
A continuación se muestra cómo enviar un mensaje SMS con la clase **Services\_Twilio**. El número **From** lo proporciona Twilio en las cuentas de evaluación para enviar mensajes SMS. El número **To** se debe comprobar para la cuenta de Twilio antes de ejecutar el código.

	// Include the Twilio PHP library.
	require_once 'Services/Twilio.php';

	// Library version.
	$version = "2010-04-01";

	// Set your account ID and authentication token.
	$sid = "your_twilio_account_sid";
	$token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
	$to_number = "NNNNNNNNNNN";
	$message = "Hello world.";

	// Create the call client.
	$client = new Services_Twilio($sid, $token, $version);

	// Send the SMS message.
	try
	{
		$client->$client->account->messages->sendMessage($from_number, $to_number, $message);
	}
	catch (Exception $e) 
	{
		echo 'Error: ' . $e->getMessage();
	}

## <a id="howto_provide_twiml_responses"></a>Entrega de respuestas de TwiML desde su propio sitio web
Cuando la aplicación inicia una llamada a la API de Twilio, Twilio envía su solicitud a una URL que debe devolver una respuesta de TwiML. El ejemplo anterior utiliza la URL [http://twimlets.com/message][twimlet_message_url] proporcionada por Twilio. (Aunque TwiML está diseñado para su uso por Twilio, puede verlo en el explorador. Por ejemplo, haga clic en [http://twimlets.com/message][twimlet_message_url] para ver un elemento `<Response>` vacío; otro ejemplo, haga clic en [http://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] para ver un elemento `<Response>` que consta de un elemento `<Say>`).

En lugar de confiar en la URL que Twilio proporciona, puede crear su propio sitio que devuelve respuestas HTTP. Puede crear el sitio en cualquier lenguaje que devuelva respuestas XML; en este tema se asume que usará PHP para crear el TwiML.

La siguiente página PHP da como resultado una respuesta de TwiML que dice **Hello World** en la llamada.

    <?php    
		header("content-type: text/xml");    
		echo "<?xml version="1.0" encoding="UTF-8"?>\n";
	?>
	<Response>    
		<Say>Hello world.</Say>
	</Response>

Como puede ver en el ejemplo anterior, la respuesta de TwiML es sencillamente un documento XML. La biblioteca de Twilio para PHP contiene clases que generarán TwiML de manera automática. El ejemplo siguiente genera la respuesta equivalente como se ha mostrado anteriormente, pero usa la clase **Services\\_Twilio\\_Twiml** de la biblioteca de Twilio para PHP:

	require_once('Services/Twilio.php');
	
	$response = new Services_Twilio_Twiml();
	$response->say("Hello world.");
	print $response;

Para obtener más información sobre TwiML, consulte [https://www.twilio.com/docs/api/twiml][twiml_reference].

Una vez tenga configurada su página de PHP para proporcionar respuestas de TwiML, use la URL de la página PHP como la URL que se transmite al método `Services_Twilio->account->calls->create`. Por ejemplo, si tiene una aplicación web llamada **MyTwiML** implementada en un servicio hospedado de Azure y el nombre de la página de PHP es **mytwiml.php**, la dirección URL se puede transmitir a **Services\_Twilio->account->calls->create**, tal como aparece a continuación:

	require_once 'Services/Twilio.php';

	$sid = "your_twilio_account_sid";
	$token = "your_twilio_authentication_token";
	$from_number = "NNNNNNNNNNN";
	$to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

	$client = new Services_Twilio($sid, $token, "2010-04-01");

	try
	{
		$call = $client->account->calls->create(
			$from_number, 
			$to_number,
  			$url.'?Message='.urlencode($message)
		);
	}
	catch (Exception $e) 
	{
		echo 'Error: ' . $e->getMessage();
	}

Si desea obtener más información acerca de cómo usar Twilio en Azure con PHP, consulte [Realización de una llamada de teléfono con Twilio en una aplicación PHP en Azure][howto_phonecall_php].

## <a id="AdditionalServices"></a>Procedimientos: Uso de servicios Twilio adicionales
Además de los ejemplos aquí mostrados, Twilio ofrece API basadas en web que puede utilizar para aprovechar las funciones adicionales de Twilio desde su aplicación de Azure. Para obtener los detalles completos, consulte la [Documentación sobre la API de Twilio][twilio_api_documentation].

## <a id="NextSteps"></a>Pasos siguientes
Ahora que conoce los fundamentos del servicio Twilio, siga estos vínculos para obtener más información:

* [Directrices de seguridad de Twilio][twilio_security_guidelines]
* [Procedimientos y código de ejemplo de Twilio][twilio_howtos]
* [Tutoriales de inicio rápido de Twilio][twilio_quickstarts] 
* [Twilio en GitHub][twilio_on_github]
* [Contactar con el servicio técnico de Twilio][twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_php]: partner-twilio-php-make-phone-call.md
[twilio_voice_request]: https://www.twilio.com/docs/api/twiml/twilio_request
[twilio_sms_request]: https://www.twilio.com/docs/api/twiml/sms/twilio_request
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]: https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart

<!---HONumber=Oct15_HO3-->