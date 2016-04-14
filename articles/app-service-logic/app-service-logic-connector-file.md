<properties
	pageTitle="Uso del conector de archivos en Aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure"
	description="Creación y configuración del conector de archivos o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure"
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
	ms.date="02/10/2016"
	ms.author="rajram"/>

# Introducción al conector de archivos y su incorporación a su aplicación lógica
>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2014-12-01-preview de las aplicaciones lógicas.

Conéctese a un sistema de archivos para cargar, descargar los archivos en un equipo host, etc. Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos. Puede agregar el conector de archivos a sus datos de flujo de trabajo empresarial y datos de proceso como parte de este flujo de trabajo en una aplicación lógica.

El conector de archivos usa el Administrador de conexiones híbridas para la conectividad híbrida con el sistema de archivos host.

## Creación de un conector de archivos para la aplicación lógica ##
Para usar el conector de archivos, deberá crear primero una instancia de la aplicación de API del conector de archivos. Se puede hacer de la forma siguiente:

1.	Abra Azure Marketplace mediante la opción +NUEVO en la parte izquierda del Portal de Azure.
2.	Vaya a "Marketplace > Aplicaciones de API" y busque "Conector de archivos".
3.	Configure el conector de archivos de la siguiente forma: ![][1]

	- **Nombre**: asigne un nombre al conector de archivos.
	- **Configuración del paquete**
		- **Carpeta raíz**: especifique la ruta de acceso de la carpeta raíz en el equipo host. Ejemplo: D:\\FileConnectorTest
		- **Cadena de conexión del Bus de servicio**: proporcione una cadena de conexión del Bus de servicio. Asegúrese de que el espacio de nombres del Bus de servicio es de tipo estándar y NO básico para poder usar las retransmisiones de bus de servicio. La Retransmisión de bus de servicio se utiliza para conectarse al Administrador de conexiones híbridas.
	- **Plan de servicio de aplicaciones**: seleccione o cree un plan de Servicio de aplicaciones.
	- **Nivel de precios**: elija un nivel de precios para el conector.
	- **Grupo de recursos**: seleccione o cree un grupo de recursos en el que vaya a estar el conector.
	- **Suscripción**: elija una suscripción en la que desee crear este conector.
	- **Ubicación**: elija la ubicación geográfica en la que desea implementar el conector.

4. Haga clic en Crear. Se creará un nuevo conector de archivos.

## Configuración del Administrador de conexiones híbridas ##
Una vez creada la instancia de aplicación de API, vaya a su panel. Para ello, haga clic en Examinar > Aplicaciones de API > Seleccione la aplicación de API del conector de archivos. Desde aquí debe configurarse el Administrador de conexiones híbridas. Para obtener más información sobre cómo configurar el Administrador de conexiones híbridas y solucionar sus problemas, consulte [Uso del Administrador de conexiones híbridas].

## Uso del conector de archivos en la aplicación lógica ##
Una vez creada la aplicación de la API, ahora puede usar el conector de archivos como desencadenador/acción para la aplicación lógica. Para ello, necesita lo siguiente:

1.	Cree una nueva aplicación lógica y elija el mismo grupo de recursos que tiene el conector de archivos. Siga las instrucciones para [crear una nueva aplicación lógica].

2.	Abra "Desencadenadores y acciones" dentro de la aplicación lógica creada para abrir el Diseñador de aplicaciones lógicas y configure el flujo.

3.	El conector de archivos aparecerá en la sección "Aplicaciones de API en este grupo de recursos" en la galería, en el lado derecho.

4.	Puede quitar la aplicación de la API del conector de archivos en el editor haciendo clic en "Conector de archivos". El conector de archivos muestra un desencadenador y cuatro acciones: ![][5]

6.	Cada uno de ellos muestra algunas propiedades. La imagen siguiente enumera las propiedades para el desencadenador y para la acción Obtener archivo: ![][6]

7. Cuando se han configurado, el desencadenador y la acción pueden utilizarse en el flujo. De forma similar, pueden configurarse también otras acciones.

> [AZURE.NOTE] El desencadenador de archivo eliminará el archivo después de leerlo correctamente desde la carpeta.

## API de REST del conector de archivos ##
Para usar el conector fuera de una lógica de aplicación, pueden aprovecharse las API de REST expuestas por el conector. Puede ver estas definiciones de API con Examinar -> Aplicación de API -> Conector de archivos. Ahora haga clic en el modo de definición de API en la sección de resumen para ver todas las API expuestas por este conector: ![][7]

Los detalles de las API se pueden encontrar en [Definición de la API del conector de archivos].

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE] Si desea empezar a trabajar con las aplicaciones lógicas de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[crear una nueva aplicación lógica]: app-service-logic-create-a-logic-app.md
[Definición de la API del conector de archivos]: https://msdn.microsoft.com/library/dn936296.aspx
[Uso del Administrador de conexiones híbridas]: app-service-logic-hybrid-connection-manager.md

<!---HONumber=AcomDC_0224_2016-->