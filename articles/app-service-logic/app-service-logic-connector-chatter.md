<properties
   pageTitle="Uso del conector de Chatter en Aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure"
   description="Creación y configuración del conector de Chatter o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure"
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


# Introducción al conector de Chatter y su incorporación a su aplicación lógica 
Conéctese a Chatter y publique un mensaje o busque una fuente. Por ejemplo, puede buscar una fuente de Chatter y cuando encuentre algo específico, puede publicar ese mensaje de Chatter en un grupo de ventas.

Puede agregar el conector de Chatter a sus datos de flujo de trabajo empresarial y datos de proceso como parte de este flujo de trabajo en una aplicación lógica.

## Acciones y desencadenadores

Un desencadenador inicia una nueva instancia en función de un evento específico, como la llegada de un nuevo mensaje de Chatter. Una acción es el resultado, como después de recibir un nuevo mensaje de Chatter, publicarlo en otro grupo de Chatter u otro sitio de medio social, como Facebook o Twitter.

El conector de Chatter puede usarse como desencadenador o como acción en una aplicación lógica y es compatible con datos en formato JSON y XML. El conector de Chatter dispone de los siguientes desencadenadores y acciones:

Desencadenadores | Acciones
--- | ---
Nuevo mensaje | <ul><li>Publicar mensaje</li><li>Buscar</li></ul>


## Creación del conector de Chatter para la aplicación lógica
Un conector puede crearse dentro de una aplicación lógica o directamente desde Azure Marketplace. Para crear un conector desde Marketplace:

1. En el panel de inicio de Azure, seleccione **Marketplace**.
2. Busque "Conector de Chatter", selecciónelo y seleccione **Crear**.
3. Escriba el nombre, el plan del Servicio de aplicaciones y otras propiedades: ![][1]  
	- **Ubicación**: elija la ubicación geográfica en la que desea implementar el conector.
	- **Suscripción**: elija una suscripción en la que desee crear este conector.
	- **Grupo de recursos**: seleccione o cree un grupo de recursos en el que vaya a estar el conector.
	- **Plan de hospedaje web**: seleccione o cree un plan de hospedaje web.
	- **Nivel de precios**: elija un nivel de precios para el conector.
	- **Nombre**: asigne un nombre al conector de Chatter.

4. Seleccione **Crear**.


## Uso del conector de Chatter en la aplicación lógica
Una vez creada la aplicación de API, ahora puede usar el conector de Chatter como desencadenador o en la aplicación lógica. Para ello, siga estos pasos:

1. En la aplicación lógica, abra **Desencadenadores y acciones** para abrir el diseñador de Aplicaciones lógicas y configure el flujo.

2. El conector de Chatter se muestra en la galería: ![][4]
3. Seleccione el conector de Chatter para agregar automáticamente en el diseñador. Seleccione **Autorizar**, escriba sus credenciales y seleccione **Permitir**: ![][5] ![][6] ![][7]

Ahora puede usar el conector de Chatter en el flujo. Puede usar el nuevo mensaje recuperado desde el desencadenador de Chatter ("Mensaje nuevo") en otras acciones del flujo. Configure las propiedades de entrada para el desencadenador de Chatter de la forma siguiente:

**Identificador de grupo**: escriba el identificador del grupo desde el que se va a recuperar el nuevo mensaje. Si no se proporciona el identificador de grupo, el nuevo mensaje se recupera de la fuente del usuario: ![][8] ![][9]


En la forma similar puede usar la acción Chatter en el flujo para enviar un mensaje mediante la selección de la acción "Enviar mensaje". Configure las propiedades de entrada para la acción "Publicar mensaje" de la siguiente manera: - **Texto del mensaje**: contenido de texto del mensaje que se va a publicar -**Id. de grupo**: especifique el identificador del grupo en el que se va publicar el nuevo mensaje. Si no se proporciona el identificador de grupo, el mensaje se publicará en la fuente del usuario. -**Nombre de archivo**: nombre del archivo que se va a adjuntar al mensaje -**Catos de contenido**: datos de contenido de los datos adjuntos -**Tipo de contenido**: tipo de contenido de los datos adjuntos -**Codificación de transferencia de contenido**: codificación de transferencia del contenido de los datos adjuntos (“none”|”base64”) - **Menciones**: matriz de los nombres de usuario que se van a etiquetar en este mensaje - **Hashtags**: matriz de los hashtags que se van a publicar junto con el mensaje

![][10] ![][11]

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE]Si desea empezar a trabajar con las aplicaciones lógicas de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-chatter/img1.PNG
[2]: ./media/app-service-logic-connector-chatter/img2.PNG
[3]: ./media/app-service-logic-connector-chatter/img3.png
[4]: ./media/app-service-logic-connector-chatter/img4.png
[5]: ./media/app-service-logic-connector-chatter/img5.PNG
[6]: ./media/app-service-logic-connector-chatter/img6.PNG
[7]: ./media/app-service-logic-connector-chatter/img7.PNG
[8]: ./media/app-service-logic-connector-chatter/img8.PNG
[9]: ./media/app-service-logic-connector-chatter/img9.PNG
[10]: ./media/app-service-logic-connector-chatter/img10.PNG
[11]: ./media/app-service-logic-connector-chatter/img11.PNG

<!---HONumber=AcomDC_1203_2015-->