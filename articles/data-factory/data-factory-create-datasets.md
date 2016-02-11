<properties 
	pageTitle="Creación de conjuntos de datos" 
	description="Descripción de los conjuntos de datos de Factoría de datos de Azure y aprenda a crearlos." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/26/2016" 
	ms.author="spelluru"/>

# Conjuntos de datos

## Descripción
Un conjunto de datos es una descripción lógica de los datos. Los datos que se describen pueden variar desde bytes simples, pasando por datos semiestructurados, como archivos CSV, hasta tablas relacionales o incluso modelos. El mecanismo de (dirección, protocolo, esquema de autenticación) para tener acceso a los datos se define en el servicio vinculado y se hace referencia a él en la definición del conjunto de datos.

## Sintaxis

	{
	    "name": "<name of dataset>",
	    "properties":
	    {
	       "structure": [ ],
	       "type": "<type of dataset>",
			"external": <boolean flag to indicate external data>,
	        "typeProperties":
	        {
	        },
	        "availability":
	        {
	
	        },
	       "policy": 
	        {      
	
	        }
	    }
	}

**Detalles de la sintaxis**

| Propiedad | Descripción | Obligatorio | Valor predeterminado |
| -------- | ----------- | -------- | ------- |
| name | Nombre del conjunto de datos | Sí | N/D |
| structure | <p>Esquema del conjunto de datos</p><p>Consulte la sección [Estructura del conjunto de datos](#Structure)para obtener más detalles</p> | Nº | N/D |
| type | Tipo de conjunto de datos | Sí | N/D |
| typeProperties | <p>Propiedades correspondiente al tipo seleccionado</p><p>Consulte la sección [Tipo de conjunto de datos](#Type) para obtener detalles sobre los tipos admitidos y sus propiedades.</p> | Sí | N/D |
| external | Marca booleana para especificar si un conjunto de datos es generado explícitamente por una canalización de la factoría de datos o no | No | false | 
| availability | <p>Define la ventana de procesamiento o el modelo de segmentación para la producción del conjunto de datos. </p><p>Consulte el tema [Disponibilidad del conjunto de datos](#Availability) para obtener más detalles</p><p>Consulte el artículo [Programación y ejecución](data-factory-scheduling-and-execution.md) para obtener más detalles sobre el modelo de segmentación del conjunto de datos</p> | Sí | N/D
| policy | Define los criterios o la condición que deben cumplir los segmentos del conjunto de datos. <p>Consulte el tema [Directiva del conjunto de datos](#Policy) para obtener más detalles</p> | No | N/D |

### Ejemplo

A continuación se muestra un ejemplo de un conjunto de datos que representa una tabla denominada**MyTable** en **Base de datos SQL de Azure**. Las cadenas de conexión de Base de datos SQL de Azure se definen en **AzureSqlLinkedService** al que se hace referencia en este conjunto de datos. Este conjunto de datos se segmenta diariamente.

	{
	    "name": "DatasetSample",
	    "properties": {
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": 
	        {
	            "tableName": "MyTable"
	        },
	        "availability": 
	        {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

## <a name="Structure"></a>Estructura del conjunto de datos

La sección **Structure** valida el esquema del conjunto de datos. Contiene la colección de las columnas y el tipo de dichas columnas. Cada columna contiene las siguientes propiedades:

Propiedad | Descripción | Obligatorio | Valor predeterminado  
-------- | ----------- | -------- | -------
name | Nombre de la columna | No | N/D 
type | Tipo de datos de la columna | No | N/D 

### Ejemplo

En el ejemplo siguiente, el conjunto de datos tiene tres columnas slicetimestamp, projectname y pageviews.

	structure:  
	[ 
	    { "name": "slicetimestamp", "type": "String"},
	    { "name": "projectname", "type": "String"},
	    { "name": "pageviews", "type": "Decimal"}
	]

## <a name="Type"></a> Tipo de conjunto de datos

Los orígenes de datos admitidos y los tipos de conjunto de datos están alineados. Consulte los temas de conector a los que se hace referencia en el artículo [Actividades de movimiento de datos](data-factory-data-movement-activities.md) para obtener información sobre los tipos y la configuración de los conjuntos de datos.

## <a name="Availability"></a> Disponibilidad del conjunto de datos

En la sección Availability de un conjunto de datos se define la ventana de procesamiento o el modelo de segmentación para la producción del conjunto de datos. Consulte el artículo [Programación y ejecución](data-factory-scheduling-and-execution.md) para obtener más detalles sobre el modelo de segmentación y dependencia del conjunto de datos.

| Propiedad | Descripción | Obligatorio | Valor predeterminado |
| -------- | ----------- | -------- | ------- |
| frequency | Especifica la unidad de tiempo para la producción de segmento del conjunto de datos.<p>**Frecuencia admitida**: Minute, Hour, Day, Week, Month</p> | Sí | N/D |
| interval | Especifica un multiplicador para frecuencia<p>"Frequency x interval" determina la frecuencia con la que se genera el segmento.</p><p>Si necesita segmentar el conjunto de datos cada hora, establezca **Frequency** en **Hour** e **interval** en **1**.</p><p>**Nota:** Si especifica Frequency en Minute, se recomienda establecer interval en menos de 15</p> | Sí | N/D |
| style | Especifica si se debe generar el segmento al principio/final del intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><p>Si se establece Frequency en Month y style se establece en EndOfInterval, se genera el segmento en el último día del mes. Si style está establecido en StartOfInterval, se genera el segmento en el primer día del mes..</p><p>Si se establece Frequency en Day y style se establece en EndOfInterval, el segmento se produce en la última hora del día.</p>Si se establece Frequency en Hour y style se establece en EndOfInterval, el segmento se produce al final de la hora. Por ejemplo, para un segmento para el período de 1 p.m. – 2 p.m., el segmento se produce a las 2 p.m.</p> | No | EndOfInterval |
| anchorDateTime | Define la posición absoluta en tiempo usada por el programador para calcular los límites de los segmentos del conjunto de datos.<p>**Nota:** si AnchorDateTime tiene partes de fecha más detalladas que frequency, se ignorarán las partes más detalladas. Por ejemplo, si **interval**es**hourly** (frequency: hour e interval: 1) y **AnchorDateTime** contiene **minutes and seconds** las partes **minutes and seconds** de AnchorDateTime se omitirán.</p>| No | 01/01/0001 |
| Offset | Intervalo de tiempo mediante el que se desplaza el inicio y el final de todos los segmentos del conjunto de datos.<p>**Nota:** si se especifican anchorDateTime y offset, el resultado es el desplazamiento combinado.</p> | No | N/D |

### Ejemplos de anchorDateTime

**Ejemplo:** segmentos del conjunto de datos de 23 horas que comienzan en el día y a la hora siguientes: 2007-04-19T08:00:00

	"availability":	
	{	
		"frequency": "Hour",		
		"interval": "23",	
		"anchorDateTime":"2007-04-19T08:00:00"	
	}


### Ejemplo de offset

Segmentos diarios que comienzan a las 6 a.m., en lugar de a medianoche, que es el valor predeterminado.

	"availability":
	{
		"frequency": "Daily",
		"interval": "1",
		"offset": "06:00:00"
	}

En este caso, SliceStart se desplaza 6 horas y será a las 6 a.m.

Para un programación de 12 meses (frecuency = month; interval = 12), offset: 60.00:00:00 significa el 1 o 2 de marzo de cada año (60 días desde el principio del año si style = StartOfInterval), en función de si el año es bisiesto o no.



## <a name="Policy"></a>Directiva del conjunto de datos

En la sección Directiva se definen los criterios o la condición que deben cumplir los segmentos del conjunto de datos.

### Directivas de validación

| Nombre de la directiva | Descripción | Aplicado a | Obligatorio | Valor predeterminado |
| ----------- | ----------- | ---------- | -------- | ------- |
| minimumSizeMB | Valida que los datos de un blob de Azure cumplen los requisitos de tamaño mínimo (en megabytes). | Blob de Azure | No | N/D |
|minimumRows | Valida que los datos de una Base de datos SQL de Azure o una tabla de Azure contienen el número mínimo de filas. | <ul><li>Base de datos SQL de Azure</li><li>Tabla de Azure</li></ul> | No | N/D

#### Ejemplos

**minimumSizeMB:**

	"policy":
	
	{
	    "validation":
	    {
	        "minimumSizeMB": 10.0
	    }
	}

**minimumRows**

	"policy":
	{
		"validation":
		{
			"minimumRows": 100
		}
	}

### Directivas de ExternalData

Los conjuntos de datos externos son los que no son producidos por una canalización de ejecución en la factoría de datos. Si el conjunto de datos está marcado como External, la directiva ExternalData puede definirse para influir en el comportamiento de la disponibilidad de los segmentos del conjunto de datos.

| Nombre | Descripción | Obligatorio | Valor predeterminado |
| ---- | ----------- | -------- | -------------- |
| dataDelay | <p>Tiempo de retraso de la comprobación de la disponibilidad de los datos externos para el segmento especificado. Por ejemplo, si se supone que los datos estarán disponibles cada hora, la comprobación de si los datos externos están realmente disponibles y el segmento correspondiente está preparado puede retrasarse mediante dataDelay.</p><p>Solo se aplica a la hora actual; por ejemplo, si ahora es la 1:00 p.m. y este valor es de 10 minutos, la validación se iniciará a la 1:10 p.m.</p><p>Esta configuración no afecta a los segmentos en el pasado (los segmentos con Slice End Time + dataDelay < Nowa) se procesarán sin ningún retraso.</p> <p>El tiempo superior a 23:59 horas se debe especificar con el formato día.horas:minutos:segundos. Por ejemplo, para especificar 24 horas, no use 24:00:00; en su lugar, use 1.00:00:00. Si usa 24:00:00, se tratará como 24 días (24.00:00:00). Para 1 día y 4 horas, especifique 1:04:00:00. </p>| No | 0 |
| retryInterval | El tiempo de espera entre un error y el siguiente reintento. Se aplica a la hora actual; si el intento anterior falla, esperamos este tiempo después del último intento. <p>Si ahora es la 1:00 p.m., empezaremos el primer intento. Si la duración para completar la primera comprobación de validación es 1 minuto y la operación falla, el siguiente reintento será a la 1:00 + 1 minuto (duración) + 1 minuto (intervalo de reintento) = 1:02 pm. </p><p>En el caso de los segmentos en el pasado, no habrá ningún retraso. El reintento se realizará inmediatamente.</p> | No | 00:01:00 (1 minuto) | 
| retryTimeout | El tiempo de espera de cada reintento.<p>Si se establece en 10 minutos, la validación se debe completar en 10 minutos. Si la validación tarda más de 10 minutos en realizarse, el reintento agotará el tiempo de espera.</p><p>Si se agota el tiempo de todos los intentos de validación, el segmento se marcará como TimedOut.</p> | No | 00:10:00 (10 minutos) |
| maximumRetry | Número de veces que se va a comprobar la disponibilidad de los datos externos. El valor máximo permitido es 10. | No | 3 | 

#### Más ejemplos

Si necesita ejecutar una canalización mensualmente en una fecha y hora específicas (supongamos que el tercer día de cada mes a las 8 de la mañana), podría usar la etiqueta **offset** para establecer la fecha y la hora en que se debería ejecutar.

	{
	  "name": "MyDataset",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "availability": {
	      "frequency": "Month",
	      "interval": 1,
	      "offset": "3.08:10:00",
	      "style": "StartOfInterval"
	    }
	  }
	}

<!---HONumber=AcomDC_0128_2016-->