<properties 
	pageTitle="Conversión de WordPress en multisitios en Servicio de aplicaciones de Azure" 
	description="Obtenga información acerca de cómo tomar un sitio web de WordPress existente creado en la galería de Azure y convertirlo en multisitio de WordPress" 
	services="app-service\web" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="01/12/2016" 
	ms.author="tomfitz"/>



# Conversión de WordPress en multisitios en Servicio de aplicaciones de Azure

## Información general

*Por [Ben Lobaugh][ben-lobaugh], [Microsoft Open Technologies Inc.][ms-open-tech]*

En este tutorial aprenderá a tomar un sitio web de WordPress existente creado a través de la galería en Azure y convertirlo en una instalación multisitio de WordPress. Adicionalmente, aprenderá a asignar un dominio personalizado a cada uno de los subsitios dentro de su instalación.

Se supone que tiene una instalación existente de WordPress. De lo contrario, siga las instrucciones que se proporcionan en [Creación de un sitio web de WordPress desde la galería de Azure][website-from-gallery].

La conversión de la instalación de un único sitio de WordPress existente a multisitio es generalmente bastante simple y muchos de los pasos iniciales que se presentan aquí provienen directamente de la página [Creación de una red][wordpress-codex-create-a-network] en [WordPress Codex](http://codex.wordpress.org).

Comencemos.

## Permitir multisitio

Primero necesita habilitar el multisitio mediante el archivo `wp-config.php` con la constante **WP\\_ALLOW\\_MULTISITE**. Hay dos métodos para editar los archivos de aplicaciones web: el primero es a través de FTP y el segundo mediante Git. Si no está familiarizado con la configuración de alguno de estos métodos, consulte los siguientes tutoriales:

* [Sitio web PHP con MySQL y FTP][website-w-mysql-and-ftp-ftp-setup]

* [Sitio web PHP con MySQL y Git][website-w-mysql-and-git-git-setup]

Abra el archivo `wp-config.php` con el editor de su preferencia y agregue el siguiente código encima de la línea `/* That's all, stop editing! Happy blogging. */`.

	/* Multisite */

	define( 'WP_ALLOW_MULTISITE', true );

¡Asegúrese de guardar el archivo y cargarlo de vuelta al servidor!

## Configuración de la red

Inicie sesión en el área *wp-admin* de su aplicación web; debería ver un elemento nuevo bajo el menú **Herramientas** llamado **Configuración de red**. Haga clic en **Configuración de red** y complete los detalles de su red.

![Pantalla de configuración de la red][wordpress-network-setup]

Este tutorial usa el esquema de sitios de *Subdirectorios* porque siempre funciona, y más adelante vamos a configurar dominios personalizados para cada subsitio. Sin embargo, debería ser posible configurar una instalación de subdominio si asigna un dominio a través del [Portal de Azure](https://portal.azure.com) y configura correctamente un DNS con caracteres comodín.

Para obtener más información sobre la configuración de subdominios frente a subdirectorios, consulte el artículo sobre [tipos de red multisitio][wordpress-codex-types-of-networks] en WordPress Codex.

## Habilitación de la red

La red está ahora configurada en la base de datos, pero todavía queda un paso para habilitar la funcionalidad de red. Finalice la configuración de `wp-config.php` y asegúrese de que `web.config` redirige correctamente cada sitio.


Después de hacer clic en el botón **Install** en la página *Network Setup*, WordPress intentará actualizar `wp-config.php` y los archivos `web.config`. No obstante, siempre debe comprobar los archivos para asegurarse de que las actualizaciones se realizaron correctamente. De lo contrario, esta pantalla le presentará las actualizaciones necesarias. Edite y guarde los archivos.


Después de realizar las actualizaciones, necesitará cerrar la sesión y volver a iniciarla en el panel de wp-admin.

Debería aparecer ahora un menú adicional en la barra de administración con la etiqueta **Mis sitios**. Este menú le permite controlar su red nueva a través del panel **Administrador de red**.

## Incorporación de dominios personalizados

El complemento [WordPress MU Domain Mapping][wordpress-plugin-wordpress-mu-domain-mapping] facilita enormemente la incorporación de dominios personalizados a cualquier sitio de su red. Para que el complemento funcione correctamente, necesita realizar cierta configuración adicional en el Portal y también en el registrador de su dominio.

## Habilitación de la asignación de dominios a la aplicación web

El modo de plan **Gratis** del [Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714) no admite la adición de dominios personalizados para Aplicaciones web. Necesitará cambiar al modo **Compartido** o **Estándar**. Para ello, siga estos pasos:

* Inicie sesión en el portal de Azure y localice su aplicación web. 
* Haga clic en la pestaña **Escalar verticalmente** en **Configuración**.
* En **General**, seleccione *COMPARTIDO* o *ESTÁNDAR*
* Haga clic en **Guardar**

Es posible que reciba un mensaje pidiéndole que verifique el cambio y que confirme que su aplicación web puede incurrir en costes, según el uso y el resto de la configuración que realice.

El procesamiento de la nueva configuración tarda unos pocos segundos, de modo que ahora es un buen momento para empezar a configurar su dominio.

## Verificación de su dominio

Antes de que Aplicaciones web de Azure permita asignar un dominio al sitio, primero necesita verificar que tiene la autorización para asignar el dominio. Para ello, debe agregar un registro nuevo CNAME a su entrada de DNS.

* Inicie sesión en el administrador de DNS de su dominio.
* Cree un nuevo *awverify* de CNAME
* Haga que *awverify* apunte a *awverify.YOUR\_DOMAIN.azurewebsites.net*

Puede pasar algún tiempo antes de que los cambios de DNS surtan efecto, de modo que si los siguientes pasos no funcionan inmediatamente, prepárese una taza de café y vuelva después para volver a intentarlo.

## Incorporación del dominio a la aplicación web

Vuelva a la aplicación web a través del Portal de Azure, haga clic en **Configuración** y, a continuación, haga clic en **Dominios personalizados y SSL**.

Cuando aparezca *Configuración de SSL*, verá los campos donde especificará todos los dominios que desea asignar a la aplicación web. Si un dominio no aparece en este lugar, no estará disponible para asignarlo dentro de WordPress, sin considerar la configuración de DNS del dominio.

![Cuadro de diálogo de administración de dominios personalizados][wordpress-manage-domains]

Después de escribir su dominio en el cuadro de texto, Azure verificará el registro CNAME que creó anteriormente. Si el DNS no se ha propagado completamente, aparecerá un indicador rojo. Si fue correcto, verá una marca de verificación verde.

Tome nota de la dirección IP que aparece en la parte inferior del cuadro de diálogo. La necesitará para configurar el registro A para su dominio.

## Configuración del registro A del dominio

Si los demás pasos fueron correctos, ahora puede asignar el dominio a la aplicación web de Azure mediante un registro A de DNS.

Es importante tener en cuenta aquí que las aplicaciones web de Azure aceptan tanto los registros CNAME como A; sin embargo, *debe* usar un registro A para permitir una asignación correcta del dominio. Un registro CNAME no se puede reenviar a otro CNAME, que es lo que Azure creó con YOUR\_DOMAIN.azurewebsites.net.

Con la ayuda de la dirección IP del paso anterior, vuelva a su administrador de DNS y configure el registro A para que apunte a dicha dirección IP.


## Instalación y configuración del complemento

Multisitio de WordPress no dispone actualmente de un método integrado para asignar dominios personalizados. Sin embargo, hay un complemento llamado [WordPress MU Domain Mapping][wordpress-plugin-wordpress-mu-domain-mapping] que agrega la funcionalidad. Inicie sesión en la sección Administrador de red de su sitio e instale el complemento **WordPress MU Domain Mapping**.

Después de instalar y activar el complemento, visite **Configuración** > **Asignación de dominios** para configurarlo. En el primer cuadro de texto, *Dirección IP del servidor*, indique la dirección IP que usó para configurar el registro A para el dominio. Establezca las *Opciones de dominio* que desee (los valores predeterminados suelen ser válidos) y haga clic en **Guardar**.

## Asignación del dominio

Visite el **Panel** del sitio al que desee asignar el dominio. Haga clic en **Herramientas** > **Asignación de dominios**, escriba el nuevo dominio en el cuadro de texto y haga clic en **Agregar**.

De manera predeterminada, el dominio nuevo se sobrescribirá con el dominio del sitio generado automáticamente. Si desea enviar todo el tráfico al dominio nuevo, marque el cuadro *Dominio principal para este blog* antes de guardar. Puede agregar un número ilimitado de dominios a un sitio, pero solo uno puede ser primario.

## Vuelva a hacerlo

Aplicaciones web de Azure permite agregar un número ilimitado de dominios a un sitio web. Para agregar otro dominio, necesitará ejecutar las secciones **Verificación de su dominio** y **Configuración del registro A del dominio** para cada dominio.

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

[ben-lobaugh]: http://ben.lobaugh.net
[ms-open-tech]: http://msopentech.com
[website-from-gallery]: https://www.windowsazure.com/develop/php/tutorials/website-from-gallery/
[wordpress-codex-create-a-network]: http://codex.wordpress.org/Create_A_Network
[website-w-mysql-and-ftp-ftp-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-ftp/#header-0
[website-w-mysql-and-git-git-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-git/#header-1
[wordpress-network-setup]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-network-setup.png
[wordpress-codex-types-of-networks]: http://codex.wordpress.org/Before_You_Create_A_Network#Types_of_multisite_network
[wordpress-plugin-wordpress-mu-domain-mapping]: http://wordpress.org/extend/plugins/wordpress-mu-domain-mapping/

[wordpress-manage-domains]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-manage-domains.png

 

<!---HONumber=AcomDC_0128_2016-->