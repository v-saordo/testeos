
* Siga los pasos que aparecen en [Instalación de una identidad de firma SSL de cliente en el servidor](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW15) para exportar el certificado que descargó en el paso anterior a un archivo .p12.

* En el Portal de Azure clásico, haga clic en **Servicios móviles** > su aplicación > la pestaña **Insertar** > **Configuración de notificaciones de inserción de Apple** > "**Cargar**. Cargue el archivo .p12 y asegúrese de que esté seleccionado el **modo** correcto (ya sea Espacio aislado o Producción, que dependerá si generó un certificado SSL de cliente de Desarrollo o Distribución). El servicio móvil ahora está configurado para trabajar con las notificaciones push en iOS.

<!---HONumber=AcomDC_1203_2015-->