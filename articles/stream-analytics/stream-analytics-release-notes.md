<properties 
	pageTitle="Notas de la versión de Análisis de transmisiones | Microsoft Azure" 
	description="Notas de la versión de Análisis de transmisiones GA" 
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
	ms.date="02/16/2016" 
	ms.author="jeffstok"/>

#Notas de la versión de Análisis de transmisiones

## Notas de la versión de Análisis de transmisiones del 12/10/2015 ##

Esta versión contiene la siguiente actualización.

Título | Descripción
---|---
Actualización de la versión de API de REST | La versión de la API de REST se actualizó a la 2015-10-01. Encontrará detalles sobre MSDN en [Referencia de la API de REST de la administración de Análisis de transmisiones](https://msdn.microsoft.com/library/azure/dn835031.aspx) e [Integración del aprendizaje automático en Análisis de transmisiones](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md).
Integración de Aprendizaje automático de Azure | Esta versión incluye compatibilidad con funciones definidas por el usuario de Aprendizaje automático de Azure. Encontrará un tutorial [aquí](stream-analytics-machine-learning-integration-tutorial.md) así como el anuncio de blog general [aquí](http://blogs.technet.com/b/machinelearning/archive/2015/12/10/apply-azure-ml-as-a-function-in-azure-stream-analytics.aspx).

## Notas de la versión de Análisis de transmisiones del 12/11/2015 ##

Esta versión contiene la siguiente actualización.

Título | Descripción
---|---
Nuevo comportamiento de SELECT | SELECT en el Análisis de transmisiones se ha ampliado para permitir * como descriptor de acceso de propiedad de un registro anidado. Para obtener más información, consulte [http://msdn.microsoft.com/library/mt622759.aspx](http://msdn.microsoft.com/library/mt622759.aspx "Tipos de datos complejos").

## Notas de la versión de Análisis de transmisiones del 22/10/2015 ##

Esta versión contiene las siguientes actualizaciones.

Título | Descripción
---|---
Características del lenguaje de consulta adicionales | Análisis de transmisiones ha ampliado el lenguaje de consulta mediante la inclusión de las siguientes características: [ABS](https://msdn.microsoft.com/library/azure/mt574054.aspx), [CEILING](https://msdn.microsoft.com/library/azure/mt605286.aspx), [EXP](https://msdn.microsoft.com/library/azure/mt605289.aspx), [FLOOR](https://msdn.microsoft.com/library/azure/mt605240.aspx), [POWER](https://msdn.microsoft.com/library/azure/mt605287.aspx), [SIGN](https://msdn.microsoft.com/library/azure/mt605290.aspx), [SQUARE](https://msdn.microsoft.com/library/azure/mt605288.aspx) y [SQRT](https://msdn.microsoft.com/library/azure/mt605238.aspx).
Se han quitado las limitaciones de agregados | En esta versión se quita la limitación de 15 agregados en una consulta. Actualmente no hay límites en el número de agregados por consulta.
Se ha agregado la característica GROUP BY System.Timestamp | La función [GROUP BY](https://msdn.microsoft.com/library/azure/dn835023.aspx) ahora permite window\_type o [System.Timestamp](https://msdn.microsoft.com/library/azure/mt598501.aspx).
Se ha agregado OFFSET para las ventanas de saltos de tamaño constante y las ventanas de salto | De forma predeterminada, las ventanas de [saltos de tamaño constante](https://msdn.microsoft.com/library/azure/dn835055.aspx) y de [salto](https://msdn.microsoft.com/library/azure/dn835041.aspx) se alinean con la hora cero (1/1/0001 12:00:00 A.M. UTC). El nuevo parámetro (opcional) 'offsetsize' permite especificar una diferencia (o alineación) personalizada.


## Notas de la versión de Análisis de transmisiones del 29/09/2015 ##

Esta versión contiene las siguientes actualizaciones.

Título | Descripción
---|---
Vista previa pública de Conjunto de aplicaciones de IoT de Azure | Análisis de transmisiones se incluye en la vista previa pública de Conjunto de aplicaciones de IoT de Azure.
Integración del Portal de vista previa de Azure | Además de presencia continua en el Portal de administración de Azure, Análisis de transmisiones ahora se integra en el [Portal de vista previa de Azure](https://azure.microsoft.com/overview/preview-portal/). Tenga en cuenta que la funcionalidad del Análisis de transmisiones en el Portal de vista previa actualmente es un subconjunto de la funcionalidad ofrecida en el Portal de administración de Azure, sin soporte para pruebas de consultas en el explorador, configuración de salida de Power BI y exploración o creación de nuevos recursos de entrada y salida en suscripciones a las que tiene acceso.
Soporte técnico para la salida de DocumentDB | Ahora se pueden enviar trabajos de Análisis de transmisiones a [DocumentDB](https://azure.microsoft.com/services/documentdb/).
Compatibilidad para entrada de Centro de IoT | Los trabajos de Análisis de transmisiones ahora pueden introducir datos de los Centros de IoT.
TIMESTAMP BY para eventos heterogéneos | Ahora puede usar [TIMESTAMP BY](http://msdn.microsoft.com/library/mt573293.aspx) con expresiones para especificar diferentes campos de marca de tiempo para cada caso cuando un único flujo de datos contiene varios tipos de eventos con marcas de tiempo en distintos campos.

## Notas de la versión de Análisis de transmisiones del 10/09/2015 ##

Esta versión contiene las siguientes actualizaciones.

Título|Descripción
---|---
Soporte para grupos de PowerBI|Para habilitar el uso compartido de datos con otros usuarios de Power BI, los trabajos de Análisis de transmisiones ahora pueden escribir en [grupos de PowerBI](stream-analytics-define-outputs.md#power-bi) dentro de su cuenta de Power BI.

## Notas de la versión de Análisis de transmisiones del 20/08/2015 ##

Esta versión contiene las siguientes actualizaciones.

Título|Descripción
---|---
Función LAST agregada |La función [LAST](http://msdn.microsoft.com/library/mt421186.aspx) ahora está disponible en los trabajos de Análisis de transmisiones, lo que le permite recuperar el evento más reciente en una secuencia de eventos en un período de tiempo determinado.
Nuevas funciones de matriz|Ahora están disponibles las funciones de matriz [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx), [GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx) y [GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx).
Nuevas funciones de registro|Ahora están disponibles las funciones de registro [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx) y [GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx).

## Notas de la versión de Análisis de transmisiones del 30/07/2015 ##

Esta versión contiene las siguientes actualizaciones.

Título|Descripción
---|---
Identificador de organización de BI energía desacoplado del identificador de Azure|Esta característica habilita la [salida de Power BI](stream-analytics-power-bi-dashboard.md) para trabajos de ASA en cualquier tipo de cuenta de Azure (Live ID o id. de organización). Además, puede tener un identificador de organización para su cuenta de Azure y usar otros distinto para autorizar la salida de Power BI.
Compatibilidad con la salida de Colas del Bus de servicio|Las salidas de [Colas del Bus de servicio](stream-analytics-connect-data-event-outputs.md#service-bus-queues) ahora están disponibles en los trabajos de Análisis de transmisiones.
Compatibilidad con la salida de Temas del Bus de servicio|Las salidas de [Temas del Bus de servicio](stream-analytics-connect-data-event-outputs.md#service-bus-topics) ahora están disponibles en los trabajos de Análisis de transmisiones.

## Notas de la versión de Análisis de transmisiones del 09/07/2015 ##

Esta versión contiene las siguientes actualizaciones.


Título|Descripción
---|---
Particiones de salida de blob personalizadas|Las salidas de almacenamiento de blobs ahora le dan la opción de especificar tanto la frecuencia en la que los blobs de salida son escritos, como la estructura y el formato de la estructura de la carpeta con la ruta de acceso a los datos de salida. 

## Notas de la versión de Análisis de transmisiones del 03/05/2015 ##

Esta versión contiene las siguientes actualizaciones.


Título|Descripción
---|---
Se ha incrementado el valor máximo del período de tolerancia de fuera de servicio|El tamaño máximo del período de tolerancia de fuera de servicio es ahora de 59:59 (MM:SS)
Formato de salida JSON: separación por líneas o matriz|Cuando envíe elementos al Almacenamiento de blobs o al Centro de eventos, tiene la opción de hacer la salida como una matriz de objetos JSON o separando los objetos JSON con una nueva línea. 

## Notas de la versión de Análisis de transmisiones del 16/04/2015 ##


Título|Descripción
---|---
Retraso en la configuración de la cuenta de almacenamiento de Azure|Al crear un trabajo de Análisis de transmisiones en una región por primera vez, se le pedirá que cree una nueva cuenta de almacenamiento o que especifique una cuenta existente para la supervisión de trabajos de Análisis de transmisiones en esa región. Debido a la latencia en la configuración de la supervisión, al crear otro trabajo de Análisis de transmisiones en la misma región al cabo de 30 minutos se le solicitará que especifique una segunda cuenta de almacenamiento en lugar de mostrar la configurada recientemente en la lista desplegable de supervisión de cuentas de almacenamiento. Para evitar la creación de una cuenta de almacenamiento innecesaria, espere 30 minutos después de crear un trabajo en una región por primera vez antes de aprovisionar trabajos adicionales en dicha región.
Actualización del trabajo|En este momento, Análisis de transmisiones no admite modificaciones dinámicas en la definición o la configuración de un trabajo en ejecución. Para cambiar la entrada, salida, la consulta, la escala o la configuración de un trabajo en ejecución, primero debe detener el trabajo.
Tipos de datos deducidos del origen de entrada|Si no se utiliza una instrucción CREATE TABLE, el tipo de entrada se deriva del formato de entrada; por ejemplo, todos los campos de CSV son cadenas. Los campos deben convertirse explícitamente al tipo adecuado mediante la función CAST para evitar errores de coincidencia de tipos.
Los campos que faltan generan valores null|Hacer referencia a un campo que no está presente en el origen de entrada dará como resultado valores null en el evento de salida.
Las instrucciones WITH deben preceder a las instrucciones SELECT|En una consulta, las instrucciones SELECT deben seguir a subconsultas definidas en instrucciones WITH.
Problema de memoria agotada|Los trabajos de Análisis de transmisiones con una gran tolerancia para eventos sin orden o consultas complejas que mantienen una gran cantidad de estados pueden hacer que el trabajo se quede sin memoria, lo que da lugar a que este se reinicie. Las operaciones de inicio y detención serán visibles en los registros de operaciones del trabajo. Para evitar este comportamiento, escale horizontalmente el resultado de la consulta entre varias particiones. En una versión futura se abordará esta limitación de forma que provoque la degradación del rendimiento de los trabajos y no que se reinicien.
Las entradas de blobs grandes sin marca de tiempo de carga pueden causar un problema de falta de memoria|El consumo de grandes archivos desde el almacenamiento de blobs puede hacer que los trabajos de Análisis de transmisiones se bloquee si no se especifica un campo de marca de tiempo a través de marca de tiempo. Para evitar este problema, mantenga el tamaño de todos los blobs por debajo de 10 MB.
Limitación del volumen de eventos de Base de datos SQL|Cuando se utiliza la Base de datos SQL como destino de la salida, unos volúmenes de datos de salida muy elevados pueden hacer que se agote el tiempo de espera del trabajo de Análisis de transmisiones. Para resolver este problema, reduzca el volumen de salida con agregados u operadores de filtro o elija el almacenamiento de blobs de Azure o los centros de eventos como destino de la salida.
Los conjuntos de datos PowerBI solo pueden contener una tabla|PowerBI no admite más de una tabla en un conjunto de datos determinado.

## Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=AzureStreamAnalytics)

## Pasos siguientes

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=AcomDC_0218_2016-->