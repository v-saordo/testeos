> [AZURE.IMPORTANT]Para recibir notificaciones de inserción de Mobile Engagement, debe habilitar `Silent Remote Notifications` en su aplicación. Deberá agregar el valor remote-notification a la matriz UIBackgroundModes en el archivo Info.plist.

1. Abra el archivo `info.plist` del proyecto
2. Haga clic con el botón derecho en el elemento superior de la lista (`Information Property List`) y agregue una nueva fila

	![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push1.png)

3. En la nueva fila, escriba `Required background modes`

	![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push2.png)

4. Haga clic en la flecha izquierda para expandir la fila
5. Agregue el siguiente valor al elemento 0 `App downloads content in response to push notifications`

	![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push3.png)

6. Una vez realizado el cambio, info.plist XML debe contener la clave y el valor siguientes:

	    <key>UIBackgroundModes</key>
	    <array>
	    <string>remote-notification</string>
	    </array>

7. Si está usando **Xcode 7+** y **iOS 9+**:
	- Habilite **Notificaciones push** en Destinos > Su nombre de destino > Capacidades.

<!---HONumber=Oct15_HO3-->