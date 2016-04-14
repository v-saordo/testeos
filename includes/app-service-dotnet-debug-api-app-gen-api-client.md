## Generación de un cliente de aplicación de API 

Las herramientas de aplicaciones de API de Visual Studio facilitan la generación de código C# que llama a las Aplicaciones de API de Azure desde aplicaciones de escritorio, de la tienda y móviles.

1. En Visual Studio, abra la solución que contiene la aplicación de API del tema [Creación de una aplicación de API](../article/app-service-api/app-service-dotnet-create-api-app.md). 

2. En el **Explorador de soluciones**, haga clic con el botón secundario en la solución y seleccione **Agregar** > **Nuevo proyecto**.

	![Incorporación de proyecto nuevo](./media/app-service-dotnet-debug-api-app-gen-api-client/01-add-new-project-v3.png)

3. En el cuadro de diálogo **Agregar nuevo proyecto**, realice los pasos siguientes:

	1. Seleccione la categoría **Escritorio de Windows**.
	
	2. Seleccione la plantilla de proyecto **Aplicación de consola**.
	
	3. Asigne el nombre al proyecto.
	
	4. Haga clic en **Aceptar** para generar el nuevo proyecto en la solución existente.
	
	![Incorporación de proyecto nuevo](./media/app-service-dotnet-debug-api-app-gen-api-client/02-contact-list-console-project-v3.png)

4. Haga clic en el proyecto de aplicación de consola recientemente creado y seleccione **Agregar** > **Cliente de aplicación de API de Azure**.

	![Incorporación de un nuevo cliente](./media/app-service-dotnet-debug-api-app-gen-api-client/03-add-azure-api-client-v3.png)
	
5. En el cuadro de diálogo **Agregar cliente de aplicación de API de Microsoft Azure**, lleve a cabo los pasos siguientes:

	1. En el cuadro de diálogo, seleccione la opción **Descargar**. 
	
	2. En la lista desplegable, seleccione la aplicación de API que ha creado antes.
	
	3. Haga clic en **Aceptar**.

	![Pantalla de generación](./media/app-service-dotnet-debug-api-app-gen-api-client/04-select-the-api-v3.png)

	El asistente descargará el archivo de metadatos de API y generará una interfaz para llamar a la aplicación de API.

	![Generación en curso](./media/app-service-dotnet-debug-api-app-gen-api-client/05-metadata-downloading-v3.png)

	Una vez completada la generación de código, verá una nueva carpeta en el Explorador de soluciones, con el nombre de la aplicación de API. Esta carpeta contiene el código que implementa el cliente y los modelos de datos.

	![Generación completa](./media/app-service-dotnet-debug-api-app-gen-api-client/06-code-gen-output-v3.png)

6. Abra el archivo **Program.cs** desde la raíz del proyecto y reemplace el método **Main** por el código siguiente:

		static void Main(string[] args)
	    {
	        var client = new ContactsList();
	
	        // Send GET request.
	        var contacts = client.Contacts.Get();
	        foreach (var c in contacts)
	        {
	            Console.WriteLine("{0}: {1} {2}",
	                c.Id, c.Name, c.EmailAddress);
	        }
	
	        // Send POST request.
			client.Contacts.Post(new Models.Contact
		    {
		        EmailAddress = "lkahn@contoso.com",
		        Name = "Loretta Kahn",
		        Id = 4
		    });
	
	        Console.WriteLine("Finished");
	        Console.ReadLine();
	    }

## Prueba del cliente de aplicación de API

Después de que se ha codificado la aplicación de API, es hora de probar el código.

1. Abra el **Explorador de soluciones**.

2. Haga clic con el botón secundario en la aplicación de consola que creó en la sección anterior.

3. En el menú contextual de la aplicación de consola, seleccione **Depurar > Iniciar nueva instancia**.

4. Se abre una ventana de consola y muestra todos los contactos.

	![Ejecución de la aplicación de consola](./media/app-service-dotnet-debug-api-app-gen-api-client/running-console-app.png)

5. Presione **Entrar** para cerrar la ventana de consola.

<!---HONumber=Oct15_HO3-->