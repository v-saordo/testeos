<properties
   pageTitle="Uso del conector de POP3 en Aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure"
   description="Creación y configuración del conector de POP3 o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="11/30/2015"
   ms.author="sameerch"/>


# Introducción al conector de POP3 y su incorporación a su aplicación lógica
Conéctese a un servidor POP3 para recuperar correos electrónicos, incluidos mensajes con datos adjuntos.

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo. Puede agregar el conector de POP3 a sus datos de flujo de trabajo empresarial y datos de proceso como parte de este flujo de trabajo en una aplicación lógica.


## Creación de un conector de POP3 para la aplicación lógica ##
Para usar el conector de POP3, deberá crear primero una instancia de la aplicación de API del conector de POP3. Esto puede hacerse desde el diseñador de la aplicación lógica directamente o fuera de él. Para crear una instancia fuera del diseñador, proceda como sigue:

1.	Abra Azure Marketplace desde la página principal del Portal de Azure.
2.	En "Todo", busque "Conector de POP3".
3.	Configure el conector de POP3 de la siguiente forma:

	![][1] - **Ubicación**: elija la ubicación geográfica en la que desea implementar el conector. - **Suscripción**: elija una suscripción en la que desee crear este conector. - **Grupo de recursos**: seleccione o cree un grupo de recursos en el que vaya a estar el conector. - **Plan de hospedaje web**: seleccione o cree un plan de hospedaje web. - **Nivel de precios**: elija un nivel de precios para el conector. - **Nombre**: asigne un nombre al conector de POP3 - **Configuración del paquete** - **Nombre de usuario**: especifique el nombre de usuario para conectarse al servidor POP3. - **Contraseña**: especifique la contraseña para conectarse al servidor POP3. - **Dirección del servidor**: especifique el nombre del servidor POP3 o la dirección IP. - **Puerto del servidor** especifique el número de puerto del servidor POP3. - **Habilitar SSL**: especifique true para usar POP3 a través del canal SSL/TLS seguro.
4.	Haga clic en Crear. Se creará un nuevo conector de POP3.
5.	Una vez creada la instancia de aplicación de la API, puede crear una aplicación lógica en el mismo grupo de recursos para usar el conector de POP3.

## Uso del conector de POP3 en la aplicación lógica ##
Una vez creada la aplicación de la API, ahora puede usar el conector de POP3 como desencadenador para la aplicación lógica. Para ello, necesita lo siguiente:

1.	Cree una nueva aplicación lógica y elija el mismo grupo de recursos que tiene el conector de POP3.

	![][2]
2.	Abra "Desencadenadores y acciones" para abrir el Diseñador de aplicaciones lógicas y configure el flujo.

	![][3]
3.	El conector de POP3 aparecerá en la sección "Aplicaciones de API en este grupo de recursos" en la galería, en el lado derecho. Selecciónelo.

	![][4]
4.	Puede quitar la aplicación de la API del conector de POP3 en el editor haciendo clic en "Conector de POP3".

5.	Ahora puede usar el conector de POP3 en el flujo. Seleccione el desencadenador "Obtener correo electrónico" y configure la frecuencia y el intervalo. Puede utilizar el correo electrónico recuperado desde el desencadenador de POP3 en otras acciones del flujo.
		 

	![][5] ![][6]

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE]Si desea empezar a trabajar con las aplicaciones lógicas de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-pop3/img1.PNG
[2]: ./media/app-service-logic-connector-pop3/img2.PNG
[3]: ./media/app-service-logic-connector-pop3/img3.png
[4]: ./media/app-service-logic-connector-pop3/img4.PNG
[5]: ./media/app-service-logic-connector-pop3/img5.PNG
[6]: ./media/app-service-logic-connector-pop3/img6.PNG

<!---HONumber=AcomDC_1203_2015-->