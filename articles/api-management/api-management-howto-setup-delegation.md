<properties 
	pageTitle="Delegación de registros de usuario y suscripciones a producto" 
	description="Obtenga información acerca de cómo delegar el registro de usuario y la suscripción de producto un tercero en la administración de la API de Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="antonba" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/03/2015" 
	ms.author="antonba"/>

# Delegación de registros de usuario y suscripciones a producto

La delegación le permite usar su sitio web actual para controlar el inicio de sesión y la suscripción de los desarrolladores, así como sus suscripciones a productos, en contraposición al uso de la funcionalidad integrada en el portal para desarrolladores. Esto habilita su sitio web como propietario de los datos de usuario para poder realizar la validación de estos pasos de forma personalizada.

## <a name="delegate-signin-up"> </a>Delegación de inicios de sesión y suscripciones de desarrolladores

Para delegar el inicio de sesión y la suscripción de los desarrolladores a su sitio web actual, deberá crear un extremo especial de delegación en el sitio que funcione como punto de entrada de cualquier solicitud de este tipo que se inicie en el portal para desarrolladores de Administración de API.

El flujo de trabajo final será el siguiente:

1. El desarrollador hace clic en el enlace de inicio de sesión o de suscripción que se encuentra en el portal para desarrolladores de Administración de API.
2. El explorador se redirige al extremo de delegación.
3. Como respuesta, el extremo de delegación se redirige a la interfaz de usuario o la presenta para pedir al usuario que inicie sesión o se suscriba.
4. Una vez conseguido, se redirige de nuevo al usuario a la página del portal para desarrolladores de Administración de API de la que partió.


Para empezar, configuremos primero Administración de API para que dirija las solicitudes a través del extremo de delegación. En el portal para editores de Administración de API, haga clic en **Seguridad** y, a continuación, haga clic en la pestaña **Delegación**. Haga clic en la casilla para activar "Delegar inicio de sesión y suscripción".

![Delegation page][api-management-delegation-signin-up]

* Determine cuál será la dirección URL del extremo especial de delegación y escríbala en el campo **Dirección URL del extremo de delegación**. 

* En el campo **Clave de autenticación de delegación**, escriba el secreto que se usará para procesar una firma suministrada para su comprobación con objeto de garantizar que la solicitud procede efectivamente de Administración de API de Azure. Puede hacer clic en el botón **generar** para que Administración de API genere de forma aleatoria una clave en su lugar.

Ahora debe crear el **extremo de delegación**. Este tiene que realizar varias acciones:

1. Recibir una solicitud de la forma siguiente:

	> *http://www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL del origen page}&salt={string}&sig={string}*

	Los parámetros de consulta para el caso de inicio de sesión / suscripción:- **operation**: identifica qué tipo de solicitud de delegación es ; solo puede ser **SignIn** en este caso - **returnUrl**: la dirección URL de la página donde el usuario hizo clic en un vínculo de inicio de sesión o de suscripción - **salt**: una cadena salt especial que se utiliza para calcular el hash de seguridad - **sig**: un hash de seguridad procesado para utilizarlo en comparación con su propio hash procesado

2. Compruebe que la solicitud procede de Administración de API de Azure (opcional, pero especialmente recomendado por motivos de seguridad).

	* Procese un hash HMAC-SHA512 de una cadena según los parámetros de consulta **returnUrl** y **salt** ([se proporciona código de ejemplo a continuación]):
        > HMAC(**salt** + '\\n' + **returnUrl**)
		 
	* Compare el hash procesado anteriormente con el valor del parámetro de consulta **sig**. Si los dos hashes coinciden, vaya a paso siguiente; de lo contrario, deniegue la solicitud.

2. Compruebe que ha recibido una solicitud para inicio de sesión/suscripción: el parámetro de consulta **operation** se establecerá en "**SignIn**".

3. Presente al usuario la interfaz de usuario para que inicie sesión o se suscriba.

4. Si el usuario se suscribe, hay que crear la cuenta correspondiente en Administración de API. [Cree un usuario] con la API de REST de Administración de API. Al hacerlo, asegúrese de que el identificador de usuario se establece en el mismo que existe en su almacén de usuario o en un identificador al que pueda realizar el seguimiento.

5. Cuando el usuario se autentique correctamente:

	* [solicite un token de inicio de sesión único (SSO)] a través de la API de REST de Administración de API

	* anexe un parámetro de consulta returnUrl a la URL de SSO que se recibió de la llamada de API anterior:
		> p. ej. https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url

	* redirija al usuario a la URL producida anteriormente

Además de la operación **SignIn**, también puede realizar la administración de cuentas siguiendo los pasos anteriores y utilizando una de las siguientes operaciones.

-	**ChangePassword**
-	**ChangeProfile**
-	**CloseAccount**

Debe pasar los siguientes parámetros de consulta para las operaciones de administración de cuenta.

-	**operation**: identifica qué tipo de solicitud de delegación es (ChangePassword, ChangeProfile o CloseAccount)
-	**userId**: el identificador de usuario de la cuenta para administrar
-	**salt**: una cadena salt especial que se usa para procesar un hash de seguridad
-	**sig**: un hash de seguridad procesado que se comparará con su propio hash procesado

## <a name="delegate-product-subscription"> </a>Delegación de suscripciones a productos

La delegación de una suscripción a productos funciona de forma similar a la delegación de inicio de sesión y suscripción de usuario. El flujo de trabajo final sería el siguiente:

1. El desarrollador selecciona un producto en el portal para desarrolladores de Administración de API y hace clic en el botón Suscribir.
2. El explorador se redirige al extremo de delegación.
3. El extremo de delegación realiza los pasos necesarios para la suscripción al producto: esto depende de usted, y puede que implique la redirección a otra página para solicitar información de facturación, la formulación de otras preguntas o simplemente el almacenamiento de la información sin que se requiera ninguna acción del usuario.


Para habilitar la funcionalidad, en la página **Delegación**, haga clic en **Delegar suscripción de productos**.

A continuación, asegúrese de que el extremo de delegación realiza las siguientes acciones:


1. Recibir una solicitud de la forma siguiente:

	> *http://www.yourwebsite.com/apimdelegation?operation={operation}&productId={product para suscribirse a}&userId={user making request}&salt={string}&sig={string}* {cadena}

	Parámetros de consulta para el caso de la suscripción de producto:- **operation**: identifica qué tipo de solicitud de delegación es. Para las solicitudes de suscripción de producto las opciones válidas son:-"Subscribe": una solicitud para suscribir al usuario a un determinado producto con el identificador proporcionado (ver a continuación) - "Unsubscribe": una solicitud para cancelar la suscripción de un usuario a un producto - "Renew": una solicitud para renovar una suscripción (por ejemplo, que van a caducar) - **productId**: el identificador del producto al que el usuario ha solicitado suscribirse - **userId**: el identificador del usuario para el que se realiza la solicitud - **salt**: una cadena salt especial que se utiliza para procesar el hash de seguridad - **sig**: un hash de seguridad procesado para utilizarlo en comparación con su propio hash procesado


2. Compruebe que la solicitud procede de Administración de API de Azure (opcional, pero especialmente recomendado por motivos de seguridad).

	* Procesar un hash HMAC-SHA512 de una cadena en función de los parámetros de consulta **productId**, **userId** y **salt**:
		> HMAC(**salt** + '\\n' + **productId** + '\\n' + **userId**)
		 
	* Compare el hash procesado anteriormente con el valor del parámetro de consulta **sig**. Si los dos hashes coinciden, vaya a paso siguiente; de lo contrario, deniegue la solicitud.
	
3. Realice cualquier procesamiento de la suscripción a producto en función del tipo de operación solicitada en **operation**; por ejemplo, facturación, preguntas adicionales, etc.

4. Tras la correcta suscripción del usuario al producto por su parte, suscriba al usuario al producto de Administración de API [llamando a la API de REST para la suscripción del producto].

## <a name="delegate-example-code"> </a> Ejemplo de código ##

Estos ejemplos de código muestran cómo aprovechar la *clave de validación de delegación*, que se establece en la pantalla de delegación del portal del publicador para crear un HMAC que se utiliza para validar la firma, probando la validez del valor returnUrl que se ha pasado. El mismo código funciona para productId y userId con pequeñas modificaciones.

**Código C# para generar el hash de returnUrl**

    using System.Security.Cryptography;

    string key = "delegation validation key";
    string returnUrl = "returnUrl query parameter";
    string salt = "salt query parameter";
    string signature;
    using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
    {
        signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
        // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
        // compare signature to sig query parameter
    }


**Código NodeJS para generar el hash de returnUrl**

	var crypto = require('crypto');
	
	var key = 'delegation validation key'; 
	var returnUrl = 'returnUrl query parameter';
	var salt = 'salt query parameter';
	
	var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
	var digest = hmac.update(salt + '\n' + returnUrl).digest();
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
	
	var signature = digest.toString('base64');

## Pasos siguientes

Para obtener más información acerca de la delegación, vea el siguiente vídeo.

> [AZURE.VIDEO delegating-user-authentication-and-product-subscription-to-a-3rd-party-site]

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[solicite un token de inicio de sesión único (SSO)]: http://go.microsoft.com/fwlink/?LinkId=507409
[Cree un usuario]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
[llamando a la API de REST para la suscripción del producto]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
[Next steps]: #next-steps
[se proporciona código de ejemplo a continuación]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png

<!---HONumber=AcomDC_1210_2015-->