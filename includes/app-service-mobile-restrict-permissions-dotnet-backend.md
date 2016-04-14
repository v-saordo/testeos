
De forma predeterminada, se pueden invocar API en un back-end de aplicación móvil de forma anónima. A continuación, deberá restringir el acceso a solo los clientes autenticados.

+ **Back-end Node.js (a través del portal)**:  
	
	En la **Configuración** de la aplicación móvil, haga clic en **Tablas fáciles** y seleccione la tabla. Haga clic en **Cambiar permisos**, seleccione **Solo acceso autenticado** para todos los permisos y luego haga clic en **Guardar**.

+ **Back-end de .NET (C#)**:

	En el proyecto de servidor, vaya a **Controladores** > **TodoItemController.cs**. Agregue el atributo `[Authorize]` a la clase **TodoItemController**, como sigue. Para restringir el acceso solo a determinados métodos, también puede aplicar este atributo solo a esos métodos en lugar de la clase. Vuelva a publicar el proyecto de servidor.


        [Authorize]
        public class TodoItemController : TableController<TodoItem>

+ **Back-end de Node.js (a través del código de Node.js)**:
	
	Para pedir autenticación para acceder a las tablas, agregue la siguiente línea al script del servidor de Node.js:


        table.access = 'authenticated';

	Para obtener más información, consulte [Autenticación necesaria para el acceso a las tablas](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Para obtener información sobre cómo descargar el proyecto de código de inicio rápido desde su sitio, consulte [How to: Download the Node.js backend quickstart code project using Git](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).

<!---HONumber=AcomDC_1210_2015-->