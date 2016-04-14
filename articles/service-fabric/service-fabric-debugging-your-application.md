<properties
   pageTitle="Depuración de la aplicación en Visual Studio | Microsoft Azure"
   description="Mejore la confiabilidad y el rendimiento de los servicios mediante su desarrollo y depuración en Visual Studio en un clúster de desarrollo local."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/28/2016"
   ms.author="jesseb"/>

# Depurar la aplicación de Service Fabric con Visual Studio

Puede ahorrar tiempo y dinero implementando y depurando su aplicación de Service Fabric de Azure en un clúster de desarrollo del equipo local. Visual Studio puede implementar la aplicación en el clúster local y conectar automáticamente el depurador a todas las instancias de la aplicación.

1. Inicie un clúster de desarrollo local siguiendo los pasos descritos en [Configurar el entorno de desarrollo de Service Fabric](service-fabric-get-started.md).

2. Presione **F5** o haga clic en **Depurar** > **Iniciar depuración**.

    ![Empezar a depurar una aplicación][startdebugging]

3. Establezca puntos de interrupción en el código y recorra la aplicación haciendo clic en los comandos del menú **Depurar**.

    > [AZURE.NOTE] Visual Studio se conecta a todas las instancias de su aplicación. Al recorrer el código, se pueden alcanzar los puntos de interrupción por varios procesos, lo que da lugar a sesiones simultáneas. Intente deshabilitar los puntos de interrupción después de que se alcancen, haciendo que el punto de interrupción sea condicional en el id. del subproceso o use los eventos de diagnóstico.

4. La ventana de **eventos de diagnóstico** se abrirá automáticamente para ver los eventos de diagnóstico en tiempo real.

    ![Ver eventos de diagnóstico en tiempo real][diagnosticevents]

5. También puede abrir la ventana de **eventos de diagnóstico** en el Explorador de servidores. En **Azure**, haga clic con el botón secundario en **Cluster de Service Fabric** > **Ver eventos de diagnóstico**

    ![Abrir la ventana de eventos de diagnóstico][viewdiagnosticevents]

6. Los eventos de diagnóstico se pueden ver en el archivo **ServiceEventSource.cs** generado automáticamente y se llaman desde el código de la aplicación.

    ```csharp
    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, Service.ServiceTypeName);
    ```

7. La ventana de **eventos de diagnóstico** admite el filtrado, la pausa y la inspección de eventos en tiempo real. El filtro es una búsqueda de cadena simple del mensaje de evento, incluido su contenido.

    ![Filtrar, pausar y reanudar, o inspeccionar eventos en tiempo real][diagnosticeventsactions]

8. Depurar servicios es similar a la depuración de cualquier otra aplicación. Los puntos de interrupción se pueden establecer normalmente a través de Visual Studio para una depuración sencilla. Aunque las Colecciones fiables se replican en varios nodos, siguen implementando IEnumerable. Esto significa que puede usar la vista de resultados en Visual Studio durante la depuración para ver lo que ha almacenado dentro. Solo tiene que establecer un punto de interrupción en el código.

    ![Empezar a depurar una aplicación][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

- [Pruebe un servicio de Service Fabric](service-fabric-testability-overview.md).
- [Administración de aplicaciones de Service Fabric en Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png

<!---HONumber=AcomDC_0204_2016-->