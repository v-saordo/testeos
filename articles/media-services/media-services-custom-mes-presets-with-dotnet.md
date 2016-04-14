<properties 
	pageTitle="Realización de tareas de codificación avanzadas mediante la personalización de valores preestablecidos de Media Encoder Estándar" 
	description="En este tema se muestra cómo realizar codificación avanzada mediante la personalización de valores preestablecidos de tarea Media Encoder Estándar. En este tema se muestra cómo usar el SDK de .NET de Servicios multimedia para crear, actualizar y eliminar filtros. También se muestra cómo especificar valores preestablecidos personalizados para el trabajo de codificación." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2016"    
	ms.author="juliako"/>


#Realización de tareas de codificación avanzadas mediante la personalización de valores preestablecidos de Media Encoder Estándar

##Información general

En este tema se muestra cómo realizar codificación avanzada mediante la personalización de valores preestablecidos de tarea Media Encoder Estándar. Se muestra en el tema sobre [uso de .NET para crear una tarea de codificación y un trabajo que ejecute esta tarea](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet). También se muestra cómo especificar valores preestablecidos personalizados para la tarea de codificación. [Este documento](https://msdn.microsoft.com/library/mt269962.aspx) contiene descripciones de elementos que usan estos valores preestablecidos predeterminados.

Se muestran los valores preestablecidos personalizados que realizan las siguientes tareas de codificación:

- [Generación de miniaturas](media-services-custom-mes-presets-with-dotnet.md#thumbnails)
- [Recorte de un vídeo](media-services-custom-mes-presets-with-dotnet.md#trim_video)
- [Creación de una superposición](media-services-custom-mes-presets-with-dotnet.md#overlay)
- [Inserción de una pista de audio silenciosa cuando la entrada no tiene audio](media-services-custom-mes-presets-with-dotnet.md#silent_audio)
- [Deshabilitar el entrelazado automático](media-services-custom-mes-presets-with-dotnet.md#deinterlacing)

##<a id="encoding_with_dotnet"></a>Codificación con el SDK de .NET de Servicios multimedia

En el ejemplo de código siguiente se usa el último SDK para .NET de Servicios multimedia para realizar las siguientes tareas:

- Crear un trabajo de codificación.
- Obtener una referencia al codificador Codificador multimedia estándar.
- Cargar el valor preestablecido personalizado JSON o XML. Puede guardar el [XML](media-services-custom-mes-presets-with-dotnet.md#xml) o [JSON](media-services-custom-mes-presets-with-dotnet.md#json) en un archivo y usar el siguiente código para cargar el archivo.

			// Load the XML (or JSON) from the local file.
		    string configuration = File.ReadAllText(fileName);  
- Agregar una única tarea de codificación al trabajo. 
- Especificar el recurso de entrada que se va a codificar.
- Crear un recurso de salida que contendrá el recurso codificado.
- Agregar un controlador de eventos para comprobar el progreso del trabajo.
- Envíe el trabajo.
	
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
		
		namespace CustomizeMESPresests
		{
		    class Program
		    {
		        // Read values from the App.config file.
		        private static readonly string _mediaServicesAccountName =
		            ConfigurationManager.AppSettings["MediaServicesAccountName"];
		        private static readonly string _mediaServicesAccountKey =
		            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		
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
		
		            // Get an uploaded asset.
		            var asset = _context.Assets.FirstOrDefault();
		
		            // Encode and generate the output using custom presets.
		            EncodeToAdaptiveBitrateMP4Set(asset);
		
		            Console.ReadLine();
		        }
		
		        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
				{
				    // Declare a new job.
				    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
				    // Get a media processor reference, and pass to it the name of the 
				    // processor to use for the specific task.
				    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
				
		
				    // Load the XML (or JSON) from the local file.
				    string configuration = File.ReadAllText("CustomPreset_JSON.json");
				
				    // Create a task
		            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
		                processor,
		                configuration,
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
		
		        static public IAsset UploadMediaFilesFromFolder(string folderPath)
		        {
		            IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);
		
		            foreach (var af in asset.AssetFiles)
		            {
		                // The following code assumes 
		                // you have an input folder with one MP4 and one overlay image file.
		                if (af.Name.Contains(".mp4"))
		                    af.IsPrimary = true;
		                else
		                    af.IsPrimary = false;
		
		                af.Update();
		            }
		
		            return asset;
		        }
		
		
		        static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
		        {
		            // Declare a new job.
		            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
		            // Get a media processor reference, and pass to it the name of the 
		            // processor to use for the specific task.
		            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
		
		            // Load the XML (or JSON) from the local file.
		            string configuration = File.ReadAllText(customPresetFileName);
		
		            // Create a task
		            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
		                processor,
		                configuration,
		                TaskOptions.None);
		
		            // Specify the input assets to be encoded.
		            // This asset contains a source file and an overlay file.
		            task.InputAssets.Add(assetSource);
		
		            // Add an output asset to contain the results of the job. 
		            task.OutputAssets.AddNew("Output asset",
		                AssetCreationOptions.None);
		
		            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
		            job.Submit();
		            job.GetExecutionProgressTask(CancellationToken.None).Wait();
		
		            return job.OutputMediaAssets[0];
		        }
		

		        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
		        {
		            Console.WriteLine("Job state changed event:");
		            Console.WriteLine("  Previous state: " + e.PreviousState);
		            Console.WriteLine("  Current state: " + e.CurrentState);
		            switch (e.CurrentState)
		            {
		                case JobState.Finished:
		                    Console.WriteLine();
		                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
		                    break;
		                case JobState.Canceling:
		                case JobState.Queued:
		                case JobState.Scheduled:
		                case JobState.Processing:
		                    Console.WriteLine("Please wait...\n");
		                    break;
		                case JobState.Canceled:
		                case JobState.Error:
		
		                    // Cast sender as a job.
		                    IJob job = (IJob)sender;
		
		                    // Display or log error details as needed.
		                    break;
		                default:
		                    break;
		            }
		        }
		
		
		        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
		        {
		            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
		            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
		
		            if (processor == null)
		                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
		
		            return processor;
		        }
		
		    }
		}


##<a id="thumbnails"></a>Generación de miniaturas

En esta sección se muestra cómo personalizar un valor preestablecido que genera vistas en miniatura. El valor preestablecido que se define a continuación contiene información sobre cómo se quiere codificar el archivo, así como la información necesaria para generar miniaturas. Puede usar cualquiera de los valores preestablecidos MES que se documentan [aquí](https://msdn.microsoft.com/library/mt269960.aspx) y agregar el código que genera miniaturas.

>[AZURE.NOTE]La configuración de **SceneChangeDetection** en el siguiente valor preestablecido solo puede establecerse en true si va a codificar en vídeo de una única velocidad de bits. Si va a codificar en vídeo de varias velocidades de bits y establece **SceneChangeDetection** en true, el codificador devolverá un error.


Para obtener información sobre el esquema, consulte [este](https://msdn.microsoft.com/library/mt269962.aspx) tema.

Asegúrese de revisar la sección [Consideraciones](media-services-custom-mes-presets-with-dotnet.md#considerations).

###<a id="json"></a>Valor preestablecido JSON


	{
	  "Version": 1.0,
	  "Codecs": [
	    {
	      "KeyFrameInterval": "00:00:02",
	      "SceneChangeDetection": "true",
	      "H264Layers": [
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 4500,
	          "MaxBitrate": 4500,
	          "BufferWindow": "00:00:05",
	          "Width": 1280,
	          "Height": 720,
	          "ReferenceFrames": 3,
	          "EntropyMode": "Cabac",
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	   
	        }
	      ],
	      "Type": "H264Video"
	    },
	    {
	      "JpgLayers": [
	        {
	          "Quality": 90,
	          "Type": "JpgLayer",
	          "Width": 640,
	          "Height": 360
	        }
	      ],
	      "Start": "{Best}",
	      "Type": "JpgImage"
	    },
	    {
	      "PngLayers": [
	        {
	          "Type": "PngLayer",
	          "Width": 640,
	          "Height": 360,
	        }
	      ],
	      "Start": "00:00:01",
		  "Step": "00:00:10",
	      "Range": "00:00:58",
	      "Type": "PngImage"
	    },
	    {
	      "BmpLayers": [
	        {
	          "Type": "BmpLayer",
	          "Width": 640,
	          "Height": 360
	        }
	      ],
	      "Start": "10%",
		  "Step": "10%",
	      "Range": "90%",
	      "Type": "BmpImage"
	    },
	    {
	      "Channels": 2,
	      "SamplingRate": 48000,
	      "Bitrate": 128,
	      "Type": "AACAudio"
	    }
	  ],
	  "Outputs": [
	    {
	      "FileName": "{Basename}_{Index}{Extension}",
	      "Format": {
	        "Type": "JpgFormat"
	      }
	    },
	    {
	      "FileName": "{Basename}_{Index}{Extension}",
	      "Format": {
	        "Type": "PngFormat"
	      }
	    },
	    {
	      "FileName": "{Basename}_{Index}{Extension}",
	      "Format": {
	        "Type": "BmpFormat"
	      }
	    },
	    {
	      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
	      "Format": {
	        "Type": "MP4Format"
	      }
	    }
	  ]
	}


###<a id="xml"></a>Valor preestablecido XML


	<?xml version="1.0" encoding="utf-16"?>
	<Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
	  <Encoding>
	    <H264Video>
	      <KeyFrameInterval>00:00:02</KeyFrameInterval>
	      <SceneChangeDetection>true</SceneChangeDetection>
	      <H264Layers>
	        <H264Layer>
	          <Bitrate>4500</Bitrate>
	          <Width>1280</Width>
	          <Height>720</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Auto</Profile>
	          <Level>auto</Level>
	          <BFrames>3</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cabac</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>4500</MaxBitrate>
	        </H264Layer>
	      </H264Layers>
	    </H264Video>
	    <AACAudio>
	      <Profile>AACLC</Profile>
	      <Channels>2</Channels>
	      <SamplingRate>48000</SamplingRate>
	      <Bitrate>128</Bitrate>
	    </AACAudio>
	    <JpgImage Start="{Best}">
	      <JpgLayers>
	        <JpgLayer>
	          <Width>640</Width>
	          <Height>360</Height>
	          <Quality>90</Quality>
	        </JpgLayer>
	      </JpgLayers>
	    </JpgImage>
	    <BmpImage Start="10%" Step="10%" Range="90%">
	      <BmpLayers>
	        <BmpLayer>
	          <Width>640</Width>
	          <Height>360</Height>
	        </BmpLayer>
	      </BmpLayers>
	    </BmpImage>
	    <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
	      <PngLayers>
	        <PngLayer>
	          <Width>640</Width>
	          <Height>360</Height>
	        </PngLayer>
	      </PngLayers>
	    </PngImage>
	  </Encoding>
	  <Outputs>
	    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
	      <MP4Format />
	    </Output>
	    <Output FileName="{Basename}_{Index}{Extension}">
	      <JpgFormat />
	    </Output>
	    <Output FileName="{Basename}_{Index}{Extension}">
	      <BmpFormat />
	    </Output>
	    <Output FileName="{Basename}_{Index}{Extension}">
	      <PngFormat />
	    </Output>
	  </Outputs>
	</Preset>

###Consideraciones

Se aplican las siguientes consideraciones:

- El uso de marcas de tiempo explícitas para inicio/paso/intervalo asume que el origen de la entrada tiene al menos 1 minuto de duración.
- Los elementos Jpg/Png/BmpImage tienen atributos de cadena Start, Step y Range, que se pueden interpretar como:

	- Número de marco si son enteros no negativos, por ejemplo, "Start": "120",
	- Relativos a la duración de origen si se expresan como sufijo de %, por ejemplo, "Start": "15%", O
	- Marca de tiempo si se expresan como formato HH:MM:SS… P. ej. "Start" : "00:01:00"

	Puede mezclar y hacer coincidir notaciones a su conveniencia.
	
	Además, Start también admite una macro especial:{Best}, que intenta determinar el primer marco "interesante" del contenido. NOTA: (Step y Range se omiten cuando Start se establece en {Best}).
	
	- Valores predeterminados: Start:{Best}
- Es necesario proporcionar explícitamente el formato de salida para cada formato de imagen: Jpg, Png o BmpFormat. Cuando está presente, MES hará coincidir JpgVideo con JpgFormat y así sucesivamente. OutputFormat presenta una nueva macro específica de códec de imagen: {Index}, que debe estar presente (una vez y sólo una vez) para formatos de salida de imagen.

##<a id="trim_video"></a>Recorte de un vídeo

En esta sección se habla sobre cómo modificar los valores preestablecidos del codificador para recortar el vídeo de entrada donde la entrada es un archivo denominado intermedio o a petición. El codificador también puede usarse para recortar un activo que se captura o se archiva desde una transmisión en directo; los detalles para ello están disponibles en [este blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Para recortar vídeos, puede usar cualquiera de los valores preestablecidos de MES que se documentan [aquí](https://msdn.microsoft.com/library/mt269960.aspx) y modificar el elemento **Sources** (tal y como se muestra a continuación). El valor de StartTime debe coincidir con las marcas de tiempo absoluto de la entrada de vídeo. Por ejemplo, si el primer fotograma del vídeo de entrada tiene una marca de tiempo de 12:00:10.000, StartTime debe ser al menos 12:00:10.000 o un valor superior. En el ejemplo siguiente, se supone que el vídeo de entrada tiene una marca de tiempo inicial de cero. Tenga en cuenta que el elemento **Sources** debe colocarse en la parte superior del esquema.
 
###<a id="json"></a>Valor preestablecido JSON
	
	{
	  "Version": 1.0,
	  "Sources": [
	    {
	      "StartTime": "00:00:04",
	      "Duration": "00:00:16"
	    }
	  ],
	  "Codecs": [
	    {
	      "KeyFrameInterval": "00:00:02",
	      "StretchMode": "AutoSize",
	      "H264Layers": [
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 3400,
	          "MaxBitrate": 3400,
	          "BufferWindow": "00:00:05",
	          "Width": 1280,
	          "Height": 720,
	          "BFrames": 3,
	          "ReferenceFrames": 3,
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	        },
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 2250,
	          "MaxBitrate": 2250,
	          "BufferWindow": "00:00:05",
	          "Width": 960,
	          "Height": 540,
	          "BFrames": 3,
	          "ReferenceFrames": 3,
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	        },
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 1500,
	          "MaxBitrate": 1500,
	          "BufferWindow": "00:00:05",
	          "Width": 960,
	          "Height": 540,
	          "BFrames": 3,
	          "ReferenceFrames": 3,
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	        },
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 1000,
	          "MaxBitrate": 1000,
	          "BufferWindow": "00:00:05",
	          "Width": 640,
	          "Height": 360,
	          "BFrames": 3,
	          "ReferenceFrames": 3,
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	        },
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 650,
	          "MaxBitrate": 650,
	          "BufferWindow": "00:00:05",
	          "Width": 640,
	          "Height": 360,
	          "BFrames": 3,
	          "ReferenceFrames": 3,
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	        },
	        {
	          "Profile": "Auto",
	          "Level": "auto",
	          "Bitrate": 400,
	          "MaxBitrate": 400,
	          "BufferWindow": "00:00:05",
	          "Width": 320,
	          "Height": 180,
	          "BFrames": 3,
	          "ReferenceFrames": 3,
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "FrameRate": "0/1"
	        }
	      ],
	      "Type": "H264Video"
	    },
	    {
	      "Profile": "AACLC",
	      "Channels": 2,
	      "SamplingRate": 48000,
	      "Bitrate": 128,
	      "Type": "AACAudio"
	    }
	  ],
	  "Outputs": [
	    {
	      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
	      "Format": {
	        "Type": "MP4Format"
	      }
	    }
	  ]
	} 

###Valor preestablecido XML
	
Para recortar vídeos, puede usar cualquiera de los valores preestablecidos de MES que se documentan [aquí](https://msdn.microsoft.com/library/mt269960.aspx) y modificar el elemento **Sources** (tal y como se muestra a continuación).

	<?xml version="1.0" encoding="utf-16"?>
	<Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
	  <Sources>
	    <Source StartTime="PT4S" Duration="PT14S"/>
	  </Sources>
	  <Encoding>
	    <H264Video>
	      <KeyFrameInterval>00:00:02</KeyFrameInterval>
	      <H264Layers>
	        <H264Layer>
	          <Bitrate>3400</Bitrate>
	          <Width>1280</Width>
	          <Height>720</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Auto</Profile>
	          <Level>auto</Level>
	          <BFrames>3</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cabac</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>3400</MaxBitrate>
	        </H264Layer>
	        <H264Layer>
	          <Bitrate>2250</Bitrate>
	          <Width>960</Width>
	          <Height>540</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Auto</Profile>
	          <Level>auto</Level>
	          <BFrames>3</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cabac</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>2250</MaxBitrate>
	        </H264Layer>
	        <H264Layer>
	          <Bitrate>1500</Bitrate>
	          <Width>960</Width>
	          <Height>540</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Auto</Profile>
	          <Level>auto</Level>
	          <BFrames>3</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cabac</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>1500</MaxBitrate>
	        </H264Layer>
	        <H264Layer>
	          <Bitrate>1000</Bitrate>
	          <Width>640</Width>
	          <Height>360</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Auto</Profile>
	          <Level>auto</Level>
	          <BFrames>3</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cabac</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>1000</MaxBitrate>
	        </H264Layer>
	        <H264Layer>
	          <Bitrate>650</Bitrate>
	          <Width>640</Width>
	          <Height>360</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Auto</Profile>
	          <Level>auto</Level>
	          <BFrames>3</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cabac</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>650</MaxBitrate>
	        </H264Layer>
	        <H264Layer>
	          <Bitrate>400</Bitrate>
	          <Width>320</Width>
	          <Height>180</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Auto</Profile>
	          <Level>auto</Level>
	          <BFrames>3</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cabac</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>400</MaxBitrate>
	        </H264Layer>
	      </H264Layers>
	    </H264Video>
	    <AACAudio>
	      <Profile>AACLC</Profile>
	      <Channels>2</Channels>
	      <SamplingRate>48000</SamplingRate>
	      <Bitrate>128</Bitrate>
	    </AACAudio>
	  </Encoding>
	  <Outputs>
	    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
	      <MP4Format />
	    </Output>
	  </Outputs>
	</Preset>

##<a id="overlay"></a>Creación de una superposición

Media Encoder Estándar permite superponer una imagen en un vídeo existente. Actualmente, se admiten los siguientes formatos: png, jpg, gif y bmp. El valor preestablecido que se definen a continuación es un ejemplo básico de superposición de vídeo.

Además de definir un archivo de valores preestablecidos, también tiene que permitir que Servicios multimedia sepa qué archivo del recurso es la imagen de superposición y qué archivo contiene el vídeo de origen en el que desea superponer la imagen. El archivo de vídeo debe ser el archivo **principal**.

El ejemplo anterior de .NET define dos funciones: **UploadMediaFilesFromFolder** y **EncodeWithOverlay**. La función UploadMediaFilesFromFolder carga archivos desde una carpeta (por ejemplo, BigBuckBunny.mp4 e Image001.png) y establece el archivo mp4 como el archivo principal del recurso. La función **EncodeWithOverlay** usa el archivo preestablecido personalizado que se le trasladó (por ejemplo, el archivo preestablecido siguiente) para crear la tarea de codificación.

>[AZURE.NOTE]Limitaciones actuales:
>
>No se admite el ajuste de opacidad de superposición.
>
>El archivo de vídeo de origen y el archivo de superposición deben estar en el mismo recurso.

###Valor preestablecido JSON
	
	{
	  "Version": 1.0,
	  "Sources": [
	    {
	      "Streams": [],
	      "Filters": {
	        "VideoOverlay": {
	          "Position": {
	            "X": 100,
	            "Y": 100,
	            "Width": 100,
	            "Height": 50
	          },
	          "AudioGainLevel": 0.0,
	          "MediaParams": [
	            {
	              "OverlayLoopCount": 1
	            },
	            {
	              "IsOverlay": true,
	              "OverlayLoopCount": 1,
	              "InputLoop": true
	            }
	          ],
	          "Source": "Image001.png",
	          "Clip": {
	            "Duration": "00:00:05"
	          },
	          "FadeInDuration": {
	            "Duration": "00:00:01"
	          },
	          "FadeOutDuration": {
	            "StartTime": "00:00:03",
	            "Duration": "00:00:04"
	          }
	        }
	      },
	      "Pad": true
	    }
	  ],
	  "Codecs": [
	    {
	      "KeyFrameInterval": "00:00:02",
	      "H264Layers": [
	        {
	          "Profile": "Baseline",
	          "Level": "auto",
	          "Bitrate": 1045,
	          "MaxBitrate": 1045,
	          "BufferWindow": "00:00:05",
	          "ReferenceFrames": 3,
	          "EntropyMode": "Cavlc",
	          "AdaptiveBFrame": true,
	          "Type": "H264Layer",
	          "Width": "400",
	          "Height": "400",
	          "FrameRate": "0/1"
	        }
	      ],
	      "Type": "H264Video"
	    },
	    {
	      "Type": "CopyAudio"
	    }
	  ],
	  "Outputs": [
	    {
	      "FileName": "{Basename}{Extension}",
	      "Format": {
	        "Type": "MP4Format"
	      }
	    }
	  ]
	}

###Valor preestablecido XML
	
	<?xml version="1.0" encoding="utf-16"?>
	<Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
	  <Sources>
	    <Source>
	      <Streams />
	      <Filters>
	        <VideoOverlay>
	          <Source>Image001.png</Source>
	          <Clip Duration="PT5S" />
	          <FadeInDuration Duration="PT1S" />
	          <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
	          <Position X="100" Y="100" Width="100" Height="50" />
	          <Opacity>0</Opacity>
	          <AudioGainLevel>0</AudioGainLevel>
	          <MediaParams>
	            <MediaParam>
	              <IsOverlay>false</IsOverlay>
	              <OverlayLoopCount>1</OverlayLoopCount>
	              <InputLoop>false</InputLoop>
	            </MediaParam>
	            <MediaParam>
	              <IsOverlay>true</IsOverlay>
	              <OverlayLoopCount>1</OverlayLoopCount>
	              <InputLoop>true</InputLoop>
	            </MediaParam>
	          </MediaParams>
	        </VideoOverlay>
	      </Filters>
	      <Pad>true</Pad>
	    </Source>
	  </Sources>
	  <Encoding>
	    <H264Video>
	      <KeyFrameInterval>00:00:02</KeyFrameInterval>
	      <H264Layers>
	        <H264Layer>
	          <Bitrate>1045</Bitrate>
	          <Width>400</Width>
	          <Height>400</Height>
	          <FrameRate>0/1</FrameRate>
	          <Profile>Baseline</Profile>
	          <Level>auto</Level>
	          <BFrames>0</BFrames>
	          <ReferenceFrames>3</ReferenceFrames>
	          <Slices>0</Slices>
	          <AdaptiveBFrame>true</AdaptiveBFrame>
	          <EntropyMode>Cavlc</EntropyMode>
	          <BufferWindow>00:00:05</BufferWindow>
	          <MaxBitrate>1045</MaxBitrate>
	        </H264Layer>
	      </H264Layers>
	    </H264Video>
	    <CopyAudio />
	  </Encoding>
	  <Outputs>
	    <Output FileName="{Basename}{Extension}">
	      <MP4Format />
	    </Output>
	  </Outputs>
	</Preset>

##<a id="silent_audio"></a>Inserción de una pista de audio silenciosa cuando la entrada no tiene audio

De forma predeterminada, si envía una entrada al codificador que solo contenga vídeo, y no audio, el activo de salida contendrá archivos que solo contienen datos de vídeos. Algunos reproductores no puede controlar estos flujos de salida. Puede usar este ajuste para forzar al codificador a agregar una pista de audio silenciosa a la salida en ese escenario.

Para forzar al codificador a producir un activo que contiene una pista de audio silenciosa cuando la entrada no tiene audio, especifique el valor de "InsertSilenceIfNoAudio".

Puede usar cualquiera de los valores preestablecidos de MES que se documentan [aquí](https://msdn.microsoft.com/library/mt269960.aspx) y realizar la siguiente modificación:

###Valor preestablecido JSON

    {
      "Channels": 2,
      "SamplingRate": 44100,
      "Bitrate": 96,
      "Type": "AACAudio",
      "Condition": "InsertSilenceIfNoAudio"
    }

###Valor preestablecido XML

    <AACAudio Condition="InsertSilenceIfNoAudio">
      <Channels>2</Channels>
      <SamplingRate>44100</SamplingRate>
      <Bitrate>96</Bitrate>
    </AACAudio>

##<a id="deinterlacing"></a>Deshabilitación del entrelazado automático

Los clientes no tienen que hacer nada si prefieren que el enlazado del contenido entrelazado se anule automáticamente. Cuando la anulación de entrelazado automática está activada (valor predeterminado), el MES realiza la detección automática de fotogramas entrelazados y solo se anula el entrelazado de los fotogramas marcados como entrelazados.

Puede desactivar la anulación de entrelazado automática. Esta opción nos e recomienda.

###Valor preestablecido JSON
	
	"Sources": [
	{
	 "Filters": {
	    "Deinterlace": {
	      "Mode": "Off"
	    }
	  },
	}
	]

###Valor preestablecido XML
	
	<Sources>
	<Source>
	  <Filters>
	    <Deinterlace>
	      <Mode>Off</Mode>
	    </Deinterlace>
	  </Filters>
	</Source>
	</Sources>




##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Otras referencias 

[Información general sobre la codificación de Servicios multimedia](media-services-encode-asset.md)

<!---HONumber=AcomDC_0224_2016-->