<properties 
	pageTitle="Uso de colas del Bus de servicio en Node.js | Microsoft Azure" 
	description="Obtenga información sobre cómo usar las colas del Bus de servicio en Azure desde una aplicación Node.js." 
	services="service-bus" 
	documentationCenter="nodejs" 
	authors="sethmanheim" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="01/26/2016" 
	ms.author="sethm"/>

# Utilización de las colas del Bus de servicio

[AZURE.INCLUDE [servicio de bus de selector de colas](../../includes/service-bus-selector-queues.md)]

Este artículo describe cómo usar las colas del Bus de servicio. Los ejemplos están escritos en JavaScript y usan el módulo Node.js de Azure. Entre los escenarios proporcionados se incluyen los siguientes: **creación de colas**, **envío y recepción de mensajes** y **eliminación de colas**. Para obtener más información acerca de las colas, consulte la sección [Pasos siguientes][].

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## Creación de una aplicación Node.js

Cree una aplicación Node.js vacía. Para obtener instrucciones acerca de cómo crear una aplicación Node.js, consulte [Creación e implementación de una aplicación Node.js en un sitio web de Azure][] o [Servicio en la nube Node.js][] (con Windows PowerShell).

## Configuración de la aplicación para usar el Bus de servicio

Para utilizar el Bus de servicio de Azure, descargue y use el paquete Azure para Node.js. Este paquete incluye un conjunto de bibliotecas que se comunican con los servicios REST del Bus de servicio.

### Uso del Administrador de paquetes para Node (NPM) para obtener el paquete

1. Use la ventana de comandos de **Windows PowerShell para Node.js** para navegar a la carpeta **c:\\node\\sbqueues\\WebRole1** en la que ha creado la aplicación de ejemplo.

2. Escriba **npm install azure** en la ventana de comandos, cuyo resultado debe ser similar al siguiente:

	```
	azure@0.7.5 node_modules\azure
		├── dateformat@1.0.2-1.2.3
		├── xmlbuilder@0.4.2
		├── node-uuid@1.2.0
		├── mime@1.2.9
		├── underscore@1.4.4
		├── validator@1.1.1
		├── tunnel@0.0.2
		├── wns@0.5.3
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
	```

3. Puede ejecutar manualmente el comando **ls** para comprobar si se ha creado la carpeta **node\_modules**. Dentro de esa carpeta, busque el paquete **azure**, que contiene las bibliotecas necesarias para obtener acceso a las colas del Bus de servicio.

### Importación del módulo

Utilizando el Bloc de notas u otro editor de texto, agregue el código siguiente en la parte superior del archivo **server.js** de la aplicación:

```
var azure = require('azure');
```

### Configuración de una conexión del Bus de servicio de Azure

El módulo Azure leerá las variables de entorno AZURE\_SERVICEBUS\_NAMESPACE y AZURE\_SERVICEBUS\_ACCESS\_KEY para obtener la información necesaria para conectarse al Bus de servicio. Si no se configuran estas variables de entorno, debe especificar la información de la cuenta al llamar a **createServiceBusService**.

Para ver un ejemplo de cómo configurar las variables de entorno en un archivo de configuración para un servicio de nube de Azure, consulte [Servicio de nube de Node.js con almacenamiento][].

Para ver un ejemplo de configuración de las variables de entorno en el [Portal de Azure clásico][] para un sitio web de Azure, consulte [Aplicación web Node.js con almacenamiento][].

## Creación de una cola

El objeto **ServiceBusService** le permite trabajar con colas de Bus de servicio. El siguiente código crea un objeto **ServiceBusService**. Agréguelo cerca de la parte superior del archivo **server.js**, tras la instrucción para importar el módulo Azure:

```
var serviceBusService = azure.createServiceBusService();
```

Al llamar a **createQueueIfNotExists** en el objeto **ServiceBusService**, se obtiene la cola especificada (si existe) o se crea una nueva con el nombre especificado. El código siguiente usa **createQueueIfNotExists** para crear una cola llamada `myqueue` o conectarse a ella:

```
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

**createServiceBusService** también admite opciones adicionales, que permiten sobrescribir la configuración de cola predeterminada, como el tiempo que dura la transmisión de un mensaje o el tamaño máximo de la cola. En el siguiente ejemplo se establece el tamaño máximo de las colas en 5 GB y el valor del período de vida (TTL) en 1 minuto:

```
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### Filtros

Las operaciones de filtrado opcionales pueden aplicarse a las tareas realizadas utilizando **ServiceBusService**. Las operaciones de filtrado pueden incluir registros, reintentos automáticos, etc. Los filtros son objetos que implementan un método con la firma:

```
function handle (requestOptions, next)
```

Después de realizar el preprocesamiento en las opciones de solicitud, el método tiene que llamar a `next`, pasando una devolución de llamada con la firma siguiente:

```
function (returnObject, finalCallback, next)
```

En esta devolución de llamada y después de procesar **returnObject** (la respuesta de la solicitud al servidor), la devolución de llamada tiene que invocar a `next`, si existe, para continuar procesando otros filtros, o bien simplemente invocar a `finalCallback`, que finaliza la invocación del servicio.

Se incluyen dos filtros que implementan la lógica de reintento con el SDK de Azure para Node.js: **ExponentialRetryPolicyFilter** y **LinearRetryPolicyFilter**. Con el siguiente código se crea un objeto **ServiceBusService** que utiliza el filtro **ExponentialRetryPolicyFilter**:

```
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## mensajes a una cola

Para enviar un mensaje a una cola del Bus de servicio, la aplicación debe llamar al método **sendQueueMessage** del objeto **ServiceBusService**. Los mensajes enviados a las colas del bus de servicio (y recibidos de ellas) son objetos **BrokeredMessage** y cuentan con un conjunto de propiedades estándar (como **Label** y **TimeToLive**), un diccionario que se usa para mantener las propiedades personalizadas específicas de la aplicación y un conjunto de datos arbitrarios de aplicaciones. Una aplicación puede establecer el cuerpo del mensaje pasando una cadena como el mensaje. Las propiedades estándar requeridas se rellenan con valores predeterminados.

En el ejemplo siguiente se demuestra cómo enviar un mensaje de prueba a la cola `myqueue` mediante **sendQueueMessage**:

```
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

Las colas del Bus de servicio admiten mensajes con un tamaño máximo de 256 KB (el encabezado, que incluye las propiedades estándar y personalizadas de la aplicación, puede tener como máximo un tamaño de 64 KB). No hay límite para el número de mensajes que contiene una cola, pero hay un tope para el tamaño total de los mensajes contenidos en una cola. El tamaño de la cola se define en el momento de la creación, con un límite de 5 GB. Para obtener más información sobre las cuotas, consulte [Colas de Bus de servicio y colas de Azure][].

## mensajes de una cola

Los mensajes se reciben de una cola utilizando el método **receiveQueueMessage** del objeto **ServiceBusService**. De manera predeterminada, los mensajes se eliminan de la cola una vez que se leen; sin embargo, puede leer (echar un vistazo) y bloquear los mensajes sin eliminarlos de la cola estableciendo el parámetro opcional **isPeekLock** en **true**.

El funcionamiento predeterminado por el que los mensajes se eliminan tras leerlos como parte del proceso de recepción es el modelo más sencillo y el que mejor funciona en aquellas situaciones en las que una aplicación puede tolerar que no se procese un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como el Bus de servicio habrá marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

Si el parámetro **isPeekLock** está establecido en **true**, el proceso de recepción se convierte en una operación en dos fases que hace posible admitir aplicaciones que no toleran la pérdida de mensajes. Cuando el Bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma fiable para su futuro procesamiento), completa la segunda fase del proceso de recepción llamando al método **deleteMessage** y facilitando el mensaje que se va a eliminar a modo de parámetro. El método **deleteMessage** marcará el mensaje como consumido y lo eliminará de la cola.

En el ejemplo siguiente se muestra cómo recibir y procesar mensajes mediante **receiveQueueMessage**. En primer lugar, el ejemplo recibe y elimina un mensaje, después recibe un mensaje con **isPeekLock** establecido en **true** y luego lo elimina mediante **deleteMessage**:

```
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## Actuación ante errores de la aplicación y mensajes que no se pueden leer

El Bus de servicio proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción es incapaz de procesar el mensaje, entonces puede llamar al método **unlockMessage** del objeto **ServiceBusService**. Esto hará que el Bus de servicio desbloquee el mensaje de la cola y esté disponible para que pueda volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay un tiempo de espera asociado con un mensaje bloqueado en la cola y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación sufre un error), entonces el Bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

En caso de que la aplicación sufra un error después de procesar el mensaje y antes de llamar al método **deleteMessage**, entonces el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Habitualmente se denomina **Al menos un procesamiento**, es decir, cada mensaje se procesará al menos una vez; aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces los desarrolladores de la aplicación deberían agregar lógica adicional a su aplicación para solucionar la entrega de mensajes duplicados. A menudo, esto se consigue usando la propiedad **MessageId** del mensaje, que permanecerá constante en todos los intentos de entrega.

## Pasos siguientes

Para obtener más información, consulte los recursos siguientes.

-   [Colas, temas y suscripciones][]
-   Repositorio del [SDK de Azure para Node][] en GitHub
-   [Centro para desarrolladores de Node.js](/develop/nodejs/)

  [SDK de Azure para Node]: https://github.com/Azure/azure-sdk-for-node
  [Portal de Azure clásico]: http://manage.windowsazure.com
  
  [Servicio en la nube Node.js]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Colas, temas y suscripciones]: service-bus-queues-topics-subscriptions.md
  [Creación e implementación de una aplicación Node.js en un sitio web de Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Servicio de nube de Node.js con almacenamiento]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
  [Aplicación web Node.js con almacenamiento]: ../storage/storage-nodejs-how-to-use-table-storage.md
  [Colas de Bus de servicio y colas de Azure]: service-bus-azure-and-service-bus-queues-compared-contrasted.md#capacity-and-quotas
 

<!---HONumber=AcomDC_0128_2016-->