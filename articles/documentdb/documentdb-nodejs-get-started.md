<properties
	pageTitle="Tutorial de NoSQL para Node.js para DocumentDB | Microsoft Azure"
	description="Tutorial de NoSQL para Node.js que crea una aplicación de consola y la base de datos de nodos con el SDK de DocumentDB de Node.js. DocumentDB es una base de datos NoSQL para JSON."
    keywords="tutorial de Node.js, base de datos de nodos"
	services="documentdb"
	documentationCenter="node.js"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="node"
	ms.topic="hero-article" 
	ms.date="02/19/2016"
	ms.author="anhoh"/>

# Tutorial de NoSQL Node.js: aplicación de consola Node.js de DocumentDB  

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.js](documentdb-nodejs-get-started.md)

Bienvenido al tutorial de Node.js para el SDK de Node.js de DocumentDB. Después de seguir este tutorial, tendrá una aplicación de consola que crea recursos de DocumentDB y realiza consultas en ellos, incluida una base de datos de nodos.

Describiremos:

- Creación y aprovisionamiento una cuenta de DocumentDB.
- Configuración de la aplicación
- Creación de una base de datos de nodos
- Creación de una colección
- Creación de documentos JSON
- Consulta de la colección
- Eliminación de la base de datos de nodos

¿No tiene tiempo? ¡No se preocupe! La solución completa está disponible en [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started). Consulte [Obtención de la solución completa](#GetSolution) para obtener instrucciones rápidas.

Después de haber completado el tutorial de Node.js, use los botones de votos en la parte superior e inferior de esta página para enviar comentarios. Si quiere que nos pongamos en contacto directamente con usted, puede incluir su dirección de correo electrónico en los comentarios.

Comencemos.

## Requisitos previos para el tutorial de Node.js

Asegúrese de que dispone de lo siguiente:

- Una cuenta de Azure activa. Si no tiene una, puede registrarse para obtener una [prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
- [Node.js](https://nodejs.org/) versión v0.10.29 o superior

## Paso 1: Creación de una cuenta de DocumentDB

Creemos una cuenta de DocumentDB. Si ya tiene una cuenta que desea usar, puede ir directamente a [Configuración de la aplicación de Node.js](#SetupNode).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupNode"></a> Paso 2: Configuración de la aplicación de Node.js

1. Abra su terminal favorito.
2. Busque la carpeta o el directorio donde desea guardar la aplicación de Node.js.
3. Cree dos archivos de JavaScript vacíos con los siguientes comandos:
	- Windows:    
	    * ```fsutil file createnew app.js 0```
        * ```fsutil file createnew config.js 0```
	- Linux/OS X: 
	    * ```touch app.js```
        * ```touch config.js```
4. Instale el módulo documentdb mediante npm. Use el comando siguiente:
    * ```npm install documentdb --save```

Estupendo. Ahora que terminamos la configuración, comencemos a escribir algo de código.

##<a id="Config"></a> Paso 3: Configuración de las opciones de la aplicación

Abra ```config.js``` en el editor de texto que prefiera.

Después, cree un objeto vacío llamado ```config``` y establezca las propiedades ```config.endpoint``` y ```config.authKey``` en sl punto de conexión y en la clave de autorización de DocumentDB. Ambas opciones de configuración se encuentran en el [Portal de Azure](https://portal.azure.com).

![Tutorial de Node.js: captura de pantalla del Portal de Azure que muestra una cuenta de DocumentDB, con el concentrador ACTIVO resaltado, el botón CLAVES resaltado en la hoja de cuenta de DocumentDB y los valores URI, CLAVE PRINCIPAL y CLAVE SECUNDARIA resaltados en la hoja Claves (base de datos de nodos).][keys]

    var config = {}

    config.endpoint = "https://YOUR_ENDPOINT_URI.documents.azure.com:443/";
    config.authKey = "oqTveZeWlbtnJQ2yMj23HOAViIr0ya****YOUR_AUTHORIZATION_KEY****ysadfbUV+wUdxwDAZlCfcVzWp0PQg==";

Ahora, agregue ```database id```, ```collection id```, y ```JSON documents``` al objeto ```config```. Debajo de donde estableció las propiedades ```config.endpoint``` y ```config.authKey```, agregue el código siguiente. Si ya dispone de datos que desea almacenar en la base de datos, puede usar la [herramienta de migración de datos](documentdb-import-data.md) de DocumentDB en lugar de agregar definiciones de documento.

    config.dbDefinition = {"id": "FamilyRegistry"};
    config.collDefinition = {"id": "FamilyCollection"};

    var documents = {
      "Andersen": {
        "id": "AndersenFamily",
        "LastName": "Andersen",
        "Parents": [
          {
            "FirstName": "Thomas"
          },
          {
            "FirstName": "Mary Kay"
          }
        ],
        "Children": [
          {
            "FirstName": "Henriette Thaulow",
            "Gender": "female",
            "Grade": 5,
            "Pets": [
              {
                "GivenName": "Fluffy"
              }
            ]
          }
        ],
        "Address": {
          "State": "WA",
          "County": "King",
          "City": "Seattle"
        }
      },
      "Wakefield":   {
          "id": "WakefieldFamily",
          "Parents": [
            {
              "FamilyName": "Wakefield",
              "FirstName": "Robin"
            },
            {
              "FamilyName": "Miller",
              "FirstName": "Ben"
            }
          ],
          "Children": [
            {
              "FamilyName": "Merriam",
              "FirstName": "Jesse",
              "Gender": "female",
              "Grade": 8,
              "Pets": [
                {
                  "GivenName": "Goofy"
                },
                {
                  "GivenName": "Shadow"
                }
              ]
            },
            {
              "FamilyName": "Miller",
              "FirstName": "Lisa",
              "Gender": "female",
              "Grade": 1
            }
          ],
          "Address": {
            "State": "NY",
            "County": "Manhattan",
            "City": "NY"
          },
          "IsRegistered": false
        }
    };

    config.docsDefinitions = documents;

Las definiciones de base de datos, colección y documento actuarán como ```database id```, ```collection id``` y datos de documentos de DocumentDB.

Por último, exporte el objeto ```config```, con el fin de que pueda hacer referencia a él en el archivo ```app.js```.

    module.exports = config;

##<a id="Connect"></a> Paso 4: Conexión a una cuenta de DocumentDB

Abra el archivo ```app.js``` vacío en el editor de texto. Importe el módulo ```documentdb``` y el módulo ```config``` recién creado.

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");

A continuación, use las propiedades ```config.endpoint``` y ```config.authKey``` que guardó anteriormente para crear DocumentClient.

    var client = new documentClient(config.endpoint, {"masterKey": config.authKey});

Ahora que está conectado a una cuenta de DocumentDB, veamos cómo trabajar con los recursos de DocumentDB.

## Paso 5: Creación de una base de datos de nodos
Para crear una [base de datos](documentdb-resources.md#databases), se puede usar la función [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) de la clase **DocumentClient**. Una base de datos es un contenedor lógico de almacenamiento de documentos particionado en recopilaciones. Agregue una función para crear la nueva base de datos en el archivo app.js con ```id``` especificado en el objeto ```config```. En primer lugar, compruebe que no existe una base de datos con el mismo identificador ```FamilyRegistry```. Si existe, usaremos esa base de datos en lugar de crear una nueva.

    var getOrCreateDatabase = function(callback) {
        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                name: '@id',
                value: config.dbDefinition.id
            }]
        };

        client.queryDatabases(querySpec).toArray(function(err, results) {
            if(err) return callback(err);

            if (results.length === 0) {
                client.createDatabase(config.dbDefinition, function(err, created) {
                    if(err) return callback(err);

                    callback(null, created);
                });
            }
            else {
                callback(null, results[0]);
            }
        });
    };

##<a id="CreateColl"></a>Paso 6: Creación de una colección  

> [AZURE.WARNING] **CreateDocumentCollectionAsync** creará una nueva colección de S1, que tiene implicaciones de precios. Para obtener más información, visite nuestra [página de precios](https://azure.microsoft.com/pricing/details/documentdb/).

Para crear una [colección](documentdb-resources.md#collections), puede usar la función [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) de la clase **DocumentClient**. Una colección es un contenedor de documentos JSON asociado a la lógica de aplicación de JavaScript. La colección recién creada se asignará a un [nivel de rendimiento S1](documentdb-performance-levels.md). Agregue una función para crear la nueva colección en el archivo app.js con el ```id``` especificado en el objeto ```config```. Vuelva a comprobar que no existe una colección con el mismo identificador ```FamilyCollection```. Si existe, usaremos esa colección en lugar de crear una nueva.

    var getOrCreateCollection = function(callback) {

        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                name: '@id',
                value: config.collDefinition.id
            }]
        };

        var dbUri = "dbs/" + config.dbDefinition.id;

        client.queryCollections(dbUri, querySpec).toArray(function(err, results) {
            if(err) return callback(err);

            if (results.length === 0) {
                client.createCollection(dbUri, config.collDefinition, function(err, created) {
                    if(err) return callback(err);
                    callback(null, created);
                });
            }
            else {
                callback(null, results[0]);
            }
        });
    };

##<a id="CreateDoc"></a>Paso 7: Creación de un documento
Para crear un [documento](documentdb-resources.md#documents), se puede usar la función [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) de la clase **DocumentClient**. Los documentos son contenido JSON definido por el usuario (arbitrario). Ahora puede insertar un documento en DocumentDB.

A continuación, agregue una función a app.js para crear los documentos que contienen los datos en formato JSON guardados en el objeto ```config```. Comprobaremos de nuevo que no existe ya un documento con el mismo identificador.

    var getOrCreateDocument = function(document, callback) {
        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                name: '@id',
                value: document.id
            }]
        };

        var collectionUri = "dbs/" + config.dbDefinition.id + "/colls/" + config.collDefinition.id;

        client.queryDocuments(collectionUri, querySpec).toArray(function(err, results) {
            if(err) return callback(err);

            if(results.length === 0) {
                client.createDocument(collectionUri, document, function(err, created) {
                    if(err) return callback(err);

                    callback(null, created);
                });
            } else {
                callback(null, results[0]);
            }
        });
    };

¡Enhorabuena! Ya tiene funciones para crear una base de datos, una colección y un documento en DocumentDB.

![Tutorial de Node.js: diagrama que muestra la relación jerárquica entre la cuenta, la base de datos, la colección y los documentos (base de datos de nodos)](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

##<a id="Query"></a>Paso 8: Consulta de recursos de DocumentDB

DocumentDB admite [consultas](documentdb-sql-query.md) enriquecidas contra los documentos JSON almacenados en cada colección. El código de ejemplo siguiente muestra una consulta que se puede ejecutar en los documentos de la colección. Agregue la función siguiente al archivo ```app.js```. DocumentDB admite consultas del tipo SQL tal y como se muestra a continuación. Para obtener más información sobre cómo crear consultas complejas, consulte [Query Playground](https://www.documentdb.com/sql/demo) y la [documentación sobre consultas](documentdb-sql-query.md).

    var queryCollection = function(documentId, callback) {
      var querySpec = {
          query: 'SELECT * FROM root r WHERE r.id=@id',
          parameters: [{
              name: '@id',
              value: documentId
          }]
      };

      var collectionUri = "dbs/" + config.dbDefinition.id + "/colls/" + config.collDefinition.id;

      client.queryDocuments(collectionUri, querySpec).toArray(function(err, results) {
          if(err) return callback(err);

          callback(null, results);
      });
    };

El siguiente diagrama muestra cómo se llama a la sintaxis de la consulta SQL de DocumentDB en la colección creada.

![Tutorial de Node.js: diagrama que ilustra el ámbito y el significado de la consulta (base de datos de nodo).](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

La palabra clave [FROM](documentdb-sql-query.md/#from-clause) es opcional en la consulta porque las consultas de DocumentDB ya tienen como ámbito una sola colección. Por lo tanto, «FROM Families f" se puede intercambiar por "FROM root r", o cualquier otra variable de nombre que elija. DocumentDB deducirá la familia, la raíz o el nombre de variable elegido y hará referencia a la colección actual de forma predeterminada.

##<a id="DeleteDatabase"></a>Paso 9: Eliminación de la base de datos de nodos

La eliminación de la base de datos creada quitará la base de datos y todos los recursos secundarios (colecciones, documentos, etc.). Para eliminar la base de datos, puede agregar el siguiente fragmento de código.

    // WARNING: this function will delete your database and all its children resources: collections and documents
    function cleanup(callback) {
        client.deleteDatabase("dbs/" + config.dbDefinition.id, function(err) {
            if(err) return callback(err);

            callback(null);
        });
    }

##<a id="Build"></a>Paso 10: Integración

Ahora que ya están establecidas todas las funciones necesarias para su aplicación, vamos a llamarlas.

El orden de las llamadas de función será * *getOrCreateDatabase* * *getOrCreateCollection* * *getOrCreateDocument* * *getOrCreateDocument* * *queryCollection* * *cleanup*

Agregue el siguiente fragmento de código al final del código en ```app.js```.

    getOrCreateDatabase(function(err, db) {
        if(err) return console.log(err);
        console.log('Read or created db:\n' + db.id + '\n');

        getOrCreateCollection(function(err, coll) {
            if(err) return console.log(err);
            console.log('Read or created collection:\n' + coll.id + '\n');

            getOrCreateDocument(config.docsDefinitions.Andersen, function(err, doc) {
                if(err) return console.log(err);
                console.log('Read or created document:\n' + doc.id + '\n');

                getOrCreateDocument(config.docsDefinitions.Wakefield, function(err, doc) {
                    if(err) return console.log(err);
                    console.log('Read or created document:\n' + doc.id + '\n');

                    queryCollection("AndersenFamily", function(err, results) {
                        if(err) return console.log(err);
                        console.log('Query results:\n' + JSON.stringify(results, null, '\t') + '\n');

                        cleanup(function(err) {
                            if(err) return console.log(err);
                            console.log('Done.');
                        });
                    });
                });
            });
        });
    });

##<a id="Run"></a> Paso 11: Configuración de la aplicación de Node.js

Ahora ya puede ejecutar la aplicación Node.js.

En el terminal, busque el archivo ```app.js``` y ejecute el comando: ```node app.js```

Ahora debería ver la salida de la aplicación GetStarted. La salida debe coincidir con el texto del ejemplo siguiente.

    Read or created db:
    FamilyRegistry

    Read or created collection:
    FamilyCollection

    Read or created document:
    AndersenFamily

    Read or created document:
    WakefieldFamily

    Query results:
    [
            {
                    "id": "AndersenFamily",
                    "LastName": "Andersen",
                    "Parents": [
                            {
                                    "FirstName": "Thomas"
                            },
                            {
                                    "FirstName": "Mary Kay"
                            }
                    ],
                    "Children": [
                            {
                                    "FirstName": "Henriette Thaulow",
                                    "Gender": "female",
                                    "Grade": 5,
                                    "Pets": [
                                            {
                                                    "GivenName": "Fluffy"
                                            }
                                    ]
                            }
                    ],
                    "Address": {
                            "State": "WA",
                            "County": "King",
                            "City": "Seattle"
                    },
                    "_rid": "tOErANjwoQcBAAAAAAAAAA==",
                    "_ts": 1444327141,
                    "_self": "dbs/tOErAA==/colls/tOErANjwoQc=/docs/tOErANjwoQcBAAAAAAAAAA==/",
                    "_etag": ""00001200-0000-0000-0000-5616aee50000"",
                    "_attachments": "attachments/"
            }
    ]

    Done.

¡Enhorabuena! Ha completado el tutorial de Node.js y tiene su primera aplicación de consola de DocumentDB.

##<a id="GetSolution"></a> Obtener la solución completa del tutorial de Node.js
Para compilar la solución GetStarted que contiene todos los ejemplos de este artículo, necesitará lo siguiente:

-   [Cuenta de DocumentDB][documentdb-create-account].
-   La solución [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) está disponible en GitHub.

Instale el módulo **documentdb** mediante npm. Use el comando siguiente: * ```npm install documentdb --save```

Después, en el archivo ```config.js```, actualice los valores de config.endpoint y config.authKey tal como se describe en el [paso 3: Configuración de las opciones de la aplicación](#Config).

## Pasos siguientes

-   ¿Desea un ejemplo más complejo de Node.js? Consulte [Compilación de una aplicación web Node.js mediante DocumentDB](documentdb-nodejs-application.md).
-	Aprenda a [supervisar una cuenta de DocumentDB](documentdb-monitor-accounts.md).
-	Ejecute las consultas en nuestro conjunto de datos de ejemplo en el [área de consultas](https://www.documentdb.com/sql/demo).
-	Obtenga más información sobre el modelo de programación en la sección sobre desarrollo de la [página de documentación de DocumentDB](../../services/documentdb/).

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png

<!---HONumber=AcomDC_0224_2016-->