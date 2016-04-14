<properties
	pageTitle="Uso de Servicios móviles: ¿cómo ayuda el Servicio de aplicaciones?"
	description="Obtenga información sobre qué ventajas aporta Servicios de aplicaciones a los proyectos de Servicios móviles existentes."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/17/2016"
	ms.author="adrianha"/>

# <a name="getting-started"></a>Uso de Servicios móviles: ¿cómo ayuda el Servicio de aplicaciones?

##Información general
Servicios móviles existentes es seguro y seguirá siendo compatible. Sin embargo hay una serie de ventajas que proporciona la plataforma *Servicio de aplicaciones de Azure* para su aplicación móvil que no están disponibles hoy en día con Servicios móviles:

- Oferta más simple, más fácil y más rentable para las aplicaciones que incluyen clientes móviles y web
- Nuevas características de host como trabajos web, CNAME personalizado y una mejor supervisión
- Integración inmediata con Administrador de tráfico
- Conectividad a los recursos locales y VPN mediante una red virtual además de las conexiones híbridas
- Supervisión, alertas y solución de problemas para su aplicación mediante NewRelic o AppInsights
- Espectro más completo de los recursos de procesos subyacentes y precios
- Escalado automático integrado, equilibrio de carga y supervisión del rendimiento
- Capacidades de prueba en producción, reversión, copia de seguridad y almacenamiento provisional incorporadas

## Nuevas características de hospedaje
En *Servicio de aplicaciones de Azure*, el código de back-end de la *aplicación móvil* se ejecuta en el mismo contenedor que la aplicación web y la aplicación de la API. De esta forma, puede beneficiarse de todas las características de este contenedor, incluidas la que no están actualmente en Servicios móviles:

- Adición de lógica de back-end de ejecución continua a través de trabajos web
- Garantía de que el código de back-end siempre está ejecutándose
- Uso de CNames para proporcionar nombres descriptivos y estables en los extremos de back-end móvil
- Escala geográfica de la aplicación con Administrador de tráfico
- Se incluyen las bibliotecas y los paquetes que desee.
- (Para. NET) Aproveche todas las características de ASP.NET, incluido MVC.
- (Para Node.js) Aproveche cualquier biblioteca puramente de JavaScript del ecosistema de Node, incluidas las bibliotecas comunes de MVC.

##Acceso a datos locales con red virtual
Con Servicios móviles ahora puede usar las conexiones híbridas para acceder a los recursos locales. Sin embargo, hay situaciones en las que es preferible una solución de VPN. Con *Servicio de aplicaciones de Azure* puede usar la red virtual de Azure para el código de back-end de la aplicación móvil.

##Uso del lenguaje de back-end favorito
El *Servicio de aplicaciones de Azure* ofrece una compatibilidad más amplia y completa para las plataformas ASP.NET y Node.js, incluido el acceso a los tiempos de ejecución más recientes.

##Configuración de la escala automática
Con Servicios móviles, todas las instancias de su código de back-end se ejecutaban en máquinas virtuales pequeñas. *Servicio de aplicaciones de Azure* le permite seleccionar el tamaño de las máquinas virtuales desde un conjunto mucho más completo de opciones. También puede escalar de forma vertical u horizontal rápidamente para controlar cualquier carga de cliente entrante basándose en varias métricas de rendimiento.

##Sepa lo que ocurre
Reaccione a los problemas en tiempo real gracias a la supervisión y a las alertas que permiten el aviso automático a usted o a su equipo. Integre análisis de aplicaciones avanzadas y la funcionalidad de supervisión de New Relic y AppInsights para obtener una perspectiva incluso más completa acerca de cómo funciona la aplicación móvil. Con el *Servicio de aplicaciones de Azure*, ahora puede configurar alertas basadas en varias métricas de rendimiento, ya sea mediante programación o a través del Portal de Azure.

##Proteja los activos
Realice una copia de seguridad automática del back-end y la base de datos. El código y los datos están seguros frente a desastres y se restauran fácilmente, por lo que puede llevar a cabo su actividad comercial con confianza.

##Preparado, listo...¡ya!
Con *Servicio de aplicaciones de Azure* ahora puede crear varios entornos de ensayo y prueba privados para las aplicaciones móviles. Úselos para realizar la prueba antes de la implementación. Cambie a una producción sin tiempo de inactividad. Las aplicaciones web se cargan previamente, por lo que se garantiza la mejor experiencia del cliente.

Para comenzar a aprovechar el *Servicio de aplicaciones* en su servicio móvil existente, siga este [tutorial](app-service-mobile-migrating-from-mobile-services.md).

<!---HONumber=AcomDC_0224_2016-->