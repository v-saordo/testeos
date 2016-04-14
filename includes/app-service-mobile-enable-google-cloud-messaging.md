1. Vaya al sitio web de la [consola de nube de Google](https://console.developers.google.com/project), inicie sesión con las credenciales de su cuenta de Google y, a continuación, haga clic en **Select a project** (seleccionar un proyecto), y después en **Create a project** (crear un proyecto).

2. Escriba un nombre de proyecto, acepte los términos del servicio y haga clic en **Create** (Crear). Si se solicita, ejecute la comprobación de SMS y haga clic de nuevo en **Crear**.

3. Tome nota del número del proyecto que aparece en la sección **Projects**.

	Más adelante en este tutorial, configurará este valor como la variable PROJECT\_ID en el cliente.

4. Haga clic en **Enable and manage APIs** (habilitar y administrar API) en **User Google APIs** (API de Google de usuario) y haga clic en **Cloud Messaging for Android** (mensajería en la nube para Android). A continuación, en la página siguiente, haga clic en **Enable API** (habilitar API).

5. Haga clic en **Credentials** (credenciales) y, después, en **Add Credential** (agregar credenciales) -> **API Key** (clave API).

6. En **Create a new key**, haga clic en **Server key**. En la siguiente ventana, haga clic en **Create**.

7. Anote el valor de **API KEY** (Clave de API).

	Usará este valor de clave de API para permitir que Azure lleve a cabo la autenticación con GCM y envíe notificaciones de inserción en nombre de su aplicación.

<!---HONumber=AcomDC_1203_2015-->