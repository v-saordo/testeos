<properties
	pageTitle="Creación de una aplicación web en un entorno del Servicio de aplicaciones"
	description="Obtenga información acerca de cómo crear aplicaciones web y planes del Servicio de aplicaciones en un entorno del Servicio de aplicaciones"
	services="app-service"
	documentationCenter=""
	authors="ccompy"
	manager="stefsch"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="01/14/2016"
	ms.author="ccompy"/>

# Creación de una aplicación web en un entorno del Servicio de aplicaciones

## Información general

Este tutorial muestra cómo crear aplicaciones web y planes del Servicio de aplicaciones en un [entorno del Servicio de aplicaciones](app-service-app-service-environment-intro.md) (ASE).

> [AZURE.NOTE] Si quiere obtener información sobre cómo crear una aplicación web pero no necesita hacerlo en un entorno del Servicio de aplicaciones, consulte [Creación de una aplicación web de .NET](web-sites-dotnet-get-started.md) o uno de los tutoriales relacionados para otros lenguajes y marcos.

## Requisitos previos

En este tutorial se supone que ha creado un entorno del Servicio de aplicaciones. Si no es así, consulte [Creación de un entorno del Servicio de aplicaciones](app-service-web-how-to-create-an-app-service-environment.md).

## Creación de una aplicación web

1. En el [Portal de Azure](https://portal.azure.com/), haga clic en **Nuevo > Web y móvil > Aplicación web**. 

	![][1]

2. Seleccione su suscripción.

	Si tiene varias suscripciones, tenga en cuenta que para crear una aplicación en el entorno del Servicio de aplicaciones, debe usar la misma suscripción que usó para crear el entorno.

3. Seleccione o cree un grupo de recursos.

	Los *grupos de recursos* le permiten administrar los recursos de Azure relacionados como una unidad y resultan útiles al definir las reglas del *control de acceso basado en rol* (RBAC) para las aplicaciones. Para obtener más información, consulte [Administración de los recursos de Azure][ResourceGroups].

4. Seleccione o cree un plan del Servicio de aplicaciones.

	Los *planes del Servicio de aplicaciones* son conjuntos administrados de aplicaciones web. Cuando se selecciona el precio, el precio que se cobra se aplica al plan del Servicio de aplicaciones y no a las aplicaciones individuales. Para escalar verticalmente el número de instancias de una aplicación web, escale verticalmente las instancias de su plan del Servicio de aplicaciones. Esto afecta a todas las aplicaciones web de ese plan. Algunas características como las ranuras de sitio o la integración de la red virtual también tienen restricciones de cantidad dentro del plan. Para obtener más información, consulte [Información general sobre los planes del Servicio de aplicaciones de Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)

	Puede identificar los planes del Servicio de aplicaciones de su ASE con solo mirar la ubicación que se indica bajo el nombre del plan.

	![][5]

	Si quiere usar un plan del Servicio de aplicaciones que ya existe en el entorno del Servicio de aplicaciones, seleccione ese plan. Si quiere crear un nuevo plan del Servicio de aplicaciones, consulte la sección siguiente de este tutorial, [Creación de un plan de servicio de aplicaciones en un entorno del Servicio de aplicaciones](#createplan).

5. Escriba el nombre de la aplicación web y luego haga clic en **Crear**.

	El nombre de la aplicación web debe ser único en el Servicio de aplicaciones de Azure. Esto significa que si quiere crear una aplicación web llamada "thisismywebapp", entonces no puede haber actualmente ninguna otra aplicación web llamada "thisismywebapp" en el Servicio de aplicaciones de Azure.

	La dirección URL de una aplicación web en un ASE es:

	[*nombre del sitio*].[*nombre del entorno del Servicio de aplicaciones*].p.azurewebsites.net

	en lugar de

	[*nombre del sitio*].azurewebsites.net

## <a name="createplan"></a> Creación de un plan del Servicio de aplicaciones

Cuando crea un plan del Servicio de aplicaciones en un entorno del Servicio de aplicaciones, sus opciones de trabajo son diferentes dado que no hay trabajos compartidos en un ASE. Los trabajos que tiene que usar son los que el administrador ha asignado al ASE. Esto significa que para crear un nuevo plan, el número de trabajos asignados al grupo de trabajo del ASE debe ser superior al número total de instancias en todos los planes que ya existen en ese grupo de trabajo. Si no tiene suficientes trabajos en su grupo de trabajo del ASE para crear su plan, deberá trabajar con el administrador del ASE para agregarlos.

Otra diferencia con los planes del Servicio de aplicaciones hospedados en un entorno del Servicio de aplicaciones es la ausencia de selección de precios. Cuando tiene un entorno del Servicio de aplicaciones, paga por los recursos de proceso que usa el sistema y no se le cobra adicionalmente por los planes de ese entorno. Normalmente, cuando crea un plan del Servicio de aplicaciones, selecciona el plan de precios que determina su facturación. Un entorno del Servicio de aplicaciones es esencialmente una ubicación privada donde puede crear contenido. Se paga por el entorno y no pro hospedar el contenido.

Las siguientes instrucciones muestran cómo crear un plan del Servicio de aplicaciones mientras crea una aplicación web, como se explica en la sección anterior de este tutorial.

1. Haga clic en **Crear nuevo** en la interfaz de usuario de selección del plan y proporcione un nombre para el plan, tal como haría normalmente fuera de un ASE.

2. Seleccione el ASE que quiere usar con su selector de ubicación.

	Como un entorno del Servicio de aplicaciones es básicamente una ubicación de implementación privada, se muestra en Ubicación.

	![][2]

	Después de seleccionar un ASE en el selector de ubicación, se actualiza la interfaz de usuario de creación del plan del Servicio de aplicaciones. La ubicación muestra ahora el nombre del sistema ASE y la región en la que está, y el selector de plan de precios se sustituye por un selector de grupos de trabajo.

	![][3]

### Selección de un grupo de trabajo

Normalmente, en el Servicio de aplicaciones de Azure y fuera de un entorno del Servicio de aplicaciones, hay tres tamaños de proceso disponibles con la selección de un plan de precios dedicado. De igual forma, puede definir hasta tres grupos de trabajo para un ASE y especificar el tamaño de proceso que se usa para ese grupo de trabajo. Para los inquilinos del ASE, eso significa que en lugar de seleccionar un plan de precios con el tamaño de proceso de su plan del Servicio de aplicaciones, se selecciona lo que se conoce como un *grupo de trabajo*.

La interfaz de usuario de selección del grupo de trabajo muestra el tamaño de proceso usado para ese grupo de trabajo debajo del nombre. La cantidad disponible se refiere al número de instancias de proceso que están disponibles para usarse en ese grupo. El grupo total puede tener en realidad un número de instancias superior a este, pero este valor se refiere simplemente al número que no está en uso. Si necesita ajustar el entorno del Servicio de aplicaciones para agregar más recursos de proceso, consulte [Configuración del entorno del Servicio de aplicaciones](app-service-web-configure-an-app-service-environment.md).

![][4]

En este ejemplo puede ver que solo hay dos grupos de trabajo disponibles. Eso se debe a que el administrador de ASE solamente asigna hosts en esos dos grupos de trabajo. El tercero se mostraría cuando haya máquinas virtuales asignadas a él.

## Después de la creación de la aplicación web

Existen algunas consideraciones que se deben tener en cuenta para ejecutar aplicaciones web y administrar planes del Servicio de aplicaciones en un ASE.

Como se indicó anteriormente, el propietario del ASE es responsable del tamaño del sistema y, por tanto, también es responsable de garantizar que haya capacidad suficiente para hospedar los planes del Servicio de aplicaciones deseado. Si no hay trabajos disponibles, no podrá crear su plan del Servicio de aplicaciones. Esto también se cumple para escalar verticalmente la aplicación web. Si necesita más instancias, el administrador del entorno del Servicio de aplicaciones debe agregar más trabajos.

Después de crear la aplicación web y el plan del Servicio de aplicaciones, es una buena idea para escalarlos verticalmente. En un ASE siempre debe haber al menos dos instancias del plan del Servicio de aplicaciones para proporcionar tolerancia a errores en las aplicaciones. El escalado de un plan del Servicio de aplicaciones en un ASE es igual que mediante la interfaz de usuario. Para obtener más información sobre el escalado, consulte [Escalado de una aplicación web en un entorno del Servicio de aplicaciones](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment
[ResourceGroups]: http://azure.microsoft.com/documentation/articles/resource-group-portal/
[AzurePowershell]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/

<!---HONumber=AcomDC_0302_2016-->