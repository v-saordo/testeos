7. Haga clic con el botón derecho en el proyecto de aplicación de API en el **Explorador de soluciones** y seleccione **Publicar** para volver a abrir el cuadro de diálogo de publicación. El perfil de publicación que creó antes aparecerá ya seleccionado. 

9. Haga clic en **Publicar** para comenzar el proceso de implementación.

	![Implementación de la aplicación de API](./media/app-service-api-pub-web-deploy/26-5-deployment-success-v3.png)

	La ventana **Actividad del Servicio de aplicaciones de Azure** muestra el progreso de la implementación.

	![Notificación de estado de la ventana de actividad del Servicio de aplicaciones de Azure](./media/app-service-api-pub-web-deploy/26-5-deployment-success-v4.png)

	Durante este proceso de implementación, Visual Studio intenta reiniciar de forma automática la *puerta de enlace*. La puerta de enlace es una aplicación web que controla las funciones administrativas de todas las aplicaciones de API en un grupo de recursos y debe reiniciarse para que reconozca los cambios en la definición de API o el archivo *apiapp.json* de una aplicación de API.
 
	Si usa otro método para implementar una aplicación de API o si Visual Studio no puede reiniciar la puerta de enlace, es posible que tenga que reiniciar manualmente la puerta de enlace. En los siguientes pasos se explica cómo hacerlo.

1. En el explorador, vaya al [Portal de vista previa de Azure](https://portal.azure.com).

2. Vaya a la hoja de la **aplicación de API** para la aplicación de API que implementó.

	Para obtener información sobre la hoja de la **aplicación de API** y cómo encontrarla, consulte [Administración de aplicaciones de API en el Servicio de aplicaciones de Azure](../articles/app-service-api/app-service-api-manage-in-portal.md).

4. Haga clic en el vínculo **Puerta de enlace**.

3. En la hoja **Puerta de enlace**, haga clic en **Reiniciar**.

	![](./media/app-service-api-pub-web-deploy/restartgateway.png)

<!---HONumber=Oct15_HO3-->