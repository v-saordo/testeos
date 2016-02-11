<properties
   pageTitle="Uso del conector de Twilio en Aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure"
   description="Creación y configuración del conector de Twilio o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure"
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


# Introducción al conector de Twilio y su incorporación a las aplicaciones lógicas
Conéctese a su cuenta de Twilio para enviar y recibir mensajes SMS. También puede recuperar números de teléfono y datos de uso. Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo. Puede agregar el conector de Twilio a sus datos de flujo de trabajo empresarial y datos de proceso como parte de este flujo de trabajo en una aplicación lógica.

## Creación de un conector de Twilio para la aplicación lógica ##
Un conector puede crearse dentro de una aplicación lógica o directamente desde Azure Marketplace. Para crear un conector desde Marketplace:

1. En el panel de inicio de Azure, seleccione **Marketplace**.
2. Busque "Conector de Twilio", selecciónelo y seleccione **Crear**.
3. Configure el conector de Twilio de la siguiente forma: ![][1]  
	- **Ubicación**: elija la ubicación geográfica en la que desea implementar el conector.
	- **Suscripción**: elija una suscripción en la que desee crear este conector.
	- **Grupo de recursos**: seleccione o cree un grupo de recursos en el que vaya a estar el conector.
	- **Plan de hospedaje web**: seleccione o cree un plan de hospedaje web.
	- **Nivel de precios**: elija un nivel de precios para el conector.
	- **Nombre**: asigne un nombre al conector de Twilio
	- **Configuración del paquete**
		- **SID de la cuenta**: el identificador único de la cuenta. El SID de la cuenta para su cuenta se puede recuperar desde <https://www.twilio.com/user/account/settings>
		- **Token de autorización**: token de autorización asociado a la cuenta. El token de autorización para la cuenta se puede recuperar desde <https://www.twilio.com/user/account/settings>


4.	Haga clic en Crear. Se crea un nuevo conector de Twilio.
5.	Una vez creada la instancia de aplicación de la API, puede crear una aplicación lógica para usar el conector de Twilio.

## Uso del conector de Twilio en la aplicación lógica ##
Una vez creada la aplicación de la API, ahora puede usar el conector de Twilio como desencadenador/acción para la aplicación lógica. Para ello, necesita lo siguiente:

1.	Cree una nueva aplicación lógica y elija el mismo grupo de recursos que tiene el conector de Twilio. ![][2]
2.	Abra "Desencadenadores y acciones" para abrir el Diseñador de aplicaciones lógicas y configure el flujo: ![][3]
3.	El conector de Twilio aparecerá en la sección "Aplicaciones de API en este grupo de recursos" en la galería, en el lado derecho: ![][4]
4. Puede quitar la aplicación de la API del conector de Twilio en el editor haciendo clic en "Conector de Twilio".

5.	Ahora puede usar el conector de Twilio en el flujo. Puede usar la acción "Enviar mensaje" en el flujo para enviar un mensaje. Configure las propiedades de entrada para la acción "Enviar mensaje" de la siguiente manera:
	- **Desde número de teléfono**: escriba un número de teléfono de Twilio habilitado para el tipo de mensaje que desee enviar. Solo los números de teléfono o códigos breves adquiridos a través de Twilio funcionarán con este conector.
	- **A número de teléfono**: el número de teléfono de destino. Formato aceptado: +, seguido por el código de país y, a continuación, el número de teléfono. Por ejemplo, +16175551212. Si omite el +, Twilio utilizará el código de país que escribió en “Desde” número.
	- **Texto**: el texto del mensaje que desea enviar.

	![][5] ![][6]

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE]Si desea empezar a trabajar con las aplicaciones lógicas de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-twilio/img1.PNG
[2]: ./media/app-service-logic-connector-twilio/img2.PNG
[3]: ./media/app-service-logic-connector-twilio/img3.png
[4]: ./media/app-service-logic-connector-twilio/img4.png
[5]: ./media/app-service-logic-connector-twilio/img5.PNG
[6]: ./media/app-service-logic-connector-twilio/img6.PNG

<!---HONumber=AcomDC_1203_2015-->