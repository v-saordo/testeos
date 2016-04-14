<properties 
	pageTitle="SDK para Node.js de DocumentDB | Microsoft Azure" 
	description="Obtenga toda la información sobre el SDK para Node.js como, por ejemplo, fechas de lanzamiento, fechas de retirada y cambios de una versión a otra del SDK para Node.js de DocumentDB." 
	services="documentdb" 
	documentationCenter="nodejs" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="02/02/2016" 
	ms.author="ryancraw"/>

# SDK de DocumentDB

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##SDK para Node.js de DocumentDB

<table>
<tr><td>**Descargar**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>
<tr><td>**Contribuciones**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>
<tr><td>**Documentación**</td><td>[Documentación de referencia del SDK para Node.js](http://azure.github.io/azure-documentdb-node/)</td></tr>
<tr><td>**Ejemplos**</td><td>[Ejemplos de código de Node.js](https://github.com/Azure/azure-documentdb-node/tree/master/samples)</td></tr>
<tr><td>**Introducción**</td><td>[Introducción al SDK para Node.js](documentdb-nodejs-get-started.md)</td></tr>
<tr><td>**Plataforma admitida actualmente**</td><td>[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)<br/>[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr>
</table></br>

##Notas de la versión
###<a name="1.5.5"/>1.5.5</a>

- Valor hashParitionResolver resolveForRead() fijo: cuando ninguna clave de partición proporcionada lanzó una excepción, en lugar de devolver una lista de todos los vínculos registrados.

###<a name="1.5.4"/>1.5.4</a>

- Corrige el problema [100 #](https://github.com/Azure/azure-documentdb-node/issues/100): agente dedicado de HTTPS: evitar la modificación del agente global para fines de DocumentDB. Use un agente dedicado para todas las solicitudes de lib.

###<a name="1.5.3"/>1.5.3</a>

- Corrige el problema [#81](https://github.com/Azure/azure-documentdb-node/issues/81): controlar correctamente guiones en identificadores de medios.

###<a name="1.5.2"/>1.5.2</a>

- Corrige el problema [#95](https://github.com/Azure/azure-documentdb-node/issues/95): advertencia de pérdida de escucha de EventEmitter

###<a name="1.5.1"/>1.5.1</a>

- Corrige el problema [#92](https://github.com/Azure/azure-documentdb-node/issues/90): cambio de nombre de la carpeta Hash a hash para sistemas que distinguen mayúsculas de minúsculas

### <a name="1.5.0"/>1.5.0</a>

- Se implementa la compatibilidad con el particionamiento, para lo que se agregan resolvedores de hash y de particiones de intervalo

### <a name="1.4.0"/>1.4.0</a>

- Implementación de Upsert. Se han agregado nuevos métodos upsertXXX en documentClient. 

### <a name="1.3.0"/>1.3.0</a>

- Omitida para alinear el número de versión con otros SDK

### <a name="1.2.2"/>1.2.2</a>

- Divide el contenedor de objetos Q promise en nuevo repositorio.
- Actualización del archivo de paquete para registro npm

### <a name="1.2.1"/>1.2.1</a>

- Se implementa el enrutamiento por identificador.
- Corrige el problema [#49](https://github.com/Azure/azure-documentdb-node/issues/49): la propiedad current entra en conflicto en conflicto con el método current()

### <a name="1.2.0"/>1.2.0</a>

- Se agregó compatibilidad con índice geoespacial.
- Valida la propiedad id para todos los recursos. Los identificadores de recursos no pueden contener los caracteres ?, /, #, &#47;&#47;, ni terminar con un espacio. 
- Agrega el nuevo encabezado "progreso de transformación de índices" a ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>

- Implementación de la directiva de indexación V2

### <a name="1.0.3"/>1.0.3</a>

- Problema [#40](https://github.com/Azure/azure-documentdb-node/issues/40): se implementaron configuraciones eslint y grunt en el núcleo y el SDK de promise

### <a name="1.0.2"/>1.0.2</a>

- Problema [#45](https://github.com/Azure/azure-documentdb-node/issues/45): el contenedor de objetos promise no incluye el encabezado con el error.

### <a name="1.0.1"/>1.0.1</a>

- Implementación de capacidad para consultar conflictos agregando readConflicts, readConflictAsync y queryConflicts
- Documentación de la API actualizada
- Problema [#41](https://github.com/Azure/azure-documentdb-node/issues/41): error client.createDocumentAsync  

### <a name="1.0.0"/>1.0.0</a>

- SDK de GA

## Fechas de lanzamiento y de retirada
Microsoft notificará la retirada de un SDK con al menos **12 meses** de antelación para facilitar la transición a una versión compatible o más reciente.

Solo se agregan nuevas características, funcionalidad y optimizaciones al SDK actual, por lo que se recomienda actualizar siempre a la última versión del SDK tan pronto como sea posible.

El servicio rechazará cualquier solicitud realizada en DocumentDB mediante un SDK retirado.

> [AZURE.WARNING]
Todas las versiones del SDK de Azure DocumentDB para Node.js anteriores a la versión **1.0.0** se retirarán el **29 de febrero de 2016**.

<br/>

| Versión | Fecha de lanzamiento | Fecha de retirada 
| ---	  | ---	         | ---
| [1\.5.5](#1.5.5) | 2 de febrero de 2016 |--- 
| [1\.5.4](#1.5.4) | 1 de febrero de 2016 |--- 
| [1\.5.2](#1.5.2) | 26 de enero de 2016 |--- 
| [1\.5.2](#1.5.2) | 22 de enero de 2016 |--- 
| [1\.5.1](#1.5.1) | 4 de enero de 2016 |--- 
| [1\.5.0](#1.5.0) | 31 de diciembre de 2015 |--- 
| [1\.4.0](#1.4.0) | 6 de octubre de 2015 |--- 
| [1\.3.0](#1.3.0) | 6 de octubre de 2015 |--- 
| [1\.2.2](#1.2.2) | 10 de septiembre de 2015 |--- 
| [1\.2.1](#1.2.1) | 15 de agosto de 2015 |--- 
| [1\.2.0](#1.2.0) | 5 de agosto 2015 |--- 
| [1\.1.0](#1.1.0) | 9 de julio de 2015 |--- 
| [1\.0.3](#1.0.3) | 4 de junio de 2015 |--- 
| [1\.0.2](#1.0.2) | 23 de mayo de 2015 |--- 
| [1\.0.1](#1.0.1) | 15 de mayo de 2015 |--- 
| [1\.0.0](#1.0.0) | 8 de abril de 2015 |--- 
| 0.9.4-versión preliminar | 6 de abril de 2015 | 29 de febrero de 2016 
| 0.9.3-versión preliminar | 14 de enero de 2015 | 29 de febrero de 2016 
| 0.9.2-versión preliminar | 18 de diciembre de 2014 | 29 de febrero de 2016
| 0.9.1-versión preliminar | 22 de agosto de 2014 | 29 de febrero de 2016 
| 0.9.0-versión preliminar | 21 de agosto de 2014 | 29 de febrero de 2016


## P+F
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Consulte también

Para más información sobre DocumentDB, vea la página del servicio [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0204_2016-->