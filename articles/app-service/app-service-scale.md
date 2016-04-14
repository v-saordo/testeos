<properties 
	pageTitle="Escala de nivel de precios de Servicio de aplicaciones de Azure" 
	description="Aprenda a escalar aplicaciones web, móviles, de API y lógicas en el Servicio de aplicaciones de Azure, incluido el escalado automático." 
	services="app-service" 
	documentationCenter="" 
	authors="stepsic-microsoft-com" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/09/2016" 
	ms.author="stepsic"/>

# Escala de nivel de precios de Servicio de aplicaciones de Azure

Para aumentar el rendimiento y la capacidad de las aplicaciones de Microsoft Azure, puede usar el [Portal de Azure](https://portal.azure.com/) para escalar el plan del Servicio de aplicaciones de **Gratis** a **Compartido**, **Básico**, **Estándar** o **Premium**.

El nivel de servicio tiene su plan de servicio de la aplicación se basa en el [*nivel de precios* para el plan](/pricing/details/app-service/). Los niveles de servicio superiores, como **Estándar** y **Premium** ofrecen una mayor robustez y flexibilidad a la hora de determinar cómo se utilizan los recursos en Azure. Cambiar el nivel de precios afecta al número de núcleos y a la cantidad de memoria que tiene su servicio, y esto se conoce como *Escalado verticalmente* (o *Reducción vertical*).

Además de escalar verticalmente el plan de tarifa, puede aumentar el número de instancias que tiene el servicio. Esto se conoce como *Reducción vertical* o *Reducción horizontal*. Vea el artículo sobre [Escalación del recuento de instancias de forma manual o automática](../insights-how-to-scale.md) para obtener más información sobre *Reducción vertical* y *Reducción horizontal*.

Para obtener información acerca de los planes de servicio de la aplicación, consulte [¿Qué es un plan de Servicios de aplicaciones?](../web-sites-web-hosting-plan-overview.md) e [Información general detallada de planes de Servicios de aplicaciones de Azure](azure-web-sites-web-hosting-plans-in-depth-overview.md). Para obtener información de los precios y características de planes Servicios de aplicaciones individuales, consulte [Detalles de precios de servicio de aplicación](/pricing/details/app-service/).

Por último, la escalación funciona de una forma distinta si desea usar un [entorno del Servicio de aplicaciones](app-service-app-service-environment-intro.md) dedicado. Consulte [Escalado de aplicaciones web en un entorno del Servicio de aplicaciones](app-service-web-scale-a-web-app-in-an-app-service-environment.md) para obtener más información.

> [AZURE.NOTE] Antes de cambiar del modo **Gratis** al modo** Básico**, **Estándar** o **Premium**, primero debe quitar los límites de gasto vigentes para la suscripción al Servicio de aplicaciones de Azure. Para ver o cambiar opciones para la suscripción a Servicios de aplicaciones de Microsoft Azure, consulte [Suscripciones a Microsoft Azure][azuresubscriptions].

<a name="scalingsharedorbasic"></a> <a name="scalingstandard"></a>

## Escalación del nivel de precios

1. En el explorador, abra el [Portal Azure][portal] y vaya a la aplicación que desee escalar.
	
2. En **Essentials** de la aplicación, haga clic en el vínculo **Plan de Servicio de aplicaciones/nivel de precios**.

3. Haga clic en **Nivel de precios** y verá la lista de niveles de servicio posibles para el plan. Cada nivel viene acompañado de un precio estimado para hacerse una idea del coste medio de ese nivel.
	
	![Selección del plan](./media/app-service-scale/ChoosePricingTier.png)
	
4. Una vez elegido el nivel, haga clic en **Seleccionar**.
	
	La ficha **Notificaciones** parpadeará en color verde indicando que se ha completado con **éxito** una vez finalizada la operación.
 
También puede conocer los distintos niveles de procesamiento de Azure [aquí](http://go.microsoft.com/fwlink/?LinkId=309169).
	
<a name="ScalingSQLServer"></a>
##Escalación de recursos relacionados
Si su aplicación depende de otros servicios, como SQL o Almacenamiento, también puede escalarlos según sus necesidades.

1. En **Essentials**, haga clic en el vínculo **Grupo de recursos**.

2. A continuación, en la parte **Resumen** de la hoja del grupo de recursos, haga clic en una de las bases de datos (o en otro recurso que desee escalar).

	![Base de datos vinculada](./media/app-service-scale/ResourceGroup.png)
	
3. En la hoja de recursos vinculada, haga clic en la parte **Nivel de precios**, seleccione uno de los niveles según sus requisitos de rendimiento y haga clic en **Seleccionar**.
	
	![Escalación de la base de datos SQL](./media/app-service-scale/ScaleDatabase.png)
	
4. Si su aplicación usa Almacenamiento, la replicación geográfica se configura automáticamente cuando se elige un nivel de precios que lo admite. Para SQL, por otra parte, tiene que configurar manualmente la replicación geográfica para aumentar las capacidades de recuperación ante desastres y de alta disponibilidad de la base de datos SQL. Para ello, haga clic en la parte **Replicación geográfica**.
	
	![Configuración de la replicación geográfica para la base de datos SQL](./media/app-service-scale/GeoReplication.png)
	
<a name="devfeatures"></a>
## Características del desarrollador
Según el nivel de precios, se encuentran disponibles las siguientes características orientadas al desarrollador:

### Valor de bits ###

- Los modos **Básico**, **Estándar** y **Premium** son compatibles con aplicaciones de 32 y 64 bits.
- El nivel de plan **Gratis** y **Compartido** solo son compatibles con aplicaciones de 32 bits.

### Compatibilidad con el depurador ###

- La compatibilidad con el depurador está disponible para los modos **Gratis**, **Compartido** y **Básico** en 1 conexión simultánea por cada plan del Servicio de aplicaciones.
- La compatibilidad con el depurador está disponible para los modos **Estándar** y **Premium** en 5 conexiones simultáneas por cada plan del Servicio de aplicaciones.

<a name="OtherFeatures"></a>
## Otras características

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
	
	[Detalles de precios del Servicio de aplicaciones](/pricing/details/app-service/)
	
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
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
 

<!---HONumber=AcomDC_0128_2016-->