<properties 
	pageTitle="Uso de tablas de búsqueda y datos de referencia en Análisis de transmisiones | Microsoft Azure" 
	description="Uso de datos de referencia en una consulta de Análisis de transmisiones" 
	keywords="tabla de búsqueda, datos de referencia"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm"
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="02/04/2016" 
	ms.author="jeffstok"/>

# Uso de datos de referencia o tablas de consulta en una transmisión de entrada de Análisis de transmisiones

Los datos de referencia (también denominados tabla de consulta) son un conjunto finito de datos estáticos o de cambio lento de naturaleza, que se usan para realizar una búsqueda o para relacionarlos con el flujo de datos. Para usar los datos de referencia en el trabajo de Análisis de transmisiones de Azure, por lo general usará una [combinación de datos de referencia](https://msdn.microsoft.com/library/azure/dn949258.aspx) en la consulta. Análisis de transmisiones usa el almacenamiento de blobs de Azure como la capa de almacenamiento de datos de referencia y con los datos de referencia de Factoría de datos de Azure se puede transformar o copiar al almacenamiento de blobs de Azure, para su uso como datos de referencia, desde [cualquier número de almacenes de datos locales y en la nube](./articles/data-factory-data-movement-activities.md). Los datos de referencia se modelan como una secuencia de blobs (que se define en la configuración de entrada) en orden ascendente por la fecha y hora que se especifique en el nombre del blob. **Solo** se pueden agregar al final de la secuencia con una fecha y hora **posterior** a la especificada en el último blob de la secuencia.

## Configuración de datos de referencia

Para configurar los datos de referencia, tiene que crear primero una entrada que sea del tipo **Datos de referencia**. En la siguiente tabla se explica cada propiedad que necesitará proporcionar al crear la entrada de los datos de referencia con su descripción:

<table>
<tbody>
<tr>
<td>Nombre de propiedad</td>
<td>Descripción</td>
</tr>
<tr>
<td>Alias de entrada</td>
<td>Nombre descriptivo que se usará en la consulta de trabajo para hacer referencia a esta entrada.</td>
</tr>
<tr>
<td>Cuenta de almacenamiento</td>
<td>El nombre de la cuenta de almacenamiento donde se encuentran los archivos de blob. Si está en la misma suscripción que su trabajo de Análisis de transmisiones, puede seleccionarla en el menú desplegable.</td>
</tr>
<tr>
<td>Clave de cuenta de almacenamiento</td>
<td>La clave secreta asociada con la cuenta de almacenamiento. Se rellena automáticamente si la cuenta de almacenamiento está en la misma suscripción que el trabajo de Análisis de transmisiones.</td>
</tr>
<tr>
<td>Contenedor de almacenamiento</td>
<td>Los contenedores proporcionan una agrupación lógica de los blobs almacenados en el servicio BLOB de Microsoft Azure. Cuando carga un blob al servicio BLOB, debe especificar un contenedor para ese blob.</td>
</tr>
<tr>
<td>Patrón de la ruta de acceso</td>
<td>La ruta de acceso de archivo para ubicar los blobs dentro del contenedor especificado. Dentro de la ruta, puede elegir especificar una o más instancias de las siguientes dos variables:<BR>{date}, {time}<BR>Ejemplo 1: products/{date}/{time}/product-list.csv<BR>Ejemplo 2: products/{date}/product-list.csv
</tr>
<tr>
<td>Formato de fecha [opcional]</td>
<td>Si ha usado {date} dentro del modelo de ruta de acceso especificada, puede seleccionar el formato de fecha en el que se organizan los archivos en la lista desplegable de formatos admitidos. Ejemplo: AAAA/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [opcional]</td>
<td>Si ha usado {time} dentro del modelo de ruta de acceso especificada, puede seleccionar el formato de hora en el que se organizan los archivos en la lista desplegable de formatos admitidos. Ejemplo: HH</td>
</tr>
<tr>
<td>Formato de serialización de eventos</td>
<td>Para asegurarse de que las consultas funcionen como se espera, Análisis de transmisiones debe saber cuál es el formato de serialización que usa para los flujos de datos entrantes. Para los datos de referencia, los formatos admitidos son CSV y JSON.</td>
</tr>
<tr>
<td>Codificación</td>
<td>Por el momento, UTF-8 es el único formato de codificación compatible.</td>
</tr>
</tbody>
</table>

## Generación de datos de referencia en una programación

Si los datos de referencia es un conjunto de datos que cambia con poca frecuencia, es posible actualizar los datos de referencia especificando un patrón de ruta de acceso en la configuración de entrada que usa los tokens de {date} y {time}. Análisis de transmisiones seleccionará las definiciones de datos de referencia según este patrón de ruta de acceso. Por ejemplo, un patrón de ````"/sample/{date}/{time}/products.csv"```` con un formato de fecha de "AAAA-MM-DD" y un formato de hora de "HH:mm" indica a Análisis de transmisiones que seleccione el blob actualizado ````"/sample/2015-04-16/17:30/products.csv"```` a las 5:30 p.m. del 16 de abril de 2015, zona horaria UTC.

> [AZURE.NOTE] Actualmente los trabajos de Análisis de transmisiones buscan la actualización de blobs solo cuando la hora del equipo coincide con la hora codificada en el nombre del blob. Por ejemplo el trabajo buscará /sample/2015-04-16/17:30/products.csv entre las 5:30 p.m. y las 5:30:59.9 p.m. el 16 de abril de 2015, zona horaria UTC. Cuando el reloj de la máquina marca las 5:31 p.m., para de buscar /sample/2015-04-16/17:30/products.csv y comienza a buscar /sample/2015-04-16/17:31/products.csv. Una excepción se produce cuando el trabajo debe volver a procesar datos anteriores en el tiempo o cuando se inicia por primera vez. En momento de iniciarse, el trabajo busca el blob más reciente generado antes de la hora de inicio del trabajo especificada. Esto se hace para garantizar que haya un conjunto de datos de referencia no vacío al iniciarse el trabajo. Si no se encuentra ninguno, se producirá un error en el trabajo y se mostrará un aviso de diagnóstico al usuario.

[Factoría de datos de Azure](https://azure.microsoft.com/documentation/services/data-factory/) puede usarse para orquestar la tarea de crear los blobs actualizados requeridos por Análisis de transmisiones para actualizar las definiciones de datos de referencia. Factoría de datos es un servicio de integración de datos basado en la nube que organiza y automatiza el movimiento y la transformación de datos. Factoría de datos admite la [conexión a un gran número de almacenes de datos locales y en la nube](./articles/data-factory-data-movement-activities.md) y el desplazamiento sencillo de los datos con la regularidad que se especifique. Para obtener más información e instrucciones paso a paso sobre cómo configurar una canalización de Factoría de datos para generar datos de referencia para Análisis de transmisiones que se actualiza según una programación predefinida, consulte este [ejemplo de GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

## Sugerencias sobre cómo actualizar los datos de referencia ##

1. Si sobrescribe blobs de datos de referencia, el Análisis de transmisiones no volverá a cargar el blob y, en algunos casos, puede impedir que el trabajo se ejecute. La mejor forma de cambiar datos de referencia consiste en agregar un nuevo blob con el mismo patrón de ruta de acceso y contenedor definidos en la entrada del trabajo, y usar una fecha y hora **posterior** a la especificada en el último blob de la secuencia.
2.	Los blobs se ordenan por la hora de "Última modificación" del blob sino únicamente por la hora y fecha que se especifique en el nombre del blob mediante las sustituciones de {date} y {time}.
3.	En ocasiones un trabajo debe retroceder al pasado, por lo que los blobs de datos de referencia no se deben modificar ni eliminar.

## Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=AzureStreamAnalytics)

## Pasos siguientes
Ya conoce Análisis de transmisiones, un servicio administrado para el análisis del streaming de datos desde Internet de las cosas. Para obtener más información acerca de este servicio, consulte:

- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!---HONumber=AcomDC_0204_2016-->