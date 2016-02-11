<properties 
	pageTitle="Configuración de un entorno del Servicio de aplicaciones" 
	description="Configuración, administración y supervisión de entornos del Servicio de aplicaciones" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/04/2016" 
	ms.author="ccompy"/>


# Configuración de un entorno del Servicio de aplicaciones #

## Información general ##

El entorno del Servicio de aplicaciones (ASE) es una capacidad del nivel Premium del Servicio de aplicaciones de Azure que ofrece nuevas funciones de escalado y acceso de red. Esta nueva funcionalidad de escalado permite colocar una instancia del Servicio de aplicaciones de Azure en la red virtual. Esta nueva funcionalidad puede hospedar Aplicaciones web, Aplicaciones móviles y Aplicaciones de API. Las Aplicaciones lógicas todavía no se ejecutan en un entorno del Servicio de aplicaciones.

Si está familiarizado con la funcionalidad del entorno del Servicio de aplicaciones (ASE), lea el documento [¿Qué es un entorno del Servicio de aplicaciones](app-service-app-service-environment-intro.md). Para obtener información sobre la creación de un ASE lea el documento [Creación de un entorno del Servicio de aplicaciones](app-service-web-how-to-create-an-app-service-environment.md).

A un alto nivel, un entorno del Servicio de aplicaciones consta de varios componentes principales:

- Recursos de proceso que se ejecutan en el servicio hospedado del entorno del Servicio de aplicaciones de Azure
- Almacenamiento
- Base de datos
- Una red virtual clásica "v1" con al menos una subred
- Subred con el servicio hospedado del entorno del Servicio de aplicaciones de Azure que se ejecuta en él

Se usan los recursos de proceso para los cuatro grupos de recursos. Cada entorno del Servicio de aplicaciones tiene un conjunto de servidores front-end y tres grupos de trabajo. No es necesario usar los 3 grupos de trabajo y, si lo desea, puede usar solo uno. Los servidores front-end son los puntos de conexión HTTP para las aplicaciones que se mantienen en el ASE. Los trabajadores están donde las aplicaciones que ejecutan realmente. Si se necesita agregar más servidores front-end o más trabajadores dependerá de cómo se realizan las aplicaciones que se colocan en el ASE. Por ejemplo, supongamos que solo tiene una aplicación en el ASE y es una aplicación de tipo Hola a todos que obtiene un gran número de solicitudes. En ese caso sería necesario escalar verticalmente los servidores front-end para controlar la carga HTTP pero en cambio no necesita escalar verticalmente sus trabajos. Intentar controlar todo esto a mano es bastante complicado, especialmente cuando se considera que cada ASE probablemente tiene una mezcla de aplicaciones que se ejecutan en él con distintos criterios de rendimiento. Afortunadamente, hemos agregado suficiente escalado automático a entornos del Servicio de aplicaciones y esto es lo que simplifica considerablemente todo. Para obtener más información sobre el escalado y el escalado automático de los entornos del Servicio de aplicaciones, siga el vínculo de [Configuración del escalado automático en un entorno del Servicio de aplicaciones][ASEAutoscale]

Cada ASE se configura con 500 Gb de almacenamiento. Este espacio se usa en todas las aplicaciones del ASE. Este espacio de almacenamiento es parte del ASE y actualmente no se puede cambiar para usar el espacio de almacenamiento del cliente.

La base de datos contiene la información que define el entorno, así como los detalles de las aplicaciones que se ejecutan en él. También es parte de la suscripción de Azure y no es algo que puedan manipular directamente los clientes.

La red virtual que se usa con el ASE puede ser una de las que realizó al crear el ASE o alguna que ya tenía antes. Si quiere que la red virtual (VNET) se encuentre en un grupo de recursos distinto del usado para el ASE, deberá crearla por separado del flujo de creación del ASE. Es una buena idea crear la subred que quiere usar al mismo tiempo ya que crear la subred durante la creación del ASE lo forzará a que esté en el mismo grupo de recursos que la red virtual. Actualmente solo se admiten las redes virtuales "clásicas" V1.

La interfaz de usuario para administrar y supervisar el entorno del Servicio de aplicaciones está disponible en el Portal de Azure. Si tiene un entorno del Servicio de aplicaciones, es probable que vea el símbolo del Servicio de aplicaciones en la barra lateral. Este símbolo se usa para representar entornos del Servicio de aplicaciones en el Portal de Azure.

![][1]

Puede usar el icono o seleccionar el botón de contenido adicional (símbolo mayor que) en la parte inferior de la barra lateral y seleccionar entornos del Servicio de aplicaciones. Ambos hacen lo mismo, es decir, abren la IU que muestra todos los entornos del Servicio de aplicaciones. Al seleccionar uno de los ASE mostrados, se abre la interfaz de usuario usada para supervisarlo y administrarlo.

![][2]

La primera hoja muestra algunas de las propiedades del ASE junto con un gráfico de métrica por grupo de recursos. Algunas de las propiedades que aparecen en el bloque Essentials también son hipervínculos que abrirán la hoja asociada. Por ejemplo puede seleccionar el nombre de red virtual para abrir la interfaz de usuario asociada a la red virtual en la que se ejecuta el entorno del Servicio de aplicaciones. Los planes del Servicio de aplicaciones y las aplicaciones abren hojas que muestran los elementos que se encuentran en su entorno del Servicio de aplicaciones.

## Supervisión ##

Los gráficos ofrecen la capacidad de ver una variedad de estadísticas de rendimiento de cada grupo de recursos. Para el grupo de servidores front-end tiene mucho sentido supervisar la CPU y la memoria promedio. Los servidores front-end tienen una carga distribuida y al examinar un promedio puede obtener una idea del rendimiento general. Los grupos de trabajo sin embargo no son lo mismo. Varios planes del Servicio de aplicaciones pueden usar los trabajos en un grupo de trabajo. Si ese es el caso, el uso de la CPU y de la memoria no proporcionan mucha información útil. Es más importante realizar el seguimiento de cuántos trabajos usó y están disponibles, sobre todo si va a administrar este sistema para que otros lo usen.

Todas las métricas que se pueden seguir en los gráficos también pueden usarse para configurar alertas. La configuración de alertas funciona de la misma forma que en cualquier otro lugar del Servicio de aplicaciones. Puede establecer una alerta de la parte de la interfaz de usuario de alertas o a partir de la exploración en profundidad de la interfaz de usuario de métricas y de hacer clic en Agregar alerta.
 
![][3]

Las métricas que acabamos de analizar son las métricas el entorno del Servicio de aplicaciones. También hay métricas disponibles en el nivel del Plan del Servicio de aplicaciones (ASP). Aquí es dónde la supervisión de la CPU y de la memoria tiene más sentido. En un ASE, todos los ASP son ASP dedicados. Es decir que las únicas aplicaciones que se ejecutan en los hosts asignados a dicho ASP son las aplicaciones de ese ASP. Para ver los detalles del ASP, solo muéstrelo desde cualquiera de las listas de la interfaz de usuario del ASE o incluso examine los planes del Servicio de aplicaciones que enumeran todos ellos.

Puede que ya esté familiarizado con las funcionalidades del escalado automático que están disponibles para los ASP que están fuera de un ASE y cómo aprovechan las métricas que están disponibles para un recurso. Lo mismo se aplica para el escalado automático de un entorno del Servicio de aplicaciones. No solo puede escalar automáticamente el ASP según las métricas en un ASE, sino que también puede configurar reglas de escalado automático para el mismo ASE. Para obtener información detallada de la configuración del escalado automático, consulte la siguiente guía detallada [Escalado automático en un entorno del Servicio de aplicaciones][ASEAutoscale].


## Propiedades ##

La hoja Configuración se abrirá automáticamente cuando active la hoja ASE. En la parte superior se encuentra Propiedades. Hay una serie de elementos aquí que son redundantes con lo que ve en Essentials, aunque lo que es muy útil es la dirección VIP, así como la dirección IP saliente. Por ahora son lo mismo, pero en el futuro quizá sea posible tener una dirección IP saliente independiente de la dirección VIP, razón por la cual ambos se describen a continuación. Por tanto, si no piensa configurar SSL y agregar una dirección IP solo para una sola aplicación en el ASE, la dirección IP que se establecerá en el DNS para las aplicaciones de su ASE será la dirección VIP que aparece a continuación en Propiedades.

![][4]

## Direcciones IP ##

Aquí es donde puede agregar más direcciones IP para su ASE para que las usen las aplicaciones. Cuando se crea una aplicación en el ASE que desea configurar con SSL de IP, deberá tener una dirección IP que esté reservada solo para esa aplicación. Para hacerlo, el ASE debe tener algunas direcciones IP que se puedan asignar. Cuando se crea un ASE, tiene una dirección con este fin. Si necesita más, vaya aquí y agregar otras. Ahora, antes de la arrastre a la máxima cobertura en caso de que desee alguna vez más, tenga en cuenta que hay un cargo por direcciones IP adicionales. Esta información se puede obtener en la siguiente página de precios: [Precios del Servicio de aplicaciones][AppServicePricing]. Solo tiene que desplazarse a la sección de las conexiones SSL. El precio adicional es el precio de SSL de IP.

**NOTA:** si agrega más direcciones IP, tenga en cuenta que se trata de una operación de escala. Solo puede haber una operación de escala en curso a la vez. Hay tres operaciones de escala:

- cambio del número de direcciones IP en el ASE que están disponibles para el uso de SSL de IP
- cambio de tamaño del recurso de proceso usado en un grupo de recursos
- cambio de la cantidad de recursos de proceso que se usa en un grupo de recursos, bien manualmente o mediante el escalado automático

## Grupos de recursos ##

En Configuración, puede llegar a la interfaz de usuario del grupo de trabajo o al grupo de servidores front-end. Cada una de estas hojas del grupo de recursos ofrece la posibilidad de ver información únicamente acerca de ese grupo de recursos, además de proporcionar controles para escalar completamente ese grupo de recursos.

La hoja base de cada grupo de recursos proporciona un gráfico con métricas de dicho grupo de recursos. Al igual que con los gráficos de la hoja de ASE, puede acceder al gráfico y configurar las alertas como desee. Configurar una alerta desde la hoja del ASE para un grupo de recursos específico es igual que hacerlo desde el grupo de recursos. Desde la hoja de configuración del grupo de trabajo tendrá acceso a la lista de todas las aplicaciones o planes del Servicio de aplicaciones que se ejecutan en este grupo de trabajo.

![][5]

### Escala de la cantidad de recursos de proceso ###

Para proporcionar una mejor perspectiva sobre cómo escalar las aplicaciones en un ASE, aquí podrá consultar la guía: [Escalado de aplicaciones en un entorno del Servicio de aplicaciones](app-service-web-scale-a-web-app-in-an-app-service-environment.md). Si desea más información sobre cómo configurar el escalado automático para los grupos de recursos del ASE, comience aquí: [Escalado automático en un entorno de Servicio de aplicaciones][ASEAutoscale]. Esta descripción detalla las operaciones de escala manual en los grupos de recursos.

Los grupos de recursos, los servidores front-end y los trabajos no son accesibles directamente a los inquilinos. Es decir, no puede usar RDP en ellos, cambiar su aprovisionamiento o actuar como administrador. Son administrados y mantenidos por Azure. Dicho esto, es el usuario quien debe decidir la cantidad y los tamaños de los recursos de proceso.

Existen tres formas de controlar cuántos servidores hay en los grupos de recursos: - Operación de escala de la hoja del ASE principal en la parte superior - Operación de escala manual de la hoja Escala del grupo de recursos individual, que se encuentra en Configuración - Escalado automático que se configura en la hoja Escala del grupo de recursos individual

Para usar la operación de escala en la hoja del ASE basta con hacer clic en ella, arrastrar el control deslizante a la cantidad deseada y guardar. Esta interfaz de usuario también admite el cambio del tamaño.

![][6]

Para usar las capacidades de escalado manual o automático en el grupo de recursos específico, comience en la hoja del ASE y vaya a Configuración. En Configuración abra un grupo de servidores front-end o grupos de trabajo, como desee, y abra el grupo que quiera cambiar. En Configuración, hay un botón de contenido adicional de escala. La hoja que se abre puede usarse para la escala manual o automática.

![][7]

Un entorno del Servicio de aplicaciones se puede configurar para usar hasta 55 recursos de proceso en total. De esos 55 recursos de proceso, solamente 50 se pueden usar para hospedar cargas de trabajo. El motivo de esto es doble. Hay un mínimo de 2 recursos de proceso front-end. Esto deja hasta 53 para admitir asignación de grupos de trabajo. Para proporcionar tolerancia a errores, necesita tener un recurso de proceso adicional asignado según las reglas siguientes:

- cada grupo de trabajo necesita al menos un recurso de proceso adicional que no se puede asignar a cargas de trabajo
- cuando la cantidad de recursos de proceso de un grupo de trabajo supera un determinado valor, se necesita otro recurso de proceso para tolerancia a errores Esto no ocurre en el grupo de servidores front-end.

Dentro de cualquier grupo de trabajo los requisitos de tolerancia a errores son que para un determinado valor de X recursos asignados a un grupo de trabajo:

- Si el valor de X está comprendido entre 2 y 20, la cantidad de recursos de proceso utilizables que puede usar para cargas de trabajo es X-1
- Si el valor de X está comprendido entre 21 y 40, la cantidad de recursos de proceso utilizables que puede usar para cargas de trabajo es X-2
- Si el valor de X está comprendido entre 41 y 53, la cantidad de recursos de proceso utilizables que puede usar para cargas de trabajo es X-3

Recuerde que la configuración mínima tiene dos servidores front-end y dos trabajos. Con las instrucciones anteriores, se muestran a continuación algunos ejemplos que sirven de aclaración.

- Si tiene 30 trabajos en un solo grupo, 28 de ellos pueden usarse para hospedar las cargas de trabajo. 
- Si tiene dos trabajos en un solo grupo, en este caso solo se puede usar uno para hospedar las cargas de trabajo.
- Si tiene 20 trabajos en un solo grupo, en este caso solo se pueden usar 19 para hospedar las cargas de trabajo.  
- Si tiene 21 trabajos en un solo grupo, en este caso solo se pueden usar 19 para hospedar las cargas de trabajo.  

El aspecto de la tolerancia a errores es importante, pero debe tenerlo en cuenta cuando la escala supera determinados umbrales. Si desea agregar más capacidad a partir de 20 instancias, en este caso 22 o un valor superior a 21 no agregará más capacidad. Lo mismo puede decirse por encima de 40, donde el número siguiente que agrega capacidad es 42.

### Escala del tamaño de los recursos de proceso ###

Además de ser capaz de administrar la cantidad de recursos de proceso que se pueden asignar a un grupo determinado, también tiene control sobre el tamaño. Con entornos del Servicio de aplicaciones puede elegir 4 tamaños diferentes (etiqueta P1 a P4). Para obtener detalles sobre los tamaños y sus precios, consulte [Precios del Servicio de aplicaciones](../app-service/app-service-value-prop-what-is.md). Los tamaños de recurso de proceso P1 a P3 son los mismos que los que normalmente están disponibles. El recurso de proceso P4 proporciona 8 núcleos con 14 GB de RAM y solamente está disponible en un entorno del Servicio de aplicaciones.

Si desea cambiar el tamaño de los recursos de proceso que se usan en los grupos, hay dos formas de llevarlo a cabo. Está el comando de escala, disponible en la hoja del ASE y también la hoja de planes de tarifa, a la que accederá en Configuración del grupo de recursos individual.

![][8]

Sin embargo, antes de realizar cambios es importante tener en cuenta unas cuantas cosas:

- los cambios realizados pueden tardar horas en completarse dependiendo de lo grande que sea el cambio solicitado
- cuando ya hay un cambio de configuración en el entorno del Servicio de aplicaciones en curso, no se puede iniciar otro cambio
- si cambia el tamaño de los recursos de proceso usados en un grupo de trabajo, puede provocar interrupciones en las aplicaciones que se ejecutan en ese grupo de trabajo

Agregar instancias adicionales a un grupo de trabajo no es una operación dañina y no conlleva ninguna interrupción de la aplicación para los recursos de ese grupo. Sin embargo, el cambio de tamaño del recurso de proceso usado en un grupo de trabajo es muy diferente. Para evitar tiempos de inactividad en cualquier aplicación durante un cambio de tamaño en un grupo de trabajo, lo mejor es:

- emplear un grupo de trabajo no usado para mostrar las instancias necesarias en el tamaño deseado
- escalar los planes del Servicio de aplicaciones al nuevo grupo de trabajo.  
 
Esto es mucho menos problemático para las aplicaciones que se encuentran en ejecución que cambiar el tamaño de recursos de cálculo con cargas de trabajo en ejecución. Para obtener detalles sobre el escalado de aplicaciones en un entorno del Servicio de aplicaciones, vaya a [Escalado de aplicaciones en un entorno del Servicio de aplicaciones](app-service-web-scale-a-web-app-in-an-app-service-environment.md).

## Red virtual ##

A diferencia del servicio hospedado que contiene el ASE, la [red virtual][virtualnetwork] y la subred están todas bajo el control del usuario. Los entornos del Servicio de aplicaciones tienen unos cuantos requisitos de red, pero el resto depende del usuario. Esos requisitos de ASE son:

- una red virtual clásica "v1" 
- una subred con al menos 8 direcciones 
- la red virtual debe ser una red virtual regional  
 
La administración de la red virtual se realiza a través de la interfaz de usuario de la red virtual o Powershell.

Dado que esta funcionalidad coloca el Servicio de aplicaciones de Azure en la red virtual, significa que las aplicaciones hospedadas en su ASE ahora pueden tener acceso a los recursos disponibles a través de ExpressRoute o VPN de sitio a sitio directamente. Las aplicaciones dentro de los entornos del Servicio de aplicaciones no requieren características de red adicionales para tener acceso a los recursos disponibles a la red virtual que hospeda el entorno del Servicio de aplicaciones. Esto significa que no es necesario usar la integración de red virtual o conexiones híbridas para acceder a los recursos o para conectarse a la red virtual. Sin embargo, puede usar ambas características para acceder a los recursos en redes que no están conectadas a la red virtual. Por ejemplo, puede usar la integración de red virtual para integrarse con una red virtual que está en su suscripción, pero que no está conectada a la red virtual en la que se encuentra su ASE. También puede usar las conexiones híbridas para tener acceso a los recursos de otras redes, de la manera habitual.

Si la red virtual está configurada con una VPN de ExpressRoute, debe tener en cuenta las necesidades de enrutamiento que tenga un ASE. Existen algunas configuraciones de rutas definida por el usuario (UDR) que son incompatibles con un ASE. Para obtener información detallada respecto a la ejecución de un ASE en una red virtual con ExpressRoute, consulte el siguiente documento: [Detalles de configuración de red para entornos del Servicio de aplicaciones con ExpressRoute][ExpressRoute].

También puede controlar ahora el acceso a las aplicaciones mediante grupos de seguridad de red. Esta capacidad le permite bloquear el entorno del Servicio de aplicaciones a solamente las direcciones IP a las que desea restringirlo. Para obtener más información sobre cómo hacerlo, vea el documento [Control del tráfico de entrada en un entorno del Servicio de aplicaciones](app-service-app-service-environment-control-inbound-traffic.md).

## Eliminación de un entorno del Servicio de aplicaciones ##

Si desea eliminar un entorno del Servicio de aplicaciones, simplemente utilice la acción Eliminar que se encuentra en la parte superior de la hoja Entorno del Servicio de aplicaciones. Al hacerlo, se le pedirá que escriba el nombre del entorno del Servicio de aplicaciones para confirmar que realmente quiere hacerlo. NOTA: Cuando se elimina un entorno del Servicio de aplicaciones, se elimina también todo su contenido.

![][9]

## Introducción

Para empezar a trabajar con los entornos del Servicio de aplicaciones, vea [Creación de un entorno del Servicio de aplicaciones](app-service-web-how-to-create-an-app-service-environment.md).

Para obtener más información acerca de la plataforma de Servicio de aplicaciones de Azure, consulte [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!---HONumber=AcomDC_0128_2016-->