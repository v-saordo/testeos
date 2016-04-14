<properties 
	pageTitle="Escalación de una aplicación web en el Servicio de aplicaciones de Azure" 
	description="Aprenda a escalar vertical y horizontalmente una aplicación web en Servicios de aplicaciones de Azure, incluido el escalado automático." 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/25/2016" 
	ms.author="cephalin"/>

# Escalación de una aplicación web en el Servicio de aplicaciones de Azure #

Para aumentar el rendimiento y la capacidad de las aplicaciones web de Microsoft Azure, puede usar el [Portal de Azure](http://portal.azure.com) para escalar el plan del [Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714) del modo **Gratis** al modo **Compartido**, **Básico**, **Estándar** o **Premium**.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

El escalado de las aplicaciones web de Azure implica dos acciones relacionadas: cambiar el modo del plan del Servicio de aplicaciones a un nivel de servicio más alto y configurar ciertas opciones de configuración después de pasar al nivel de servicio superior. En este artículo se incluyen ambos temas. Los niveles de servicio superiores, como los modos **Estándar** y **Premium**, ofrecen una mayor robustez y flexibilidad a la hora de determinar cómo se utilizan los recursos en Azure.

La configuración de escalado tarda solo unos segundos en aplicarse y afecta a todas las aplicaciones web del plan del Servicio de aplicaciones. No requieren que el código se cambie o que las aplicaciones tengan que volver a implementarse.

Para obtener información acerca de los planes de servicio de la aplicación, consulte [¿Qué es un plan de Servicios de aplicaciones?](../app-service/app-service-how-works-readme.md) e [Información general detallada de planes de Servicios de aplicaciones de Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). Para obtener información de los precios y características de planes Servicios de aplicaciones individuales, consulte [Detalles de precios del Servicio de aplicaciones](/pricing/details/web-sites/).

> [AZURE.NOTE] Antes de cambiar una aplicación web del modo **Gratis** al modo **Básico**, **Estándar** o **Premium**, primero debe quitar los límites de gasto vigentes para la suscripción al Servicio de aplicaciones de Azure. Para ver o cambiar opciones para la suscripción a Servicios de aplicaciones de Microsoft Azure, consulte [Suscripciones a Microsoft Azure][azuresubscriptions].

<a name="scalingsharedorbasic"></a>
<!-- ===================================== -->
## Escalado al modo Compartido o Básico
<!-- ===================================== -->

1. En el explorador, abra el [Portal de Azure][portal].
	
2. En la hoja de la aplicación web, haga clic en **Toda la configuración** y en **Escalar verticalmente**.
	
	![Selección del plan][ChooseWHP]
	
4. En la hoja **Elegir el nivel de precios**, seleccione el modo **Compartido** o **Básico** y, a continuación, haga clic en **Seleccionar**.
	
	La ficha **Notificaciones** parpadeará en color verde indicando que se ha completado con **éxito** una vez finalizada la operación.
	
5. En la configuración, haga clic en **Escalar horizontalmente**, seleccione *Recuento de instancias que elijo manualmente* en la lista desplegable, deslice la barra **Instancia** de izquierda a derecha para aumentar el número de instancias y haga clic en **Guardar** en la barra de comandos. La opción del tamaño de instancia no está disponible en el modo **Compartido**. Para obtener más información sobre los tamaños de instancias, consulte [Tamaños de máquinas virtuales y servicios en la nube de Microsoft Azure][vmsizes].
	
	![Tamaño de instancia para el modo básico][ChooseBasicInstances]
	
	La ficha **Notificaciones** parpadeará en color verde indicando que se ha completado con **éxito** una vez finalizada la operación.
	
<a name="scalingstandard"></a>
<!-- ================================= -->
## Escalado al modo Estándar o Premium
<!-- ================================= -->

> [AZURE.NOTE] Antes de cambiar un plan del Servicio de aplicaciones al modo **Estándar** o **Premium**, debe quitar los límites de gasto vigentes para la suscripción al Servicio de aplicaciones de Microsoft Azure. De lo contrario, se arriesga a que su aplicación web no esté disponible si alcanza los límites antes de la finalización del período de facturación. Para ver o cambiar opciones para la suscripción a Servicios de aplicaciones de Microsoft Azure, consulte [Suscripciones a Microsoft Azure][azuresubscriptions].

1. Para escalar al modo **Estándar** o **Premium**, siga los mismos pasos iniciales que al escalar a **Compartido** o **Básico**, a continuación, elija un modo **Estándar** o **Premium** en **Elegir el nivel de precios** y haga clic en **Seleccionar**. 
	
	La pestaña **Notificaciones** parpadeará en color verde indicando que se ha completado con **éxito** una vez finalizada la operación y se activará el **Escalado automático**.
	
	![Reducción horizontal de los modos Estándar o Premium][ScaleStandard]
	
	Puede deslizar la barra **Instancia** para escalar manualmente en más instancias, como en el modo **Básico** como se mostró anteriormente. Sin embargo, aquí aprenderá a escalar automáticamente la aplicación.
	
2. En **Escalar por**, seleccione **Reglas de programación y rendimiento** para escalar automáticamente la aplicación.
	
	![Modo de escalado automático definido en Rendimiento][Autoscale]
	
3. En **Configuración**, haga clic en **Predeterminado, escalar 1-1**, mueva los dos controles deslizantes para definir el número mínimo y máximo de instancias que se van a escalar automáticamente para el plan de Servicios de aplicaciones. Para este tutorial, mueva el control deslizante al máximo, hasta **6** instancias.
	
4. Haga clic en **Aceptar**.
	
4. En **Configuración**, haga clic en **Porcentaje de CPU > 80 (aumentar recuento en 1)** para configurar reglas de escalado automático para la métrica predeterminada.
	
	![Definir conjunto de métricas][SetTargetMetrics]
	
	Puede configurar reglas de escalación automática para las métricas de rendimiento diferentes, incluyendo la CPU, la memoria, la cola de disco, la cola HTTP y el flujo de datos. A continuación, podrá configurar la escalación automática para el porcentaje de CPU que hace lo siguiente:
	
	- Escalado horizontal de 1 instancia si la CPU supera el 80% en los últimos 10 minutos
	- Escalación horizontal de 3 instancias si la CPU supera el 90% en los últimos 5 minutos
	- Escalación vertical de 1 instancia si la CPU es inferior al 50% en los últimos 30 minutos 
	
	
4. Deje el cuadro desplegable **Nombre de métrica** como **Porcentaje de CPU**.
	
5. En **Reglas de escalado vertical**, configure la primera regla. Para ello, establezca el **Operador** en **Mayor que**, el **Umbral** en **70** (%), la **Duración** en **10** (minutos), la **Agregación de tiempo** en Media, la **Acción** en **Aumentar recuento en**, el Valor en **1** (instancia) y el **Tiempo de finalización** en **10** (minutos).
	
	![Definir primera regla de escalado automático][SetFirstRule]
	
	>[AZURE.NOTE] La configuración de **Duración del enfriamiento** especifica el tiempo que tiene que esperar esta regla tras una acción de escalado anterior para volver a escalar.
	
6. Haga clic en **Agregar regla** y configure la segunda regla. Para ello, establezca el **Operador** en **Mayor que**, el **Umbral** en **90** (%), la **Duración** en **1** (minutos), la **Agregación de tiempo** en Media, la **Acción** en **Aumentar recuento en**, el **Valor** en **3** (instancia) y el **Tiempo de finalización** en **1** (minutos).

7. Haga clic en **Aceptar**.
	
	![Definir segunda regla de escalado automático][SetSecondRule]
	
5. En **Configuración**, haga clic en **Agregar regla** y configure la tercera regla. Para ello, establezca el **Operador** en **Menor que**, el **Umbral** en **50** (%), la **Duración** en **30** (minutos), la **Agregación de tiempo** en **Media**, la **Acción** en **Reducir recuento en**, el **Valor** en **1** (instancia) y el **Tiempo de finalización** en **60** (minutos).
	
	![Definir tercera regla de escalado automático][SetThirdRule]
	
7. Haga clic en **Aceptar**. La regla de escalado automático ahora debe reflejarse en la hoja **Configuración de escala**.
	
	![Definir el resultado de la regla de escalado automático][SetRulesFinal]

<a name="ScalingSQLServer"></a>
##Escalación de una base de datos SQL Server conectada a su aplicación web
Si tiene una o más bases de datos de SQL Server vinculadas a la aplicación web (independientemente del modo de plan de Servicios de aplicaciones), puede escalarlas rápidamente en función de sus necesidades.

1. Para escalar una de las bases de datos vinculadas, abra la hoja de la aplicación web en el [Portal de Azure][portal]. En el menú desplegable que se puede contraer **Essentials**, haga clic en el vínculo **Grupo de recursos**. A continuación, en la parte **Resumen** de la hoja del grupo de recursos, haga clic en una de las bases de datos vinculadas.

	![Base de datos vinculada][ResourceGroup]
	
2. En la hoja de base de datos SQL vinculada, haga clic en la parte **Configuración** > **Plan de tarifa**, seleccione uno de los planes según sus requisitos de rendimiento y haga clic en **Seleccionar**.
	
	![Escalación de la base de datos SQL][ScaleDatabase]
	
3. También puede configurar la replicación geográfica para aumentar las capacidades de recuperación ante desastres y de alta disponibilidad de la base de datos SQL. Para ello, haga clic en la parte **Replicación geográfica**.
	
	![Configuración de la replicación geográfica para la base de datos SQL][GeoReplication]

<a name="devfeatures"></a>
## Características del desarrollador
Según el modo de aplicación web, se encuentran disponibles las siguientes características orientadas al desarrollador:

### Valor de bits ###

- Los modos **Básico**, **Estándar** y **Premium** son compatibles con aplicaciones de 32 y 64 bits.
- Los modos de plan **Gratis**y **Compartido** solo son compatibles con aplicaciones de 32 bits.

### Compatibilidad con el depurador ###

- La compatibilidad con el depurador está disponible para los modos **Gratis**, **Compartido** y **Básico** en 1 conexión simultánea por cada plan del Servicio de aplicaciones.
- La compatibilidad con el depurador está disponible para los modos **Estándar** y **Premium** en 5 conexiones simultáneas por cada plan del Servicio de aplicaciones.

<a name="OtherFeatures"></a>
## Otras características

### Supervisión de extremos web ###

- La supervisión de extremos web está disponible en los modos **Básico**, **Estándar** y **Premium**. Para obtener más información sobre la supervisión de extremos web, consulte [Supervisión de aplicaciones web](web-sites-monitor.md).

- Para obtener información detallada sobre todas las características restantes en los planes de Servicios de aplicaciones, incluido el precio y las características de interés para todos los usuarios (incluidos los desarrolladores), consulte [Detalles de precios de Servicios de aplicaciones](/pricing/details/web-sites/).

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

<a name="Next Steps"></a>
## Pasos siguientes

- Para comenzar con Azure, consulte [Evaluación gratuita de Microsoft Azure](/pricing/free-trial/).
- Para obtener información sobre el precio, soporte técnico y contrato de nivel de servicio, consulte los siguientes vínculos.
	
	[Detalles de precios de Transferencias de datos](/pricing/details/data-transfers/)
	
	[Planes de soporte técnico de Azure](/support/plans/)
	
	[Contratos de nivel de servicio](/support/legal/sla/)
	
	[Detalles de precios de Base de datos SQL](/pricing/details/sql-database/)
	
	[Tamaños de máquinas virtuales y servicios en la nube de Microsoft Azure][vmsizes]
	
	[Detalles de precios del Servicio de aplicaciones](/pricing/details/web-sites/)
	
	[Detalles de precios de Servicios de aplicaciones: las conexiones SSL](/pricing/details/web-sites/#ssl-connections)

- Para obtener información sobre las prácticas recomendadas de Servicios de aplicaciones, incluida la creación de una arquitectura resistente y escalable, consulte [Prácticas recomendadas: Aplicaciones web del Servicio de aplicaciones](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).

- Vídeos sobre cómo escalar aplicaciones web:
	
	- [Cuándo escalar sitios web de Azure: con Stefan Schackow](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
	- [Escalación automática de sitios web de Azure mediante CPU o programación: con Stefan Schackow](/documentation/videos/auto-scaling-azure-web-sites/)
	- [Escalación de sitios web de Azure: con Stefan Schackow](/documentation/videos/how-azure-web-sites-scale/)

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- LINKS -->
[vmsizes]: http://go.microsoft.com/fwlink/?LinkId=309169
[SQLaccountsbilling]: http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]: http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ChooseBasicInstances]: ./media/web-sites-scale/scale2InstancesBasic.png
[SaveButton]: ./media/web-sites-scale/05SaveButton.png
[BasicComplete]: ./media/web-sites-scale/06BasicComplete.png
[ScaleStandard]: ./media/web-sites-scale/scale3InstancesStandard.png
[Autoscale]: ./media/web-sites-scale/scale4AutoScale.png
[SetTargetMetrics]: ./media/web-sites-scale/scale5AutoScaleTargetMetrics.png
[SetFirstRule]: ./media/web-sites-scale/scale6AutoScaleFirstRule.png
[SetSecondRule]: ./media/web-sites-scale/scale7AutoScaleSecondRule.png
[SetThirdRule]: ./media/web-sites-scale/scale8AutoScaleThirdRule.png
[SetRulesFinal]: ./media/web-sites-scale/scale9AutoScaleFinal.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
 

<!---HONumber=AcomDC_0302_2016-->