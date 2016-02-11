<properties
   pageTitle="Probar el rendimiento de la aplicación web de Azure | Microsoft Azure"
   description="Ejecute pruebas de rendimiento de aplicación web de Azure para la manera en que su aplicación controla la carga del usuario. Mida el tiempo de respuesta y encuentre los errores que puedan indicar problemas."
   services="app-service\web"
   documentationCenter=""
   authors="ecfan"
   manager="douge"
   editor="jimbe"/>

<tags
   ms.service="app-service-web"
   ms.workload="web"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="01/13/2016"
   ms.author="estfan; manasma"/>

# Realizar una prueba de tu aplicación web de Azure bajo carga

Compruebe el rendimiento de su aplicación web antes de iniciarla o de implementar actualizaciones en la producción. De esa manera, puede evaluar mejor si su aplicación está lista para publicarse. Siéntase más seguro de que su aplicación puede controlar el tráfico durante el uso máximo o en su siguiente inserción de marketing.

Durante la vista previa pública, puede probar el rendimiento de su aplicación de forma gratuita en el Portal de Azure. Estas pruebas simulan la carga del usuario en la aplicación durante un período de tiempo específico y miden la respuesta de la aplicación. Por ejemplo, los resultados de las pruebas muestran la rapidez de la aplicación para responder a un número específico de usuarios. También muestran cuántas solicitudes tienen error, lo que podría indicar problemas con su aplicación.

![Encontrar problemas de rendimiento en su aplicación web][TestOverview]

## Antes de comenzar

*	Necesitará una [suscripción de Azure][AzureSubscription], si no tiene ya una. Aprenda cómo puede [abrir una cuenta de Azure de forma gratuita][AzureFreeTrial].

*	Necesitará una cuenta de [Visual Studio Team Services (VSTS)][WhatIsTeamServices] para mantener su historial de prueba de rendimiento. Cree su nueva cuenta al configurar su prueba de rendimiento o use una cuenta existente si usted es el propietario de la cuenta. [¿Qué más puedo hacer con una cuenta de Visual Studio Team Services?](#TeamServicesAccount)

*	Implemente su aplicación para realizar pruebas en un entorno que no sea de producción. Haga que su aplicación use un plan de Servicio de aplicaciones que no sea el plan usado en la producción. De esa manera, no afectará a los clientes existentes ni ralentizará la aplicación en producción.

## Configurar y ejecutar la prueba de rendimiento

0.	Inicie sesión en el [Portal de Azure][AzurePortal]. Para usar una cuenta de Visual Studio Team Services que posea, inicie sesión como propietario de la cuenta.

0.	Vaya a la aplicación web.

	![Ir a Examinar todo, Aplicaciones web, su aplicación web][WebApp]

0.	Vaya a **Prueba de rendimiento**.

	![Ir a Herramientas, Prueba de rendimiento][ExpandedTools]
 
0.	Ahora vinculará una cuenta de [Visual Studio Team Services (VSTS)][WhatIsTeamServices] para mantener su historial de prueba de rendimiento.

	Si tiene una cuenta de Team Services que pueda usar, seleccione esa cuenta. Si no es así, cree una cuenta nueva.

	![Seleccionar la cuenta de Team Services existente o crear una cuenta nueva][ExistingNewTeamServicesAccount]

0.	Crear su prueba de rendimiento. Establezca los detalles y ejecute la prueba. Puede ver los resultados en tiempo real mientras se ejecuta la prueba.

	Por ejemplo, supongamos que tenemos una aplicación que repartió cupones en las ventas delas vacaciones del año pasado. Este evento duró 15 minutos con una carga máxima de 100 clientes simultáneos. Queremos duplicar el número de clientes este año. También queremos mejorar la satisfacción del cliente, reduciendo el tiempo de carga de página de 5 a 2 segundos. Por tanto, probaremos el rendimiento de nuestra aplicación actualizada con 250 usuarios durante 15 minutos.

	Simularemos la carga en nuestra aplicación generando usuarios virtuales (clientes) que visitan nuestro sitio web a la vez. Esto nos mostrará cuántas solicitudes generan error o responden lentamente.

	![Crear, configurar y ejecutar la prueba de rendimiento][NewTest]

	 *	La dirección URL predeterminada de su aplicación web se agrega automáticamente. Puede cambiar la dirección URL para probar otras páginas (solo solicitudes HTTP GET).

	 *	Para simular las condiciones locales y reducir la latencia, seleccione una ubicación más cercana a los usuarios para generar la carga.

	Esta es la prueba en curso. Durante el primer minuto, nuestra página se carga con mayor lentitud de la deseada.

	![Prueba de rendimiento en curso con datos en tiempo real][TestRunning]

	Después de realiza la prueba, nos damos cuenta de que la página se carga con mayor rapidez después del primer minuto. Esto ayuda a identificar dónde podemos querer iniciar la solución del problema.

	![La prueba de rendimiento completada muestra resultados, incluidas solicitudes con error][TestDone]
	
Agradecemos sus comentarios. Para preguntas o problemas, póngase en contacto con nosotros en <vsoloadtest@microsoft.com>.

##	Preguntas y respuestas

#### P: ¿Hay un límite en el tiempo durante el cual puedo ejecutar una prueba? 

R: Sí, puede ejecutar la prueba como máximo durante una hora en el Portal de Azure.

#### P: ¿Cuánto tiempo puedo ejecutar las pruebas de rendimiento? 

R: Tras la vista previa pública, obtendrá 20.000 minutos de usuarios virtuales (VUM) de manera gratuita cada mes con su cuenta de Visual Studio Team Services. Un VUM es el número de usuarios virtuales multiplicados por el número de minutos de su prueba. Si sus necesidades superan el límite gratuito, puede adquirir más tiempo y pagar solo por lo que use.

#### P: ¿Dónde puedo comprobar cuántos VUM he usado hasta ahora?

R: Puede comprobar este importe en el Portal de Azure.

![Ir a la cuenta de Team Services][TeamServicesAccount]

![Comprobar los VUM usados][CheckTestTime]

<a name="VSOAccount"></a>
#### P: ¿Qué más puedo hacer con una cuenta de Visual Studio Team Services?

R: Para encontrar su cuenta nueva, vaya a ```https://{accountname}.visualstudio.com```. Comparta su código, compile, pruebe, realice un seguimiento del trabajo y entregue software: todo ello en la nube con cualquier lenguaje o herramienta. Obtenga más información sobre la manera en que las características y los servicios de [Visual Studio Team Services][WhatIsTeamServices] ayudan al equipo a colaborar con mayor facilidad y a implementar de forma continua.

<!--Image references-->
[WebApp]: ./media/app-service-web-app-performance-test/azure-np-web-apps.png
[TestOverview]: ./media/app-service-web-app-performance-test/azure-np-perf-test-overview.png
[ExpandedTools]: ./media/app-service-web-app-performance-test/azure-np-web-app-details-tools-expanded.png
[ExistingNewTeamServicesAccount]: ./media/app-service-web-app-performance-test/azure-np-no-vso-account.png
[NewTest]: ./media/app-service-web-app-performance-test/azure-np-new-performance-test.png
[TestRunning]: ./media/app-service-web-app-performance-test/azure-np-running-perf-test.png
[TestDone]: ./media/app-service-web-app-performance-test/azure-np-perf-test-done.png
[TeamServicesAccount]: ./media/app-service-web-app-performance-test/azure-np-vso-accounts.png
[CheckTestTime]: ./media/app-service-web-app-performance-test/azure-np-vso-accounts-vum-summary.png

<!--Reference links -->
[AzurePortal]: https://portal.azure.com
[AzureSubscription]: https://account.windowsazure.com/subscriptions
[AzureFreeTrial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[WhatIsTeamServices]: https://www.visualstudio.com/products/what-is-visual-studio-online-vs

<!---HONumber=AcomDC_0128_2016-->