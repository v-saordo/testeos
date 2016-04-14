<properties
   pageTitle="Información general de Service Fabric | Microsoft Azure"
   description="Información general de Service Fabric donde las aplicaciones se componen de muchos microservicios para ofrecer escala y resiliencia. Service Fabric es una plataforma de sistemas distribuidos que se usa para crear aplicaciones escalables, confiables y fáciles de administrar para la nube."
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/12/2016"
   ms.author="mfussell"/>

# Información general de Service Fabric
Service Fabric es una plataforma de sistemas distribuidos que facilita el empaquetado, la implementación y la administración de microservicios escalables y de confianza, además de abordar los importantes desafíos a los que se enfrentan el desarrollo y la administración aplicaciones en la nube. Mediante el uso de Service Fabric, los desarrolladores y administradores pueden evitar solucionar problemas complejos de infraestructura y centrarse en su lugar en la implementación de cargas de trabajo exigentes y críticas, sabiendo que son escalables, confiables y fáciles de administrar. Service Fabric representa la plataforma middleware de próxima generación para crear y administrar estas aplicaciones de clase empresarial, de escala de nube de nivel 1.

## Aplicaciones compuestas por microservicios
Service Fabric le permite crear y administrar aplicaciones escalables y confiables, compuestas por microservicios que se ejecutan en una densidad muy alta con un grupo compartido de máquinas (que se conocen comúnmente como clúster de Service Fabric). Ofrece un sofisticado tiempo de ejecución para crear microservicios con y sin estado distribuidos y escalables. También proporciona capacidades de administración de aplicaciones completas para el aprovisionamiento, la implementación, la supervisión, la actualización/aplicación de revisiones y eliminación de aplicaciones implementadas.

¿Por qué son importantes los microservicios? Las dos razones principales son:

1. Le permiten escalar diferentes partes de la aplicación, según sus necesidades

2. Los equipos de desarrollo pueden ser más ágiles en la implementación de los cambios y, por tanto, dar servicio a los clientes con mayor rapidez y con más frecuencia.

Service Fabric impulsa muchos servicios de Microsoft en la actualidad, como Base de datos SQL de Azure, DocumentDB de Azure, Cortana, Power BI, Microsoft Intune, Centros de eventos de Azure, IoT de Azure, muchos servicios principales de Azure y Skype Empresarial, por nombrar algunos.

Service Fabric se adapta para crear servicios "nacidos en la nube" que pueden comenzar con un tamaño pequeño, según sea necesario y crecer a gran escala con cientos o miles de máquinas.

Actualmente, los servicios de escala de Internet se crean mediante microservicios. Entre los ejemplos de microservicios se encuentran las puertas de enlace de protocolo, perfiles de usuario, carros de la compra, procesamiento de inventario, colas y memorias caché. Service Fabric es una plataforma de microservicios que da a cada microservicio un nombre único con o sin estado.

Service Fabric ofrece capacidades completas de administración del ciclo de vida y de tiempo de ejecución para las aplicaciones compuestas de estos microservicios. Hospeda microservicios dentro de contenedores que se implementan y activan en el clúster de Service Fabric. Al igual que se hace posible una orden de aumento de magnitud en la densidad al pasar de las máquinas virtuales a contenedores, una orden de magnitud similar en la densidad se hace posible al pasar de contenedores a microservicios. Por ejemplo, un único clúster de base de datos SQL de Azure, que se basa en Service Fabric, se compone de cientos de máquinas que ejecutan decenas de miles de contenedores que hospedan un total de cientos de miles de bases de datos. (Cada base de datos es un microservicio con estado de Service Fabric). Lo mismo puede decirse de los centros de eventos y otros servicios mencionados anteriormente. Este es el motivo por el que el término "hiperescala" puede usarse para describir las capacidades de Service Fabric. Si los contenedores dan una alta densidad, los microservicios proporcionan hiperescala.

Para más información sobre los microservicios, lea el artículo [¿Por qué usar un enfoque de microservicios para crear aplicaciones?](service-fabric-overview-microservices.md)

![Plataforma de Service Fabric][Image1]

## Microservicios de Service Fabric con estado y sin estado

Los microservicios sin estado (puertas de enlace de protocolo, servidores proxy web, etc.) no mantienen ningún estado mutable fuera de ninguna solicitud determinada y su respuesta del servicio. Los roles de trabajo de los servicios en la nube de Azure son un ejemplo de servicio sin estado. Los microservicios con estado (cuentas de usuario, bases de datos, dispositivos, carros de la compra, colas, etc.) mantienen un estado mutable y autoritativo más allá de la solicitud y su respuesta. Actualmente, las aplicaciones de escala de Internet se componen de una combinación de microservicios con estado y sin estado.

¿Por qué no usar simplemente los servicios sin estado para todo? Las dos razones principales son:

1. La capacidad de crear servicios de procesamiento de transacciones en línea (OLTP) tolerantes a errores, de baja latencia y alto rendimiento, como interfaces de usuario interactivas, búsqueda, sistemas de Internet de las cosas (IoT), sistemas de comercialización, sistemas de detección de fraudes y procesamiento de tarjetas de crédito y, a continuación, administración de recursos personales, etc. al mantener el código y los datos cerca en la misma máquina.

2. La simplificación del diseño de la aplicación, como microservicios con estado, elimina la necesidad de colas adicionales y memorias caché. Tradicionalmente eran necesarios para satisfacer los requisitos de disponibilidad y latencia de una aplicación puramente sin estado. Como los servicios con estado son de alta disponibilidad y de baja latencia de forma natural, hay menos partes en movimiento que administrar en su aplicación en su conjunto.

Para obtener más información sobre los patrones de aplicación y el diseño que usa Service Fabric, vea [Escenarios de aplicación](service-fabric-application-scenarios.md)

## Administración del ciclo de vida de aplicación
Service Fabric ofrece compatibilidad de primera clase para toda la administración del ciclo de vida de aplicación (ALM) de las aplicaciones de nube: desde el desarrollo hasta la implementación, la administración diaria, el mantenimiento y, finalmente, la retirada.

Las capacidades de ALM de Service Fabric permiten a los administradores de aplicación/operadores de TI usar flujos de trabajo simples y de baja interacción para aprovisionar, implementar, aplicar revisiones y supervisar aplicaciones. Estos flujos de trabajo integrados reducen en gran medida la carga de los operadores de TI para mantener las aplicaciones continuamente disponibles.

La mayoría de las aplicaciones constan de una combinación de microservicios con estado y sin estado y otros tiempos de ejecución/ejecutables que se implementan juntos. Al tener tipos seguros en las aplicaciones y los microservicios empaquetados, Service Fabric permite la implementación de varias instancias de aplicación, en las que cada una de ellas se puede administrar y actualizar de forma independiente. Lo importante es que Service Fabric puede implementar *cualquier* ejecutable o tiempo de ejecución y hacer que estos sean confiables. Por ejemplo, se puede usar para implementar ASP.NET 5, Node.js, scripts o cualquier cosa que conforme la aplicación.

Para obtener más información sobre la administración del ciclo de vida de aplicaciones, vea [ciclo de vida de aplicación](service-fabric-application-lifecycle.md)

## Principales capacidades
Usando Service Fabric, puede:

- Desarrollar aplicaciones escalables de forma masiva, de recuperación automática.

- Desarrollar con un enfoque de "centro de datos en su equipo". El entorno de desarrollo local es el mismo código que se ejecuta en los centros de datos de Azure.

- Desarrollar aplicaciones compuestas por microservicios, con el modelo de programación de Service Fabric, o simplemente archivos ejecutables y otros marcos de aplicación que elija, como ASP.NET, Node.js, etc.

- Desarrollar microservicios con estado y sin estado y hacer que sean de alta confianza.

- Simplificar el diseño de su aplicación, mediante el uso de microservicios con estado en lugar de memorias caché y colas.

- Implementar aplicaciones en cuestión de segundos.

- Implementar en Azure o en nubes locales que ejecutan Windows Server o Linux con cero cambios de código. Crear una vez e implementar en cualquier clúster de Service Fabric.

- Implementar aplicaciones en una densidad mayor que las máquinas virtuales, implementando cientos o miles de aplicaciones por equipo.

- Implementar versiones diferentes de la misma aplicación en paralelo, siendo cada una de ellas actualizable por separado.

- Administrar el ciclo de vida de sus aplicaciones con estado sin tiempo de inactividad, incluidas las actualizaciones de última hora y las que no lo son.

- Administrar aplicaciones mediante las API de .NET, PowerShell o las interfaces de REST.

- Actualizar y aplicar revisiones a microservicios dentro de las aplicaciones de manera independiente.

- Supervisar y diagnosticar el estado de sus aplicaciones y establecer directivas para realizar reparaciones automáticas.

- Escalar o reducir verticalmente su clúster de Service Fabric con facilidad, sabiendo que las aplicaciones se escalan según los recursos disponibles.

- Vea el equilibrador de recursos de recuperación automática organizar la redistribución de aplicaciones en el clúster de Service Fabric para recuperarse de errores y optimizar la distribución de la carga en función de los recursos disponibles.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

* Para obtener más información:
	* [¿Por qué usar un enfoque de microservicios para crear aplicaciones?](service-fabric-overview-microservices.md)
	* [Información general sobre la terminología](service-fabric-technical-overview.md)
* Configuración del [entorno de desarrollo](service-fabric-get-started.md) de Service Fabric  
* Selección de un [marco de modelo de programación](service-fabric-choose-framework.md) para el servicio


[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png

<!---HONumber=AcomDC_0224_2016-->