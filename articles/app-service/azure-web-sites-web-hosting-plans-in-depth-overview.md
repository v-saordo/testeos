<properties 
	pageTitle="Introducción detallada sobre los planes del Servicio de aplicaciones de Azure" 
	description="Obtenga información acerca de cómo funcionan los planes del Servicio de aplicaciones de Azure y cómo benefician a su experiencia de administración." 
	keywords="servicio de aplicaciones, servicio de aplicaciones de azure, escala, escalable, plan del servicio de aplicaciones, costo del servicio de aplicaciones"
	services="app-service" 
	documentationCenter="" 
	authors="btardif" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2015" 
	ms.author="byvinyal"/>

#Introducción detallada sobre los planes del Servicio de aplicaciones de Azure#

Un plan del **Servicio de aplicaciones** representa un conjunto de características y capacidades que puede compartir entre múltiples aplicaciones del [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714), incluidas las aplicaciones web, aplicaciones móviles, aplicaciones lógicas o aplicaciones de API. Estos planes admiten 5 niveles de precios (**Gratis**, **Compartido**, **Básico**, **Estándar** y **Premium**), donde cada uno de ellos tiene sus propias funcionalidades y capacidad. Las aplicaciones de una misma suscripción y ubicación geográfica pueden compartir un plan. Todas las aplicaciones que comparten un plan pueden aprovechar todas las capacidades y características definidas por el nivel de dicho plan. Todas las aplicaciones asociadas a un plan dado se ejecutan en los recursos definidos por dicho plan. Por ejemplo, si el plan está configurado para usar dos instancias "pequeñas" en el nivel de servicio estándar, todas las aplicaciones asociadas a este plan se ejecutarán en ambas instancias con la funcionalidad de dicho nivel de servicio. Las instancias del plan en las que se ejecutan las aplicaciones están totalmente administradas y tienen una alta disponibilidad.

En este artículo exploraremos las características clave, como el nivel y la escala de un plan del Servicio de aplicaciones y el papel que juegan mientras administra sus aplicaciones.

##Aplicaciones y planes del Servicio de aplicaciones

Una aplicación del Servicio de aplicaciones se puede asociar a un solo plan de dicho servicio en un momento determinado.

Tanto las aplicaciones como los planes se incluyen en un grupo de recursos. Un grupo de recursos sirve como límite del ciclo de vida de cada uno de los recursos que contiene. Los grupos de recursos le permiten administrar todos los componentes de una aplicación conjuntamente.

La capacidad de tener varios planes del Servicio de aplicaciones en un solo grupo de recursos permite asignar diferentes aplicaciones a distintos recursos físicos. Esto permite, por ejemplo, la separación de los recursos entre entornos de desarrollo, prueba y producción. Un posible escenario se daría si desea asignar un plan con su propio conjunto de recursos dedicado para las aplicaciones de producción y un segundo plan para los entornos de desarrollo y prueba. De esta forma, las pruebas de carga de una nueva versión de las aplicaciones no competirán por los mismos recursos que las aplicaciones de producción, que prestan servicio a clientes reales.

Al tener varios planes en un solo grupo de recursos, también puede definir una aplicación que se extienda entre regiones. Por ejemplo, una aplicación de alta disponibilidad que se ejecute en dos regiones incluirá dos planes, uno por cada región, y una aplicación asociada a cada plan. En tal situación, todas las copias de la aplicación se asociarán a un solo grupo de recursos. Al tener una sola vista de un grupo de recursos con varios planes y aplicaciones, la administración, el control y la visión del estado de la aplicación resultan más sencillos.

## Creación de un nuevo plan del Servicio de aplicaciones frente al uso de un plan existente

Al crear una nueva aplicación, debe pensar en crear un nuevo grupo de recursos cuando dicha aplicación representa un proyecto nuevo. En este caso, crear un nuevo grupo de recursos, un plan y una aplicación es la elección correcta.

Si la aplicación que está a punto de crear es un componente de otra aplicación más grande, esta aplicación web debería crearse dentro del grupo de recursos asignado a dicha aplicación de mayor tamaño.

Independientemente de que la nueva aplicación sea totalmente nueva o bien sea parte de otra más grande, puede aprovechar un plan existente del Servicio de aplicaciones para hospedarla o crear uno nuevo. Es más bien una cuestión de capacidad y de la carga esperada. Si la nueva aplicación va a utilizar muchos recursos y sus factores de escala son distintos a los del resto de aplicaciones hospedadas en un plan existente, se recomienda aislarla en su propio plan.

La creación de un nuevo plan permite asignar un nuevo conjunto de recursos para la aplicación web y le proporciona un mayor control sobre la asignación de recursos, ya que cada plan obtiene su propio conjunto de instancias.
 
Al tener capacidad para mover aplicaciones entre los planes, también puede cambiar la forma en que los recursos se asignan en la aplicación de mayor tamaño.
 
Por último, si desea crear una nueva aplicación en otra región distinta y dicha región no tiene un plan existente, tendrá que crear un nuevo plan en esta región para poder hospedar la aplicación en ella.

## Creación de un plan del Servicio de aplicaciones

No se puede crear un plan de Servicio de aplicaciones. Sin embargo, puede crear un nuevo plan explícitamente durante la creación de la aplicación.

Para ello, en el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715), haga clic en **NUEVO**, seleccione **Web y móvil** y luego elija **Aplicaciones web**, **Aplicaciones móviles**, **Aplicaciones lógicas** o **Aplicaciones de API**. ![][createWebApp]

A continuación, puede seleccionar o crear el plan del Servicio de aplicaciones para la nueva aplicación.
  
 ![][createASP]

Para crear un nuevo plan de Servicio de aplicaciones, haga clic en **+ Crear nuevo**, escriba el nombre del **plan del Servicio de aplicaciones** y seleccione una **ubicación** adecuada. Haga clic en el **Plan de tarifa** y seleccione un plan de tarifa adecuado para el servicio. Seleccione **Ver todos** para ver más opciones de precios, como **Gratis** y **Compartido**. Una vez haya seleccionado el plan de tarifa, haga clic en el botón **Seleccionar**.
 
## Cambio de una aplicación a un plan del Servicio de aplicaciones diferente

Puede mover una aplicación a un plan distinto del Servicio de aplicaciones en el [Portal de Azure](https://portal.azure.com). Las aplicaciones pueden moverse entre planes de la misma región geográfica.

Para mover una aplicación a otro plan, navegue hasta la aplicación que desea mover y, a continuación, haga clic en **Cambiar plan del Servicio de aplicaciones**.
 
De esta forma se abrirá la hoja del plan del Servicio de aplicaciones. Llegados a este punto, puede elegir un plan existente o bien crear uno nuevo. Los planes de una ubicación geográfica diferente aparecen atenuados y no se pueden seleccionar.

![][change]

Tenga en cuenta que cada plan tiene su propio nivel de precios. Cuando mueve un sitio de un nivel **Gratis** a un nivel **Estándar**, la aplicación podrá aprovechar todas las características y recursos del nivel **Estándar**.

## Escalación de un plan del Servicio de aplicaciones

Existen tres formas de escalar un plan:

- Cambie el **nivel de precios** del plan. Por ejemplo, un plan del nivel **Básico** puede convertirse al nivel **Estándar** o **Premium** y todas las aplicaciones asociadas a dicho plan podrán aprovechar las características que se ofrecen en el nuevo nivel de servicio.
- Cambiar el **tamaño de instancia** de un plan. Por ejemplo, un plan de nivel **Básico** que use instancias **pequeñas** puede cambiarse para usar instancias **grandes**. Todas las aplicaciones asociadas a dicho plan podrán aprovechar la memoria y los recursos de CPU adicionales que ofrece el tamaño de instancia más grande.
- Cambio del **recuento de instancias** del plan. Por ejemplo, un plan **Estándar** escalado a 3 instancias puede escalarse hasta 10 instancias y un plan **Premium** (vista previa) puede escalarse hasta 20 instancias (con algunas restricciones). Todas las aplicaciones asociadas a dicho plan podrán aprovechar la memoria y los recursos de CPU adicionales que ofrece el mayor recuento de instancias.

En la imagen siguiente se ven las hojas **Plan del Servicio de aplicaciones** y **Nivel de precios**. Al hacer clic en la parte **Nivel de precios** de la hoja **Plan del Servicio de aplicaciones** se expande la hoja **Nivel de precios**, donde puede cambiar el nivel de precios y el tamaño de instancia del plan.
 
 ![][pricingtier]

##Resumen

Los planes del Servicio de aplicaciones representan un conjunto de características y capacidades que puede compartir entre las aplicaciones. Los planes del Servicio de aplicaciones proporcionan la flexibilidad necesaria para asignar aplicaciones específicas a un conjunto de recursos determinado y optimizar aún más el uso de los recursos de Azure. De esta forma, si desea ahorrar gastos en el entorno de prueba, puede compartir un plan entre varias aplicaciones. Asimismo, puede escalarlo entre varias regiones y planes para maximizar el rendimiento del entorno de producción.

## Lo que ha cambiado

* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
   
[pricingtier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/appserviceplan-pricingtier.png
[assign]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/assing-appserviceplan.png
[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png

<!---HONumber=AcomDC_0128_2016-->