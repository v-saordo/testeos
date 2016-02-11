<properties 
	pageTitle="Escalado de una aplicación en un entorno del Servicio de aplicaciones" 
	description="Escalado de una aplicación en un entorno del Servicio de aplicaciones" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/14/2016" 
	ms.author="ccompy"/>

# Escalado de aplicaciones en un entorno del Servicio de aplicaciones #

A alto nivel, los entornos del Servicio de aplicaciones son esencialmente implementaciones personales del Servicio de aplicaciones de Azure en la red virtual y solo puede se pueden administrar mediante su suscripción. Ofrecen nuevas capacidades de red porque se encuentran en la red virtual y también se pueden escalar más allá de lo que normalmente está disponible en los entornos del Servicio de aplicaciones de Azure. Si necesita obtener más información sobre lo que es un entorno del Servicio de aplicaciones (ASE), vea [¿Qué es un entorno del Servicio de aplicaciones][WhatisASE]. Para ver detalles sobre la creación de un entorno del Servicio de aplicaciones o una aplicación web en un entorno del Servicio de aplicaciones, vea [Creación de un entorno del Servicio de aplicaciones][HowtoCreateASE] y [Creación de una aplicación web en un entorno del Servicio de aplicaciones][CreateWebappinASE].

Como recordatorio rápido, al cambiar normalmente un atributo de escala para una aplicación web, móvil o de API en el Servicio de aplicaciones de Azure, lo está cambiado en el nivel del plan del Servicio de aplicaciones (ASP). Para obtener detalles sobre el escalado planes del Servicio de aplicaciones o solamente detalles sobre planes del Servicio de aplicaciones fuera de los entornos del Servicio de aplicaciones, vea [Escalado de una aplicación web en el Servicio de aplicaciones de Azure][ScaleWebapp] e [Introducción detallada sobre los planes del Servicio de aplicaciones de Azure][Appserviceplans].

Escalar una aplicación en un entorno del Servicio de aplicaciones es muy parecido a escalar normalmente las aplicaciones. En el Servicio de aplicaciones de Azure hay normalmente tres cosas que puede escalar:

- plan de precios
- tamaño de trabajo (para instancias dedicadas)
- número de instancias.

En un entorno del Servicio de aplicaciones no es necesario seleccionar o cambiar el plan de precios. En términos de capacidades ya está en un nivel de capacidad de precios Premium. En un entorno del Servicio de aplicaciones tampoco hay trabajos compartidos. Son todos trabajos dedicados.

Con respecto a los tamaños de trabajo, el administrador de ASE puede asignar el tamaño del recurso de proceso para que lo use cada grupo de trabajo. Eso significa que puede tener el grupo de trabajo 1 con recursos de proceso P4 y el grupo de trabajo 2 con recursos de proceso P1, si lo desea. No tienen que estar en orden de tamaño. Para obtener información sobre los tamaños y sus precios consulte el documento [Precios de Servicio de aplicaciones de Azure][AppServicePricing]. Esto deja las opciones de escalado para aplicaciones web y planes del Servicio de aplicaciones en un entorno del Servicio de aplicaciones para que sean:

- selección de grupo de trabajo
- número de instancias

El cambio de cualquier elemento se realiza a través de la IU adecuada mostrada con sus planes del Servicio de aplicaciones hospedados en el ASE. No podrá escalar verticalmente el plan del Servicio de aplicaciones por encima de la cantidad de recursos de proceso disponibles en el grupo de trabajo en el que se encuentra dicho plan. Si necesita más, tendrá que agregar más recursos de proceso al grupo de trabajo en el que los necesita a través del administrador de ASE. Para obtener información sobre la reconfiguración de su ASE, lea esta información: [Configuración de un entorno del Servicio de aplicaciones][HowtoConfigureASE]. Puede que también quiera aprovechar las características de escalado automático de ASE para agregar capacidad en función de una programación o unas métricas. Para obtener más detalles sobre la configuración de escalado automático para el propio entorno de ASE, consulte [Escalado automático y entorno del Servicio de aplicaciones][ASEAutoscale].

![][1]

### Escalado del número de instancias ###

Cuando crea la aplicación web en un entorno del Servicio de aplicaciones por primera vez, debe escalarlo hasta al menos 2 instancias para proporcionar tolerancia a errores.

Si su ASE tiene suficiente capacidad, entonces esto es bastante sencillo. Vaya a su plan del Servicio de aplicaciones que contiene los sitios que desea escalar verticalmente y seleccione Escalar. Se abre la interfaz de usuario, donde puede establecer manualmente la escala del ASP o configurar reglas de escalado automático para este. Para escalar manualmente la aplicación, simplemente establezca ***Escalar por*** en ***un recuento de instancias que escribe manualmente***. Desde aquí, arrastre el control deslizante a la cantidad deseada o escríbala en el cuadro situado junto a él.

![][2]

Las reglas de escalado automático de un ASP en un ASE funcionan igual que lo hacen normalmente. Puede seleccionar ***Porcentaje de CPU*** en ***Escalar por*** y crear reglas de escalado automático para el ASP según el porcentaje de CPU, o puede crear reglas más complejas con ***reglas de programación y rendimiento***. Para ver detalles más completos sobre la configuración de escalado automático, use esta guía [Escalado de una aplicación web en el Servicio de aplicaciones de Azure][AppScale].


### Selección del grupo de trabajo ###

Como se indicó anteriormente, a la selección del grupo de trabajo se accede desde la interfaz de usuario de ASP. Abra la hoja del ASP que quiera escalar y seleccione un grupo de trabajo. Verá todos los grupos de trabajo que ha configurado en el entorno del Servicio de aplicaciones. Si solamente tiene un grupo de trabajo, solo se mostrará ese grupo en la lista. Para cambiar el grupo de trabajo en el que se encuentra el ASP, simplemente seleccione el grupo de trabajo al que quiere mover su plan del Servicio de aplicaciones.

![][3]

Antes de mover el ASP de un grupo de trabajo a otro, es importante asegurarse de que tendrá la capacidad adecuada para él. En la lista de grupos de trabajo, no solo se muestra el nombre del grupo de trabajo, sino que también puede ver cuántos trabajos están disponibles en ese grupo de trabajo. Asegúrese de que hay suficientes instancias disponibles para contener su plan de Servicio de aplicaciones. Si necesita más recursos de proceso en el grupo de trabajo al que desea moverse, tendrá que agregarlos a través del administrador de ASE.

> [AZURE.NOTE] Al mover un ASP de un grupo de trabajo se reiniciarán las aplicaciones de ese ASP. Esto puede ocasionar un pequeño tiempo de inactividad en la aplicación dependiendo de lo que tarde en reiniciarse.

## Introducción

Para empezar a trabajar con los entornos del Servicio de aplicaciones, vea [Creación de un entorno del Servicio de aplicaciones][HowtoCreateASE].

Para obtener más información acerca de la plataforma Servicio de aplicaciones de Azure, consulte [Servicio de aplicaciones de Azure][AzureAppService].

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ScaleWebapp]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[CreateWebappinASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-a-web-app-in-an-ase/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[AppScale]: http://azure.microsoft.com/documentation/articles/web-sites-scale/

<!---HONumber=AcomDC_0128_2016-->