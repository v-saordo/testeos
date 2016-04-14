1. En el **Explorador de soluciones**, haga clic en el proyecto (no en la solución) y en **Publicar**. 

	![Opción de menú de publicación de proyecto](./media/app-service-api-pub-web-create/20-publish-gesture-v3.png)

2. Haga clic en la pestaña **Perfil** y en **Aplicaciones de API de Microsoft Azure (vista previa)**.

	![Cuadro de diálogo Publicación web](./media/app-service-api-pub-web-create/21-select-api-apps-for-deployment-v2.png)

3. Haga clic en **Nuevo** para aprovisionar una nueva aplicación de API en su suscripción de Azure.

	![Selección del cuadro de diálogo Servicios de API existentes](./media/app-service-api-pub-web-create/23-publish-to-apiapps-v3.png)

4. En el cuadro de diálogo **Crear una aplicación de API**, escriba lo siguiente:

	- En **Nombre de la aplicación de API**, escriba el nombre que se usa para este tutorial. 
	- Si tiene varias suscripciones de Azure, seleccione la que desee usar.
	- En el **Plan del Servicio de aplicaciones**, seleccione de entre los planes del Servicio de aplicaciones o elija **Crear nuevo plan del Servicio de aplicaciones** y escriba el nombre de un nuevo plan. 
	- En **Grupo de recursos**, seleccione de entre los grupos de recursos existentes o elija **Crear nuevo grupo de recursos** y escriba un nombre. 
	- En **Nivel de acceso**, seleccione **Disponible para cualquier persona**. Puede restringir el acceso más adelante a través del Portal de vista previa de Azure.
	- En **Región**, seleccione una región cercana.  

	![Configuración del cuadro de diálogo de Aplicación web de Microsoft Azure](./media/app-service-api-pub-web-create/24-new-api-app-dialog-v3.png)

5. Haga clic en **Aceptar** para crear la aplicación de API en su suscripción.

	Como este proceso puede tardar unos minutos, Visual Studio muestra un cuadro de diálogo de confirmación.

6. Haga clic en **Aceptar** en el cuadro de diálogo de confirmación.
 
	El proceso de aprovisionamiento crea el grupo de recursos y la aplicación de API en su suscripción de Azure. Visual Studio muestra el progreso en la ventana **Actividad del Servicio de aplicaciones de Azure**.

	![Notificación de estado mediante la ventana de actividad del Servicio de aplicaciones de Azure](./media/app-service-api-pub-web-create/26-provisioning-success-v3.png)

<!---HONumber=Oct15_HO3-->