
1. En el [Portal de Azure](https://azure.portal.com/), haga clic en **Examinar** > **Servicios de aplicaciones**. A continuación, haga clic en el back-end de la aplicación móvil y en **Todas las configuraciones** y, por último, en **Móvil**, haga clic en **Push**.

2. En Servicios de notificación push de **Windows (WNS)**, escriba la **clave de seguridad** (secreto de cliente) y el **SID del paquete** que ha obtenido en el sitio de Servicios Live. Por último, haga clic en **Guardar**.

    ![Establezca la clave de API de GCM en el portal](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

El back-end de la aplicación móvil está ahora configurado para usar WNS para enviar notificaciones push a la aplicación de Windows por medio de su centro de notificaciones.

<!---HONumber=AcomDC_1203_2015-->