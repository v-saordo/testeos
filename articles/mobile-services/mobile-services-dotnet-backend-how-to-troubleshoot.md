<properties
	pageTitle="Solución de problemas en el backend .NET de Servicios móviles | Microsoft Azure"
	description="Obtenga información acerca de cómo diagnosticar y corregir problemas con los servicios móviles mediante el back-end de .NET"
	services="mobile-services"
	documentationCenter=""
	authors="wesmc7777"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/07/2016" 
	ms.author="wesmc;ricksal"/>

# Solución de problemas en el backend .NET de Servicios móviles

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


El desarrollo con Servicios móviles suele ser sencillo y sin complicaciones, pero, aun así, pueden salir cosas mal a veces. En este tutorial se tratan algunas técnicas que le permitirán solucionar problemas comunes que pueden surgir con el extremo .NET de Servicios móviles.

1. [Depuración de HTTP](#HttpDebugging)
2. [Depuración en tiempo de ejecución](#RuntimeDebugging)
3. [Análisis de registros de diagnóstico](#Logs)
4. [Depuración de la resolución de ensamblados en la nube](#AssemblyResolution)
5. [Solución de problemas de migraciones con Entity Framework](#EFMigrations)

<a name="HttpDebugging"></a>
## Depuración de HTTP

Cuando se desarrollan aplicaciones con Servicios móviles, lo normal es aprovechar las ventajas del SDK de cliente de Servicios móviles para la plataforma que utilice (Tienda Windows, iOS, Android, etc.). Sin embargo, a veces resulta útil ir al nivel HTTP y observar las llamadas de fila conforme tienen lugar en la red. Esta estrategia es especialmente útil cuando se depuran problemas de conectividad y serialización. El backend .NET de Servicios Móviles permite usar esta estrategia junto con la depuración de Visual Studio en modo local y remoto (más información en la próxima sección) para obtener una idea completa de la ruta que sigue una llamada HTTP antes de invocar el código del servicio.

Puede usar cualquier depurador HTTP para enviar e inspeccionar el tráfico HTTP. [Fiddler](http://www.telerik.com/fiddler) es una conocida herramienta que suelen usar los desarrolladores para este propósito. Con el fin de ponérselo fácil a los desarrolladores, Servicios móviles incluye un depurador de HTTP basado en web (también denominado "cliente de prueba") con su servicio móvil, lo que reduce la necesidad de herramientas externas. Si hospeda su servicio móvil de forma local, estará disponible en un URI similar a [http://localhost:59233](http://localhost:59233). Si lo hospeda en la nube, el URI tendrá el formato [http://todo-list.azure-mobile.net](http://todo-list.azure-mobile.net). Los pasos siguientes son válidos independientemente de dónde esté hospedado el servicio:

1. Comience con un proyecto de servidor de Servicios móviles en **Visual Studio 2013 Update 2** o posterior. Si no tiene uno a mano, puede crearlo; para ello, seleccione **Archivo**, **Nuevo**, **Proyecto** y luego seleccione el nodo **Nube** y seguidamente la plantilla **Servicios móviles de Microsoft Azure**.
2. Pulse **F5** para compilar y ejecutar el proyecto. En la página de inicio, seleccione **Probar**.

    >[AZURE.NOTE]
    Si el servicio está hospedado en modo local, al hacer clic en el vínculo, se le dirigirá a la página siguiente. Sin embargo, si está hospedado en la nube, se le pedirá que proporcione un conjunto de credenciales. Esto es para asegurarse de que usuarios no autorizados no tienen acceso a información sobre su API y sus cargas. Para ver la página, debe iniciar sesión con un **nombre de usuario en blanco** y la **clave de la aplicación** como contraseña. La clave de la aplicación está disponible en el Portal de Azure clásico; vaya a la pestaña **Panel** del servicio móvil y seleccione **Administrar claves**.
    >
    > ![Mensaje de autenticación para obtener acceso a la página de ayuda][HelpPageAuth]

3. La página que ve (denominada "página de ayuda") muestra una lista de todas las API HTTP disponibles en su servicio móvil. Seleccione una de las API (si comenzó a usar la plantilla de proyecto de Servicios móviles en Visual Studio, debe ver **GET tables/TodoItem**).

    ![Página de ayuda][HelpPage]

4. La página resultante muestra la documentación y los ejemplos de JSON de la solicitud, y la respuesta que espera la API. Seleccione el botón **Probar esto**.

    ![Página de prueba de una API][HelpPageApi]

5. Se trata del "cliente de prueba", que le permite enviar una solicitud HTTP para probar su API. Seleccione **Enviar**.

    ![Cliente de prueba][TestClient]

6. Verá la respuesta HTTP del servicio móvil directamente en la ventana del explorador.

    ![Cliente de prueba con respuesta HTTP][TestClientResponse]

Ahora puede explorar cómodamente en su explorador web las diferentes API HTTP que expone su servicio móvil.

<a name="RuntimeDebugging"></a>
## Depuración en tiempo de ejecución

Una de las principales características del backend .NET es la capacidad para depurar el código del servicio de forma local, pero también para depurar el código en ejecución en el entorno de la nube. Siga estos pasos:

1. Abra el proyecto de servicio móvil que desee depurar en **Visual Studio 2013 Update 2** o posterior.
2. Configure la carga de símbolos. Vaya al menú **Depurar** y seleccione **Opciones y configuración**. Asegúrese de que la casilla **Habilitar Solo mi código** está desactivada y que **Habilitar compatibilidad de servidor de origen** está activada.

    ![Configure la carga de símbolos][SymbolLoading]

3. Seleccione el nodo **Símbolos** a la izquierda y agregue una referencia al servidor [SymbolSource] mediante el URI [http://srv.symbolsource.org/pdb/Public](http://srv.symbolsource.org/pdb/Public). Los símbolos para el backend .NET de Servicios móviles están disponibles aquí con cada nueva versión.

    ![Configure el servidor de símbolos][SymbolServer]

4. Establezca un punto de interrupción en el trozo de código que desea depurar. Por ejemplo, establezca un punto de interrupción en el método **GetAllTodoItems()** del controlador **TodoItemController** que viene con la plantilla de proyecto de Servicios móviles de Visual Studio.
5. Presione **F5** para depurar el servicio de forma local. La primera carga puede ser lenta mientras Visual Studio descarga los símbolos para el backend .NET de Servicios móviles.
6. Como se explica en la sección anterior sobre la depuración HTTP, utilice el cliente de prueba para enviar una solicitud HTTP al método donde estableció el punto de interrupción. Por ejemplo, para enviar una solicitud al método **GetAllTodoItems()**, seleccione **GET tables/TodoItem** en la página de ayuda, elija **probar esto** y después **enviar**.
7. Visual Studio debe pararse en el punto de interrupción que ha establecido y debe haber disponible un seguimiento completo de la pila con el código fuente en la ventana **Pila de llamadas** en Visual Studio.

    ![Llegada a un punto de interrupción][Breakpoint]

8. Ya puede publicar el servicio en Azure y estará disponible para depurarlo exactamente igual que como acabamos de ver. Publique el proyecto. Para ello, haga clic en él con el botón derecho en el **Explorador de soluciones** y seleccione **Publicar**.
9. En la pestaña **Configuración** del Asistente para publicación, seleccione la configuración **Depurar**. Esto hace que se publiquen los símbolos de depuración relevantes con el código.

    ![Opción Depurar en la publicación][PublishDebug]

10. Una vez que el servicio se ha publicado correctamente, abra el **Explorador de servidores** y expanda **Azure** y los nodos de **Servicios móviles**. Inicie sesión si es necesario.
11. Haga clic con el botón derecho en el servicio móvil que acaba de publicar y elija **Asociar depurador**.

    ![Attach debugger][AttachDebugger]

12. Igual que hizo en el paso 6, envíe una solicitud HTTP para invocar el método donde estableció el punto de interrupción. Esta vez, utilice la página de ayuda y el cliente de prueba para el servicio móvil hospedado en Azure.
13. Visual Studio se detendrá en el punto de interrupción.

Ya tiene acceso a todo el potencial del depurador de Visual Studio, tanto si desarrolla en modo local como con el servicio móvil ejecutándose en Azure.

<a name="Logs"></a>
## Análisis de registros de diagnóstico

Conforme el servicio móvil controla las solicitudes de sus clientes, genera una gran variedad de información de diagnóstico muy útil, además de capturar las excepciones que encuentre. Por otro lado, puede instrumentar también el código del controlador con más registros para aprovechar las ventajas de la propiedad [**Log**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.apiservices.log.aspx) disponible en la propiedad [**Services**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.tables.tablecontroller.services.aspx) de cada elemento [**TableController**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.tables.tablecontroller.aspx).

Cuando se hace depuración en modo local, los registros aparecen en la ventana **Resultados** de Visual Studio.

![Registros en la ventana Resultados de Visual Studio][LogsOutputWindow]

Tras publicar el servicio en Azure, los registros de la instancia de servicio que se ejecuta en la nube están disponibles si hace clic con el botón derecho en el servicio móvil en el **Explorador de servidores** de Visual Studio y luego selecciona **Ver registros**.

![Registros en el Explorador de servidores de Visual Studio][LogsServerExplorer]

Los mismos registros están disponibles también en el Portal de Azure clásico en la pestaña **Registros** del servicio móvil.

![Registros en el Portal de Azure clásico][LogsPortal]

<a name="AssemblyResolution"></a>
## Depuración de la resolución de ensamblados en la nube

Al publicar el servicio móvil en Azure, se carga en el entorno de hospedaje de Servicios móviles, que garantiza actualizaciones y revisiones sin problemas de la canalización HTTP que hospeda el código del controlador. Esto incluye todos los ensamblados a los que se hace referencia en los [paquetes de NuGet para back-end de .NET](http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22): el equipo actualiza constantemente el servicio para usar las versiones más recientes de esos ensamblados.

A veces se pueden introducir conflictos de versiones al hacer referencia a *diferentes versiones principales* de los ensamblados necesarios (se permiten versiones *secundarias* diferentes). Esto ocurre a menudo cuando NuGet le pide que actualice a la última versión de uno de los paquetes que utiliza el back-end .NET de Servicios móviles.

>[AZURE.NOTE] Los Servicios móviles actualmente solo son compatibles con ASP.NET 5.1; ASP.NET 5.2 no es compatible actualmente. La actualización de los paquetes de NuGet de ASP.NET a 5.2.* puede producir un error después de la implementación.

Si actualiza estos paquetes, cuando publique el servicio actualizado en Azure, verá una página de advertencia donde se indica el conflicto:

![Página de ayuda donde se indica un conflicto de carga de ensamblados][HelpConflict]

Esto irá acompañado del registro de un mensaje de excepción similar al siguiente en los registros del servicio:

    Found conflicts between different versions of the same dependent assembly 'Microsoft.ServiceBus': 2.2.0.0, 2.3.0.0. Please change your project to use version '2.2.0.0' which is the one currently supported by the hosting environment.

Este problema es fácil de corregir: simplemente revierta a una versión compatible del ensamblado necesario y vuelva a publicar su servicio.

<a name="EFMigrations"></a>
## Solución de problemas de migraciones con Entity Framework

Cuando se usa el backend .NET de Servicios móviles con Base de datos SQL, se utiliza Entity Framework (EF) como tecnología de acceso a datos que permite consultar la base de datos y almacenar objetos en ella. Un aspecto importante que EF controla en nombre del desarrollador es el modo en que cambian las columnas de la base de datos (también conocidas como *esquema*) conforme cambian las clases de modelo especificadas en el código. Este proceso se conoce como [Migraciones de Code First](http://msdn.microsoft.com/data/jj591621).

Las migraciones pueden ser complejas y pueden requerir que se mantenga el estado de la base de datos sincronizado con el modelo EF para que funcionen correctamente. Para obtener instrucciones sobre el modo de controlar las migraciones con un servicio móvil y los errores que pueden surgir, consulte [Modificación del modelo de datos a un servicio móvil de back-end de .NET](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

<!-- IMAGES -->

[HelpPageAuth]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/6.png
[HelpPage]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/7.png
[HelpPageApi]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/8.png
[TestClient]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/9.png
[TestClientResponse]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/10.png
[SymbolLoading]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/1.png
[SymbolServer]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/2.png
[Breakpoint]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/3.png
[PublishDebug]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/4.png
[AttachDebugger]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/5.png
[LogsOutputWindow]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/11.png
[LogsServerExplorer]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/12.png
[LogsPortal]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/13.png
[HelpConflict]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/14.png


<!-- Links -->
[SymbolSource]: http://symbolsource.org

<!---HONumber=AcomDC_0211_2016-->