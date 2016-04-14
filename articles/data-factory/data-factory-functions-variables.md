<properties 
	pageTitle="Data Factory: funciones y variables del sistema | Microsoft Azure" 
	description="Proporciona una lista de funciones y variables del sistema de Data Factory de Azure" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"
	services="data-factory"
/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2016" 
	ms.author="spelluru"/>

# Data Factory de Azure: funciones y variables del sistema
Este artículo proporciona información sobre las funciones y las variables compatibles con Data Factory de Azure.
  
## Variables del sistema de Data Factory

Nombre de la variable | Descripción | Ámbito del objeto | Ámbito JSON y casos de uso
------------- | ----------- | ------------ | ------------------------
WindowStart | Inicio del intervalo de tiempo para la ventana de ejecución de la actividad actual | activity | <ol><li>Especifique las consultas de selección de datos. Consulte los artículos de conector indicados en el artículo [Actividades del movimiento de datos](data-factory-data-movement-activities.md).</li><li>Pase los parámetros al script de Hive (ejemplo mostrado anteriormente).</li>
WindowEnd | Final del intervalo de tiempo para la ventana de ejecución de actividad actual | activity | Igual que el anterior.
SliceStart | Inicio del intervalo de tiempo para el segmento de datos que se está generando | activity<br/>dataset | <ol><li>Especifique las rutas de acceso de la carpeta dinámica y los nombres de archivo mientras se trabaja con el [blob de Azure](data-factory-azure-blob-connector.md) y con los [conjuntos de datos del sistema de archivos](data-factory-onprem-file-system-connector.md).</li><li>Especifique las dependencias de entrada con las funciones de Factoría de datos en la colección de entradas de la actividad.</li></ol>
SliceEnd | Fin del intervalo de tiempo para el segmento de datos que se está generando | actividad<br/>conjunto de datos | Igual que el anterior. 

> [AZURE.NOTE] Actualmente Factoría de datos requiere que el programa especificado en la actividad coincida exactamente con el programa especificado en la disponibilidad del conjunto de datos de salida. Esto significa que WindowStart, WindowEnd, SliceStart y SliceEnd siempre se asignan al mismo período de tiempo y un segmento de salida única.
 
## Funciones de Data Factory 

Puede usar las funciones de Factoría de datos junto con las variables del sistema mencionadas anteriormente con los fines siguientes:

1.	Especifique las consultas de selección de datos (consulte los artículos de conector mencionados en el artículo [Actividades de movimiento de datos](data-factory-data-movement-activities.md).

	La sintaxis para invocar una función de Factoría de datos es: **$$<function>** para las consultas de selección de datos y otras propiedades de la actividad y conjuntos de datos.  
2. Especificar las dependencias de entrada con las funciones de Factoría de datos en la colección de entradas de la actividad (consulte el ejemplo anterior).

	$$ no es necesario para especificar expresiones de dependencia de entrada.

En el ejemplo siguiente, la propiedad **sqlReaderQuery** de un archivo JSON se asigna a un valor devuelto por la función **Text.Format**. Este ejemplo también usa una variable de sistema denominada **WindowStart**, que representa la hora de inicio de la ventana de ejecución de la actividad.
	
	{
	    "Type": "SqlSource",
	    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
	}

### Funciones

Las tablas siguientes muestran todas las funciones de Factoría de datos de Azure:

Categoría | Función | Parámetros | Descripción
-------- | -------- | ---------- | ----------- 
Hora | AddHours(X,Y) | X: DateTime <p>Y: int</p> | Agrega Y horas a la hora especificada X. <p>Ejemplo: 9/5/2013 12:00:00 p.m. + 2 horas = 5/9/2013 2:00:00 p.m.</p>
Hora | AddMinutes(X,Y) | X: DateTime <p>Y: int</p> | Agrega Y minutos a X.<p>Ejemplo: 15/9/2013 12:00:00 p.m. + 15 minutos = 15/9/2013 12:15:00 p.m.</p>
Hora | StartOfHour(X) | X: Datetime | Obtiene el tiempo de inicio para la hora representada por el componente de hora de X. <p>Ejemplo: StartOfHour de 15/9/2013 05:10:23 p.m. es 15/9/2013 05:00:00 p.m.</p>
Date | AddDays(X,Y) | X: DateTime<p>Y: int</p> | Agrega Y días a X. <p>Ejemplo: 15/9/2013 12:00:00 p.m. + 2 días = 17/9/2013 12:00:00 p.m.</p>
Date | AddMonths(X,Y) | X: DateTime<p>Y: int</p> | Agrega Y meses a X. <p>Ejemplo: 15/9/2013 12:00:00 p.m. + 1 mes = 15/10/2013 12:00:00 p.m.</p> 
Date | AddQuarters(X,Y) | X: DateTime <p>Y: int</p> | Agrega Y * 3 meses a X. <p>Ejemplo: 15/9/2013 12:00:00 p.m. + 1 trimestre = 15/12/2013 12:00:00 p.m.</p>
Date | AddWeeks(X,Y) | X: DateTime<p>Y: int</p> | Agrega Y * 7 días a X. <p>Ejemplo: 15/9/2013 12:00:00 p.m. + 1 semana = 22/9/2013 12:00:00 p.m.</p>
Date | AddYears(X,Y) | X: DateTime<p>Y: int</p> | Agrega Y años a X. <p>Ejemplo: 15/9/2013 12:00:00 p.m. + 1 año = 15/9/2014 12:00:00 p.m.</p>
Date | Day(X) | X: DateTime | Obtiene el componente de día de X.<p>Ejemplo: Día de 15/9/2013, 12:00:00 p.m. es 9.</p>
Date | DayOfWeek(X) | X: DateTime | Obtiene el día del componente de la semana de X.<p>Ejemplo: DayOfWeek de 15/9/2013, 12:00:00 p.m. es domingo.</p>
Date | DayOfYear(X) | X: DateTime | Obtiene el día del año representado por el componente de año de X.<p>Ejemplos:<br/>1/12/2015: día 335 de 2015<br/>31/12/2015: día 365 de 2015<br/>31/12/2016: día 366 de 2016 (año bisiesto)</p>
Date | DaysInMonth(X) | X: DateTime | Obtiene los días del mes representado por el componente de mes del parámetro X.<p>Ejemplo: DaysInMonth de 15/9/2013 son 30 debido a que hay 30 días del mes de septiembre.</p>
Date | EndOfDay(X) | X: DateTime | Obtiene la fecha y hora que representa el final del día (componente de días) de X.<p>Ejemplo: EndOfDay de 15/9/2013 05:10:23 p.m. es 15/9/2013 11:59:59 p.m.</p>
Date | EndOfMonth(X) | X: DateTime | Obtiene el final del mes representado por el componente de mes del parámetro X.<p>Ejemplo: EndOfMonth de 15/9/2013 05:10:23 p.m. es 30/9/2013 11:59:59 p.m. (fecha y hora que representa el final del mes de septiembre)</p>
Date | StartOfDay(X) | X: DateTime | Obtiene el inicio del día representado por el componente de día del parámetro X.<p>Ejemplo: StartOfDay de 15/9/2013 05:10:23 p.m. es 15/9/2013 12:00:00 a.m.</p>
DateTime | From(X) | X: String | Analiza la cadena X en una fecha y hora.
DateTime | Ticks(X) | X: DateTime | Obtiene la propiedad ticks del parámetro X. Un ciclo es igual a 100 nanosegundos. El valor de esta propiedad representa el número de ciclos transcurridos desde la medianoche del 1 de enero de 0001. 
Texto | Format(X) | X: String variable | Da formato al texto.

#### Ejemplo de Text.Format

	"defines": { 
	    "Year" : "$$Text.Format('{0:yyyy}',WindowStart)",
	    "Month" : "$$Text.Format('{0:MM}',WindowStart)",
	    "Day" : "$$Text.Format('{0:dd}',WindowStart)",
	    "Hour" : "$$Text.Format('{0:hh}',WindowStart)"
	}

Consulte el tema [Cadenas con formato de fecha y hora personalizado](https://msdn.microsoft.com/library/8kb3ddd4.aspx), en el que se describen las diferentes opciones de formato que puede usar (por ejemplo: aa frente a aaaa).

> [AZURE.NOTE] Cuando se usa una función dentro de otra función, no es necesario usar el prefijo **$$** para la función interna. Por ejemplo: $$Text.Format('PartitionKey eq \\'my\_pkey\_filter\_value\\' y RowKey ge \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(SliceStart, -6)). En este ejemplo, observe que el prefijo **$$** no se usa para la función **Time.AddHours**.
  

<!---HONumber=AcomDC_0224_2016-->