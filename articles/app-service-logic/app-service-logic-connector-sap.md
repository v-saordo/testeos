<properties
   pageTitle="Uso del conector de SAP en Aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure"
   description="Creación y configuración del conector de SAP o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="harishkragarwal"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/18/2016"
   ms.author="sameerch"/>


# Introducción al conector de SAP y su incorporación a las aplicaciones lógicas
>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2014-12-01-preview de las aplicaciones lógicas.

Conéctese a SAP local para RFC u obtener metadatos. Hay escenarios donde puede que necesite trabajar con SAP, que se instala localmente y detrás del firewall. El uso del conector de SAP en su flujo de trabajo le permitir conseguir distintos resultados. Ejemplos:

1.	Exponer una parte de los datos que residen en SAP a través de un front-end de usuario móvil o web.
2.	Publicar datos en SAP después del correspondiente procesamiento.
3.	Extraer datos de SAP para su uso en un proceso empresarial.

Los conectores pueden utilizarse en aplicaciones lógicas para capturar, procesar o insertar datos como parte de un flujo. Puede agregar el conector de SAP a sus datos de flujo de trabajo empresarial y datos de proceso como parte de este flujo de trabajo en una aplicación lógica.


Para estos escenarios, deben cumplirse las siguientes condiciones:

1. Creación de una instancia de la aplicación de API del conector de SAP
2. Establecimiento de conectividad híbrida para la comunicación de la aplicación de API con el SAP local
3. Utilización de la aplicación de API creada en una aplicación lógica para lograr el proceso empresarial deseado


## Creación de una instancia de la aplicación de API del conector de SAP ##

Un conector puede crearse dentro de una aplicación lógica o directamente desde Azure Marketplace. Para crear un conector desde Marketplace:

1. En el panel de inicio de Azure, seleccione **Marketplace**.
2. Busque "Conector de SAP", selecciónelo y seleccione **Crear**.
3. Configúrelo de la siguiente forma:
	1. Proporcione detalles genéricos, por ejemplo, el nombre, el plan de servicio de aplicación, etc., en la primera hoja.

	2. Como parte de la configuración del paquete, deberá proporcionar las credenciales de SAP. Proporcione también una cadena de conexión de Bus de servicio de Azure. Esta se usa para establecer conectividad híbrida con el SAP local.

	3. RFC, TRFC, BAPI e IDOC deben configurarse según la necesidad del escenario. Si hay que proporcionar varios valores, se pueden separar con comas.

![][1]

## Configuración la aplicación de API de conector de SAP recién creada ##

Acceda a la aplicación de API que acaba de crear mediante Examinar -> Aplicaciones de API -> <Name of the API App just created>. Verá el siguiente comportamiento. La instalación está incompleta, ya que todavía no se ha establecido la conexión híbrida: ![][2]

El conector SAP requiere conectividad híbrida para conectarse a *cualquier* extremo SAP. Para establecer la conectividad híbrida, haga lo siguiente:

1. Copie la cadena de conexión principal.
2. Haga clic en el vínculo 'Descargar y configurar’.
3. Siga el proceso de instalación que se inicia y especifique la cadena de conexión principal cuando se le pida.
4. Una vez que se completa el proceso de instalación, se muestra un cuadro de diálogo similar al siguiente: ![][3]

Más información sobre la [integración con un servidor SAP local](app-service-logic-integrate-with-an-on-premise-sap-server.md).

Ahora, cuando acceda de nuevo a la aplicación de API que ha creado, verá que el estado de la conexión híbrida se muestra como "conectado": ![][4]

Nota: si desea cambiar a la cadena de conexión secundaria, solo tiene que volver a realizar la configuración híbrida y proporcionar la cadena de conexión secundaria en lugar de la cadena de conexión principal.

## Uso en una aplicación lógica ##

El conector de SAP puede usarse como una acción/paso únicamente en una aplicación lógica.

Al crear o editar una aplicación lógica, elija la aplicación de API del conector de SAP que ha creado anteriormente. Con esto se mostrarán todas las acciones permitidas, entre las que podrá elegir: ![][5]

Tras seleccionar una acción, se mostrará una lista de parámetros de entrada para esa acción. Proporcione los valores adecuados y haga clic en el icono de la marca: ![][6]

La acción o paso aparece ahora como configurado en la aplicación lógica. Se mostrarán las salidas de la operación y se podrán usar como entradas en un paso posterior: ![][7]

Complete la aplicación lógica para definir el proceso empresarial y, a continuación, ejecútelo para lograr el objetivo deseado.

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE] Si desea empezar a trabajar con las aplicaciones lógicas de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-sap/Create.jpg
[2]: ./media/app-service-logic-connector-sap/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-sap/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-sap/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-sap/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-sap/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-sap/LogicApp3.jpg

<!---HONumber=AcomDC_0224_2016-->