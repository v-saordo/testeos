<properties
    pageTitle="Depuración de aplicaciones de Azure en Eclipse"
    description="Obtenga información sobre la depuración de aplicaciones de Azure con el kit de herramientas de Azure para Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="02/26/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690949.aspx -->

# Depuración de aplicaciones de Azure en Eclipse #

Si usa Windows como sistema operativo, puede depurar las aplicaciones con el kit de herramientas de Azure para Eclipse, tanto si se ejecutan en Azure como en el emulador de proceso. La imagen siguiente muestra el cuadro de diálogo de propiedades **Debugging** (Depuración) que se usa para habilitar la depuración remota:

![][ic719504]

En este tutorial se supone que ya tiene una aplicación correctamente creada y está familiarizado con el emulador de proceso y la implementación en Azure.

Como punto de partida de este tema, vamos a usar la aplicación del tutorial [Uso de la biblioteca de tiempo de ejecución del servicio de Azure en JSP][]. Si aún no lo ha hecho, cree esa aplicación antes de continuar.

## Para depurar la aplicación mientras se ejecuta en Azure ##

>[AZURE.WARNING] La compatibilidad actual de este kit de herramientas con la depuración remota de Java está pensada principalmente para implementaciones que se ejecuten en el emulador de proceso de Azure. Dado que la conexión de depuración no es segura, no debe habilitar la depuración remota en implementaciones de producción. Si necesita depurar una aplicación que se ejecuta en la nube de Azure, use una implementación de ensayo, pero tenga en cuenta que el acceso no autorizado a la sesión de depuración es posible si alguien conoce la dirección IP de la implementación en la nube, aunque se trate de una implementación de ensayo.

1. Compile el proyecto para realizar pruebas en el emulador: en el Project Explorer (Explorador de proyectos) de Eclipse, haga clic en **MyAzureProject**, **Properties** (Propiedades), **Azure**, y establezca **Build for** (Compilar para) en **Deployment to cloud** (Implementación en la nube).
1. Recompile el proyecto: en el menú de Eclipse, haga clic en **Project** (Proyecto) y luego en **Build All** (Compilar todo).
1. Implementación de la aplicación en *ensayo* de Azure
    >[AZURE.IMPORTANT] Como se mencionó anteriormente, se recomienda especialmente depurar en el emulador de proceso en la mayoría de los casos y luego depurar en el entorno de ensayo solo si se requiere depuración adicional. Se recomienda no depurar en el entorno de producción.
1. Cuando la implementación esté lista en Azure, obtenga el nombre DNS para la implementación del [Portal de administración de Azure][]. Una implementación de ensayo tiene un nombre DNS en forma de http://*&lt;guid&gt;*.cloudapp.net, donde *&lt;guid&gt;* es un valor GUID que Azure asigna.
1. En el explorador de proyectos de Eclipse, haga clic con el botón derecho en **WorkerRole1**, **Azure**, y luego en **Debugging** (Depuración).
1. En el cuadro de diálogo **Properties for WorkerRole1 Debugging** (Propiedades para la depuración de WorkerRole1):
    1. Active **Enable remote debugging for this role** (Habilitar la depuración remota para este rol).
    1. Como **Input endpoint to use** (Punto de conexión de entrada que se va a usar), use **Debugging (public:8090, private:8090)** (Depuración (público:8090, privado:8090)).
    1. Asegúrese de que **Start JVM in suspended mode, waiting for a debugger connection** (Iniciar JVM en modo suspendido, esperando una conexión del depurador) está desactivada.

        >[AZURE.IMPORTANT] La opción **Start JVM in suspended mode, waiting for a debugger connection** está diseñada solo para escenarios de depuración avanzada en el emulador de proceso (no para implementaciones en la nube). Si se usa la opción **Start JVM in suspended mode, waiting for a debugger connection**, suspenderá el proceso de inicio del servidor hasta que el depurador de Eclipse se conecte a su JVM. Aunque puede emplear esta opción para una sesión de depuración con el emulador de proceso, no la use para una sesión de depuración en una implementación en la nube. La inicialización de un servidor se produce en una tarea de inicio de Azure y la nube de Azure no tiene puntos de conexión públicos disponibles hasta que se complete la tarea de inicio. Por lo tanto, un proceso de inicio no se completará correctamente si esta opción está habilitada en una implementación de la nube, ya que no será capaz de recibir una conexión desde un cliente externo de Eclipse.
    1. Haga clic en **Create Debug Configurations** (Crear configuraciones de depuración).
1. En el cuadro de diálogo **Azure Debug Configuration** (Configuración de depuración de Azure):
    1. Como **Java project to debug** (Proyecto Java para depurar), seleccione el proyecto **MyHelloWorld**.
    1. En **Configure debugging for** (Configurar depuración para), active **Azure cloud (staging)** (Nube de Azure (ensayo)).
    1. Asegúrese de que **Azure compute emulator** (Emulador de proceso de Azure) está desactivada.
    1. Para **Host**, escriba el nombre DNS de su implementación de ensayo, pero sin la parte ****http://** del principio. Por ejemplo (use su GUID en lugar del GUID que se muestra aquí): **4e616d65-6f6e-6d65-6973-526f62657274.cloudapp.net**
1. Haga clic en **OK** (Aceptar) para cerrar el cuadro de diálogo **Azure Debug Configuration**.
1. Haga clic en **OK** para cerrar el cuadro de diálogo **Properties for WorkerRole1 Debugging**.
1. Si no tiene un punto de interrupción ya establecido en index.jsp, establézcalo:
    1. En el explorador de proyectos de Eclipse, expanda **MyHelloWorld**, expanda **WebContent**, y haga doble clic en **index.jsp**.
    1. Dentro de index.jsp, haga clic con el botón derecho en la barra azul a la izquierda del código Java y haga clic en **Toggle Breakpoints** (Alternar puntos de interrupción), como se muestra a continuación: ![][ic551537]
1. En el menú de Eclipse, haga clic en **Run** (Ejecutar) y luego en **Debug Configurations** (Depurar configuraciones).
1. En el cuadro de diálogo **Debug Configurations** (Depurar configuraciones), expanda **Remote Java Application** (Aplicación de Java remota) en el panel de la izquierda, seleccione **Azure Cloud (WorkerRole1)** (Nube de Azure (WorkerRole1)) y haga clic en **Debug** (Depurar).
1. En el explorador, ejecute la aplicación de ensayo ****http://***&lt;guid&gt;***.cloudapp.net/MyHelloWorld**, sustituyendo el GUID de su nombre DNS por *&lt;guid&gt;*. Si un cuadro de diálogo **Confirm Perspective Switch** (Confirmar cambio de perspectiva) se lo pide, haga clic en **Yes** (Sí). La sesión de depuración debe ejecutarse ahora en la línea de código donde se estableció el punto de interrupción.

>[AZURE.NOTE] Si intenta iniciar una conexión de depuración remota con una implementación que tiene varias instancias de rol en ejecución, actualmente no puede controlar con qué instancia se conectará inicialmente el depurador, ya que el equilibrador de carga de Azure seleccionará una instancia aleatoriamente. Aunque una vez que conectado a esa instancia, seguirá depurando la misma instancia. Tenga en cuenta también que si hay un período de inactividad de más de cuatro minutos (por ejemplo, cuando se detiene en un punto de interrupción durante demasiado tiempo), puede que Azure cierre la conexión.

## Depuración de una instancia de rol específica en una implementación de varias instancias ##

Cuando la implementación se ejecuta en la nube, es muy probable que se ejecute en varias instancias de proceso o de rol. Esto le permite aprovechar la garantía de 99,95% de disponibilidad de Azure y escalar horizontalmente la aplicación.

En estos casos, es posible que tenga que depurar la aplicación de Java de forma remota en una instancia de rol específica. Pero, si habilita un punto de conexión de entrada normal para la depuración, el equilibrador de carga de Azure hará casi imposible conectar el depurador a una instancia de rol específica. En su lugar lo conectará a una instancia de rol que elige aleatoriamente.

Este es el tipo de escenario donde si saca partido de los puntos de conexión de entrada de instancia le resultará más fácil depurar una instancia de rol específica.

Supongamos que tiene previsto ejecutar hasta cinco instancias de rol de la implementación. Mediante la página de propiedades **Endpoints** (Puntos de conexión) del cuadro de diálogo de propiedades del rol, cree un punto de conexión de entrada de instancia y asigne un intervalo de puertos públicos, en lugar de un solo número de puerto. Por ejemplo, en el cuadro de entrada **Public port** (Puerto público), especifique **81-85**.

Después de implementar la aplicación con este punto de conexión de instancia, Azure asignará un número de puerto único de este intervalo a cada una de las instancias de rol. Después, para averiguar a qué instancia se asignó el número de puerto, puede usar la variable de entorno *InstanceEndpointName***\_PUBLICPORT** (donde *InstanceEndpointName* es el nombre que se asignó al crear el punto de conexión de la instancia) que el kit de herramientas configuró automáticamente en la implementación (por ejemplo, al devolver este valor en el pie de página de una página web, para que pueda leerlo cuando vaya a él).

Cuando sepa qué número de puerto público se ha asignado a esa instancia, puede hacer referencia a él en la configuración de depuración en Eclipse, añadiéndolo como afijo al nombre de host del servicio. Esto permitirá que el depurador de Eclipse se conecte a esa instancia específica y no a alguna de las demás instancias.

## Solo Windows: Para depurar la aplicación mientras se ejecuta en el emulador de proceso ##

>[AZURE.NOTE] El emulador de Azure solo está disponible en Windows. Omita esta sección si usa un sistema operativo distinto de Windows.

1. Compile el proyecto para realizar pruebas en el emulador: en el Project Explorer (Explorador de proyectos) de Eclipse, haga clic en **MyAzureProject**, **Properties** (Propiedades), **Azure**, y establezca **Build for** (Compilar para) en **Testing in emulator** (Pruebas en el emulador).
1. Recompile el proyecto: en el menú de Eclipse, haga clic en **Project** (Proyecto) y luego en **Build All** (Compilar todo).
1. En el explorador de proyectos de Eclipse, haga clic con el botón derecho en **WorkerRole1**, **Azure**, y luego en **Debugging** (Depuración).
1. En el cuadro de diálogo **Properties for WorkerRole1 Debugging** (Propiedades para la depuración de WorkerRole1):
    1. Active **Enable remote debugging for this role** (Habilitar la depuración remota para este rol).
    1. Como **Input endpoint to use**, use el punto de conexión predeterminado que generó automáticamente el kit de herramientas, que aparece como **Debugging (public:8090, private:8090)**.
    1. Asegúrese de que la opción **Start JVM in suspended mode, waiting for a debugger connection** (Iniciar JVM en modo suspendido, esperando una conexión del depurador) está desactivada.

        >[AZURE.IMPORTANT] La opción **Start JVM in suspended mode, waiting for a debugger connection** está diseñada solo para escenarios de depuración avanzada en el emulador de proceso (no para implementaciones en la nube). Si se usa la opción **Start JVM in suspended mode, waiting for a debugger connection**, suspenderá el proceso de inicio del servidor hasta que el depurador de Eclipse se conecte a su JVM. Aunque puede emplear esta opción para una sesión de depuración con el emulador de proceso, no la use para una sesión de depuración en una implementación en la nube. La inicialización de un servidor se produce en una tarea de inicio de Azure y la nube de Azure no tiene puntos de conexión públicos disponibles hasta que se complete la tarea de inicio. Por lo tanto, un proceso de inicio no se completará correctamente si esta opción está habilitada en una implementación de la nube, ya que no será capaz de recibir una conexión desde un cliente externo de Eclipse.
    1. Haga clic en **Create Debug Configurations** (Crear configuraciones de depuración).
1. En el cuadro de diálogo **Azure Debug Configuration** (Configuración de depuración de Azure):
    1. Como **Java project to debug** (Proyecto Java para depurar), seleccione el proyecto **MyHelloWorld**.
    1. En **Configure debugging for** (Configurar depuración para), active **Azure compute emulator** (Emulador de proceso de Azure).
1. Haga clic en **OK** (Aceptar) para cerrar el cuadro de diálogo **Azure Debug Configuration**.
1. Haga clic en **OK** para cerrar el cuadro de diálogo **Properties for WorkerRole1 Debugging**.
1. Establezca un punto de interrupción en index.jsp:
    1. En el explorador de proyectos de Eclipse, expanda **MyHelloWorld**, expanda **WebContent**, y haga doble clic en **index.jsp**.
    1. Dentro de index.jsp, haga clic en la barra azul a la izquierda del código Java y haga clic en **Toggle Breakpoints**, como se muestra a continuación: ![][ic551537] Se establece un punto de interrupción si aparece un icono de punto de interrupción dentro de la barra azul a la izquierda del código Java.
1. Inicie la aplicación en el emulador de proceso haciendo clic en el botón **Run in Azure Emulator** (Ejecutar en el emulador de Azure) en la barra de herramientas de Azure.
1. En el menú de Eclipse, haga clic en **Run** (Ejecutar) y luego en **Debug Configurations** (Depurar configuraciones).
1. En el cuadro de diálogo **Debug Configurations** (Depurar configuraciones), expanda **Remote Java Application** (Aplicación de Java remota) en el panel de la izquierda, seleccione **Azure Emulator (WorkerRole1)** (Emulador de Azure (WorkerRole1)) y haga clic en **Debug** (Depurar).
1. Cuando el emulador de proceso indique que la aplicación se ejecuta en el explorador, ejecute ****http://localhost:8080/MyHelloWorld**. Si un cuadro de diálogo **Confirm Perspective Switch** (Confirmar cambio de perspectiva) se lo pide, haga clic en **Yes** (Sí). La sesión de depuración debe ejecutarse ahora en la línea de código donde se estableció el punto de interrupción.

Con esto se ha mostrado cómo depurar en el emulador de proceso; la sección siguiente muestra cómo depurar una aplicación implementada en Azure.  


## Notas de la depuración ##

* Tras la depuración, puede cambiar la perspectiva de **Depurar** a **Java** haciendo clic en el menú de Eclipse, en **Window** (Ventana), **Open Perspective** (Abrir perspectiva), y seleccionando la perspectiva que quiere usar.
* Para habilitar la depuración remota en GlassFish, no use la característica de configuración de depuración remota del kit de herramientas de Azure para Eclipse. En su lugar, configure manualmente GlassFish. Debido a la manera en que GlassFish trata las opciones de Java predefinidas en variables de entorno, la característica de configuración de depuración remota de este kit de herramientas no funciona correctamente con GlassFish. Si la característica de configuración de depuración remota de este kit de herramientas está habilitada, puede que impida que GlassFish se inicie.

## Otras referencias ##

[Kit de herramientas de Azure para Eclipse][]

[Creación de una aplicación Hello World para Azure en Eclipse][]

[Instalación del kit de herramientas de Azure para Eclipse][]

Para obtener más información sobre el uso de Azure con Java, vea el [Centro para desarrolladores de Java de Azure][].

<!-- URL List -->

[Centro para desarrolladores de Java de Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Portal de administración de Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creación de una aplicación Hello World para Azure en Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalación del kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Uso de la biblioteca de tiempo de ejecución del servicio de Azure en JSP]: http://go.microsoft.com/fwlink/?LinkID=699551

<!-- IMG List -->

[ic719504]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic719504.png
[ic551537]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic551537.png

<!---HONumber=AcomDC_0302_2016-->