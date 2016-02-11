<properties
	pageTitle="Introducción a Aplicaciones web"
	description="Más información sobre Aplicaciones web del Servicio de aplicaciones"
	services="app-service\web"
	documentationCenter=""
	authors="jaime-espinosa"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/09/2016"
	ms.author="jaime.espinosa"/>


#Introducción a Aplicaciones web

El [Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714) es una plataforma totalmente administrada para desarrolladores profesionales que aporta un amplio conjunto de capacidades a escenarios web, móviles y de integración. Use el Servicio de aplicaciones de Azure para crear e implementar aplicaciones web críticas que se ajusten a su negocio de forma rápida.

Aproveche la eficacia de las [Aplicaciones web del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714) para usar los lenguajes y marcos que ya conoce y de los que depende, implementar sus aplicaciones rápidamente en la nube de Azure y mejorar el código de forma continua sin tener que volver a preocuparse por la infraestructura.

![Marketplace web](./media/app-service-web-overview/marketplace.png)

## Mucho más que simples sitios web##

Las empresas modernas interactúan con sus clientes de formas cada vez más sofisticadas. Compañías de todos los tipos consideran su presencia web corporativa como una parte crítica de su empresa y componente principal de su plan de negocio. Para adaptarse a este importante hecho, las empresas buscan una plataforma que proporcione agilidad, escalabilidad y seguridad. Además, necesitan poder vincularse a sus sistemas de negocio existentes, ser capaces de implementar rápidamente nuevo código y poner al día las instancias en todo el planeta. Con el Servicio de aplicaciones y Aplicaciones web de Azure, las organizaciones pueden satisfacer los requisitos de sus clientes de forma rápida y rentable.

## ¿Por qué Aplicaciones web? ##

Aplicaciones web del Servicio de aplicaciones de Azure es una plataforma totalmente administrada que permite compilar, implementar y escalar aplicaciones web empresariales en cuestión de segundos. Céntrese en el código de la aplicación y deje que Azure se encargue de la infraestructura para escalarlo y ejecutarlo de forma segura. Aplicaciones web se caracteriza por su:

- **Uso familiar y rápido**: use sus conocimientos para codificar en su lenguaje, marco de trabajo e IDE favoritos. Con tan solo unos cuantos clic, agregue control de versiones, capacidad de actualización, inicio de sesión único, agente de identidad, almacenamiento aislado y supervisión de rendimiento a las aplicaciones web existentes. Obtenga acceso a una completa galería que podrá usar como bloques de creación para acelerar el desarrollo. Experimente una productividad de desarrollo sin precedentes con capacidades innovadoras como la integración continua, la depuración de sitios activos y el IDE de Visual Studio, líder del sector.
- **Carácter empresarial**: Aplicaciones web se ha diseñado para compilar y hospedar aplicaciones críticas y seguras. Compile aplicaciones empresariales integradas de Active Directory que se conecten de forma segura a recursos locales y, después, hospédelas en una plataforma fiable en la nube compatible con ISO, SOC2 y PCI, a la vez que disfruta de contratos de nivel de servicio de carácter empresarial.
- **Escala global**: Aplicaciones web se ha optimizado para proporcionar disponibilidad y escalado automático en una infraestructura de centro de datos globales. Amplíe o reduzca fácilmente aplicaciones a petición, con alta disponibilidad proporcionada en y a través de distintas regiones geográficas. La replicación de datos y el hospedaje de servicios en varias ubicaciones es fácil y rápido, con lo que la expansión a nuevas regiones y geografías es tan sencillo como hacer clic.  

## Conceptos de Aplicaciones web ##

- **Galería de Aplicaciones web**: seleccione en una lista ampliable de plantillas de aplicaciones web existentes. Aproveche lo mejor de la comunidad de aplicaciones OSS mediante la instalación con un solo clic de paquetes como Wordpress, Joomla y Drupal. Comience con buen pie el proceso de desarrollo de su aplicación usando marcos de trabajo como .NET MVC, Django y CakePHP.
- **Ajuste de escala automático**: Aplicaciones web le permite ampliar y reducir rápidamente sus recursos para hacer frente a cualquier carga de trabajo entrante de los clientes. Manualmente seleccione el número y tamaño de las máquinas virtuales o configure el escalado automático para escalar los servidores en función de la carga o programación.
- **Integración continua**: configure la integración continua y los flujos de trabajo de implementación con VSTS, GitHub, TeamCity, Hudson o BitBucket, lo que permite compilar, probar e implementar automáticamente la aplicación web en cada comprobación del código o pruebas de integración correctas.
- **Espacios de implementación**: realice una [implementación de ensayo][Slots] para comprobar el código en un entorno de preproducción que sea idéntico al de la aplicación web de producción del Servicio de aplicaciones de Azure. Cuando esté satisfecho, publique una nueva versión de la aplicación sin tiempo de inactividad mediante una operación de intercambio. 
- **Prueba de producción**: lleve las implementaciones de ensayo a un nivel superior y realice pruebas A/B para comprobar el nuevo código con una fracción configurable de su tráfico dinámico. 
- **Trabajos web**: ejecute cualquier programa o script en las máquinas virtuales de Aplicaciones web. Ejecute los trabajos de forma continua o según una programación y escale para ejecutar en varias máquinas virtuales. Use el [SDK de Trabajos web][Webjobs] de Azure para la integración con el Almacenamiento de Azure o el bus de servicio.
- **Conexiones híbridas**: acceda a los datos locales mediante [conexiones híbridas](../integration-hybrid-connection-overview.md) y [VNET](../app-service-web/web-sites-integrate-with-vnet.md).

## Introducción ##
Para comenzar a usar Aplicaciones web, siga el tutorial [Creación de una aplicación web ASP.NET][create].

Para obtener más información sobre la plataforma de Servicio de aplicaciones de Azure, consulte [Servicio de aplicaciones de Azure][appservice].

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: web-sites-dotnet-get-started.md
[Webjobs]: websites-dotnet-webjobs-sdk-get-started.md
[Slots]: web-sites-staged-publishing.md

 

<!---HONumber=AcomDC_0128_2016-->