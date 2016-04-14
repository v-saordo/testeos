<properties 
	pageTitle="Depuración de una máquina virtual o un servicio en la nube de Azure en Visual Studio | Microsoft Azure"
	description="Depurar un servicio en la nube o una máquina virtual en Visual Studio"
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="" />
<tags 
	ms.service="visual-studio-online"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="02/04/2016"
	ms.author="tarcher" />

# Depuración de una máquina virtual o un servicio en la nube de Azure en Visual Studio

Visual Studio proporciona distintas opciones para la depuración de servicios en la nube y máquinas virtuales de Azure.



## Depuración del servicio en la nube en el equipo local

Puede ahorrar tiempo y dinero si utiliza el emulador de proceso de Azure para depurar su servicio en la nube en un equipo local. La depuración de un servicio localmente antes de su implementación puede mejorar la fiabilidad y el rendimiento sin pagar por tiempo de proceso. No obstante, pueden producirse algunos errores solo al ejecutar un servicio en la nube en el propio Azure. Puede depurar estos errores si habilita la depuración remota al publicar el servicio y luego adjuntar el depurador a una instancia de rol.

El emulador simula el servicio de proceso de Azure y se ejecuta en el entorno local para que pueda probar y depurar el servicio en la nube antes de implementarlo. El emulador administra el ciclo de vida de sus instancias de rol y proporciona acceso a recursos simulados, tales como el almacenamiento local. Al depurar o ejecutar el servicio desde Visual Studio, este entorno inicia automáticamente el emulador como una aplicación en segundo plano y después implementa el servicio en el emulador. Puede utilizar el emulador para ver su servicio cuando se ejecuta en el entorno local. Puede ejecutar la versión completa o la versión Express del emulador. (A partir de Azure 2.3, la versión Express del emulador es la opción predeterminada). Consulte [Uso de Emulator Express para ejecutar y depurar localmente un servicio en la nube](https://msdn.microsoft.com/library/dn339018.aspx).

### Para depurar el servicio en la nube en el equipo local

1. En la barra de menús, elija **Depurar**, **Iniciar depuración** para ejecutar el proyecto de servicio en la nube de Azure. Como alternativa, puede presionar F5. Verá un mensaje que indica que el emulador de proceso se está iniciando. Cuando se inicia el emulador, el icono de bandeja del sistema lo confirma.

    ![Emulador de Azure en la bandeja del sistema](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC783828.png)

1. Muestre la interfaz de usuario para el emulador de proceso abriendo el menú contextual del icono de Azure en el área de notificación y haciendo clic en **Mostrar la UI del emulador de proceso**.

    El panel izquierdo de la interfaz de usuario muestra los servicios implementados actualmente en el emulador de proceso y las instancias de rol que cada servicio ejecuta. Puede elegir el servicio o los roles para mostrar información sobre el ciclo de vida, el registro y el diagnóstico en el panel derecho. Si coloca el foco en el margen superior de una ventana incluida, se expande para rellenar el recuadro derecho.

1. Recorra la aplicación haciendo clic en los comandos del menú **Depurar** y estableciendo puntos de interrupción en el código. A medida que recorre la aplicación en el depurador, los paneles se actualizan con el estado actual de la aplicación. Al detener la depuración, se elimina la implementación de la aplicación. Si su aplicación incluye un rol web y ha establecido la propiedad Acción de inicio para iniciar el explorador web, Visual Studio inicia la aplicación web en el explorador. Si cambia el número de instancias de un rol en la configuración del servicio, debe detener el servicio en la nube y después reiniciar la depuración para que pueda depurar estas nuevas instancias del rol.

    **Nota:** cuando se deja de ejecutar o depurar el servicio, el emulador de proceso y el emulador de almacenamiento locales no se detienen. Debe detenerlos explícitamente en el área de notificación.


## Depuración de un servicio en la nube en Azure

Para depurar un servicio en la nube desde un equipo remoto, debe habilitar esta funcionalidad de forma explícita cuando implemente su servicio en la nube para que los servicios requeridos (como msvsmon.exe) se instalen en las máquinas virtuales que ejecutan sus instancias de rol. Si no habilitó la depuración remota al publicar el servicio, deberá volver a publicar el servicio con la depuración remota habilitada.

Si habilita la depuración remota para un servicio en la nube, este no mostrará un rendimiento inferior ni incurrirá en cargos adicionales. No debe usar la depuración remota en un servicio de producción, porque podría perjudicar a los clientes que usan el servicio.

>[AZURE.NOTE] Al publicar un servicio en la nube en Visual Studio, puede habilitar **IntelliTrace** para los roles de ese servicio cuyo destino sea .NET Framework 4 o .NET Framework 4.5. Con **IntelliTrace**, puede examinar eventos que se produjeron en una instancia de rol en el pasado y reproducir el contexto a partir de ese momento. Consulte [Depuración con IntelliTrace y Visual Studio de un servicio en la nube publicado](http://go.microsoft.com/fwlink/?LinkID=623016) y [Uso de IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx).

### Para habilitar la depuración remota para un servicio en la nube

1. Abra el menú contextual del proyecto de Azure y haga clic en **Publicar**.

1. Seleccione el entorno de **ensayo** y la configuración de **depuración**.

    Esto es solo de referencia. Puede optar por ejecutar sus entornos de prueba en un entorno de producción. No obstante, puede perjudicar a los usuarios si habilita la depuración remota en el entorno de producción. Puede seleccionar la configuración Liberar, pero la configuración Depurar simplifica la depuración.

    ![Elegir la configuración Depurar](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746717.gif)

1. Siga los pasos habituales pero active la casilla **Habilitar depurador remoto para todos los roles** de la pestaña **Configuración avanzada**.

    ![Configuración de depuración](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746718.gif)

### Para asociar el depurador a un servicio en la nube en Azure

1. En el Explorador de servidores, expanda el nodo del servicio en la nube.

1. Abra el menú contextual del rol o la instancia de rol al que quiera asociarlo y luego haga clic en **Asociar depurador**.

    Si depura un rol, el depurador de Visual Studio se asociará a cada instancia de dicho rol. El depurador se interrumpirá en un punto de interrupción para la primera instancia de rol que ejecute esta línea de código y cumpla las condiciones de dicho punto de interrupción. Si depura una instancia, el depurador solo se asociará a dicha instancia y se interrumpirá en un punto de interrupción solamente cuando esa instancia específica ejecute esa línea de código y cumpla las condiciones del punto de interrupción.

    ![Asociar depurador](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746719.gif)

1. Después de que el depurador se asocie a una instancia, realice la depuración del modo habitual. El depurador se asocia automáticamente al proceso de host adecuado para el rol. En función del tipo de rol, el depurador se asocia a w3wp.exe, WaWorkerHost.exe o WaIISHost.exe. Para comprobar el proceso al que se ha asociado el depurador, expanda el nodo de la instancia en el Explorador de servidores. Consulte [Arquitectura de roles de Azure](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) para obtener más información sobre los procesos de Azure.

    ![Seleccionar el cuadro de diálogo de tipo de código](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

1. Para identificar los procesos a los que se ha adjuntado el depurador, abra el cuadro de diálogo Procesos. Para ello, en la barra de menús, seleccione Depurar, Windows, Procesos. (Teclado: Ctrl + Alt + Z) Para desasociar un proceso específico, abra su menú contextual y, a continuación, haga clic en Desasociar proceso. También puede localizar el nodo de la instancia en el Explorador de servidores, buscar el proceso, abrir su menú contextual y hacer clic en Desasociar proceso.

    ![Depurar procesos](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC690787.gif)

>[AZURE.WARNING] Evite detenciones prolongadas en los puntos de interrupción durante la depuración remota. Azure trata un proceso que se detiene durante algo más que unos minutos como si hubiese dejado de responder y detiene el envío de tráfico a dicha instancia. Si efectúa una detención demasiado prolongada, msvsmon.exe se separará del proceso.

Para separar el depurador de todos los procesos de su instancia o rol, abra el menú contextual del rol o la instancia que está depurando y haga clic en Desasociar depurador.

## Limitaciones de la depuración remota en Azure

Desde Azure SDK 2.3, la depuración remota presenta las siguientes limitaciones.

- Con la depuración remota habilitada, no puede publicar un servicio en la nube que presente algún rol con más de 25 instancias.

- El depurador usa los puertos de 30400 a 30424, de 31400 a 31424 y de 32400 a 32424. Si intenta usar alguno de estos puertos, no podrá publicar su servicio y aparecerá uno de los siguientes mensajes de error en el registro de actividad de Azure:

    - Error al validar el archivo .cscfg con el archivo .csdef. El intervalo de puertos reservado 'intervalo de puertos' para el extremo Microsoft.WindowsAzure.Plugins.RemoteDebugger.Connector del rol 'rol' se superpone con un intervalo o puerto ya definido.
    - Error en la asignación. Vuelva a intentarlo más tarde, intente reducir el tamaño de VM o el número de instancias de rol, o intente implementar en una región distinta.


## Depuración de máquinas virtuales de Azure

Puede depurar programas que se ejecuten en máquinas virtuales de Azure usando el Explorador de servidores en Visual Studio. Si habilita la depuración remota en una máquina virtual de Azure, Azure instalará la extensión de depuración remota en la máquina virtual. A continuación, podrá asociarla a procesos en la máquina virtual y realizar la depuración como lo haría normalmente.

>[AZURE.NOTE] Las máquinas virtuales creadas a través de la pila del Administrador de recursos de Azure se pueden depurar remotamente mediante Cloud Explorer en Visual Studio de 2015. Para obtener más información, consulte [Administración de recursos de Azure con Cloud Explorer](http://go.microsoft.com/fwlink/?LinkId=623031).

### Para depurar una máquina virtual de Azure

1. En el Explorador de servidores, expanda el nodo Máquinas virtuales y seleccione el nodo de la máquina virtual que desea depurar.

1. Abra el menú contextual y haga clic en **Habilitar depuración**. Cuando se le pida que confirme si desea habilitar la depuración en la máquina virtual, haga clic en **Sí**.

    Azure instala la extensión de depuración remota en la máquina virtual para habilitar la depuración.

    ![Comando para habilitar la depuración en máquina virtual](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Registro de actividades de Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)

1. Cuando la extensión de depuración remota finalice la instalación, abra el menú contextual de la máquina virtual y haga clic en **Asociar depurador...**

    Azure obtiene una lista de los procesos presentes en la máquina virtual y los muestra en el cuadro de diálogo Asociar al proceso.

    ![Comando Asociar depurador](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)

1. En el cuadro de diálogo **Asociar al proceso**, haga clic en **Seleccionar** para limitar la lista de resultados de forma que solo se muestren los tipos de código que quiere depurar. Puede depurar código administrado de 32 o 64 bits, código nativo o ambos.

    ![Seleccionar el cuadro de diálogo de tipo de código](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

1. Haga clic en los procesos que quiere depurar en la máquina virtual y después en **Asociar**. Por ejemplo, puede seleccionar el proceso w3wp.exe si desea depurar una aplicación web en la máquina virtual. Consulte [Depuración de uno o varios procesos en Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) y [Arquitectura de roles de Azure](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) para obtener más información.

## Creación de un proyecto web y una máquina virtual para la depuración

Antes de publicar el proyecto de Azure, es posible que le resulte útil probarlo en un entorno contenido que sea compatible con los entornos de depuración y pruebas, y en el que pueda instalar programas de pruebas y supervisión. Una manera de hacerlo es depurar su aplicación de forma remota en una máquina virtual.

Los proyectos ASP.NET de Visual Studio ofrecen una opción para crear una práctica máquina virtual que se puede usar para probar aplicaciones. La máquina virtual incluye extremos requeridos habitualmente, como PowerShell, Escritorio remoto y WebDeploy.

### Para crear de un proyecto web y una máquina virtual para la depuración

1. En Visual Studio, cree una nueva aplicación web ASP.NET.

1. En el cuadro de diálogo Nuevo proyecto ASP.NET, en la sección Azure, elija **Máquina virtual** del cuadro de lista desplegable. Deje la casilla **Crear recursos remotos** activada. Haga clic en **Aceptar** para continuar.

    Aparecerá el cuadro de diálogo **Crear máquina virtual en Azure**.


    ![Cuadro de diálogo Crear nuevo proyecto ASP.NET](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746723.png)

    **Nota:** se le pedirá que inicie sesión en la cuenta de Azure si todavía no lo ha hecho.

1. Seleccione los distintos valores de configuración de la máquina virtual y después haga clic en **Aceptar**. Consulte [Máquinas virtuales](http://go.microsoft.com/fwlink/?LinkId=623033) para obtener más información.

    El nombre que escriba como nombre DNS será el nombre de la máquina virtual.

    ![Cuadro de diálogo Crear máquina virtual en Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746724.png)

    Azure crea la máquina virtual y, a continuación, aprovisiona y configura los extremos, como Escritorio remoto y Web Deploy.



1. Cuando la máquina virtual esté completamente configurada, seleccione el nodo de la máquina virtual en el Explorador de servidores.

1. Abra el menú contextual y haga clic en **Habilitar depuración**. Cuando se le pida que confirme si desea habilitar la depuración en la máquina virtual, haga clic en **Sí**.

    Azure instala la extensión de depuración remota en la máquina virtual para habilitar la depuración.

    ![Comando para habilitar la depuración en máquina virtual](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Registro de actividades de Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)

1. Publique su proyecto tal y como se describe en [Implementación de un proyecto web utilizando publicación con un solo clic en Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx). Dado que desea realizar la depuración en la máquina virtual, en la página **Configuración** del asistente para **Publicación web**, seleccione **Depurar** como configuración. Esto garantiza la disponibilidad de los símbolos de código durante la depuración.

    ![Configuración Publicar](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718349.png)

1. En **Opciones de publicación de archivos**, seleccione **Quitar archivos adicionales en destino** si el proyecto ya se implementó anteriormente.

1. Después de publicar el proyecto, en el menú contextual de la máquina virtual en el Explorador de servidores, haga clic en **Asociar depurador...**

    Azure obtiene una lista de los procesos presentes en la máquina virtual y los muestra en el cuadro de diálogo Asociar al proceso.

    ![Comando Asociar depurador](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)

1. En el cuadro de diálogo **Asociar al proceso**, haga clic en **Seleccionar** para limitar la lista de resultados de forma que solo se muestren los tipos de código que quiere depurar. Puede depurar código administrado de 32 o 64 bits, código nativo o ambos.

    ![Seleccionar el cuadro de diálogo de tipo de código](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

1. Haga clic en los procesos que quiere depurar en la máquina virtual y después en **Asociar**. Por ejemplo, puede seleccionar el proceso w3wp.exe si desea depurar una aplicación web en la máquina virtual. Consulte [Depuración de uno o varios procesos en Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) para obtener más información.

## Pasos siguientes

- Use **Intellitrace** para recopilar un registro de llamadas y eventos de un servidor de versión. Consulte [Depuración con IntelliTrace y Visual Studio de un servicio en la nube publicado](http://go.microsoft.com/fwlink/?LinkID=623016).
- Use **Diagnósticos de Azure** para registrar información detallada del código que se ejecuta en los roles, bien sea en el entorno de desarrollo o en Azure. Consulte [Recopilación de datos de registro mediante Diagnósticos de Azure](http://go.microsoft.com/fwlink/p/?LinkId=400450).

<!---HONumber=AcomDC_0211_2016-->