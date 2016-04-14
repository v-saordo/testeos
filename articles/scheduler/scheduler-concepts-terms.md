<properties
 pageTitle="Conceptos, términos y entidades del programador | Microsoft Azure"
 description="Conceptos del Programador de Azure, terminología y jerarquía de entidades, incluidos trabajos y colecciones de trabajos. Proporciona un ejemplo completo de un trabajo programado."
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
 ms.topic="get-started-article"
 ms.date="12/04/2015"
 ms.author="krisragh"/>

# Conceptos, terminología y jerarquía de entidades de Programador

## Jerarquía de entidades del Programador

En la tabla siguiente se describen los recursos principales expuestos o utilizados por la API del Programador:

|Recurso | Descripción |
|---|---|
|**Servicio en la nube**|Conceptualmente, un servicio en la nube representa una aplicación. Una suscripción puede tener varios servicios en la nube.|
|**Colección de trabajos**|Una colección de trabajos contiene un grupo de trabajos y mantiene configuraciones, cuotas y aceleradores compartidos por los trabajos de la colección. La colección de trabajos la crea el propietario de la suscripción y agrupa los trabajos en función de los límites de uso o de aplicación. Está limitado a una región. También permite la aplicación de cuotas para restringir el uso de todos los trabajos de la colección. Las cuotas incluyen MaxJobs y MaxRecurrence.|
|**Trabajo**|Un trabajo define una única acción periódica, con estrategias simples o complejas para su ejecución. Las acciones pueden incluir solicitudes HTTP o de cola de almacenamiento.|
|**Historial de trabajos**|Un historial de trabajos representa los detalles de una ejecución de un trabajo. Contiene los trabajos realizados correctamente y los errores, así como los detalles de las respuestas.|

## Administración de entidades de Programador

En un nivel superior, el Programador y la API de administración de servicio exponen las operaciones siguientes en los recursos:

|Capacidad|Descripción y dirección URI|
|---|---|
|**Administración de servicios en la nube**|GET, PUT y DELETE permiten la creación y modificación de servicios en la nube <p>`https://management.core.windows.net/{subscriptionId}/cloudservices/{cloudServiceName}`</p>|
|**Administración de la colección de trabajos**|GET, PUT y DELETE permiten la creación y modificación de las colecciones de trabajos y trabajos que contienen. Una colección de trabajos es un contenedor para trabajos, asignaciones para cuotas y configuración compartida. Ejemplos de cuotas, descritos más adelante, son el número máximo de trabajos y un intervalo menor de periodicidad. <p>PUT y DELETE: `https://management.core.windows.net/{subscriptionId}/cloudservices/{cloudServiceName}/resources/scheduler/jobcollections/{jobCollectionName}`</p><p>GET: `https://management.core.windows.net/{subscriptionId}/cloudservices/{cloudServiceName}/resources/scheduler/~/jobcollections/{jobCollectionName}`</p>
|**Administración de trabajos**|GET, PUT, POST, PATCH y DELETE permiten la creación y modificación de trabajos. Todos los trabajos deben pertenecer a una colección de trabajos que ya existe, así que no hay ninguna creación implícita.<p>`https://management.core.windows.net/{subscriptionId}/cloudservices/{cloudServiceName}/resources/scheduler/~/jobcollections/{jobCollectionName}/jobs/{jobId}`</p>|
|**Administración del historial de trabajos**|GET admite la recuperación de 60 días de historial de ejecución de trabajos, como el tiempo de trabajo transcurrido y los resultados de la ejecución del trabajo. Agrega compatibilidad de parámetros de cadena de consulta para filtrar basándose en el estado y la situación. <P>`https://management.core.windows.net/{subscriptionId}/cloudservices/{cloudServiceName}/resources/scheduler/~/jobcollections/{jobCollectionName}/jobs/{jobId}/history`</p>|

## Tipos de trabajo

Hay dos tipos de trabajos: los trabajos HTTP (incluidas trabajos HTTPS que admiten SSL) y los trabajos de cola de almacenamiento. Los trabajos HTTP son perfectos si dispone de un extremo de una carga de trabajo o servicio existente. Puede usar trabajos de cola de almacenamiento para enviar mensajes a colas de almacenamiento, por lo que dichos trabajos son ideales para cargas de trabajo que utilizan colas de almacenamiento.

## La entidad "Trabajo" en detalle

En un nivel básico, un trabajo programado cuenta con varias partes:

- La acción que se va a realizar cuando se activa el temporizador de trabajo  

- (Opcional) El tiempo para ejecutar el trabajo

- (Opcional) Cuándo y con qué frecuencia se debe repetir el trabajo

- (Opcional) Una acción que se desencadena si se produce un error en la acción principal

Internamente, un trabajo programado contiene también datos proporcionados por el sistema, como la siguiente hora de ejecución programada.

El código siguiente proporciona un ejemplo completo de un trabajo programado. En las secciones siguientes se proporcionan los detalles.

	{
		"startTime": "2012-08-04T00:00Z",               // optional
		"action":
		{
			"type": "http",
			"retryPolicy": { "retryType":"none" },
			"request":
			{
				"uri": "http://contoso.com/foo",        // required
				"method": "PUT",                        // required
				"body": "Posting from a timer",         // optional
				"headers":                              // optional

				{
					"Content-Type": "application/json"
				},
			},
		   "errorAction":
		   {
			   "type": "http",
			   "request":
			   {
				   "uri": "http://contoso.com/notifyError",
				   "method": "POST",
			   },
		   },
		},
		"recurrence":                                   // optional
		{
			"frequency": "week",                        // can be "year" "month" "day" "week" "minute"
			"interval": 1,                              // optional, how often to fire (default to 1)
			"schedule":                                 // optional (advanced scheduling specifics)
			{
				"weekDays": ["monday", "wednesday", "friday"],
				"hours": [10, 22]
			},
			"count": 10,                                 // optional (default to recur infinitely)
			"endTime": "2012-11-04",                     // optional (default to recur infinitely)
		},
		"state": "disabled",                           // enabled or disabled
		"status":                                       // controlled by Scheduler service
		{
			"lastExecutionTime": "2007-03-01T13:00:00Z",
			"nextExecutionTime": "2007-03-01T14:00:00Z ",
			"executionCount": 3,
											    "failureCount": 0,
												"faultedCount": 0
		},
	}

Como se muestra en el trabajo programado de ejemplo anterior, una definición de trabajo tiene varias partes:

- Hora de inicio ("startTime")  

- Acción ("action"), que incluye la acción del error ("errorAction")

- Periodicidad ("recurrence")

- Situación ("state")

- Estado ("status")

- Directiva de reintentos (“retryPolicy”)

Examinemos cada uno de ellos en detalle:

## startTime

"startTime" es la hora de inicio y permite al que llama especificar un desplazamiento de zona horaria en el cable en [formato ISO-8601](http://en.wikipedia.org/wiki/ISO_8601).

## action y errorAction

"action" es la acción que se invoca en cada repetición y describe un tipo de invocación del servicio. La acción es lo que se ejecutará en la programación especificada. El programador admite acciones HTTP y de cola de almacenamiento.

La acción en el ejemplo anterior es una acción http. A continuación se muestra un ejemplo de una acción de cola de almacenamiento:

	{
			"type": "storageQueue",
			"queueMessage":
			{
				"storageAccount": "myStorageAccount",  // required
				"queueName": "myqueue",                // required
				"sasToken": "TOKEN",                   // required
				"message":                             // required
					"My message body",
			},
	}

"errorAction" es el controlador de errores, la acción que se invoca cuando se produce un error en la acción primaria. Puede usar esta variable para llamar a un extremo de control de errores o para enviar una notificación al usuario. Esto puede usarse para llegar a un extremo secundario en caso de que el principal no esté disponible (por ejemplo, en caso de un desastre en el sitio del extremo) o se puede usar para notificar a un extremo de control de errores. Igual que la acción primaria, la acción de error puede ser una lógica simple o compuesta basada en otras acciones. Para obtener información sobre cómo crear un token de SAS, consulte [Crear y usar una firma de acceso compartido](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## recurrence

La periodicidad tiene varias partes:

- Frecuencia: un minuto, hora, día, semana, mes, año  

- Intervalo: el intervalo de frecuencia definido para la periodicidad

- Programación prescrita: especifique minutos, horas, días de la semana, meses y días de mes para de la periodicidad

- Recuento: número de repeticiones

- Hora de finalización: ningún trabajo se ejecutará después de la hora de finalización especificada

Un trabajo es periódico si tiene un objeto de periodicidad especificado en la definición de JSON. Si se especifican a la vez count y endTime, se respeta la regla de finalización que se produzca primero.

## state

La situación del trabajo es una de cuatro valores: habilitado, deshabilitado, completado o con errores. Puede utilizar PUT o PATCH para los trabajos para actualizarlos al estado habilitado o deshabilitado. Si un trabajo se ha completado o tiene errores, es el estado final el que no se puede actualizar (aunque todavía se pueda eliminar el trabajo). Un ejemplo de la propiedad state es la siguiente:


    	"state": "disabled", // enabled, disabled, completed, or faulted
Los trabajos completados y con errores se eliminan después de 60 días.

## status

Una vez que se inició un trabajo de Programador, se devolverá información sobre el estado actual del trabajo. Este objeto no es configurable por el usuario: lo establece el sistema. Sin embargo, se incluye en el objeto de trabajo (en lugar de un recurso vinculado independiente) para que se pueda obtener fácilmente el estado de un trabajo.

El estado del trabajo incluye el tiempo de la ejecución anterior (si hay alguna), el tiempo de la siguiente ejecución programada (para los trabajos en curso) y el recuento de la ejecución del trabajo.

## retryPolicy

Si se produce un error en un trabajo de Programador, es posible especificar una directiva de reintentos para determinar cuándo y cómo se vuelve a intentar la acción. Esto viene determinado por el objeto **retryType**: está establecido en **none** si no hay ninguna directiva de reintentos, como se mostró anteriormente. Establézcalo en **fixed** si hay una directiva de reintentos.

Para establecer una directiva de reintentos, se pueden especificar dos configuraciones adicionales: un intervalo de reintento (**retryInterval**) y el número de reintentos (**retryCount**).

El intervalo de reintentos, especificado con el objeto **retryInterval**, es el intervalo entre reintentos. Su valor predeterminado es 1 minuto, su valor mínimo es 1 minuto y su valor máximo es de 18 meses. Se define en el formato ISO 8601. Del mismo modo, se especifica el valor del número de reintentos con el objeto **retryCount**; es el número de veces que se realiza un reintento. Su valor predeterminado es 5 y su valor máximo es 20. Tanto **retryInterval** como **retryCount** son opcionales. Obtienen sus valores predeterminados si **retryType** está establecido en **fixed** y no se especifican explícitamente los valores.

## Consulte también

 [¿Qué es Programador?](scheduler-intro.md)
 
 [Introducción al uso de Programador de Azure en el Portal de Azure](scheduler-get-started-portal.md)

 [Planes y facturación en Programador de Azure](scheduler-plans-billing.md)

 [Creación de programaciones complejas y periodicidad avanzada con Programador de Azure](scheduler-advanced-complexity.md)

 [Referencia de API de REST de Programador de Azure](https://msdn.microsoft.com/library/mt629143)

 [Referencia de cmdlets de PowerShell de Programador de Azure](scheduler-powershell-reference.md)

 [Alta disponibilidad y confiabilidad de Programador de Azure](scheduler-high-availability-reliability.md)

 [Límites, valores predeterminados y códigos de error de Programador de Azure](scheduler-limits-defaults-errors.md)

 [Autenticación de salida de Programador de Azure](scheduler-outbound-authentication.md)

<!---HONumber=AcomDC_0128_2016-->