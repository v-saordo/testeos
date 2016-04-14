1. Inicie sesión en el [Portal de Azure].

2. En la esquina superior izquierda de la ventana, haga clic en el botón **+NUEVO** > **Web y móvil** > **Aplicación móvil** e indique un nombre para el back-end de la aplicación móvil.

3. En el cuadro **Grupo de recursos**, seleccione un grupo de recursos existente. Si no tiene grupos de recursos, escriba el mismo nombre que la aplicación.
 
	En este punto, se selecciona el plan del Servicio de aplicaciones predeterminado, que se encuentra en el [nivel Estándar](https://azure.microsoft.com/pricing/details/app-service/). La configuración del plan del Servicio de aplicaciones determina la ubicación, características, costo y recursos de procesos asociados a la aplicación. Puede seleccionar un plan de Servicio de aplicaciones ya existente o crear uno nuevo. Para más información acerca de los planes del Servicio de aplicaciones y cómo crear un nuevo plan en un plan de tarifa diferente y en la ubicación deseada, consulte [Introducción detallada sobre los planes del Servicio de aplicaciones de Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

4. Use el plan del Servicio de aplicaciones predeterminado, seleccione un plan diferente o [cree un nuevo plan](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan) y, a continuación, haga clic en **Crear**.
	
	Esto crea el back-end de aplicación móvil Más adelante, implementará el proyecto de servidor en este back-end. El aprovisionamiento de un back-end de aplicación móvil puede tardar varios minutos; la hoja **Configuración** para el back-end de aplicación móvil se mostrará una vez finalizado. Para poder usar el back-end de aplicación móvil, debe definir igualmente una conexión a un almacén de datos.

    > [AZURE.NOTE] Como parte de este tutorial, va a crear una instancia y un nuevo servidor de Base de datos SQL. Puede reutilizar esta nueva base de datos y administrarla como lo haría con cualquier otra instancia de Base de datos SQL. Si ya hay una base de datos en la misma ubicación que el nuevo back-end de aplicación móvil, puede elegir **Usar una base de datos existente** y después seleccionar dicha base de datos. No se recomienda el uso de una base de datos en una ubicación diferente debido a los costos adicionales de ancho de banda y las elevadas latencias. Existen otras opciones de almacenamiento de datos.

6. En la hoja **Configuración** del nuevo back.end de Aplicaciones móviles, haga clic en **Inicio rápido** > la plataforma de aplicaciones cliente > **Conectar a una base de datos**.

	![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)

7. En la hoja **Agregar conexión de datos**, haga clic en **Base de datos SQL** > **Crear una nueva base de datos**, escriba el **Nombre** de la base de datos, seleccione un plan de tarifa y haga clic en **Servidor**.
 
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)

8. En la hoja **Nuevo servidor**, escriba un nombre de servidor único en el campo **Nombre del servidor**, proporcione un **Inicio de sesión del administrador del servidor** y una **Contraseña** seguros, compruebe que **Permitir que los servicios de Azure accedan al servidor** esté seleccionado y haga clic en **Aceptar** dos veces. Esto crea la nueva base de datos y el servidor.

10. De nuevo en la hoja **Agregar conexión de datos**, haga clic en **Cadena de conexión**, escriba los valores de inicio de sesión y una contraseña para la base de datos, y haga clic en **Aceptar** dos veces.

	La creación de la base de datos puede tardar unos minutos. Use el área de **notificaciones** para supervisar el progreso de la implementación. No puede continuar hasta que la base de datos esté implementada correctamente.


<!-- URLs. -->
[Portal de Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0224_2016-->