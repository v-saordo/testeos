

1.  En el equipo Mac, inicie **Acceso a llaves**. Abra **Categoría** > **Mis certificados**. Busque el certificado SSL para exportar (que descargó anteriormente) y muestre su contenido. Seleccione solo el certificado sin seleccionar la llave privada y [expórtelo](https://support.apple.com/kb/PH20122?locale=en_US).

2. En el Portal de Azure, haga clic en **Examinar todo** > **Servicios de aplicaciones** > el back-end de la aplicación de móvil > **Configuración** > **Móvil** > **Push** > **Configurar los valores obligatorios** > **+ Centro de notificaciones**, proporcione un nombre y un espacio de nombres para el centro de notificaciones y después haga clic en el botón **Aceptar**.

  	![][1]

3. En la hoja **Crear centro de notificaciones**, haga clic en el botón **Crear**.
     
    Antes de continuar con el paso siguiente, haga clic en **Notificaciones**, para asegurarse de que la configuración del centro de notificaciones está completa.

4. En el Portal de Azure, haga clic en **Examinar todo** > **Servicios de aplicaciones** > el back-end de la aplicación de móvil > **Configuración** > **Móvil** > **Push** > **Servicios de notificaciones push de Apple** > **Cargar certificado**. Cargue el archivo .p12 y seleccione el **modo** correcto (dependerá de si generó un certificado SSL de cliente de Desarrollo o Distribución). El servicio ahora está configurado para trabajar con las notificaciones push en iOS.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png

<!---HONumber=AcomDC_1203_2015-->