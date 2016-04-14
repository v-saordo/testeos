
Tiene la posibilidad de probar las notificaciones de inserción con su servicio móvil que se ejecuta en el equipo o máquina virtual local antes de publicar en Azure. Para ello, debe establecer información acerca del centro de notificaciones utilizado por su aplicación en el archivo web.config. Esta información se utiliza solo cuando se ejecuta localmente para conectar con el centro de notificaciones; se ignora cuando se publica en Azure.

1. Abra el archivo readme.html en la carpeta de proyecto de servicio móvil. 

	Esto muestra la página **La configuración push está casi completa** (si todavía no la tiene abierta). La sección **Paso 3: Modificar la configuración web** contiene la información de conexión al centro de notificaciones.

2. En su proyecto de servicio móvil en Visual Studio, abra el archivo Web.config del servicio; luego, en **Cadenas de conexión**, agregue la cadena de conexión **MS\_NotificationHubConnectionString** desde la página **La configuración push está casi completa**.

3. En **Configuración de la aplicación**, sustituya el valor de la opción de aplicación **MS\_NotificationHubName** por el nombre del centro de notificaciones encontrado en la página **La configuración push está casi completa**.

4. Haga clic con el botón secundario en el proyecto de servicio móvil y haga clic en **Depurar** y en **Iniciar nueva instancia**. Apunte la dirección URL raíz del servicio de la página de arranque mostrada en el explorador.

	Esta es la dirección URL del host local del proyecto back-end de .NET. Usará esta dirección para probar la aplicación en el servicio móvil que se ejecuta en el PC local.

Ahora, el proyecto de servicio móvil está configurado para conectarse al centro de notificaciones en Azure cuando se ejecuta localmente. Es importante usar el mismo nombre de centro de notificaciones y la misma cadena de conexión del portal porque esta configuración de proyecto que hay en Web.config se reemplaza por la configuración del portal cuando se ejecuta en Azure.

<!---HONumber=Nov15_HO4-->