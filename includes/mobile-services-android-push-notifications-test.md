
###Configuración de un emulador de Android para realizar pruebas
Cuando ejecute esta aplicación en el emulador, asegúrese de utilizar un dispositivo virtual Android (AVD) compatible con las API de Google.

> [AZURE.IMPORTANT]Para recibir notificaciones de inserción, debe configurar la cuenta de Google en el dispositivo virtual de Android (en el emulador, diríjase a **Settings** (Configuración) y haga clic en **Add Account** [Agregar cuenta]). Además, asegúrese de que el emulador esté conectado a Internet.

1. En **Tools** (Herramientas), haga clic en **Open Android Emulator Manager** (Abrir Administrador de emulador Android), seleccione su dispositivo y, a continuación, haga clic en **Edit** (Editar).

   	![Administrador de dispositivos virtuales de Android](./media/mobile-services-android-push-notifications-test/notification-hub-create-android-app7.png)

2. Seleccione **Google APIs** (API de Google) en **Target** (Destino) y, a continuación, haga clic en **OK** (Aceptar).

   	![Edite el dispositivo virtual Android](./media/mobile-services-android-push-notifications-test/notification-hub-create-android-app8.png)

3. En la barra de herramientas de la parte superior, haga clic en **Run** (Ejecutar) y, a continuación, seleccione la aplicación. Esto inicia el emulador y ejecuta la aplicación.

  La aplicación recupera el valor *registrationId* de GCM y se registra con el Centro de notificaciones.

###La inserción de un nuevo elemento genera una notificación.

1. En la aplicación, escriba texto significativo como _A new Mobile Services task_ y, a continuación, haga clic en el botón **Add** (Agregar).

2. Deslice el dedo hacia abajo desde la parte superior de la pantalla para abrir el Centro de notificaciones del dispositivo y visualizar la notificación.

	![Visualice la notificación en el centro de notificaciones](./media/mobile-services-android-push-notifications-test/notification-area-received.png)

<!---HONumber=Oct15_HO3-->