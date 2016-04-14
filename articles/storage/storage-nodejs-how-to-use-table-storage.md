<properties
	pageTitle="Uso del almacenamiento de tablas de Azure en Node.js | Microsoft Azure"
	description="Obtenga información sobre como usar el almacenamiento de tablas de Azure. Los ejemplos de código están escritos usando la API Node.js."
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


# Uso del almacenamiento de tablas de Azure en Node.js

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]


## Información general

Este tema muestra cómo realizar algunas tareas comunes a través del servicio Tabla de Azure en una aplicación Node.js.

En los ejemplos de código de este tema se considera que ya tiene una aplicación Node.js. Para obtener información sobre cómo crear una aplicación Node.js en Azure, consulte alguno de estos temas:

- [Creación de una aplicación web de Node.js en el Servicio de aplicaciones de Azure](../app-service-web/web-sites-nodejs-develop-deploy-mac.md)
- [Creación e implementación de una aplicación web Node.js en Azure con WebMatrix](../app-service-web/web-sites-nodejs-use-webmatrix.md)
- [Compilación e implementación de una aplicación Node.js en un Servicio en la nube de Azure](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (con Windows PowerShell)


[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## Configuración de la aplicación para obtener acceso al almacenamiento de Azure

Para usar Almacenamiento de Azure necesitará el SDK de almacenamiento de Azure para Node.js, que incluye un conjunto de útiles bibliotecas que se comunican con los servicios REST de almacenamiento.

### Uso del Administrador de paquetes para Node (NPM) para instalar el paquete

1.  Use una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix) y vaya a la carpeta donde creó la aplicación.

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

Agregue el código siguiente a la parte superior del archivo **server.js** de la aplicación:

    var azure = require('azure-storage');

## Configuración de una conexión de almacenamiento de Azure

El módulo azure leerá las variables de entorno AZURE\_STORAGE\_ACCOUNT, AZURE\_STORAGE\_ACCESS\_KEY o AZURE\_STORAGE\_CONNECTION\_STRING para obtener la información necesaria para conectarse a su cuenta de almacenamiento de Azure. Si no se configuran estas variables de entorno, debe especificar la información de la cuenta al llamar a **TableService**.

Para ver un ejemplo de cómo configurar las variables de entorno del [Portal de Azure](https://portal.azure.com) para un sitio web de Azure, consulte [Aplicación web de Node.js con servicio Tabla de Azure].

## Creación de una tabla

El código siguiente crea un objeto **TableService** que usa para crear una tabla. Agregue lo siguiente cerca de la parte superior de **server.js**.

    var tableSvc = azure.createTableService();

La llamada a **createTableIfNotExists** creará una nueva tabla con el nombre especificado si no existe ya. El ejemplo siguiente crea una tabla llamada "mytable", si es que no existe todavía:

    tableSvc.createTableIfNotExists('mytable', function(error, result, response){
		if(!error){
			// Table exists or created
		}
	});

El `result` será `true` si se crea una nueva tabla y `false` si la tabla ya existe. La `response` contendrá información sobre la solicitud.

### Filtros

Las operaciones opcionales de filtrado se pueden aplicar a las operaciones realizadas usando **TableService**. Las operaciones de filtrado pueden incluir registros, reintentos automáticos, etc. Los filtros son objetos que implementan un método con la firma:

		function handle (requestOptions, next)

Después de realizar el preprocesamiento en las opciones de solicitud, el método tiene que llamar a "next" pasando una devolución de llamada con la firma siguiente:

		function (returnObject, finalCallback, next)

En esta devolución de llamada y después de procesar returnObject (la respuesta de la solicitud al servidor), la devolución de llamada tiene que invocar a next, si existe, para continuar procesando otros filtros, o bien simplemente invocar a finalCallback para finalizar la invocación del servicio.

Se incluyen dos filtros que implementan la lógica de reintento con el SDK de Azure para Node.js: **ExponentialRetryPolicyFilter** y **LinearRetryPolicyFilter**. Lo siguiente crea un objeto **TableService** que usa **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var tableSvc = azure.createTableService().withFilter(retryOperations);

## Adición de una entidad a una tabla

Para agregar una entidad, primero cree un objeto que defina las propiedades de la entidad. Todas las entidades deben contener un valor para **PartitionKey** y para **RowKey**, que son identificadores únicos de la entidad.

* **PartitionKey**: determina la partición en la que se almacena la entidad

* **RowKey**: identifica de forma única la entidad dentro de la partición

Tanto **PartitionKey** como **RowKey** deben ser valores de cadena. Para obtener más información, consulte [Descripción del modelo de datos del servicio Tabla](http://msdn.microsoft.com/library/azure/dd179338.aspx).

Este es un ejemplo de la definición de una entidad. Tenga en cuenta que **dueDate** se define como un tipo de **Edm.DateTime**. La especificación del tipo es opcional, y los tipos se deducen si no se especifican.

	var task = {
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'},
	  description: {'_':'take out the trash'},
	  dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
	};

> [AZURE.NOTE] Hay también un campo **Timestamp** para cada registro; Azure lo establece cuando se inserta o actualiza una entidad.

También puede usar **entityGenerator** para crear entidades. En el siguiente ejemplo se crea la misma entidad de tarea mediante **entityGenerator**.

	var entGen = azure.TableUtilities.entityGenerator;
    var task = {
	  PartitionKey: entGen.String('hometasks'),
      RowKey: entGen.String('1'),
      description: entGen.String('take out the trash'),
      dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
    };

Para agregar una entidad a su tabla, pase el objeto de la entidad al método **insertEntity**.

	tableSvc.insertEntity('mytable',task, function (error, result, response) {
		if(!error){
			// Entity inserted
		}
	});

Si la operación se realiza correctamente, `result` contendrá la etiqueta [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) del registro insertado y `response` contendrá información sobre la operación.

Respuesta de ejemplo:

	{ '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }

> [AZURE.NOTE] De forma predeterminada, el elemento **insertEntity** no devuelve la entidad insertada como parte de la información de `response`. Si tiene pensado realizar otras operaciones en esta entidad o desea almacenar en caché la información, puede ser útil que la devuelva como parte de `result`. Para ello, puede habilitar **echoContent** de la manera siguiente:
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## Actualización de una entidad

Hay varios métodos para actualizar una entidad existente:

* **updateEntity**: actualiza una entidad que ya existe reemplazándola

* **mergeEntity**: actualiza una entidad que ya existe combinando los valores de las nuevas propiedades con la entidad existente

* **insertOrReplaceEntity**: actualiza una entidad existente reemplazándola. Si no hay entidades, se insertará una nueva.

* **insertOrMergeEntity**: actualiza una entidad que ya existe combinando los valores de las nuevas propiedades con los existentes. Si no hay entidades, se insertará una nueva.

El ejemplo siguiente demuestra cómo actualizar una entidad usando **updateEntity**:

	tableSvc.updateEntity('mytable', updatedTask, function(error, result, response){
      if(!error) {
        // Entity updated
      }
    });

> [AZURE.NOTE] De manera predeterminada, al actualizar una entidad no se comprueba si otro proceso ha modificado anteriormente los datos que se actualizan. Para permitir las actualizaciones simultáneas:
>
> 1. Obtenga la etiqueta ETag del objeto que se va a actualizar. Esta se devuelve como parte del valor de `response` para cualquier operación relacionada con entidades y se puede recuperar a través de `response['.metadata'].etag`.
>
> 2. Al realizar una operación de actualización en una entidad, agregue la información de ETag anteriormente recuperada a la nueva entidad. Por ejemplo:
>
>     `entity2['.metadata'].etag = currentEtag;`
>    
> 3. Realice la operación de actualización. Si la entidad se modificó desde que recuperara el valor de ETag, como por ejemplo, otra instancia de la aplicación, se devolverá un `error` indicando que la condición de actualización especificada en la solicitud no se ha satisfecho.

Con **updateEntity** y **mergeEntity**, si la entidad que se está actualizando no existe, se producirá un error en la operación de actualización. Por lo tanto, si desea almacenar una entidad independientemente de la que ya existe, use **insertOrReplaceEntity** o **insertOrMergeEntity**.

El `result` de operaciones de actualización correctas contendrá la etiqueta **Etag** de la entidad actualizada.

## Trabajo con grupos de entidades

A veces resulta útil enviar varias operaciones juntas en un lote a fin de garantizar el procesamiento atómico por parte del servidor. Para ello, use la clase **TableBatch** para crear un lote y, a continuación, use el método **executeBatch** de **TableService** para realizar las operaciones por lotes.

 El ejemplo siguiente demuestra cómo enviar dos entidades en un lote:

    var task1 = {
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'},
	  description: {'_':'Take out the trash'},
	  dueDate: {'_':new Date(2015, 6, 20)}
	};
	var task2 = {
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '2'},
	  description: {'_':'Wash the dishes'},
	  dueDate: {'_':new Date(2015, 6, 20)}
	};

	var batch = new azure.TableBatch();

	batch.insertEntity(task1, {echoContent: true});
	batch.insertEntity(task2, {echoContent: true});

	tableSvc.executeBatch('mytable', batch, function (error, result, response) {
	  if(!error) {
	    // Batch completed
	  }
	});

En las operaciones por lotes realizadas correctamente, `result` contendrá información de cada operación del lote.

### Trabajar con operaciones por lotes

Las operaciones agregadas a un lote se pueden inspeccionar mirando la propiedad `operations`. También se pueden usar los siguientes métodos para trabajar con operaciones.

* **clear**: borra todas las operaciones de un lote

* **getOperations**: obtiene una operación del lote

* **hasOperations**: devuelve true si el lote contiene operaciones

* **removeOperations**: quita una operación.

* **size**: devuelve el número de operaciones del lote

## Recuperación de una entidad por clave

Para devolver una entidad específica basada en los valores de **PartitionKey** y **RowKey**, use el método **retrieveEntity**.

    tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
	  if(!error){
	    // result contains the entity
	  }
    });

Después de completar esta operación, `result` contendrá la entidad.

## Consulta de un conjunto de entidades

Para consultar una tabla, use el objeto **TableQuery** para compilar una expresión de consulta mediante las siguientes cláusulas:

* **select**: son los campos que va a devolver la consulta

* **where**: la cláusula where.

	* **and**: es una condición where `and`

	* **or**: es una condición where `or`

* **top**: es el número de elementos que se obtendrán


En el siguiente ejemplo se crea una consulta que devolverá los cinco elementos principales con un valor de PartitionKey de 'hometasks'.

	var query = new azure.TableQuery()
	  .top(5)
	  .where('PartitionKey eq ?', 'hometasks');

Dado que **select** no se usa, se devolverán todos los campos. Para realizar la consulta en una tabla, use **queryEntities**. En el siguiente ejemplo se usa esta consulta para devolver entidades de 'mytable'.

	tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
	  if(!error) {
	    // query was successful
	  }
	});

Si la operación se realiza correctamente, `result.entries` contendrá una matriz de entidades que coinciden con la consulta. Si la consulta no puede devolver todas las entidades, `result.continuationToken` será non-*null* y se puede usar como el tercer parámetro de **queryEntities** para recuperar más resultados. Para la consulta inicial, use *null* para el tercer parámetro.

### Consulta de un subconjunto de propiedades de las entidades

Una consulta de tabla puede recuperar solo algunos campos de una entidad. Esto reduce el ancho de banda y puede mejorar el rendimiento de las consultas, en especial en el caso de entidades de gran tamaño. Use la cláusula **select** y pase los nombres de los campos que se van a devolver. Por ejemplo, la siguiente consulta solo devolverá los campos **description** y **dueDate**.

	var query = new azure.TableQuery()
	  .select(['description', 'dueDate'])
	  .top(5)
	  .where('PartitionKey eq ?', 'hometasks');

## Eliminación de una entidad

Puede eliminar una entidad usando sus claves de partición y fila. En este ejemplo, el objeto **task1** contiene los valores **RowKey** y **PartitionKey** de la entidad que se va a eliminar. A continuación, el objeto pasa al método **deleteEntity**.

	var task = {
	  PartitionKey: {'_':'hometasks'},
	  RowKey: {'_': '1'}
	};

    tableSvc.deleteEntity('mytable', task, function(error, response){
	  if(!error) {
		// Entity deleted
	  }
	});

> [AZURE.NOTE] Cuando elimine elementos, debería considerar el uso de etiquetas ETag para garantizar que otro proceso no haya modificado el elemento. Consulte [Actualización de una entidad](#update-an-entity) para obtener información sobre el uso de etiquetas ETag.

## Eliminación de una tabla

El código siguiente elimina una tabla de la cuenta de almacenamiento.

    tableSvc.deleteTable('mytable', function(error, response){
		if(!error){
			// Table deleted
		}
	});

Si no está seguro de si existe la tabla, use **deleteTableIfExists**.

## Usar tokens de continuación

Cuando consulte tablas para grandes cantidades de resultados, busque tokens de continuación. Puede haber grandes cantidades de datos disponibles para su consulta de los que podría no darse cuenta si no crear para reconocer cuando hay un token de continuación presente.

El objeto de resultados devuelto al consultar los conjuntos de entidades, establece una propiedad `continuationToken` cuando hay un token de este tipo presente. Entonces, podrá usarlo al realizar una consulta para continuar moviéndose por las entidades de tabla y partición.

Al consultar, se puede proporcionar un parámetro continuationToken entre la instancia de objeto de consulta y la función de devolución de llamada:

```
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

Si inspecciona el objeto `continuationToken`, encontrará propiedades como `nextPartitionKey`, `nextRowKey` y `targetLocation`, que puede usar para iterar en todos los resultados.

También hay un ejemplo de continuación en el repositorio de Node.js de Almacenamiento de Azure en GitHub. Busque `examples/samples/continuationsample.js`.

## Trabajo con firmas de acceso compartido

Las firmas de acceso compartido (SAS) constituyen una manera segura de ofrecer acceso granular a las tablas sin proporcionar el nombre o las claves de su cuenta de almacenamiento. Las SAS se usan con frecuencia para proporcionar acceso limitado a sus datos, por ejemplo, para permitir que una aplicación móvil consulte registros.

Una aplicación de confianza, como por ejemplo un servicio basado en la nube, genera una SAS mediante el valor **generateSharedAccessSignature** del elemento **TableService** y se lo proporciona a una aplicación en la que no se confía o en la que se confía parcialmente, como una aplicación móvil. La SAS se genera usando una directiva que describe las fechas de inicio y de finalización durante las cuales la SAS es válida, junto con el nivel de acceso otorgado al titular de la SAS.

En el siguiente ejemplo se genera una nueva directiva de acceso compartido que permitirá al titular de la SAS consultar ('r') la tabla, y que expira 100 minutos después de la hora en que se crea.

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);

	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	};

	var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
	var host = tableSvc.host;

Tenga en cuenta que también se debe proporcionar la información del host, puesto que es necesaria cuando el titular de la SAS intenta acceder a la tabla.

La aplicación cliente usa entonces la SAS con **TableServiceWithSAS** para realizar operaciones en la tabla. En el siguiente ejemplo se realiza la conexión a la tabla y se realiza una consulta.

	var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
	var query = azure.TableQuery()
	  .where('PartitionKey eq ?', 'hometasks');

	sharedTableService.queryEntities(query, null, function(error, result, response) {
	  if(!error) {
		// result contains the entities
	  }
	});

Dado que la SAS se generó solo con acceso de consulta, si se realizara un intento para insertar, actualizar o eliminar entidades, se devolvería un error.

### Listas de control de acceso

Se puede usar una lista de control de acceso (ACL) para definir la directiva de acceso para una SAS. Esto es útil si desea permitir que varios clientes accedan a la tabla, pero cada uno con directivas de acceso diferentes.

Una ACL se implementa mediante el uso de un conjunto de directivas de acceso, con un Id. asociado a cada directiva. En los siguientes ejemplos se definen dos directivas; una para "user1" y otra para "user2":

	var sharedAccessPolicy = [
	  {
	    AccessPolicy: {
	      Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user1'
	  },
	  {
	    AccessPolicy: {
	      Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user2'
	  }
	];

En el siguiente ejemplo se obtiene la ACL actual para **hometasks** y luego se agregan las nuevas directivas mediante **setTableAcl**. Este enfoque permite lo siguiente:

	tableSvc.getTableAcl('hometasks', function(error, result, response) {
      if(!error){
		//push the new policy into signedIdentifiers
		result.signedIdentifiers.push(sharedAccessPolicy);
		tableSvc.setTableAcl('hometasks', result, function(error, result, response){
	  	  if(!error){
	    	// ACL set
	  	  }
		});
	  }
	});

Después de establecer una ACL, puede crear luego una SAS basada en el Id. de una directiva. En el siguiente ejemplo se crea una nueva SAS para 'user2':

	tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });

## Pasos siguientes

Para obtener más información, consulte los siguientes recursos:

-   [Blog del equipo de almacenamiento de Azure][]
-   Repositorio del [SDK de almacenamiento de Azure para Node.js][] en GitHub
-   [Centro para desarrolladores de Node.js](/develop/nodejs/)

  [SDK de almacenamiento de Azure para Node.js]: https://github.com/Azure/azure-storage-node
  [OData.org]: http://www.odata.org/
  [Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Azure Portal]: portal.azure.com

  [Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
  [Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Website with WebMatrix]: ../web-sites-nodejs-use-webmatrix.md
  [Node.js Cloud Service with Storage]: ../storage-nodejs-use-table-storage-cloud-service-app.md
  [Aplicación web de Node.js con servicio Tabla de Azure]: ../storage-nodejs-use-table-storage-web-site.md
  [Create and deploy a Node.js application to an Azure website]: ../web-sites-nodejs-develop-deploy-mac.md

<!----HONumber=AcomDC_0218_2016-->