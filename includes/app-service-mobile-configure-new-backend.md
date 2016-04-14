
1. De nuevo en la configuración del back-end de la aplicación móvil, haga clic en **Inicio rápido** > la plataforma cliente. 

2. En **Crear una API de tabla**, seleccione su **Lenguaje back-end**, ya sea **C#** o **Node.js**:

	+ **Back-end de Node.js** (mediante el portal): acepte la confirmación y haga clic en **Crear tabla TodoItem**. Esto crea una nueva tabla *TodoItem* en la base de datos.
	 
		>[AZURE.IMPORTANT] Al cambiar un back-end de aplicación existente a Node.js, se sobrescribirá todo el contenido del sitio.

	+ **Back-end de .NET (C#)**: haga clic en **Descargar**, extraiga los archivos de proyecto comprimidos en el equipo local, abra la solución en Visual Studio, compile el proyecto para restaurar los paquetes de NuGet y después implemente el proyecto en Azure. Para más información sobre cómo implementar un proyecto de servidor back-end de .NET en Azure, consulte [Procedimientos: Publicación del proyecto de servidor](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#publish-server-project) en el tema SDK de back-end de .NET.
	 
Ahora el back-end de aplicación móvil está listo para usarse con la aplicación cliente.

<!---HONumber=AcomDC_0211_2016-->