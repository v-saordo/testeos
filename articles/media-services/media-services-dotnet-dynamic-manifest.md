<properties 
	pageTitle="Creación de filtros con .NET SDK de Servicios multimedia de Azure" 
	description="En este tema se describe cómo crear filtros para que su cliente pueda usarlos para el streaming de secciones específicas de una secuencia. Servicios multimedia crea manifiestos dinámicos para lograr este streaming selectivo." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede,cenkdin" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
  ms.date="02/03/2016"
	ms.author="juliako"/>


#Creación de filtros con .NET SDK de Servicios multimedia de Azure

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-dynamic-manifest.md)
- [REST](media-services-rest-dynamic-manifest.md)

A partir de la versión 2.11, los Servicios multimedia permiten definir filtros para los activos. Estos filtros son reglas del lado servidor que permitirán a los clientes elegir realizar acciones como: reproducir solo una sección de un vídeo (en lugar de reproducir el vídeo completo), o especificar solo un subconjunto de las representaciones de audio y vídeo que el dispositivo de su cliente puede controlar (en lugar de todas las copias asociadas al activo). Este filtrado de sus activos se logra a través de los **manifiestos dinámicos** que se crean tras la solicitud del cliente para transmitir un vídeo en función de los filtros especificados.

Para obtener más información detallada relacionada con filtros y manifiesto dinámico, vea [Información general de manifiesto dinámico](media-services-dynamic-manifest-overview.md).

En este tema se muestra cómo usar el SDK de .NET de Servicios multimedia para crear, actualizar y eliminar filtros.


Tenga en cuenta que si actualiza un filtro, se pueden tardar hasta 2 minutos en que el extremo de streaming actualice las reglas. Si el contenido se proporcionó con este filtro (y se almacenó en caché en los servidores proxy y cachés de CDN), la actualización de este filtro puede generar errores del reproductor. Se recomienda borrar la memoria caché después de actualizar el filtro. Si esta opción no es posible, piense en usar un filtro diferente.

##Tipos usados para crear filtros

Al crear filtros, se usan los siguientes tipos:

- **IStreamingFilter**. Este tipo está basado en la siguiente API de REST [Filter](http://msdn.microsoft.com/library/azure/mt149056.aspx)
- **IStreamingAssetFilter**. Este tipo está basado en la siguiente API de REST [AssetFilter](http://msdn.microsoft.com/library/azure/mt149053.aspx)
- **PresentationTimeRange**. Este tipo está basado en la siguiente API de REST [PresentationTimeRange](http://msdn.microsoft.com/library/azure/mt149052.aspx)
- **FilterTrackSelectStatement** y **IFilterTrackPropertyCondition**. Estos tipos se basan en las siguientes API de REST [FilterTrackSelect y FilterTrackPropertyCondition](http://msdn.microsoft.com/library/azure/mt149055.aspx)


##Creación/actualización/lectura/eliminación de filtros globales

El código siguiente muestra cómo usar .NET para crear, actualizar, leer y eliminar filtros de recursos.
	
	string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();
	            
	List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();
	
	FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
	filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
	filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
	filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
	filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
	filterTrackSelectStatements.Add(filterTrackSelectStatement);
	
	// Create
	IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);
	
	// Update
	filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
	filter.Update();
	
	// Read
	var filterUpdated = _context.Filters.FirstOrDefault();
	Console.WriteLine(filterUpdated.Name);

	// Delete
	filter.Delete();


##Creación/actualización/lectura/eliminación de filtros de recursos

El código siguiente muestra cómo usar .NET para crear, actualizar, leer y eliminar filtros de recursos.

	
	string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
	var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);
	
	string filterName = "AssetFilter_" + Guid.NewGuid().ToString();
	
	    
	// Create
	IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
	                                    new PresentationTimeRange(), 
	                                    new List<FilterTrackSelectStatement>());
	
	// Update
	filter.PresentationTimeRange = 
	        new PresentationTimeRange(start: 6000000000, end: 72000000000);
	
	filter.Update();
	
	// Read
	asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
	var filterUpdated = asset.AssetFilters.FirstOrDefault();
	Console.WriteLine(filterUpdated.Name);
	
	// Delete
	filterUpdated.Delete();
	



##Generar direcciones URL de streaming que usan filtros

Para obtener información sobre cómo publicar y entregar los activos, vea [Información general de entrega de contenido a clientes](media-services-deliver-content-overview.md).


En los ejemplos siguientes se muestra cómo agregar filtros a sus URL de streaming.

**MPEG DASH**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


**HDS**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f, filter=MyFilter)


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Otras referencias 

[Información general de manifiestos dinámicos](media-services-dynamic-manifest-overview.md)
 

<!---HONumber=AcomDC_0211_2016-->