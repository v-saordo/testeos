<properties 
	pageTitle="Creación de canales que realizan la codificación en directo de transmisiones de una sola velocidad de bits a transmisiones de varias velocidades de bits con .NET SDK" 
	description="Este tutorial le guía por los pasos para crear un canal que reciba una secuencia en directo de una sola velocidad de bits y la codifique como secuencia de varias velocidades de bits con .NET SDK." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako,anilmur" 
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


#Creación de canales que realizan la codificación en directo de transmisiones de una sola velocidad de bits a transmisiones de varias velocidades de bits con .NET SDK

> [AZURE.SELECTOR]
- [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [REST API](https://msdn.microsoft.com/library/azure/dn783458.aspx)

> [AZURE.NOTE]
> Para completar este tutorial, deberá tener una cuenta de Azure. Para obtener más información, consulte [Evaluación gratuita de Azure](/pricing/free-trial/?WT.mc_id=A261C142F). 

##Información general

Este tutorial le guía por los pasos para crear un **canal** que reciba una secuencia en directo de una sola velocidad de bits y la codifique como secuencia de varias velocidades de bits.

Para obtener información más detallada sobre los canales habilitados para la codificación en directo, consulte [Uso de canales que realizan la codificación en directo de una secuencia de una sola velocidad de bits a otra de varias velocidades](media-services-manage-live-encoder-enabled-channels.md).


##Escenario común de streaming en vivo

Los pasos siguientes describen las tareas para crear aplicaciones comunes de streaming en vivo.

>[AZURE.NOTE] Actualmente, la duración máxima recomendada de un evento en directo es de 8 horas. Si necesita ejecutar un canal durante largos períodos de tiempo, póngase en contacto con amslived en Microsoft.com.

1. Conecte una cámara de vídeo a un equipo. Inicie y configure un codificador local en directo que pueda generar una secuencia de una sola velocidad de bits en uno de los siguientes protocolos: RTMP, Smooth Streaming o RTP (MPEG-TS). Para obtener más información, consulte [Compatibilidad con RTMP de Servicios multimedia de Azure y codificadores en directo](http://go.microsoft.com/fwlink/?LinkId=532824).

	Este paso también puede realizarse después de crear el canal.

1. Cree e inicie un canal.

1. Recupere la URL de ingesta de canales.

	El codificador en directo usa la URL de ingesta para enviar la secuencia al canal.

1. Recupere la URL de vista previa de canal.

	Use esta dirección URL para comprobar que el canal recibe correctamente la secuencia en directo.

2. Cree un recurso.
3. Si desea que el recurso se cifre dinámicamente durante la reproducción, haga lo siguiente:
1. Cree una clave de contenido.
1. Configure la directiva de autorización de claves de contenido.
1. Configure la directiva de entrega de recursos (usada por el empaquetado y el cifrado dinámicos).
3. Cree un programa y especifique que se use el recurso que ha creado.
1. Publique el recurso asociado al programa mediante la creación de un localizador a petición.

	Asegúrese de tener al menos una unidad de streaming reservada en el extremo de streaming desde el que desea transmitir el contenido.

1. Inicie el programa cuando esté listo para iniciar el streaming y el archivo.
2. Si lo desea, puede señalar el codificador en directo para iniciar un anuncio. El anuncio se inserta en el flujo de salida.
1. Detenga el programa cuando quiera detener el streaming y el archivo del evento.
1. Elimine el programa (y, opcionalmente, elimine el recurso).

## Temas que se abordarán

Este tema muestra cómo ejecutar distintas operaciones en los canales y programas mediante el SDK .NET de Servicios multimedia. Dado que la ejecución de muchas de las operaciones es prolongada, se usan las API de .NET que administran operaciones de este tipo.

En el tema se muestra cómo:

1. Crear e iniciar un canal. Se usan las API de ejecución prolongada.
1. Obtener el extremo de ingesta (entrada) de los canales. Este extremo debe proporcionarse al codificador que puede enviar una secuencia en directo de una sola velocidad de bits.
1. Obtener el extremo de vista previa. Este extremo se usa para obtener una vista previa de la secuencia.
1. Crear un recurso que se usará para almacenar el contenido. También deben configurarse las directivas de entrega de recursos, como se muestra en este ejemplo.
1. Crear un programa y especificar que se use el recurso creado anteriormente. Iniciar el programa. Se usan las API de ejecución prolongada.
1. Crear un localizador para el recurso, para que el contenido se publique y pueda transmitirse en vivo mediante streaming a los clientes.
1. Mostrar y ocultar pizarras. Iniciar y detener anuncios. Se usan las API de ejecución prolongada.
1. Limpiar el canal y todos los recursos asociados.


##Requisitos previos

Los siguientes requisitos son necesarios para completar el tutorial.

- Para completar este tutorial, deberá tener una cuenta de Azure. 
	
	En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](/pricing/free-trial/?WT.mc_id=A261C142F). Obtenga créditos que puede usar para probar los servicios de Azure de pago. Incluso después de que se agoten los créditos, puede mantener la cuenta y usar los servicios y características gratuitos de Azure, como la característica de Aplicaciones web del Servicio de aplicaciones de Azure.
- Una cuenta de Servicios multimedia. Para crear una cuenta de Servicios multimedia, consulte el tema de [creación de cuenta](media-services-create-account.md).
- Visual Studio 2010 SP1 (Professional, Premium, Ultimate o Express) o versiones posteriores.
- Debe usar el SDK de Servicios multimedia para .NET versión 3.2.0.0 o posterior.
- Una cámara web y un codificador que pueda enviar una secuencia en vivo de una sola velocidad de bits.

##Consideraciones

- Actualmente, la duración máxima recomendada de un evento en directo es de 8 horas. Si necesita ejecutar un canal durante largos períodos de tiempo, póngase en contacto con amslived en Microsoft.com.
- Asegúrese de tener al menos una unidad de streaming reservada en el extremo de streaming desde el que desea transmitir el contenido.

##Descarga de un ejemplo

Obtenga y ejecute un ejemplo desde [aquí](https://azure.microsoft.com/documentation/samples/media-services-dotnet-encode-live-stream-with-ams-clear/).


##Configuración para el desarrollo con el SDK de Servicios multimedia para .NET

1. Cree una aplicación de consola mediante Visual Studio.
1. Agregue el SDK de Servicios multimedia para .NET a la aplicación de consola mediante el paquete NuGet correspondiente.

##Conexión con Servicios multimedia
Se recomienda usar un archivo app.config para almacenar la clave de cuenta y nombre de Servicios multimedia.

>[AZURE.NOTE]Para buscar los valores de nombre y clave, vaya al Portal de Azure clásico, seleccione la cuenta de Servicios multimedia y haga clic en el icono "ADMINISTRAR CLAVES" en la parte inferior de la ventana del portal. Al hacer clic en el icono junto a cada cuadro de texto, se copia el valor al Portapapeles del sistema.

Agregue la sección appSettings al archivo app.config y establezca los valores de nombre y clave de la cuenta de Servicios multimedia.


	<?xml version="1.0"?>
	<configuration>
	  <appSettings>
	      <add key="MediaServicesAccountName" value="YouMediaServicesAccountName" />
	      <add key="MediaServicesAccountKey" value="YouMediaServicesAccountKey" />
	  </appSettings>
	</configuration>
	 
	
##Ejemplo de código

	using System;
	using System.Collections.Generic;
	using System.Configuration;
	using System.IO;
	using System.Linq;
	using System.Net;
	using Microsoft.WindowsAzure.MediaServices.Client;
	using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
	
	namespace EncodeLiveStreamWithAmsClear
	{
	    class Program
	    {
	        private const string ChannelName = "channel001";
	        private const string AssetlName = "asset001";
	        private const string ProgramlName = "program001";
	
	        // Read values from the App.config file.
	        private static readonly string _mediaServicesAccountName =
	            ConfigurationManager.AppSettings["MediaServicesAccountName"];
	        private static readonly string _mediaServicesAccountKey =
	            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	
	        // Field for service context.
	        private static CloudMediaContext _context = null;
	        private static MediaServicesCredentials _cachedCredentials = null;
	
	
	        static void Main(string[] args)
	        {
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName,
	                            _mediaServicesAccountKey);
	            // Used the cached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	
	            IChannel channel = CreateAndStartChannel();
	
	            // The channel's input endpoint:
	            string ingestUrl = channel.Input.Endpoints.FirstOrDefault().Url.ToString();
	
	            Console.WriteLine("Intest URL: {0}", ingestUrl);
	
	
	            // Use the previewEndpoint to preview and verify 
	            // that the input from the encoder is actually reaching the Channel. 
	            string previewEndpoint = channel.Preview.Endpoints.FirstOrDefault().Url.ToString();
	
	            Console.WriteLine("Preview URL: {0}", previewEndpoint);
	
	            // When Live Encoding is enabled, you can now get a preview of the live feed as it reaches the Channel. 
	            // This can be a valuable tool to check whether your live feed is actually reaching the Channel. 
	            // The thumbnail is exposed via the same end-point as the Channel Preview URL.
	            string thumbnailUri = new UriBuilder
	            {
	                Scheme = Uri.UriSchemeHttps,
	                Host = channel.Preview.Endpoints.FirstOrDefault().Url.Host,
	                Path = "thumbnails/input.jpg"
	            }.Uri.ToString();
	
	            Console.WriteLine("Thumbain URL: {0}", thumbnailUri);
	
	            // Once you previewed your stream and verified that it is flowing into your Channel, 
	            // you can create an event by creating an Asset, Program, and Streaming Locator. 
	            IAsset asset = CreateAndConfigureAsset();
	
	            IProgram program = CreateAndStartProgram(channel, asset);
	
	            ILocator locator = CreateLocatorForAsset(program.Asset, program.ArchiveWindowLength);
	
	            // You can use slates and ads only if the channel type is Standard.  
	            StartStopAdsSlates(channel);
	
	            // Once you are done streaming, clean up your resources.
	            Cleanup(channel);
	
	        }
	
	        public static IChannel CreateAndStartChannel()
	        {
	            var channelInput = CreateChannelInput();
	            var channePreview = CreateChannelPreview();
	            var channelEncoding = CreateChannelEncoding();
	
	
	            ChannelCreationOptions options = new ChannelCreationOptions
	            {
	                EncodingType = ChannelEncodingType.Standard,
	                Name = ChannelName,
	                Input = channelInput,
	                Preview = channePreview,
	                Encoding = channelEncoding
	            };
	
	            Log("Creating channel");
	            IOperation channelCreateOperation = _context.Channels.SendCreateOperation(options);
	            string channelId = TrackOperation(channelCreateOperation, "Channel create");
	
	            IChannel channel = _context.Channels.Where(c => c.Id == channelId).FirstOrDefault();
	
	            Log("Starting channel");
	            var channelStartOperation = channel.SendStartOperation();
	            TrackOperation(channelStartOperation, "Channel start");
	
	            return channel;
	        }
	
	        /// <summary>
	        /// Create channel input, used in channel creation options. 
	        /// </summary>
	        /// <returns></returns>
	        private static ChannelInput CreateChannelInput()
	        {
	            return new ChannelInput
	            {
	                StreamingProtocol = StreamingProtocol.RTPMPEG2TS,
	                AccessControl = new ChannelAccessControl
	                {
	                    IPAllowList = new List<IPRange>
	                    {
	                        new IPRange
	                        {
	                            Name = "TestChannelInput001",
	                            Address = IPAddress.Parse("0.0.0.0"),
	                            SubnetPrefixLength = 0
	                        }
	                    }
	                }
	            };
	        }
	
	        /// <summary>
	        /// Create channel preview, used in channel creation options. 
	        /// </summary>
	        /// <returns></returns>
	        private static ChannelPreview CreateChannelPreview()
	        {
	            return new ChannelPreview
	            {
	                AccessControl = new ChannelAccessControl
	                {
	                    IPAllowList = new List<IPRange>
	                    {
	                        new IPRange
	                        {
	                            Name = "TestChannelPreview001",
	                            Address = IPAddress.Parse("0.0.0.0"),
	                            SubnetPrefixLength = 0
	                        }
	                    }
	                }
	            };
	        }
	
	        /// <summary>
	        /// Create channel encoding, used in channel creation options. 
	        /// </summary>
	        /// <returns></returns>
	        private static ChannelEncoding CreateChannelEncoding()
	        {
	            return new ChannelEncoding
	            {
	                SystemPreset = "Default720p",
	                IgnoreCea708ClosedCaptions = false,
	                AdMarkerSource = AdMarkerSource.Api,
	                // You can only set audio if streaming protocol is set to StreamingProtocol.RTPMPEG2TS.
	                AudioStreams = new List<AudioStream> { new AudioStream { Index = 103, Language = "eng" } }.AsReadOnly()
	            };
	        }
	
	        /// <summary>
	        /// Create an asset and configure asset delivery policies.
	        /// </summary>
	        /// <returns></returns>
	        public static IAsset CreateAndConfigureAsset()
	        {
	            IAsset asset = _context.Assets.Create(AssetlName, AssetCreationOptions.None);
	
	            IAssetDeliveryPolicy policy =
	                _context.AssetDeliveryPolicies.Create("Clear Policy",
	                AssetDeliveryPolicyType.NoDynamicEncryption,
	                AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
	
	            asset.DeliveryPolicies.Add(policy);
	
	            return asset;
	        }
	
	        /// <summary>
	        /// Create a Program on the Channel. You can have multiple Programs that overlap or are sequential;
	        /// however each Program must have a unique name within your Media Services account.
	        /// </summary>
	        /// <param name="channel"></param>
	        /// <param name="asset"></param>
	        /// <returns></returns>
	        public static IProgram CreateAndStartProgram(IChannel channel, IAsset asset)
	        {
	            IProgram program = channel.Programs.Create(ProgramlName, TimeSpan.FromHours(3), asset.Id);
	            Log("Program created", program.Id);
	
	            Log("Starting program");
	            var programStartOperation = program.SendStartOperation();
	            TrackOperation(programStartOperation, "Program start");
	
	            return program;
	        }
	
	        /// <summary>
	        /// Create locators in order to be able to publish and stream the video.
	        /// </summary>
	        /// <param name="asset"></param>
	        /// <param name="ArchiveWindowLength"></param>
	        /// <returns></returns>
	        public static ILocator CreateLocatorForAsset(IAsset asset, TimeSpan ArchiveWindowLength)
	        {
             	// You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
	            var locator = _context.Locators.CreateLocator
	                (
	                    LocatorType.OnDemandOrigin,
	                    asset,
	                    _context.AccessPolicies.Create
	                        (
	                            "Live Stream Policy",
	                            ArchiveWindowLength,
	                            AccessPermissions.Read
	                        )
	                );
	
	            return locator;
	        }
	
	        /// <summary>
	        /// Perform operations on slates.
	        /// </summary>
	        /// <param name="channel"></param>
	        public static void StartStopAdsSlates(IChannel channel)
	        {
	            int cueId = new Random().Next(int.MaxValue);
	            var path = Path.GetFullPath(Path.Combine(AppDomain.CurrentDomain.BaseDirectory, @"..\\..\\SlateJPG\\DefaultAzurePortalSlate.jpg"));
	
	            Log("Creating asset");
	            var slateAsset = _context.Assets.Create("Slate test asset " + DateTime.Now.ToString("yyyy-MM-dd HH-mm"), AssetCreationOptions.None);
	            Log("Slate asset created", slateAsset.Id);
	
	            Log("Uploading file");
	            var assetFile = slateAsset.AssetFiles.Create("DefaultAzurePortalSlate.jpg");
	            assetFile.Upload(path);
	            assetFile.IsPrimary = true;
	            assetFile.Update();
	
	            Log("Showing slate");
	            var showSlateOpeartion = channel.SendShowSlateOperation(TimeSpan.FromMinutes(1), slateAsset.Id);
	            TrackOperation(showSlateOpeartion, "Show slate");
	
	            Log("Hiding slate");
	            var hideSlateOperation = channel.SendHideSlateOperation();
	            TrackOperation(hideSlateOperation, "Hide slate");
	
	            Log("Starting ad");
	            var startAdOperation = channel.SendStartAdvertisementOperation(TimeSpan.FromMinutes(1), cueId, false);
	            TrackOperation(startAdOperation, "Start ad");
	
	            Log("Ending ad");
	            var endAdOperation = channel.SendEndAdvertisementOperation(cueId);
	            TrackOperation(endAdOperation, "End ad");
	
	            Log("Deleting slate asset");
	            slateAsset.Delete();
	        }
	
	        /// <summary>
	        /// Clean up resources associated with the channel.
	        /// </summary>
	        /// <param name="channel"></param>
	        public static void Cleanup(IChannel channel)
	        {
	            IAsset asset;
	            if (channel != null)
	            {
	                foreach (var program in channel.Programs)
	                {
	                    asset = _context.Assets.Where(se => se.Id == program.AssetId)
	                                            .FirstOrDefault();
	
	                    Log("Stopping program");
	                    var programStopOperation = program.SendStopOperation();
	                    TrackOperation(programStopOperation, "Program stop");
	
	                    program.Delete();
	
	                    if (asset != null)
	                    {
	                        Log("Deleting locators");
	                        foreach (var l in asset.Locators)
	                            l.Delete();
	
	                        Log("Deleting asset");
	                        asset.Delete();
	                    }
	                }
	
	                Log("Stopping channel");
	                var channelStopOperation = channel.SendStopOperation();
	                TrackOperation(channelStopOperation, "Channel stop");
	
	                Log("Deleting channel");
	                var channelDeleteOperation = channel.SendDeleteOperation();
	                TrackOperation(channelDeleteOperation, "Channel delete");
	            }
	        }
	
	
	        /// <summary>
	        /// Track long running operations.
	        /// </summary>
	        /// <param name="operation"></param>
	        /// <param name="description"></param>
	        /// <returns></returns>
	        public static string TrackOperation(IOperation operation, string description)
	        {
	            string entityId = null;
	            bool isCompleted = false;
	
	            Log("starting to track ", null, operation.Id);
	            while (isCompleted == false)
	            {
	                operation = _context.Operations.GetOperation(operation.Id);
	                isCompleted = IsCompleted(operation, out entityId);
	                System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
	            }
	            // If we got here, the operation succeeded.
	            Log(description + " in completed", operation.TargetEntityId, operation.Id);
	
	            return entityId;
	        }
	
	        /// <summary> 
	        /// Checks if the operation has been completed. 
	        /// If the operation succeeded, the created entity Id is returned in the out parameter.
	        /// </summary> 
	        /// <param name="operationId">The operation Id.</param> 
	        /// <param name="channel">
	        /// If the operation succeeded, 
	        /// the entity Id associated with the sucessful operation is returned in the out parameter.</param>
	        /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
	        private static bool IsCompleted(IOperation operation, out string entityId)
	        {
	
	            bool completed = false;
	
	            entityId = null;
	
	            switch (operation.State)
	            {
	                case OperationState.Failed:
	                    // Handle the failure. 
	                    // For example, throw an exception. 
	                    // Use the following information in the exception: operationId, operation.ErrorMessage.
	                    Log("operation failed", operation.TargetEntityId, operation.Id);
	                    break;
	                case OperationState.Succeeded:
	                    completed = true;
	                    entityId = operation.TargetEntityId;
	                    break;
	                case OperationState.InProgress:
	                    completed = false;
	                    Log("operation in progress", operation.TargetEntityId, operation.Id);
	                    break;
	            }
	            return completed;
	        }
	
	
	        private static void Log(string action, string entityId = null, string operationId = null)
	        {
	            Console.WriteLine(
	                "{0,-21}{1,-51}{2,-51}{3,-51}",
	                DateTime.Now.ToString("yyyy'-'MM'-'dd HH':'mm':'ss"),
	                action,
	                entityId ?? string.Empty,
	                operationId ?? string.Empty);
	        }
	    }
	}	


##Siguientes pasos: Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### ¿Busca alguna otra cosa?

Si este tema no contiene lo que esperaba, falta algo o no satisface de alguna forma sus necesidades, háganos llegar sus comentarios mediante el subproceso de Disqus siguiente.

<!---HONumber=AcomDC_0211_2016-->