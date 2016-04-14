3. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto (no en la solución) y seleccione **Agregar > Cliente de aplicación de API de Azure**. 

	![](./media/app-service-api-dotnet-add-generated-client/03-add-azure-api-client-v3.png)
	
3. En el cuadro de diálogo **Agregar cliente de aplicación de API de Azure**, haga clic en **Descargar de la aplicación de API de Azure**.

5. En la lista desplegable, seleccione la aplicación de API que desea llamar.

7. Haga clic en **Aceptar**.

	![Pantalla de generación](./media/app-service-api-dotnet-add-generated-client/04-select-the-api-v3.png)

	El asistente descarga el archivo de metadatos de la API y genera una interfaz con tipo para llamar a la aplicación de API.

	![Generación en curso](./media/app-service-api-dotnet-add-generated-client/05-metadata-downloading-v3.png)

	Una vez completada la generación de código, verá una nueva carpeta en el **Explorador de soluciones**, con el nombre de la aplicación de API. Esta carpeta contiene el código que implementa las clases y los modelos de datos de cliente.

	![Generación completa](./media/app-service-api-dotnet-add-generated-client/06-code-gen-output-v3.png)

<!---HONumber=Oct15_HO3-->