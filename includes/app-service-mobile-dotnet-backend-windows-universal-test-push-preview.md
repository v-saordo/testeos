
1. Haga clic con el botón derecho en el proyecto de la Tienda Windows, haga clic en **Establecer como proyecto de inicio** y luego presione la tecla F5 para ejecutar la aplicación de la Tienda Windows.
	
	Cuando la aplicación se inicia, el dispositivo se registra para recibir notificaciones push.

2. Detenga la aplicación de la Tienda Windows y repita el paso anterior para ejecutar la aplicación de la Tienda Windows Phone.

	En este momento, ambos dispositivos están registrados para recibir notificaciones push.

3. Vuelva a ejecutar la aplicación de la Tienda Windows, escriba texto en **Insertar un TodoItem** y haga clic en **Guardar**.

   	![](./media/mobile-services-javascript-backend-windows-universal-test-push/mobile-quickstart-push1.png)

   	Después de hacer esto, las aplicaciones de la Tienda Windows y de Windows Phone recibirán una notificación push de WNS.

   	![](./media/mobile-services-javascript-backend-windows-universal-test-push/mobile-quickstart-push2.png)

	La notificación se muestra en Windows Phone aunque la aplicación no se esté ejecutando.

   	![](./media/mobile-services-javascript-backend-windows-universal-test-push/mobile-quickstart-push5-wp8.png)

<!---HONumber=August15_HO6-->