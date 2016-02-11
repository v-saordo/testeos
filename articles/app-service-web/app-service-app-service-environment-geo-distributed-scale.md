<properties 
	pageTitle="Escala distribuida geográficamente con entornos de Servicio de aplicaciones" 
	description="Aprenda a escalar aplicaciones horizontalmente con distribución geográfica con el Administrador de tráfico y los entornos de Servicio de aplicaciones." 
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
	ms.date="01/09/2016" 
	ms.author="stefsch"/>

# Escala distribuida geográficamente con entornos de Servicio de aplicaciones

## Información general ##
Aquellos escenarios de aplicaciones que requieren una escala muy elevada pueden superar la capacidad de recursos de proceso disponible para una sola implementación de una aplicación. Algunos ejemplos de los escenarios que requieren una escala extremadamente alta son las aplicaciones de votación, los acontecimientos deportivos y los espectáculos televisados. Se pueden cumplir los requisitos de gran escala mediante el escalado horizontal de las aplicaciones, con la realización de varias implementaciones de la aplicación en una única región, así como entre regiones, para afrontar los requisitos extremos de carga.

Los entornos de Servicio de aplicaciones son una plataforma ideal para el escalado horizontal. Una vez seleccionado una configuración de entorno de Servicio de aplicaciones que admita una tasa de solicitudes conocida, los desarrolladores pueden implementar más entornos de Servicio de aplicaciones a modo de "plantilla" para lograr una capacidad de carga máxima deseada.

Por ejemplo, imagine una aplicación que se ejecuta en una configuración de entorno de Servicio de aplicaciones y en la que se probó el procesamiento de 20.000 solicitudes por segundo (RPS). Si la capacidad de carga máxima deseada es 100.000 RPS, se pueden crear y configurar cinco (5) entornos de Servicio de aplicaciones para asegurarse de que la aplicación pueda afrontar la carga máxima prevista.

Puesto que los clientes suelen acceder a las aplicaciones mediante un dominio personalizado (o mnemónico), los desarrolladores necesitan una forma de distribuir las solicitudes de aplicaciones a todas las instancias del entorno de Servicio de aplicaciones. Una excelente manera de lograr esto es resolver el dominio personalizado con un [perfil del Administrador de tráfico de Azure][AzureTrafficManagerProfile]. El perfil del Administrador de tráfico puede configurarse para que apunte a todos los entornos de Servicio de aplicaciones individuales. El Administrador de tráfico controlará automáticamente la distribución de los clientes por todos los entornos de Servicio de aplicaciones en función de la configuración de equilibrio de carga en el perfil del Administrador de tráfico. Este enfoque funciona independientemente de si todos los entornos de Servicio de aplicaciones están implementados en una sola región de Azure o en varias regiones de Azure del mundo.

Además, como los clientes acceden a las aplicaciones a través del dominio mnemónico, desconocen el número de entornos de Servicio de aplicaciones que ejecutan una aplicación. Como resultado, los desarrolladores pueden agregar y quitar rápida y fácilmente entornos de Servicio de aplicaciones según la carga de tráfico observada.

En el siguiente diagrama conceptual, se muestra una aplicación escalada horizontalmente en tres entornos de Servicio de aplicaciones en una única región.

![Arquitectura conceptual][ConceptualArchitecture]

El resto de este tema lo guía por los pasos necesarios para configurar una topología distribuida para la aplicación de ejemplo usando varios entornos de Servicio de aplicaciones.

## Planeación de la topología ##
Antes de crear la superficie de una aplicación distribuida, resulta útil tener algunos datos con antelación.

- **Dominio personalizado para la aplicación:** ¿cuál es el nombre de dominio personalizado que los clientes usarán para acceder a la aplicación? Para la aplicación de ejemplo, el nombre de dominio personalizado es *www.scalableasedemo.com*.
- **Dominio del Administrador de tráfico:** se debe elegir un nombre de dominio al crear un [perfil del Administrador de tráfico de Azure][AzureTrafficManagerProfile]. Este nombre se combinará con el sufijo *trafficmanager.net* para registrar una entrada de dominio que es administrada por el Administrador de tráfico. Para la aplicación de ejemplo, el nombre elegido es *scalable-ase-demo*. Como resultado, el nombre de dominio completo administrado por el Administrador de tráfico es *scalable-ase-demo.trafficmanager.net*.
- **Estrategia para el escalado de la superficie de la aplicación:** ¿se distribuirá la superficie de la aplicación entre distintos entornos de Servicio de aplicaciones en una sola región? ¿Varias regiones? ¿Una combinación de ambos enfoques? La decisión debería basarse en las expectativas de dónde se vaya a originar el tráfico del cliente, así como también en la medida en que el resto de la infraestructura de back-end de apoyo de una aplicación pueda escalarse. Por ejemplo, con una aplicación totalmente sin estado, se puede escalar una aplicación de forma masiva usando una combinación de varios entornos de Servicio de aplicaciones por región de Azure, multiplicados por más entornos de Servicio de aplicaciones implementados en varias regiones de Azure. Con más de 15 regiones de Azure públicas entre las que elegir, los clientes pueden realmente crear una superficie de aplicación de gran escala en todo el mundo. Para la aplicación de ejemplo usada en este artículo, se crearon tres entornos de Servicio de aplicaciones en una sola región de Azure (Centro y Sur de EE. UU.).
- **Convención de nomenclatura para los entornos de Servicio de aplicaciones:** cada entorno de Servicio de aplicaciones requiere un nombre único. Si existen más de uno o dos entornos de Servicio de aplicaciones, resulta útil disponer de una convención de nomenclatura para ayudar a identificar cada uno de ellos. Para la aplicación de ejemplo, se usó una convención de nomenclatura sencilla. Los nombres de los tres entornos de Servicio de aplicaciones son *fe1ase*, *fe2ase* y *fe3ase*.
- **Convención de nomenclatura para las aplicaciones:** ya que se implementarán varias instancias de la aplicación, se necesita un nombre para cada instancia de la aplicación implementada. Una característica poco conocida pero muy cómoda de los entornos de Servicio de aplicaciones es que se puede usar el mismo nombre de aplicación en varios entornos de Servicio de aplicaciones. Dado que cada entorno de Servicio de aplicaciones tiene un sufijo de dominio único, los desarrolladores pueden reutilizar el mismo nombre de aplicación en cada entorno. Por ejemplo, un desarrollador podría denominar a las aplicaciones como sigue:*myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*, etc. No obstante, para la aplicación de ejemplo, cada instancia tiene un nombre único. Los nombres de las instancias de aplicación usados son *webfrontend1*, *webfrontend2* y *webfrontend3*.


## Configuración de un perfil del Administrador de tráfico ##
Una vez que se implementan varias instancias de una aplicación en varios entornos de Servicio de aplicaciones, las instancias de aplicación individuales se pueden registrar con el Administrador de tráfico. Para la aplicación de ejemplo, se necesita un perfil del Administrador de tráfico para *scalable-ase-demo.trafficmanager.net* que pueda enrutar a los clientes a cualquiera de las siguientes instancias de la aplicación implementadas:

- **webfrontend1.fe1ase.p.azurewebsites.net:** una instancia de la aplicación de ejemplo que se implementa en el primer entorno de Servicio de aplicaciones.
- **webfrontend2.fe2ase.p.azurewebsites.net:** una instancia de la aplicación de ejemplo que se implementa en el segundo entorno de Servicio de aplicaciones.
- **webfrontend3.fe3ase.p.azurewebsites.net:** una instancia de la aplicación de ejemplo que se implementa en el tercer entorno de Servicio de aplicaciones.

La forma más sencilla de registrar varios extremos de Servicio de aplicaciones de Azure, todos los cuales se ejecutan en la **misma** región de Azure, es haciendo uso de la [compatibilidad del Administrador de recursos de Azure con la vista previa del Administrador de tráfico de Azure][ARMTrafficManager].

El primer paso consiste en crear un perfil del Administrador de tráfico de Azure. El código siguiente muestra cómo se creó el perfil para la aplicación de ejemplo:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Observe cómo el parámetro *RelativeDnsName* se estableció en *scalable-ase-demo*. Así es cómo se crea el nombre de dominio *scalable-ase-demo.trafficmanager.net* y se asocia con un perfil del Administrador de tráfico.

El parámetro *TrafficRoutingMethod* define la directiva de equilibrio de carga que el Administrador de tráfico usará para determinar cómo distribuir la carga de los clientes entre todos los extremos disponibles. En este ejemplo, se eligió el método *Weighted*. Como resultado, las solicitudes de clientes se distribuirán entre todos los extremos de la aplicación registrados en función de las ponderaciones relativas asociadas con cada extremo.

Con el perfil creado, se agrega cada instancia de la aplicación al perfil como un *extremo externo*. El código siguiente muestra las direcciones URL para cada una de las tres instancias de la aplicación que se agregan al perfil.

    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type ExternalEndpoints –Target webfrontend1.fe1ase.p.azurewebsites.net –EndpointStatus Enabled –Weight 10
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type ExternalEndpoints –Target webfrontend2.fe2ase.p.azurewebsites.net –EndpointStatus Enabled –Weight 10
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type ExternalEndpoints –Target webfrontend3.fe3ase.p.azurewebsites.net –EndpointStatus Enabled –Weight 10
    
    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Observe que existe una llamada a *Add-AzureTrafficManagerEndpointConfig* para cada instancia de aplicación individual. El parámetro *Target* de cada comando de Powershell apunta al nombre de dominio completo (FQDN) de cada una de las tres instancias de la aplicación implementadas. Los distintos FQDN son los valores que se usarán para recorrer la cadena de CNAME DNS de *scalable-ase-demo.trafficmanager.net* con el fin de distribuir la carga de tráfico entre todos los extremos registrados en el perfil del Administrador de tráfico.

Los tres extremos usan el mismo valor (10) para el parámetro *Weight*. Como resultado, el Administrador de tráfico distribuye las solicitudes de los clientes entre las tres instancias de la aplicación de forma relativamente uniforme.

*Nota:* dado que la compatibilidad de ARM con el Administrador de tráfico se encuentra en vista previa, los extremos de Servicio de aplicaciones de Azure tienen que establecer el parámetro *Type* en *ExternalEndpoints*. En el futuro, la variante de ARM del Administrador de tráfico admitirá de forma nativa los extremos de Servicio de aplicaciones de Azure como un tipo de extremo.

## Hacer que el dominio personalizado de la aplicación apunte al dominio del Administrador de tráfico ##
El último paso necesario es hacer que el dominio personalizado de la aplicación apunte al dominio del Administrador de tráfico. Para la aplicación de ejemplo, esto significa que debe apuntar a *www.scalableasedemo.com* en *scalable-ase-demo.trafficmanager.net*. Este paso se debe completar con el registrador de dominios que administra el dominio personalizado.

Usando las herramientas de administración de dominios del registrador, se debe crear un registro CNAME que apunte al dominio personalizado en el dominio del Administrador de tráfico. En la figura siguiente se muestra un ejemplo de esta configuración de CNAME:

![CNAME para el dominio personalizado][CNAMEforCustomDomain]

Aunque no se trata en este tema, recuerde que cada instancia de aplicación individual debe tener el dominio personalizado registrado con ella también. De lo contrario, si una solicitud llega a una instancia de la aplicación y la aplicación no tiene el dominio personalizado registrado con la aplicación, la solicitud producirá un error.

En este ejemplo, el dominio personalizado es *www.scalableasedemo.com* y cada instancia de la aplicación tiene el dominio personalizado asociado con él.

![Dominio personalizado][CustomDomain]

Como resumen del registro de un dominio personalizado con las aplicaciones de Servicio de aplicaciones de Azure, consulte el siguiente artículo acerca del [registro de dominios personalizados][RegisterCustomDomain].

## Prueba de la topología distribuida ##
El resultado final de la configuración del Administrador de tráfico y de DNS es que las solicitudes de *www.scalableasedemo.com* fluirán en la siguiente secuencia:

1. Un dispositivo o explorador realizará una búsqueda DNS de *www.scalableasedemo.com*.
2. La entrada CNAME en el registrador de dominios hace que la búsqueda DNS se redirija al Administrador de tráfico de Azure.
3. Se realiza una búsqueda DNS de *scalable-ase-demo.trafficmanager.net* en uno de los servidores DNS del Administrador de tráfico de Azure.
4. Según la directiva de equilibrio de carga (el parámetro *TrafficRoutingMethod* usado antes al crear el perfil del Administrador de tráfico), el Administrador de tráfico seleccionará uno de los extremos configurados y devolverá el FQDN de ese extremo al explorador o dispositivo.
5.  Dado que el FQDN del extremo es la dirección URL de una instancia de la aplicación que se ejecuta en un entorno de Servicio de aplicaciones, el explorador o el dispositivo pedirá a un servidor DNS de Microsoft Azure que resuelva el FQDN en una dirección IP. 
6. El explorador o el dispositivo enviará la solicitud HTTP/S a la dirección IP.  
7. La solicitud llegará a una de las instancias de la aplicación que se ejecutan en uno de los entornos de Servicio de aplicaciones.

En la imagen de consola siguiente, se muestra una búsqueda DNS para el dominio personalizado de la aplicación de ejemplo que se ha resuelto correctamente en una instancia de la aplicación que se ejecuta en uno de los tres entornos de Servicio de aplicaciones (en este caso, el segundo de los tres):

![Búsqueda DNS][DNSLookup]

## Información y vínculos adicionales ##
Documentación sobre [compatibilidad del Administrador de recursos de Azure (ARM) con la vista previa del Administrador de tráfico de Azure][ARMTrafficManager].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]: https://azure.microsoft.com/documentation/articles/traffic-manager-manage-profiles/
[ARMTrafficManager]: https://azure.microsoft.com/documentation/articles/traffic-manager-powershell-arm/
[RegisterCustomDomain]: https://azure.microsoft.com/es-ES/documentation/articles/web-sites-custom-domain-name/


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]: ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]: ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]: ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png

<!---HONumber=AcomDC_0128_2016-->