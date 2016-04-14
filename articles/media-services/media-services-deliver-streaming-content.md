<properties 
	pageTitle="Cómo entregar contenido de streaming de Servicios multimedia" 
	description="Aprenda a crear un localizador que se usa para generar una dirección URL de streaming. Los ejemplos de código están escritos en C# y utilizan el SDK de Servicios multimedia para .NET." 
	authors="juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
  ms.date="02/03/2016"
	ms.author="juliako"/>


#Entrega de contenido de streaming
 
> [AZURE.SELECTOR]
- [REST](media-services-rest-deliver-streaming-content.md)
- [.NET](media-services-deliver-streaming-content.md)
- [Portal](media-services-manage-content.md#publish)

##Información general

Puede transmitir un conjunto de archivos MP4 de velocidad de bits adaptable creando un localizador de streaming a petición y compilando una dirección URL de streaming. El tema [Codificación de un recurso](media-services-encode-asset.md) muestra cómo codificar en un conjunto de MP4 de velocidad de bits adaptable.

>[AZURE.NOTE]Si el contenido está cifrado, configure la directiva de entrega de activos (como se describe en [este](media-services-dotnet-configure-asset-delivery-policy.md) tema) antes de crear un localizador.

También puede utilizar un localizador de streaming a petición para generar direcciones URL que señalen a archivos MP4 que se pueden descargar progresivamente.

En este tema se muestra cómo crear un localizador de streaming a petición para publicar el recurso y crear direcciones URL de streaming Smooth, MPEG DASH y HLS. También se muestra cómo generar direcciones URL de descarga progresiva.
  	 
##Creación de un localizador de streaming a petición

Para crear el localizador de streaming a petición y obtener las direcciones URL, deberá hacer lo siguiente:

   1. Si se cifra el contenido, defina una directiva de acceso.
   2. Cree un localizador de streaming a petición.
   3. Si planea transmitir, obtenga el archivo de manifiesto de streaming (.ism) del recurso. 
   		
	Si planea la descarga progresiva, obtenga los nombres de los archivos MP4 del recurso.  
   4. Genere direcciones URL para el archivo de manifiesto o archivos MP4. 
   

###Uso del SDK de .NET de Servicios multimedia 

Generación de direcciones URL de streaming

	private static void BuildStreamingURLs(IAsset asset)
	{
	
	    // Create a 30-day readonly access policy. 
      	// You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
	    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
	        TimeSpan.FromDays(30),
	        AccessPermissions.Read);
	
	    // Create a locator to the streaming content on an origin. 
	    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
	        policy,
	        DateTime.UtcNow.AddMinutes(-5));
	
	    // Display some useful values based on the locator.
	    Console.WriteLine("Streaming asset base path on origin: ");
	    Console.WriteLine(originLocator.Path);
	    Console.WriteLine();
	
	    // Get a reference to the streaming manifest file from the  
	    // collection of files in the asset. 
	    var manifestFile = asset.AssetFiles.Where(f => f.Name.ToLower().
	                                EndsWith(".ism")).
	                                FirstOrDefault();
	    
	    // Create a full URL to the manifest file. Use this for playback
	    // in streaming media clients. 
	    string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
	    Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
	    Console.WriteLine(urlForClientStreaming);
	    Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
	    Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
	    Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
	    Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
	    Console.WriteLine();
	}

El código produce la salida siguiente:
	
	URL to manifest for client streaming using Smooth Streaming protocol:
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest
	URL to manifest for client streaming using HLS protocol:
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)
	URL to manifest for client streaming using MPEG DASH protocol:
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)
	

>[AZURE.NOTE]También puede transmitir el contenido por una conexión SSL. Para ello, asegúrese de que las URL de streaming comienzan por HTTPS.

Creación de direcciones URL de descarga progresiva

	private static void BuildProgressiveDownloadURLs(IAsset asset)
	{
	    // Create a 30-day readonly access policy. 
	    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
	        TimeSpan.FromDays(30),
	        AccessPermissions.Read);
	
	    // Create an OnDemandOrigin locator to the asset. 
	    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
	        policy,
	        DateTime.UtcNow.AddMinutes(-5));
	
	    // Display some useful values based on the locator.
	    Console.WriteLine("Streaming asset base path on origin: ");
	    Console.WriteLine(originLocator.Path);
	    Console.WriteLine();
	
	    // Get MP4 files.
	    IEnumerable<IAssetFile> mp4AssetFiles = asset
	        .AssetFiles
	        .ToList()
	        .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));
	            
	    // Create a full URL to the MP4 files. Use this to progressively download files.
	    foreach (var pd in mp4AssetFiles)
	        Console.WriteLine(originLocator.Path + pd.Name);
	}

El código produce la salida siguiente:
	
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4
	
	. . . 

###Uso de Extensiones del SDK .NET de Servicios multimedia

El código siguiente llama a los métodos de extensiones del SDK de .NET que crean un localizador y generan las direcciones URL de Smooth Streaming, HLS y MPEG-DASH para el streaming adaptable.

	// Create a loctor.
	_context.Locators.Create(
	    LocatorType.OnDemandOrigin,
	    inputAsset,
	    AccessPermissions.Read,
	    TimeSpan.FromDays(30));
	
	// Get the streaming URLs.
	Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
	Uri hlsUri = inputAsset.GetHlsUri();
	Uri mpegDashUri = inputAsset.GetMpegDashUri();
	
	Console.WriteLine(smoothStreamingUri);
	Console.WriteLine(hlsUri);
	Console.WriteLine(mpegDashUri);


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Otras referencias

[Descargar activos](media-services-deliver-asset-download.md) [Configuración de directivas de entrega de activos](media-services-dotnet-configure-asset-delivery-policy.md)

<!---HONumber=AcomDC_0211_2016-->