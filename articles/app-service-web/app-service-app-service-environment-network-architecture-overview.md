<properties 
	pageTitle="Información general sobre la arquitectura de red de los entornos del Servicio de aplicaciones" 
	description="Introducción a la arquitectura de la topología de red de los entornos del Servicio de aplicaciones." 
	services="app-service" 
	documentationCenter="" 
	authors="stefsch" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/26/2016" 
	ms.author="stefsch"/>

# Información general sobre la arquitectura de red de los entornos del Servicio de aplicaciones

## Introducción ##
Los entornos de Servicio de aplicaciones siempre se crean dentro de una subred de una [red virtual][virtualnetwork]; las aplicaciones que se ejecutan en un entorno de Servicio de aplicaciones pueden comunicarse con extremos privados ubicados dentro de la misma topología de red virtual. Puesto que los clientes pueden bloquear partes de su infraestructura de red virtual, es importante conocer los tipos de flujos de comunicación de red que se producen con un entorno del Servicio de aplicaciones.

## Flujo de red general ##
 
Un entorno del Servicio de aplicaciones siempre tiene una dirección IP virtual (VIP) pública. Todo el tráfico entrante llega a esa VIP pública, incluido el tráfico HTTP y HTTPS para las aplicaciones, así como otro tráfico de FTP, la funcionalidad de depuración remota y las operaciones de administración de Azure. Para obtener una lista completa de los puertos específicos (necesarios y opcionales) que están disponibles en la VIP pública, consulte el artículo sobre el [control del tráfico entrante][controllinginboundtraffic] en un entorno del Servicio de aplicaciones.

En el diagrama siguiente se ofrece información general sobre los distintos flujos de red entrantes y salientes:

![Flujos de red generales][GeneralNetworkFlows]

Un entorno del Servicio de aplicaciones puede comunicarse con una variedad de extremos privados del cliente. Por ejemplo, las aplicaciones que se ejecutan en el entorno del Servicio de aplicaciones pueden conectarse a servidores de base de datos que se ejecutan en máquinas virtuales de IaaS en la misma topología de red virtual.

>[AZURE.IMPORTANT] Si examinamos el diagrama de red, los "Otros recursos de equipo" se implementan en una subred diferente de la del entorno del Servicio de aplicaciones. Al implementar recursos en la misma subred que el entorno del Servicio de aplicaciones (ASE), se bloqueará la conectividad del ASE a esos recursos (excepto para el enrutamiento específico dentro del entorno del Servicio de aplicaciones). Implemente en su lugar en una subred diferente (en la misma red virtual). A continuación, el entorno del Servicio de aplicaciones podrá conectarse. No se necesita ninguna configuración adicional.

Los entornos del Servicio de aplicaciones también se comunican con la base de datos SQL y los recursos del Almacenamiento de Azure necesarios para administrar y operar un entorno del Servicio de aplicaciones. Algunos recursos de almacenamiento y SQL con los que se comunica un entorno del Servicio de aplicaciones se encuentran en la misma región que el entorno del Servicio de aplicaciones, mientras que otros se encuentran en regiones de Azure remotas. Como resultado, la conectividad saliente a Internet siempre es necesaria para que un entorno del Servicio de aplicaciones funcione correctamente.

Puesto que un entorno del Servicio de aplicaciones se implementa en una subred, los grupos de seguridad de red pueden usarse para controlar el tráfico entrante a la subred. Para obtener información detallada sobre cómo controlar el tráfico entrante a un entorno del Servicio de aplicaciones, consulte el siguiente [artículo][controllinginboundtraffic].

Para obtener información detallada sobre cómo permitir la conectividad saliente de Internet desde un entorno del Servicio de aplicaciones, consulte el artículo siguiente sobre cómo trabajar con [Express Route][ExpressRoute]. El mismo enfoque que se describe en este artículo se aplica al trabajar con conectividad de sitio a sitio y al usar la tunelización forzada.

## Direcciones de red de salida ##
Cuando un entorno del Servicio de aplicaciones realiza las llamadas salientes, una dirección IP siempre se asocia con las llamadas salientes. La dirección IP específica que se usa depende de si el extremo al que se llama se encuentra dentro de la topología de red virtual o fuera de ella.

Si el extremo al que se llama está **fuera** de la topología de red virtual, entonces la dirección saliente (también conocida como la dirección NAT saliente) que se utiliza es la VIP pública del entorno del Servicio de aplicaciones. Esta dirección se encuentra en la interfaz de usuario del portal para el entorno de Servicio de aplicaciones en la hoja Propiedades.
 
![Dirección IP saliente][OutboundIPAddress]

Esta dirección también se puede determinar si se crea una aplicación en el entorno de Servicio de aplicaciones y después se realiza una *nslookup* en la dirección de la aplicación. La dirección IP resultante es tanto la VIP pública como la dirección NAT saliente del entorno del Servicio de aplicaciones.

Si el extremo al que se llama está **dentro** de la topología de red virtual, la dirección saliente de la aplicación de llamada será la dirección IP interna del recurso de proceso individual que ejecuta la aplicación. Sin embargo, no hay una asignación persistente de direcciones IP internas de red virtual a las aplicaciones. Las aplicaciones pueden moverse a través de recursos informáticos diferentes y el grupo de recursos informáticos disponibles en un entorno del Servicio de aplicaciones puede cambiar debido a las operaciones de ajuste de escala.

Sin embargo, puesto que un entorno del Servicio de aplicaciones siempre se encuentra dentro de una subred, se garantiza que la dirección IP interna de un recurso informático que ejecuta una aplicación siempre se queda dentro del intervalo CIDR de la subred. Como resultado, cuando las ACL específicas o los grupos de seguridad de red se utilizan para proteger el acceso a otros extremos en la red virtual, el intervalo de subred que contiene el entorno del Servicio de aplicaciones necesita tener acceso.

En el diagrama siguiente se explican estos conceptos de manera detallada:

![Direcciones de red de salida][OutboundNetworkAddresses]

En el diagrama anterior:

- Dado que la VIP pública del entorno del Servicio de aplicaciones es 192.23.1.2, que es la dirección IP saliente que se utiliza cuando se realizan llamadas a los extremos de "Internet".
- El intervalo CIDR de la subred que contiene para el entorno del Servicio de aplicaciones es 10.0.1.0/26. Otros extremos dentro de la misma infraestructura de red virtual verán las llamadas de aplicaciones como originadas en algún lugar dentro de este intervalo de direcciones.

## Llamadas entre entornos del Servicio de aplicaciones ##
El escenario puede resultar más complejo si implementa varios entornos del Servicio de aplicaciones en la misma red virtual y realiza llamadas salientes desde un entorno del Servicio de aplicaciones a otro. Estos tipos de llamadas cruzadas entre entornos del Servicio de aplicaciones también se tratarán como llamadas de "Internet".

En el siguiente diagrama, se muestra un ejemplo de arquitectura en capas con aplicaciones en un entorno de Servicio de aplicaciones (p. ej., aplicaciones web de "entrada principal") que llaman a aplicaciones en un segundo entorno de Servicio de aplicaciones (por ejemplo, aplicaciones de API de back-end internas que no se crearon para que sean accesibles desde Internet).

![Llamadas entre entornos del Servicio de aplicaciones][CallsBetweenAppServiceEnvironments]

En el ejemplo anterior, el entorno de Servicio de aplicaciones "ASE One" tiene la dirección IP saliente 192.23.1.2. Si una aplicación que se ejecuta en este entorno de Servicio de aplicaciones hace una llamada saliente a una aplicación que se ejecuta en un segundo entorno de Servicio de aplicaciones ("ASE Two") ubicado en la misma red virtual, la llamada saliente se tratará como una llamada de "Internet". Como resultado, el tráfico de red que llega al segundo entorno de Servicio de aplicaciones se mostrará como originado en 192.23.1.2 (es decir, no el intervalo de direcciones de subred del primer entorno de Servicio de aplicaciones).

Aunque las llamadas entre diferentes entornos de Servicio de aplicaciones se tratan como llamadas de "Internet", cuando ambos entornos de Servicio de aplicaciones se encuentran en la misma región de Azure, el tráfico de red permanecerá en la red regional de Azure y no pasará físicamente a través de la Internet pública. Como resultado, puede usar un grupo de seguridad de red en la subred del segundo entorno de Servicio de aplicaciones para permitir solo las llamadas entrantes desde el primer entorno de Servicio de aplicaciones (cuya dirección IP saliente es 192.23.1.2), lo que garantiza una comunicación segura entre los entornos de Servicio de aplicaciones.

## Información y vínculos adicionales ##
Se puede obtener más información sobre los puertos de entrada usados por los entornos de Servicio de aplicaciones y sobre el uso de grupos de seguridad de red para controlar el tráfico entrante [aquí][controllinginboundtraffic].

Los detalles sobre el uso de rutas definidas por el usuario para conceder acceso saliente a Internet a los entornos del Servicio de aplicaciones están disponibles en este [artículo][ExpressRoute].


<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

<!---HONumber=AcomDC_0302_2016-->