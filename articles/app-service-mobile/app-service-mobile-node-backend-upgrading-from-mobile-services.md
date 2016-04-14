<properties
	pageTitle="Actualización de Servicios móviles a Servicio de aplicaciones de Azure - Node.js"
	description="Aprenda a actualizar fácilmente la aplicación de Servicios móviles a una aplicación móvil del Servicio de aplicaciones."
	services="app-service\mobile"
	documentationCenter=""
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile"
	ms.devlang="node"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="chrande"/>

# Actualización del Servicio móvil de Azure de Node.js existente a Servicio de aplicaciones

Aplicaciones móviles del Servicio de aplicaciones es una nueva forma de crear aplicaciones móviles con Microsoft Azure. Para más información, vea [¿Qué es Aplicaciones móviles?].

En este tema se describe cómo actualizar una aplicación back-end de Node.js existente en Servicios móviles de Azure a una nueva instancia de Aplicaciones móviles del Servicio de aplicaciones. Cuando realice esta migración, la aplicación de Servicios móviles existente puede continuar funcionando.

Cuando un back-end móvil se actualiza a Servicio de aplicaciones de Azure, accede a todas las características de Servicio de aplicaciones y se factura conforme a los [precios del Servicio de aplicaciones], no según los precios de Servicios móviles.

## Migración frente a actualización

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP] Se recomienda [realizar una migración](app-service-mobile-migrating-from-mobile-services.md) antes de pasar por una actualización. De este modo, puede colocar las dos versiones de la aplicación en el mismo Plan del Servicio de aplicaciones y no incurrir en ningún coste adicional.

### Mejoras en el SDK de servidor para Node.js de Aplicaciones móviles

La actualización al nuevo [SDK de Aplicaciones móviles](https://www.npmjs.com/package/azure-mobile-apps) ofrece muchas mejoras, entre las que se incluyen:

- Basado en [marco Express](http://expressjs.com/en/index.html), el nuevo SDK para Node es ligero y está diseñado para mantenerse al día con nuevas versiones de Node a medida que salen. Puede personalizar el comportamiento de la aplicación con middleware de Express.

- Ofrece mejoras de rendimiento significativas en comparación con el SDK de Servicios móviles.

- Ahora puede hospedar un sitio web junto con el back-end móvil; asimismo, es fácil agregar el Azure Mobile SDK a cualquier aplicación expressv4 existente.

- Creado para desarrollo multiplataforma y local, el SDK de Aplicaciones móviles se puede desarrollar y ejecutar localmente en plataformas Windows, Linux y OSX. Ahora es fácil usar técnicas de desarrollo comunes de Node como, por ejemplo, ejecutar pruebas [Mocha](https://mochajs.org/) antes de la implementación.

- Puede utilizar Redis con módulos nativos como [hiredis](https://www.npmjs.com/package/hiredis). No es necesario incluir archivos binarios en los paquetes de implementación ya que el Servicio de aplicaciones instalará los paquetes npm de forma automática.

## <a name="overview"></a>Información general básica de actualización

A diferencia del SDK de Aplicaciones móviles para .NET, la actualización de un back-end de Node de Servicios móviles a Aplicaciones móviles no es tan simple como intercambiar paquetes. Ahora posee la pila de toda la aplicación, en lugar de controlarla Azure y, por tanto, tiene que crear una aplicación Express básica para hospedar el back-end móvil. En el caso de la tabla y los controladores de API, los conceptos son similares, pero ahora debe exportar objetos de tabla y las API de función han cambiado ligeramente. Este artículo le guiará a través de las estrategias básicas de la actualización, pero para poder migrar, conviene que lea los [procedimientos de back-end de Node](app-service-mobile-node-backend-how-to-use-server-sdk.md) antes de empezar.

>[AZURE.TIP] Lea y comprenda por completo el resto de este tema antes de iniciar una actualización. Tome nota de cualquier característica usada que se mencione a continuación.

Los SDK de cliente de Servicios móviles **no** son compatibles con el nuevo SDK de servidor de Aplicaciones móviles. A fin de ofrecer continuidad del servicio para la aplicación, no debe publicar cambios en un sitio que actualmente presta servicio a clientes publicados. En su lugar, debe crear una aplicación móvil que actúe como duplicado. Puede colocar esta aplicación en el mismo Plan del Servicio de aplicaciones para evitar incurrir en costos financieros adicionales.

Entonces tendrá dos versiones de la aplicación,: una que permanece igual y presta servicio a aplicaciones publicadas en su estado natural, y otra que después se puede actualizar y destinar a una nueva versión del cliente. Puede mover y probar el código a su ritmo, pero debe asegurarse de que las correcciones de errores se apliquen a ambas. Cuando crea que la cantidad que elija de aplicaciones cliente en estado natural se han actualizado a la versión más reciente, puede eliminar si quiere la aplicación migrada original. No incurre en costos monetarios adicionales, si se hospeda en el mismo Plan del Servicio de aplicaciones que la aplicación móvil.

El esquema completo del proceso de actualización es el siguiente:

1. Crear una aplicación móvil
2. Actualizar el proyecto para usar los nuevos SDK de servidor
3. Publicar el proyecto en la nueva aplicación móvil
4. Publicar una nueva versión de la aplicación cliente que utilice la nueva aplicación móvil
5. (Opcional) Eliminar la aplicación de servicio móvil migrada original

La eliminación puede tener lugar cuando no vea tráfico alguno en la aplicación de servicio móvil migrada original.

## <a name="mobile-app-version"></a> Inicio de la actualización
El primer paso en la actualización es crear el recurso de aplicación móvil que hospedará la nueva versión de la aplicación. Si ya ha migrado un servicio móvil existente, conviene crear esta versión en el mismo plan de hospedaje. Abra el [Portal de Azure] y vaya a la aplicación migrada. Tome nota del Plan del Servicio de aplicaciones en el que se ejecuta.

### Creación de una segunda instancia de aplicación
A continuación, cree una segunda instancia de aplicación. Cuando se le pida que seleccione el Plan del Servicio de aplicaciones o el "plan de hospedaje", elija el plan de la aplicación migrada.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Probablemente deseará usar la misma base de datos y la base de datos central de notificaciones como hizo en Servicios móviles. Para copiar estos valores, abra el [Portal de Azure] y vaya a la aplicación original, luego haga clic en **Configuración** > **Configuración de aplicación**. En **Cadenas de conexión**, copie `MS_NotificationHubConnectionString` y `MS_TableConnectionString`. Navegue al nuevo sitio de actualización y péguelos en él, sobrescribiendo los valores existentes. Repita este proceso para cualquier otra opción de configuración de la aplicación que la aplicación requiera. Si no usa un servicio migrado, puede leer las cadenas de conexión y la configuración de aplicación en la pestaña **Configurar** de la sección Servicios móviles del [Portal de Azure].

### Creación de un back-end de aplicación móvil básico con Node

Cada back-end de Node.js de aplicación móvil del Servicio de aplicaciones de Azure se inicia como una aplicación ExpressJS. Puede crear una aplicación [Express](http://expressjs.com/en/index.html) básica de la forma siguiente:

1. En una ventana de comandos o de PowerShell, cree un nuevo directorio para el proyecto.

        mkdir basicapp

2. Ejecute npm init para inicializar la estructura del paquete.

        cd basicapp
        npm init

    El comando npm init le formulará una serie de preguntas para inicializar el proyecto. Vea el resultado del ejemplo a continuación

    ![La salida de npm init][0]

3. Instalación de las bibliotecas express y azure-mobile-apps desde el repositorio npm.

        npm install --save express azure-mobile-apps

4. Cree un archivo app.js para implementar el servidor móvil básico.

        var express = require('express'),
            azureMobileApps = require('azure-mobile-apps');

        var app = express(),
            mobile = azureMobileApps();

        // Important all tables in the 'tables' directory
        mobile.tables.import('./tables');
        mobile.api.import('./api');

        // Provide initialization of any tables that are statically defined
        mobile.tables.initialize().then(function () {
           // Add the mobile API so it is accessible as a Web API
           app.use(mobile);

           // Start listening on HTTP
           var port = process.env.PORT || 3000;
           app.listen(port, function () {
               console.log('Now listening on ', port)
           });
        });

Para obtener más ejemplos, consulte nuestro [repositorio de GitHub](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples).

## Actualización del proyecto de servidor

El servicio Aplicaciones móviles ofrece un nuevo [SDK de servidor de Aplicaciones móviles] que proporciona prácticamente la misma funcionalidad que el tiempo de ejecución de Servicios móviles, pero ahora usted posee el tiempo de ejecución completo; no le obliga a usar una versión de Node ni ninguna actualización de código. Si ha seguido los pasos anteriores, dispone de una versión básica en tiempo de ejecución de Node Mobile. Ahora puede comenzar a mover las tablas y la lógica de la API de su Servicio móvil a su Aplicación móvil, personalizar la configuración del servidor, habilitar la inserción, configurar la autenticación, etc.

### Configuración base

El servidor cuenta con gran cantidad de opciones de configuración, pero incluye diversos valores predeterminados para que se sea fácil empezar a trabajar. Muchas de las opciones se configurarán automáticamente en el [Portal de Azure], a través los menús de configuración **Datos**, **Autenticación/autorización** e **Inserción**. En el caso del desarrollo local, si desea usar datos, autenticación e inserción, puede que tenga que configurar el entorno de desarrollo local.

Puede definir la configuración del servidor mediante variables de entorno que se establecen a través de Configuración de aplicaciones en el back-end de aplicación móvil.

Si desea personalizar aún más el SDK de Aplicaciones móviles, pase un [objeto de configuración](http://azure.github.io/azure-mobile-apps-node/global.html#configuration) al inicializador o [cree un archivo denominado azureMobile.js](app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-config-localdev) en la raíz del proyecto.

### Trabajo con datos y tablas

El SDK incluye un proveedor de datos en memoria que permite experiencias introductorias fáciles y rápidas. Debe pasar a usar una base de datos SQL cuanto antes, ya que el proveedor en memoria perderá todos los datos en el reinicio y no permanece coherente de una instancia a otra.

Para comenzar a mover la lógica empresarial del Servicio móvil a Aplicaciones móviles, deberá crear primero un archivo con el nombre de la tabla (al que se le añade 'js') en el directorio `./tables`. Puede ver un ejemplo completo de una tabla de Aplicación móvil en [GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/todo/tables/TodoItem.js). La versión más sencilla es:

    var azureMobileApps = require('azure-mobile-apps');

    // Create a new table definition
    var table = azureMobileApps.table();

    module.exports = table;

Para empezar a migrar parte de la lógica, necesitará una función de la tabla para cada uno de los `<tablename>.<operation>.js`. Vamos a agregar una función de lectura para obtener un ejemplo.

En un Servicio móvil con una tabla TodoItem y una operación de lectura que filtra los elementos por userid, similar al siguiente:

    function(query, user, request) {
        query.where({ userId: user.userId});
        request.execute();
    }

La función que agregamos al código de la tabla de Aplicaciones móviles de Azure tendría el siguiente aspecto:

    table.read(function (context) {
        context.query.where({ userId: context.user.id });
        return context.execute();
    });

La consulta, el usuario y la solicitud se combinan en un contexto. Los siguientes campos están disponibles en el objeto de contexto:

| Campo | Tipo | Descripción |
| :------ | :--------------------- | :---------- |
| query | queryjs/Query | La consulta de OData analizada |
| id | cadena o número | El identificador asociado a la solicitud |
| item | objeto | El elemento que se inserta o elimina |
| req | express.Request | El objeto de solicitud rápida actual |
| res | express.Response | El objeto de respuesta rápida actual |
| data | data | El proveedor de datos configurado |
| tables | function | Una función que acepta un nombre de tabla de cadena y devuelve un objeto de acceso de tabla |
| user | auth/user | Nombre del usuario autenticado |
| results | objeto | Los resultados de la operación de ejecución |
| push | NotificationHubService | El servicio de Centros de notificaciones, si se ha configurado |

Para más información, consulte la [documentación actual de la API](http://azure.github.io/azure-mobile-apps-node).

### CORS

CORS se puede habilitar a través de una [opción de configuración de CORS](http://azure.github.io/azure-mobile-apps-node/global.html#corsConfiguration) en el SDK.

El principal motivo de preocupación si se usa CORS es que deben permitirse los encabezados `eTag` y `Location` para que los SDK de cliente funcionen correctamente.

### Notificaciones de inserción

El SDK de Centros de notificaciones de Azure ha tenido algunas actualizaciones importantes desde Servicios móviles, por lo que algunas firmas de función de los Centros de notificaciones pueden ser diferentes. De lo contrario, la funcionalidad es similar a la de Servicios móviles; si existe la configuración de aplicación para Centros de notificaciones, Azure Mobile SDK aprovisionará una instancia de Centros de notificaciones y la expondrá en `context.push`. Puede encontrar un ejemplo en [GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/push-on-insert/tables/TodoItem.js), con la sección correspondiente que se muestra a continuación:

    table.insert(function (context) {
        // For details of the Notification Hubs JavaScript SDK,
        // see https://azure.microsoft.com/es-ES/documentation/articles/notification-hubs-nodejs-how-to-use-notification-hubs/
        logger.silly('Running TodoItem.insert');

        // This push uses a template mechanism, so we need a template/
        var payload = '<toast><visual><binding template="Toast01"><text id="1">INSERT</text></binding></visual></toast>';

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    context.push.wns.send(null, payload, 'wns/toast', function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.silly('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
    });


### Trabajos programados
Los trabajos programados no están integrados en las aplicaciones móviles, por lo que tendrá que actualizar individualmente los trabajos existentes que tenga en el back-end de Servicios móviles. Una opción es crear un [trabajo web] programado en el sitio del código de aplicación móvil. También puede configurar una API que contenga el código de trabajo y configurar el [Programador de Azure] para llegar a ese punto de conexión en la programación prevista.

## <a name="authentication"></a>Consideraciones de autenticación

Ahora, los componentes de autenticación de Servicios móviles se han movido a la característica Autenticación/autorización de Servicio de aplicaciones. Para aprender a habilitar esta característica para el sitio, lea el tema [Incorporación de autenticación a una aplicación móvil](app-service-mobile-ios-get-started-users.md).

Con algunos proveedores, como AAD, Facebook y Google, podrá aprovechar el registro existente de la aplicación de copia. Basta con ir al portal del proveedor de identidades y agregar una nueva dirección URL de redirección al registro. Seguidamente, configure Autenticación/autorización de Servicio de aplicaciones con el identificador y el secreto del cliente.

### Entidad de usuario y autorización de acción de controlador

Para limitar el acceso a la tabla, puede establecerlo en el nivel de tabla con `table.access = 'authenticated';`. Puede ver un ejemplo completo en [GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/authentication/tables/TodoItem.js).

Puede acceder a la información de identidad de usuario a través del método `user.getIdentity` que se describe [aquí](http://azure.github.io/azure-mobile-apps-node/module-azure-mobile-apps_auth_user.html#~getIdentity).

## <a name="updating-clients"></a>Actualización de clientes
Cuando tenga un back-end de aplicación móvil operativo, podrá trabajar en una nueva versión de la aplicación cliente que lo usa. Aplicaciones móviles incluye también una nueva versión de los SDK de cliente y, de forma similar a la actualización del servidor anterior, tendrá que quitar todas las referencias a los SDK de Servicios móviles antes de instalar las versiones de Aplicaciones móviles.

Uno de los principales cambios entre las versiones es que los constructores ya no requieren una clave de aplicación. Ahora basta con pasar la dirección URL de la aplicación móvil. Por ejemplo, en los clientes .NET, el constructor `MobileServiceClient` es ahora:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Encontrará información sobre cómo instalar los nuevos SDK y cómo usar la nueva estructura a través de los vínculos siguientes:

- [iOS versión 3.0.0 o posterior](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) versión 2.0.0 o posterior](app-service-mobile-dotnet-how-to-use-client-library.md)

Si la aplicación hace uso de notificaciones de inserción, tome nota de las instrucciones de registro específicas para cada plataforma, ya que también ha habido algunos cambios al respecto.

Cuando tenga la nueva versión de cliente lista, pruébela en el proyecto de servidor actualizado. Después de comprobar que funciona, puede publicar una nueva versión de la aplicación para clientes. Al final, cuando los clientes hayan tenido ocasión de recibir estas actualizaciones, puede eliminar la versión de Servicios móviles de su aplicación. Llegados a este punto, ha actualizado completamente a una aplicación móvil del Servicio de aplicaciones mediante el SDK de servidor de Aplicaciones móviles más reciente.

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png

<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[¿Qué es Aplicaciones móviles?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /es-ES/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[SDK de servidor de Aplicaciones móviles]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Programador de Azure]: /es-ES/documentation/services/scheduler/
[trabajo web]: ../app-service-web/websites-webjobs-resources.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[precios del Servicio de aplicaciones]: https://azure.microsoft.com/es-ES/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md

[Portal de Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/es-ES/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/es-ES/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

<!---HONumber=AcomDC_0211_2016-->