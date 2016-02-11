<properties
   pageTitle="Uso eficaz de entornos DevOps para las aplicaciones web"
   description="Aprenda a usar ranuras de implementación para configurar y administrar varios entornos de desarrollo para la aplicación"
   services="app-service\web"
   documentationCenter=""
   authors="sunbuild"
   manager="yochayk"
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="web"
   ms.date="12/24/2015"
   ms.author="sumuth"/>

# Uso eficaz de entornos DevOps para las aplicaciones web

En este artículo se muestra cómo configurar y administrar implementaciones de aplicaciones web para varias versiones de la aplicación como desarrollo, ensayo, control de calidad y producción. Cada versión de la aplicación se puede considerar un entorno de desarrollo para una necesidad específica dentro de su proceso de implementación; por ejemplo, el equipo de desarrolladores puede usar el entorno de control de calidad para probar la calidad de la aplicación antes de enviar los cambios a producción. La configuración de varios entornos de desarrollo puede ser una tarea difícil dado que debe realizar el seguimiento de los recursos y administrarlos (proceso, aplicación web, base de datos, caché, etc.) a través de estos entornos, así como implementar el contenido de un entorno a otro.

## Configuración de un entorno que no es de producción (ensayo, desarrollo, control de calidad)
El siguiente paso después de que la aplicación web de producción está en funcionamiento, consiste en crear un entorno que no es de producción. Para usar ranuras de implementación, asegúrese de estar ejecutando el modo del Plan de servicio de aplicaciones **Estándar** o **Premium**. Los espacios de implementación son realmente aplicaciones web activas realmente con sus propios nombres de host. Los elementos de contenido y configuración de aplicaciones web se pueden intercambiar entre dos espacios de implementación, incluida la ranura de producción. La implementación de la aplicación en un espacio de implementación ofrece las ventajas siguientes:

1. Puede validar los cambios en la aplicación web en un espacio de implementación de ensayo antes de intercambiarlo con el espacio de producción.
2. La implementación de una aplicación web en un espacio en primer lugar y su intercambio con el de la producción garantiza que todas las instancias del espacio estén correctas antes de colocarse en producción. Esto elimina tiempos de inactividad a la hora de implementar la aplicación web. El redireccionamiento del tráfico es impecable y no se anulan las solicitudes como consecuencia de las operaciones de intercambio. Este flujo de trabajo completo puede automatizarse mediante la configuración de [Intercambio automático](web-sites-staged-publishing.md#configure-auto-swap-for-your-web-app).
3. Después de un intercambio, el espacio con la aplicación web de ensayo anterior tiene ahora la aplicación web de producción anterior. Si los cambios intercambiados en la ranura de producción no son los esperados, puede realizar el mismo intercambio inmediatamente para volver a la "última aplicación web buena conocida".

Para configurar una ranura de implementación de ensayo, consulte [Configuración de entornos de ensayo para aplicaciones web en el Servicio de aplicaciones de Azure](web-sites-staged-publishing.md) . Cada entorno debe incluir su propio conjunto de recursos; por ejemplo, si su aplicación web usa una base de datos, entonces tanto la aplicación web de producción como la aplicación web de ensayo deben usar diferentes bases de datos. Agregue recursos de entorno de desarrollo de ensayo, como base de datos, almacenamiento o caché para configurar el entorno de desarrollo de ensayo.

## Ejemplos del uso de varios entornos de desarrollo

Todo proyecto debe seguir la administración de un código fuente con al menos dos entornos, un entorno de desarrollo y un entorno de producción; sin embargo, al usar sistemas de administración del contenido, marcos de aplicaciones, etc., podemos encontrarnos con problemas en los que la aplicación no admite ese escenario de forma predeterminada. Esto sucede con algunos de los marcos más conocidos que describimos a continuación. Muchas preguntas vienen a la mente cuando se trabaja con un CMS o un marco, como por ejemplo:

1. ¿Cómo dividirlo en diferentes entornos?
2. ¿Qué archivos puedo cambiar sin afectar a las actualizaciones de la versión del marco?
3. ¿Cómo administro la configuración de cada entorno?
4. ¿Cómo administro módulos, actualizaciones de versiones de complementos o actualizaciones principales de versión del marco?

Existen muchas maneras de configurar un entorno múltiple para su proyecto y los ejemplos siguientes son un solo uno de tales métodos para las aplicaciones respectivas.

### WordPress
En esta sección obtendrá información sobre cómo configurar un flujo de trabajo de implementación mediante ranuras de WordPress. WordPress, al igual que la mayoría de soluciones de CMS, no admiten el trabajo con varios entornos de desarrollo de forma predeterminada. Aplicaciones web del Servicio de aplicaciones presenta nuevas características que facilitan el almacenamiento de la configuración fuera del código.

Antes de crear una ranura de ensayo, configure el código de aplicación para admitir varios entornos. Para admitir varios entornos en WordPress debe editar `wp-config.php` en la aplicación web de desarrollo local y agregar el código siguiente al principio del archivo. De esta forma, la aplicación puede seleccionar la configuración correcta en función del entorno seleccionado.


	// Support multiple environments
	// set the config file based on current environment
	/**/
	if (strpos(filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING),'localhost') !== false) {
	    // local development
	    $config_file = 'config/wp-config.local.php';
	}
	elseif  ((strpos(getenv('WP_ENV'),'stage') !== false) ||  (strpos(getenv('WP_ENV'),'prod' )!== false )){
	      //single file for all azure development environments
	      $config_file = 'config/wp-config.azure.php';
	}
	$path = dirname(__FILE__) . '/';
	if (file_exists($path . $config_file)) {
	    // include the config file if it exists, otherwise WP is going to fail
	    require_once $path . $config_file;
	}



Cree una carpeta bajo la raíz de la aplicación web llamada `config` y agregue dos archivos, `wp-config.azure.php` y `wp-config.local.php`, que representan el entorno de Azure y el entorno local, respectivamente.

Copie lo siguiente en `wp-config.local.php`:

```
	
	<?php
	
	// MySQL settings
	/** The name of the database for WordPress */
	
	define('DB_NAME', 'yourdatabasename');
	
	/** MySQL database username */
	define('DB_USER', 'yourdbuser');
	
	/** MySQL database password */
	define('DB_PASSWORD', 'yourpassword');
	
	/** MySQL hostname */
	define('DB_HOST', 'localhost');
	/**
	 * For developers: WordPress debugging mode.
	 * * Change this to true to enable the display of notices during development.
	 * It is strongly recommended that plugin and theme developers use WP_DEBUG
	 * in their development environments.
	 */
	define('WP_DEBUG', true);
	
	//Security key settings
	define('AUTH_KEY',         'put your unique phrase here');
	define('SECURE_AUTH_KEY',  'put your unique phrase here');
	define('LOGGED_IN_KEY',    'put your unique phrase here');
	define('NONCE_KEY',        'put your unique phrase here');
	define('AUTH_SALT',        'put your unique phrase here');
	define('SECURE_AUTH_SALT', 'put your unique phrase here');
	define('LOGGED_IN_SALT',   'put your unique phrase here');
	define('NONCE_SALT',       'put your unique phrase here');
	
	/**
	 * WordPress Database Table prefix.
	 *
	 * You can have multiple installations in one database if you give each a unique
	 * prefix. Only numbers, letters, and underscores please!
	 */
	$table_prefix  = 'wp_';
```

La configuración de las claves de seguridad que se indican arriba puede ayudar a impedir que la aplicación web se piratee, así que use valores únicos. Si debe generar la cadena para estas claves de seguridad, puede ir al generador automático y crear nuevas claves o valores mediante este [vínculo](https://api.wordpress.org/secret-key/1.1/salt).

Copie el siguiente código en `wp-config.azure.php`:


```

	<?php
	// MySQL settings
	/** The name of the database for WordPress */
	
	define('DB_NAME', getenv('DB_NAME'));
	
	/** MySQL database username */
	define('DB_USER', getenv('DB_USER'));
	
	/** MySQL database password */
	define('DB_PASSWORD', getenv('DB_PASSWORD'));
	
	/** MySQL hostname */
	define('DB_HOST', getenv('DB_HOST'));
	
	/**
	* For developers: WordPress debugging mode.
	*
	* Change this to true to enable the display of notices during development.
	* It is strongly recommended that plugin and theme developers use WP_DEBUG
	* in their development environments.
	* Turn on debug logging to investigate issues without displaying to end user. For WP_DEBUG_LOG to
	* do anything, WP_DEBUG must be enabled (true). WP_DEBUG_DISPLAY should be used in conjunction
	* with WP_DEBUG_LOG so that errors are not displayed on the page */
	
	*/
	define('WP_DEBUG', getenv('WP_DEBUG'));
	define('WP_DEBUG_LOG', getenv('TURN_ON_DEBUG_LOG'));
	define('WP_DEBUG_DISPLAY',false);
	
	//Security key settings
	/** If you need to generate the string for security keys mentioned above, you can go the automatic generator to create new keys/values: https://api.wordpress.org/secret-key/1.1/salt **/
	define('AUTH_KEY' ,getenv('DB_AUTH_KEY'));
	define('SECURE_AUTH_KEY',  getenv('DB_SECURE_AUTH_KEY'));
	define('LOGGED_IN_KEY', getenv('DB_LOGGED_IN_KEY'));
	define('NONCE_KEY', getenv('DB_NONCE_KEY'));
	define('AUTH_SALT',  getenv('DB_AUTH_SALT'));
	define('SECURE_AUTH_SALT', getenv('DB_SECURE_AUTH_SALT'));
	define('LOGGED_IN_SALT',   getenv('DB_LOGGED_IN_SALT'));
	define('NONCE_SALT',   getenv('DB_NONCE_SALT'));
	
	/**
	* WordPress Database Table prefix.
	*
	* You can have multiple installations in one database if you give each a unique
	* prefix. Only numbers, letters, and underscores please!
	*/
	$table_prefix  = getenv('DB_PREFIX');
```

#### Uso de rutas de acceso relativas
Por último, hay que permitir que la aplicación WordPress use rutas de acceso relativas. WordPress almacena la información de dirección URL en la base de datos. Como consecuencia, mover el contenido de un entorno a otro es más difícil dado que es necesario actualizar la base de datos cada vez que se mueve del entorno local al de ensayo o del entorno de ensayo al entorno de producción. Para reducir el riesgo de problemas que pueden deberse a la implementación de la base de datos cada vez que se pasa de un entorno a otro, use el [complemento de vínculos relativos a la raíz](https://wordpress.org/plugins/root-relative-urls/) que se puede instalar mediante el panel de administrador de WordPress o descargar manualmente [aquí](https://downloads.wordpress.org/plugin/root-relative-urls.zip).

Agregue las siguientes entradas al archivo `wp-config.php` delante del comentario `That's all, stop editing!`:

```

    define('WP_HOME', 'http://' . filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
	define('WP_SITEURL', 'http://' . filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
	define('WP_CONTENT_URL', '/wp-content');
	define('DOMAIN_CURRENT_SITE', filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
```

Active el complemento en el menú `Plugins` del panel de administrador de WordPress. Guarde la configuración de vínculo permanente para la aplicación de WordPress.

#### El archivo `wp-config.php` final
Las actualizaciones principales de WordPress no afectan a los archivos `wp-config.php` , `wp-config.azure.php` y `wp-config.local.php`. Esta es la apariencia final del archivo `wp-config.php`:

```

	<?php
	/**
	 * The base configurations of the WordPress.
	 *
	 * This file has the following configurations: MySQL settings, Table Prefix,
	 * Secret Keys, and ABSPATH. You can find more information by visiting
	 *
	 * Codex page. You can get the MySQL settings from your web host.
	 *
	 * This file is used by the wp-config.php creation script during the
	 * installation. You don't have to use the web web app, you can just copy this file
	 * to "wp-config.php" and fill in the values.
	 *
	 * @package WordPress
	 */
	
	// Support multiple environments
	// set the config file based on current environment
	if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) { // local development
	    $config_file = 'config/wp-config.local.php';
	}
	elseif  ((strpos(getenv('WP_ENV'),'stage') !== false) ||  (strpos(getenv('WP_ENV'),'prod' )!== false )){
	    $config_file = 'config/wp-config.azure.php';
	}
	
	
	$path = dirname(__FILE__) . '/';
	if (file_exists($path . $config_file)) {
	    // include the config file if it exists, otherwise WP is going to fail
	    require_once $path . $config_file;
	}
	
	/** Database Charset to use in creating database tables. */
	define('DB_CHARSET', 'utf8');
	
	/** The Database Collate type. Don't change this if in doubt. */
	define('DB_COLLATE', '');
	
	
	/* That's all, stop editing! Happy blogging. */
	
	define('WP_HOME', 'http://' . filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
	define('WP_SITEURL', 'http://' . filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
	define('WP_CONTENT_URL', '/wp-content');
	define('DOMAIN_CURRENT_SITE', filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
	
	/** Absolute path to the WordPress directory. */
	if ( !defined('ABSPATH') )
		define('ABSPATH', dirname(__FILE__) . '/');
	
	/** Sets up WordPress vars and included files. */
	require_once(ABSPATH . 'wp-settings.php');
```

#### Configuración de un entorno de ensayo
Suponiendo que ya tenga una aplicación web de WordPress que se ejecuta en Azure Web, inicie sesión en el [Portal de Azure](https://portal.azure.com/) y vaya a la aplicación web de WordPress. Si no, puede crear una desde Marketplace. Para obtener más información, haga clic [aquí](web-sites-php-web-site-gallery.md). Haga clic en **Configuración** -> **Espacios de implementación** -> **Agregar** para crear una ranura de implementación con la fase de nombre. Una ranura de implementación es otra aplicación web que comparte los mismos recursos que la aplicación web principal creada anteriormente.

![Creación de la ranura de implementación de ensayo](./media/app-service-web-staged-publishing-realworld-scenarios/1setupstage.png)

Agregue otra base de datos MySQL, por ejemplo, `wordpress-stage-db` al grupo de recursos `wordpressapp-group`.

 ![Incorporación de una base de datos de MySQL a un grupo de recursos](./media/app-service-web-staged-publishing-realworld-scenarios/2addmysql.png)

Actualice las cadenas de conexión para la ranura de implementación de ensayo de modo que apunte a la base de datos recién creada, `wordpress-stage-db`. Tenga en cuenta que la aplicación web de producción, `wordpressprodapp`, y la aplicación web de ensayo, `wordpressprodapp-stage`, deben apuntar a bases de datos diferentes.

#### Configuración de valores de aplicación específicos del entorno
Los desarrolladores pueden almacenar pares de cadena clave -valor en Azure como parte de la información de configuración asociada con una aplicación web llamada Configuración de aplicaciones. En tiempo de ejecución, Aplicaciones web del Servicio de aplicaciones recupera automáticamente estos valores y los pone a disposición del código que se ejecuta en la aplicación web. Desde el punto de vista de la seguridad, esto resulta beneficioso ya que la información confidencial como las cadenas de conexión de base de datos con contraseñas, nunca se muestran como texto no cifrado en un archivo como `wp-config.php`.

Este proceso que se define a continuación es útil cuando se realizan actualizaciones, ya que incluye cambios tanto en los archivos como en la base de datos de la aplicación de WordPress:

- Actualización de versión de WordPress
- Agregar nuevos complementos o editar o actualizar los existentes
- Agregar nuevos temas o editar o actualizar los existentes

Configure los valores de aplicación para:

- La información de base de datos
- Activar o desactivar el registro de WordPress
- Configurar la seguridad de WordPress

![Configuración de aplicaciones para la aplicación web de Wordpress](./media/app-service-web-staged-publishing-realworld-scenarios/3configure.png)

Asegúrese de que ha agregado la siguiente configuración de aplicación para la ranura de ensayo y la aplicación web de producción. Tenga en cuenta que la aplicación web de producción y la aplicación web de ensayo usan diferentes bases de datos. Desactive la casilla **Configuración de ranuras** para todos los parámetros de configuración, excepto WP\_ENV. Esta operación cambiara la configuración de la aplicación web, junto con el contenido del archivo y la base de datos. Si la casilla **Configuración de ranuras** está **Activada**, la configuración de la aplicación web y la configuración de la cadena de conexión NO se moverán entre los entornos al realizar una operación de INTERCAMBIO y, por lo tanto, si existe algún cambio en la base de datos este no afectará a la aplicación web de producción.

Implemente la aplicación web de entorno de desarrollo local en la aplicación web de ensayo y la base de datos mediante WebMatrix o cualquier otra herramienta de su elección, como FTP, Git o PhpMyAdmin.

![Cuadro de diálogo de publicación de WebMatrix para una aplicación web de WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/4wmpublish.png)

Examine y pruebe la aplicación web provisional. Si consideramos un escenario en el que se va actualizar el tema de la aplicación web, esta sería la aplicación web de ensayo.

![Examen de la aplicación web de ensayo antes del intercambio de ranuras](./media/app-service-web-staged-publishing-realworld-scenarios/5wpstage.png)


 Si todo está bien, haga clic en el botón **Intercambiar** en la aplicación web de ensayo para mover el contenido al entorno de producción. En este caso, intercambia la aplicación web y la base de datos entre los entornos durante cada una de las operaciones de **intercambio**.

![Vista previa de los cambios de intercambio para WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/6swaps1.png)

 >[AZURE.NOTE]
 Si tiene un escenario en el que solo necesita insertar archivos (sin actualizaciones de la base de datos), entonces **active** la casilla **Configuración de ranuras** de todas las bases de datos relacionadas con la *configuración de aplicaciones* y la *configuración de cadenas de conexión* en la hoja de configuración de la aplicación web, dentro del Portal de Azure, antes de realizar el intercambio. En este caso DB\_NAME, DB\_HOST, DB\_PASSWORD, DB\_USER, la configuración predeterminada de cadena de conexión, no se debe mostrar en los cambios de vista previa al realizar un **intercambio**. En este momento, cuando finalice la operación de **intercambio**, la aplicación web de WordPress **SOLO** tendrá los archivos actualizados.

Antes de realizar un intercambio, aquí está la aplicación web de WordPress de producción ![Aplicación web de producción antes del intercambio de ranuras](./media/app-service-web-staged-publishing-realworld-scenarios/7bfswap.png)

Después de la operación de intercambio, el tema se actualiza en la aplicación web de producción.

![Aplicación web de producción después del intercambio de ranuras](./media/app-service-web-staged-publishing-realworld-scenarios/8afswap.png)

En una situación en que necesite **revertir** los cambios, puede ir a la configuración de la aplicación web de producción y hacer clic en el botón **Intercambiar** para cambiar la aplicación web y la base de datos de la ranura de producción a la de ensayo. Es importante que recuerde que, si los cambios de la base de datos se incluyen en una operación de **intercambio** en un momento dado, la próxima vez que vuelva a implementar en la aplicación web de ensayo, deberá implementar los cambios de la base de datos en la base de datos actual para la aplicación web de ensayo, que podría ser la base de datos de producción o de ensayo anterior.

#### Resumen
En líneas generales, el proceso de cualquier aplicación con una base de datos es el siguiente:

1. Instalar la aplicación en el entorno local
2. Incluir configuración específica del entorno (aplicación web de Azure y local)
3. Configurar los entornos en las Aplicaciones web del Servicio de aplicaciones: ensayo, producción
4. Si tiene una aplicación de producción que ya se está ejecutando en Azure, sincronice el contenido de producción (archivos y código + base de datos) con el entorno local y de ensayo.
5. Desarrollar la aplicación en el entorno local
6. Colocar la aplicación web de producción en modo de mantenimiento o bloqueado y sincronizar el contenido de la base de datos de producción con los entornos de ensayo y de desarrollo
7. Implementar el entorno de desarrollo y probarlo
8. Implementar el entorno de producción
9. Repita los pasos del 4 al 6

### Umbraco
En esta sección, aprenderá cómo el CMS de Umbraco usa un módulo personalizado para implementar a través del entorno de DevOps múltiple. En este ejemplo se proporciona un enfoque diferente para administrar varios entornos de desarrollo.

El [CMS de Umbraco](http://umbraco.com/) es una conocida solución de CMS para .NET que usan muchos desarrolladores. Proporciona el módulo [Courier2](http://umbraco.com/products/more-add-ons/courier-2) para implementar desde el entorno de desarrollo, pasando por el de ensayo hasta llegar a producción. Puede crear fácilmente un entorno de desarrollo local para una aplicación web de CMS mediante Visual Studio o Webmatrix..

1. Para crear una aplicación web de Umbraco con Visual Studio, haga clic [aquí](https://our.umbraco.org/documentation/Installation/install-umbraco-with-nuget).
2. Para crear una aplicación web de Umbraco con WebMatrix, haga clic [aquí](http://umbraco.com/help-and-support/video-tutorials/getting-started/working-with-webmatrix) .

Recuerde siempre quitar la carpeta `install` de la aplicación y no cargarla nunca en aplicaciones web de ensayo o producción. En este tutorial, usaremos WebMatrix.

#### Configuración de un entorno de ensayo
Cree una ranura de implementación (como se mencionó anteriormente) para la aplicación web de CMS de Umbraco. Se supone que ya tiene una en funcionamiento. Si no, puede crear una desde Marketplace.

Actualice la cadenas de conexión para la ranura de implementación de ensayo de modo que apunte a la base de datos recién creada: **umbraco-stage-db**. La aplicación web de producción (umbraositecms-1) y la aplicación web de ensayo (umbracositecms-1-stage) **DEBEN** apuntar a bases de datos diferentes.

![Actualización de la cadena de conexión para la aplicación web de ensayo con la nueva base de datos de ensayo](./media/app-service-web-staged-publishing-realworld-scenarios/9umbconnstr.png)

Haga clic en **Obtener configuración de publicación** para la ranura de implementación **ensayo**. Se descargará un archivo de configuración de publicación que almacena toda la información que necesita Visual Studio o WebMatrix para publicar la aplicación desde la aplicación web de desarrollo local hasta la aplicación web de Azure.

 ![Obtención de configuración de publicación de la aplicación web de ensayo](./media/app-service-web-staged-publishing-realworld-scenarios/10getpsetting.png)

- Abra la aplicación web de desarrollo local en **WebMatrix** o **Visual Studio**. En este tutorial se usa WebMatrix, así que primero debe importar el archivo de configuración de publicación de la aplicación web de ensayo

![Importación de la configuración de publicación para Umbraco mediante WebMatrix](./media/app-service-web-staged-publishing-realworld-scenarios/11import.png)

- Revise los cambios en el cuadro de diálogo e implemente la aplicación web local en la aplicación web de Azure: *umbracositecms-1-stage*. Al implementar los archivos directamente en la aplicación web de ensayo, se omitirán todos los archivos de la `~/app_data/TEMP/` carpeta puesto que estos se volverán a generar la primera vez que se inicie la aplicación web de ensayo. También debe omitir el archivo `~/app_data/umbraco.config`, dado que también se volverá a generar.

![Revisión de los cambios de publicación en WebMatrix](./media/app-service-web-staged-publishing-realworld-scenarios/12umbpublish.png)

- Después de publicar correctamente la aplicación web local de Umbraco en la aplicación web de ensayo, busque esta última y ejecute algunas pruebas para descartar cualquier problema.

#### Configuración del módulo de implementación Courier2
Con el módulo [Courier2](http://umbraco.com/products/more-add-ons/courier-2) puede insertar contenido, hojas de estilo, módulos de desarrollo y mucho más desde una aplicación web de ensayo hasta la aplicación web de producción, con un simple clic con el botón derecho. De este modo se facilita el desarrollo y se reduce el riesgo de interrumpir la aplicación web de producción al implementar una actualización. Adquiera una licencia de Courier2 para el dominio `*.azurewebsites.net` y su dominio personalizado (por ejemplo, http://abc.com). Después de haber adquirido la licencia, coloque la licencia descargada (archivo .LIC) en la carpeta `bin`.

![Eliminación del archivo de licencia de la carpeta bin](./media/app-service-web-staged-publishing-realworld-scenarios/13droplic.png)

Descargue el paquete de Courier2 [aquí](https://our.umbraco.org/projects/umbraco-pro/umbraco-courier-2/). Inicie sesión en la aplicación web de ensayo, pongamos http://umbracocms-site-stage.azurewebsites.net/umbraco y haga clic en el menú **Developer** (Desarrollador) y seleccione **Packages** (Paquetes). Haga clic en **Install** (Instalar) para instalar el paquete local.

![Instalador de paquetes de Umbraco](./media/app-service-web-staged-publishing-realworld-scenarios/14umbpkg.png)

Cargue el paquete courier2 mediante el programa de instalación.

![Carga de paquete del módulo Courier](./media/app-service-web-staged-publishing-realworld-scenarios/15umbloadpkg.png)

Para configurarlo, debe actualizar el archivo courier.config en la carpeta **Config** de la aplicación web.

```xml
<!-- Repository connection settings -->
  <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
  <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear:  -->
        <repository name="production web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
            <url>http://umbracositecms-1.azurewebsites.net</url>
            <user>0</user>
            <!--<login>user@email.com</login> -->
            <!-- <password>user_password</password>-->
           <!-- <passwordEncoding>Clear</passwordEncoding>-->
           </repository>
  </repositories>
 ```

En `<repositories>`, introduzca la dirección URL del sitio de producción y la información de usuario. Si está usando el proveedor de pertenencia de Umbraco predeterminado, agregue el identificador del usuario de administración en la sección <user>. Si está usando el proveedor de pertenencia de Umbraco personalizado, use `<login>`,`<password>` para que el módulo Courier2 sepa cómo conectarse al sitio de producción. Para obtener más información, revise la [documentación](http://umbraco.com/help-and-support/customer-area/courier-2-support-and-download/developer-documentation) del módulo Courier.

De igual forma, instale el módulo Courier en el sitio de producción y configúrelo para que apunte a la aplicación web de ensayo en su archivo courier.config respectivo, tal como se muestra aquí.

```xml
  <!-- Repository connection settings -->
  <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
  <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear:  -->
        <repository name="Stage web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
            <url>http://umbracositecms-1-stage.azurewebsites.net</url>
            <user>0</user>
           </repository>
  </repositories>
```

Haga clic en la pestaña Courier2 en el panel de aplicaciones web de CMS de Umbraco y seleccione las ubicaciones. Debería ver el nombre del repositorio como se mencionó en `courier.config`. Haga esto tanto en aplicaciones web de producción como de ensayo.

![Visualización del repositorio de la aplicación web de destino](./media/app-service-web-staged-publishing-realworld-scenarios/16courierloc.png)

Ahora, vamos a implementar algo de contenido del sitio de ensayo en el sitio de producción. Vaya a Content (Contenido) y seleccione una página existente o cree una nueva. Seleccionaremos una página existente de mi aplicación web donde el título de la página cambia a **Getting Started – new** (Introducción: nuevo) y ahora haremos clic en **Save and Publish** (Guardar y publicar).

![Cambio del título de la página y publicación](./media/app-service-web-staged-publishing-realworld-scenarios/17changepg.png)

Ahora, seleccione la página modificada y *haga clic con el botón derecho* para ver todas las opciones. Haga clic en **Courier** para ver el cuadro de diálogo de implementación. Haga clic en **Deploy** (Implementar) para iniciar la implementación.

![Cuadro de diálogo de implementación del módulo Courier](./media/app-service-web-staged-publishing-realworld-scenarios/18dialog1.png)

Revise los cambios y haga clic en Continue (Continuar).

![Revisión de los cambios del cuadro de diálogo de implementación del módulo Courier](./media/app-service-web-staged-publishing-realworld-scenarios/19dialog2.png)

El registro de implementación muestra si la implementación es correcta.

 ![Visualización de los registros de implementación del módulo Courier](./media/app-service-web-staged-publishing-realworld-scenarios/20successdlg.png)

Examine la aplicación web de producción para ver si se reflejan los cambios.

 ![Examen de la aplicación web de producción](./media/app-service-web-staged-publishing-realworld-scenarios/21umbpg.png)

Para obtener más información sobre cómo usar Courier, revise la documentación.

#### Actualización de la versión de CMS de Umbraco

Courier no ayudará a la implementación con la actualización de una versión a otra de CMS de Umbraco. Al actualizar la versión de CMS de Umbraco, debe comprobar si hay incompatibilidades con los módulos personalizados o los módulos de terceros y las bibliotecas principales de Umbraco. Como procedimiento recomendado

1. Realice SIEMPRE una copia de seguridad de su aplicación web y de la base de datos antes de realizar una actualización. En la aplicación web de Azure, puede configurar copias de seguridad automáticas de los sitios web con la característica de copia de seguridad y restaurar el sitio en caso necesario mediante la característica de restauración. Para obtener más detalles, consulte [Realización de una copia de seguridad de las aplicaciones web](web-sites-backup.md) y [Restauración de la aplicación web](web-sites-restore.md).

2. Compruebe si los paquetes de terceros que usa son compatibles con la versión a la que va a actualizar. En la página de descarga del paquete, revise la compatibilidad del proyecto con la versión de CMS de Umbraco.

Para obtener más detalles sobre cómo actualizar la aplicación web localmente, siga las instrucciones que se mencionan [aquí](https://our.umbraco.org/documentation/getting-started/setup/upgrading/general).

Cuando se haya actualizado el sitio de desarrollo local, publique los cambios en la aplicación web de ensayo. Pruebe la aplicación y, si está todo bien, use el botón **Intercambiar** para **cambiar** el sitio de ensayo a la aplicación web de producción. Al realizar la operación de **intercambio**, puede ver los cambios que afectarán a la configuración de la aplicación web. Con esta operación de **intercambio**, lo que hacemos es cambiar las aplicaciones web y las bases de datos. Esto significa que, después del intercambio, la aplicación web de producción apuntará ahora a la base de datos umbraco-stage-db y la aplicación web de ensayo apuntará a la base de datos umbraco-prod-db.

![Vista previa de intercambio para la implementación de CMS de Umbraco](./media/app-service-web-staged-publishing-realworld-scenarios/22umbswap.png)

Las ventajas de intercambiar la aplicación web y la base de datos son las siguientes: 1. Ofrece la posibilidad de revertir a la versión anterior de la aplicación web con otro **intercambio** si hay algún problema con la aplicación. 2. Para realizar una actualización deberá implementar los archivos y la base de datos de la aplicación web de ensayo en la aplicación web y la base de datos de producción. Son muchas las cosas que pueden salir mal al implementar los archivos y las bases de datos. Mediante la característica **Intercambiar** de las ranuras, podemos reducir tanto el tiempo de inactividad durante una actualización como el riesgo de errores que pueden producirse al implementar los cambios. 3. Ofrece la posibilidad de hacer **pruebas A/B** mediante la característica de [pruebas en producción](https://azure.microsoft.com/documentation/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/).

Este ejemplo demuestra la flexibilidad de la plataforma, donde puede compilar módulos personalizados parecidos al módulo Courier de Umbraco para administrar la implementación entre entornos.

## Referencias
[Agile Software Development con el Servicio de aplicaciones de Azure](app-service-agile-software-development.md)

[Configuración de entornos de ensayo para aplicaciones web en el Servicio de aplicaciones de Azure](web-sites-staged-publishing.md)

[How to block web access to non-production deployment slots (Bloqueo del acceso web a ranuras de implementación que no son de producción)](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

<!---HONumber=AcomDC_0128_2016-->