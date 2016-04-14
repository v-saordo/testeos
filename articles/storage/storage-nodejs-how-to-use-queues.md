<properties
	pageTitle="Uso del almacenamiento de colas de Node.js | Microsoft Azure"
	description="Aprenda a utilizar el servicio Cola de Azure para crear y eliminar colas e insertar, obtener y eliminar mensajes. Ejemplos escritos en Node.js."
	services="storage"
	documentationCenter="nodejs"
	authors="rmcmurray"
	manager="wpickett"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="micurd"/>


# Uso del almacenamiento de colas de Node.js

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## Información general

Esta guía le indicará cómo actuar en situaciones habituales usando el servicio Cola de Microsoft Azure. Los ejemplos están escritos usando la API Node.js. Entre los escenarios descritos se incluyen **insertar**, **ojear**, **obtener** y **eliminar** mensajes de la cola, así como **crear y eliminar colas**.

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Creación de una aplicación Node.js

Cree una aplicación Node.js vacía. Para obtener instrucciones sobre cómo crear una aplicación Node.js, consulte [Creación de una aplicación web Node.js en el Servicio de aplicaciones de Azure], [Creación e implementación de una aplicación Node.js en un Servicio de nube de Azure] con Windows PowerShell o [Creación e implementación de una aplicación web Node.js en Azure con Web Matrix].

## Configuración de la aplicación para obtener acceso al almacenamiento

Para usar el almacenamiento de Azure necesitará el SDK de almacenamiento de Azure para Node.js, que incluye un conjunto de útiles bibliotecas que se comunican con los servicios REST de almacenamiento.

### Uso del Administrador de paquetes para Node (NPM) para obtener el paquete

1.  Utilice una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix) y vaya a la carpeta donde ha creado la aplicación de ejemplo.

2.  Escriba **npm install azure-storage** en la ventana de comandos. La salida del comando es similar al ejemplo siguiente.

		azure-storage@0.5.0 node_modules\azure-storage
		+-- extend@1.2.1
		+-- xmlbuilder@0.4.3
		+-- mime@1.2.11
		+-- node-uuid@1.4.3
		+-- validator@3.22.2
		+-- underscore@1.4.4
		+-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
		+-- xml2js@0.2.7 (sax@0.5.2)
		+-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3.  Puede ejecutar manualmente el comando **ls** para comprobar si se ha creado la carpeta **node\_modules**. Dentro de dicha carpeta, encontrará el paquete **azure-storage**, que contiene las bibliotecas necesarias para el acceso al almacenamiento.

### Importación del paquete

Con el Bloc de notas u otro editor de texto, agregue lo siguiente en la parte superior del archivo **server.js** de la aplicación en la que pretenda usar el almacenamiento:

    var azure = require('azure-storage');

## Configuración de una conexión de almacenamiento de Azure

El módulo azure leerá las variables de entorno AZURE\_STORAGE\_ACCOUNT, AZURE\_STORAGE\_ACCESS\_KEY o AZURE\_STORAGE\_CONNECTION\_STRING para obtener la información necesaria para conectarse a su cuenta de almacenamiento de Azure. Si no se configuran estas variables de entorno, debe especificar la información de la cuenta al llamar a **createQueueService**.

Para ver un ejemplo de cómo configurar las variables de entorno del [Portal de Azure](https://portal.azure.com) para un sitio web de Azure, consulte [Aplicación web de Node.js con servicio Tabla de Azure].

## Creación de una cola

El siguiente código crea un objeto **QueueService**, que le permite trabajar con colas.

    var queueSvc = azure.createQueueService();

Utilice el método **createQueueIfNotExists**, que devuelve la cola especificada si ya existe o crea una nueva cola con el nombre especificado si todavía no existe.

	queueSvc.createQueueIfNotExists('myqueue', function(error, result, response){
      if(!error){
        // Queue created or exists
	  }
	});

Si la cola se crea, `result` es verdadero. Si la cola existe, `result` es falso.

### Filtros

Las operaciones de filtrado opcionales pueden aplicarse a las tareas realizadas utilizando **QueueService**. Las operaciones de filtrado pueden incluir registros, reintentos automáticos, etc. Los filtros son objetos que implementan un método con la firma:

		function handle (requestOptions, next)

Después de realizar el preprocesamiento en las opciones de solicitud, el método tiene que llamar a "next" pasando una devolución de llamada con la firma siguiente:

		function (returnObject, finalCallback, next)

En esta devolución de llamada y después de procesar returnObject (la respuesta de la solicitud al servidor), la devolución de llamada tiene que invocar a next, si existe, para continuar procesando otros filtros, o bien simplemente invocar a finalCallback para finalizar la invocación del servicio.

Se incluyen dos filtros que implementan la lógica de reintento con el SDK de Azure para Node.js: **ExponentialRetryPolicyFilter** y **LinearRetryPolicyFilter**. Lo siguiente crea un objeto **QueueService** que usa **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var queueSvc = azure.createQueueService().withFilter(retryOperations);

## Inserción de un mensaje en una cola

Para insertar un mensaje en una cola, utilice el método **createMessage** para crear un nuevo mensaje y agregarlo a la cola.

	queueSvc.createMessage('myqueue', "Hello world!", function(error, result, response){
	  if(!error){
	    // Message inserted
	  }
	});

## Inspección del siguiente mensaje

Puede inspeccionar el mensaje situado en la parte delantera de una cola, sin quitarlo de la cola, mediante una llamada al método **peekMessages**. De forma predeterminada, **peekMessages** inspecciona un único mensaje.

	queueSvc.peekMessages('myqueue', function(error, result, response){
	  if(!error){
		// Message text is in messages[0].messagetext
	  }
	});

El `result` contiene el mensaje.

> [AZURE.NOTE] Si se usa **peekMessages** cuando no existen mensajes en la cola, no se devolverá un error, pero tampoco se devolverán mensajes.

## Extracción del siguiente mensaje de la cola

El procesamiento de un mensaje es un proceso que consta de dos etapas:

1. Extracción del mensaje de la cola.

2. Eliminación del mensaje.

Para quitar un mensaje de la cola, use **getMessages**. De esta forma los mensajes se hacen invisible en la cola, así que ningún otro cliente puede procesarlos. Después de que la aplicación haya procesado un mensaje, llame a **deleteMessage** para eliminarlo de la cola. En el siguiente ejemplo se obtiene un mensaje y luego se elimina:

	queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
	    // Message text is in messages[0].messagetext
        var message = result[0];
        queueSvc.deleteMessage('myqueue', message.messageid, message.popreceipt, function(error, response){
	      if(!error){
		    //message deleted
		  }
		});
	  }
	});

> [AZURE.NOTE] De manera predeterminada, un mensaje solo está oculto durante 30 segundos, después de lo cual es visible para otros clientes. Puede especificar un valor diferente usando `options.visibilityTimeout` con **getMessages**.

> [AZURE.NOTE]
Si usa **getMessages** cuando no existen mensajes en la cola, no se devolverá un error, pero tampoco se devolverán mensajes.

## Cambio del contenido de un mensaje en cola

Puede cambiar el contenido de un mensaje local en la cola mediante **updateMessage**. En el ejemplo siguiente se actualiza el texto de un mensaje:

    queueSvc.getMessages('myqueue', function(error, result, response){
	  if(!error){
		// Got the message
		var message = result[0];
		queueSvc.updateMessage('myqueue', message.messageid, message.popreceipt, 10, {messageText: 'new text'}, function(error, result, response){
		  if(!error){
			// Message updated successfully
		  }
		});
	  }
	});

## Opciones adicionales para extraer mensajes de la cola

Hay dos formas de personalizar la recuperación de mensajes de una cola:

* `options.numOfMessages` - Recuperar un lote de mensajes (hasta 32).
* `options.visibilityTimeout` - Establecer un tiempo de espera de invisibilidad más largo o más corto.

En el siguiente ejemplo se usa el método **getMessages** para obtener 15 mensajes en una llamada. A continuación, procesa cada mensaje con un bucle for. También se establece el tiempo de espera de invisibilidad en cinco minutos para todos los mensajes que devuelve este método.

    queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, result, response){
	  if(!error){
		// Messages retreived
		for(var index in result){
		  // text is available in result[index].messageText
		  var message = result[index];
		  queueSvc.deleteMessage(queueName, message.messageid, message.popreceipt, function(error, response){
			if(!error){
			  // Message deleted
			}
		  });
		}
	  }
	});

## Obtención de la longitud de la cola

El método **getQueueMetadata** devuelve metadatos sobre la cola, junto con el número aproximado de mensajes que esperan en la cola.

    queueSvc.getQueueMetadata('myqueue', function(error, result, response){
	  if(!error){
		// Queue length is available in result.approximatemessagecount
	  }
	});

## Enumeración de las colas

Para recuperar una lista de colas, use **listQueuesSegmented**. Para recuperar una lista filtrada por un prefijo determinado, use **listQueuesSegmentedWithPrefix**.

	queueSvc.listQueuesSegmented(null, function(error, result, response){
	  if(!error){
	    // result.entries contains the list of queues
	  }
	});

Si no se pueden devolver todas las colas, `result.continuationToken` se puede usar como el primer parámetro de **listQueuesSegmented** o el segundo parámetro de **listQueuesSegmentedWithPrefix** para recuperar más resultados.

## Eliminación de una cola

Para eliminar una cola y todos los mensajes contenidos en ella, llame al método **deleteQueue** en el objeto de cola.

    queueSvc.deleteQueue(queueName, function(error, response){
		if(!error){
			// Queue has been deleted
		}
	});

Para borrar todos los mensajes de una cola sin eliminarla, use **clearMessages**.

## Trabajo con firmas de acceso compartido

Las firmas de acceso compartido (SAS) constituyen una manera segura de ofrecer acceso granular a las colas sin proporcionar el nombre o las claves de su cuenta de almacenamiento. Las SAS se usan con frecuencia para proporcionar acceso limitado a sus colas, por ejemplo, para permitir que una aplicación móvil envíe mensajes.

Una aplicación de confianza, como un servicio basado en la nube, genera una SAS mediante el valor **generateSharedAccessSignature** del elemento **QueueService**, y lo proporciona a una aplicación en la que no se confía o en la que se confía parcialmente. Por ejemplo, una aplicación móvil. La SAS se genera usando una directiva que describe las fechas de inicio y de finalización durante las cuales la SAS es válida, junto con el nivel de acceso otorgado al titular de la SAS.

En el siguiente ejemplo se genera una nueva directiva de acceso compartido que permitirá al titular de la SAS agregar mensajes a la cola, y que expira 100 minutos después de la hora en que se crea.

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);

	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
	    Start: startDate,
	    Expiry: expiryDate
	  }
	};

	var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
	var host = queueSvc.host;

Tenga en cuenta que también se debe proporcionar la información del host, puesto que es necesaria cuando el titular de la SAS intenta acceder a la cola.

La aplicación cliente usa entonces la SAS con **QueueServiceWithSAS** para realizar operaciones contra la cola. En el siguiente ejemplo se realiza la conexión a la cola y se crea un mensaje.

	var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
	sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
	  if(!error){
	    //message added
	  }
	});

Dado que la SAS se generó solo con acceso para agregar, si se realizara un intento para leer, actualizar o eliminar mensajes, se devolvería un error.

### Listas de control de acceso

Se puede usar una lista de control de acceso (ACL) para definir la directiva de acceso para una SAS. Esto es útil si desea permitir que varios clientes accedan a la cola, pero cada uno con directivas de acceso diferentes.

Una ACL se implementa mediante el uso de un conjunto de directivas de acceso, con un Id. asociado a cada directiva. En los siguientes ejemplos se definen dos directivas; una para "user1" y otra para "user2":

	var sharedAccessPolicy = [
	  {
	    AccessPolicy: {
	      Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user1'
	  },
	  {
	    AccessPolicy: {
	      Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user2'
	  }
	];

En el siguiente ejemplo se obtiene la ACL actual para **myqueue** y luego se agregan las nuevas directivas mediante **setQueueAcl**. Este enfoque permite lo siguiente:

	queueSvc.getQueueAcl('myqueue', function(error, result, response) {
      if(!error){
		//push the new policy into signedIdentifiers
		result.signedIdentifiers.push(sharedAccessPolicy);
		queueSvc.setQueueAcl('myqueue', result, function(error, result, response){
	  	  if(!error){
	    	// ACL set
	  	  }
		});
	  }
	});

Después de establecer una ACL, puede crear luego una SAS basada en el Id. de una directiva. En el siguiente ejemplo se crea una nueva SAS para 'user2':

	queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });

## Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del almacenamiento de colas, utilice estos vínculos para obtener más información acerca de tareas de almacenamiento más complejas.

-   Visite el [Blog del equipo de almacenamiento de Azure][].
-   Visite el repositorio del [SDK de almacenamiento de Azure para Node.js][] en GitHub.

  [SDK de almacenamiento de Azure para Node.js]: https://github.com/Azure/azure-storage-node
  [using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Azure Portal]: https://portal.azure.com
  [Creación de una aplicación web Node.js en el Servicio de aplicaciones de Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Node.js Cloud Service with Storage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
  [Aplicación web de Node.js con servicio Tabla de Azure]: ../app-service-web/storage-nodejs-use-table-storage-web-site.md


  [Queue1]: ./media/storage-nodejs-how-to-use-queues/queue1.png
  [plus-new]: ./media/storage-nodejs-how-to-use-queues/plus-new.png
  [quick-create-storage]: ./media/storage-nodejs-how-to-use-queues/quick-storage.png



  [Creación e implementación de una aplicación Node.js en un Servicio de nube de Azure]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Creación e implementación de una aplicación web Node.js en Azure con Web Matrix]: ../app-service-web/web-sites-nodejs-use-webmatrix.md

<!----HONumber=AcomDC_0218_2016-->