<properties 
	pageTitle="Uso del conector de Slack en Aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure"
	description="Creación y configuración del conector de Slack o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure"
	authors="rajeshramabathiran" 
	manager="erikre" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="rajram"/>

# Introducción al conector de Slack y su incorporación a su aplicación lógica
>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2014-12-01-preview de las aplicaciones lógicas. Para la versión de esquema 2015-08-01-preview, haga clic en [API de Slack](../connectors/create-api-slack.md).

Conéctese a los canales de Slack y publique mensajes para su equipo. Los conectores pueden usarse en aplicaciones lógicas como parte de un "flujo de trabajo" para realizar diferentes tareas. El uso del conector de Slack en su flujo de trabajo, en combinación con otros conectores, puede permitirle conseguir diversos resultados. Por ejemplo, puede usar el [conector de Facebook](app-service-logic-connector-facebook.md) en el flujo de trabajo para publicar un mensaje en el canal de Slack.

## Acciones y desencadenadores
Los *desencadenadores* son eventos que se producen. Por ejemplo, cuando se actualiza un pedido o cuando se agrega un cliente nuevo. Un *acción* es el resultado del desencadenador. Por ejemplo, cuando se actualiza un pedido,se envía una alerta al vendedor. O bien, cuando se agrega un nuevo cliente, a este se le envía un correo electrónico de bienvenida.

El conector de Slack puede usarse como acción en una aplicación lógica y es compatible con datos en formato JSON y XML. Actualmente, no hay ningún desencadenador disponible para el conector de Slack.

El conector de Slack dispone de los siguientes desencadenadores y acciones:

Desencadenadores | Acciones
--- | ---
None | Publicar mensaje

## Creación del conector de Slack
Un conector puede crearse dentro de una aplicación lógica o directamente desde Azure Marketplace. Para crear un conector desde Marketplace:

1. En el panel de inicio de Azure, seleccione **Marketplace**.
2. Seleccione **Aplicaciones de API** y busque "Conector de Slack".
3. Escriba el nombre, el plan de Servicio de aplicaciones y otras propiedades: 
	![][1] 

4. Haga clic en **Crear**.

## Uso del conector como acción en la aplicación lógica

> [AZURE.IMPORTANT] Los conectores y las aplicaciones lógicas siempre deben crearse en el mismo grupo de recursos.

Una vez creado el conector de Slack, puede agregarlo como acción a su aplicación lógica:

1.	En la aplicación lógica, abra **Desencadenadores y acciones**. [Creación de una nueva aplicación lógica](app-service-logic-create-a-logic-app.md)

2.	El conector de Slack aparece en la galería en el lado derecho: 
	![][2]

3.	Seleccione el conector de Slack que creó para añadirlo automáticamente a la aplicación lógica.
4.	Seleccione **Autorizar**. Inicie sesión en su cuenta de Slack. Hacia el final, se le pedirá que conceda permiso de acceso al conector a su cuenta de Slack. Seleccione **Autorizar**: 
	![][3] 
	![][4] 
	![][5] 
	![][6]  
	
5.	Ahora puede usar el conector Slack en el flujo. Está disponible la acción Publicar mensaje: 
	![][7]


Vamos a explicar el proceso para "Publicar mensaje". Puede usar esta acción para publicar un mensaje en cualquier canal de Slack: 
	![][8]

Configure las propiedades de entrada de la acción "Publicar mensaje" de la forma siguiente:

Propiedad | Descripción
--- | ---
Texto | Escriba el texto del mensaje que se va a publicar.
Nombre del canal | Especifique el canal de Slack donde se publica este mensaje. Si no se especifica el canal, el mensaje se registra en #general.
Propiedades avanzadas | **Nombre de usuario de bot**: nombre del bot que se usará en este mensaje. Si no se especifica, los mensajes se publicarán como "Bot".<p><p>**Dirección URL de icono**: la dirección URL de la imagen que se usará como icono en este mensaje.<p><p>**Emoji de icono**: Emoji que se usará como icono en este mensaje. Esta propiedad reemplaza la propiedad Dirección URL de icono.


El conector de Slack dispone de API de REST para que pueda usar el conector fuera de la aplicación lógica. Abra el conector de Slack y seleccione **Definición de API**: 
	![][9]


## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE] Si desea empezar a trabajar con las aplicaciones lógicas de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-slack/img1.PNG
[2]: ./media/app-service-logic-connector-slack/img2.PNG
[3]: ./media/app-service-logic-connector-slack/img3.PNG
[4]: ./media/app-service-logic-connector-slack/img4.PNG
[5]: ./media/app-service-logic-connector-slack/img5.PNG
[6]: ./media/app-service-logic-connector-slack/img6.PNG
[7]: ./media/app-service-logic-connector-slack/img7.PNG
[8]: ./media/app-service-logic-connector-slack/img8.PNG
[9]: ./media/app-service-logic-connector-slack/img9.PNG

<!---HONumber=AcomDC_0224_2016-->