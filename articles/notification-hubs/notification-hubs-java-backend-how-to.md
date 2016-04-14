<properties 
	pageTitle="Uso de los Centros de notificaciones con Java" 
	description="Obtenga información acerca de cómo usar los Centros de notificaciones de Azure desde un back-end de Java." 
	services="notification-hubs" 
	documentationCenter="" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="java" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="11/01/2015" 
	ms.author="wesmc"/>

# Uso de los Centros de notificaciones desde Java
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]
		
En este tema se describen las principales características del nuevo SDK de Java del Centro de notificaciones de Azure oficial y totalmente compatible. Este es un proyecto de código abierto; puede ver el código entero en el [SDK de Java].

En general, puede acceder a todas las características de los Centros de notificaciones desde un servidor back-end de Java, PHP, Python y Ruby mediante la interfaz REST del Centro de notificaciones, como se describe en el tema de MSDN [API de REST de los Centros de notificaciones](http://msdn.microsoft.com/library/dn223264.aspx). Este SDK de Java proporciona un contenedor fino de estas interfaces REST en Java.

El SDK admite actualmente:

- CRUD en los Centros de notificaciones 
- CRUD en los registros
- Administración de la instalación
- Importación y exportación de registros
- Envíos regulares
- Envíos programados
- Operaciones asincrónicas mediante Java NIO
- Plataformas admitidas: APNS (iOS), GCM (Android), WNS (aplicaciones de la Tienda Windows), MPNS (Windows Phone), ADM (Amazon Kindle Fire), Baidu (Android sin servicios de Google) 

## Uso del SDK

### Compilación y creación

Uso de [Maven]

Para crear:

	mvn package

## Código

### Operaciones CRUD de Centros de notificaciones

**Creación de un administrador de espacio de nombres:**
	
	NamespaceManager namespaceManager = new NamespaceManager("connection string")

**Creación de un Centro de notificaciones:**
	
	NotificationHubDescription hub = new NotificationHubDescription("hubname");
	hub.setWindowsCredential(new WindowsCredential("sid","key"));
	hub = namespaceManager.createNotificationHub(hub);
	
 OR

	hub = new NotificationHub("connection string", "hubname");

**Obtención del Centro de notificaciones:**
	
	hub = namespaceManager.getNotificationHub("hubname");

**Actualización del Centro de notificaciones:**
	
	hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
	hub = namespaceManager.updateNotificationHub(hub);

**Eliminación del Centro de notificaciones:**
	
	namespaceManager.deleteNotificationHub("hubname");

### Operaciones CRUD de registro
**Creación de un cliente del Centro de notificaciones:**

	hub = new NotificationHub("connection string", "hubname");

**Creación de un registro de Windows:**

	WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
	reg.getTags().add("myTag");
	reg.getTags().add("myOtherTag");    
	hub.createRegistration(reg);

**Creación de un registro de iOS:**

	AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
	reg.getTags().add("myTag");
	reg.getTags().add("myOtherTag");
	hub.createRegistration(reg);

Del mismo modo puede crear registros para Android (GCM), Windows Phone (MPNS) y Kindle Fire (ADM).

**Creación de registros de plantilla:**

	WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
	reg.getHeaders().put("X-WNS-Type", "wns/toast");
	hub.createRegistration(reg);

**Creación de registros mediante el patrón create registrationid + upsert**

Quita los duplicados debido a las respuestas perdidas si se almacenan identificadores de registro en el dispositivo:

	String id = hub.createRegistrationId();
	WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
	hub.upsertRegistration(reg);

**Actualización de registros:**
	
	hub.updateRegistration(reg);

**Eliminación de registros:**
	
	hub.deleteRegistration(regid);

**Consulta de registros:**

* 	**Obtención de un único registro:**
	
		hub.getRegistration(regid);
	
* 	**Obtención de todos los registros en el centro:**
	
		hub.getRegistrations();
	
* 	**Obtención de registros con etiqueta:**
	
		hub.getRegistrationsByTag("myTag");
	
* 	**Obtención de registros por canal:**
	
		hub.getRegistrationsByChannel("devicetoken");

Todas las consultas de la colección admiten los tokens $top y continuation.

### Uso de la API de instalación
La API de instalación es un mecanismo alternativo para la administración de registros. En lugar de mantener varios registros que no es un procedimiento sencillo y puede fácilmente realizarse de manera incorrecta o ineficaz, ahora es posible usar un objeto de instalación ÚNICO. La instalación contiene todo lo que necesita: canal de inserción (token del dispositivo), etiquetas, plantillas, iconos secundarios (para WNS y APNS). No es necesario llamar al servicio para obtener el identificador: solo hay que generar el GUID o cualquier otro identificador, mantenerlo en el dispositivo y enviarlo a su back-end junto con el canal de inserción (token del dispositivo). En el back-end, solo debe hacer una llamada: CreateOrUpdateInstallation, es completamente idempotente, así que no dude en reintentarlo si es necesario.

Un ejemplo para Amazon Kindle Fire es como el siguiente:

	Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
	hub.createOrUpdateInstallation(installation);

Si desea actualizarlo:

	installation.addTag("foo");
	installation.addTemplate("template1", new InstallationTemplate("{"data":{"key1":"$(value1)"}}","tag-for-template1"));
	installation.addTemplate("template2", new InstallationTemplate("{"data":{"key2":"$(value2)"}}","tag-for-template2"));
	hub.createOrUpdateInstallation(installation);

Para escenarios avanzados, tenemos la capacidad de actualización parcial que permite modificar solo determinadas propiedades del objeto de instalación. Básicamente la actualización parcial es un subconjunto de las operaciones de revisiones de JSON que se puede ejecutar con el objeto de instalación.

	PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
	PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
	PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{"data":{"key3":"$(value3)"}}","tag-for-template1")).toJson());
	hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);

Eliminación de la instalación:

	hub.deleteInstallation(installation.getInstallationId());

CreateOrUpdate, Patch y Delete son eventualmente coherentes con Get. La operación solicitada se dirige a la cola de sistema durante la llamada y se ejecutará en segundo plano. Tenga en cuenta que Get no está diseñado para el escenario principal de tiempo de ejecución, sino solo con fines de depuración y solución de problemas, dado que está estrechamente limitado por el servicio.

El flujo de envío de las instalaciones es el mismo que el de registros. Hemos presentado una opción para dirigir la notificación a la instalación particular; utilice únicamente la etiqueta "InstallationId:{desired-id}". Para el caso anterior sería similar al siguiente:

	Notification n = Notification.createWindowsNotification("WNS body");
	hub.sendNotification(n, "InstallationId:{installation-id}");

Para una de varias plantillas:

	Map<String, String> prop =  new HashMap<String, String>();
	prop.put("value3", "some value");
	Notification n = Notification.createTemplateNotification(prop);
	hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");

### Programación de notificaciones (disponibles para el nivel estándar)

Igual que el envío normal pero con un parámetro adicional - scheduledTime que indica cuándo se debe entregar la notificación. El servicio acepta cualquier punto de tiempo entre ahora + 5 minutos y ahora + 7 días.

**Programación de una notificación nativa de Windows:**

	Calendar c = Calendar.getInstance();
	c.add(Calendar.DATE, 1);    
	Notification n = Notification.createWindowsNotification("WNS body");
	hub.scheduleNotification(n, c.getTime());

### Importación y exportación (disponible para el nivel estándar)
A veces es necesario para realizar la operación masiva en registros. Normalmente es para la integración con otro sistema o solo una corrección masiva para actualizar las etiquetas. Se recomienda encarecidamente utilizar el flujo de Get/Update si estamos hablando de miles de registros. La capacidad de importación y exportación está pensada para cubrir el escenario. Básicamente proporciona un acceso a algún contenedor de blobs en la cuenta de almacenamiento como un origen de los datos entrantes y la ubicación de salida.

**Envío del trabajo de exportación:**

	NotificationHubJob job = new NotificationHubJob();
	job.setJobType(NotificationHubJobType.ExportRegistrations);
	job.setOutputContainerUri("container uri with SAS signature");
	job = hub.submitNotificationHubJob(job);


**Envío del trabajo de importación:**

	NotificationHubJob job = new NotificationHubJob();
	job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
	job.setImportFileUri("input file uri with SAS signature");
	job.setOutputContainerUri("container uri with SAS signature");
	job = hub.submitNotificationHubJob(job);

**Espere hasta que se realiza el trabajo:**

	while(true){
	    Thread.sleep(1000);
	    job = hub.getNotificationHubJob(job.getJobId());
	    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
	        break;
	}       

**Obtener todos los trabajos:**

	List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();

**URI con firma SAS**: se trata de la dirección URL de algún archivo de blobs o contenedor de blobs más el conjunto de parámetros, como los permisos y la hora de expiración, más la firma de todas las operaciones realizadas mediante la clave SAS de la cuenta. El SDK de Java de almacenamiento de Azure tiene amplias capacidades, incluida la creación de ese tipo de URI. Como una alternativa sencilla puede echar un vistazo a la clase de prueba ImportExportE2E (desde la ubicación de github) que tiene una implementación muy básica y compacta del algoritmo de firma.

###Envío de notificaciones
El objeto de notificación es simplemente un cuerpo con encabezados, algunos métodos de utilidad ayudan en la creación de los objetos de notificación nativos y de plantilla.

* **Tienda Windows y Windows Phone 8.1 (no Silverlight)**

		String toast = "<toast><visual><binding template="ToastText01"><text id="1">Hello from Java!</text></binding></visual></toast>";
		Notification n = Notification.createWindowsNotification(toast);
		hub.sendNotification(n);

* **iOS**

		String alert = "{"aps":{"alert":"Hello from Java!"}}";
		Notification n = Notification.createAppleNotification(alert);
		hub.sendNotification(n);

* **Android**

		String message = "{"data":{"msg":"Hello from Java!"}}";
		Notification n = Notification.createGcmNotification(message);
		hub.sendNotification(n);

* **Silverlight para Windows Phone 8.0 y 8.1**

		String toast = "<?xml version="1.0" encoding="utf-8"?>" +
			        "<wp:Notification xmlns:wp="WPNotification">" +
			           "<wp:Toast>" +
			                "<wp:Text1>Hello from Java!</wp:Text1>" +
			           "</wp:Toast> " +
			        "</wp:Notification>";
		Notification n = Notification.createMpnsNotification(toast);
		hub.sendNotification(n);

* **Kindle Fire**

		String message = "{"data":{"msg":"Hello from Java!"}}";
		Notification n = Notification.createAdmNotification(message);
		hub.sendNotification(n);

* **Envío a etiquetas**

		Set<String> tags = new HashSet<String>();
		tags.add("boo");
		tags.add("foo");
		hub.sendNotification(n, tags);

* **Envío a la expresión de etiqueta**

		hub.sendNotification(n, "foo && ! bar");

* **Envío de la notificación de plantilla**

		Map<String, String> prop =  new HashMap<String, String>();
		prop.put("prop1", "v1");
		prop.put("prop2", "v2");
		Notification n = Notification.createTemplateNotification(prop);
		hub.sendNotification(n);

La ejecución del código de Java debe generar ahora una notificación que aparece en el dispositivo de destino.

##<a name="next-steps"></a>Pasos siguientes
En este tema hemos mostrado cómo crear un simple cliente REST en Java para Centros de notificaciones. Desde aquí puede:

* Descargue el [SDK de Java] completo, que contiene todo el código SDK. 
* Practique con los ejemplos:
	- [Introducción a los Centros de notificaciones]
	- [Envío de noticias de última hora]
	- [Envío de noticias de última hora localizadas]
	- [Envío de notificaciones a usuarios autenticados]
	- [Envío de notificaciones entre plataformas a usuarios autenticados]

[SDK de Java]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Introducción a los Centros de notificaciones]: http://www.windowsazure.com/manage/services/notification-hubs/getting-started-windows-dotnet/
[Envío de noticias de última hora]: http://www.windowsazure.com/manage/services/notification-hubs/breaking-news-dotnet/
[Envío de noticias de última hora localizadas]: http://www.windowsazure.com/manage/services/notification-hubs/breaking-news-localized-dotnet/
[Envío de notificaciones a usuarios autenticados]: http://www.windowsazure.com/manage/services/notification-hubs/notify-users/
[Envío de notificaciones entre plataformas a usuarios autenticados]: http://www.windowsazure.com/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Maven]: http://maven.apache.org/
 

<!---HONumber=AcomDC_0302_2016-->