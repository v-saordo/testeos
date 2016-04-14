<properties
	pageTitle="Cómo trabajar con el SDK del servidor back-end de Node.js para Aplicaciones móviles | Servicio de aplicaciones de Azure"
	description="Obtenga información sobre cómo trabajar con el SDK del servidor back-end de Node.js para Aplicaciones móviles del Servicio de aplicaciones de Azure."
	services="app-service\mobile"
	documentationCenter=""
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="node"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="adrianhall"/>

# Uso del SDK de Node.js de Aplicaciones móviles de Azure

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

En este artículo se ofrece información detallada y ejemplos sobre cómo trabajar con un back-end de Node.js en Aplicaciones móviles del Servicio de aplicaciones de Azure.

## <a name="Introduction"></a>Introducción

Aplicaciones móviles del Servicio de aplicaciones de Azure proporciona la funcionalidad de agregar una API web de acceso a datos optimizada para móviles a una aplicación web. El SDK de Aplicaciones móviles del Servicio de aplicaciones de Azure se proporciona para las aplicaciones web de ASP.NET y Node.js. El SDK proporciona las siguientes operaciones:

- Operaciones de tabla (lectura, inserción, actualización, eliminación) para el acceso a datos
- Operaciones API personalizadas

Ambas operaciones se incluyen para autenticación en todos los proveedores de identidades permitidos por el Servicio de aplicaciones de Azure, incluidos los proveedores de identidades sociales, como Facebook, Twitter, Google y Microsoft, así como Azure Active Directory para la identidad de empresa.

Puede encontrar ejemplos para cada caso de uso en el [directorio de ejemplos de GitHub].

### <a name="howto-cmdline-basicapp"></a>Creación de un back-end de Node.js básico mediante la línea de comandos

Cada back-end de Node.js de aplicación móvil del Servicio de aplicaciones de Azure se inicia como una aplicación ExpressJS. ExpressJS es el marco del servicio web más popular disponible para Node.js. Puede crear una aplicación [Express] básica de la forma siguiente:

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

		// Define a TodoItem table
		mobile.tables.add('TodoItem');

		// Add the mobile API so it is accessible as a Web API
		app.use(mobile);

		// Start listening on HTTP
		app.listen(process.env.PORT || 3000);

Esta aplicación crea una WebAPI sencilla optimizada para móviles con un único punto de conexión (`/tables/TodoItem`) que proporciona el acceso no autenticado a un almacén de datos SQL subyacente mediante un esquema dinámico. Es adecuado para los siguientes inicios rápidos de la biblioteca de cliente:

- [Inicio rápido de cliente de Android]
- [Inicio rápido de cliente de Apache Cordova]
- [Inicio rápido de cliente de iOS]
- [Inicio rápido de cliente de Windows]
- [Inicio rápido de cliente de Xamarin.iOS]
- [Inicio rápido de cliente de Xamarin.Android]
- [Inicio rápido de cliente de Xamarin.Forms]


Puede encontrar el código de esta aplicación básica en el [ejemplo "basicapp" en GitHub].

### <a name="howto-vs2015-basicapp"></a>Creación de un back-end de Node con Visual Studio de 2015

Visual Studio 2015 requiere una extensión para desarrollar aplicaciones Node.js en el IDE. Para comenzar, descargue e instale [Node.js Tools 1.1 para Visual Studio]. Una vez instalado Node.js Tools para Visual Studio, cree una aplicación Express 4.x:

1. Abra el cuadro de diálogo **Nuevo proyecto** (desde **Archivo** > **Nuevo** > **Proyecto...**).

2. Expanda **Plantillas** > **JavaScript** > **Node.js**.

3. Seleccione **Basic Azure Node.js Express 4 Application**.

4. Rellene el nombre del proyecto. Haga clic en *Aceptar*.

	![Nuevo proyecto de Visual Studio de 2015][1]

5. Haga clic con el botón derecho en el nodo **npm** y seleccione **Instalar nuevos paquetes npm...**

6. Quizá tenga que actualizar el catálogo npm al crear su primera aplicación de Node.js. En caso de ser necesario, se le pedirá que lo haga; para ello, haga clic en **Actualizar**.

7. Escriba _azure-mobile-apps_ en el cuadro de búsqueda. Haga clic en el paquete **azure-mobile-apps 2.0.0** y haga clic en **Instalar paquete**.

	![Instalar nuevos paquetes npm][2]

8. Haga clic en **Cerrar**.

9. Abra el archivo _app.js_ para agregar compatibilidad con el SDK de Aplicaciones móviles de Azure. En la línea 6 al final de las instrucciones requiere de la biblioteca, agregue el código siguiente:

        var bodyParser = require('body-parser');
        var azureMobileApps = require('azure-mobile-apps');

    Aproximadamente en línea 27 después de las demás instrucciones app.use, agregue el código siguiente:

        app.use('/users', users);

        // Azure Mobile Apps Initialization
        var mobile = azureMobileApps();
        mobile.tables.add('TodoItem');
        app.use(mobile);

    Guarde el archivo .

10. Ejecute la aplicación localmente (la API se proporcionará en http://localhost:3000) o publíquela en Azure.

### <a name="download-quickstart"></a>Descarga del proyecto de código de inicio rápido de un back-end de Node.js mediante Git

Al crear un nuevo back-end de aplicación móvil de Node.js mediante la hoja **Inicio rápido** del portal, se crea automáticamente un nuevo proyecto de Node.js y se implementa en su sitio. Puede agregar tablas y API, así como editar archivos de código para el back-end de Node.js en el portal. Puede utilizar igualmente cualquiera de las herramientas de implementación existentes para descargar el proyecto de back-end a fin de agregar o modificar tablas y API, y publicar el proyecto de nuevo. Para obtener más información, consulte la [Guía de implementación del Servicio de aplicaciones de Azure]. El siguiente procedimiento usa un repositorio de Git para descargar el código del proyecto de inicio rápido.

1. Si aún no lo ha hecho, instale Git. Los pasos requeridos para instalar Git varían según los sistemas operativos. Consulte [Installing Git](http://git-scm.com/book/en/Getting-Started-Installing-Git) para obtener una guía sobre la instalación y las distribuciones específicas del sistema operativo.

2. Siga los pasos de [Habilitación del repositorio de aplicaciones web](../app-service-web/web-sites-publish-source-control.md#Step4) a fin de habilitar el repositorio de Git para el sitio del back-end, y tome nota del nombre de usuario y de la contraseña de la implementación.

3. En la hoja para el back-end de la aplicación móvil, tome nota del valor de **URL de clonación de Git**.

4.  Ejecute el comando `git clone` en una herramienta de línea de comandos compatible con Git mediante la URL de clonación de Git e introduzca la contraseña cuando sea necesario, como en el ejemplo siguiente:

		$ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. Desplácese al directorio local, que en el ejemplo anterior es /todolist, y observe si se han descargado archivos del proyecto. En la subcarpeta /tables, encontrará el archivo todoitem.json, que define permisos en la tabla, y el archivo todoitem.js, que define los scripts de operaciones CRUD para la tabla.

6. Después de realizar cambios en archivos del proyecto, ejecute los siguientes comandos para agregar, confirmar y cargar los cambios en el sitio:

		$ git commit -m "updated the table script"
		$ git push origin master

	Al agregar nuevos archivos al proyecto, primero debe ejecutar el comando `git add .`.

Cada vez que se inserta un nuevo conjunto de confirmaciones en el sitio, se vuelve a publicar el sitio.

### <a name="howto-publish-to-azure"></a>Publicación del back-end de Node.js en Azure

Microsoft Azure proporciona varios mecanismos para publicar su back-end de Node.js de Aplicaciones móviles del Servicio de aplicaciones de Azure en el servicio de Azure. Incluyen el uso de herramientas de implementación integradas en Visual Studio, herramientas de línea de comandos y opciones de implementación continua basadas en control de código fuente. Para más información sobre este tema, consulte la [Guía de implementación del Servicio de aplicaciones de Azure].

El Servicio de aplicaciones de Azure tiene instrucciones específicas para la aplicación de Node.js que usted debe revisar antes de realizar la implementación:

- [Especificación de una versión de Node.js en una aplicación Azure]
- [Uso de módulos de Node]

### <a name="howto-enable-homepage"></a>Habilitación de una página de inicio para la aplicación

Muchas aplicaciones son una combinación de aplicaciones web y móviles, y el marco de trabajo ExpressJS le permite combinar las dos facetas. Sin embargo, es posible que en ocasiones solo quiera implementar una interfaz móvil. Es útil proporcionar una página de aterrizaje para garantizar que el servicio de aplicaciones está en funcionamiento. Puede proporcionar su propia página de inicio o habilitar una de carácter temporal. Para habilitar una página de inicio temporal, ajuste el constructor de aplicación móvil al siguiente valor:

    var mobile = azureMobileApps({ homePage: true });

Puede agregar esta configuración al archivo `azureMobile.js` si solo desea que esta opción esté disponible al desarrollar de forma local.

## <a name="TableOperations"></a>Operaciones de tabla

El SDK del servidor de Node.js de azure-mobile-apps proporciona mecanismos para exponer las tablas de datos almacenadas en Base de datos SQL de Azure como una WebAPI. Se proporcionan cinco operaciones.

| Operación | Descripción |
| --------- | ----------- |
| GET /tables/\_tablename\_ | Obtener todos los registros de la tabla |
| GET /tables/\_tablename\_/:id | Obtener un registros específico de la tabla |
| POST /tables/\_tablename\_ | Crear un nuevo registro en la tabla |
| PATCH /tables/\_tablename\_/:id | Actualizar un registro existente en la tabla |
| DELETE /tables/\_tablename\_/:id | Eliminar un registro de la tabla |

Esta WebAPI admite [OData] y amplía el esquema de tabla para admitir la [sincronización de datos sin conexión].

### <a name="howto-dynamicschema"></a>Definición de tablas con un esquema dinámico

Antes de usar una tabla, esta debe definirse. Las tablas pueden definirse con un esquema estático (en el que el desarrollador define las columnas en el esquema) o dinámicamente (en el que el SDK controla el esquema según las solicitudes entrantes). Además, el desarrollador puede controlar aspectos específicos de la WebAPI agregando código Javascript a la definición.

Como procedimiento recomendado, debe definir cada tabla en un archivo de Javascript en el directorio de tablas y luego usar el método tables.import() para importar las tablas. Al ampliar la aplicación básica, el archivo app.js debe ajustarse:

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
	    mobile = azureMobileApps();

    // Define the database schema that is exposed
    mobile.tables.import('./tables');

    // Provide initialization of any tables that are statically defined
    mobile.tables.initialize().then(function () {
        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);

        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);
    });

Defina la tabla en ./tables/TodoItem.js:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Additional configuration for the table goes here

    module.exports = table;

Las tablas usan el esquema dinámico de forma predeterminada. Para desactivar globalmente el esquema dinámico, establezca la configuración de la aplicación **MS\_DynamicSchema** en false en el Portal de Azure.

Puede encontrar un ejemplo completo en el [ejemplo "todo" en GitHub].

### <a name="howto-staticschema"></a>Definición de tablas con un esquema estático

Puede definir explícitamente las columnas que desea exponer a través de la WebAPI. El SDK de Node.js de azure-mobile-apps agregará automáticamente todas las columnas adicionales necesarias para la sincronización de datos sin conexión a la lista que se proporcione. Por ejemplo, las aplicaciones de cliente de inicio rápido requieren una tabla con dos columnas: text (una cadena) y complete (un booleano). Esto se puede definir en el archivo JavaScript de definición de la tabla (ubicado en el directorio de tablas) de la forma siguiente:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    module.exports = table;

Si define las tablas estáticamente, también debe llamar al método tables.initialize() para crear el esquema de base de datos en el inicio. El método tables.initialize() devuelve [Promise], que se usa para asegurarse de que el servicio web no atienda solicitudes antes de que la base de datos se inicialice.

### <a name="howto-sqlexpress-setup"></a>Uso de SQL Express como almacén de datos de desarrollo en el equipo local

El SDK de Node de Aplicaciones móviles de Azure proporciona tres opciones de fábrica para servir datos:

- Use el controlador **memory** para proporcionar un almacén de ejemplos no persistente
- Utilice el controlador **mssql** para ofrecer un almacén de datos SQL Express para desarrollo
- Use el controlador **mssql** para ofrecer un almacén de datos de Base de datos SQL de Azure para producción

El SDK de Node.js de Aplicaciones móviles de Azure usa el [paquete de mssql para Node.js] para establecer y usar una conexión tanto a SQL Express como a Base de datos SQL. Este paquete requiere que habilite las conexiones TCP en la instancia de SQL Express.

> [AZURE.TIP] El controlador memory no proporciona un conjunto completo de servicios para la realización de pruebas. Si desea probar el back-end localmente, se recomienda el uso de un almacén de datos de SQL Express y del controlador mssql.

1. Descargue e instale [Microsoft SQL Server 2014 Express]. Asegúrese de instalar la edición SQL Server 2014 Express con Tools. A menos que necesite explícitamente compatibilidad con 64 bits, la versión de 32 bits consumirá menos memoria cuando se ejecuta.

2. Ejecute el Administrador de configuración de SQL Server 2014.

  1. Expanda el nodo **Configuración de red de SQL Server** en el menú de árbol de la izquierda.
  2. Haga clic en **Protocolos para SQLEXPRESS**.
  3. Haga clic con el botón derecho en **TCP/IP** y seleccione **Habilitar**. Haga clic en **Aceptar** en el cuadro de diálogo emergente.
  4. Haga clic con el botón derecho en **TCP/IP** y seleccione **Propiedades**.
  5. Haga clic en la pestaña **Direcciones IP**.
  6. Busque el nodo **IPAll**. En el campo **Puerto TCP**, escriba **1433**.

	 	 ![Configure SQL Express for TCP/IP][3]
  7. Haga clic en **Aceptar**. Haga clic en **Aceptar**, en el cuadro de diálogo emergente.
  8. Haga clic en **Servicios de SQL Server** en el menú de árbol de la izquierda.
  9. Haga clic con el botón derecho en **SQL Server (SQLEXPRESS)** y seleccione **Reiniciar**.
  10. Cierre el Administrador de configuración de SQL Server 2014.

3. Ejecute SQL Server 2014 Management Studio y conéctese a la instancia local de SQL Express.

  1. Haga clic con el botón derecho en la instancia del Explorador de objetos y seleccione **Propiedades**.
  2. Seleccione la página **Seguridad**.
  3. Asegúrese de que el **Modo de autenticación de Windows y SQL Server** está seleccionado.
  4. Haga clic en **Aceptar**.

  		![Configure SQL Express Authentication][4]

  5. Expanda **Seguridad** > **Inicios de sesión** en el Explorador de objetos
  6. Haga clic con el botón derecho en **Inicios de sesión** y seleccione **Nuevo inicio de sesión...**
  7. Escriba un nombre de inicio de sesión. Seleccione **Autenticación de SQL Server**. Escriba una contraseña y después escriba la misma contraseña en **Confirmar contraseña**. Tenga en cuenta que la contraseña debe cumplir los requisitos de complejidad de Windows.
  8. Haga clic en **Aceptar**.

  		![Add a new user to SQL Express][5]

  9. Haga clic con el botón derecho en el nuevo inicio de sesión y seleccione **Propiedades**.
  10. Seleccione la página **Roles del servidor**.
  11. Active la casilla que se encuentra junto al rol del servidor **dbcreator**.
  12. Haga clic en **Aceptar**.
  13. Cierre SQL Server 2015 Management Studio.

Asegúrese de que registrar el nombre de usuario y la contraseña que seleccionó. Puede que necesite asignar permisos o roles de servidor adicionales dependiendo de los requisitos específicos de la base de datos.

La aplicación de Node.js leerá la variable de entorno **SQLCONNSTR\_MS\_TableConnectionString** para leer la cadena de conexión para esta base de datos. Se puede establecer en el entorno. Por ejemplo, puede usar PowerShell para establecer esta variable de entorno:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Tenga en cuenta que debe tener acceso a la base de datos a través de una conexión TCP/IP y proporcionar un nombre de usuario y una contraseña para la conexión.

### <a name="howto-config-localdev"></a>Configuración del proyecto para el desarrollo local

Aplicaciones móviles de Azure lee un archivo de JavaScript denominado _azureMobile.js_ del sistema de archivos local. No debería usar este archivo para configurar el SDK de Aplicaciones móviles de Azure en producción; use en su lugar la configuración de la aplicación dentro del [Portal de Azure]. El archivo _azureMobile.js_ debe exportar un objeto de configuración. La configuración más común es la siguiente:

- Database Settings
- Configuración del registro de diagnóstico
- Configuración de CORS alternativa

A continuación se muestra un archivo de ejemplo _azureMobile.js_ que implementa la configuración de la base de datos anterior:

    module.exports = {
        cors: {
            origins: [ 'localhost' ]
        },
        data: {
            provider: 'mssql',
            server: '127.0.0.1',
            database: 'mytestdatabase',
            user: 'azuremobile',
            password: 'T3stPa55word'
        },
        logging: {
            level: 'verbose'
        }
    };

Se recomienda que agregue _azureMobile.js_ al archivo _.gitignore_ (o a otro archivo de omisiones de control de código fuente) para evitar que las contraseñas se almacenen en la nube. Configure siempre los valores de producción en la configuración de la aplicación dentro del [Portal de Azure].

### <a name="howto-appsettings"></a>Configuración de aplicaciones para configurar la aplicación móvil

La mayoría de las opciones de configuración del archivo _azureMobile.js_ tiene una configuración de aplicación equivalente en el [Portal de Azure]. Para configurar la aplicación en Configuración de aplicaciones, use la siguiente lista:

| Configuración de aplicación | Configuración de _azureMobile.js_ | Descripción | Valores válidos |
| :-------------------------- | :------------------------ | :---------------------------------------- | :------------------------------------------ |
| **MS\_MobileAppName** | name | Nombre de la aplicación | cadena |
| **MS\_MobileLoggingLevel** | logging.level | Nivel mínimo de registro de mensajes en el registro | error, advertencia, información, detallado, depuración, absurdo |
| **MS\_DebugMode** | debug | Habilitar o deshabilitar el modo de depuración | true, false |
| **MS\_TableSchema** | data.schema | Nombre del esquema predeterminado para tablas SQL | cadena (valor predeterminado: dbo) |
| **MS\_DynamicSchema** | data.dynamicSchema | Habilitar o deshabilitar el modo de depuración | true, false |
| **MS\_DisableVersionHeader** | version (establecida en undefined)| Deshabilita el encabezado X-ZUMO-Server-Version | true, false |
| **MS\_SkipVersionCheck** | skipversioncheck | Deshabilita la comprobación de la versión de API de cliente | true, false |

Para establecer una configuración de aplicación:

1. Inicie sesión en el [Portal de Azure].
2. Seleccione **Todos los recursos** o **Servicios de aplicaciones** y haga clic en el nombre de la aplicación móvil.
3. Se abrirá la hoja Configuración de forma predeterminada, si no, haga clic en **Configuración**.
4. Haga clic en **Configuración de la aplicación** en el menú GENERAL.
5. Desplácese hasta la sección Configuración de aplicación.
6. Si la configuración de la aplicación ya existe, haga clic en el valor de configuración de la aplicación para editarlo.
7. Si no existe, escriba la configuración de aplicación en el cuadro Clave y el valor en el cuadro Valor.
8. Cuando termine, haga clic en **Guardar**.

Si cambia la mayoría de las opciones de configuración de la aplicación será necesario reiniciar el servicio.

### <a name="howto-use-sqlazure"></a>Uso de Base de datos SQL como almacén de datos de producción

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

El uso de Base de datos SQL de Azure como almacén de datos es idéntico en todos los tipos de aplicaciones del Servicio de aplicaciones de Azure. Si todavía no lo ha hecho, siga estos pasos para crear un nuevo back-end de aplicación móvil.

1. Inicie sesión en el [Portal de Azure].

2. En la esquina superior izquierda de la ventana, haga clic en el botón **+NUEVO** > **Web y móvil** > **Aplicación móvil** e indique un nombre para el back-end de la aplicación móvil.

3. En el cuadro **Grupo de recursos**, escriba el mismo nombre de la aplicación.

4. Se seleccionará el Plan del Servicio de aplicaciones predeterminado. Si desea cambiar el Plan del Servicio de aplicaciones, haga clic en el Plan del Servicio de aplicaciones > **+ Crear nuevo**. Proporcione un nombre al Plan del Servicio de aplicaciones nuevo y seleccione una ubicación adecuada. Haga clic en el nivel de precios y seleccione un nivel de precios adecuado para el servicio. Seleccione **Ver todos** para ver más opciones de precios, como **Gratis** y **Compartido**. Una vez seleccionado el plan de tarifa, haga clic en el botón **Seleccionar**. De nuevo en la hoja **Plan del Servicio de aplicaciones**, haga clic en **Aceptar**.

5. Haga clic en **Crear**. Se crea un back-end de la aplicación móvil, donde implementará después el proyecto de servidor. El aprovisionamiento de un back-end de la aplicación móvil puede tardar unos minutos. Cuando se aprovisiona el back-end de la aplicación móvil, el portal abrirá la hoja **Configuración** del back-end de la aplicación móvil.

Una vez creado el back-end de la aplicación móvil, puede conectar una base de datos SQL al back-end de la aplicación móvil o bien crear una nueva base de datos SQL. En esta sección, creará una nueva base de datos SQL.

> [AZURE.NOTE] Si ya hay una base de datos en la misma ubicación que el nuevo back-end de la aplicación móvil, puede elegir **Usar una base de datos existente** y seleccionar la base de datos. No se recomienda el uso de una base de datos en una ubicación diferente debido a los costos adicionales de ancho de banda y las elevadas latencias.

6. En el nuevo back-end de la aplicación móvil, haga clic en **Configuración** > **Aplicación móvil** > **Datos** > **+Agregar**.

7. En la hoja **Agregar conexión de datos**, haga clic en **Base de datos SQL - Configurar los valores obligatorios** > **Crear una base de datos nueva**. Escriba el nombre de la base de datos nueva en el campo **Nombre**.

8. Haga clic en **Servidor**. En la hoja **Nuevo servidor**, escriba un nombre de servidor único en el campo **Nombre del servidor** y proporcione un **Inicio de sesión del administrador del servidor** y una **Contraseña** adecuados. Asegúrese de que **Permitir que los servicios de Azure accedan al servidor** está activado. Haga clic en **Aceptar**.

	![Creación de una Base de datos SQL de Azure][6]

9. En la hoja **Nueva base de datos**, haga clic en **Aceptar**.

10. En la hoja **Agregar conexión de datos**, seleccione **Cadena de conexión** y especifique el inicio de sesión y la contraseña que proporcionó al crear la base de datos. Si usa una base de datos existente, indique las credenciales de inicio de sesión de esa base de datos. Cuando las especifique, haga clic en **Aceptar**.

11. De nuevo en la hoja **Agregar conexión de datos**, haga clic en **Aceptar** para crear la base de datos.

<!--- END OF ALTERNATE INCLUDE -->

La creación de la base de datos puede tardar unos minutos. Use el área de **notificaciones** para supervisar el progreso de la implementación. No continúe hasta que la base de datos se haya implementado correctamente. Una vez implementada correctamente, se creará una cadena de conexión para la instancia de Base de datos SQL en la configuración de la aplicación de back-end móvil. Puede ver la configuración de esta aplicación en **Configuración** > **Configuración de la aplicación** > **Cadenas de conexión**.

### <a name="howto-tables-auth"></a>Autenticación necesaria para el acceso a las tablas

Si quiere usar la autenticación del Servicio de aplicaciones con el punto de conexión de tablas, tiene que configurar primero la autenticación del Servicio de aplicaciones en el [Portal de Azure]. Para obtener más información sobre cómo configurar la autenticación en un Servicio de aplicaciones de Azure, revise la Guía de configuración del proveedor de identidades que pretende usar:

- [Configuración de la aplicación para usar el inicio de sesión de Azure Active Directory]
- [Configuración de la aplicación para usar el inicio de sesión de Facebook]
- [Configuración de la aplicación para usar el inicio de sesión de Google]
- [Configuración de la aplicación para usar el inicio de sesión de Microsoft]
- [Configuración de la aplicación para usar el inicio de sesión de Twitter]

Cada tabla tiene una propiedad de acceso que se puede usar para controlar el acceso a la tabla. En el ejemplo siguiente se muestra una tabla definida estáticamente con la autenticación necesaria.

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

La propiedad de acceso puede tomar uno de tres valores

  - *anonymous* indica que la aplicación cliente puede leer los datos sin autenticación
  - *authenticated* indica que la aplicación cliente tiene que enviar un token de autenticación válido con la solicitud
  - *disabled* indica que esta tabla está deshabilitada actualmente

Si la propiedad de acceso no está definida, se permite el acceso no autenticado.

### <a name="howto-tables-getidentity"></a>Uso de notificaciones de autenticación con las tablas

Puede configurar un número de notificaciones que se solicitan cuando se configura la autenticación. Estas notificaciones no suelen estar disponibles a través del objeto `context.user`. Sin embargo, se pueden recuperar mediante el método `context.user.getIdentity()`. El método `getIdentity()` devuelve una promesa que se resuelve en un objeto. El objeto tiene como clave el método de autenticación (facebook, google, twitter, microsoftaccount o aad).

Por ejemplo, si establece la autenticación mediante una cuenta Microsoft y solicita la notificación de direcciones de correo electrónico, puede agregar la dirección de correo electrónico al registro con la información siguiente:

    var azureMobileApps = require('azure-mobile-apps');

    // Create a new table definition
    var table = azureMobileApps.table();

    table.columns = {
        "emailAddress": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;
    table.access = 'authenticated';

    /**
    * Limit the context query to those records with the authenticated user email address
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function queryContextForEmail(context) {
        return context.user.getIdentity().then((data) => {
            context.query.where({ emailAddress: data.microsoftaccount.claims.emailaddress });
            return context.execute();
        });
    }

    /**
    * Adds the email address from the claims to the context item - used for
    * insert operations
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function addEmailToContext(context) {
        return context.user.getIdentity().then((data) => {
            context.item.emailAddress = data.microsoftaccount.claims.emailaddress;
            return context.execute();
        });
    }

    // Configure specific code when the client does a request
    // READ - only return records belonging to the authenticated user
    table.read(queryContextForEmail);

    // CREATE - add or overwrite the userId based on the authenticated user
    table.insert(addEmailToContext);

    // UPDATE - only allow updating of record belong to the authenticated user
    table.update(queryContextForEmail);

    // DELETE - only allow deletion of records belong to the authenticated uer
    table.delete(queryContextForEmail);

    module.exports = table;

Para ver qué notificaciones están disponibles, use un explorador web para ver el punto de conexión `/.auth/me` de su sitio.

### <a name="howto-tables-disabled"></a>Deshabilitación del acceso a operaciones de tabla específicas

Además de aparecer en la tabla, la propiedad de acceso puede usarse para controlar operaciones individuales. Hay cuatro operaciones:

  - *read* es la operación GET de RESTful en la tabla
  - *insert* es la operación POST de RESTful en la tabla
  - *update* es la operación PATCH de RESTful en la tabla
  - *delete* es la operación DELETE de RESTful en la tabla

Por ejemplo, es posible que quiera proporcionar una tabla de solo lectura no autenticada. Esto puede indicar en la definición de tabla siguiente:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Read-Only table - only allow READ operations
    table.read.access = 'anonymous';
    table.insert.access = 'disabled';
    table.update.access = 'disabled';
    table.delete.access = 'disabled';

    module.exports = table;

### <a name="howto-tables-query"></a>Ajuste de la consulta que se usa con las operaciones de tabla

Un requisito común para las operaciones de tabla consiste en proporcionar una vista restringida de los datos. Por ejemplo, puede proporcionar una tabla que esté etiquetada con el identificador del usuario autenticado, como que el usuario solo pueda leer o actualizar sus propios registros. La definición de la tabla siguiente proporcionará esta funcionalidad:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define a static schema for the table
    table.columns = {
        "userId": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;

    // Require authentication for this table
    table.access = 'authenticated';

    // Ensure that only records for the authenticated user are retrieved
    table.read(function (context) {
		context.query.where({ userId: context.user.id });
		return context.execute();
	});

    // When adding records, add or overwrite the userId with the authenticated user
    table.insert(function (context) {
	    context.item.userId = context.user.id;
	    return context.execute();
    }

    module.exports = table;

Las operaciones que normalmente ejecutan una consulta tendrán una propiedad de consulta que se puede ajustar con una cláusula where. La propiedad de consulta es un objeto [QueryJS] que se usa para convertir una consulta de OData en algo que puede procesar el back-end de datos. En los casos de igualdad simple (como el anterior), puede usarse un mapa. También es relativamente fácil agregar cláusulas SQL específicas:

    context.query.where('myfield eq ?', 'value');

### <a name="howto-tables-softdelete"></a>Configuración de una eliminación temporal en una tabla

La eliminación temporal no elimina realmente los registros. Los marca como eliminados dentro de la base de datos al establecer la columna de eliminados en true. El SDK de Aplicaciones móviles de Azure quita automáticamente los registros temporalmente eliminados de los resultados, a menos que el SDK de cliente móvil use IncludeDeleted(). Para configurar una tabla para la eliminación temporal, establezca la propiedad softDelete en el archivo de definición de tabla. Un ejemplo podría ser:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
		"complete": "boolean"
	};

	// Turn off dynamic schema
	table.dynamicSchema = false;

	// Turn on Soft Delete
	table.softDelete = true;

	// Require authentication to access the table
	table.access = 'authenticated';

	module.exports = table;

Debe establecer un mecanismo para depurar registros, ya sea desde una aplicación cliente a través de un WebJob o mediante un mecanismo personalizado.

### <a name="howto-tables-seeding"></a>Inicialización de la base de datos con datos

Al crear una nueva aplicación, puede inicializar una tabla con datos. Esto puede hacerse en el archivo JavaScript de definición de tabla de la forma siguiente:

	var azureMobileApps = require('azure-mobile-apps');

	var table = azureMobileApps.table();

	// Define the columns within the table
	table.columns = {
		"text": "string",
		"complete": "boolean"
	};
	table.seed = [
		{ text: 'Example 1', complete: false },
		{ text: 'Example 2', complete: true }
	];

	// Turn off dynamic schema
	table.dynamicSchema = false;

	// Require authentication to access the table
	table.access = 'authenticated';

	module.exports = table;

Es importante tener en cuenta que la inicialización de datos se ha realizado únicamente cuando se crea la tabla por el SDK de Aplicaciones móviles de Azure. Si la tabla ya existe en la base de datos, no se inserta ningún dato en la tabla. Si el esquema dinámico está activado, se deducirá el esquema de los datos inicializados.

Se recomienda llamar explícitamente al método initialize() para crear la tabla cuando el servicio comienza a ejecutarse.

### <a name="Swagger"></a>Habilitación de la compatibilidad con Swagger

Las Aplicaciones móviles del Servicio de aplicaciones de Azure incorporan compatibilidad con [Swagger]. Para habilitar la compatibilidad con Swagger, instale primero el archivo swagger-ui como una dependencia:

    npm install --save swagger-ui

Una vez instalado, puede habilitar la compatibilidad con Swagger en el constructor de Aplicaciones móviles de Azure:

    var mobile = azureMobileApps({ swagger: true });

Es probable que solo quiera habilitar la compatibilidad con Swagger en las ediciones de desarrollo. Puede hacerlo mediante la configuración de aplicación `NODE_ENV`:

    var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });

El punto de conexión de Swagger se ubicará en http://_yoursite_.azurewebsites.net/swagger. Puede acceder a la interfaz de usuario de Swagger a través del punto de conexión `/swagger/ui`. Tenga en cuenta que si elige pedir autenticación en la aplicación entera, Swagger produce un error en el punto de conexión /. Para obtener los mejores resultados, permita que pasen las solicitudes no autenticadas en la configuración de autenticación y autorización del Servicio de aplicaciones de Azure y, luego, controle la autenticación mediante la propiedad `table.access`.

También puede agregar la opción de Swagger a su archivo `azureMobile.js` si solo desea que haya compatibilidad con Swagger al desarrollar de forma local.

## <a name="CustomAPI"></a>API personalizadas

Además de la API de acceso a datos a través del punto de conexión /tables, Aplicaciones móviles de Azure puede proporcionar cobertura de API personalizada. Las API personalizadas se definen de forma similar a las definiciones de tabla y pueden tener acceso a las mismas utilidades, incluida la autenticación.

Si quiere usar la autenticación del Servicio de aplicaciones con una API personalizada, tiene que configurar primero la autenticación del Servicio de aplicaciones en el [Portal de Azure]. Para obtener más información sobre cómo configurar la autenticación en un Servicio de aplicaciones de Azure, revise la Guía de configuración del proveedor de identidades que pretende usar:

- [Configuración de la aplicación para usar el inicio de sesión de Azure Active Directory]
- [Configuración de la aplicación para usar el inicio de sesión de Facebook]
- [Configuración de la aplicación para usar el inicio de sesión de Google]
- [Configuración de la aplicación para usar el inicio de sesión de Microsoft]
- [Configuración de la aplicación para usar el inicio de sesión de Twitter]

### <a name="howto-customapi-basic"></a>Definición de una API sencilla personalizada

Las API personalizadas se definen en forma muy parecida a la API de tablas.

1. Crear un directorio **api**.
2. Cree un archivo JavaScript de definición de API en el directorio **api**.
3. Use el método import para importar el directorio **api**.

A continuación se muestra la definición de la api de prototipo basada en el ejemplo de aplicación básica usado anteriormente.

	var express = require('express'),
		azureMobileApps = require('azure-mobile-apps');

	var app = express(),
		mobile = azureMobileApps();

	// Import the Custom API
	mobile.api.import('./api');

	// Add the mobile API so it is accessible as a Web API
	app.use(mobile);

	// Start listening on HTTP
	app.listen(process.env.PORT || 3000);

Tomemos una API sencilla que devolverá la fecha del servidor mediante el método _Date.now()_. Este es el archivo api/date.js:

	var api = {
		get: function (req, res, next) {
			var date = { currentTime: Date.now() };
			res.status(200).type('application/json').send(date);
		});
	};

	module.exports = api;

Cada parámetro es uno de los verbos estándar de RESTful: GET, POST, PATCH o DELETE. El método es una función estándar de [ExpressJS Middleware] que envía el la salida necesaria.

### <a name="howto-customapi-auth"></a>Autenticación necesaria para el acceso a una API personalizada

El SDK de Aplicaciones móviles de Azure implementa la autenticación de la misma manera para el punto de conexión de las tablas y para las API personalizadas. Para agregar autenticación a la API desarrollada en la sección anterior, agregue una propiedad **access**:

	var api = {
		get: function (req, res, next) {
			var date = { currentTime: Date.now() };
			res.status(200).type('application/json').send(date);
		});
	};
	// All methods must be authenticated.
	api.access = 'authenticated';

	module.exports = api;

También puede especificar la autenticación en operaciones específicas:

	var api = {
		get: function (req, res, next) {
			var date = { currentTime: Date.now() };
			res.status(200).type('application/json').send(date);
		}
	};
	// The GET methods must be authenticated.
	api.get.access = 'authenticated';

	module.exports = api;

Debe usar el mismo token que se utiliza para el punto de conexión de tablas en las API personalizadas que requieren autenticación.

### <a name="howto-customapi-auth"></a>Control de grandes cargas de archivos

El SDK de Aplicaciones móviles de Azure usa el [middleware de analizador de cuerpo](https://github.com/expressjs/body-parser) para aceptar y descodificar el contenido del cuerpo del envío. Puede configurar previamente el analizador de cuerpo para aceptar tamaños mayores de cargas de archivos:

	var express = require('express'),
        bodyParser = require('body-parser'),
		azureMobileApps = require('azure-mobile-apps');

	var app = express(),
		mobile = azureMobileApps();

    // Set up large body content handling
    app.use(bodyParser.json({ limit: '50mb' }));
    app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

	// Import the Custom API
	mobile.api.import('./api');

	// Add the mobile API so it is accessible as a Web API
	app.use(mobile);

	// Start listening on HTTP
	app.listen(process.env.PORT || 3000);

Puede ajustar el límite de 50 MB que mostramos anteriormente. Tenga en cuenta que el archivo estará codificado en Base 64 antes de la transmisión, lo que aumentará el tamaño real de la carga.

### <a name="howto-customapi-sql"></a>Ejecución de instrucciones SQL personalizadas

El SDK de aplicaciones móviles de Azure permite el acceso a todo el contexto a través del objeto de solicitud, lo que le permite ejecutar fácilmente instrucciones SQL parametrizadas para el proveedor de datos definido:

    var api = {
        get: function (request, response, next) {
            // Check for parameters - if not there, pass on to a later API call
            if (typeof request.params.completed === 'undefined')
                return next();

            // Define the query - anything that can be handled by the mssql
            // driver is allowed.
            var query = {
                sql: 'UPDATE TodoItem SET complete=@completed',
                parameters: [{
                    completed: request.params.completed
                }]
            };

            // Execute the query.  The context for Azure Mobile Apps is available through
            // request.azureMobile - the data object contains the configured data provider.
            request.azureMobile.data.execute(query)
            .then(function (results) {
                response.json(results);
            });
        }
    };

    api.get.access = 'authenticated';
    module.exports = api;

Se puede acceder a este punto de conexión mediante
## <a name="Debugging"></a>Depuración y solución de problemas

El Servicio de aplicaciones de Azure proporciona varias técnicas de depuración y de solución de problemas para las aplicaciones Node.js. Todas estas técnicas están disponibles.

- [Supervisión de Aplicaciones web en el Servicio de aplicaciones de Azure]
- [Habilitación del registro de diagnóstico para aplicaciones web en el Servicio de aplicaciones de Azure]
- [Solución de problemas de una aplicación web en el Servicio de aplicaciones de Azure con Visual Studio]

### <a name="howto-diagnostic-logs"></a>Escritura en los registros de diagnóstico de Aplicaciones móviles de Azure

Las aplicaciones Node.js tienen acceso a una amplia gama de herramientas de registro de diagnóstico. Internamente, el SDK de Node.js de Aplicaciones móviles de Azure usa [Winston] para el registro de diagnóstico. Esto se habilita automáticamente al activar el modo de depuración o al establecer la configuración de la aplicación **MS\_DebugMode** en true en el [Portal de Azure]. Los registros generados aparecerán en los registros de diagnóstico en el [Portal de Azure].

## <a name="in-portal-editing"></a>Experiencia en edición de código en portal

Las herramientas especiales del Portal de Azure facilitan el trabajo con un proyecto de back-end de Node.js sin tener que descargar el proyecto de código. Las tablas fáciles y las API fáciles del portal le permiten crear y trabajar con tablas y API personalizadas directamente en el portal. Puede incluso editar los scripts de API y de operaciones de tablas en el portal mediante el editor "Monaco" de Visual Studio Team Services.

### <a name="work-easy-tables"></a>Trabajo con tablas fáciles en el Portal de Azure

Al hacer clic en **Tablas fáciles** en la configuración del sitio del back-end, puede agregar una tabla nueva, así como modificar o eliminar una tabla existente. También puede ver los datos de la tabla.

![Trabajo con tablas fáciles](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

Los siguientes comandos están disponibles en la barra de comandos para una tabla:

+ **Cambiar permisos**: modifica el permiso para leer, insertar, actualizar y eliminar operaciones en la tabla. Las opciones son permitir el acceso anónimo, requerir autenticación o deshabilitar todos los accesos a la operación. Esto modifica el archivo de código del proyecto table.json.
+ **Editar script**: el archivo de script para la tabla se abre en el editor de Visual Studio Team Services.
+ **Administrar esquema**: agrega o elimina columnas o cambia el índice de tabla.
+ **Borrar tabla**: trunca una tabla existente eliminando todas las filas de datos pero dejando el esquema sin cambiar.
+ **Eliminar filas**: elimina filas individuales de datos.
+ **Ver registros de streaming**: le conecta con el servicio de registro de streaming de su sitio.

###<a name="work-easy-apis"></a>Trabajo con API fáciles en el Portal de Azure

Al hacer clic en **API fáciles** en la configuración del sitio del back-end, puede agregar un nuevo punto de conexión de API personalizado, así como modificar o eliminar un punto de conexión de API existente.

![Trabajo con API fáciles](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

En el portal, puede cambiar los permisos de acceso para una acción de HTTP determinada, editar el archivo de script de API en el editor de Visual Studio Team Services o ver los registros de streaming.

###<a name="online-editor"></a>Edición de código en Visual Studio Team Services

El Portal de Azure le permite editar archivos de script de back-end de Node.js en Visual Studio Team Services sin tener que descargar el proyecto en el equipo local. Para editar archivos de script en el editor en línea:

1. En la hoja de back-end de la aplicación móvil, haga clic en **Toda la configuración** > **Tablas fáciles** o **API fáciles**, en una tabla o API y luego en **Editar script**. El archivo de script se abre en el editor de Visual Studio Team Services.

	![Editor de códigos de Visual Studio Team Services](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-visual-studio-editor.png)

2. Realice los cambios en el archivo de código en el editor en línea. Los cambios se guardan automáticamente a medida que se escriben.

En el editor, también puede ejecutar el código en el sitio.


<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ../../includes/media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png

<!-- URLs -->
[Inicio rápido de cliente de Android]: app-service-mobile-android-get-started.md
[Inicio rápido de cliente de Apache Cordova]: app-service-mobile-cordova-get-started.md
[Inicio rápido de cliente de iOS]: app-service-mobile-ios-get-started.md
[Inicio rápido de cliente de Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
[Inicio rápido de cliente de Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
[Inicio rápido de cliente de Xamarin.Forms]: app-service-mobile-xamarin-forms-get-started.md
[Inicio rápido de cliente de Windows]: app-service-mobile-windows-store-dotnet-get-started.md
[HTML/Javascript Client QuickStart]: app-service-html-get-started.md
[sincronización de datos sin conexión]: app-service-mobile-offline-data-sync.md
[Configuración de la aplicación para usar el inicio de sesión de Azure Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Configuración de la aplicación para usar el inicio de sesión de Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Configuración de la aplicación para usar el inicio de sesión de Google]: app-service-mobile-how-to-configure-google-authentication.md
[Configuración de la aplicación para usar el inicio de sesión de Microsoft]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Configuración de la aplicación para usar el inicio de sesión de Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md
[Guía de implementación del Servicio de aplicaciones de Azure]: ../app-service-web/web-sites-deploy.md
[Supervisión de Aplicaciones web en el Servicio de aplicaciones de Azure]: ../app-service-web/web-sites-monitor.md
[Habilitación del registro de diagnóstico para aplicaciones web en el Servicio de aplicaciones de Azure]: ../app-service-web/web-sites-enable-diagnostic-log.md
[Solución de problemas de una aplicación web en el Servicio de aplicaciones de Azure con Visual Studio]: ../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md
[Especificación de una versión de Node.js en una aplicación Azure]: ../nodejs-specify-node-version-azure-apps.md
[Uso de módulos de Node]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service-web/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: http://expressjs.com/
[Swagger]: http://swagger.io/

[Portal de Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/es-ES/docs/Web/JavaScript/Reference/Global_Objects/Promise
[ejemplo "basicapp" en GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[ejemplo "todo" en GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[directorio de ejemplos de GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 para Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[paquete de mssql para Node.js]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/es-ES/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

<!-----HONumber=AcomDC_0302_2016-->