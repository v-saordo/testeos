<properties 
	pageTitle="Codificación de un recurso mediante Codificador multimedia estándar" 
	description="Aprenda a usar el Estándar de codificador multimedia para codificar contenido multimedia en Servicios multimedia. Los ejemplos de código usan la API de REST." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/01/2016" 
	ms.author="juliako"/>


#Codificación de un recurso mediante Codificador multimedia estándar


> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encode-asset.md)
- [REST](media-services-rest-encode-asset.md)
- [Portal](media-services-manage-content.md#encode)

##Información general
Para entregar vídeo digital a través de Internet, debe comprimir los archivos multimedia. Los archivos de vídeo digital son bastante grandes y pueden ser demasiado pesados para entregarlos a través de Internet o para que los dispositivos de sus clientes los muestren correctamente. La codificación es el proceso de compresión de vídeo y audio para que los clientes puedan ver el contenido multimedia.

Los trabajos de codificación son una de las operaciones de procesamiento más habituales en los Servicios multimedia. Los trabajos de codificación se crean para convertir archivos multimedia de una codificación a otra. Al codificar, puede usar el codificador integrado en Servicios multimedia (Codificador multimedia estándar). También puede usar un codificador proporcionado por un socio de Servicios multimedia; los codificadores de terceros están disponibles a través de Azure Marketplace. Puede especificar los detalles de las tareas de codificación mediante cadenas preestablecidas definidas para el codificador o mediante archivos de configuración preestablecidos. Para consultar los tipos de valores preestablecidos disponibles, vea [Valores preestablecidos de tareas para el Codificador multimedia estándar](http://msdn.microsoft.com/library/mt269960).

Cada trabajo puede tener una o más tareas según el tipo de procesamiento que desee llevar a cabo. A través de la API de REST, puede crear trabajos y sus tareas relacionadas en una de las dos maneras siguientes:

- Las tareas se pueden definir en línea mediante la propiedad de navegación de las tareas en entidades de trabajo o 
- mediante el procesamiento por lotes de OData.
  

Se recomienda codificar siempre los archivos intermedios en un conjunto de archivos MP4 de velocidad de bits adaptable y, a continuación, convertirlo al formato deseado con el [empaquetado dinámico](media-services-dynamic-packaging-overview.md). Para aprovechar al máximo el empaquetado dinámico, primero debe obtener al menos una unidad de streaming a petición para el extremo de streaming desde el que va a entregar el contenido. Para obtener más información, consulte [Escalación de Servicios multimedia](media-services-manage-origins.md#scale_streaming_endpoints).

Si el recurso de salida tiene el almacenamiento cifrado, asegúrese de configurar la directiva de entrega de recursos. Para más información, consulte [Configuración de la directiva de entrega de recursos](media-services-rest-configure-asset-delivery-policy.md).


>[AZURE.NOTE]Antes de empezar a hacer referencia a procesadores de multimedia, compruebe que dispone del identificador de procesador de multimedia correcto. Para obtener más información, consulte [Obtención de una instancia de procesador multimedia](media-services-rest-get-media-processor.md).

##Creación de un trabajo con una sola tarea de codificación 

>[AZURE.NOTE] Al trabajar con la API de REST de Servicios multimedia, se aplican las consideraciones siguientes:
>
>Al obtener acceso a las entidades de Servicios multimedia, debe establecer los campos de encabezado específicos y los valores en las solicitudes HTTP. Para obtener más información, consulte [Configuración del desarrollo de la API de REST de Servicios multimedia](media-services-rest-how-to-use.md).

>Después de conectarse correctamente a https://media.windows.net, recibirá una redirección 301 especificando otro URI de Servicios multimedia. Debe realizar las llamadas subsiguientes al nuevo URI como se describe en [Conexión a Servicios multimedia con la API de REST](media-services-rest-connect_programmatically.md).
>
>Cuando se utiliza JSON y se especifica el uso de la palabra clave **\_\_metadata** en la solicitud (por ejemplo, para referencias a un objeto vinculado) DEBE establecer el encabezado **Accept** en [formato JSON detallado](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): Accept: application/json;odata=verbose.

En el ejemplo siguiente se muestra cómo crear y publicar un trabajo con un conjunto de tareas para codificar un vídeo con una resolución y calidad específicas. Al codificar con Codificador multimedia estándar, puede usar los valores preestablecidos de configuración de tareas especificados [aquí](http://msdn.microsoft.com/library/mt269960).
	
Solicitud:

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
	Host: media.windows.net

	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Multiple Bitrate 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Respuesta:
	
	HTTP/1.1 201 Created

	. . . 

###Establecimiento del nombre del recurso de salida

En el ejemplo siguiente se muestra cómo establecer el atributo assetName:

	{ "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName="CustomOutputAssetName">JobOutputAsset(0)</outputAsset></taskBody>"}

##Consideraciones

- Las propiedades TaskBody deben usar archivos XML literales para definir el número de recursos de entrada o salida que se usarán en la tarea. El tema Tarea incluye la definición de esquema XML para el archivo XML.
- En la definición de TaskBody, cada valor interno para <inputAsset> y <outputAsset> se debe establecer como JobInputAsset(value) o JobOutputAsset(value).
- Una tarea puede tener varios recursos de salida. Un elemento JobOutputAsset(x) solo se puede usar una vez como salida de una tarea en un trabajo.
- Puede especificar JobInputAsset o JobOutputAsset como un recurso de entrada de una tarea.
- Las tareas no pueden formar un ciclo.
- El parámetro de valor que se pasa a JobInputAsset o JobOutputAsset representa el valor de índice para un recurso. Los recursos reales se definen en las propiedades de navegación InputMediaAssets y OutputMediaAssets en la definición de la entidad Job. 
- Dado que Servicios multimedia se basa en OData v3, se hace referencia a los recursos individuales de las colecciones de propiedades de navegación InputMediaAssets y OutputMediaAssets a través de un par nombre-valor "\_\_metadata: uri".
- InputMediaAssets se asigna a uno o más recursos que ha creado en Servicios multimedia. El sistema crea OutputMediaAssets. Estos no hacen referencia a ningún recurso existente.
- Se puede asignar un nombre a OutputMediaAssets con el atributo assetName. Si este atributo no está presente, el nombre de OutputMediaAsset será el valor del texto interno del elemento <outputAsset> con un sufijo del valor Job Name o del valor Job Id (en el caso que no se haya definido la propiedad Name). Por ejemplo, si establece un valor para assetName como "Sample", se establecería la propiedad de OutputMediaAsset Name en "Sample". Sin embargo, si no se ha definido un valor para assetName, pero se ha especificado el nombre del trabajo como "NewJob", OutputMediaAsset Name será "JobOutputAsset (value) \_NewJob".


##Creación de un trabajo con tareas encadenadas

En muchos escenarios de aplicaciones, los desarrolladores desean crear una serie de tareas de procesamiento. En Servicios multimedia, puede crear una serie de tareas encadenadas. Cada tarea realiza distintos pasos de procesamiento diferentes y puede usar diferentes procesadores multimedia. Las tareas encadenadas pueden entregar un recurso de una tarea a otra, realizando una secuencia lineal de tareas en el recurso. Sin embargo, no es necesario que las tareas realizadas en un trabajo estén en una secuencia. Al crear una tarea encadenada, los objetos **ITask** encadenados se crean en un solo objeto **IJob**.

>[AZURE.NOTE] Actualmente hay un límite de 30 tareas por trabajo. Si necesita encadenar más de 30 tareas, cree más de un trabajo para incluir las tareas.


	POST https://media.windows.net/api/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

	{  
	   "Name":"NewTestJob",
	   "InputMediaAssets":[  
	      {  
	         "__metadata":{  
	            "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
	         }
	      }
	   ],
	   "Tasks":[  
	      {  
	         "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
	         "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
	         "TaskBody":"<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
	      },
	      {  
	         "Configuration":"H264 Smooth Streaming 720p",
	         "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
	         "TaskBody":"<?xml version="1.0" encoding="utf-16"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
	      }
	   ]
	}


###Consideraciones

Para habilitar el encadenamiento de tareas:

- Un trabajo debe tener al menos 2 tareas
- Debe haber al menos una tarea cuya entrada sea salida de otra tarea del trabajo.

## Uso del procesamiento por lotes de OData 

En el ejemplo siguiente se muestra cómo usar el procesamiento por lotes de OData para crear un trabajo y tareas. Para obtener información sobre el procesamiento por lotes, consulte [Procesamiento por lotes del protocolo Open Data (OData)](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).
 
	POST https://media.windows.net/api/$batch HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
	Accept: multipart/mixed
	Accept-Charset: UTF-8
	Authorization: Bearer <token>
	x-ms-version: 2.11
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
	Host: media.windows.net
	
	
	--batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
	Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d
	
	--changeset_122fb0a4-cd80-4958-820f-346309967e4d
	Content-Type: application/http
	Content-Transfer-Encoding: binary
	
	POST https://media.windows.net/api/Jobs HTTP/1.1
	Content-ID: 1
	Content-Type: application/json
	Accept: application/json
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	Accept-Charset: UTF-8
	Authorization: Bearer <token>
	x-ms-version: 2.11
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
	
	{"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}
	
	--changeset_122fb0a4-cd80-4958-820f-346309967e4d
	Content-Type: application/http
	Content-Transfer-Encoding: binary
	
	POST https://media.windows.net/api/$1/Tasks HTTP/1.1
	Content-ID: 2
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	Accept-Charset: UTF-8
	Authorization: Bearer <token>
	x-ms-version: 2.11
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
	
	{  
	   "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
	   "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
	   "TaskBody":"<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName="Custom output name">JobOutputAsset(0)</outputAsset></taskBody>"
	}

	--changeset_122fb0a4-cd80-4958-820f-346309967e4d--
	--batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--
 


## Creación de un trabajo mediante una plantilla de trabajo


Cuando se procesan varios recursos usando un conjunto común de tareas, las plantillas de trabajo son útiles para especificar los valores preestablecidos de las tareas predeterminadas, el orden de las tareas y así sucesivamente.

En el ejemplo siguiente se muestra cómo crear una plantilla de trabajo con una plantilla de tarea definida en línea. La plantilla de tarea usa Estándar de codificador multimedia como procesador multimedia para codificar el archivo de recursos; sin embargo, también se pueden usar otros procesadores multimedia.


	POST https://media.windows.net/API/JobTemplates HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	Host: media.windows.net

	
	{"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version="1.0" encoding="utf-8"?><jobTemplate><taskBody taskTemplateId="nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
 

>[AZURE.NOTE]A diferencia de otras entidades de Servicios multimedia, debe definir un nuevo identificador GUID para cada plantilla de tarea y colocarlo en el identificador de la plantilla de tarea y en la propiedad Id en el cuerpo de la solicitud. El esquema de identificación de contenido debe seguir el esquema descrito en Identificación de entidades de Servicios multimedia de Azure. Además, las plantillas de trabajo no se pueden actualizar. Por el contrario, debe crear una nueva con los cambios actualizados.
 

Si se realiza correctamente, se devuelve la respuesta siguiente:
	
	HTTP/1.1 201 Created
	
	. . .


En el ejemplo siguiente se muestra cómo crear un trabajo que hace referencia a un identificador de plantilla de trabajo:

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	Host: media.windows.net

	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}
	 

Si se realiza correctamente, se devuelve la respuesta siguiente:
	
	HTTP/1.1 201 Created
	
	. . . 



##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Pasos siguientes
Ahora que sabe cómo crear un trabajo para codificar un recurso, vaya al tema [Comprobación del progreso del trabajo con los Servicios multimedia](media-services-rest-check-job-progress.md).


##Otras referencias

[Obtención de una instancia de procesador multimedia](media-services-rest-get-media-processor.md)

<!---HONumber=AcomDC_0302_2016-->