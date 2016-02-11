<properties 
	pageTitle="Servicio de aplicaciones de Azure: escalado de aplicaciones de Servicio de aplicaciones" 
	description="Aprenda los pormenores del escalado de una aplicación en Servicio de aplicaciones." 
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
	
# Servicio de aplicaciones de Azure: escalado de aplicaciones de Servicio de aplicaciones

Las aplicaciones hospedadas en el Servicio de aplicaciones de Azure pueden [lograr una escala masiva](https://azure.microsoft.com/blog/canadian-broadcasting-corporation-radio-canada-leverage-azure-for-smooth-election-coverage/); sin embargo, la tarea de escalar una aplicación es compleja y no existe una solución que sirva para todas las situaciones. Para escalar correctamente su aplicación, tenga en cuenta tres áreas clave que contribuirán a su éxito:

1. Comprensión de la arquitectura de la aplicación y sus puntos débiles.
	* ¿Se trata de una aplicación con estado? ¿O sin estado?
	* ¿Cuáles son los componentes de la aplicación?
		* ¿Dónde se encuentran los cuellos de botella en la aplicación? 
	* Cuando se aplica carga a la aplicación, ¿qué será lo primero en dejar de funcionar?
2. Comprensión de los requisitos de carga y rendimiento esperados.
	* ¿Se necesita que la aplicación sirva a mil usuarios? ¿O a un millón?
	* ¿Procederá el tráfico de una sola ubicación geográfica o será global?
	* ¿Hay variaciones estacionales? ¿Picos de tráfico? 
	* ¿Con qué rapidez debe responder la aplicación? ¿Un segundo? ¿Un milisegundo?
3. Comprensión y aprovechamiento correcto de la plataforma que hospeda su aplicación.
	* ¿Qué características debo aprovechar para lograr mis objetivos de escala?
	
Esta sección lo ayudará a comprender todos los factores y a diseñar una estrategia que aproveche las características necesarias de Servicio de aplicaciones para lograr sus objetivos de escalabilidad.

[AZURE.INCLUDE [app-service-blueprint-scaling-app-service-applications](../../includes/app-service-blueprint-scaling-app-service-applications.md)]

<!---HONumber=AcomDC_0128_2016-->