
1. En el Explorador de servidores de Visual Studio, expanda el nodo **Azure**, **Servicios móviles** y su servicio móvil.

2. Haga clic con el botón derecho del mouse en la tabla **TodoItem**, haga clic en **Editar permisos**, establezca todos los permisos en **Solo usuarios autenticados** y, a continuación, haga clic en **Aplicar**. Esto garantiza que todas las operaciones frente a la tabla _TodoItem_ requieren un usuario autenticado.

3. Haga clic con el botón derecho del mouse en el proyecto de la aplicación de cliente, haga clic en **Depurar** y, a continuación, en **Iniciar nueva instancia**; compruebe que se genera una excepción no controlada con el código de estado 401 (No autorizado) después de que se inicie la aplicación.

	Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla *TodoItem* requiere ahora autenticación.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

<!---HONumber=Oct15_HO3-->