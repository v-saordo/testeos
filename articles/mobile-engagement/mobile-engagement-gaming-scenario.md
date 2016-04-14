<properties 
	pageTitle="Implementación de Azure Mobile Engagement para una aplicación de juegos"
	description="Escenario de aplicación de juegos para implementar Azure Mobile Engagement" 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-multiple"
	ms.workload="mobile" 
	ms.date="02/29/2016"
	ms.author="piyushjo"/>

#Implementación de Mobile Engagement con una aplicación de juegos

## Información general

Una nueva empresa de juegos ha lanzado una aplicación de juego de estrategia/juego de rol dedicada a la pesca. El juego lleva 6 meses en activo. Ha cosechado un gran éxito, con millones de descargas, y la retención es muy alta en comparación con otras aplicaciones de juego de nuevas empresas. En la reunión de revisión trimestral, las partes interesadas están de acuerdo en que necesitan aumentar los ingresos medios por usuario (ARPU). Los módulos premium en el juego están disponibles como ofertas especiales. Estos módulos de juego permiten a los usuarios actualizar la apariencia y el rendimiento de los sedales y los cebos o aparejos en el juego. Sin embargo, las ventas de módulos son muy bajas. Por tanto, deciden en primer lugar analizar la experiencia del cliente con una herramienta de análisis y después desarrollar un programa de compromiso para aumentar las ventas mediante la segmentación avanzada.

Partiendo de [Azure Mobile Engagement: Guía de introducción con procedimientos recomendados](mobile-engagement-getting-started-best-practices.md), crean una estrategia de compromiso.

##Objetivos y KPI

Las partes interesadas clave para el juego se reúnen. Todos están de acuerdo en un objetivo principal: aumentar las ventas de módulos premium en un 15%. Crean indicadores clave de rendimiento (KPI) de negocio para medir y promover este objetivo.

* ¿En qué nivel del juego se compran estos módulos?
* ¿Cuáles son los ingresos por usuario, por sesión, por semana y por mes?
* ¿Cuáles son los tipos favoritos de compra?

En la parte 1 de la [Guía de introducción](mobile-engagement-getting-started-best-practices.md), se explica cómo definir los objetivos y los KPI.

Con los KPI de negocio ya definidos, la encargada de productos móviles crea los KPI de compromiso para determinar la retención y las nuevas tendencias de usuarios.

* Supervisar la retención y el uso en los siguientes intervalos: a diario, cada 2 días, semanalmente, mensualmente y cada tres meses
* Recuentos de usuarios activos
* La clasificación de la aplicación en la tienda

Según las recomendaciones del equipo de TI, se han agregado los siguientes KPI técnicos para responder a las preguntas siguientes:

* ¿Cuál es la ruta de acceso de los usuarios (qué página visitan, cuánto tiempo pasan los usuarios en ella)?
* Número de bloqueos o errores encontrados por sesión
* ¿Qué versiones de sistema operativo ejecutan mis usuarios?
* ¿Cuál es el tamaño medio de pantalla de mis usuarios?
* ¿Qué tipo de conectividad de Internet tienen mis usuarios?

Para cada KPI, la encargada de productos móviles especifica los datos que necesita y dónde se encuentran en su cuaderno de estrategias.

## Programa de compromiso e integración

Antes de crear un programa avanzado de compromiso, el director de proyectos móviles a cargo del proyecto debe comprender a fondo cómo y cuándo los usuarios consumen los productos.

Después de 3 meses, el director de proyectos móviles ha recopilado datos suficientes para mejorar las ventas por notificación push en aplicación. Descubre que:

* La primera compra generalmente se produce en el nivel 14. En el 90% de los casos, la compra es de nuevas armas legendarias por 3 USD.
* En el 80% de los casos, los usuarios que han realizado una compra siguen usando el producto y hacen más compras.
* Los usuarios que han superado el nivel 20 empiezan a gastar más de 10 USD por semana.
* Los usuarios tienden a comprar módulos premium en los niveles 16, 24 y 32.

Gracias a este análisis, el director de proyectos móviles decide crear secuencias específicas de notificaciones push para aumentar las ventas en aplicación. Crea tres secuencias push denominadas: Programa de bienvenida, Programa de ventas y Programa de inactivos. Para más información, vea los [cuadernos de estrategias](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks) ![][1].

<!--Image references-->

[1]: ./media/mobile-engagement-game-scenario/notification-scenario.png

<!--Link references-->

<!---HONumber=AcomDC_0302_2016-->