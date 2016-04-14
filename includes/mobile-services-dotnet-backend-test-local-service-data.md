En esta sección, utilizará Visual Studio para hospedar localmente el servicio móvil en la estación de trabajo de desarrollo en IIS Express. A continuación, probará la aplicación y el servicio de back-end.


1. En Visual Studio, presione la tecla F7 o haga clic en **Compilar solución** en el menú **Compilar** para compilar tanto la aplicación de la Tienda Windows como el servicio móvil. Compruebe que ambos proyectos se compilen sin errores en la ventana de resultados de Visual Studio.

2. En Visual Studio, presione la tecla F5 o haga clic en **Iniciar depuración** en el menú **Depurar** para ejecutar la aplicación y hospedar el servicio móvil de manera local en IIS Express.

 
3. Escriba el texto de un nuevo TodoItem. A continuación, haga clic en **Guardar**. De este modo, se inserta un nuevo TodoItem en la base de datos creada por el servicio móvil hospedado de manera local en IIS Express.

    ![](./media/mobile-services-dotnet-backend-test-local-service-data/new-local-todoitem.png)

4. Haga clic en la casilla correspondiente a uno de los elementos para marcarlo como completado.

    ![](./media/mobile-services-dotnet-backend-test-local-service-data/local-item-checked.png)

5. En Visual Studio, puede visualizar los cambios en la base de datos creada por el servicio de back-end si abre el Explorador de servidores y expande las conexiones de datos. Haga clic con el botón derecho en la tabla de TodoItems situada debajo de **MS\_TableConnectionString** y, a continuación, haga clic en **Mostrar datos de tabla**

    ![](./media/mobile-services-dotnet-backend-test-local-service-data/vs-show-local-table-data.png)

<!---HONumber=Oct15_HO3-->