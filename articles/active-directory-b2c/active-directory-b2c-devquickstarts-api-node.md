<properties
	pageTitle="Versión preliminar de B2C: protección de una API web mediante node.js | Microsoft Azure"
	description="Creación de una API web Node.js que acepta tokens de un inquilino B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="brandwe"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
  	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="hero-article"
	ms.date="02/17/2016"
	ms.author="brandwe"/>

# Versión preliminar de B2C: protección de una API web mediante node.js

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]


> [AZURE.NOTE] Este artículo no trata sobre cómo implementar el inicio de sesión, el registro y la administración de perfiles con Azure AD B2C. Se centra en llamar a las API web una vez que el usuario está autenticado. Si aún no lo ha hecho, debe comenzar con el [tutorial de introducción a las aplicaciones web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) para obtener información sobre los conceptos básicos de Azure Active Directory B2C.


> [AZURE.NOTE]	Este ejemplo se escribió para conectase con nuestra [aplicación de ejemplo B2C para iOS](active-directory-b2c-devquickstarts-ios.md). Primero realice este tutorial y luego continúe con ese ejemplo.

**Passport** es middleware de autenticación para Node.js. Muy flexible y modular, Passport puede instalarse discretamente en cualquier aplicación web basada en Express o Restify. Un conjunto completo de estrategias admite la autenticación mediante un nombre de usuario y contraseña, Facebook, Twitter, etc. Hemos desarrollado una estrategia para Azure Active Directory (Azure AD). Instalará este módulo y, a continuación, agregará el complemento `passport-azure-ad` de Azure AD.

Para ello, necesitará lo siguiente:

1. Registrar una aplicación con Azure AD.
2. Configurar la aplicación para usar el complemento `azure-ad-passport` de Passport.
3. Configurar una aplicación cliente para llamar a la API web "lista de tareas pendientes".

El código de este tutorial [se mantiene en GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs). Para poder continuar, puede [descargar el esqueleto de la aplicación como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/skeleton.zip) o clonar el esqueleto:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```

La aplicación completa se ofrece al final de este tutorial.

> [AZURE.WARNING] 	Para nuestra versión preliminar de B2C, debe usar el mismo **Id. de cliente**/**Id. de aplicación**, así como directivas tanto para el servidor de tareas de la API web como para el cliente que se conecta a él. Esto se aplica también a nuestros tutoriales de iOS y Android. Si previamente ha creado una aplicación en cualquiera de estos tutoriales, use esos valores en lugar de crear otros.


## Obtener un directorio de Azure AD B2C

Para poder usar Azure AD B2C, debe crear un directorio o inquilino. Un directorio es un contenedor para todos los usuarios, las aplicaciones, los grupos, etc. Si aún no tiene ninguno, [cree un directorio B2C](active-directory-b2c-get-started.md) antes de continuar.

## Creación de una aplicación

A continuación, debe crear una aplicación en su directorio de B2C, que ofrece a Azure AD información que necesita para comunicarse de forma segura con su aplicación. En este caso, tanto la aplicación cliente como la API web se representarán mediante un **identificador de aplicación** único, ya que conforman una aplicación lógica. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de:

- Incluir una **aplicación web/API web** en la aplicación.
- Escribir `http://localhost/TodoListService` como **URL de respuesta**. Es la dirección URL predeterminada para este ejemplo de código.
- Crear un **Secreto de aplicación** para la aplicación y copiarlo. Lo necesitará más adelante. Lo necesitará en breve. Tenga en cuenta que este valor debe [incluirse entre secuencias de escape XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) antes de usarlo.
- Copiar el **Id. de aplicación** asignado a la aplicación. También lo necesitará más adelante.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Crear sus directivas

En Azure AD B2C, cada experiencia del usuario se define mediante una [directiva](active-directory-b2c-reference-policies.md). Esta aplicación contiene tres experiencias de identidad: registro, inicio de sesión e inicio de sesión con Facebook. Debe crear una directiva de cada tipo, como se describe en el [artículo de referencia de las directivas](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Cuando cree las tres directivas, asegúrese de:

- Seleccionar **Nombre para mostrar** y otros atributos de registro en la directiva de registro.
- Elegir las notificaciones de aplicación **Nombre para mostrar** e **Id. de objeto** en todas las directivas. Puede elegir también otras notificaciones.
- Copiar el **Nombre** de cada directiva después de crearla. Debe tener el prefijo `b2c_1_`. Necesitará esos nombres de directiva más adelante.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Una vez creadas las tres directivas, está listo para compilar la aplicación.

Tenga en cuenta que este artículo no trata sobre cómo usar las directivas que acaba de crear. Para aprender cómo funcionan las directivas en Azure AD B2C, comience con el [tutorial de introducción a las aplicaciones web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Descarga de Node.js para su plataforma

Para usar correctamente este ejemplo, debe disponer de una instalación en funcionamiento de Node.js.

Instale Node.js desde [nodejs.org](http://nodejs.org).

## Instalación de MongoDB en la plataforma

Para usar correctamente este ejemplo, debe disponer también de una instalación en funcionamiento de MongoDB. Usará MongoDB para que nuestra API de REST sea persistente en instancias de servidor.

Instale MongoDB desde [mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] En este tutorial se asume que usará la instalación predeterminada y los puntos de conexión del servidor de MongoDB, que en el momento de escribir este artículo es `mongodb://localhost`.

## Instalación de los módulos Restify en su API web

Utilizará Restify para crear la API de REST. Restify es un marco de aplicación de Node.js mínimo y flexible que deriva de Express. Tiene un sólido conjunto de características para crear API de REST sobre Connect.

### Instalar Restify

En la línea de comandos, cambie el directorio a `azuread`. Si el directorio `azuread` no existe, créelo.

`cd azuread` o `mkdir azuread;`

Escriba el comando siguiente:

`npm install restify`

Este comando instala Restify.

#### ¿Ha recibido un error?

En algunos sistemas operativos, al utilizar `npm`, podría recibir el error `Error: EPERM, chmod '/usr/local/bin/..'` y una solicitud de que ejecute la cuenta como administrador. En ese caso, utilice el comando `sudo` para ejecutar `npm` en un nivel de privilegio más elevado.

#### ¿Recibió un error de DTrace?

Es posible que vea algo parecido a esto al instalar Restify:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```

Restify proporciona un mecanismo eficaz para rastrear llamadas a REST mediante DTrace. Sin embargo, muchos sistemas operativos no tienen DTrace disponible. Puede omitir estos errores con seguridad.

El resultado del comando debe ser similar al siguiente:

	restify@2.6.1 node_modules/restify
	├── assert-plus@0.1.4
	├── once@1.3.0
	├── deep-equal@0.0.0
	├── escape-regexp-component@1.0.2
	├── qs@0.6.5
	├── tunnel-agent@0.3.0
	├── keep-alive-agent@0.0.1
	├── lru-cache@2.3.1
	├── node-uuid@1.4.0
	├── negotiator@0.3.0
	├── mime@1.2.11
	├── semver@2.2.1
	├── spdy@1.14.12
	├── backoff@2.3.0
	├── formidable@1.0.14
	├── verror@1.3.6 (extsprintf@1.0.2)
	├── csv@0.3.6
	├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
	└── bunyan@0.22.0 (mv@0.0.5)

## Instalación de Passport en la API web

[Passport](http://passportjs.org/) es middleware de autenticación para Node.js. Muy flexible y modular, Passport puede instalarse discretamente en cualquier aplicación web basada en Express o Restify. Un conjunto completo de estrategias admite la autenticación mediante un nombre de usuario y contraseña, Facebook, Twitter, etc. Hemos desarrollado una estrategia para Azure AD. Instalará este módulo y, a continuación, agregará el complemento de estrategia de Azure AD.

En la línea de comandos, cambie el directorio a `azuread`, si aún no está allí.

Escriba el siguiente comando para instalar Passport:

`npm install passport`

El resultado de este comando debe ser similar al siguiente:

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## Incorporación de passport-azuread a la API web

A continuación, agregue la estrategia de OAuth mediante `passport-azuread`, un conjunto de estrategias que conectan Azure AD a Passport. Utilice esta estrategia para los tokens de portador en el ejemplo de API de REST.

> [AZURE.NOTE] Aunque OAuth2 proporciona un marco de trabajo en el que se puede emitir cualquier tipo de token conocido, solo ciertos tipos de token tienen un uso generalizado. Los tokens para la protección de puntos de conexión se denominan tokens de portador. Son el tipo de token más emitido en OAuth2. Muchas implementaciones asumen que los tokens de portador son el único tipo de token emitido.

En la línea de comandos, cambie el directorio a `azuread`, si aún no está allí.

Escriba el siguiente comando para instalar el módulo `passport-azure-ad` de Passport:

`npm install passport-azure-ad`

El resultado de este comando debe ser similar al siguiente:

``
passport-azure-ad@1.0.0 node_modules/passport-azure-ad
├── xtend@4.0.0
├── xmldom@0.1.19
├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
├── underscore@1.8.3
├── async@1.3.0
├── jsonwebtoken@5.0.2
├── xml-crypto@0.5.27 (xpath.js@1.0.6)
├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
└── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)
``

## Incorporación de módulos MongoDB a la API web

Utilizará MongoDB como almacén de datos. Por ese motivo, debe instalar Mongoose, un complemento muy utilizado para administrar modelos y esquemas, y el controlador de base de datos de MongoDB, también llamado MongoDB.

* `npm install mongoose`
* `npm install mongodb`

## Instalar módulos adicionales

A continuación, instale los demás módulos necesarios.

En la línea de comandos, cambie el directorio a `azuread`, si aún no está allí:

`cd azuread`

Escriba los comandos siguientes para instalar los módulos en su directorio `node_modules`:

* `npm install crypto`
* `npm install assert-plus`
* `npm install posix-getopt`
* `npm install util`
* `npm install path`
* `npm install connect`
* `npm install xml-crypto`
* `npm install xml2js`
* `npm install xmldom`
* `npm install async`
* `npm install request`
* `npm install underscore`
* `npm install grunt-contrib-jshint@0.1.1`
* `npm install grunt-contrib-nodeunit@0.1.2`
* `npm install grunt-contrib-watch@0.2.0`
* `npm install grunt@0.4.1`
* `npm install xtend@2.0.3`
* `npm install bunyan`
* `npm update`


## Creación de un archivo server.js con sus dependencias

El archivo `server.js` proporcionará la mayor parte de la funcionalidad de su servidor de API web. Agregará la mayor parte de nuestro código a este archivo. Para la producción, debería refactorizar la funcionalidad en archivos más pequeños, como controladores y rutas independientes. En este tutorial, use server.js para esta funcionalidad.

En la línea de comandos, cambie el directorio a `azuread`, si aún no está allí:

`cd azuread`

Cree un archivo `server.js` en un editor. Agregue la siguiente información:

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Guarde el archivo . Volverá a ella más adelante.

## Creación de un archivo config.js para almacenar la configuración de Azure AD

Este archivo de código pasa los parámetros de configuración desde el Portal de Azure AD al archivo `Passport.js`. Creó estos valores de configuración al agregar la API web al portal en la primera parte del tutorial. Explicaremos qué incluir en los valores de estos parámetros después de haber copiado el código.

En la línea de comandos, cambie el directorio a `azuread`, si aún no está allí:

`cd azuread`

Cree un archivo `config.js` en un editor. Agregue la siguiente información:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>',
identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration', // For using Microsoft you should never need to change this.
tenantName:'<tenant name>',
policyName:'b2c_1_<sign in policy name>',
};

```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### Valores obligatorios

`IdentityMetadata`: aquí es donde `passport-azure-ad` buscará los datos de configuración para el proveedor de identidades. También buscará aquí las claves validar los tokens de web JSON. Probablemente no desea cambiarlo si utiliza Azure AD.

`audience`: identificador uniforme de recursos (URI) del portal que identifica el servicio. En nuestro ejemplo se usa `http://localhost/TodoListService`.

`tenantName`: su nombre de inquilino (por ejemplo, **contoso.onmicrosoft.com**).

`policyName`: la directiva con la que desea validar los tokens que entran en el servidor. Debe ser la misma directiva que usa en la aplicación cliente para iniciar sesión.

> [AZURE.NOTE] Para nuestra versión preliminar de B2C, use las mismas directivas en las instalaciones de cliente y de servidor. Si ya realizó un tutorial y creó estas directivas, no necesita volver a hacerlo. Puesto que completó el tutorial, no necesita configurar nuevas directivas para los tutoriales de cliente en el sitio.

## Incorporación de la configuración a su archivo server.js

Debe leer estos valores del archivo `config.js` que acaba de crear en su aplicación. Para ello, agregue el archivo `.config` como un recurso necesario en su aplicación y, a continuación, establezca las variables globales en las del documento `config.js`.

En la línea de comandos, cambie el directorio a `azuread`, si aún no está allí:

`cd azuread`

Abra el archivo `server.js` en un editor. Agregue la siguiente información:

```Javascript
var config = require('./config');
```
Agregue una nueva sección a `server.js` que incluya el siguiente código:

```Javascript
// We pass these options in to the ODICBearerStrategy.
var options = {
// The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience
};
// array to hold logged-in users and the current logged-in user (owner)
var users = [];
var owner = null;
// Our logger
var log = bunyan.createLogger({
name: 'Microsoft Azure Active Directory Sample'
});
```

## Incorporación del modelo MongoDB y la información del esquema mediante Mongoose

La preparación anterior se amortizará al unir estos tres archivos en un servicio de la API de REST.

Para este tutorial, use MongoDB para almacenar las tareas, como se explicó anteriormente.

En el archivo `config.js` que creó, llamó a la base de datos **tasklist**. También es lo que incluyó al final de la dirección URL de conexión `mongoose_auth_local`. No es necesario crear esta base de datos por anticipado en MongoDB. Creará la base de datos la primera vez que se ejecute la aplicación de servidor, si aún no existe.

Después de indicar al servidor la base de datos MongoDB que se usará, debemos escribir algo de código adicional para crear el modelo y el esquema para las tareas del servidor.

### Expansión del modelo

Este modelo de esquema es muy sencillo. Se puede expandir según sea necesario.

`name`: quién está asignado a la tarea. Se trata de una **cadena**.

`task`: la propia tarea. Se trata de una **cadena**.

`date`: la fecha en que vence la tarea. Se trata de un elemento **datetime**.

`completed`: si la tarea está completa o no. Se trata de un elemento **booleano**.

### Creación del esquema en el código

En la línea de comandos, cambie el directorio a `azuread`, si aún no está allí:

`cd azuread`

Abra el archivo `server.js` en un editor. Agregue la siguiente información debajo de la entrada de configuración:

```Javascript
// MongoDB setup
// Set up some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');
```
Se conectará al servidor de MongoDB y devolverá un objeto de esquema.

### Utilice el esquema para crear el modelo en el código

Debajo del código que escribió anteriormente, agregue el código siguiente:

```Javascript
// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Primero cree el esquema y, después, cree un objeto de modelo que utilizará para almacenar los datos en todo el código al definir sus **rutas**.

## Incorporación de rutas para el servidor de la tarea de API de REST

Ahora que tiene un modelo de base de datos con el que trabajar, agregue las rutas que usará para nuestro servidor de API de REST.

### Información acerca de las rutas en Restify

Las rutas funcionan en Restify exactamente del mismo modo que funcionan al usar la pila Express. Defina rutas mediante el URI al que espera que llamen las aplicaciones cliente. En la mayoría de los casos, debe definir las rutas en un archivo independiente. Para este tutorial, colocará las rutas en el archivo `server.js`. Se recomienda factorizarlas en su propio archivo para el uso de producción.

Un patrón típico de una ruta Restify es:

```Javascript
function createObject(req, res, next) {
// do work on Object
_object.name = req.params.object; // passed value is in req.params under object
///...
return next(); // keep the server going
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```

Este es el patrón en su nivel más básico. Restify y Express proporcionan una funcionalidad mucho más profunda, como la definición de tipos de aplicación y la realización de un enrutamiento complejo en distintos puntos de conexión. Para este tutorial, mantendremos estas rutas simples.

#### Incorporación de rutas predeterminadas al servidor

Ahora agregará las rutas CRUD básicas de **create**, **retrieve**, **update** y **delete**.

En la línea de comandos, cambie el directorio a `azuread`, si aún no está allí:

`cd azuread`

Abra el archivo `server.js` en un editor. Agregue la siguiente información debajo de las entradas de la base de datos que creó anteriormente:

```Javascript
/**
*
* APIs for our REST task server
*/
// Create a task
function createTask(req, res, next) {
// Restify currently has a bug that doesn't allow you to set default headers
// The headers comply with CORS and allow us to mongodbServer our response to any origin
res.header("Access-Control-Allow-Origin", "*");
res.header("Access-Control-Allow-Headers", "X-Requested-With");
// Create a new task model, fill it up and save it to Mongodb
var _task = new Task();
if (!req.params.task) {
req.log.warn({
params: p
}, 'createTodo: missing task');
next(new MissingTaskError());
return;
}
_task.owner = owner;
_task.task = req.params.task;
_task.date = new Date();
_task.save(function(err) {
if (err) {
req.log.warn(err, 'createTask: unable to save');
next(err);
} else {
res.send(201, _task);
}
});
return next();
}
// Delete a task by name
function removeTask(req, res, next) {
Task.remove({
task: req.params.task,
owner: owner
}, function(err) {
if (err) {
req.log.warn(err,
'removeTask: unable to delete %s',
req.params.task);
next(err);
} else {
log.info('Deleted task:', req.params.task);
res.send(204);
next();
}
});
}
// Delete all tasks
function removeAll(req, res, next) {
Task.remove();
res.send(204);
return next();
}
// Get a specific task based on name
function getTask(req, res, next) {
log.info('getTask was called for: ', owner);
Task.find({
owner: owner
}, function(err, data) {
if (err) {
req.log.warn(err, 'get: unable to read %s', owner);
next(err);
return;
}
res.json(data);
});
return next();
}
/// Simple returns the list of TODOs that were loaded.
function listTasks(req, res, next) {
// Restify currently has a bug that doesn't allow you to set default headers
// The headers comply with CORS and allow us to mongodbServer our response to any origin
res.header("Access-Control-Allow-Origin", "*");
res.header("Access-Control-Allow-Headers", "X-Requested-With");
log.info("listTasks was called for: ", owner);
Task.find({
owner: owner
}).limit(20).sort('date').exec(function(err, data) {
if (err)
return next(err);
if (data.length > 0) {
log.info(data);
}
if (!data.length) {
log.warn(err, "There is no tasks in the database. Add one!");
}
if (!owner) {
log.warn(err, "You did not pass an owner when listing tasks.");
} else {
res.json(data);
}
});
return next();
}
```

#### Incorporación del control de errores para las rutas

Debe agregar un cierto control de errores, para poder comunicar al cliente los problemas que encuentra de una forma que pueda comprender.

Agregue el código siguiente debajo del código que escribió anteriormente:

```Javascript
///--- Errors for communicating something interesting back to the client
function MissingTaskError() {
restify.RestError.call(this, {
statusCode: 409,
restCode: 'MissingTask',
message: '"task" is a required parameter',
constructorOpt: MissingTaskError
});
this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);
function TaskExistsError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 409,
restCode: 'TaskExists',
message: owner + ' already exists',
constructorOpt: TaskExistsError
});
this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);
function TaskNotFoundError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 404,
restCode: 'TaskNotFound',
message: owner + ' was not found',
constructorOpt: TaskNotFoundError
});
this.name = 'TaskNotFoundError';
}
util.inherits(TaskNotFoundError, restify.RestError);
```


## Creación del servidor

Ahora ha definido la base de datos y colocado las rutas en su sitio. Lo último que debe hacer es agregar la instancia del servidor que va a administrar las llamadas.

Restify y Express proporcionan una personalización más avanzada para un servidor de la API de REST, pero aquí utilizará la configuración más básica.

```Javascript
/**
* Our server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directory TODO Server",
version: "2.0.1"
});
// Ensure that we don't drop data on uploads
server.pre(restify.pre.pause());
// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());
// Handle annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());
// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());
// Allow five requests/second by IP, and burst to 10
server.use(restify.throttle({
burst: 10,
rate: 5,
ip: true,
}));
// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
mapParams: true
}));
```
## Incorporación de las rutas al servidor (sin autenticación)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers is protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the passport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiment with removing API protection
/* by removing the passport.authenticate() method like this:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```
## Ejecución del servidor antes de agregar la compatibilidad con OAuth

Debe probar el servidor antes de agregar la autenticación.

La forma más fácil de hacerlo es con `curl` en una línea de comandos. Antes de hacerlo, necesita una utilidad sencilla que le permita analizar el resultado como JSON. En primer lugar, instale la herramienta JSON.

`$npm install -g jsontool`

De esta forma, se instala la herramienta JSON globalmente. Después de instalar la herramienta JSON, pruebe el servidor:

Asegúrese de que la instancia de MongoDB se esté ejecutando.

`$sudo mongodb`

Cambie al directorio `azuread` y utilice `curl`.

`$ cd azuread`
`$ node server.js`

`$ curl -isS http://127.0.0.1:8080 | json`

```Shell
HTTP/1.1 200 OK
Connection: close
Content-Type: application/json
Content-Length: 171
Date: Tue, 14 Jul 2015 05:43:38 GMT
[
"GET /",
"POST /tasks/:owner/:task",
"POST /tasks (for JSON body)",
"GET /tasks",
"PUT /tasks/:owner",
"GET /tasks/:owner",
"DELETE /tasks/:owner/:task"
]
```

Agregue una tarea:

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

La respuesta debe ser:

```Shell
HTTP/1.1 201 Created
Connection: close
Access-Control-Allow-Origin: *
Access-Control-Allow-Headers: X-Requested-With
Content-Type: application/x-www-form-urlencoded
Content-Length: 5
Date: Tue, 04 Feb 2014 01:02:26 GMT
Hello
```
Puede enumerar las tareas del usuario "Brandon" de este modo:

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Si funciona, está preparado para agregar OAuth al servidor de API de REST.

Tiene un servidor de API de REST con MongoDB.

## Incorporación de autenticación al servidor de API de REST

Ahora que tiene un servidor de API de REST en ejecución, puede hacer que sea útil en Azure AD.

En la línea de comandos, cambie el directorio a `azuread`, si aún no está allí:

`cd azuread`

### Use el elemento OIDCBearerStrategy que se incluye con passport-azure-ad

Hasta ahora ha creado un servidor típico de TODO de REST sin ningún tipo de autorización. Ahora puede empezar a reunir la autorización.

En primer lugar, necesitará indicar que desea usar Passport. Agregue lo siguiente directamente debajo del resto de la configuración del servidor:

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts Passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
Al escribir las API, siempre debería vincular los datos a un elemento único del token cuya identidad el usuario no pueda suplantar. Cuando el servidor almacena elementos TODO, lo hace en función del **Id. de objeto** del usuario en el token (llamado a través de token.oid) que se coloca en el campo "owner". Esto garantiza que solo el usuario pueda tener acceso a sus elementos TODO y que ningún otro usuario pueda acceder a los elementos que se han especificado. En la API "owner" no queda expuesto, así que un usuario externo puede solicitar los elementos TODO de los demás, incluso si se autentican.

A continuación, use la estrategia de portador que viene con `passport-azure-ad`. (Por ahora solo veremos el código). Incluya esto después de lo anterior:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementer.
/* Here we just autoregister any user and implement a FindById().
/* You'll want to do something smarter.
**/
var findById = function(id, fn) {
for (var i = 0, len = users.length; i < len; i++) {
var user = users[i];
if (user.sub === id) {
log.info('Found user: ', user);
return fn(null, user);
}
}
return fn(null, null);
};
var oidcStrategy = new OIDCBearerStrategy(options,
function(token, done) {
log.info('verifying the user');
log.info(token, 'was the token retrieved');
findById(token.sub, function(err, user) {
if (err) {
return done(err);
}
if (!user) {
// "Auto-registration"
log.info('User was added automatically as they were new. Their sub is: ', token.sub);
users.push(token);
owner = token.sub;
return done(null, token);
}
owner = token.sub;
return done(null, user, token);
});
}
);
passport.use(oidcStrategy);
```

Passport usa un patrón para todas sus estrategias (incluidos Twitter y Facebook) similar a las que cumplen todos los escritores de estrategias. Le pasa un elemento `function()` que tiene los parámetros `token` y `done`. La estrategia regresará después de haber realizado todo el trabajo. A continuación, debe almacenar el usuario y guardar el token para que no haga falta volver a solicitarlo.

> [AZURE.IMPORTANT]
El código anterior toma cualquier usuario que se autentique en el servidor. Esto se conoce como registro automático. En los servidores de producción, no debería permitir que ningún usuario acceda sin que antes pase por el proceso de registro que haya decidido. Este suele ser el patrón que se observa en las aplicaciones de consumidor que permiten registrarse con Facebook, pero que luego piden que se rellene información adicional. Si no se tratara de un programa de línea de comandos, podríamos haber extraído el correo electrónico del objeto de token devuelto y, a continuación, haber pedido a los usuarios que rellenaran la información adicional. Puesto que se trata de un servidor de prueba, simplemente los agregaremos a la base de datos en memoria.

### Protección de puntos de conexión

Los puntos de conexión se protegen al especificar la llamada `passport.authenticate()` mediante el protocolo que desea utilizar.

Puede editar la ruta en el código de servidor para que haga algo más interesante:

```Javascript
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## Ejecución de la aplicación de servidor para comprobar que le rechaza

Puede volver a usar `curl` para ver si ahora tiene la protección de OAuth2 contra sus puntos de conexión. Hágalo antes de ejecutar cualquiera de nuestros SDK de cliente en este punto de conexión. Los encabezados que se devuelven deberían ser suficientes para indicar que está en el camino correcto.

Asegúrese de que la instancia de MongoDB se esté ejecutando:

	$sudo mongodb

Cambie al directorio y utilice `curl`:

	$ cd azuread
	$ node server.js

Pruebe una operación POST básica:

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Un error 401 es la respuesta que desea. Indica que la capa Passport intenta redirigir al punto de conexión de autorización.


## Ahora tiene un servicio de API de REST que usa OAuth2

Ha avanzado todo lo que se puede con este servidor sin usar un cliente compatible con OAuth2. Para eso, necesitará un tutorial adicional.

Si desea simplemente obtener información sobre cómo implementar una API de REST con Restify y OAuth2, tiene suficiente código para poder seguir desarrollando su servicio y construir sobre la base de este ejemplo.

Como referencia, se proporciona el ejemplo finalizado (sin sus valores de configuración) [como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/complete.zip). También puede clonarlo desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```


## Pasos siguientes

Ahora puede pasar a temas más avanzados, como:

[Conexión a una API web mediante iOS con B2C](active-directory-b2c-devquickstarts-ios.md)

<!---HONumber=AcomDC_0302_2016-->