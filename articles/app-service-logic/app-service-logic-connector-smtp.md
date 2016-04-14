<properties
   pageTitle="Uso del conector de SMTP en Aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure"
   description="Creación y configuración del conector de SMTP o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/11/2016"
   ms.author="rajram"/>


# Introducción al conector de SMTP y su incorporación a su aplicación lógica
>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2014-12-01-preview de las aplicaciones lógicas. Para la versión de esquema 2015-08-01-preview, haga clic en [API de SMTP](../connectors/create-api-smtp.md).

Conéctese a un servidor SMTP para enviar correos electrónicos, incluidos mensajes con datos adjuntos. La acción "Enviar correo electrónico" del conector de SMTP permite enviar correo electrónico a las direcciones de correo electrónico especificadas.

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo de trabajo. Puede agregar el conector de SMTP a sus datos de flujo de trabajo empresarial y datos de proceso como parte de este flujo de trabajo en una aplicación lógica.


## Acciones y desencadenadores
Los *desencadenadores* son eventos que se producen. Por ejemplo, cuando se actualiza un pedido o cuando se agrega un cliente nuevo. Un *acción* es el resultado del desencadenador. Por ejemplo, cuando se actualice un pedido o se agregue un nuevo cliente, enviar un correo electrónico al nuevo cliente.

El conector de SMTP puede usarse como acción en una aplicación lógica y es compatible con datos en formato JSON y XML. Actualmente, no hay ningún desencadenador para este conector.

El conector de SMTP dispone de los siguientes desencadenadores y acciones:

Desencadenadores | Acciones
--- | ---
None | Enviar correo electrónico


## Creación del conector de SMTP
Un conector puede crearse dentro de una aplicación lógica o directamente desde Azure Marketplace. Para crear un conector desde Marketplace:

1. En el panel de inicio de Azure, seleccione **Marketplace**.
2. Seleccione **Aplicaciones de API** y busque "Conector de SMTP".
3. Haga clic en **Crear** para crear el conector.
4. Escriba el nombre, el plan del Servicio de aplicaciones y otras propiedades.
5. Escriba la siguiente configuración del paquete:

	Nombre | Obligatorio | Descripción
	--- | --- | ---
	User Name | Sí | Escriba un nombre de usuario que se puede conectar al servidor SMTP.
	Password | Sí | Escriba la contraseña del nombre de usuario.
	Dirección del servidor | Sí | Escriba el nombre o la dirección IP del servidor SMTP.
	Puerto del servidor | Sí | Escriba el número de puerto del servidor SMTP.
	Habilitación de SSL | No | Escriba *true* para usar SMTP a través de un canal seguro de SSL/TLS.

6. Seleccione **Crear**.

> [AZURE.IMPORTANT] Algunos servidores SMTP pueden tener problemas con el funcionamiento de este conector (SendGrid y Gmail). Si desea enviar correo desde SendGrid, nuestro [repositorio de GitHub](https://github.com/logicappsio/SendGridAPI) tiene una API personalizada que interactuará directamente con las API de SendGrid.

## Uso del conector de SMTP en la aplicación lógica
Una vez creado el conector, ahora puede usar el conector de SMTP como acción para la aplicación lógica. Para ello, siga estos pasos:

1.	Creación de una nueva aplicación lógica: 
	![][2]
2.	Abra **Desencadenadores y acciones** para abrir el diseñador de Aplicaciones lógicas y configurar el flujo de trabajo: 
	![][3]
3.	El conector de SMTP aparecerá en la sección "Aplicaciones de API de este grupo de recursos" en la galería, en el lado derecho. Selecciónelo: 
	![][4]
4.	Seleccione el conector de SMTP para agregarlo automáticamente al diseñador de flujo de trabajo.

Ahora puede configurar el conector de SMTP para usarlo en el flujo de trabajo. Seleccione la acción **Enviar correo electrónico** y configure las propiedades de entrada:

	Propiedad | Descripción
	--- | ---
	Para | Escriba la dirección de correo electrónica de los destinatarios. Separe las distintas direcciones de correo electrónico con puntos y comas (;). Por ejemplo, escriba: *recipient1@domain.com;recipient2@domain.com*.
	Cc | Escriba la dirección de correo electrónica de los destinatarios. Separe varias direcciones de correo electrónico mediante un punto y coma (;). Por ejemplo, escriba: *recipient1@domain.com;recipient2@domain.com*.
	Asunto | Especifique el asunto del correo electrónico.
	Cuerpo | Escriba el cuerpo del correo electrónico.
	Es HTML | Si esta propiedad está establecida en true, el contenido del cuerpo se envía como HTML.
	Bcc | Especifique la dirección de correo electrónico de los destinatarios para la copia de carbón oculta. Separe varias direcciones de correo electrónico mediante un punto y coma (;). Por ejemplo, escriba: *recipient1@domain.com;recipient2@domain.com*.
	Importancia | Especifique la importancia del correo electrónico. Las opciones son Normal, Baja y Alta.
	Datos adjuntos | Datos adjuntos que se deben enviar con el correo electrónico. Contiene los campos siguientes: Contiene los campos siguientes:<ul><li>Contenido (String)</li><li>Codificación de trasferencia de contenido (Enum) (“none”|”base64”)</li><li>Tipo de contenido (String)</li><li>ID de contenido (String)</li><li>Nombre de archivo (String)</li></ul>

![][5] 
![][6]

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE] Si desea empezar a trabajar con las aplicaciones lógicas de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-smtp/img1.PNG
[2]: ./media/app-service-logic-connector-smtp/img2.PNG
[3]: ./media/app-service-logic-connector-smtp/img3.png
[4]: ./media/app-service-logic-connector-smtp/img4.PNG
[5]: ./media/app-service-logic-connector-smtp/img5.PNG
[6]: ./media/app-service-logic-connector-smtp/img6.PNG

<!---HONumber=AcomDC_0224_2016-->