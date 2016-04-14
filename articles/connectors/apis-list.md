<properties
	pageTitle="Lista de API administradas por Microsoft | Servicio de aplicaciones de Microsoft Azure"
	description="Obtenga una lista completa de las API administradas de Microsoft que puede usar para crear Aplicaciones lógicas en el Servicio de aplicaciones de Azure."
	services="app-service\logic"
	documentationCenter=""
	authors="MSFTMAN"
	manager="erikre"
	editor=""
    tags="connectors"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/02/2016"
	ms.author="deonhe"/>

# Lista de API administradas

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas. Para la versión de esquema 2014-12-01-preview, haga clic en [Lista de conectores](../app-service-logic/app-service-logic-connectors-list.md).

Seleccione un icono para aprender a aprovechar rápidamente estas API a fin de crear aplicaciones que llamen a estos servicios. Estas API se pueden utilizar para crear aplicaciones lógicas, PowerApps o ambos tipos.

Para obtener información y una lista de lo que se incluye con cada nivel de servicio de precios, consulte [Precios del Servicio de aplicaciones de Azure](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Si desea empezar a utilizar Aplicaciones lógicas de Azure antes de suscribirse para obtener una cuenta de Azure, vaya a [Probar Servicio de aplicaciones](https://tryappservice.azure.com/?appservice=logic). Podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

|API existentes||||
|-----------|-----------|-----------|-----------|
|[![Icono de API][blobicon]<br/>**Blob de Azure**][azureblobdoc]|[![Icono de API][bingsearchicon]<br/>**Búsqueda de Bing**][bingsearchdoc]|[![Icono de API][boxicon]<br/>**Box**][boxDoc]|[![Icono de API][crmonlineicon]<br/>**CRM en línea**][crmonlinedoc]|
|[![Icono de API][dropboxicon]<br/>**Dropbox**][dropboxdoc]|[![Icono de API][facebookicon]<br/>**Facebook**][facebookdoc]|[![Icono de API][ftpicon]<br/>**FTP**][ftpdoc]|[![Icono de API][googledriveicon]<br/>**Google Drive**][googledrivedoc]|
|[![Icono de API][microsofttranslatoricon]<br/>**Translator**][microsofttranslatordoc]|[![Icono de API][office365icon]<br/>**Office 365**<br/>**Outlook**][office365outlookdoc]|[![Icono de API][office365icon]<br/>**Office 365**<br/>**Usuarios**][office365usersdoc]|[![Icono de API][office365icon]<br/>**Office 365**<br/>**Vídeo**][office365videodoc]|
|[![Icono de API][onedriveicon]<br/>**OneDrive**][onedrivedoc]|[![Icono de API][salesforceicon]<br/>**Salesforce**][salesforcedoc]|[![Icono de API][servicebusicon]<br/>**Bus de servicio**][servicebusdoc]|[![Icono de API][sftpicon]<br/>**SFTP**][sftpdoc]|
|[![Icono de API][sharepointicon]<br/>**SharePoint**<br/>**Online**][sharepointdoc]|[![Icono de API][slackicon]<br/>**Slack**<br/>][slackdoc]|[![Icono de API][smtpicon]<br/>**SMTP**][smtpdoc]|[![Icono de API][sqlicon]<br/>**SQL Azure**][sqldoc]|
|[![Icono de API][twilioicon]<br/>**Twilio**][twiliodoc]|[![Icono de API][twittericon]<br/>**Twitter**][twitterdoc]|[![Icono de API][yammericon]<br/>**Yammer**][yammerdoc] | |


### Las API pueden ser desencadenadores
Varias API proporcionan desencadenadores que pueden notificar a la aplicación que se producen eventos determinados. Por ejemplo, la API de FTP tiene el desencadenador OnUpdatedFile. Puede crear una aplicación lógica o una PowerApp que escuche al desencadenador y realice alguna acción cuando este se ponga en marcha.

Existen dos tipos de desencadenadores:

* Desencadenadores de sondeo: estos desencadenadores sondean el servicio que proceda a una frecuencia especificada para comprobar si hay nuevos datos. Cuando haya nuevos datos disponibles, se ejecutará una nueva instancia de la aplicación con los datos como entrada. Para impedir que los mismos datos se consuman varias veces, el desencadenador puede limpiar los datos que se han leído y pasado a la aplicación.
* Desencadenadores de inserción: estos desencadenadores escuchan datos en un extremo o esperan a que se produzca un evento. A continuación, desencadena una nueva instancia de la aplicación. La API de Twitter es un ejemplo.


### Las API pueden ser acciones
Las API también pueden utilizarse como acciones dentro de sus aplicaciones. Las acciones resultan útiles para buscar datos que luego se pueden utilizar en la ejecución de la aplicación. Por ejemplo, puede que necesite buscar datos de clientes de una base de datos SQL al procesar un pedido. O bien, puede que necesite escribir, actualizar o eliminar datos en una tabla de destino. Para ello, puede usar las acciones proporcionadas por las API. Las acciones se asignan a las operaciones que se definen en los metadatos de Swagger.


[Novedades](../app-service-logic/app-service-logic-schema-2015-08-01.md) [Cree una aplicación lógica ahora](../app-service-logic/app-service-logic-create-a-logic-app.md) [Empiece a usar a PowerApps ahora](../power-apps/powerapps-get-started-azure-portal.md)

<!--API Documentation-->
[azureblobdoc]: ./create-api-azureblobstorage.md "Conéctese a un blob de Azure para administrar archivos en el contenedor de blobs."
[bingsearchDoc]: ./create-api-bingsearch.md "Busque en Bing para web, imágenes, noticias y vídeo."
[boxDoc]: ./create-api-box.md "Se conecta a Box y puede realizar las operaciones de cargar, obtener, eliminar, enumerar y más tareas de archivos."
[crmonlinedoc]: ./create-api-crmonline.md "Conéctese a Dynamics CRM Online y haga más con los datos de CRM Online."
[dropboxdoc]: ./create-api-dropbox.md "Se conecta a Dropbox y puede realizar las operaciones de obtener, eliminar, enumerar y más tareas de archivos."
[exceldoc]: ./create-api-excel.md "Conéctese con Excel."
[facebookdoc]: ./create-api-facebook.md "Conéctese con Facebook para publicar en una escala de tiempo, obtener una fuente de páginas y más."
[ftpdoc]: ./create-api-ftp.md "Se conecta a un servidor FTP/FTPS y realiza diferentes tareas de FTP, incluidas la carga, la obtención y la eliminación de archivos, etc."
[googledrivedoc]: ./create-api-googledrive.md "Conéctese con Google Drive e interactúe con los datos."
[microsofttranslatordoc]: ./create-api-microsofttranslator.md
[office365outlookdoc]: ./create-api-office365-outlook.md "El conector Office 365 puede enviar y recibir correos electrónicos, administrar su calendario y administrar sus contactos con su cuenta de Office 365."
[officeunifieddoc]: ./create-api-bingsearch.md
[office365usersdoc]: ./create-api-office365-users.md
[office365videodoc]: ./create-api-office365-video.md
[onedrivedoc]: ./create-api-onedrive.md "Se conecta a su Microsoft OneDrive personal y cargue, elimine, mostrar archivos, etc."
[salesforcedoc]: ./create-api-salesforce.md "Se conecta a su cuenta de Salesforce y puede administrar cuentas, clientes potenciales, oportunidades y mucho más."
[servicebusdoc]: ./create-api-servicebus.md "Puede enviar mensajes desde los temas y las colas del Bus de servicio y recibir mensajes de suscripciones y colas del Bus de servicio."
[sharepointdoc]: ./create-api-sharepointonline.md "Se conecta a SharePoint Online para administrar documentos y enumerar elementos."
[slackdoc]: ./create-api-slack.md "Conéctese a Slack y publique mensajes en los canales de Slack."
[sftpdoc]: ./create-api-sftp.md "Se conecta a SFTP y puede cargar, obtener, eliminar archivos, etc."
[smtpdoc]: ./create-api-smtp.md "Se conecta a un servidor SMTP y envía correo con datos adjuntos."
[sqldoc]: ./create-api-sqlazure.md "Se conecta a Base de datos de SQL Azure. Puede crear, actualizar, obtener y eliminar entradas en una tabla de base de datos SQL."
[twiliodoc]: ./create-api-twilio.md "Se conecta a Twilio y puede enviar y recibir mensajes, obtener los números disponibles, administrar los números de teléfono entrantes y mucho más."
[twitterdoc]: ./create-api-twitter.md "Se conecta a Twitter y puede obtener escalas de tiempo, publicar tweets, etc."
[yammerdoc]: ./create-api-yammer.md "Se conecta a Yammer para publicar mensajes y obtener nuevos mensajes."

<!--Icon references-->
[blobicon]: ./media/apis-list/blobicon.png
[bingsearchicon]: ./media/apis-list/bingsearchicon.png
[boxicon]: ./media/apis-list/boxicon.png
[ftpicon]: ./media/apis-list/ftpicon.png
[crmonlineicon]: ./media/apis-list/dynamicscrmicon.png
[dropboxicon]: ./media/apis-list/dropboxicon.png
[excelicon]: ./media/apis-list/excelicon.png
[facebookicon]: ./media/apis-list/facebookicon.png
[googledriveicon]: ./media/apis-list/googledriveicon.png
[microsofttranslatoricon]: ./media/apis-list/translatoricon.png
[office365icon]: ./media/apis-list/office365icon.png
[onedriveicon]: ./media/apis-list/onedriveicon.png
[salesforceicon]: ./media/apis-list/salesforceicon.png
[servicebusicon]: ./media/apis-list/servicebusicon.png
[sftpicon]: ./media/apis-list/sftpicon.png
[sharepointicon]: ./media/apis-list/sharepointicon.png
[slackicon]: ./media/apis-list/slackicon.png
[smtpicon]: ./media/apis-list/smtpicon.png
[sqlicon]: ./media/apis-list/sqlicon.png
[twilioicon]: ./media/apis-list/twilioicon.png
[twittericon]: ./media/apis-list/twittericon.png
[yammericon]: ./media/apis-list/yammericon.png

<!---HONumber=AcomDC_0302_2016-->