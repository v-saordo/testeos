
1. Navegue a la [consola de Google Cloud](https://console.developers.google.com/project) e inicie sesión con las credenciales de su cuenta de Google+. 
 
2. Haga clic en **Create Project** (Crear proyecto), escriba un nombre de proyecto y luego haga clic en **Create** (Crear). Si se solicita, ejecute la comprobación de SMS y haga clic de nuevo en **Crear**.

   	![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)

	 Escriba el **nombre del nuevo proyecto** y haga clic en **Create project** (Crear proyecto).

3. Tome nota del número del proyecto que aparece en la sección **Projects**. Tendrá que establecer este valor como la variable *PROJECT\_ID* en el cliente.

4. En el panel de proyecto, haga clic en **Use Google APIs** (Usar API de Google) > **Cloud Messaging for Android** (Mensajería en la nube de Google para Android) y luego, en la página siguiente, haga clic en **Enable API** (Habilitar API).

5. En el API Manager (Administrador de API), haga clic en **Credentials** (Credenciales) > **Add credential** (Agregar credencial) > **API Key** (Clave de API).

   	![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-create-server-key.png)

6. En **Create a new key** (Crear una nueva clave), haga clic en **Server key** (Clave de servidor), escriba un nombre para la clave y haga clic en **Create** (Crear).

7. Anote el valor de **API KEY** (Clave de API).

	Usará este valor de clave de API para permitir que Azure lleve a cabo la autenticación con GCM y envíe notificaciones de inserción en nombre de su aplicación.

<!---HONumber=AcomDC_1203_2015-->