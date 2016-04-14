<properties 
	pageTitle="SDK para Python de DocumentDB | Microsoft Azure" 
	description="Obtenga toda la información sobre el SDK de Python como, por ejemplo, fechas de lanzamiento, fechas de retirada y cambios de una versión a otra del SDK para Python de DocumentDB." 
	services="documentdb" 
	documentationCenter="python" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="01/19/2016" 
	ms.author="ryancraw"/>

# SDK de DocumentDB

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##SDK para Python de DocumentDB

<table>
<tr><td>**Descargar**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>
<tr><td>**Contribuciones**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>
<tr><td>**Documentación**</td><td>[Documentación de referencia del SDK para Python](http://azure.github.io/azure-documentdb-python/)</td></tr>
<tr><td>**Introducción**</td><td>[Introducción al SDK para Python](documentdb-python-application.md)</td></tr>
<tr><td>**Plataforma admitida actualmente**</td><td>[Python 2.7](https://www.python.org/download/releases/2.7/)</td></tr>
</table></br>

## Notas de la versión

### <a name="1.5.0"/>[1\.5.0](https://pypi.python.org/pypi/pydocumentdb/1.5.0)
- Se han agregado solucionadores de particiones de hash e intervalo para ayudar con el particionamiento de las aplicaciones entre varias particiones.

### <a name="1.4.2"/>[1\.4.2](https://pypi.python.org/pypi/pydocumentdb/1.4.2)
- Implementación de Upsert. Se han agregado nuevos métodos upsertXXX para admitir la característica Upsert.
- Se implementa el enrutamiento por identificador. Sin cambios en la API pública, todos los cambios son internos.

### <a name="1.2.0"/>[1\.2.0](https://pypi.python.org/pypi/pydocumentdb/1.2.0)
- Compatible con índice geoespacial.
- Valida la propiedad id para todos los recursos. Los identificadores de recursos no pueden contener los caracteres ?, /, #, \\, ni terminar con un espacio.
- Agrega el nuevo encabezado "progreso de transformación de índices" a ResourceResponse.

### <a name="1.1.0"/>[1\.1.0](https://pypi.python.org/pypi/pydocumentdb/1.1.0)
- Implementación de la directiva de indexación V2

### <a name="1.0.1"/>[1\.0.1](https://pypi.python.org/pypi/pydocumentdb/1.0.1)
- Compatibilidad con conexión de proxy

### <a name="1.0.0"/>[1\.0.0](https://pypi.python.org/pypi/pydocumentdb/1.0.0)
- SDK de GA

## Fechas de lanzamiento y retirada
Microsoft notificará la retirada de un SDK con al menos **12 meses** de antelación para facilitar la transición a una versión compatible o más reciente.

Solo se agregan nuevas características, funcionalidad y optimizaciones al SDK actual, por lo que se recomienda actualizar siempre a la última versión del SDK tan pronto como sea posible.

El servicio rechazará cualquier solicitud realizada en DocumentDB mediante un SDK retirado.

> [AZURE.WARNING]Todas las versiones del SDK de Azure DocumentDB para Python anteriores a la versión **1.0.0** se retirarán el **29 de febrero de 2016**.

<br/>

| Versión | Fecha de lanzamiento | Fecha de retirada 
| ---	  | ---	         | ---
| [1\.5.0](#1.5.0) | 03 de enero de 2016 |--- 
| [1\.4.2](#1.4.2) | 06 de octubre de 2015 |--- 
| [1\.4.1](#1.4.1) | 06 de octubre de 2015 |--- 
| [1\.2.0](#1.2.0) | 06 de agosto de 2015 |--- 
| [1\.1.0](#1.1.0) | 09 de julio de 2015 |--- 
| [1\.0.1](#1.0.1) | 25 de mayo de 2015 |--- 
| [1\.0.0](#1.0.0) | 07 de abril de 2015 |--- 
| versión preliminar 0.9.4 | 14 de enero de 2015 | 29 de febrero de 2016 
| versión preliminar 0.9.3 | 09 de diciembre de 2014 | 29 de febrero de 2016 
| versión preliminar 0.9.2 | 25 de noviembre de 2014 | 29 de febrero de 2016 
| versión preliminar 0.9.1 | 23 de septiembre de 2014 | 29 de febrero de 2016 
| versión preliminar 0.9.0 | 21 de agosto de 2014 | 29 de febrero de 2016

## P+F
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Consulte también

Para más información sobre DocumentDB, vea la página del servicio [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0121_2016-->