<properties
	pageTitle="Uso del conector de OneDrive en Aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure"
	description="Creación y configuración del conector de OneDrive o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure"
	authors="rajeshramabathiran"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="rajram"/>

# Introducción al conector de OneDrive y su incorporación a su aplicación lógica
Conéctese a su OneDrive para cargar, descargar y eliminar archivos. Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo. Puede agregar el conector de OneDrive a sus datos de flujo de trabajo empresarial y datos de proceso como parte de este flujo de trabajo en una aplicación lógica.

## Creación de un conector de OneDrive para la aplicación lógica ##
Para usar el conector de OneDrive, deberá crear primero una instancia de la aplicación de API del conector de OneDrive. Esto puede hacerse desde el diseñador de la aplicación lógica directamente o fuera de él. Para crear una instancia fuera del diseñador, proceda como sigue:

1.	Abra Azure Marketplace desde la página principal del Portal de Azure.
2.	En "Todo", busque "Conector de OneDrive".
3.	Configure el conector de OneDrive de la siguiente forma:

	![][1]
	- **Nombre**: asigne un nombre al conector de OneDrive.
	- **Plan del Servicio de aplicaciones**: seleccione o cree un plan del Servicio de aplicaciones.
	- **Nivel de precios**: elija un nivel de precios para el conector.
	- **Grupo de recursos**: seleccione o cree un grupo de recursos donde debe residir el conector.
	- **Suscripción**: elija la suscripción en la que desea crear el conector.
	- **Ubicación**: elija la ubicación geográfica donde desea que se implemente el conector.

4. Haga clic en Crear. Se creará un nuevo conector de OneDrive.
5. Una vez creada la instancia de aplicación de la API, puede crear una aplicación lógica en el mismo grupo de recursos para usar el conector de OneDrive.

## Uso del conector de OneDrive en la aplicación lógica ##
Una vez creada la aplicación de la API, ahora puede usar el conector de OneDrive como desencadenador/acción para la aplicación lógica. Para ello, necesita lo siguiente:

1.	Cree una nueva aplicación lógica y elija el mismo grupo de recursos que tiene el conector de OneDrive. Siga las instrucciones para [crear una nueva aplicación lógica].

2.	Abra "Desencadenadores y acciones" dentro de la aplicación lógica creada para abrir el Diseñador de aplicaciones lógicas y configure el flujo.

3.	El conector de OneDrive aparecerá en la sección "Aplicaciones de API en este grupo de recursos" en la galería, en el lado derecho.

	![][2]
4.	Puede quitar la aplicación de la API del conector de OneDrive en el editor haciendo clic en "Conector de OneDrive". Haga clic en el botón Autorizar. Proporcione la credenciales de Microsoft (si no ha iniciado sesión automáticamente). Haga clic en "Sí" para permitir el acceso.

	![][3]
	![][4]

5.	Ahora puede usar el conector de OneDrive en el flujo. Actualmente, los desencadenadores no están disponibles en el conector de OneDrive. Las acciones disponibles son: Obtener archivo, Cargar un archivo, Eliminar archivo y Listar archivos.

	![][5]

6.	Veamos el escenario de "Cargar archivo". Puede usar la acción de OneDrive "Cargar archivo" para cargar un archivo en su cuenta de OneDrive.

	![][6]

	Configure las propiedades de entrada para la acción "Cargar archivo" de la siguiente manera:

 - **Ruta de archivo**: especifique la ruta de acceso del archivo que se va a cargar.
 - **Contenido**: especifique el contenido del archivo que se va a cargar.
 - **Codificación de la transferencia de contenido**: especifique ninguna o Base64.
 - **Sobrescribir**: especifique "true" para sobrescribir el archivo si ya existe. Se trata de una propiedad avanzada.

7. Cuando se han configurado, la acción "Cargar un archivo" se configura y se puede utilizar en el flujo. De forma similar, pueden configurarse también otras acciones.

8. Para usar el conector fuera de una lógica de aplicación, pueden aprovecharse las API de REST expuestas por el conector. Puede ver estas definiciones de API con Examinar -> Aplicación de API -> Conector de OneDrive. Ahora haga clic en el modo de definición de API en la sección de resumen para ver todas las API expuestas por este conector.

	![][7]

9. Los detalles de las API se pueden encontrar en [Definición de la API de OneDrive].

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE]Si desea empezar a trabajar con las aplicaciones lógicas de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-onedrive/img1.PNG
[2]: ./media/app-service-logic-connector-onedrive/img2.PNG
[3]: ./media/app-service-logic-connector-onedrive/img3.PNG
[4]: ./media/app-service-logic-connector-onedrive/img4.PNG
[5]: ./media/app-service-logic-connector-onedrive/img5.PNG
[6]: ./media/app-service-logic-connector-onedrive/img6.PNG
[7]: ./media/app-service-logic-connector-onedrive/img7.PNG

<!-- Links -->
[crear una nueva aplicación lógica]: app-service-logic-create-a-logic-app.md
[Definición de la API de OneDrive]: https://msdn.microsoft.com/library/dn974227.aspx

<!---HONumber=Nov15_HO3-->