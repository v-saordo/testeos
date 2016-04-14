<properties 
	pageTitle="SDK para .NET de DocumentDB | Microsoft Azure" 
	description="Obtenga toda la información sobre el SDK para .NET como, por ejemplo, fechas de lanzamiento, fechas de retirada y cambios de una versión a otra del SDK para .NET de DocumentDB." 
	services="documentdb" 
	documentationCenter=".net" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/19/2016" 
	ms.author="ryancraw"/>

# SDK de DocumentDB

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##SDK para .NET de DocumentDB

<table> <tr><td>**Descarga**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr> <tr><td>**Documentación **</td><td>[documentación de referencia del SDK de .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr> <tr><td>**Ejemplos**</td><td>[ejemplos de código .NET](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)</td></tr> <tr><td>**Introducción**</td><td>[Introducción al SDK de .NET de DocumentDB](documentdb-get-started.md)</td></tr> <tr><td>**Versión de Framework compatible actualmente**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr> </table></br>

## Notas de la versión

### <a name="1.5.3"/>[1\.5.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.3)
  - **[Corregido]** La consulta del punto de conexión de DocumentDB genera: 'System.Net.Http.HttpRequestException: Error al copiar el contenido en una transmisión.

### <a name="1.5.2"/>[1\.5.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.2)
  - Compatibilidad de LINQ ampliada, incluidos nuevos operadores de paginación, expresiones condicionales y comparación de intervalos.
    - Operador Take para habilitar el comportamiento de SELECT TOP en LINQ
    - Operador CompareTo para habilitar las comparaciones de intervalos de cadenas
    - Operadores conditional (?) y coalesce (??)
  - **[Corregido]** ArgumentOutOfRangeException al combinar la proyección Model en la consulta Where-In de LINQ [n.º 81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="1.5.1"/>[1\.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
 - **[Corregido]** Si Select no es la última expresión, el proveedor LINQ suponía que no había ninguna proyección y generaba SELECT * incorrectamente. [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="1.5.0"/>[1\.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
 - Upsert implementada, métodos de UpsertXXXAsync agregados
 - Mejoras de rendimiento para todas las solicitudes
 - Compatibilidad del proveedor de LINQ con los métodos condicionales, de fusión y CompareTo para cadenas
 - **[Corregido]** proveedor LINQ--> la implementación del método Contains en List para generar el mismo SQL que en IEnumerable y en una matriz
 - **[Corregido]** BackoffRetryUtility usa el mismo HttpRequestMessage de nuevo en lugar de crear uno nuevo al reintentar
 - **[Obsoleto]** UriFactory.CreateCollection--> debería usar ahora UriFactory.CreateDocumentCollection
 
### <a name="1.4.1"/>[1\.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
 - **[Corregido]** problemas de localización al usar la información de referencia cultural diferente de EN como nl-NL etc. 
 
### <a name="1.4.0"/>[1\.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
  - Enrutamiento por identificador.
    - Nueva aplicación auxiliar UriFactory para ayudar a construir vínculos de recursos basados en identificador
    - Nuevas sobrecargas en DocumentClient para tener en cuenta URI
  - Se agregaron IsValid() y IsValidDetailed() en LINQ para geoespaciales
  - Compatibilidad mejorada con el proveedor de LINQ
    - **Math**: Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan, Truncate
    - **String**: Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString, ToUpper
    - **Array**: Concat, Contains, Count
    - Operador **IN**

### <a name="1.3.0"/>[1\.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
  - Se agregó compatibilidad para la modificación de las directivas de indexación
    - Nuevo método de ReplaceDocumentCollectionAsync en DocumentClient
    - Nueva propiedad IndexTransformationProgress en ResourceResponse<T> para el seguimiento del porcentaje de progreso de cambios de la directiva de índice
    - DocumentCollection.IndexingPolicy ahora es mutable
  - Se agregó compatibilidad para consulta e indexación espacial
    - Nuevo espacio de nombres Microsoft.Azure.Documents.Spatial para serializar y deserializar tipos espaciales como punto y polígono
    - Nueva clase de SpatialIndex para la indexación de datos GeoJSON almacenados en DocumentDB
  - **[Corregido]**: consulta SQL incorrecta generada a partir de la expresión linq [n.º 38](https://github.com/Azure/azure-documentdb-net/issues/38)

### <a name="1.2.0"/>[1\.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.2.0)
- Dependencia de Newtonsoft.Json v5.0.7 
- Cambios para admitir Order By
  - Compatibilidad del proveedor LINQ para OrderBy() u OrderByDescending()
  - IndexingPolicy para admitir Order By 
  
		**NB: Posible cambio brusco** 
  
    	Si tiene un código existente que aprovisiona colecciones con una directiva de indexación personalizada, habrá que actualizar el código existente para admitir la nueva clase IndexingPolicy. Si no tiene ninguna directiva de indexación personalizada, este cambio no le afectará.

### <a name="1.1.0"/>[1\.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.1.0)
- Compatibilidad para las particiones de datos mediante las nuevas clases HashPartitionResolver y RangePartitionResolver y el IPartitionResolver
- Serialización de DataContract
- Compatibilidad con el GUID de proveedor de LINQ
- Compatibilidad con UDF en LINQ

### <a name="1.0.0"/>[1\.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.0.0)
- SDK de GA

> [AZURE.NOTE]
Se produjo un cambio del nombre de paquete de NuGet entre la vista previa y la disponibilidad general. Pasamos de **Microsoft.Azure.Documents.Client** a **Microsoft.Azure.DocumentDB** <br/>


### <a name="0.9.x-preview"/>[0\.9.x-preview](https://www.nuget.org/packages/Microsoft.Azure.Documents.Client)
- SDK de vista previa [obsoleto]

## Fechas de lanzamiento y de retirada
Microsoft notificará la retirada de un SDK con al menos **12 meses** de antelación para facilitar la transición a una versión compatible o más reciente.

Solo se agregan nuevas características, funcionalidad y optimizaciones al SDK actual, por lo que se recomienda actualizar siempre a la última versión del SDK tan pronto como sea posible.

El servicio rechazará cualquier solicitud realizada en DocumentDB mediante un SDK retirado.

> [AZURE.WARNING]
Todas las versiones del SDK de Azure DocumentDB para .NET anteriores a la versión **1.0.0** se retirarán el **29 de febrero de 2016**.
 
<br/>
 
| Versión | Fecha de lanzamiento | Fecha de retirada 
| ---	  | ---	         | ---
| [1\.5.3](#1.5.3) | 19 de febrero de 2016 |--- 
| [1\.5.2](#1.5.2) | 14 de diciembre de 2015 |--- 
| [1\.5.1](#1.5.1) | 23 de noviembre de 2015 |--- 
| [1\.5.0](#1.5.0) | 5 de octubre de 2015 |--- 
| [1\.4.1](#1.4.1) | 25 de agosto de 2015 |--- 
| [1\.4.0](#1.4.0) | 13 de agosto de 2015 |--- 
| [1\.3.0](#1.3.0) | 5 de agosto de 2015 |--- 
| [1\.2.0](#1.2.0) | 6 de julio de 2015 |--- 
| [1\.1.0](#1.1.0) | 30 de abril de 2015 |--- 
| [1\.0.0](#1.0.0) | 8 de abril de 2015 |--- 
| [versión preliminar 0.9.3](#0.9.x-preview) | 12 de marzo de 2015 | 29 de febrero de 2016 
| [versión preliminar 0.9.2](#0.9.x-preview) | enero de 2015 | 29 de febrero de 2016 
| [versión preliminar 0.9.1](#0.9.x-preview) | 13 de octubre de 2014 | 29 de febrero de 2016 
| [versión preliminar 0.9.0](#0.9.x-preview) | 21 de agosto de 2014 | 29 de febrero de 2016

## P+F
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Otras referencias

Para más información sobre DocumentDB, vea la página del servicio [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0224_2016-->
