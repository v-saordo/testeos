<properties
   pageTitle="Creación de la primera aplicación de Service Fabric en Visual Studio | Microsoft Azure"
   description="Creación, implementación y depuración de una aplicación de Service Fabric con Visual Studio"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/11/2016"
   ms.author="seanmck"/>

# Creación de la primera aplicación de Azure Service Fabric en Visual Studio

El SDK de Service Fabric incluye un complemento para Visual Studio que proporciona herramientas y plantillas para crear, implementar y depurar aplicaciones de Service Fabric. Este tema le guiará por el proceso de creación de su primera aplicación en Visual Studio.

## Requisitos previos

Antes de comenzar, asegúrese de haber [configurado el entorno de desarrollo](service-fabric-get-started.md).

## Tutorial en vídeo

El siguiente vídeo le guía a través de los pasos de este tutorial:

>[AZURE.VIDEO creating-your-first-service-fabric-application-in-visual-studio]

## Creación de la aplicación

Una aplicación de Service Fabric puede contener uno o varios servicios, cada uno de ellos con un rol específico en la prestación de la funcionalidad de la aplicación. Con el Asistente para nuevo proyecto se puede crear un proyecto de aplicación, junto con el primer proyecto de servicio. Puede agregar más servicios posteriormente.

1. Inicie Visual Studio como administrador.

2. Haga clic en **Archivo > Nuevo proyecto > Nube > Aplicación de Service Fabric**.

3. Asigne un nombre a la aplicación y haga clic en **Aceptar**.

	![Cuadro de diálogo de proyecto nuevo en Visual Studio.][1]

4. En la página siguiente, se le pedirá que elija el primero tipo de servicio que se va a incluir en la aplicación. En este tutorial, elegiremos **Con estado**. Asígnele un nombre y haga clic en **Aceptar**.

	![Cuadro de diálogo de servicio nuevo en Visual Studio.][2]

	>[AZURE.NOTE] Para más información sobre las opciones, consulte [Elección de un marco para su servicio](service-fabric-choose-framework.md).

	Visual Studio crea el proyecto de aplicación y el proyecto de servicio con estado y los muestra en el Explorador de soluciones.

	![Explorador de soluciones después de la creación de una aplicación con el servicio con estado][3]

	El proyecto de aplicación no contiene código directamente. En su lugar, hace referencia a un conjunto de proyectos de servicio. Además, contiene otros tres tipos de contenido:

	- **Perfiles de publicación**: se usa para administrar las herramientas preferidas para los distintos entornos.

	- **Scripts**: incluye un script de PowerShell para implementar o actualizar aplicaciones. Visual Studio usa este script en segundo plano y se puede invocar directamente desde la línea de comandos.

	- **Definición de aplicación**: incluye el manifiesto de la aplicación y los archivos de parámetros de la aplicación asociados que definen la aplicación y permiten configurarla específicamente para un entorno determinado.

    Para obtener información general del contenido del proyecto de servicio, consulte [Introducción a Reliable Services de Microsoft Azure Service Fabric](service-fabric-reliable-services-quick-start.md).

## Implemente la aplicación y depúrela

Ahora que tiene una aplicación, puede probar a ejecutarla.

1. Presione F5 en Visual Studio para implementar la aplicación, con el fin de depurarla.

	>[AZURE.NOTE] La primera vez que se realiza esta operación tarda mucho tiempo en completarse, ya que Visual Studio tiene que crear un clúster local para el desarrollo. En los clústeres locales se ejecuta el mismo código de plataforma que se compila en los clústeres de varias máquinas, pero en una sola máquina. El estado de la creación del clúster se ve en la ventana de salida de Visual Studio.

	Cuando el clúster esté listo, recibirá una notificación de la aplicación del administrador de la bandeja de sistema del clúster local que se incluye con el SDK.

	![Notificación de bandeja de sistema del clúster local][4]

2. Una vez que se inicie la aplicación, Visual Studio mostrará automáticamente el visor de eventos de diagnóstico, donde se pueden ver los resultados del seguimiento del servicio.

	![Visor de eventos de diagnóstico][5]

	En el caso de la plantilla del servicio con estado, los mensajes muestran simplemente el valor del contador que se incrementa en el método `RunAsync` de MyStatefulService.cs.

3. Expanda uno de los eventos para ver más detalles, incluido el nodo en que se ejecuta el código. En este caso, es el nodo 2, aunque puede que en su equipo sea otro.

	![Detalle del Visor de eventos de diagnóstico][6]

	El clúster local contiene cinco nodos que se hospedan en una sola máquina. Imita a un clúster de cinco nodos, en el que los nodos están en máquinas distintas. Desactivemos uno de los nodos del clúster local para simular la pérdida de una máquina y ejercitar el depurador de Visual Studio al mismo tiempo.

    >[AZURE.NOTE] Los eventos de diagnóstico de la aplicación que emite la plantilla del proyecto usan la clase `ServiceEventSource` incluida. Para más información, consulte [Supervisión y diagnóstico de servicios en una configuración de desarrollo de máquina local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

4. Busque en el proyecto de servicio la clase que deriva de StatefulService (por ejemplo, MyStatefulService) y establezca un punto de interrupción en la primera línea del método `RunAsync`.

	![Punto de interrupción en el método RunAsync de servicio con estado][7]

5. Haga clic con el botón derecho en la aplicación de la bandeja del sistema del Administrador de clústeres local y elija **Administrar clúster local** para iniciar el Explorador de Service Fabric.

    ![Inicie el Explorador de Service Fabric desde el Administrador de clústeres locales][systray-launch-sfx]

    El Explorador de Service Fabric ofrece una representación visual de un clúster, incluidos el conjunto de las aplicaciones que se implementan en él y el conjunto de nodos físicos que lo componen. Para aprender más sobre Service Fabric, consulte [Visualización del clúster](service-fabric-visualizing-your-cluster.md).

6. En el panel izquierdo, expanda **Clúster > Nodos** y busque el nodo en que se ejecuta el código.

7. Haga clic en **Acciones > Desactivar (reiniciar)** para simular un reinicio de la máquina.

	![Detener un nodo en el Explorador de Service Fabric][sfx-stop-node]

	Momentáneamente, debería ver que se alcanza el punto de interrupción en Visual Studio cuando el cálculo hacía perfectamente en un nodo se conmuta por error a otro.

8. Vuelva al Visor de eventos de diagnóstico y observe los mensajes. Tenga en cuenta que el contador no ha dejado de incrementarse, aunque los eventos proceden de otro nodo.

    ![Visor de eventos de diagnóstico después de la conmutación por error][diagnostic-events-viewer-detail-post-failover]

### Limpiar

  Antes de concluir, es importante recordar que el clúster local es real. Incluso después de detener el depurador y de cerrar Visual Studio, las aplicaciones se seguirán ejecutando en segundo plano. Según la naturaleza de las aplicaciones, esta actividad en segundo plano puede consumir importantes recursos en su máquina. Tiene varias opciones para administrar esto:

  1. Para quitar una aplicación individual y todos sus datos, utilice la acción **Quitar aplicación** en el Explorador de Service Fabric.

  2. Para cerrar el clúster pero mantener los datos y los seguimientos de la aplicación, haga clic en **Detener clúster** en la aplicación de bandeja del sistema.

  3. Para eliminar totalmente el clúster, haga clic en **Quitar clúster** en la aplicación de bandeja del sistema. Tenga en cuenta que esta opción generará otra implementación lenta la próxima vez que presione F5 en Visual Studio. Úsela solo si no tiene intención utilizar el clúster local durante algún tiempo o si necesita reclamar recursos.



## Pasos siguientes

- [Vea cómo puede exponer los servicios a Internet con un front-end de servicio web](service-fabric-add-a-web-frontend.md)
- [Más información sobre cómo crear clústeres en Azure](service-fabric-cluster-creation-via-portal.md)
- [Más información sobre Reliable Services](service-fabric-reliable-services-quick-start.md)
- [Intente crear un servicio con el modelo de programación Reliable Actors](service-fabric-reliable-actors-get-started.md)

<!-- Image References -->

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog.png
[2]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
[3]: ./media/service-fabric-create-your-first-application-in-visual-studio/solution-explorer-stateful-service-template.png
[4]: ./media/service-fabric-create-your-first-application-in-visual-studio/local-cluster-manager-notification.png
[5]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer.png
[6]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail.png
[7]: ./media/service-fabric-create-your-first-application-in-visual-studio/runasync-breakpoint.png
[sfx-stop-node]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-deactivate-node.png
[systray-launch-sfx]: ./media/service-fabric-create-your-first-application-in-visual-studio/launch-sfx.png
[diagnostic-events-viewer-detail-post-failover]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail-post-failover.png

<!---HONumber=AcomDC_0218_2016-->