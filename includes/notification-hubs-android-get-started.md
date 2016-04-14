

>[AZURE.NOTE]Para llevar a cabo este procedimiento, debe tener una cuenta de Google asociada a una dirección de correo electrónico comprobada. Para crear una cuenta de Google, vaya a <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.


1. Diríjase al sitio web <a href="http://cloud.google.com/console" target="_blank">Google Cloud Console</a>, inicie sesión con las credenciales de su cuenta de Google y, a continuación, haga clic en **Create Project**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)

	>[AZURE.NOTE]Si ya tiene un proyecto existente, se le dirigirá a la página <strong>Projects</strong> (Proyectos) después de iniciar sesión. Para crear un proyecto nuevo desde el panel, expanda <strong>API Project</strong> (Proyecto de API), haga clic en <strong>Create...</strong> (Crear) en <strong>Other projects</strong> (Otros proyectos) y, a continuación, escriba un nombre de proyecto y haga clic en <strong>Create project</strong> (Crear proyecto).

2. Escriba un nombre de proyecto, acepte los términos del servicio y haga clic en **Create** (Crear). Si se solicita, ejecute la comprobación de SMS y haga clic de nuevo en **Crear**.

3. Tome nota del número del proyecto que aparece en la sección **Projects**.

	Más adelante en este tutorial, configurará este valor como la variable PROJECT\_ID en el cliente.

4. En la columna izquierda, expanda **API y autenticación**, haga clic en **API** y, a continuación, desplácese hacia abajo y haga clic en el botón de alternancia para habilitar **Servicio de mensajería en la nube de Google para Android** y acepte las condiciones del servicio.

	![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5. Haga clic en **Credentials** y, a continuación, en **Create new Key**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6. En **Create a new key**, haga clic en **Server key**. En la siguiente ventana, haga clic en **Create**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7. Anote el valor de **API KEY** (Clave de API).

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png)

	Usará este valor de clave de API para permitir que Azure lleve a cabo la autenticación con GCM y envíe notificaciones de inserción en nombre de su aplicación.

<!---HONumber=Oct15_HO3-->