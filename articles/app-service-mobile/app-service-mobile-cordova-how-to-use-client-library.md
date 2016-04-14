<properties
	pageTitle="Uso del complemento de Apache Cordova para Aplicaciones móviles de Azure"
	description="Uso del complemento de Apache Cordova para Aplicaciones móviles de Azure"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="adrianha"/>

# Uso de una biblioteca de cliente de Apache Cordova para Aplicaciones móviles de Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

En esta guía se muestran algunos escenarios comunes del uso del último [complemento de Apache Cordova para Aplicaciones móviles de Azure]. Si no está familiarizado con Aplicaciones móviles de Azure, realice primero el tutorial [Creación de una aplicación de Apache Cordova] para crear un back-end, crear una tabla y descargar un proyecto de Apache Cordova previamente compilado. En esta guía nos centramos en el complemento de Apache Cordova del lado cliente.

##<a name="Setup"></a>Configuración y requisitos previos

En esta guía se asume que ha creado un back-end con una tabla. En esta guía se asume que la tabla tiene el mismo esquema que las tablas de dichos tutoriales. En esta guía también se supone que ha agregado el complemento de Apache Cordova al código. Si no lo ha hecho, puede agregar el complemento Apache Cordova al proyecto en la línea de comandos:

```
cordova plugin add ms-azure-mobile-apps
```

Para más información sobre la creación de [su primera aplicación de Apache Cordova], consulte su documentación.

##<a name="create-client"></a>Creación del cliente

Cree una conexión de cliente mediante la creación de un objeto `WindowsAzure.MobileServicesClient`. Sustituya `appUrl` por la dirección URL a la aplicación móvil.

```
var client = WindowsAzure.MobileServicesClient(appUrl);
```

##<a name="table-reference"></a>Creación de una referencia de tabla

Para acceder a los datos o actualizarlos, cree una referencia a la tabla de back-end. Reemplace `tableName` por el nombre de la tabla.

```
var table = client.getTable(tableName);
```

##<a name="querying"></a>Consulta de una referencia de tabla

Una vez que tiene una referencia de tabla, puede utilizarla para consultar datos en el servidor. Las consultas se realizan en un lenguaje "similar a LINQ". Para devolver todos los datos de la tabla, utilice lo siguiente:

```
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

Se llama a la función success con los resultados. No use `for (var i in results)` en la función success dado que se repetirá información que está incluida en los resultados cuando se utilicen otras funciones de consulta (como `.includeTotalCount()`).

Para más información sobre la sintaxis de consulta, consulte la [documentación de objetos de consulta].

### Filtrado de datos en el servidor

Puede usar una cláusula `where` en la referencia de tabla:

```
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

También puede utilizar una función que filtre el objeto. En este caso, la variable `this` se asigna al objeto actual que se está filtrando. El siguiente código es funcionalmente equivalente al ejemplo anterior:

```
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

### Paginación mediante datos

Utilice los métodos take() y skip(). Por ejemplo, si desea dividir la tabla en registros de 100 filas:

```
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

El método `.includeTotalCount()` se utiliza para agregar un campo totalCount al objeto de resultados. El campo totalCount se rellena con el número total de registros que se devolverían si no se utilizara ninguna paginación.

A continuación, puede usar la variable de páginas y algunos botones de la interfaz de usuario para proporcionar una lista de páginas; utilice loadPage() para cargar los nuevos registros de cada página. Debe implementar algún tipo de almacenamiento en caché para acelerar el acceso a los registros que ya se han cargado.


###<a name="sorting-data"></a>Devolución de los datos ordenados

Utilice los métodos de consulta .orderBy() o .orderByDescending():

```
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

Para más información sobre el objeto de consulta, consulte la [documentación de objetos de consulta].

##<a name="inserting"></a>Insertar datos

Cree un objeto de JavaScript con la fecha adecuada y llame a table.insert() de manera asincrónica:

```
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

Tras la inserción correcta, el elemento insertado se devuelve con los campos adicionales que son necesarios para las operaciones de sincronización. Debe actualizar su propia caché con esta información para actualizaciones posteriores.

Tenga en cuenta que el SDK de servidor de Node.js para Aplicaciones móviles de Azure admite el esquema dinámico con fines de desarrollo. En el caso del esquema dinámico, el esquema de la tabla se actualiza sobre la marcha, lo que permite agregar columnas a la tabla con solo especificarlas en una operación de inserción o de actualización. Se recomienda desactivar el esquema dinámico antes de pasar la aplicación a producción.

##<a name="modifying"></a>Modificación de datos

De forma similar al método .insert(), debe crear un objeto Update y luego llamar a .update(). El objeto Update debe contener el identificador del registro que se va a actualizar; este se obtiene al leer el registro o al llamar a .insert().

```
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

##<a name="deleting"></a>Eliminación de datos

Llame al método .del() para eliminar un registro. Pase el identificador de una referencia de objeto:

```
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```

##<a name="auth"></a>Autenticación de usuarios

El Servicio de aplicaciones de Azure es compatible con la autenticación y la autorización de los usuarios de aplicaciones mediante diversos proveedores de identidades externos: Facebook, Google, Cuenta Microsoft y Twitter. Puede establecer permisos en tablas para restringir el acceso a operaciones específicas solo a usuarios autenticados. También puede usar la identidad de usuarios autenticados para implementar reglas de autorización en scripts del servidor. Para obtener más información, consulte el tutorial [Introducción a la autenticación].

Al utilizar la autenticación en una aplicación de Apache Cordova, los siguientes complementos de Cordova deben estar disponibles:

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

Se admiten dos flujos de autenticación: un flujo de servidor y un flujo de cliente. El flujo de servidor ofrece la experiencia de autenticación más simple, ya que se basa en la interfaz de autenticación web del proveedor. El flujo de cliente permite una mayor integración con capacidades específicas del dispositivo, como el inicio de sesión único, ya que se basa en SDK específicos del dispositivo y específicos del proveedor.

##<a name="server-auth"></a>Autenticación con un proveedor (flujo de servidor)

Para que los Servicios móviles administren el proceso de autenticación en la aplicación de la Tienda Windows o HTML5, debe registrar la aplicación con el proveedor de identidades. A continuación, en el Servicio de aplicaciones de Azure, tendrá que configurar el identificador y el secreto de la aplicación proporcionados por el proveedor. Para obtener más información, consulte el tutorial [Incorporación de la autenticación a su aplicación].

Cuando haya registrado el proveedor de identidades, llame simplemente al método .login() con el nombre de su proveedor. Por ejemplo, para iniciar sesión con Facebook, use el siguiente código:

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Si usa un proveedor de identidades que no sea Facebook, cambie el valor que ha pasado al método de inicio de sesión anterior por uno de los siguientes: `microsoftaccount`, `facebook`, `twitter`, `google` o `aad`.

En este caso, el Servicio de aplicaciones de Azure administra el flujo de autenticación de OAuth 2.0: muestra la página de inicio de sesión del proveedor seleccionado y genera un token de autenticación del Servicio de aplicaciones después de que se realice un inicio de sesión correcto con el proveedor de identidades. La función login, cuando se completa, devuelve un objeto JSON (user) que expone el identificador de usuario y el token de autenticación del Servicio de aplicaciones en los campos userId y authenticationToken, respectivamente. El token puede almacenarse en caché y volver a usarse hasta que expire.

##<a name="client-auth"></a>Autenticación con un proveedor (flujo de cliente)

La aplicación también puede ponerse en contacto de manera independiente con el proveedor de identidades y proporcionar el token devuelto al Servicio de aplicaciones para la autenticación. Este flujo de cliente le permite proporcionar una experiencia de inicio de sesión único para los usuarios o recuperar datos de usuario adicionales del proveedor de identidades.

### Ejemplo básico de autenticación social

Este ejemplo usa el SDK de cliente de Facebook para la autenticación:

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```
En este ejemplo se asume que el token proporcionado por el SDK del proveedor correspondiente se almacena en la variable token.

### Ejemplo de Cuenta Microsoft

El siguiente ejemplo usa el SDK de Live, que admite el inicio de sesión único para aplicaciones de la Tienda Windows mediante la cuenta de Microsoft:

```
WL.login({ scope: "wl.basic"}).then(function (result) {
      client.login(
            "microsoftaccount",
            {"authenticationToken": result.session.authentication_token})
      .done(function(results){
            alert("You are now logged in as: " + results.userId);
      },
      function(error){
            alert("Error: " + err);
      });
});
```

En este ejemplo simplificado se obtiene un token de Live Connect, que se suministra al Servicio de aplicaciones mediante la llamada a la función login.

##<a name="auth-getinfo"></a>Obtención de información sobre el usuario autenticado

Se puede recuperar la información de autenticación del usuario actual desde el punto de conexión `/.auth/me` mediante cualquier método de AJAX. Por ejemplo, para usar la API de captura:

```
var url = client.applicationUrl + '/.auth/me';
fetch(url)
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

También podría utilizar jQuery u otra API de AJAX para capturar la información. Los datos se reciben como un objeto JSON.

##<a name="register-for-push"></a>Registro de notificaciones push

Instale [phonegap-plugin-push] para administrar las notificaciones push. Este complemento se puede agregar fácilmente mediante el comando `cordova plugin add` en la línea de comandos, o por medio del instalador de complementos Git dentro de Visual Studio. El siguiente código de la aplicación de Apache Cordova registrará el dispositivo para notificaciones push:

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Use el SDK de Centros de notificaciones para enviar notificaciones push desde el servidor. Nunca debe enviar notificaciones push directamente desde los clientes ya que esta acción podría usarse para desencadenar un ataque por denegación de servicio contra Centros de notificaciones o el PNS.

<!-- URLs. -->
[Creación de una aplicación de Apache Cordova]: app-service-mobile-cordova-get-started.md
[Introducción a la autenticación]: app-service-mobile-cordova-get-started-users.md
[Incorporación de la autenticación a su aplicación]: app-service-mobile-cordova-get-started-users.md

[complemento de Apache Cordova para Aplicaciones móviles de Azure]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[su primera aplicación de Apache Cordova]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[documentación de objetos de consulta]: https://msdn.microsoft.com/es-ES/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0224_2016-->