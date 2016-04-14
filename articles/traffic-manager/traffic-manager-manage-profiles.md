<properties
   pageTitle="Administrar perfiles del Administrador de tráfico de Azure | Microsoft Azure"
   description="Este artículo le ayudará a crear, deshabilitar, habilitar, eliminar y ver el historial de un perfil del Administrador de tráfico de Azure."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="joaoma" />

# Administrar un perfil del Administrador de tráfico de Azure

Use un perfil del Administrador de tráfico para especificar los servicios en la nube o extremos de sitio web que se van a supervisar mediante el Administrador de tráfico y qué método de enrutamiento del tráfico desea emplear para distribuir las conexiones a estos extremos.

## Crear un perfil del Administrador de tráfico mediante Creación rápida

Puede crear rápidamente un perfil del Administrador de tráfico mediante Creación rápida en el Portal de Azure clásico. Creación rápida permite crear perfiles con valores de configuración básicos. Sin embargo, no puede usar Creación rápida para valores como, por ejemplo, el conjunto de extremos (servicios en la nube y sitios web), el orden de conmutación por error para el método de enrutamiento del tráfico de conmutación por error o la configuración de supervisión. Después de crear el perfil, puede configurar estas opciones en el Portal de Azure clásico. El Administrador de tráfico admite un máximo de 200 extremos por perfil. Sin embargo, la mayoría de los escenarios de uso tan solo requieren un pequeño número de extremos.

### Para crear un nuevo perfil del Administrador de tráfico

1. **Implemente en el entorno de producción sus servicios en la nube y sitios web.** Para obtener más información sobre servicios en la nube, consulte [Servicios en la nube](http://go.microsoft.com/fwlink/p/?LinkId=314074). Para obtener más información sobre los servicios en la nube, consulte [Procedimientos recomendados](https://msdn.microsoft.com/library/azure/5229dd1c-5a91-4869-8522-bed8597d9cf5#bkmk_TrafficManagerBestPracticesProfile). Para obtener más información acerca de los sitios web, consulte [Sitios web](http://go.microsoft.com/fwlink/p/?LinkId=393327).

2. **Inicie sesión en el Portal de Azure clásico**. Para crear un nuevo perfil del Administrador de tráfico, haga clic en **Nuevo** en la parte inferior izquierda del portal, haga clic en **Servicios de red > Administrador de tráfico** y, a continuación, en **Creación rápida** para comenzar a configurar su perfil.
3. **Configure el prefijo DNS.** Proporcione a su perfil de Administrador de tráfico un nombre de prefijo DNS único. Puede especificar solo el prefijo de un nombre de dominio de Administrador de tráfico.
4. **Seleccione la suscripción.** Seleccione la suscripción de Azure apropiada. Cada perfil está asociado a una sola suscripción. Si sólo tiene una suscripción, esta opción no aparecerá.
5. **Método de enrutamiento del tráfico de rendimiento** Seleccione el método de enrutamiento del tráfico en **Directiva de enrutamiento del tráfico**. Para obtener más información acerca de los métodos de enrutamiento del tráfico, consulte[ Información acerca de los métodos de enrutamiento del tráfico del Administrador de tráfico](traffic-manager-load-balancing-methods.md).
6. **Haga clic en “Crear” para crear un nuevo perfil**. Cuando haya terminado de configurar el perfil, puede buscarlo en el panel del Administrador de tráfico del Portal de Azure clásico.
7. **Configure los puntos de conexión, la supervisión y la configuración adicional en el Portal de Azure clásico**. Dado que sólo se pueden configurar valores básicos mediante la Creación rápida, es necesario configurar opciones adicionales tales como la lista de extremos y el orden de conmutación por error del extremo, con el fin de completar la configuración deseada. 


## Deshabilitar, habilitar o eliminar un perfil

Puede deshabilitar un perfil de Administrador de tráfico existente de para que no envíe solicitudes de usuario a sus extremos configurados. Cuando deshabilite un perfil de Administrador de tráfico, dicho perfil y la información que contiene se mantendrán intactos y pueden editarse en la interfaz del Administrador de tráfico. Si desea volver a habilitar el perfil, puede hacerlo fácilmente en el Portal de Azure clásico y los envíos se reanudarán. Cuando se crea un perfil del Administrador de tráfico en el Portal de Azure clásico, este se habilita automáticamente. Si decide que un perfil ya no será necesario, puede eliminarlo.

### Para deshabilitar un perfil

1. Modifique el registro de recursos DNS en el servidor DNS de Internet para usar el tipo de registro adecuado y un puntero a otro nombre o a la dirección IP de una ubicación específica en Internet. En otras palabras, debe cambiar el registro de recursos DNS del servidor DNS de Internet para que ya no utilice un registro de recursos CNAME que señale al nombre de dominio del perfil del Administrador de tráfico.
2. El tráfico dejará de dirigirse a los extremos a través de la configuración del perfil del Administrador de tráfico.
3. Seleccione el perfil que desea deshabilitar. Para seleccionar el perfil, en la página Administrador de tráfico, resalte el perfil haciendo clic en la columna situada junto al nombre del perfil. No haga clic en el nombre del perfil ni en la flecha que aparece junto al nombre, ya que esto le llevará a la página de configuración del perfil.
4. Después de seleccionar el perfil, haga clic en **Deshabilitar** en la parte inferior de la página.

### Para habilitar un perfil

1. Seleccione el perfil que desea habilitar. Para seleccionar el perfil, en la página Administrador de tráfico, resalte el perfil haciendo clic en la columna situada junto al nombre del perfil. No haga clic en el nombre del perfil ni en la flecha que aparece junto al nombre, ya que esto le llevará a la página de configuración del perfil.
2. Después de seleccionar el perfil, haga clic en **Habilitar** en la parte inferior de la página.
3. Modifique el registro de recursos DNS del servidor DNS de Internet para que use el tipo de registro CNAME, que asigna el nombre de dominio de la empresa al nombre de dominio del perfil del Administrador de tráfico. Para obtener más información, consulte [Selección de un dominio de Internet de la compañía para un dominio del Administrador de tráfico](traffic-manager-point-internet-domain.md).
4. El tráfico comenzará a dirigirse de nuevo a los extremos.

### Para eliminar un perfil

1. Asegúrese de que el registro de recursos DNS del servidor DNS de Internet ya no usa un registro de recursos CNAME que señale al nombre de dominio del perfil del Administrador de tráfico.
2. Seleccione el perfil que desea eliminar. Para seleccionar el perfil, en la página Administrador de tráfico, resalte el perfil haciendo clic en la columna situada junto al perfil. No haga clic en el nombre del perfil ni en la flecha que aparece junto al nombre, ya que esto le llevará a la página de configuración del perfil.
4. Después de seleccionar el perfil, haga clic en **Eliminar** en la parte inferior de la página.

## Ver el historial de cambios de perfiles del Administrador de tráfico

Puede ver el historial de cambios del perfil del Administrador de tráfico en el Portal de Azure clásico, en Servicios de administración.

### Para ver el historial de cambios del Administrador de tráfico

1. En el panel izquierdo del Portal de Azure clásico, haga clic en **Servicios de administración**.
2. En la página de Servicios de administración, haga clic en **Registros de operaciones**.
3. En la página Registros de operaciones puede usar los filtros para ver el historial de cambios del perfil del Administrador de tráfico. Después de seleccionar las opciones de filtrado, haga clic en la marca de verificación para ver los resultados.
   - Para ver los cambios de todos los perfiles, seleccione la suscripción y un intervalo de tiempo y, a continuación, seleccione **Administrador de tráfico** en el menú contextual **Tipo**.
   - Para filtrar por nombre de perfil, escriba el nombre del perfil en el campo **Nombre del servicio** o selecciónelo en el menú contextual.
   - Para ver los detalles de cada cambio individualmente, seleccione la fila que contiene el cambio que desea ver y haga clic en **Detalles**, en la parte inferior de la página. En la ventana **Detalles de la operación**, puede ver la representación XML del objeto de API que se creó o actualizó como parte de la operación y copiar el código XML en el Portapapeles.


## Pasos siguientes

[Agregación de un extremo](traffic-manager-endpoints.md)

[Configuración del método de enrutamiento de conmutación por error](traffic-manager-configure-failover-routing-method.md)

[Configuración del método de enrutamiento round robin](traffic-manager-configure-round-robin-routing-method.md)

[Configuración del método de enrutamiento de rendimiento](traffic-manager-configure-performance-routing-method.md)

[Solución de problemas de estado degradado del Administrador de tráfico](traffic-manager-troubleshooting-degraded.md)

<!---HONumber=AcomDC_1210_2015-->