<properties 
 pageTitle="Creación de programaciones complejas y periodicidad avanzada con Programador de Azure" 
 description="" 
 services="scheduler" 
 documentationCenter=".NET" 
 authors="krisragh" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="scheduler" 
 ms.workload="infrastructure-services" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="12/04/2015" 
 ms.author="krisragh"/>

# Creación de programaciones complejas y periodicidad avanzada con Programador de Azure  

## Información general

En el corazón de un trabajo del Programador de Azure se encuentra la *programación*. La programación determina cuándo y cómo el Programador ejecuta el trabajo.

Programador de Azure le permite especificar distintas programaciones periódicas y únicas para un trabajo. Las programaciones *únicas* se desencadenan una vez en una hora especificadas; en efecto, son programaciones *periódicas* que se ejecutan solo una vez. Las programaciones recurrentes se activan en una frecuencia predeterminada.

Con esta flexibilidad, Programador de Azure le permite admitir una amplia variedad de escenarios empresariales:

-	Limpieza de datos periódicos; por ejemplo, todos los días hay que eliminar todos los tweets de más de 3 meses.
-	Archivado; por ejemplo, cada mes hay que insertar el histórico de facturas en el servicio de copia de seguridad.
-	Solicitudes de datos externos; por ejemplo, cada 15 minutos hay que extraer un nuevo informe meteorológico de esquí de NOAA.
-	Procesamiento de imágenes; por ejemplo, todos los días laborables, fuera de las horas pico, hay que utilizar la informática en la nube para comprimir las imágenes cargadas durante ese día.


En este artículo se describen ejemplos de trabajos que se pueden crear con Programador de Azure. Se proporcionan los datos JSON que describen cada programación. Si se usa la [API de REST de Programador](https://msdn.microsoft.com/library/azure/dn528946.aspx), puede usar este mismo JSON para [crear un trabajo de Programador de Azure](https://msdn.microsoft.com/library/azure/dn528937.aspx).

## Escenarios admitidos

Los diversos ejemplos de este tema muestran la gran variedad de escenarios que admite Programador de Azure. En general, estos ejemplos muestran cómo crear programaciones para diversos patrones de comportamiento, incluidos los siguientes:

-	Ejecutar una vez en una determinada fecha y hora
-	Ejecutar y repetir un número de veces explícitas
-	Ejecutar inmediatamente y repetir 
-	Ejecutar y repetir cada *n* minutos, horas, días, semanas o meses a partir de un período de tiempo determinado
-	Ejecutar y repetir con frecuencia semanal o mensual, pero solo en días específicos, en determinados días de la semana o en días específicos del mes
-	Ejecutar y repetir varias veces en un período; por ejemplo, el último viernes y lunes de cada mes, o a las 5:15 a.m. y a las 5:15 p.m. todos los días

## Fechas y fecha/hora

Las fechas en Programador de Azure siguen la [especificación ISO-8601](http://en.wikipedia.org/wiki/ISO_8601) y solo incluyen la fecha.

Las referencias a fecha/hora en los trabajos de Programador de Azure siguen la [especificación ISO-8601](http://en.wikipedia.org/wiki/ISO_8601) y solo incluyen las partes de fecha y de hora. Una fecha y hora que no especifica un desfase de hora UTC se supone que es una hora UTC.

## Uso de JSON y API de REST para crear programaciones

Para crear una programación simple con los ejemplos JSON en este artículo y la API de REST de Programador de Azure, [cree primero un servicio en la nube](https://msdn.microsoft.com/library/azure/dn528943.aspx), [después, cree una colección de trabajos](https://msdn.microsoft.com/library/azure/dn528940.aspx) y, [finalmente, cree un trabajo](https://msdn.microsoft.com/library/azure/dn528937.aspx). Cuando se crea un trabajo, puede especificar la programación y periodicidad mediante JSON como en el extracto siguiente:

	{
	    "startTime": "2012-08-04T00:00Z", // optional
	     …
	    "recurrence":                     // optional
	    {
	        "frequency": "week",     // can be "year" "month" "day" "week" "hour" "minute"
	        "interval": 1,                // optional, how often to fire (default to 1)
	        "schedule":                   // optional (advanced scheduling specifics)
	        {
	            "weekDays": ["monday", "wednesday", "friday"],
	            "hours": [10, 22]                      
	        },
	        "count": 10,                  // optional (default to recur infinitely)
	        "endTime": "2012-11-04",      // optional (default to recur infinitely)
	    },
	    …
	}
	
## Información general: conceptos básicos de esquema de trabajo

En la tabla siguiente se muestra una descripción general de los elementos más importantes relacionados con la periodicidad y la programación de un trabajo:

|**Nombre JSON**|**Descripción**|
|:--|:--|
|**_startTime_**|_startTime_ es una fecha y hora. En las programaciones simples, _startTime_ es la primera repetición y, en las programaciones complejas, el trabajo se iniciará no antes que _startTime_.|
|**_recurrence_**|El objeto _recurrence_ especifica las reglas de periodicidad para el trabajo y la periodicidad con la que se ejecutará el trabajo. El objeto recurrence admite los elementos _frequency, interval, endTime, count,_ y _schedule_. Si se define _recurrence_, se requiere _frequency_; los otros elementos de _recurrence_ son opcionales.|
|**_frequency_**|La cadena _frequency_ representa la unidad de frecuencia en la que se repite el trabajo. Los valores admitidos son _"minute", "hour", "day", "week",_ o _"month"_.|
|**_interval_**|_interval_ es un entero positivo e indica el intervalo para el valor de _frequency_ que determina la frecuencia con la que se ejecutará el trabajo. Por ejemplo, si _interval_ es 3 y _frequency_ es "week", el trabajo se repite cada tres semanas. Programador de Azure admite un valor máximo de _interval_ de 18 meses para la frecuencia mensual, 78 semanas para la frecuencia semanal o 548 días para la frecuencia diaria. Para una frecuencia de horas y minutos, el intervalo admitido es 1 < = _interval_ < = 1000.|
|**_endTime_**|La cadena _endTime_ especifica la fecha y hora pasadas las cuales el trabajo no se debe ejecutar. No es válido tener un valor de _endTime_ en el pasado. Si no hay ningún _endTime_ o se especifica un valor count, el trabajo se ejecuta indefinidamente. Tanto _endTime_ como _count_ no se pueden incluir en el mismo trabajo.|
|**_count_**|<p>_count_ es un entero positivo (mayor que cero) que especifica el número de veces que debe ejecutarse este trabajo antes de finalizar. </p><p>_count_ representa el número de veces que se ejecuta el trabajo antes de que se determine como completado. Por ejemplo, para un trabajo que se ejecuta diariamente con un valor de _count_ de 5 y con lunes como fecha de inicio, el trabajo se completa después de la ejecución el viernes. Si la fecha de inicio se encuentra en el pasado, la primera ejecución se calcula a partir de la hora de creación.</p><p>Si no hay ningún _endTime_ o _count_ especificados, el trabajo se ejecuta indefinidamente. Tanto _endTime_ como _count_ no se pueden incluir en el mismo trabajo.</p>|
|**_schedule_**|Un trabajo con una frecuencia especificada modifica su periodicidad según una programación periódica. Una _programación_ contiene modificaciones basadas en minutos, horas, días de la semana, días del mes y número de semana.|

## Información general: Valores predeterminados del esquema de trabajo, límites y ejemplos

Después de esta información general, tratemos cada uno de estos elementos de detalle.

|**Nombre JSON**|**Tipo de valor**|**¿Necesario?**|**Valor predeterminado**|**Valores válidos**|**Ejemplo**|
|:---|:---|:---|:---|:---|:---|
|**_startTime_**|String|No|None|Fechas-horas ISO-8601|<code>"startTime" : "2013-01-09T09:30:00-08:00"</code>|
|**_recurrence_**|Objeto|No|None|Objeto de periodicidad|<code>"recurrence" : { "frequency" : "monthly", "interval" : 1 }</code>|
|**_frequency_**|String|Sí|None|"minute", "hour", "day", "week", "month"|<code>"frequency" : "hour"</code> |
|**_interval_**|Number|No|1|1 a 1000.|<code>"interval":10</code>|
|**_endTime_**|String|No|None|Valor de fecha y hora que representa un periodo de tiempo en el futuro|<code>"endTime" : "2013-02-09T09:30:00-08:00"</code> |
|**_count_**|Number|No|None|>= 1|<code>"count": 5</code>|
|**_schedule_**|Objeto|No|None|Objeto de programación|<code>"schedule" : { "minute" : [30], "hour" : [8,17] }</code>|

## Profundización: _startTime_

La siguiente tabla captura cómo _startTime_ controla cómo se ejecuta un trabajo.

|**Valor de startTime**|**Sin periodicidad**|**Periodicidad. Sin programación**|**Periodicidad con programación**|
|:--|:--|:--|:--|
|**Sin hora de inicio**|Ejecutar inmediatamente una vez|Ejecutar inmediatamente una vez. Realizar las ejecuciones posteriores basándose en el cálculo del último tiempo de ejecución.|<p>Ejecutar inmediatamente una vez</p><p>Realizar las ejecuciones posteriores según la programación de periodicidad</p>|
|**Hora de inicio en el pasado**|Ejecutar inmediatamente una vez|<p>Calcular la primera hora de ejecución futura después de la hora de inicio y ejecutarlo en ese momento</p><p>Realizar las ejecuciones posteriores basadas en el cálculo de la última hora de ejecución</p><p>Consulte el ejemplo después de esta tabla para obtener una explicación adicional</p>|<p>El trabajo se inicia _no antes que_ la hora de inicio especificada. La primera repetición se basa en la programación que se calcula a partir de la hora de inicio</p><p>Realizar las ejecuciones posteriores según la programación de periodicidad</p>|
|**Hora de inicio en el futuro o en el presente**|Ejecutar una vez a la hora de inicio especificada|<p>Ejecutar una vez en la hora de inicio especificada</p><p>Realizar las ejecuciones posteriores basándose en el cálculo desde la última hora de ejecución</p>|<p>El trabajo se inicia _no antes que_ la hora de inicio especificada. La primera repetición se basa en la programación que se calcula a partir de la hora de inicio</p><p>Realizar las ejecuciones posteriores según la programación de periodicidad</p>|

Veamos un ejemplo de lo que sucede cuando _startTime_ se encuentra en el pasado, con _recurrence_ pero sin _schedule_. Suponga que la fecha y hora actual es 08-04-2015 13:00, _startTime_ es 2015-04-07 14:00 y _recurrence_ es de 2 días (definida con _frequency_: día e _interval_: 2+.) Tenga en cuenta que _startTime_ se encuentra en el pasado y se produce antes de la hora actual.

En estas condiciones, la _primera ejecución_ será el 2015-04-09 a las 14:00. El motor del Programador calcula las repeticiones de la ejecución desde la hora de inicio. Se descartan las instancias en el pasado. El motor utiliza la instancia siguiente que tiene lugar en el futuro. En este caso, _startTime_ es 2015-04-07 a las 2:00 p.m., así que la siguiente instancia es 2 días a partir de ese momento, que es el 2015-04-09 a las 2:00 p.m.

Tenga en cuenta que la primera ejecución debería ser la misma incluso si startTime es 2015-04-05 14:00 o 2015-04-01 14:00. Después de la primera ejecución, las ejecuciones posteriores se calculan con la programación, por lo que se realizarían el 2015-04-11 a 2:00 p.m., a continuación el 2015-04-13 a las 2:00 p.m., después el+ 2015-04-15 a las 2:00 p.m., etc.

Por último, cuando un trabajo tiene una programación, si no se establecen en la programación las horas y minutos, se asume el valor predeterminado de las horas y minutos de la primera ejecución, respectivamente.

## Profundización: _schedule_

Por un lado, _schedule_ puede _limitar_ el número de ejecuciones de trabajos. Por ejemplo, si un trabajo con una frecuencia de "mes" tiene un valor de _schedule_ que se ejecuta solo el día 31, el trabajo se ejecuta solo en los meses que tienen 31 <sup></sup> días.

Por otro lado, una _programación_ también puede _expandir_ el número de ejecuciones de trabajos. Por ejemplo, si un trabajo con una frecuencia "mensual" tiene una _programación_ que se ejecuta los días 1 y 2 del mes, el trabajo se ejecuta en los días primero y segundo <sup></sup> <sup></sup> del mes en lugar de solo una vez al mes.

Si se especifican varios elementos de programación, el orden de evaluación es de mayor a menor: número de semana, mes, día, día de la semana, hora y minuto.

En la siguiente tabla se describen los elementos de _schedule_ con detalle:

|**Nombre JSON**|**Descripción**|**Valores válidos**|
|:---|:---|:---|
|**minutes**|Minutos de la hora en la que se ejecuta el trabajo|<ul><li>Entero o</li><li>matriz de enteros</li></ul>|
|**hours**|Horas del día en las que se ejecuta el trabajo|<ul><li>Entero o</li><li>matriz de enteros</li></ul>|
|**weekDays**|Días de la semana en los que se ejecutará el trabajo Solo se puede especificar con una frecuencia semanal.|<ul><li>"Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday" o "Sunday"</li><li>Matriz con cualquiera de los valores anteriores (tamaño máx. de matriz 7)</li></ul>\_No\_ distingue mayúsculas de minúsculas|
|**monthlyOccurrences**|Determina los días del mes en los que se ejecutará el trabajo. Solo se puede especificar con una frecuencia mensual.|<ul><li>Matriz de objetos monthlyOccurence:</li></ul> <pre>{ "day": _day_,<br /> "occurrence": _occurence_<br />}</pre><p> _day_ es el día de la semana en el que se ejecutará el trabajo; por ejemplo, {Sunday} es cada domingo del mes. Necesario.</p><p>El valor de _occurrence_ es la repetición del día durante el mes, por ejemplo, {domingo, -1} es el último domingo del mes. Opcional.</p>|
|**monthDays**|Día del mes en el que se ejecutará el trabajo. Solo se puede especificar con una frecuencia mensual.|<ul><li>Cualquier valor < = -1 y > = -31.</li><li>Cualquier valor > = 1 y < = 31.</li><li>Una matriz de los valores anteriores</li></ul>|

## Ejemplos: Programaciones de periodicidad

A continuación de muestran unos ejemplos de programaciones de periodicidad, centrándose en el objeto de programación y sus subelementos.

Todas las programaciones siguientes asumen que el _intervalo_ está establecido en 1. Además, una debe asumir la frecuencia correcta de acuerdo con lo que está en _schedule_: por ejemplo, una no puede usar la frecuencia "day" y tiene una modificación "monthDays" en la programación. Estas restricciones se han descrito anteriormente.

|**Ejemplo**|**Descripción**|
|:---|:---|
|<code>{"hours":[5]}</code>|Se ejecuta a las 5 a.m. cada día. Programador de Azure hace corresponder cada valor en "horas" con cada valor de "minutos", uno por uno, para crear una lista de todas las veces en las que se va a ejecutar el trabajo.|
|<code>{"minutes":[15],"hours":[5]}</code>|Se ejecuta a las 5:15 a.m. cada día.|
|<code>{"minutes":[15],"hours":[5,17]}</code>|Se ejecuta a las 5:15 a.m. y 5:15 p.m. todos los días.|
|<code>{"minutes":[15,45],"hours":[5,17]}</code>|Se ejecuta a las 5:15 a.m., 5:45 a.m., 5:15 p.m. y a las 5:45 p.m. cada día.|
|<code>{"minutes":[0,15,30,45]}</code>|Se ejecuta cada 15 minutos.|
|<code>{hours":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23]}</code>|Se ejecuta cada hora. Este trabajo se ejecuta cada hora. Los minutos se controlan mediante _startTime_, si se especifica una fecha y hora de inicio, o si no se especifica, en el momento de creación. Por ejemplo, si la hora de inicio o la hora de creación (lo que se aplique) es 12:25 p.m., el trabajo se ejecutará a las 00:25, 01:25, 02:25, …, 23:25. La programación equivale a tener un trabajo con un valor de _frequency_ de "hora", un valor de _interval_ de 1 y ningún valor de _schedule_. La diferencia es que esta programación puede usarse con diferentes valores en _frequency_ e _interval_ para crear también otros trabajos. Por ejemplo, si el valor de _frequency_ fuera "mes", la programación se ejecutaría solo una vez al mes en lugar de todos los días si _frequency_ fuera "día"|
|<code>{minutes:[0]}</code>|Se ejecuta cada hora durante la hora. Esta tarea también se ejecuta cada hora, pero a la hora exacta (por ejemplo, 12 a.m., 1 a.m., 2 a.m., etc.) Esto es equivalente a un trabajo con una frecuencia de "hora", una hora de inicio con cero minutos y a ninguna programación si la frecuencia fuera "día", pero si la frecuencia fuera "semana" o "mes", la programación podría ejecutarse solo un día a la semana o un día del mes, respectivamente.|
|<code>{"minutes":[15]}</code>|Se ejecuta a «y cuarto» cada hora. Se ejecuta cada hora, comenzando en 00:15 a.m., 1:15 a.m., 2:15 a.m., etc. y terminando en 10:15 p.m. y las 11:15 p.m.|
|<code>{"hours":[17],"weekDays":["saturday"]}</code>|Se ejecuta a las 5 p.m. los sábados cada semana.|
|<code>{hours":[17],"weekDays":["monday","wednesday","friday"]}</code>|Se ejecuta a las 5 p.m. el lunes, el miércoles y el viernes de cada semana,|
|<code>{"minutes":[15,45],"hours":[17],"weekDays":["monday","wednesday","friday"]}</code>|Se ejecuta a las 5:15 p.m. y las 5:45 p.m. el lunes, el miércoles y el viernes cada semana.|
|<code>{"hours":[5,17],"weekDays":["monday","wednesday","friday"]}</code>|Se ejecuta a las 5 a.m. y a las 5 p.m. el lunes, el miércoles y el viernes de cada semana.|
|<code>{"minutes":[15,45],"hours":[5,17],"weekDays":["monday","wednesday","friday"]}</code>|Se ejecuta a las 5:15 a.m., 5:45 a.m., 5:15 p.m. y las 5:45 p.m. el lunes, el miércoles y el viernes cada semana|
|<code>{"minutes":[0,15,30,45], "weekDays":["monday","tuesday","wednesday","thursday","friday"]}</code>|Se ejecuta cada 15 minutos los días laborables.|
|<code>{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday","tuesday","wednesday","thursday","friday"]}</code>|Se ejecuta cada 15 minutos los días laborables entre las 9 a.m. y las 4:45 p.m.|
|<code>{"weekDays":["sunday"]}</code>|Se ejecuta los domingos a la hora de inicio.|
|<code>{"weekDays":["tuesday", "thursday"]}</code>|Se ejecuta los martes y los jueves a la hora de inicio.|
|<code>{"minutes":[0],"hours":[6],"monthDays":[28]}</code>|Se ejecuta a las 6 a.m. del día 28 de cada mes (suponiendo una frecuencia de mes).|
|<code>{"minutes":[0],"hours":[6],"monthDays":[-1]}</code>|Se ejecuta a las 6 a.m. el último día del mes. Si desea ejecutar un trabajo en el último día del mes, use -1 en lugar de día 28, 29, 30 o 31.|
|<code>{"minutes":[0],"hours":[6],"monthDays":[1,-1]}</code>|Se ejecuta a las 6 a.m. el primer y último día de cada mes.|
|<code>{monthDays":[1,-1]}</code>|Se ejecuta el primer y último día de cada mes a la hora de inicio.|
|<code>{monthDays":[1,14]}</code>|Se ejecuta el primer y decimocuarto día de cada mes a la hora de inicio.|
|<code>{monthDays":[2]}</code>|Se ejecuta el segundo día del mes en la hora de inicio.|
|<code>{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday","occurrence":1}]}</code>|Se ejecuta el primer viernes de cada mes a las 5 a.m.|
|<code>{"monthlyOccurrences":[{"day":"friday","occurrence":1}]}</code>|: Se ejecuta el primer viernes de cada mes a la hora de inicio.|
|<code>{"monthlyOccurrences":[{"day":"friday","occurrence":-3}]}</code>|Se ejecuta el tercer viernes desde el final del mes, cada mes, a la hora de inicio.|
|<code>{"minutes":[15],"hours":[5],"monthlyOccurrences":[{"day":"friday","occurrence":1},{"day":"friday","occurrence":-1}]}</code>|Se ejecuta el primer y último viernes de cada mes a las 5:15 a.m.|
|<code>{"monthlyOccurrences":[{"day":"friday","occurrence":1},{"day":"friday","occurrence":-1}]}</code>|Se ejecuta el primer y último viernes de cada mes a la hora de inicio.|
|<code>{"monthlyOccurrences":[{"day":"friday","occurrence":5}]}</code>|Se ejecuta el quinto viernes de cada mes a la hora de inicio. Si no hay ningún quinto viernes en un mes, no se ejecuta ya que se ha programado para ejecutarse solo el quinto viernes. Puede considerar usar -1 en lugar de 5 para la repetición si desea ejecutar el trabajo en el último viernes del mes.|
|<code>{"minutes":[0,15,30,45],"monthlyOccurrences":[{"day":"friday","occurrence":-1}]}</code>|Se ejecuta cada 15 minutos el último viernes del mes.|
|<code>{"minutes":[15,45],"hours":[5,17],"monthlyOccurrences":[{"day":"wednesday","occurrence":3}]}</code>|Se ejecuta a las 5:15 a.m., 5:45 a.m., 5:15 p., y 5:45 p.m. el tercer miércoles de cada mes|

## Otras referencias
 

 [¿Qué es Programador?](scheduler-intro.md)
 
 [Conceptos, terminología y jerarquía de entidades de Programador de Azure](scheduler-concepts-terms.md)

 [Introducción al Programador de Azure en el Portal de Azure](scheduler-get-started-portal.md)

 [Planes y facturación en Programador de Azure](scheduler-plans-billing.md)

 [Referencia de API de REST de Programador de Azure](https://msdn.microsoft.com/library/mt629143)

 [Referencia de cmdlets de PowerShell de Programador de Azure](scheduler-powershell-reference.md)

 [Alta disponibilidad y confiabilidad de Programador de Azure](scheduler-high-availability-reliability.md)

 [Límites, valores predeterminados y códigos de error de Programador de Azure](scheduler-limits-defaults-errors.md)

 [Autenticación de salida de Programador de Azure](scheduler-outbound-authentication.md)
 
  

<!---HONumber=AcomDC_0128_2016-->