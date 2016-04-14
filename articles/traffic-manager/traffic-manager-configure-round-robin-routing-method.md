<properties
   pageTitle="Configuración del método de enrutamiento del tráfico round robin del Administrador de tráfico| Microsoft Azure"
   description="Este artículo le ayuda a configurar el equilibrio de carga Round Robin para los extremos del Administrador de tráfico."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="joaoma" />

# Configuración del método de enrutamiento round robin

Un patrón del método de enrutamiento del tráfico es proporcionar un conjunto de extremos idénticos, que incluye servicios en la nube y sitios web, y enviar tráfico a cada uno de ellos de forma equitativa (round robin). En los pasos siguientes se explica cómo configurar el Administrador de tráfico con el fin de realizar este tipo de método de enrutamiento del tráfico. Para obtener más información acerca de los diferentes métodos de enrutamiento del tráfico, consulte [Información acerca de los métodos de enrutamiento del tráfico del Administrador de tráfico](traffic-manager-load-balancing-methods.md).

>[AZURE.NOTE]Azure ya proporciona la funcionalidad de equilibrio de carga Round Robin para sitios web en un centro de datos (también denominado región). El Administrador de tráfico permite especificar el método de enrutamiento del tráfico round robin para sitios web en distintos centros de datos.

## Equilibre el enrutamiento del tráfico de forma equitativa (round robin) en un conjunto de extremos:

1. En el Portal de Azure clásico, en el panel izquierdo, haga clic en el icono **Administrador de tráfico** para abrir el panel del Administrador de tráfico. Si aún no ha creado su perfil de Administrador de tráfico, consulte [Administración de perfiles del Administrador de tráfico](traffic-manager-manage-profiles.md) para conocer el procedimiento de creación de un perfil básico del Administrador de tráfico.
2. En el panel Administrador de tráfico del Portal de Azure clásico, localice el perfil del Administrador de tráfico que contiene la configuración que desea modificar y haga clic en la flecha situada a la derecha del nombre del perfil. Se abrirá la página de configuración del perfil.
3. En la página del perfil, haga clic en **Extremos** y compruebe que están presentes los extremos del servicio que desea incluir en la configuración. Para saber cómo agregar o quitar extremos, consulte [Administración de extremos en el Administrador de tráfico](traffic-manager-endpoints.md).
4. En la página del perfil, haga clic en **Configurar** en la parte superior, para abrir la página de configuración.
5. En **Configuración del método de enrutamiento del tráfico**, compruebe que dicho método sea **Round Robin**. De lo contrario, haga clic en **Round Robin** en la lista desplegable.
6. Compruebe que la **Configuración de supervisión** sea correcta. La supervisión garantiza que no se envíe tráfico a los extremos sin conexión. Para supervisar los extremos, debe especificar una ruta de acceso y un nombre de archivo. Tenga en cuenta que una barra diagonal “/“ es una entrada válida para la ruta de acceso relativa e implica que el archivo se encuentra en el directorio raíz (valor predeterminado). Para obtener más información acerca de la supervisión, consulte [Información acerca de la supervisión del Administrador de tráfico](traffic-manager-monitoring.md).
7. Una vez que haya terminado de cambiar la configuración, haga clic en **Guardar** en la parte inferior de la página.
8. Pruebe los cambios de la configuración. Para obtener más información, consulte [Comprobación de la configuración del Administrador de tráfico](traffic-manager-testing-settings.md).
9. Una vez que el perfil del Administrador de tráfico se haya configurado y esté en funcionamiento, edite el registro DNS en el servidor DNS relevante para redireccionar el nombre de dominio de la empresa al nombre de dominio del Administrador de tráfico. Para obtener más información acerca del procedimiento, consulte [Seleccionar un dominio de la compañía en Internet para un dominio del Administrador de tráfico](traffic-manager-point-internet-domain.md).

## Pasos siguientes


[Hacer que un dominio de Internet de la empresa indique un dominio del Administrador de tráfico](traffic-manager-point-internet-domain.md)

[Métodos de enrutamiento del Administrador de tráfico](traffic-manager-routing-methods.md)

[Configuración del método de enrutamiento de conmutación por error](traffic-manager-configure-failover-routing-method.md)

[Configuración del método de enrutamiento de rendimiento](traffic-manager-configure-performance-routing-method.md)

[Solución de problemas de estado degradado del Administrador de tráfico](traffic-manager-troubleshooting-degraded.md)

[Administrador de tráfico: deshabilitación, habilitación o eliminación de un perfil](disable-enable-or-delete-a-profile.md)

[Administrador de tráfico: deshabilitación o habilitación de un extremo](disable-or-enable-an-endpoint.md)

 

<!---HONumber=AcomDC_1210_2015-->