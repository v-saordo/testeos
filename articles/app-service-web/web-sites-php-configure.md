<properties
	pageTitle="Configuración de PHP en Aplicaciones web del Servicio de aplicaciones de Azure | Microsoft Azure"
	description="Obtenga información acerca de cómo configurar la instalación de PHP predeterminada o agregar una instalación de PHP personalizada para Aplicaciones web en el Servicio de aplicaciones de Azure."
	services="app-service"
	documentationCenter="php"
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="tomfitz"/>

#Configuración de PHP en Aplicaciones web del Servicio de aplicaciones de Azure

## Introducción

En esta guía se explica cómo configurar el tiempo de ejecución de PHP integrado en Aplicaciones web en el [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714), ofrecer un tiempo de ejecución de PHP personalizado y habilitar extensiones. Para utilizar el Servicio de aplicaciones, regístrese para obtener acceso a la [evaluación gratuita]. Para obtener el máximo partido de esta guía, primero debe crear una aplicación web PHP en el Servicio de aplicaciones.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Cómo: Cambiar la versión de PHP integrada
PHP 5.4 está instalado de forma predeterminada y está disponible para utilizarse inmediatamente después de crear una aplicación web de Azure. La mejor forma de ver la revisión publicada disponible, su configuración predeterminada y las extensiones habilitadas es implementando un script que llame a la función [phpinfo()].

Las versiones de PHP 5.5 y PHP 5.6 también están disponibles, pero no están habilitadas de forma predeterminada. Para actualizar la versión de PHP, siga uno de estos métodos:

### Portal de Azure

1. Vaya a la aplicación web en el [Portal de Azure ](https://portal.azure.com)y haga clic en el botón **Configuración**.

	![Configuración de aplicaciones web][settings-button]

2. En la hoja **Configuración**, seleccione **Configuración de la aplicación** y elija la nueva versión de PHP.

    ![Configuración de la aplicación][application-settings]

3. Haga clic en el botón **Guardar**, situado en la parte superior de la hoja **Configuración de aplicaciones web**.

	![Guardar la configuración][save-button]

### Azure PowerShell (Windows)

1. Abra Azure PowerShell e inicie sesión en su cuenta:

        PS C:\> Login-AzureRmAccount

2. Establezca la versión PHP para la aplicación web.

        PS C:\> Set-AzureWebsite -PhpVersion [5.4 | 5.5 | 5.6] -Name {site-name}

3. La versión de PHP ya está configurada. Puede confirmar esta configuración:

        PS C:\> Get-AzureWebsite -Name {site-name} | findstr PhpVersion

### Interfaz de línea de comandos de Azure (Linux, Mac, Windows)

Para usar la interfaz de línea de comandos de Azure, debe tener **Node.js** instalado en el equipo.

1. Abra Terminal e inicie sesión en su cuenta.

        azure login

2. Establezca la versión PHP para la aplicación web.

        azure site set --php-version [5.4 | 5.5] {site-name}

3. La versión de PHP ya está configurada. Puede confirmar esta configuración:

        azure site show {site-name}


## Cómo: Cambiar las configuraciones de PHP integradas

En todos los tiempos de ejecución de PHP integrados es posible cambiar las opciones de configuración siguiendo los pasos que se indican a continuación. (Para obtener información acerca de las directivas, consulte la [lista de directivas de php.ini ]).

### Cambiar la configuración de PHP\_INI\_USER, PHP\_INI\_PERDIR, PHP\_INI\_ALL

1. Agregue un archivo [.user.ini] al directorio raíz.
2. Agregue la configuración al archivo `.user.ini` usando la misma sintaxis que usaría en un archivo `php.ini`. Por ejemplo, si desea activar la configuración `display_errors` y configurar `upload_max_filesize` como 10M, el archivo `.user.ini` debería contener este texto:

		; Example Settings
		display_errors=On
		upload_max_filesize=10M

3. Implemente la aplicación web.
4. Reinicie la aplicación web. (Es necesario reiniciar porque la frecuencia con la que PHP lee los archivos `.user.ini` depende de la configuración `user_ini.cache_ttl`, que es una configuración a nivel de sistema con un valor predeterminado de 300 segundos (5 minutos). El reinicio de la aplicación web fuerza a PHP a leer la nueva configuración del archivo `.user.ini`).

Como alternativa al uso de un archivo `.user.ini`, puede usar la función[ ini\_set()] en los scripts para definir las opciones de configuración que no son directivas a nivel de sistema.

### Cambiar la configuración de PHP\_INI\_SYSTEM

1. Agregar una configuración de aplicación a la aplicación web con la clave `PHP_INI_SCAN_DIR` y el valor `d:\home\site\ini`
2. Cree un archivo `settings.ini` utilizando la consola Kudu (http://&lt;site-name&gt;.scm.azurewebsite.net) en el directorio `d:\home\site\ini`.
3. Agregue la configuración al archivo `settings.ini`, para ello, use la misma sintaxis que usaría en un archivo php.ini. Por ejemplo, si desea señalar la configuración `curl.cainfo` en un archivo `*.crt` y establecer la configuración de 'wincache.maxfilesize' en 512 KB, su archivo `settings.ini`debería contener este texto:

		; Example Settings
		curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
		wincache.maxfilesize=512
4. Reinicie la aplicación web para cargar los cambios.

## Cómo: Habilitar las extensiones en el tiempo de ejecución predeterminado de PHP
Como se ha mencionado en la sección anterior, la mejor forma de ver la versión de PHP predeterminada, su configuración predeterminada y las extensiones habilitadas es implementar un script que llame a la función [phpinfo()]. Para habilitar extensiones adicionales, siga los pasos que se detallan a continuación.

### Configurar mediante configuración de ini

1. Agregue un directorio `ext` al directorio `d:\home\site`.
2. Coloque los archivos con extensión `.dll` en el directorio `ext` (por ejemplo, `php_mongo.dll` y `php_xdebug.dll`). Asegúrese de que las extensiones sean compatibles con la versión predeterminada de PHP (que es, para lo que aquí se expone, PHP 5.4) y que sean compatibles con VC9 y no seguras para subprocesos (nts).
3. Agregar una configuración de aplicación a la aplicación web con la clave `PHP_INI_SCAN_DIR` y el valor `d:\home\site\ini`
4. Cree un archivo `ini` en `d:\home\site\ini` denominado `extensions.ini`.
5. Agregue la configuración al archivo `extensions.ini`, para ello, use la misma sintaxis que usaría en un archivo php.ini. Por ejemplo, si desea habilitar las extensiones de MongoDB y XDebug su archivo `extensions.ini` debería contener este texto:

		; Enable Extensions
		extension=d:\home\site\ext\php_mongo.dll
		zend_extension=d:\home\site\ext\php_xdebug.dll
6. Reinicie la aplicación web para cargar los cambios.

### Configurar a través de la configuración de la aplicación

1. Agregue un directorio `bin` al directorio raíz.
2. Coloque los archivos con extensión `.dll` en el directorio `bin` (por ejemplo, `php_mongo.dll`). Asegúrese de que las extensiones sean compatibles con la versión predeterminada de PHP (que es, para lo que aquí se expone, PHP 5.4) y que sean compatibles con VC9 y no seguras para subprocesos (nts).
3. Implemente la aplicación web.
4. Vaya a la aplicación web en el Portal de Azure y haga clic en el botón **Configuración**.

	![Configuración de aplicaciones web][settings-button]

5. En la hoja **Configuración**, seleccione **Configuración de la aplicación** y desplácese a la sección **Configuración de aplicaciones**.
6. En la sección **Configuración de aplicaciones**, cree una clave **PHP\_EXTENSIONS**. El valor de esta clave es una ruta de acceso relativa a la raíz del sitio web: **bin\\your-ext-file**.

	![Habilitar extensiones en app settings][php-extensions]

7. Haga clic en el botón **Guardar**, situado en la parte superior de la hoja **Configuración de aplicaciones web**.

	![Guardar la configuración][save-button]

También se pueden usar las extensiones Zend mediante una clave **PHP\_ZENDEXTENSIONS**. Para habilitar varias extensiones, incluya una lista separada por comas de los archivos `.dll` como valor de configuración de aplicaciones.


## Cómo: Usar un tiempo de ejecución de PHP personalizado
En lugar del tiempo de ejecución de PHP, Aplicaciones web del Servicio de aplicaciones puede usar un tiempo de ejecución de PHP facilitado por el usuario para ejecutar scripts PHP. El tiempo de ejecución que facilita se puede configurar mediante un archivo `php.ini` que también usted facilita. Para usar un tiempo de ejecución de PHP personalizado en Aplicaciones web, siga estos pasos.

1. Obtenga una versión compatible con VC9 y VC11 no segura para subprocesos de PHP para Windows. Las versiones recientes de PHP para Windows se pueden encontrar aquí: [http://windows.php.net/download/]. Las versiones anteriores pueden encontrarse archivadas aquí: [http://windows.php.net/downloads/releases/archives/].
2. Modifique el archivo `php.ini` para el tiempo de ejecución. Tenga en cuenta que Aplicaciones web omitirá cualquier configuración que se corresponda con directivas que sean solo de nivel de sistema. (Para obtener información acerca de las directivas solo a nivel de sistema, consulte la [lista de directivas de php.ini]).
3. También puede agregar extensiones al tiempo de ejecución de PHP y habilitarlas en el archivo `php.ini`.
4. Agregue un directorio `bin` al directorio raíz y coloque en él el directorio que contiene el tiempo de ejecución de PHP (por ejemplo, `bin\php`).
5. Implemente la aplicación web.
4. Vaya a la aplicación web en el Portal de Azure y haga clic en el botón **Configuración**.

	![Configuración de aplicaciones web][settings-button]

7. En la hoja **Configuración**, seleccione **Configuración de aplicaciones** y desplácese a la sección **Asignaciones de controlador**. Agregue `*.php` al campo Extensión y agregue la ruta de acceso al ejecutable `php-cgi.exe`. Si coloca el tiempo de ejecución de PHP en el directorio `bin` de la raíz de la aplicación, la ruta de acceso será `D:\home\site\wwwroot\bin\php\php-cgi.exe`.

	![Especificar el controlador en hander mappings][handler-mappings]

8. Haga clic en el botón **Guardar**, situado en la parte superior de la hoja **Configuración de aplicaciones web**.

	![Guardar la configuración][save-button]

## Pasos siguientes

Para más información, vea el [Centro para desarrolladores de PHP](/develop/php/).

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

[evaluación gratuita]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[lista de directivas de php.ini]: http://www.php.net/manual/en/ini.list.php
[lista de directivas de php.ini ]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ ini\_set()]: http://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png
 

<!---HONumber=AcomDC_0302_2016-->