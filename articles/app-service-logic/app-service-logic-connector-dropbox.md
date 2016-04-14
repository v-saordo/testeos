<properties
	pageTitle="Uso del conector de Dropbox en Aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure"
	description="Creación y configuración del conector de Dropbox o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure"
	authors="anuragdalmia"
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
	ms.author="sameerch"/>

# Introducción al conector de Dropbox y su incorporación a su aplicación lógica
>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2014-12-01-preview de las aplicaciones lógicas. Para la versión de esquema 2015-08-01-preview, haga clic en [API de Dropbox](../connectors/create-api-dropbox.md).

Conéctese a la cuenta de Dropbox para cargar o descargar archivos. Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos. Puede agregar el conector de Dropbox a sus datos de flujo de trabajo empresarial y datos de proceso como parte de este flujo de trabajo en una aplicación lógica.

## Acciones y desencadenadores

Un desencadenador inicia una nueva instancia en función de un evento específico, como la llegada de un nuevo mensaje. Una acción es el resultado, por ejemplo después de recibir un nuevo mensaje cargar el archivo en Dropbox.

El conector de Dropbox puede usarse como acción en una aplicación lógica y es compatible con datos en formato JSON y XML. El conector de Dropbox dispone de los siguientes desencadenadores y acciones:

Desencadenadores | Acciones
--- | ---
None | <ul><li>Eliminar archivo</li><li>Obtener archivo</li><li>Cargar archivo</li><li>Enumerar archivos</li></ul>


## Creación de un conector de Dropbox para la aplicación lógica
Un conector puede crearse dentro de una aplicación lógica o directamente desde Azure Marketplace. Para crear un conector desde Marketplace:

1. En el panel de inicio de Azure, seleccione **Marketplace**.
2. Busque "Conector de Dropbox", selecciónelo y seleccione **Crear**.
3. Escriba el nombre, el plan de Servicio de aplicaciones y otras propiedades: 
	![][1]
	- **Ubicación**: elija la ubicación geográfica en la que desea implementar el conector.
	- **Suscripción**: elija una suscripción en la que desee crear este conector.
	- **Grupo de recursos**: seleccione o cree un grupo de recursos en el que vaya a estar el conector.
	- **Plan del Servicio de aplicaciones**: seleccione o cree un plan de hospedaje web
	- **Nivel de precios**: elija un nivel de precios para el conector.
	- **Nombre**: asigne un nombre al conector de Dropbox  
4. Seleccione **Crear**.


## Uso del conector de Dropbox en la aplicación lógica
Una vez creada la aplicación de la API, ahora puede usar el conector de Dropbox como desencadenador/acción para la aplicación lógica. Para ello, siga estos pasos:

1.	En la aplicación lógica, abra **Desencadenadores y acciones** para abrir el diseñador de Aplicaciones lógicas y configure el flujo: 
	![][3]
2.	El conector de Dropbox se muestra en la galería: 
	![][4]
3.	Seleccione el conector de Dropbox para agregar automáticamente en el diseñador. Seleccione **Autorizar**, escriba sus credenciales y seleccione **Permitir**: 
	![][5] 
	![][6] 
	![][7]

Ahora puede usar el conector de Dropbox en el flujo. Puede usar la acción de Dropbox "Cargar archivo" para cargar un archivo en su cuenta de Dropbox: 
	![][8] 
	![][9]

Configure las propiedades de entrada para la acción "Cargar archivo" de la siguiente manera:

- **Ruta de archivo**: especifique la ruta de acceso del archivo de Dropbox de destino que se va a cargar. Ejemplo: Fotos/imagen.png
- **Contenido**: especifique el contenido del archivo que se va a cargar. A menudo, esto deriva de un paso anterior en la aplicación lógica.
- **Codificación de la transferencia de contenido**: especifique ninguna o Base64.
- **Sobrescribir**: especifique "true" para sobrescribir el archivo si ya existe.

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE] Si desea empezar a trabajar con las aplicaciones lógicas de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-dropbox/img1.PNG
[2]: ./media/app-service-logic-connector-dropbox/img2.PNG
[3]: ./media/app-service-logic-connector-dropbox/img3.png
[4]: ./media/app-service-logic-connector-dropbox/img4.png
[5]: ./media/app-service-logic-connector-dropbox/img5.PNG
[6]: ./media/app-service-logic-connector-dropbox/img6.PNG
[7]: ./media/app-service-logic-connector-dropbox/img7.PNG
[8]: ./media/app-service-logic-connector-dropbox/img8.PNG
[9]: ./media/app-service-logic-connector-dropbox/img9.PNG

<!---HONumber=AcomDC_0224_2016-->