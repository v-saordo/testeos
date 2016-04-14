Azure Marketplace pone a disposición del usuario una gran variedad de populares aplicaciones web desarrolladas por Microsoft, compañías de terceros e iniciativas de software de código abierto. Las aplicaciones web creadas desde Azure Marketplace no requieren la instalación de ningún software que no sea el explorador utilizado para conectarse al [Portal de vista previa de Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

En este tutorial, aprenderá a:

- Crear una nueva aplicación web mediante Azure Marketplace.

- Implementación de la aplicación web mediante el Portal de vista previa de Azure.
 
Va a crear un blog de WordPress que utiliza una plantilla predeterminada. La siguiente ilustración muestra la aplicación completada:


![Blog de WordPress][13]

>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de suscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Creación de una aplicación web en el portal

1. Inicie sesión en el Portal de vista previa de Azure.

2. Haga clic en el icono **Marketplace** para abrir Azure Marketplace:

    ![Icono de Marketplace][marketplace]

    O bien, haga clic en el icono **Nuevo** en la superior derecha del panel y seleccione **Marketplace** en la parte inferior de la lista.
	
    ![Crear nuevo][5]
	
3. Seleccione **Web + Móvil**. Busque **WordPress** y haga clic en el icono **WordPress**.

	![WordPress desde la lista][7]
	
5. Después de leer la descripción de la aplicación de WordPress, seleccione **Crear**.

6. Haga clic en **Aplicación web** y proporcione los valores necesarios para configurar la aplicación web.
	
    ![configurar su aplicación][8]

7. Haga clic en **Base de datos** y proporcione los valores necesarios para configurar la base de datos MySQL.

    ![configurar base de datos][database]

8. Proporcione un nombre para un nuevo grupo de recursos.

    ![Definición de un grupo de recursos][groupname]

8. Si es necesario, haga clic en **SUSCRIPCIÓN** y especifique la suscripción que se va a usar.

7. Cuando haya terminado de definir la aplicación web, haga clic en **Crear** y espere mientras se crea la nueva aplicación web.

   Una vez creada la aplicación, verá el grupo de recursos que contiene la aplicación web y la base de datos.

   ![mostrar el grupo][resourcegroup]

## Inicio y administración de la aplicación web de WordPress
	
1. Haga clic en la nueva aplicación para ver los detalles acerca de la aplicación.

    ![panel de inicio][10]

2. En la página **Aspectos básicos**, haga clic en **Examinar** o en el vínculo situado en **Dirección URL** para abrir la página de bienvenida de la aplicación web.

    ![URL del sitio][browse]

3. Si no ha instalado WordPress, escriba la información de configuración apropiada que solicite WordPress y haga clic en **Instalar WordPress** para finalizar la configuración y abrir la página de inicio de sesión de la aplicación web.

4. Haga clic en **Inicio de sesión** y escriba sus credenciales.

5. Tendrá una nueva aplicación web WordPress con un aspecto similar al de la aplicación web que se muestra a continuación.

	![su sitio de WordPress][13]






[5]: ./media/website-from-gallery/start-marketplace.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/search-web-app.png
[8]: ./media/website-from-gallery/set-web-app.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/select-web.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png
[webapps]: ./media/website-from-gallery/selectwebapps.png
[database]: ./media/website-from-gallery/set-db.png
[resourcegroup]: ./media/website-from-gallery/show-rg.png
[browse]: ./media/website-from-gallery/browse-web.png
[marketplace]: ./media/website-from-gallery/marketplace-icon.png
[groupname]: ./media/website-from-gallery/set-rg.png

<!---HONumber=Oct15_HO3-->