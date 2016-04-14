<properties 
	pageTitle="Uso de Twilio para voz, VoIP y mensajería SMS en Azure" 
	description="Aprenda a realizar llamadas telefónicas y a enviar mensajes SMS con el servicio de la API de Twilio en Azure. Ejemplos de código escritos en Node.js." 
	services="" 
	documentationCenter="nodejs" 
	authors="devinrader" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="wpickett"/>


# Uso de Twilio para voz, VoIP y mensajería SMS en Azure

Esta guía demuestra cómo crear aplicaciones que se comunican con Twilio y Node.js en Azure.

<a id="whatis"/>
## ¿Qué es Twilio?

Twilio es una plataforma de API que hace que los desarrolladores puedan hacer y recibir llamadas telefónicas, enviar y recibir mensajes de texto e insertar llamadas de VoIP en aplicaciones móviles nativas o basadas en explorador de manera sencilla. Revisemos brevemente su funcionamiento antes de analizar los detalles.

### Recepción de llamadas y mensajes de texto

Twilio permite que los desarrolladores [compren números de teléfono programables][purchase_phone] que se pueden usar tanto para enviar y recibir llamadas como para mensajes de texto. Cuando un número de Twilio recibe una llamada o un texto entrante, Twilio le enviará a la aplicación web una solicitud HTTP, POST o GET, pidiéndole instrucciones sobre cómo controlar la llamada o el texto. El servidor responderá la solicitud HTTP de Twilio con [TwiML][twiml], un conjunto simple de etiquetas XML que contienen instrucciones sobre cómo controlar una llamada o texto. Veremos ejemplos de TwiML en un momento.

### Realización de llamadas y envío de mensajes de texto

Al realizar solicitudes HTTP a la API del servicio web de Twilio, los desarrolladores pueden enviar mensajes de texto o iniciar llamadas telefónicas salientes. En el caso de las llamadas salientes, el desarrollador también debe especificar una dirección URL que devuelve instrucciones de TwiML sobre cómo controlar una llamada saliente una vez conectada.

### Inserción de funcionalidades de VoIP en código de interfaz de usuario (JavaScript, iOS o Android)

Twilio brinda un SDK de cliente que puede transformar cualquier explorador web de escritorio, aplicación iOS o aplicación Android en un teléfono de VoIP. En este artículo, nos centraremos en cómo usar la llamada de VoIP en el explorador. Además del SDK de JavaScript de Twilio que se ejecuta en el explorador, se debe usar una aplicación de servidor (nuestra aplicación Node.js) para emitir un "token de funcionalidad" al cliente de JavaScript. Lea más sobre el uso de VoIP con node.js en el [blog de desarrolladores de Twilio][voipnode].

<a id="signup"/>
## Suscripción en Twilio (con descuento de Microsoft)

Antes de usar servicios de Twilio, debe [suscribirse para obtener una cuenta][signup]. Los clientes de Microsoft Azure obtendrán un descuento especial. [Asegúrese de suscribirse aquí][signup]

<a id="azuresite"/>
## Creación e implementación de un sitio web de Azure para Node.js

A continuación, deberá crear un sitio web de Node.js para que se ejecute en Azure. [La documentación oficial para hacerlo está aquí][azure_new_site]. En un alto nivel, hará lo siguiente:

* Suscripción para obtener una cuenta de Azure, si todavía no tiene una
* Uso de la consola de administración de Azure para crear un sitio web
* Incorporación de compatibilidad con control de código fuente (supondremos que usó Git)
* Creación de un archivo `server.js` con una simple aplicación web node.js
* Implementación de esta aplicación simple en Azure

<a id="twiliomodule"/>
## Configuración del módulo de Twilio

A continuación, comenzaremos a escribir una aplicación simple de Node.js que usa la API de Twilio. Antes de comenzar, necesitamos configurar nuestras credenciales para la cuenta de Twilio.

### Configuración de credenciales de Twilio en variables de entorno del sistema

A fin de realizar solicitudes autenticadas contra el back-end de Twilio, necesitamos el token de autenticación y el SID de nuestra cuenta, que funciona como nombre de usuario y contraseña definidos para la cuenta de Twilio. La forma más segura de configurarlos para su uso con el módulo de nodo en Azure es a través de variables de entorno del sistema, que puede definir directamente en la consola de administración de Azure.

Seleccione el sitio web de Node.js y haga clic en el vínculo "CONFIGURAR". Si se desplaza un poco hacia abajo, verá un área en la que puede definir las propiedades de configuración para la aplicación. Escriba las credenciales de su cuenta de Twilio ([las encontrará en el panel de Twilio][twilio_dashboard]) tal y como se muestra; asegúrese de llamarlas "TWILIO\_ACCOUNT\_SID" y "TWILIO\_AUTH\_TOKEN", respectivamente:

![Consola de administración de Azure][azure-admin-console]

Una vez que ha configurado estas variables, reinicie la aplicación en la consola de Azure.

### Declaración del módulo de Twilio en package.json

A continuación, debemos crear un archivo package.json para administrar nuestras dependencias de módulo de nodo vía [npm]. En el mismo nivel del archivo "server.js" que creó en el tutorial de Azure/Node.js, cree un archivo llamado "package.json". Dentro de este archivo coloque lo siguiente:

  { "name": "application-name", "version": "0.0.1", "private": true, "scripts": { "start": "node server" }, "dependencies": { "express": "3.1.0", "ejs": "*", "twilio":"*" } }

Haciendo esto, el módulo de Twilio se declara como una dependencia, al igual que el popular [express web framework][express] y el motor de plantilla EJS. Ahora ya estamos: comencemos a escribir código.

<a id="makecall"/>
## Realización de una llamada saliente

Creemos un formulario simple que realizará una llamada a un número de nuestra elección. Abra server.js y escriba el siguiente código. Observe que donde dice "CHANGE\_ME" debe poner el nombre del sitio web de Azure:

    // Module dependencies
    var express = require('express'), 
      path = require('path'), 
      http = require('http'), 
      twilio = require('twilio');

    // Create Express web application
    var app = express();

    // Express configuration
    app.configure(function(){
      app.set('port', process.env.PORT || 3000);
      app.set('views', __dirname + '/views');
      app.set('view engine', 'ejs');
      app.use(express.favicon());
      app.use(express.logger('dev'));
      app.use(express.bodyParser());
      app.use(express.methodOverride());
      app.use(app.router);
      app.use(express.static(path.join(__dirname, 'public')));
    });
    app.configure('development', function(){
      app.use(express.errorHandler());
    });

    // Render an HTML user interface for the application's home page
    app.get('/', function(request, response) {
      response.render('index');
    });

    // Handle the form POST to place a call
    app.post('/call', function(request, response) {
      var client = twilio();
      client.makeCall({
          // make a call to this number
          to:request.body.number,

          // Change to a Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // A URL in our app which generates TwiML
          // Change "CHANGE_ME" to your app's name
          url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });
    });

    // Generate TwiML to handle an outbound call
    app.post('/outbound_call', function(request, response) {
      var twiml = new twilio.TwimlResponse();

      // Say a message to the call's receiver 
      twiml.say('hello - thanks for checking out Twilio and Azure', {
          voice:'woman'
      });

      response.set('Content-Type', 'text/xml');
      response.send(twiml.toString());
    });

    // Start server
    http.createServer(app).listen(app.get('port'), function(){
      console.log("Express server listening on port " + app.get('port'));
    });

A continuación, crear un directorio llamado "views"; dentro de este directorio, crear un archivo llamado "index.ejs" con el siguiente contenido:

    <!DOCTYPE html>
    <html>
    <head>
      <title>Twilio Test</title>
      <style>
        input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
      </style>
    </head>
    <body>
      <h1>Twilio Test</h1>
      <form action="/call" method="POST">
          <input placeholder="Enter a phone number" name="number"/>
          <br/>
          <input type="submit" value="Call the number above"/>
      </form>
    </body>
    </html>

Ahora, implemente el sitio web en Azure y abra su página principal. Debe poder escribir el número de teléfono en el campo de texto y recibir una llamada desde el número de Twilio.

<a id="sendmessage"/>
## Envío de un mensaje SMS

Ahora, configuremos una lógica de control de formulario e interfaz de usuario para enviar un mensaje de texto. Abra "server.js" y agregue el siguiente código después de la última llamada a "app.post":

    app.post('/sms', function(request, response) {
      var client = twilio();
      client.sendSms({
          // send a text to this number
          to:request.body.number,

          // A Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // The body of the text message
          body: request.body.message
          
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });
    });

En "views/index.ejs", agregue otro formulario debajo del primero para enviar un número y un mensaje de texto:

    <form action="/sms" method="POST">
      <input placeholder="Enter a phone number" name="number"/>
      <br/>
      <input placeholder="Enter a message to send" name="message"/>
      <br/>
      <input type="submit" value="Send text to the number above"/>
    </form>

Vuelva a implementar la aplicación en Azure y ahora debiera poder enviar ese formulario y enviarse usted mismo (o a cualquiera de sus amigos más cercanos) un mensaje de texto.

<a id="nextsteps"/>
## Pasos siguientes

Ha aprendido los aspectos básicos relacionados con el uso de Node.js y Twilio para crear aplicaciones que se comuniquen. Pero estos ejemplos son solo una muestra muy pequeña de lo que es posible hacer con Twilio y Node.js. Si desea obtener más información sobre el uso de Twilio con Node.js, revise los siguientes recursos:

* [Documentos oficiales de módulo][docs]
* [Tutorial de VoIP con aplicaciones node.js][voipnode]
* [Votr: una aplicación de votación en tiempo real por medio de SMS con node.js y CouchDB (tres partes)][votr]
* [Programación por parejas en el explorador con node.js][pair]

Esperamos que disfrute del acceso a Node.js y Twilio en Azure.

[purchase_phone]: https://www.twilio.com/user/account/phone-numbers/available/local
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: http://ahoy.twilio.com/azure
[azure_new_site]: /app-service-web/web-sites-nodejs-develop-deploy-mac.md
[twilio_dashboard]: https://www.twilio.com/user/account
[npm]: http://npmjs.org
[express]: http://expressjs.com
[voipnode]: http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: http://twilio.github.io/twilio-node/
[votr]: http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png

<!---HONumber=AcomDC_0302_2016-->