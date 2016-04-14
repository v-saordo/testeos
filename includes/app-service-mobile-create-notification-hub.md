Siga estos pasos para crear un nuevo centro de notificaciones para usar con las notificaciones push. Si ya dispone de un centro de notificaciones, también puede conectarlo al back-end de la aplicación móvil.

1. En el [Portal de Azure], haga clic en **Examinar** > **Servicios de aplicaciones** y después haga clic en el back-end de la aplicación móvil y en **Todas las configuraciones**; luego, en **Móvil**, haga clic en **Push** > **Centro de notificaciones**.

2. Haga clic en **+Centro de notificaciones**, escriba un nuevo nombre de **Centro de notificaciones**, que puede ser el mismo que el back-end de aplicación móvil, escriba un nuevo nombre de espacio de nombres o utilice uno existente, haga clic en **Aceptar** y finalmente en **Crear**.

	![](./media/app-service-mobile-create-notification-hub/create-new-hub-flow.png)

	Esto crea un nuevo centro de notificaciones y lo conecta a la aplicación móvil. Si dispone de un centro de notificaciones existente, puede conectarlo al back-end de la aplicación móvil en lugar de crear uno nuevo.

Ya ha conectado un centro de notificaciones al back-end de la aplicación móvil. Más adelante podrá configurar este centro de notificaciones para que se conecte a un servicio de notificación de plataforma (PNS) que envíe notificaciones push al dispositivo nativo.

[Portal de Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_1203_2015-->