<properties 
	pageTitle="Actualización de Servicios multimedia después de rotar las claves de acceso" 
	description="En este artículo se proporcionan instrucciones sobre cómo actualizar Servicios multimedia tras rotar las claves de acceso de almacenamiento." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako,milangada,cenkdin" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="02/03/2016"  
	ms.author="juliako"/>

#Actualización de Servicios multimedia después de rotar las claves de acceso de almacenamiento

Cuando crea una nueva cuenta de Servicios multimedia de Azure, también se le pide que seleccione una cuenta de almacenamiento de Azure que se usa para almacenar el contenido multimedia. Tenga en cuenta que puede [agregar más de una cuenta de almacenamiento](meda-services-managing-multiple-storage-accounts.md) a su cuenta de Servicios multimedia.

Cuando se crea una nueva cuenta de almacenamiento, Azure genera dos claves de acceso de almacenamiento de 512 bits, que se usan para autenticar el acceso a su cuenta de almacenamiento. Para aumentar la seguridad de sus conexiones de almacenamiento, se recomienda regenerar y rotar periódicamente su claves de acceso de almacenamiento. Se proporcionan dos claves de acceso (principal y secundaria) con el fin de permitirle mantener conexiones con la cuenta de almacenamiento mediante el uso de una clave de acceso mientras regenera la otra. Este procedimiento se conoce también como "rotación de claves de acceso".

Servicios multimedia depende de una clave de almacenamiento que se le ofrece. En concreto, los localizadores que se usan para transmitir o descargar sus activos dependen de la clave de acceso de almacenamiento. Cuando se crea una cuenta de AMS toma una dependencia en la clave de acceso de almacenamiento principal de forma predeterminada, pero como usuario puede actualizar la clave de almacenamiento que AMS tiene. Debe asegurarse de que los Servicios multimedia conocen qué clave usar siguiendo los pasos descritos en este tema. Además, al rotar las claves de acceso de almacenamiento, debe asegurarse de actualizar sus localizadores para que no haya ninguna interrupción en su servicio de streaming (este paso también se describe en el tema).

>[AZURE.NOTE]Si tiene varias cuentas de almacenamiento, realizaría este procedimiento con cada una.
>
>Antes de ejecutar los pasos que se describen en este tema en una cuenta de producción, asegúrese de probarlos en una cuenta de ensayo.


## Paso 1: Regeneración de la clave de acceso de almacenamiento secundaria

Para comenzar, regenere la clave de almacenamiento secundaria. De forma predeterminada, Servicios multimedia no usa la clave secundaria. Para información sobre cómo rotar las claves de almacenamiento, vea [Vista, copia y regeneración de las claves de acceso de almacenamiento](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
  
##<a id="step2"></a>Paso 2: Actualización de Servicios multimedia para usar la nueva clave de almacenamiento secundaria

Actualice Servicios multimedia para usar la clave de acceso de almacenamiento secundaria. Puede usar uno de los dos métodos siguientes para sincronizar la clave de almacenamiento regenerada con Servicios multimedia.

- Use el Portal de Azure clásico: seleccione su cuenta de Servicios multimedia y haga clic en el icono "ADMINISTRAR CLAVES" en la parte inferior de la ventana del portal. Según la clave de almacenamiento con la que desee que se sincronice Servicios multimedia, seleccione el botón para sincronizar la clave principal o para sincronizar la clave secundaria. En este caso, use la clave secundaria.

- Use la API de REST de Servicios multimedia.

En el siguiente código de ejemplo se muestra cómo construir la solicitud https://endpoint/<subscriptionId>/services/mediaservices/Accounts/<accountName>/StorageAccounts/<storageAccountName>/Key con el fin de sincronizar la clave de almacenamiento especificada con Servicios multimedia. En este caso, se usa el valor de la clave de almacenamiento secundaria. Para más información, vea [Uso de la API de REST de administración de Servicios multimedia](http://msdn.microsoft.com/library/azure/dn167656.aspx).
 
		public void UpdateMediaServicesWithStorageAccountKey(string mediaServicesAccount, string storageAccountName, string storageAccountKey)
		{
		    var clientCert = GetCertificate(CertThumbprint);
		
		    HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/StorageAccounts/{3}/Key",
		        Endpoint, SubscriptionId, mediaServicesAccount, storageAccountName));
		    request.Method = "PUT";
		    request.ContentType = "application/json; charset=utf-8";
		    request.Headers.Add("x-ms-version", "2011-10-01");
		    request.Headers.Add("Accept-Encoding: gzip, deflate");
		    request.ClientCertificates.Add(clientCert);
		
		
		    using (var streamWriter = new StreamWriter(request.GetRequestStream()))
		    {
		        streamWriter.Write(""");
		        streamWriter.Write(storageAccountKey);
		        streamWriter.Write(""");
		        streamWriter.Flush();
		    }
		
		    using (var response = (HttpWebResponse)request.GetResponse())
		    {
		        string jsonResponse;
		        Stream receiveStream = response.GetResponseStream();
		        Encoding encode = Encoding.GetEncoding("utf-8");
		        if (receiveStream != null)
		        {
		            var readStream = new StreamReader(receiveStream, encode);
		            jsonResponse = readStream.ReadToEnd();
		        }
		    }
		}

Tras este paso, actualice los localizadores existentes (que tienen una dependencia de la clave de almacenamiento anterior), como se muestra en el siguiente paso.

>[AZURE.NOTE]Espere 30 minutos antes de realizar ninguna operación con Servicios multimedia (por ejemplo, crear nuevos localizadores) a fin de impedir que los trabajos pendientes sea vean afectados.

##Paso 3: Actualización de los localizadores 

>[AZURE.NOTE]Al rotar las claves de acceso de almacenamiento, debe asegurarse de actualizar sus localizadores existentes para que no haya ninguna interrupción en su servicio de streaming.

Espere al menos 30 minutos tras la sincronización de la nueva clave de almacenamiento con AMS. Después, puede volver a crear los localizadores OnDemand de modo que tomen dependencia de la nueva clave de almacenamiento especificada y mantengan la URL existente.

Tenga en cuenta que al actualizar (o volver a crear) un localizador de SAS, la dirección URL siempre cambiará.

>[AZURE.NOTE] Para asegurarse de que conserva las URL existentes de los localizadores OnDemand, deberá eliminar el localizador existente y crear uno nuevo con el mismo identificador.
 
En el siguiente ejemplo de .NET se muestra cómo se puede volver a crear un localizador con el mismo identificador.
	
	private static ILocator RecreateLocator(CloudMediaContext context, ILocator locator)
	{
	    // Save properties of existing locator.
	    var asset = locator.Asset;
	    var accessPolicy = locator.AccessPolicy;
	    var locatorId = locator.Id;
	    var startDate = locator.StartTime;
	    var locatorType = locator.Type;
	    var locatorName = locator.Name;
	
	    // Delete old locator.
	    locator.Delete();
	
	    if (locator.ExpirationDateTime <= DateTime.UtcNow)
	    {
	        throw new Exception(String.Format(
	            "Cannot recreate locator Id={0} because its locator expiration time is in the past",
	            locator.Id));
	    }
	
	    // Create new locator using saved properties.
	    var newLocator = context.Locators.CreateLocator(
	        locatorId,
	        locatorType,
	        asset,
	        accessPolicy,
	        startDate,
	        locatorName);
	
	
	
	    return newLocator;
	}


##Paso 5: Regeneración de la clave de acceso de almacenamiento principal

Regenere la clave de acceso de almacenamiento principal. Para obtener información sobre cómo rotar las claves de almacenamiento, consulte [Vista, copia y regeneración de las claves de acceso de almacenamiento](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

##Paso 6: Actualización de Servicios multimedia para usar la nueva clave de almacenamiento principal
	
Use el mismo procedimiento que se describe en el [paso 2](media-services-roll-storage-access-keys.md#step2), pero esta vez sincronice la nueva clave de acceso de almacenamiento principal con la cuenta de Servicios multimedia.

>[AZURE.NOTE]Espere 30 minutos antes de realizar ninguna operación con Servicios multimedia (por ejemplo, crear nuevos localizadores) a fin de impedir que los trabajos pendientes sea vean afectados.

##Paso 7: Actualización de los localizadores  

Pasados 30 minutos, puede volver a crear los localizadores OnDemand de modo que tomen dependencia de la nueva clave de almacenamiento primaria y mantengan la URL existente.

Use el mismo procedimiento descrito en el [paso 3](media-services-roll-storage-access-keys.md#step-3-update-locators).


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



###Agradecimientos 

Nos gustaría mencionar a las siguientes personas que han contribuido a crear este documento: Cenk Dingiloglu, Gada Milán y Seva Titov.

<!---HONumber=AcomDC_0211_2016-->