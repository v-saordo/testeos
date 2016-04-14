>[AZURE.NOTE]Para llevar a cabo este procedimiento, debe tener una cuenta de Google asociada a una dirección de correo electrónico comprobada. Para crear una cuenta de Google, vaya a <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Diríjase al sitio web [Google Cloud Console](https://console.developers.google.com/project), inicie sesión con las credenciales de su cuenta de Google y, a continuación, haga clic en **Crear proyecto**.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project.png)

   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project-2.png)

2. Escriba un nombre de proyecto, acepte los términos del servicio y haga clic en **Create** (Crear). Si se solicita, ejecute la comprobación de SMS y haga clic de nuevo en **Crear**.

3. Tome nota del número del proyecto que aparece en la sección **Projects**. Lo necesitará más adelante en el tutorial para rellenar el archivo de Android Manifest.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)

4. En la columna izquierda, expanda **API y autenticación**, haga clic en **API** y luego desplácese hacia abajo y haga clic en **mensajería en la nube para Android**. A continuación, haga clic en la página siguiente en **Enable API** y acepte los términos del servicio.

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-gcm-2.png)

5. Haga clic en **Credenciales** y, después, en **Agregar credenciales**-> **Clave API**.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

6. En **Create a new key**, haga clic en **Server key**. En la siguiente ventana, haga clic en **Create**.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)


   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key6.png)

7. Anote el valor de **API KEY** (Clave de API). Usará este valor de clave de API más adelante para configurar la sección de inserción nativa.

<!---HONumber=Nov15_HO1-->