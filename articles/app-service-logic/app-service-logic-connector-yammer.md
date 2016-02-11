<properties
   pageTitle="Uso del conector de Yammer en Aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure"
   description="Creación y configuración del conector de Yammer o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure"
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


# Uso del conector de Yammer en la aplicación lógica #
Conéctese a Yammer y ejecute la acción Publicar mensaje y un desencadenador para recuperar el mensaje nuevo.

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo.

## Creación de un conector de Yammer para la aplicación lógica ##
Para usar el conector de Yammer, deberá crear primero crear una instancia de la aplicación de API del conector de Yammer. Se puede hacer de la forma siguiente:

1. En el panel de inicio de Azure, seleccione **Marketplace**.
2. Busque "Conector de Yammer", selecciónelo y seleccione **Crear**.
3. Configure el conector de Yammer de la siguiente forma: ![][1]

	- **Ubicación**: elija la ubicación geográfica en la que desea implementar el conector.
	- **Suscripción**: elija una suscripción en la que desee crear este conector.
	- **Grupo de recursos**: seleccione o cree un grupo de recursos en el que vaya a estar el conector.
	- **Plan del Servicio de aplicaciones**: seleccione o cree un plan de hospedaje web
	- **Nivel de precios**: elija un nivel de precios para el conector.
	- **Nombre**: asigne un nombre al conector de Yammer.

4. Haga clic en Crear. Se crea un nuevo conector de Yammer.
5. Una vez creada la instancia de aplicación de la API, puede crear una aplicación lógica para usar el conector de Yammer.

## Uso del conector de Yammer en la aplicación lógica ##
Una vez creada la aplicación de la API, ahora puede usar el conector de Yammer como desencadenador/acción para la aplicación lógica. Para ello, necesita lo siguiente:

1.	Cree una nueva aplicación lógica: ![][2]

2.	Abra "Desencadenadores y acciones" para abrir el Diseñador de aplicaciones lógicas y configure el flujo: ![][3]

3.	El conector de Yammer aparecerá en la sección "Aplicaciones de la API en este grupo de recursos" de la galería, en el lado derecho: ![][4]

4. Puede colocar la aplicación de la API del conector de Yammer en el editor haciendo clic en "Conector de Yammer". Haga clic en el botón Autorizar. Proporcione las credenciales de Yammer. Haga clic en “Permitir”: ![][5] ![][6] ![][7]

Ahora puede usar el conector de Yammer en el flujo.

## Uso del conector de Yammer como desencadenador

Puede utilizar el nuevo mensaje recuperado desde el desencadenador de Yammer ("Nuevo mensaje") en otras acciones en el flujo. Configure las propiedades de entrada del desencadenador de Yammer de la siguiente manera:

- **Id. de grupo**: el Id. del grupo desde el que se debe recuperar el nuevo mensaje. Si no se proporciona el Id. de grupo, el mensaje se recuperarán de la fuente siguiente. El Id. de grupo se puede recuperar desde la dirección URL de grupo en Yammer.
		
	Ejemplo: el Id. de grupo en la siguiente dirección URL es "5453203": https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203 ![][8] ![][9]

## Uso del conector de Yammer para publicar un mensaje

También puede utilizar el conector de Yammer como una acción en las aplicaciones lógicas. En primer lugar, especifique un desencadenador para la aplicación lógica o marque “ejecutar esta lógica manualmente” (como se muestra a continuación). Agregue el conector de Yammer, autorice según corresponda y elija la acción "Publicar mensaje". Configure las propiedades de entrada de la acción "Publicar mensaje" de la siguiente manera:

- **Texto del mensaje**: contenido de texto del mensaje que se va a enviar.
- **Id. de grupo**: especifique el identificador del grupo al que se va a enviar el nuevo mensaje. Si no se proporciona el Id. de grupo, el mensaje se enviará a toda la fuente de la compañía. El Id. de grupo se puede recuperar desde la dirección URL de grupo en Yammer.  

	Ejemplo: el Id. de grupo en la siguiente dirección URL es "5453203": https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203
- 	**Usuarios de etiquetas**: matriz de nombres de red de usuario que tiene que etiquetarse en el mensaje: ![][10] ![][11]

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE]Si desea empezar a trabajar con las aplicaciones lógicas de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-yammer/img1.PNG
[2]: ./media/app-service-logic-connector-yammer/img2.PNG
[3]: ./media/app-service-logic-connector-yammer/img3.png
[4]: ./media/app-service-logic-connector-yammer/img4.png
[5]: ./media/app-service-logic-connector-yammer/img5.PNG
[6]: ./media/app-service-logic-connector-yammer/img6.PNG
[7]: ./media/app-service-logic-connector-yammer/img7.png
[8]: ./media/app-service-logic-connector-yammer/img8.PNG
[9]: ./media/app-service-logic-connector-yammer/img9.PNG
[10]: ./media/app-service-logic-connector-yammer/img10.PNG
[11]: ./media/app-service-logic-connector-yammer/img11.PNG

<!---HONumber=AcomDC_1203_2015-->