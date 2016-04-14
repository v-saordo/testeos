

De manera predeterminada, todas las solicitudes a recursos de servicios móviles están restringidas a clientes que presentan la clave de aplicación, lo que no protege estrictamente el acceso a los recursos. Para proteger los recursos, debe restringir el acceso solo a los clientes autenticados.

1. En Visual Studio, abra el proyecto de servicio móvil, expanda la carpeta Controllers y abra **TodoItemController.cs**. La clase **TodoItemController** implementa el acceso a los datos para la tabla TodoItem. Agregue la siguiente instrucción `using`:

		using Microsoft.WindowsAzure.Mobile.Service.Security;

2. Aplique el siguiente atributo _AuthorizeLevel_ a la clase **TodoItemController**.

		[AuthorizeLevel(AuthorizationLevel.User)]

	De esta forma, se garantiza que todas las operaciones en la tabla _TodoItem_ requieren un usuario autenticado. También puede aplicar el atributo *AuthorizeLevel* en el nivel de método.

3. (Opcional) Si desea depurar la autenticación localmente, expanda la carpeta `App_Start`, abra **WebApiConfig.cs** y agregue el código siguiente al método **Registrar**.

		config.SetIsHosted(true);

	Esto indica al proyecto del servicio móvil local que se ejecute como si estuviera hospedado en Azure, incluyendo la autorización a la configuración de *AuthorizeLevel*. Sin esta configuración, todas las solicitudes HTTP a localhost se permiten sin autenticación, a pesar de la configuración de *AuthorizeLevel*. Al habilitar el modo autoalojado, también deberá establecer un valor para la clave de aplicación local.

4. (Opcional) En el archivo de proyecto web.config, establezca un valor de cadena para la configuración de la aplicación `MS_ApplicationKey`.

	Esta es la contraseña que se utiliza (sin nombre de usuario) para probar las páginas de ayuda de API cuando se ejecuta el servicio localmente. Este valor de cadena no se utiliza en el sitio activo en Azure y no es necesario utilizar la clave de aplicación real; funcionará cualquier valor de cadena válido.
 
4. Vuelva a publicar el proyecto.

<!---HONumber=Oct15_HO3-->