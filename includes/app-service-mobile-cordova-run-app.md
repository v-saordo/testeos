
1. Visite el [Portal de Azure]. Haga clic en **Examinar todo** > **Aplicaciones móviles** > el back-end que acaba de crear. En la configuración de la aplicación de móvil, haga clic en **Inicio rápido** > **Cordova**. En **Configurar la aplicación cliente**, seleccione **Crear aplicación nueva** y, a continuación, haga clic en **Descargar**. Se descargará un proyecto de Cordova completo para que una aplicación previamente configurada pueda conectarse al back-end.

2. Desempaquete el archivo ZIP descargado en un directorio del disco duro.

3. Abra el proyecto con **Visual Studio**. Haga clic en **Abrir** > **Proyecto/solución...**.

4. Encuentre el archivo _sitename_.sln y haga clic en **Abrir**.

5. El emulador predeterminado es **Ripple - Nexus (Galaxy)**. Haga clic en la flecha desplegable junto al emulador y seleccione **Emulador de Android de Google**.

6. Haga clic en **Emulador de Android de Google**. Se generará y ejecutará el proyecto. Puede que vea una advertencia de seguridad de red del Emulador de Android de Google que solicita acceso a la red. Finalmente, se mostrará el Emulador de Android de Google y se ejecutará la aplicación.

7. En la aplicación, escriba un texto significativo, como _Realizar el tutorial_ y luego haga clic en el botón "Agregar". Esto envía una solicitud POST al back-end de Azure implementado anteriormente. El back-end inserta datos de la solicitud que está en la tabla TodoItem SQL y devuelve información acerca de los elementos recién almacenados a la aplicación móvil. La aplicación móvil muestra estos datos en la lista.

    ![](./media/app-service-mobile-cordova-quickstart/quickstart-startup.png)

[Portal de Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0218_2016-->