<properties
	pageTitle="Uso del cifrado dinámico AES-128 y del servicio de entrega de claves"
	description="Servicios multimedia de Microsoft Azure le permite entregar el contenido cifrado con claves de cifrado AES de 128 bits. Servicios multimedia también proporciona el servicio de entrega de claves que distribuye claves de cifrado a los usuarios autorizados. En este tema se muestra cómo cifrar dinámicamente con AES-128 y usar el servicio de entrega de claves."
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
	ms.topic="get-started-article" 
 	ms.date="02/03/2016"  
	ms.author="juliako"/>

#Uso del cifrado dinámico AES-128 y del servicio de entrega de claves

> [AZURE.SELECTOR]
- [.NET](media-services-protect-with-aes128.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##Información general

Servicios multimedia de Microsoft Azure le permite entregar secuencias Http-Live-Streaming (HLS) y Smooth Streaming cifradas con el Estándar de cifrado avanzado (AES) (mediante claves de cifrado de 128 bits). Servicios multimedia también proporciona el servicio de entrega de claves que distribuye claves de cifrado a los usuarios autorizados. Si desea que Servicios multimedia cifre un recurso, debe asociar una clave de cifrado con el recurso y, además, configurar directivas de autorización para la clave. Cuando un reproductor solicita una secuencia, Servicios multimedia usa la clave especificada para cifrar de forma dinámica el contenido mediante AES. Para descifrar la secuencia, el reproductor solicitará la clave del servicio de entrega de claves. Para decidir si el usuario está o no autorizado para obtener la clave, el servicio evalúa las directivas de autorización que especificó para la clave.

Servicios multimedia admite varias formas de autenticar a los usuarios que realizan solicitudes de clave. La directiva de autorización de clave de acceso podría tener una o más restricciones de autorización: abrir, restricción de token o restricción de IP. La directiva con restricción token debe ir acompañada de un token emitido por un Servicio de tokens seguros (STS). Servicios multimedia admite tokens en formato [tokens web simples](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) y en formato [tokens web JSON](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Para obtener más información, consulte [Configuración de la directiva de autorización de la clave de contenido](media-services-protect-with-aes128.md#configure_key_auth_policy).

Para aprovechar las ventajas del cifrado dinámico, debe disponer de un recurso que contenga un conjunto de archivos MP4 o archivos de origen Smooth Streaming, de varias velocidades de bits. También deberá configurar la directiva de entrega para el recurso (se describe más adelante en este tema). Luego, según el formato especificado en la URL de streaming, el servidor de streaming a petición se asegurará de que se reciba la secuencia en el protocolo elegido. Como resultado, solo tendrá que almacenar y pagar los archivos en formato de almacenamiento único y Servicios multimedia creará y proporcionará la respuesta adecuada en función de las solicitudes de un cliente.

Este tema será útil para los desarrolladores que trabajan en aplicaciones que proporcionan multimedia protegida. Se describe cómo configurar el servicio de entrega de claves con directivas de autorización para que solo los clientes autorizados puedan recibir las claves de cifrado. También se describe cómo usar el cifrado dinámico.

>[AZURE.NOTE]Para empezar a usar el cifrado dinámico, primero debe obtener al menos una unidad de escalado (también conocida como unidad de streaming). Para obtener más información, consulte [Escalación de un servicio multimedia](media-services-manage-origins.md#scale_streaming_endpoints).

##Flujo de trabajo de cifrado dinámico AES-128 y del servicio de entrega de claves

Éstos son los pasos generales que deberá realizar al cifrar los recursos con AES, mediante el servicio de entrega claves de Servicios multimedia y también mediante cifrado dinámico.

1. [Creación de un recurso y carga de los archivos en el recurso](media-services-protect-with-aes128.md#create_asset). 
1. [Codificación del recurso que contiene el archivo con Adaptive Bitrate MP4 Set](media-services-protect-with-aes128.md#encode_asset).
1. [Creación de una clave de contenido y su asociación con el recurso codificado](media-services-protect-with-aes128.md#create_contentkey). En Servicios multimedia, la clave de contenido contiene la clave de cifrado del recurso.
1. [Configuración de la directiva de autorización de la clave de contenido](media-services-protect-with-aes128.md#configure_key_auth_policy). El usuario debe configurar la directiva de autorización de claves y el cliente (reproductor) debe conocerla para que se le entregue la clave de contenido. 
1. [Configuración de la directiva de entrega para un recurso](media-services-protect-with-aes128.md#configure_asset_delivery_policy). La configuración de la directiva de entrega incluye: la URL de adquisición de claves y vector de inicialización (IV) (AES 128 requiere que se suministre el mismo IV en el cifrado y descifrado), el protocolo de entrega (por ejemplo, MPEG DASH, HLS, HDS, Smooth Streaming o todos) y el tipo de cifrado dinámico (por ejemplo Envelope o sin cifrado dinámico). 

	Puede aplicar diferentes directivas a cada protocolo en el mismo recurso. Por ejemplo, puede aplicar cifrado PlayReady a Smooth/DASH y AES Envelope a HLS. Se bloqueará la transmisión para todos los protocolos que no estén definidos en una directiva de entrega (por ejemplo, si agrega una sola directiva que solo especifica HLS como el protocolo). La excepción a esta regla se produce en el caso de que no haya definido ninguna directiva de entrega de recursos. En tal caso, todos los protocolos estarán habilitados sin cifrar.

1. [Creación de un localizador a petición](media-services-protect-with-aes128.md#create_locator) para obtener una URL de streaming.

El tema también muestra [cómo una aplicación cliente puede solicitar una clave al servicio de entrega de claves](media-services-protect-with-aes128.md#client_request).

Encontrará un [ejemplo](media-services-protect-with-aes128.md#example) completo para .NET al final del tema.

La siguiente imagen muestra el flujo de trabajo que se ha descrito anteriormente. Aquí el token se usa para la autenticación.

![Protección con AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

El resto de este tema proporciona explicaciones detalladas, ejemplos de código y vínculos a temas que le muestran cómo lograr las tareas descritas arriba.

##Limitaciones actuales

Si agrega o actualiza la directiva de entrega de recursos, debe eliminar un localizador existente (si hay) y crear uno nuevo.

##<a id="create_asset"></a>Creación de un recurso y carga de los archivos en el recurso

Para administrar, codificar y transmitir por secuencias sus vídeos, primero debe cargar el contenido en Servicios multimedia de Microsoft Azure. Una vez cargado, el contenido se almacena de forma segura en la nube para su posterior procesamiento y transmisión.

Para obtener información detallada, consulte [Carga de archivos en una cuenta de Servicios multimedia](media-services-dotnet-upload-files.md).

##<a id="encode_asset"></a>Codificación del activo que contiene el archivo con Adaptive Bitrate MP4 Set.

Con el cifrado dinámico, todo lo que tiene que hacer es crear un recurso que contenga un conjunto de archivos MP4 o archivos Smooth Streaming, de varias velocidades de bits. Luego, según el formato especificado en la solicitud de manifiesto o fragmento, el servidor de streaming a petición se asegurará de que reciba la secuencia en el protocolo elegido. Como resultado, solo tendrá que almacenar y pagar los archivos en formato de almacenamiento único y Servicios multimedia creará y proporcionará la respuesta adecuada en función de las solicitudes de un cliente. Para obtener más información, consulte el tema [Información general sobre el empaquetado dinámico](media-services-dynamic-packaging-overview.md).

Para obtener instrucciones sobre cómo codificar, consulte [Codificación de un recurso mediante Codificador multimedia estándar](media-services-dotnet-encode-with-media-encoder-standard.md).

##<a id="create_contentkey"></a>Creación de una clave de contenido y su asociación con el activo codificado

En Servicios multimedia, la clave de contenido contiene la clave con la que desea cifrar un recurso.

Para obtener más información, consulte [Creación de la clave de contenido](media-services-dotnet-create-contentkey.md).

##<a id="configure_key_auth_policy"></a>Configuración de la directiva de autorización de claves de contenido

Servicios multimedia admite varias formas de autenticar a los usuarios que realizan solicitudes de clave. El usuario debe configurar la directiva de autorización de claves y el cliente (reproductor) debe conocerla para que se le entregue la clave. La directiva de autorización de clave de acceso podría tener una o más restricciones de autorización: abrir, restricción de token o restricción de IP.

Para obtener información detallada, consulte [Configuración de la directiva de autorización de claves de contenido](media-services-dotnet-configure-content-key-auth-policy.md).

##<a id="configure_asset_delivery_policy"></a>Configuración de directivas de entrega de recursos 

Configure la directiva de entrega de sus recursos. Algunos de los elementos que incluye la configuración de la directiva de entrega de recursos son:

- La URL de adquisición de clave. 
- El vector de inicialización (IV) que se usará para el cifrado Envelope. AES 128 requiere que se suministre el mismo IV en el cifrado y descifrado. 
- El protocolo de entrega de recursos (por ejemplo, MPEG DASH, HLS, HDS, Smooth Streaming o todos).
- El tipo de cifrado dinámico (por ejemplo, AES Envelope) o sin cifrado dinámico. 

Para obtener más información, consulte [Configuración de la directiva de entrega de recursos](media-services-rest-configure-asset-delivery-policy.md).

##<a id="create_locator"></a>Creación de un localizador de streaming a petición para obtener una URL de streaming

Deberá suministrar al usuario la URL de streaming para Smooth, DASH o HLS.

>[AZURE.NOTE]Si agrega o actualiza la directiva de entrega de recursos, debe eliminar un localizador existente (si hay) y crear uno nuevo.

Para obtener instrucciones sobre cómo publicar un recurso y generar una dirección URL de streaming, vea [Creación de una dirección URL de streaming](media-services-deliver-streaming-content.md).

##Obtención de un token de prueba

Obtenga un token de prueba basado en la restricción de token que se usó para la directiva de autorización de claves.

	// Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
	// back into a TokenRestrictionTemplate class instance.
	TokenRestrictionTemplate tokenTemplate = 
	    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
	
	// Generate a test token based on the data in the given TokenRestrictionTemplate.
	//The GenerateTestToken method returns the token without the word “Bearer” in front
	//so you have to add it in front of the token string. 
	string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
	Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

Puede usar [AMS Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) para probar la secuencia.

##<a id="client_request"></a>Cómo el cliente puede solicitar una clave al servicio de entrega de claves

En el paso anterior, creó la URL que apunta a un archivo de manifiesto. El cliente debe extraer la información necesaria de los archivos de manifiesto de streaming para realizar una solicitud al servicio de entrega de claves.

###Archivos de manifiesto

El cliente debe extraer el valor de la URL (que también contiene el identificador de la clave de contenido) del archivo de manifiesto. El cliente intentará entonces obtener la clave de cifrado del servicio de entrega de claves. El cliente también debe extraer el valor de IV y usarlo para descifrar la transmisión. El siguiente fragmento de código muestra el elemento <Protection> del manifiesto de Smooth Streaming.

	<Protection>
	  <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
	    <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
	      <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
	      <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
	      <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
	                        keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
	                                        kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
	    </ContentProtection>
	  </ProtectionHeader>
	</Protection>

En el caso de HLS, el manifiesto raíz se divide en archivos de segmento.

Por ejemplo, el manifiesto raíz es: http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl) y contiene una lista de nombres de archivo de segmento.
	
	. . . 
	#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
	QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
	#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
	QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
	…

Si abre uno de los archivos de segmento en el editor de texto (por ejemplo, http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl)), debe contener #EXT-X-KEY# que indica que el archivo está cifrado.
	
	#EXTM3U
	#EXT-X-VERSION:4
	#EXT-X-ALLOW-CACHE:NO
	#EXT-X-MEDIA-SEQUENCE:0
	#EXT-X-TARGETDURATION:9
	#EXT-X-KEY:METHOD=AES-128,
	URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
	     kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
	        IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
	#EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
	#EXTINF:8.425708,no-desc
	Fragments(video=0,format=m3u8-aapl)
	#EXT-X-ENDLIST
	
###Solicitud de la clave al servicio de entrega de claves

El código siguiente muestra cómo enviar una solicitud al servicio de entrega clave de Servicios multimedia mediante un URI de entrega de claves (que se ha extrajo del manifiesto) y un token (en este tema no se habla de cómo obtener tokens web simples de un servicio de token seguro).

	private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
	{
	    HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);
	            
	    request.Method = "POST";
	    request.ContentType = "text/xml";
	    if (!string.IsNullOrEmpty(token))
	    {
	        request.Headers[AuthorizationHeader] = token;
	    }
	    request.ContentLength = 0;
	
	    var response = request.GetResponse();
	 
	    var stream = response.GetResponseStream();
	    if (stream == null)
	    {
	        throw new NullReferenceException("Response stream is null");
	    }
	
	    var buffer = new byte[256];
	    var length = 0;
	    while (stream.CanRead && length <= buffer.Length)
	    {
	        var nexByte = stream.ReadByte();
	        if (nexByte == -1)
	        {
	            break;
	        }
	        buffer[length] = (byte)nexByte;
	        length++;
	    }
	    response.Close();
	
	    // AES keys must be exactly 16 bytes (128 bits).
	    var key = new byte[length];
	    Array.Copy(buffer, key, length);
	    return key;
	}
	
##<a id="example"></a>Ejemplo

1. Creación de un nuevo proyecto de consola
1. Use NuGet para instalar y agregar Extensiones del SDK de Servicios multimedia de Azure para .NET. Al instalar este paquete, también se instala el SDK de Servicios multimedia para .NET y agrega todas las demás dependencias necesarias.
2. Agregue el archivo de configuración que contiene el nombre de cuenta y la información de clave:

	
		<?xml version="1.0" encoding="utf-8"?>
		<configuration>
		    <startup> 
		        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		    </startup>
			  <appSettings>
			
			    <add key="MediaServicesAccountName" value="AccountName"/>
			    <add key="MediaServicesAccountKey" value="AccountKey"/>
			
			    <add key="Issuer" value="http://testacs.com"/>
			    <add key="Audience" value="urn:test"/>
			  </appSettings>
		</configuration>

1. Sobrescriba el código del archivo Program.cs con el código mostrado en esta sección.
	
	Asegúrese de actualizar las variables para que apunten a las carpetas donde se encuentran los archivos de entrada.
			
		
		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using System.Net;
		using System.Security.Cryptography;
		using System.Text;
		using System.Threading.Tasks;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using Newtonsoft.Json.Linq;
		using System.Threading;
		using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
		using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
		using System.Web;
		using System.Globalization;
		
		namespace AESDynamicEncryptionAndKeyDeliverySvc
		{
		    class Program
		    {
		        // Read values from the App.config file.
		        private static readonly string _mediaServicesAccountName =
		            ConfigurationManager.AppSettings["MediaServicesAccountName"];
		        private static readonly string _mediaServicesAccountKey =
		            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		
		        // A Uri describing the issuer of the token.  
		        // Must match the value in the token for the token to be considered valid.
		        private static readonly Uri _sampleIssuer =
		            new Uri(ConfigurationManager.AppSettings["Issuer"]);
		        // The Audience or Scope of the token.  
		        // Must match the value in the token for the token to be considered valid.
		        private static readonly Uri _sampleAudience =
		            new Uri(ConfigurationManager.AppSettings["Audience"]);
		
		        // Field for service context.
		        private static CloudMediaContext _context = null;
		        private static MediaServicesCredentials _cachedCredentials = null;
		
		        private static readonly string _mediaFiles =
		            Path.GetFullPath(@"../..\Media");
		
		        private static readonly string _singleMP4File =
		            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
		
		
		        static void Main(string[] args)
		        {
		            // Create and cache the Media Services credentials in a static class variable.
		            _cachedCredentials = new MediaServicesCredentials(
		                            _mediaServicesAccountName,
		                            _mediaServicesAccountKey);
		            // Used the chached credentials to create CloudMediaContext.
		            _context = new CloudMediaContext(_cachedCredentials);
		
		            bool tokenRestriction = false;
		            string tokenTemplateString = null;
		
		            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
		            Console.WriteLine("Uploaded asset: {0}", asset.Id);
		
		            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
		            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
		
		            IContentKey key = CreateEnvelopeTypeContentKey(encodedAsset);
		            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
		            Console.WriteLine();
		
		            if (tokenRestriction)
		                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
		            else
		                AddOpenAuthorizationPolicy(key);
		
		            Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
		            Console.WriteLine();
		
		            CreateAssetDeliveryPolicy(encodedAsset, key);
		            Console.WriteLine("Created asset delivery policy. \n");
		            Console.WriteLine();
		
		            if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
		            {
		                // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
		                // back into a TokenRestrictionTemplate class instance.
		                TokenRestrictionTemplate tokenTemplate =
		                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
		
		                // Generate a test token based on the data in the given TokenRestrictionTemplate.
		                // Note, you need to pass the key id Guid because we specified 
		                // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
		                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
		
		                //The GenerateTestToken method returns the token without the word “Bearer” in front
		                //so you have to add it in front of the token string. 
		                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
		                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
		                Console.WriteLine();
		            }
		
		            // You can use the bit.ly/aesplayer Flash player to test the URL 
		            // (with open authorization policy). 
		            // Paste the URL and click the Update button to play the video. 
		            //
		            string URL = GetStreamingOriginLocator(encodedAsset);
		            Console.WriteLine("Smooth Streaming Url: {0}/manifest", URL);
		            Console.WriteLine();
		            Console.WriteLine("HLS Url: {0}/manifest(format=m3u8-aapl)", URL);
		            Console.WriteLine();
		
		            Console.ReadLine();
		        }
		
		        static public IAsset UploadFileAndCreateAsset(string singleFilePath)
		        {
		            if (!File.Exists(singleFilePath))
		            {
		                Console.WriteLine("File does not exist.");
		                return null;
		            }
		
		            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
		            IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.StorageEncrypted);
		
		            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));
		
		            Console.WriteLine("Created assetFile {0}", assetFile.Name);
		
		            var policy = _context.AccessPolicies.Create(
		                                    assetName,
		                                    TimeSpan.FromDays(30),
		                                    AccessPermissions.Write | AccessPermissions.List);
		
		            var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);
		
		            Console.WriteLine("Upload {0}", assetFile.Name);
		
		            assetFile.Upload(singleFilePath);
		            Console.WriteLine("Done uploading {0}", assetFile.Name);
		
		            locator.Delete();
		            policy.Delete();
		
		            return inputAsset;
		        }
		
		        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
		        {
		            // Declare a new job.
		            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
		            // Get a media processor reference, and pass to it the name of the 
		            // processor to use for the specific task.
		            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
		
		            // Create a task with the encoding details, using a string preset.
		            // In this case "H264 Multiple Bitrate 720p" preset is used.
		            ITask task = job.Tasks.AddNew("My encoding task",
		                processor,
		                "H264 Multiple Bitrate 720p",
		                TaskOptions.None);
		
		            // Specify the input asset to be encoded.
		            task.InputAssets.Add(asset);
		            // Add an output asset to contain the results of the job. 
		            // This output is specified as AssetCreationOptions.None, which 
		            // means the output asset is not encrypted. 
		            task.OutputAssets.AddNew("Output asset",
		                AssetCreationOptions.None);
		
		            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
		            job.Submit();
		            job.GetExecutionProgressTask(CancellationToken.None).Wait();
		
		            return job.OutputMediaAssets[0];
		        }
		
		        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
		        {
		            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
		            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
		
		            if (processor == null)
		                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
		
		            return processor;
		        }
		
		        static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
		        {
		            // Create envelope encryption content key
		            Guid keyId = Guid.NewGuid();
		            byte[] contentKey = GetRandomBuffer(16);
		
		            IContentKey key = _context.ContentKeys.Create(
		                                    keyId,
		                                    contentKey,
		                                    "ContentKey",
		                                    ContentKeyType.EnvelopeEncryption);
		
		            // Associate the key with the asset.
		            asset.ContentKeys.Add(key);
		
		            return key;
		        }
		
		        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
		        {
		            // Create ContentKeyAuthorizationPolicy with Open restrictions 
		            // and create authorization policy             
		            IContentKeyAuthorizationPolicy policy = _context.
		                                    ContentKeyAuthorizationPolicies.
		                                    CreateAsync("Open Authorization Policy").Result;
		
		            List<ContentKeyAuthorizationPolicyRestriction> restrictions =
		                new List<ContentKeyAuthorizationPolicyRestriction>();
		
		            ContentKeyAuthorizationPolicyRestriction restriction =
		                new ContentKeyAuthorizationPolicyRestriction
		                {
		                    Name = "HLS Open Authorization Policy",
		                    KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
		                    Requirements = null // no requirements needed for HLS
		                        };
		
		            restrictions.Add(restriction);
		
		            IContentKeyAuthorizationPolicyOption policyOption =
		                _context.ContentKeyAuthorizationPolicyOptions.Create(
		                "policy",
		                ContentKeyDeliveryType.BaselineHttp,
		                restrictions,
		                "");
		
		            policy.Options.Add(policyOption);
		
		            // Add ContentKeyAutorizationPolicy to ContentKey
		            contentKey.AuthorizationPolicyId = policy.Id;
		            IContentKey updatedKey = contentKey.UpdateAsync().Result;
		            Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
		        }
		
		        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
		        {
		            string tokenTemplateString = GenerateTokenRequirements();
		
		            IContentKeyAuthorizationPolicy policy = _context.
		                                    ContentKeyAuthorizationPolicies.
		                                    CreateAsync("HLS token restricted authorization policy").Result;
		
		            List<ContentKeyAuthorizationPolicyRestriction> restrictions =
		                    new List<ContentKeyAuthorizationPolicyRestriction>();
		
		            ContentKeyAuthorizationPolicyRestriction restriction =
		                    new ContentKeyAuthorizationPolicyRestriction
		                    {
		                        Name = "Token Authorization Policy",
		                        KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
		                        Requirements = tokenTemplateString
		                    };
		
		            restrictions.Add(restriction);
		
		            //You could have multiple options 
		            IContentKeyAuthorizationPolicyOption policyOption =
		                _context.ContentKeyAuthorizationPolicyOptions.Create(
		                    "Token option for HLS",
		                    ContentKeyDeliveryType.BaselineHttp,
		                    restrictions,
		                    null  // no key delivery data is needed for HLS
		                    );
		
		            policy.Options.Add(policyOption);
		
		            // Add ContentKeyAutorizationPolicy to ContentKey
		            contentKey.AuthorizationPolicyId = policy.Id;
		            IContentKey updatedKey = contentKey.UpdateAsync().Result;
		            Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
		
		            return tokenTemplateString;
		        }
		
		        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
		        {
		            Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
		
		            string envelopeEncryptionIV = Convert.ToBase64String(GetRandomBuffer(16));
		
		            // The following policy configuration specifies: 
		            //   key url that will have KID=<Guid> appended to the envelope and
		            //   the Initialization Vector (IV) to use for the envelope encryption.
		            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
		                new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
		            {
		                        {AssetDeliveryPolicyConfigurationKey.EnvelopeKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
		                        {AssetDeliveryPolicyConfigurationKey.EnvelopeEncryptionIVAsBase64, envelopeEncryptionIV}
		            };
		
		            IAssetDeliveryPolicy assetDeliveryPolicy =
		                _context.AssetDeliveryPolicies.Create(
		                            "AssetDeliveryPolicy",
		                            AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
		                            AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS,
		                            assetDeliveryPolicyConfiguration);
		
		            // Add AssetDelivery Policy to the asset
		            asset.DeliveryPolicies.Add(assetDeliveryPolicy);
		            Console.WriteLine();
		            Console.WriteLine("Adding Asset Delivery Policy: " +
		                assetDeliveryPolicy.AssetDeliveryPolicyType);
		        }
		
		        static public string GetStreamingOriginLocator(IAsset asset)
		        {
		
		            // Get a reference to the streaming manifest file from the  
		            // collection of files in the asset. 
		
		            var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
		                                        EndsWith(".ism")).
		                                        FirstOrDefault();
		
		            // Create a 30-day readonly access policy. 
                	// You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
		            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
		                TimeSpan.FromDays(30),
		                AccessPermissions.Read);
		
		            // Create a locator to the streaming content on an origin. 
		            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
		                policy,
		                DateTime.UtcNow.AddMinutes(-5));
		
		            // Create a URL to the manifest file. 
		            return originLocator.Path + assetFile.Name;
		        }
		
		        static private string GenerateTokenRequirements()
		        {
		            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
		
		            template.PrimaryVerificationKey = new SymmetricVerificationKey();
		            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
		            template.Audience = _sampleAudience.ToString();
		            template.Issuer = _sampleIssuer.ToString();
		
		            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
		
		            return TokenRestrictionTemplateSerializer.Serialize(template);
		        }
		
		        static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
		        {
		            Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
		                ((IJob)sender).Name,
		                e.CurrentState,
		                DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
		        }
		
		        static private byte[] GetRandomBuffer(int size)
		        {
		            byte[] randomBytes = new byte[size];
		            using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
		            {
		                rng.GetBytes(randomBytes);
		            }
		
		            return randomBytes;
		        }
		    }
		}


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0211_2016-->