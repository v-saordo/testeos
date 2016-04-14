<properties
	pageTitle="Lista de conectores y aplicaciones de API disponibles | Servicio de aplicaciones de Microsoft Azure"
	description="Obtenga información acerca de los conectores y las aplicaciones de API de Servicio de aplicaciones de Azure"
	services="app-service\logic"
	documentationCenter=""
	authors="MandiOhlinger"
	manager="erikre"
	editor="cgronlun"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/11/2016"
	ms.author="mandia"/>


# Lista de conectores y aplicaciones de API para usarlas en sus aplicaciones lógicas
>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2014-12-01-preview de las aplicaciones lógicas. Para consultar la versión de esquema 2015-08-01-preview, haga clic en la opción [Lista de API](../connectors/apis-list.md).

Obtenga información acerca de todos los conectores disponibles y las aplicaciones de API creados por Microsoft para usarlos con Aplicaciones lógicas.

Para obtener información y una lista de lo que se incluye con cada nivel de servicio de precios, consulte [Precios del Servicio de aplicaciones de Azure](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Si desea empezar a utilizar Aplicaciones lógicas de Azure antes de suscribirse para obtener una cuenta de Azure, vaya a [Probar Servicio de aplicaciones](https://tryappservice.azure.com/?appservice=logic). Podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Conectores principales
En la siguiente tabla se enumeran todos los conectores y las aplicaciones de API disponibles creados por Microsoft, disponibles como conectores principales:

Nombre | Descripción
--- | ---
[HDInsight de Azure](app-service-logic-connector-hdinsight.md) | Use este conector para crear un clúster de Hadoop en Azure, enviar diferentes trabajos de Hadoop y mucho más.
[Bus de servicio de Azure](app-service-logic-connector-azureservicebus.md) | Puede enviar mensajes desde los temas y las colas del Bus de servicio y recibir mensajes de suscripciones y colas del Bus de servicio.
[Blob de almacenamiento de Azure](app-service-logic-connector-azurestorageblob.md) | Se conecta al almacenamiento de blobs y puede realizar las operaciones de obtener, eliminar, enumerar, etc. 
[Traductor de Bing](https://azure.microsoft.com/marketplace/partners/microsoft_com/bingtranslator) | Use Bing para traducir texto en otro idioma.
[Box](app-service-logic-connector-box.md) | Se conecta a Box y puede realizar las operaciones de cargar, obtener, eliminar, enumerar y más tareas de archivos.
[Chatter](app-service-logic-connector-chatter.md) | Se conecta a Chatter y puede publicar mensajes, buscar e incluso recuperar mensajes nuevos.
[Dropbox](app-service-logic-connector-dropbox.md) | Se conecta a Dropbox y puede realizar las operaciones de obtener, eliminar, enumerar y más tareas de archivos.
[Facebook](app-service-logic-connector-facebook.md) | Se conecta a Facebook y puede publicar mensajes, imágenes, etc. También puede obtener mensajes y comentarios, obtener la información de usuario sobre gustos diferentes, incluidos libros y películas.
[HTTP](app-service-logic-connector-http.md) | El agente de escucha HTTP abre un extremo que actúa como servidor HTTP y escucha las solicitudes HTTP o HTTPS entrantes. La acción HTTP no requiere una aplicación de API y se admite de forma nativa en las aplicaciones lógicas.
[Microsoft Office 365](app-service-logic-connector-office365.md) | El conector Office 365 puede enviar y recibir correos electrónicos, administrar su calendario y administrar sus contactos con su cuenta de Office 365.
[Microsoft OneDrive](app-service-logic-connector-onedrive.md) | Se conecta a su Microsoft OneDrive personal y cargue, elimine, mostrar archivos, etc.
[Microsoft SharePoint](app-service-logic-connector-sharepoint.md) | Se conecta a Microsoft SharePoint Server o SharePoint Online local, administra documentos y enumera elementos. Se admiten métodos de autenticación diferentes, como credenciales predeterminadas, OAuth 2.0, autenticación de Windows y autenticación basada en formularios.
[Microsoft Yammer](app-service-logic-connector-yammer.md) | Se conecta a Yammer para publicar mensajes y obtener nuevos mensajes.
[QuickBooks](app-service-logic-connector-quickbooks.md) | Puede completar tareas diferentes incluidas crear, actualizar y consultar diferentes entidades de Intuit QuickBooks como clientes, artículos, facturas, etc.
[Slack](app-service-logic-connector-slack.md) | Conéctese a Slack y publique mensajes en los canales de Slack.
[Salesforce](app-service-logic-connector-salesforce.md) | Se conecta a su cuenta de Salesforce y puede administrar cuentas, clientes potenciales, oportunidades y mucho más.
[SugarCRM](app-service-logic-connector-sugarcrm.md) | Se conecta a SugarCRM en línea y puede crear, actualizar, leer, etc. con diferentes tipos de módulos, como cuentas, contactos, etc.
[Twilio](app-service-logic-connector-twilio.md) | Se conecta a Twilio y puede enviar y recibir mensajes, obtener los números disponibles, administrar los números de teléfono entrantes y mucho más.
[Twitter](app-service-logic-connector-twitter.md) | Se conecta a Twitter y puede obtener escalas de tiempo, publicar tweets, etc.
[Esperar](app-service-logic-connector-wait.md) | Utilice este conector para retrasar la ejecución de la aplicación. Puede retrasar la aplicación durante un tiempo determinado o hasta una repetición en un momento determinado.


## Conectores de integración de empresa
En la siguiente tabla se enumeran todos los conectores y las aplicaciones de API creados por Microsoft disponibles como conectores de integración de empresa:

Nombre | Descripción
------------- | -------------
[Conector AS2](app-service-logic-connector-as2.md) | Puede recibir y enviar mensajes mediante el protocolo de transporte de AS2. Los datos se transportan de manera segura y confiable a través de cifrado y certificados digitales.
[BizTalk EDIFACT](app-service-logic-connector-edifact.md) | Recibe y envía mensajes mediante el protocolo EDIFACT en comunicaciones negocio a negocio.
[Codificador de archivos sin formato de BizTalk](app-service-logic-flatfile-encoder.md) | Proporciona interoperabilidad entre los datos de archivo sin formato (como excel y csv) y los datos XML. Esta aplicación de API puede convertir un archivo sin formato a XML y viceversa.
[Codificador JSON de BizTalk](app-service-logic-connector-jsonencoder.md) | Un codificador y descodificador que ayuda a la interoperabilidad de la aplicación entre los datos JSON y XML. Puede convertir una instancia JSON en XML y viceversa.
[Reglas de BizTalk](app-service-logic-use-biztalk-rules.md) | Usa las reglas de BizTalk para definir y controlar la lógica de negocios dentro de una organización. Las directivas empresariales se pueden actualizar sin volver a recopilar o sin volver a implementar las aplicaciones asociadas.
[Administración de socios comerciales de BizTalk](app-service-logic-connector-tpm.md) | Define y conserva las relaciones negocio a negocio con socios, acuerdos y esquemas y certificados que se usan en los contratos. Estas relaciones se hacen valer a través de aplicaciones de API de AS2, EDIFACT y X12.
[Servicio de transformación de BizTalk](app-service-logic-transform-xml-documents.md) | Convierte datos de un formato a otro. También puede cargar un mapa existente (archivo .trfm), ver los vínculos entre los esquemas de origen y destino y usar la funcionalidad 'Probar’ con un contenido XML de entrada de ejemplo. También hay disponibles diferentes funciones integradas, como manipulaciones de cadenas, asignación condicional, etc.
[BizTalk X12](app-service-logic-connector-x12.md) | Recibe y envía mensajes mediante el protocolo X12 en comunicaciones negocio a negocio.
[Validador XML de BizTalk](app-service-logic-xml-validator.md) | Valida los datos XML en esquemas XML predefinidos. Puede usar los esquemas existentes o generar esquemas basándose en una instancia de archivo sin formato, instancia de JSON o conectores existentes.
[Extractor XPath de BizTalk](app-service-logic-xpath-extract.md) | Busca y extrae datos de contenido XML basándose en una expresión XPath que elija.
[Conector de DB2](app-service-logic-connector-db2.md) | Se conecta a una base de datos DB2 de IBM de forma local y en una máquina virtual de Azure con un sistema operativo Windows. Puede asignar las operaciones Web API y API de OData a comandos de lenguaje de consulta estructurado de Informix. <br/><br/>Ningún desencadenador. Las acciones incluyen la selección, inserción, actualización, eliminación de tablas e instrucción personalizada<br/><br/>Este conector también incluye el cliente de Microsoft para DRDA para conectarse a un servidor de Informix a través de una red TCP/IP.
[Archivo](app-service-logic-connector-file.md) | Mediante este conector, puede conectarse a la red o al sistema de archivo local y completar diferentes tareas de archivos, incluidas la carga, la eliminación y la enumeración de archivos, etc.
[FTP<br/>FTPS](app-service-logic-connector-ftp.md) | Se conecta a un servidor FTP/FTPS y realiza diferentes tareas de FTP, incluidas la carga, la obtención y la eliminación de archivos, etc.
[Informix](app-service-logic-connector-informix.md) | Se conecta a una base de datos Informix de IBM, de forma local y en una máquina virtual de Azure con un sistema operativo Windows. Puede asignar las operaciones Web API y API de OData a comandos de lenguaje de consulta estructurado de Informix.<br/><br/>Ningún desencadenador. Las acciones incluyen la selección, inserción, actualización, eliminación de tablas e instrucción personalizada.<br/><br/>Cuando se utiliza de forma local, puede usarse la VPN o Azure ExpressRoute. Este conector también incluye el cliente de Microsoft para DRDA para conectarse a un servidor de Informix a través de una red TCP/IP.
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Se conecta a SQL Server local o a Base de datos SQL de Azure. Puede crear, actualizar, obtener y eliminar entradas en una tabla de base de datos SQL.
MQ | Se conecta a la versión 8 de IBM WebSphere MQ Server, en local y en una máquina virtual de Azure con un sistema operativo Windows. Cuando se utiliza de forma local, puede usarse la VPN o Azure ExpressRoute. El conector también incluye el cliente de Microsoft para MQ.<br/><br/>Ningún desencadenador. Ninguna acción.<br/><br/>**Nota** Actualmente no se puede utilizar con aplicaciones lógicas.
[Base de datos de Oracle](app-service-logic-connector-oracle.md) | Se conecta a la base de datos de Oracle local y puede crear, actualizar, obtener y eliminar entradas en una tabla de base de datos.
[POP3](app-service-logic-connector-pop3.md) (Protocolo de oficina de correos)| Se conecta a un servidor POP3 para recuperar correos electrónicos con datos adjuntos.
[SAP](app-service-logic-connector-sap.md) | Se conecta a un servidor SAP local e invoca RFC, BAPI y tRFC, y envía IDOC.
[SFTP](app-service-logic-connector-sftp.md) (Protocolo de transferencia de archivos SSH)| Se conecta a SFTP y puede cargar, obtener, eliminar archivos, etc.
[SMTP](app-service-logic-connector-smtp.md) (Protocolo simple de transferencia de correo) | Se conecta a un servidor SMTP y envía correo con datos adjuntos.

## Conectores como desencadenadores
Varios conectores proporcionan desencadenadores para aplicaciones lógicas. Estos desencadenadores son de dos tipos:

1. Desencadenadores de sondeo: estos desencadenadores sondean el servicio que proceda a una frecuencia especificada para comprobar si hay nuevos datos. Cuando haya nuevos datos, se ejecuta una nueva instancia de la aplicación lógica con los datos como entrada. Para impedir que los mismos datos se consuman varias veces, el desencadenador puede limpiar los datos que se han leído y pasado a la aplicación lógica. Algunos de estos conectores son los de archivo, SQL y almacenamiento de Azure.
2. Desencadenadores de inserción: estos desencadenadores escuchan datos en un extremo o esperan a que se produzca un evento. A continuación, desencadenan una nueva instancia de una aplicación de la lógica. Ejemplos de estos conectores son los de escucha HTTP y Twitter.

## Conectores como acciones
Los conectores también pueden utilizarse como acciones dentro de una aplicación lógica. Las acciones son útiles para buscar datos dentro de la aplicación lógica que, a continuación, puede utilizarse en la ejecución. Por ejemplo, puede que necesite consultar datos de una base de datos SQL para obtener información adicional acerca de un cliente al procesar un pedido. O bien, puede que necesite escribir, actualizar o eliminar datos en un destino. Para ello, puede usar las acciones proporcionadas por los conectores. Las acciones se asignan a operaciones en las aplicaciones de API (como definen sus metadatos en Swagger).

## Cree sus propios conectores y aplicaciones de API
[Referencia sobre conectores y aplicaciones de API](http://aka.ms/appservicesconnectorreference) [Desencadenadores de aplicación de API del Servicio de aplicaciones de Azure](../app-service-api/app-service-api-dotnet-triggers.md) [Referencia sobre aplicaciones lógicas](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## Más sobre los conectores y las aplicaciones de API
[Qué son los conectores y las aplicaciones de API de BizTalk](app-service-logic-what-are-biztalk-api-apps.md) [Uso del Administrador de conexiones híbridas en el Servicio de aplicaciones de Azure](app-service-logic-hybrid-connection-manager.md) [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md)

<!---HONumber=AcomDC_0224_2016-->