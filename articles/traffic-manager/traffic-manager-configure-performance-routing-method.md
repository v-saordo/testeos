<properties 
   pageTitle="Configuración del método de enrutamiento del tráfico de rendimiento | Microsoft Azure"
   description="Este artículo le ayudará a configurar el método de enrutamiento del tráfico de rendimiento en el Administrador de tráfico"
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
   ms.date="12/09/2015"
   ms.author="joaoma" />

# Configuración del método de enrutamiento del tráfico de rendimiento

Para redirigir el tráfico a los servicios en la nube y los sitios web (extremos) que se encuentran en distintos centros de datos de todo el mundo (también conocidos como regiones), puede dirigir el tráfico entrante al extremo con la latencia más baja desde el cliente que realiza la solicitud. Normalmente, el centro de datos con la latencia más baja corresponde a la distancia geográfica más cercana. El método de enrutamiento del tráfico de rendimiento le permitirá realizar la distribución según la latencia inferior, pero no puede tener en cuenta los cambios en tiempo real en la carga o la configuración de red. Para obtener más información sobre los distintos métodos de enrutamiento de tráfico que proporciona el Administrador de tráfico de Azure, vea [Acerca de la supervisión del Administrador de tráfico](traffic-manager-load-balancing-methods.md).

## Redirija el tráfico en función de la latencia más baja en un conjunto de extremos:

1. En el Portal de Azure clásico, en el panel izquierdo, haga clic en el icono **Administrador de tráfico** para abrir el panel del Administrador de tráfico. Si aún no ha creado su perfil de Administrador de tráfico, consulte [Administración de perfiles del Administrador de tráfico](traffic-manager-manage-profiles.md) para conocer el procedimiento de creación de un perfil básico del Administrador de tráfico.
2. En el panel Administrador de tráfico del Portal de Azure clásico, localice el perfil del Administrador de tráfico que contiene la configuración que desea modificar y haga clic en la flecha situada a la derecha del nombre del perfil. Se abrirá la página de configuración del perfil.
3. En la página del perfil,haga clic en **Extremos** y compruebe que están presentes los extremos del servicio que desea incluir en la configuración. Para saber cómo agregar o quitar extremos del perfil, consulte [Administración de extremos en el Administrador de tráfico](traffic-manager-endpoints.md).
4. En la página del perfil, haga clic en **Configurar** en la parte superior, para abrir la página de configuración.
5. En **Configuración del método de enrutamiento del tráfico**, compruebe que dicho método sea **Rendimiento*. Si no es así, haga clic en **Rendimiento** en la lista desplegable.
6. Compruebe que la **Configuración de supervisión** sea correcta. La supervisión garantiza que no se envíe tráfico a los extremos sin conexión. Para supervisar los extremos, debe especificar una ruta de acceso y un nombre de archivo. Tenga en cuenta que una barra diagonal “/“ es una entrada válida para la ruta de acceso relativa e implica que el archivo se encuentra en el directorio raíz (valor predeterminado). Para obtener más información acerca de la supervisión, consulte [Acerca de la supervisión del Administrador de tráfico](traffic-manager-monitoring.md).
7. Una vez que haya terminado de cambiar la configuración, haga clic en **Guardar** en la parte inferior de la página.
8. Pruebe los cambios de la configuración. Para obtener más información, consulte [Comprobación de la configuración del Administrador de tráfico](traffic-manager-testing-settings.md).
9. Una vez que el perfil del Administrador de tráfico se haya configurado y esté en funcionamiento, edite el registro DNS en el servidor DNS relevante para redireccionar el nombre de dominio de la empresa al nombre de dominio del Administrador de tráfico. Para obtener más información acerca del procedimiento, consulte [Seleccionar un dominio de la compañía en Internet para un dominio del Administrador de tráfico](traffic-manager-point-internet-domain.md).

## Pasos siguientes


[Hacer que un dominio de Internet de la empresa indique un dominio del Administrador de tráfico](traffic-manager-point-internet-domain.md)

[Métodos de enrutamiento del Administrador de tráfico](traffic-manager-routing-methods.md)

[Configuración del método de enrutamiento de conmutación por error](traffic-manager-configure-failover-routing-method.md)

[Configuración del método de enrutamiento round robin](traffic-manager-configure-round-robin-routing-method.md)

[Solución de problemas de estado degradado del Administrador de tráfico](traffic-manager-troubleshooting-degraded.md)

[Administrador de tráfico: deshabilitación, habilitación o eliminación de un perfil](disable-enable-or-delete-a-profile.md)

[Administrador de tráfico: deshabilitación o habilitación de un extremo](disable-or-enable-an-endpoint.md)
 

<!---HONumber=AcomDC_0204_2016-->