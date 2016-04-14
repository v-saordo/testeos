<properties 
	pageTitle="SDK para Java de DocumentDB | Microsoft Azure" 
	description="Obtenga toda la información sobre el SDK para Java como, por ejemplo, fechas de lanzamiento, fechas de retirada y cambios de una versión a otra del SDK para Java de DocumentDB." 
	services="documentdb" 
	documentationCenter="java" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="01/19/2016" 
	ms.author="ryancraw"/>

# SDK de DocumentDB

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##SDK para Java de DocumentDB

<table>
<tr><td>**Descargar**</td><td>[Maven](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb)</td></tr>
<tr><td>**Contribuciones**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>
<tr><td>**Documentación**</td><td>[Documentación de referencia del SDK para Java](http://azure.github.io/azure-documentdb-java/)</td></tr>
<tr><td>**Introducción**</td><td>[Introducción al SDK para Java](documentdb-java-application.md)</td></tr>
<tr><td>**Runtime admitido actualmente**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## Notas de la versión

### <a name="1.5.1"/>[1\.5.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.1)
- Se ha corregido un error en HashPartitionResolver para generar valores hash en little endian que sean consistentes con otros SDK.

### <a name="1.5.0"/>[1\.5.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.0)
- Se han agregado solucionadores de particiones de hash e intervalo para ayudar con el particionamiento de las aplicaciones entre varias particiones.

### <a name="1.4.0"/>[1\.4.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.4.0)
- Implementación de Upsert. Se han agregado nuevos métodos upsertXXX para admitir la característica Upsert.
- Se implementa el enrutamiento por identificador. Sin cambios en la API pública, todos los cambios son internos.

### <a name="1.3.0"/>1.3.0
- Versión omitida para alinear el número de versión con otros SDK

### <a name="1.2.0"/>[1\.2.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.2.0)
- Compatible con índice geoespacial.
- Valida la propiedad id para todos los recursos. Los identificadores de recursos no pueden contener los caracteres ?, /, #, \\, ni terminar con un espacio.
- Agrega el nuevo encabezado "progreso de transformación de índices" a ResourceResponse.

### <a name="1.1.0"/>[1\.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.1.0)
- Implementación de la directiva de indexación V2

### <a name="1.0.0"/>[1\.0.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.0.0)
- SDK de GA

## Fechas de lanzamiento y de retirada
Microsoft notificará la retirada de un SDK con al menos **12 meses** de antelación para facilitar la transición a una versión compatible o más reciente.

Solo se agregan nuevas características, funcionalidad y optimizaciones al SDK actual, por lo que se recomienda actualizar siempre a la última versión del SDK tan pronto como sea posible.

El servicio rechazará cualquier solicitud realizada en DocumentDB mediante un SDK retirado.

> [AZURE.WARNING]Todas las versiones del SDK de Azure DocumentDB para Java anteriores a la versión **1.0.0** se retirarán el **29 de febrero de 2016**.

<br/>

| Versión | Fecha de lanzamiento | Fecha de retirada 
| ---	  | ---	         | ---
| [1\.5.1](#1.5.1) | 31 de diciembre de 2015 |--- 
| [1\.5.0](#1.5.0) | 04 de diciembre de 2015 |--- 
| [1\.4.0](#1.4.0) | 05 de octubre de 2015 |--- 
| [1\.3.0](#1.3.0) | 05 de octubre de 2015 |--- 
| [1\.2.0](#1.2.0) | 05 de agosto de 2015 |--- 
| [1\.1.0](#1.1.0) | 09 de julio de 2015 |--- 
| [1\.0.1](#1.0.1) | 12 de mayo de 2015 |--- 
| [1\.0.0](#1.0.0) | 07 de abril de 2015 |--- 
| versión preliminar 0.9.5 | 09 de marzo de 2015 | 29 de febrero de 2016 
| versión preliminar 0.9.4 | 17 de febrero de 2015 | 29 de febrero de 2016 
| versión preliminar 0.9.3 | 13 de enero de 2015 | 29 de febrero de 2016 
| versión preliminar 0.9.2 | 19 de diciembre de 2014 | 29 de febrero de 2016 
| versión preliminar 0.9.1 | 19 de diciembre de 2014 | 29 de febrero de 2016 
| versión preliminar 0.9.0 | 10 de diciembre de 2014 | 29 de febrero de 2016

## P+F
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Otras referencias

Para más información sobre DocumentDB, consulte la página del servicio [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0121_2016-->