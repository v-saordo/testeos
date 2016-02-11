<properties
	pageTitle="Introducción a Azure AD Cordova | Microsoft Azure"
	description="Creación de una aplicación Cordova que se integra con Azure AD para el inicio de sesión y llama a las API protegidas de Azure AD mediante OAuth."
	services="active-directory"
	documentationCenter=""
	authors="vibronet"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="vittorib"/>

# Integración de Azure AD con una aplicación Apache Cordova

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Apache Cordova permite desarrollar aplicaciones HTML5/JavaScript que se pueden ejecutar en dispositivos móviles como aplicaciones nativas completas. Con Azure AD, puede agregar capacidades de autenticación de nivel empresarial a sus aplicaciones Cordova. Gracias a un SDK nativo de Azure AD del complemento Cordova disponible en iOS, Android, Tienda Windows y Windows Phone, podrá mejorar su aplicación para que admita el inicio de sesión con las cuentas de AD de sus usuarios, permita acceder a Office 365 y a la API de Azure e, incluso, garantice la seguridad de las llamadas a su propia API web personalizada.

En este tutorial, usaremos el complemento de Apache Cordova para la biblioteca de autenticación de Active Directory (ADAL) para mejorar una aplicación sencilla con las siguientes características:

-	Con solo unas líneas de código, podrá autenticar a un usuario de AD y obtener un token para llamar a la API Graph de Azure AD.
-	Utilice ese token para invocar la API Graph para consultar ese directorio y mostrar los resultados.  
-	Aproveche la caché de tokens de ADAL para reducir al máximo las peticiones de autenticación que se le muestran al usuario.

Para ello, deberá hacer lo siguiente:

2. Registrar una aplicación con Azure AD
2. Agregar código a la aplicación para solicitar tokens
3. Agregue código para usar el token para hacer consultas a la API Graph y mostrar los resultados.
4. Crear el proyecto de implementación de Cordova con todas las plataformas a las que desee dirigirse y el complemento de ADAL de Cordova a fin de probar la solución en emuladores.

## *0. Requisitos previos*

Para realizar este tutorial, necesitará lo siguiente:

- Un inquilino de Azure AD que disponga de una cuenta con derechos de desarrollo de aplicaciones.
- Un entorno de desarrollo configurado para usar Apache Cordova.  

Si ya los tiene configurados, vaya directamente al paso 1.

Si no tiene un inquilino de Azure AD, puede encontrar [instrucciones sobre cómo conseguir uno aquí](active-directory-howto-tenant.md).

Si no dispone de Apache Cordova configurado en su equipo, instale lo siguiente:

- [Git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- [NodeJS](https://nodejs.org/download/)
- [Interfaz de línea de comandos Cordova](https://cordova.apache.org/) (se instala fácilmente mediante el administrador de paquetes NPM: `npm install -g cordova`)

Tenga en cuenta que deberían funcionar tanto en PC como en Mac.

Cada plataforma de destino tiene diferentes requisitos previos.

- Para compilar y ejecutar la versión de la aplicación para Windows Tablet/PC o Phone
	- [Visual Studio 2013 para Windows con la actualización 2 o posterior](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (Express u otra versión).
- Para compilar y ejecutar la aplicación para iOS
	-   Xcode 5.x o superior. Puede descargarla en http://developer.apple.com/downloads o en la [tienda de aplicaciones Mac](http://itunes.apple.com/us/app/xcode/id497799835?mt=12).
	-   [ios sim](https://www.npmjs.org/package/ios-sim): permite iniciar aplicaciones de iOS en el simulador de iOS desde la línea de comandos (se puede instalar fácilmente mediante el terminal: `npm install -g ios-sim`)

- Para compilar y ejecutar la aplicación para Android
	- Instale el [Kit de desarrollo de Java (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) o una versión posterior. Asegúrese de que `JAVA_HOME` (variable de entorno) se haya configurado correctamente según la ruta de instalación de JDK (por ejemplo, C:\\Archivos de programa\\Java\\jdk1.7.0\_75).
	- Instale el [SDK de Android](http://developer.android.com/sdk/installing/index.html?pkg=tools) y agregue la ubicación `<android-sdk-location>\tools` (por ejemplo, C:\\tools\\Android\\android-sdk\\tools) a su variable de entorno `PATH`.
	- Abra el Administrador de SDK de Android (por ejemplo, a través de terminal: `android`) y proceda con la instalación.
    - SDK para la plataforma de *Android 5.0.1 (API 21)*
    - *Herramientas de compilación de SDK de Android*, versión 19.1.0 o posterior
    - *Repositorio compatible con Android* (extras)

  El SDK de Android no proporciona ninguna instancia de emulador predeterminada. Cree una nueva ejecutando `android avd` desde el terminal y, a continuación, seleccione *Crear...* si desea ejecutar la aplicación Android en el emulador. El *nivel de API* recomendado es 19 o superior. Consulte [Administrador AVD](http://developer.android.com/tools/help/avd-manager.html) para obtener más información acerca de las opciones de creación y el emulador de Android.


## *1. Registre una aplicación con Azure AD*

Nota: este paso es opcional. El tutorial proporciona valores configurados previamente que le permitirán ver el ejemplo en acción sin tener que realizar cualquier aprovisionamiento en su propio inquilino. Sin embargo, se recomienda que realice este paso y se familiarice con el proceso, ya que será necesario si va a crear sus propias aplicaciones.

Azure AD solo emitirá tokens para aplicaciones conocidas. Para poder usar Azure AD desde su aplicación, deberá crear una entrada para él en el inquilino. Para registrar una nueva aplicación en el inquilino:

- Inicie sesión en el Portal de administración de Azure.
- En el panel de navegación izquierdo, haga clic en Active Directory.
- Seleccione el inquilino en el que desea registrar la aplicación.
- Haga clic en la pestaña Aplicaciones y en Agregar en el cajón inferior.
- Siga las indicaciones y cree una nueva "Aplicación de cliente nativa".
    - El nombre de la aplicación servirá de descripción de la aplicación para los usuarios finales.
    -	El "URI de redirección" es el URI que se usa para devolver tokens a su aplicación. Escriba `http://MyDirectorySearcherApp`.

Una vez que haya completado el registro, AAD asignará a su aplicación un identificador de cliente único. Necesitará este valor en las secciones siguientes: se encuentra en la pestaña Configuración de la aplicación recién creada.

## *2. Clone los repositorios necesarios para el tutorial*

En el shell o en la línea de comandos, escriba lo siguiente:

    git clone https://github.com/AzureAD/azure-activedirectory-library-for-cordova.git
    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## *3. Cree la aplicación Cordova*

Hay varias maneras de crear aplicaciones de Cordova. En este tutorial, utilizaremos la interfaz de línea de comandos (CLI) de Cordova. En el shell o en la línea de comandos, escriba lo siguiente:


     cordova create DirSearchClient --copy-from="NativeClient-MultiTarget-Cordova/DirSearchClient"

Esta acción creará la estructura de carpetas y el scaffolding para el proyecto Cordova y se copiará el contenido del proyecto de inicio en la subcarpeta www. Acceda a la nueva carpeta de DirSearchClient.

    cd .\DirSearchClient

Agregue el complemento de lista blanca, que es necesario para invocar la API Graph.

     cordova plugin add https://github.com/apache/cordova-plugin-whitelist.git

A continuación, agregue todas las plataformas que desea admitir. Para obtener un ejemplo funcional, deberá ejecutar al menos uno de los siguientes comandos. Tenga en cuenta que no podrá emular iOS en Windows ni Windows/Windows Phone en Mac.

    cordova platform add android@97718a0a25ec50fedf7b023ae63bfcffbcfafb4b
    cordova platform add ios
    cordova platform add windows

Por último, puede agregar el complemento de ADAL para Cordova al proyecto.

    cordova plugin add ../azure-activedirectory-library-for-cordova

## *3. Agregue código para autenticar a los usuarios y obtener tokens de AAD*

La aplicación que estamos desarrollando en este tutorial proporciona una característica de búsqueda de directorio esencial, donde el usuario final puede escribir el alias de cualquier usuario en el directorio y visualizar algunos atributos básicos. El proyecto de inicio contiene la definición de la interfaz de usuario básica de la aplicación (en www/index.html) y el scaffolding que conecta los ciclos de eventos de la aplicación básica, los enlaces de la interfaz de usuario y la lógica de visualización de los resultados (en www/js/index.js). Lo único que hemos omitido es la adición de la lógica implementando las tareas de identidad.

Lo primero que hay que hacer es introducir en el código los valores de protocolo que usa AAD para identificar la aplicación y los recursos a los que se dirige. Estos valores se utilizarán para construir las solicitudes de tokens más adelante. Inserte el fragmento de código siguiente en la parte superior del archivo index.js.

```javascript
    var authority = "https://login.windows.net/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

Los valores `redirectUri` y `clientId` deben coincidir con los valores que describen la aplicación en AAD. Puede encontrarlos en la pestaña Configurar del portal de Azure, tal como se describe en el paso 1 de este tutorial. Nota: si ha optado por no registrar una nueva aplicación en su propio inquilino, puede simplemente pegar los valores preconfigurados anteriores tal cual, lo que le permitirá ver la ejecución del ejemplo, aunque siempre debe crear su propia entrada para las aplicaciones diseñadas para la producción.

A continuación, hay que agregar el código de la solicitud de token real. Inserte el siguiente fragmento de código entre las definiciones `search ` y `renderdata `.

```javascript
    // Shows user authentication dialog if required.
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user cridentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
Examinemos esa función dividiéndola en dos partes principales. Este ejemplo está diseñado para que funcione con cualquier inquilino, en lugar de vincularse a uno concreto. Se utiliza el extremo "/common", que permite al usuario especificar cualquier cuenta en el momento de la autenticación y dirige la solicitud al inquilino al que pertenece. Esta primera parte del método inspecciona la memoria caché de ADAL para ver si ya hay un token almacenado. Si lo hay, usa los inquilinos de los que proviene para volver a inicializar ADAL. Esto es necesario para evitar mensajes adicionales, ya que el uso de "/common" siempre hace que se le pida al usuario que especifique una nueva cuenta. ```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
``` La segunda parte del método realiza la solicitud tokewn correcta. El método `acquireTokenSilentAsync` solicita a ADAL que devuelva un token para el recurso especificado sin mostrar ninguna experiencia de usuario. Esto puede ocurrir si la memoria caché ya tiene un token de acceso adecuado almacenado, o si hay un token de actualización que puede utilizarse para obtener un nuevo token de acceso sin mostrar ningún símbolo del sistema. Si dicho intento falla, se recurre a `acquireTokenAsync`, que solicitará visiblemente al usuario que se autentique. ```javascript
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user cridentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
``` Ahora que tenemos el token, podemos invocar finalmente la API Graph y realizar la consulta de búsqueda que queremos. Inserte el fragmento de código siguiente justo debajo de la definición `authenticate`.

```javascript
// Makes Api call to receive user list.
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
Los archivos de punto de partida proporcionaron una experiencia de usuario esencial para especificar el alias del usuario en un cuadro de texto. Este método utiliza ese valor para construir una consulta, combinarla con el token de acceso, enviarlo a Graph y analizar los resultados. El método renderData, que ya está presente en el archivo de punto de partida, se encarga de visualizar los resultados.

## *4. Lleve a cabo la ejecución*
La aplicación está lista finalmente para ejecutarse. El funcionamiento es muy sencillo: una vez que se inicie la aplicación, escriba en el cuadro de texto el alias del usuario que desea buscar. A continuación, haga clic en el botón. Se le pedirá que se autentique. Tras autenticarse y realizar la búsqueda correctamente, se mostrarán los atributos del usuario buscado. Las ejecuciones posteriores llevarán a cabo la búsqueda sin mostrar ningún símbolo del sistema, gracias a la presencia en memoria caché del token obtenido anteriormente. Los pasos concretos para ejecutar la aplicación varían según la plataforma.


##### Para compilar y ejecutar la versión de la aplicación para Windows Tablet/PC

   `cordova run windows`

   __Nota__: durante la primera ejecución, le pedirá que inicie sesión en una licencia de desarrollador. Consulte [Obtención de una licencia de desarrollador](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx) para obtener más detalles.


##### Para compilar y ejecutar la aplicación en Windows Phone 8.1

   Para realizar la ejecución en el dispositivo conectado: `cordova run windows --device -- --phone`

   Para realizar la ejecución en el emulador predeterminado: `cordova emulate windows -- --phone`

   Utilice `cordova run windows --list -- --phone` para ver todos los destinos disponibles y `cordova run windows --target=<target_name> -- --phone` para ejecutar la aplicación en el emulador o el dispositivo concreto (por ejemplo, `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`).
##### Para compilar y realizar la ejecución en dispositivos Android

   Para realizar la ejecución en el dispositivo conectado: `cordova run android --device`

   Para realizar la ejecución en el emulador predeterminado: `cordova emulate android`

   __Nota__: asegúrese de que ha creado la instancia del emulador usando *AVD Manager*, tal como se mostró en la sección *Requisitos previos*.

   Utilice `cordova run android --list` para ver todos los destinos disponibles y `cordova run android --target=<target_name>` para ejecutar la aplicación en el emulador o el dispositivo concreto (por ejemplo, `cordova run android --target="Nexus4_emulator"`).

##### Para compilar y realizar la ejecución en dispositivos iOS

   Para realizar la ejecución en el dispositivo conectado: `cordova run ios --device`

   Para realizar la ejecución en el emulador predeterminado: `cordova emulate ios`

   __Nota__: asegúrese de que el paquete `ios-sim` esté instalado para ejecutarse en el emulador. Consulte la sección *Requisitos previos* para obtener más detalles.

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run application on specific device or emulator (for example,  `cordova run android --target="iPhone-6"`).

Utilice `cordova run --help` para ver la compilación y las opciones de ejecución adicionales.

Como referencia, aquí puede ver el ejemplo finalizado (sin sus valores de configuración). Ahora puede pasar a escenarios más avanzados (y, en consecuencia, más interesantes). También puede probar lo siguiente:

[Protección de una API Web Node.js con Azure AD >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0128_2016-->