<properties
	pageTitle="Usar el análisis de almacenamiento para recopilar datos de registros y métricas | Microsoft Azure"
	description="El análisis de almacenamiento permite realizar un seguimiento de los datos de métricas para todos los servicios de almacenamiento y recopilar registros de Almacenamiento de blobs, Almacenamiento de colas y Almacenamiento de tablas."
	services="storage"
	documentationCenter=""
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/14/2016"
	ms.author="robinsh"/>

# Análisis de almacenamiento

## Información general

El análisis de almacenamiento de Azure realiza el registro y proporciona datos de métricas para una cuenta de almacenamiento. Puede usar estos datos para hacer un seguimiento de solicitudes, analizar tendencias de uso y diagnosticar problemas con la cuenta de almacenamiento.

Para utilizar el análisis de almacenamiento, debe habilitarlo para cada servicio que desee supervisar. Puede habilitarlo desde el [Portal de Azure](https://portal.azure.com). Para obtener más detalles, consulte [Supervisión de una cuenta de almacenamiento en el Portal de Azure](storage-monitor-storage-account.md). También puede habilitar el análisis de almacenamiento mediante programación a través de la API de REST o la biblioteca de cliente. Use las operaciones [Get Blob Service Properties](https://msdn.microsoft.com/library/hh452239.aspx), [Get Queue Service Properties](https://msdn.microsoft.com/library/hh452243.aspx), [Get Table Service Properties](https://msdn.microsoft.com/library/hh452238.aspx) y [Get File Service Properties](https://msdn.microsoft.com/library/mt427369.aspx) para habilitar el Análisis de almacenamiento para cada servicio.

Los datos agregados se almacenan en un blob conocido (para el registro) y en tablas conocidas (para las métricas), a los que se puede tener acceso mediante las API del servicio Blob y del servicio Tabla.

El análisis de almacenamiento tiene un límite de 20 TB en cuanto a la cantidad de datos almacenados, que es independiente del límite total de la cuenta de almacenamiento. Para obtener más información sobre las directivas de facturación y retención de datos, consulte [Facturación del análisis de almacenamiento](https://msdn.microsoft.com/library/hh360997.aspx). Para obtener más información sobre los límites de las cuentas de almacenamiento, consulte [Objetivos de escalabilidad y rendimiento del almacenamiento de Azure](storage-scalability-targets.md).

Para obtener orientación exhaustiva sobre el uso de análisis de almacenamiento y otras herramientas para identificar, diagnosticar y solucionar problemas relacionados con el Almacenamiento de Azure, consulte [Supervisión, diagnóstico y solución de problemas de Almacenamiento de Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).


## Acerca del registro del análisis de almacenamiento

El análisis de almacenamiento registra información detallada sobre las solicitudes correctas y erróneas realizadas a un servicio de almacenamiento. Esta información se puede utilizar para supervisar solicitudes concretas y para diagnosticar problemas con un servicio de almacenamiento. Las solicitudes se registran en función de la mejor opción.

Solo se crean entradas del registro si hay actividad del servicio de almacenamiento. Por ejemplo, si una cuenta del almacenamiento tiene actividad en el servicio Blob pero no en los servicios Tabla o Cola, solo se crearán los registros que pertenezcan al servicio Blob.

El registro del Análisis de almacenamiento no está disponible para el servicio de archivos de Azure.

### Registrar solicitudes de autenticación

Se registran los siguientes tipos de solicitudes autenticadas:

- Solicitudes correctas

- Solicitudes erróneas, incluyendo errores de tiempo de espera, de limitación, de red, de autorización, etc

- Solicitudes que utilizan una firma de acceso compartido (SAS), incluyendo las solicitudes correctas y las erróneas

- Solicitudes de datos de análisis

Las solicitudes realizadas por el propio análisis de almacenamiento, como la creación o eliminación del registro, no se registran. Puede encontrar una lista completa de los datos registrados en los temas [Operaciones y mensajes de estado registrados por el análisis de almacenamiento](https://msdn.microsoft.com/library/hh343260.aspx) y [Formato del registro del análisis de almacenamiento](https://msdn.microsoft.com/library/hh343259.aspx).

### Registrar solicitudes anónimas
Se registran los siguientes tipos de solicitudes anónimas:

- Solicitudes correctas

- Errores del servidor

- Errores de tiempo de espera para el cliente y el servidor

- Solicitudes GET erróneas con el código de error 304 (Sin modificar)

El resto de solicitudes anónimas erróneas no se registran. Puede encontrar una lista completa de los datos registrados en los temas [Operaciones y mensajes de estado registrados por el análisis de almacenamiento](https://msdn.microsoft.com/library/hh343260.aspx) y [Formato del registro del análisis de almacenamiento](https://msdn.microsoft.com/library/hh343259.aspx).

### Cómo se almacenan los registros
Todos los registros se almacenan en blobs en bloques en un contenedor denominado $logs, que se crea automáticamente cuando se habilita el análisis de almacenamiento para una cuenta de almacenamiento. El contenedor $logs se encuentra en el espacio de nombres del blob de la cuenta de almacenamiento, por ejemplo: `http://<accountname>.blob.core.windows.net/$logs`. Este contenedor no se puede eliminar una vez habilitado el análisis de almacenamiento, aunque su contenido sí se puede eliminar.

>[Azure.NOTE] El contenedor $logs no se muestra cuando se realiza una operación de lista de contenedores, como el método [ListContainers](https://msdn.microsoft.com/library/azure/dd179352.aspx). Se debe tener acceso a él directamente. Por ejemplo, puede utilizar el método [ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx) para acceder a los blobs en el contenedor `$logs`. A medida que se registran las solicitudes, el análisis de almacenamiento cargará los resultados intermedios como bloques. Periódicamente, el análisis de almacenamiento confirmará estos bloques para que estén disponibles como blobs.

Es posible que haya entradas duplicadas en los registros creados durante la misma hora. Puede determinar si un registro es un duplicado comprobando el número **RequestId** y **Operation**.

### Convenciones de nomenclatura de los registros
Cada registro se escribirá en el formato siguiente:

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log

En la tabla siguiente se describe cada atributo del nombre del registro:

| Atributo | Descripción |
|----------------	|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| <service-name> | El nombre del servicio de almacenamiento. Por ejemplo: blob, tabla o cola |
| AAAA | El año de cuatro dígitos para el registro. Por ejemplo: 2011 |
| MM | El mes de dos dígitos para el registro. Por ejemplo: 07. |
| DD | El mes de dos dígitos para el registro. Por ejemplo: 07. |
| hh | La hora de dos dígitos que indica la hora inicial para los registros, en formato UTC de 24 horas. Por ejemplo: 18. |
| mm | El número de dos dígitos que indica el minuto inicial para los registros. Este valor no se admite en la versión actual del análisis de almacenamiento, y su valor será siempre 00. |
| <counter> | Un contador basado en cero con seis dígitos que indica el número de blobs del registro generados para el servicio de almacenamiento en un período de tiempo de una hora. Este contador se inicia en 000000. Por ejemplo: 000001. |

A continuación se muestra un nombre completo de registro de ejemplo que combina los ejemplos anteriores.

    blob/2011/07/31/1800/000001.log

A continuación se muestra un URI de ejemplo que se puede utilizar para obtener acceso al registro anterior.

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log

Cuando se registra una solicitud de almacenamiento, el nombre del registro resultante depende de la hora en la que se completó la operación solicitada. Por ejemplo, si una solicitud GetBlob se completó a las 6:30 p.m. el 31/7/2011, el registro se escribirá con el prefijo siguiente: `blob/2011/07/31/1800/`

### Metadatos del registro
Todos los blobs del registro se almacenan con metadatos que se pueden utilizar para identificar los datos de registro que contiene el blob. La tabla siguiente describe cada atributo de metadatos.

| Atributo | Descripción |
|------------	|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| LogType | Describe si el registro contiene información relacionada con las operaciones de lectura, escritura o eliminación. Este valor puede incluir un tipo o una combinación de los tres, separados por comas. Ejemplo 1: escritura; Ejemplo 2: lectura, escritura; Ejemplo 3: lectura, escritura, eliminación. |
| StartTime | La primera hora de una entrada en el registro, en el formato YYYY-MM-DDThh:mm:ssZ. Por ejemplo: 2011-07-31T18:21:46Z. |
| EndTime | La hora más reciente de una entrada en el registro, en el formato YYYY-MM-DDThh:mm:ssZ. Por ejemplo: 2011-07-31T18:22:09Z. |
| LogVersion | La versión del formato del registro. Actualmente, el único valor admitido es: 1.0. |

La lista siguiente muestra metadatos completos de ejemplo utilizando los ejemplos anteriores.

- LogType=escritura

- StartTime=2011-07-31T18:21:46Z

- EndTime=2011-07-31T18:22:09Z

- LogVersion=1.0

### Obtener acceso a los datos de registro

Se puede tener acceso a todos los datos del contenedor `$logs` utilizando las API del servicio BLOB, incluidas las API de .NET proporcionadas por la biblioteca administrada de Azure. El administrador de la cuenta de almacenamiento puede leer y eliminar registros, pero no puede crearlos ni actualizarlos. Los metadatos y el nombre del registro se pueden utilizar al consultar un registro. Es posible que los registros de una hora determinada aparezcan desordenados, pero los metadatos siempre especifican el intervalo de tiempo de las entradas de registro en un registro. Por consiguiente, se puede utilizar una combinación de nombres y metadatos de registro al buscar un registro concreto.

## Acerca de las métricas del análisis de almacenamiento

El análisis de almacenamiento puede almacenar métricas que incluyen estadísticas acumuladas de las transacciones y datos de capacidad sobre las solicitudes realizadas a un servicio de almacenamiento. Las transacciones se notifican tanto en el nivel de operación de API como en el nivel de servicio de almacenamiento, y la capacidad se notifica en el nivel de servicio de almacenamiento. Los datos de las métricas se pueden utilizar para analizar el uso del servicio de almacenamiento, diagnosticar problemas con las solicitudes realizadas en el mismo y mejorar el rendimiento de las aplicaciones que utilizan un servicio.

Para utilizar el análisis de almacenamiento, debe habilitarlo para cada servicio que desee supervisar. Puede habilitarlo desde el [Portal de Azure](https://portal.azure.com). Para obtener más detalles, consulte [Supervisión de una cuenta de almacenamiento en el Portal de Azure](storage-monitor-storage-account.md). También puede habilitar el análisis de almacenamiento mediante programación a través de la API de REST o la biblioteca de cliente. Use las operaciones **Get Service Properties** para habilitar el análisis de almacenamiento de cada servicio.

### Métricas de transacciones

Se registra un conjunto sólido de datos a intervalos de cada hora o de cada minuto para cada servicio de almacenamiento y operación de API que se ha solicitado, incluidos entradas/salidas, disponibilidad, errores y porcentajes de solicitudes por categorías. Puede ver una lista completa de los detalles de transacción en el tema [Esquema de las tablas de métricas del análisis de almacenamiento](https://msdn.microsoft.com/library/hh343264.aspx).

Los datos de las transacciones se registran en dos niveles: el nivel de servicio y el nivel de operación de API. En el nivel de servicio, las estadísticas que resumen todas las operaciones de API solicitadas se escriben en una entidad de la tabla cada hora, aunque no se haya realizado ninguna solicitud al servicio. En el nivel de operación de API, las estadísticas se escriben únicamente en una entidad si la operación se solicitó durante esa hora.

Por ejemplo, si realiza una operación **GetBlob** en el servicio Blob, las métricas de análisis de almacenamiento registrarán la solicitud y la incluirán en los datos agregados tanto para el servicio BLOB como para la operación **GetBlob**. Sin embargo, si no se solicita ninguna operación **GetBlob** durante esa hora, no escribirá ninguna entidad en la `$MetricsTransactionsBlob` para dicha operación.

Las métricas de transacciones se registran para las solicitudes del usuario y para las solicitudes realizadas por el análisis de almacenamiento. Por ejemplo, se registran las solicitudes realizadas por el análisis de almacenamiento para escribir registros y entidades de tabla. Para obtener más información sobre cómo se facturan estas solicitudes, consulte [Facturación y análisis de almacenamiento](https://msdn.microsoft.com/library/hh360997.aspx).

### Métricas de capacidad

>[AZURE.NOTE] Actualmente, las métricas de capacidad solo están disponibles para el servicio Blob. Las métricas de capacidad para el servicio Tabla y el servicio Cola estarán disponibles en versiones futuras del análisis de almacenamiento.

Los datos de capacidad se registran diariamente para el servicio Blob de una cuenta de almacenamiento, y se escriben dos entidades de tabla. Una entidad proporciona estadísticas para los datos de usuario y la otra proporciona estadísticas sobre el contenedor de blob `$logs` utilizado por el análisis de almacenamiento. La tabla `$MetricsCapacityBlob` incluye las estadísticas siguientes:

- **Capacity**: la cantidad de almacenamiento utilizado por el servicio BLOB de la cuenta de almacenamiento, en bytes.

- **ContainerCount**: el número de contenedores de blobs del servicio BLOB de la cuenta de almacenamiento.

- **ObjectCount**: el número de blobs en bloque o en páginas confirmados y sin confirmar del servicio BLOB de la cuenta de almacenamiento.

Para obtener más información acerca de las métricas de capacidad, vea el [Esquema de las tablas de métricas del análisis de almacenamiento](https://msdn.microsoft.com/library/hh343264.aspx).

### Cómo se almacenan las métricas

Todos los datos de métricas para cada uno de los servicios de almacenamiento se almacenan en tres tablas reservadas para ese servicio: una tabla para la información sobre transacciones, otra tabla para la información sobre las transacciones por minuto y una última tabla para la información sobre capacidad. La información sobre transacciones y transacciones por minuto consta de datos de solicitudes y respuestas, y la información sobre capacidad consta de datos de uso del almacenamiento. Se puede acceder a las métricas por hora y por minuto, y a la capacidad del servicio Blob de una cuenta de almacenamiento, en tablas cuyos nombres se describen en la tabla siguiente.

| Nivel de métricas | Nombres de tabla | Versiones compatibles |
|------------------------------------	|-----------------------------------------------------------------------------------------------------------------------------	|----------------------------------------------------------------------------------------------------------------------------------------------	|
| Métricas por horas, ubicación principal | $MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue | Versiones anteriores a 2013-08-15 solamente. Si bien estos nombres todavía se admiten, se recomienda que empiece a usar las tablas que se muestran a continuación. |
| Métricas por horas, ubicación principal | $MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue | Todas las versiones, incluida 2013-08-15. |
| Métricas por minutos, ubicación principal | $MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue | Todas las versiones, incluida 2013-08-15. |
| Métricas por horas, ubicación secundaria | $MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue | Todas las versiones, incluida 2013-08-15. Debe estar habilitada la replicación con redundancia geográfica con acceso de lectura. |
| Métricas por minutos, ubicación secundaria | $MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue | Todas las versiones, incluida 2013-08-15. Debe estar habilitada la replicación con redundancia geográfica con acceso de lectura. |
| Capacidad (solo servicio Blob) | $MetricsCapacityBlob | Todas las versiones, incluida 2013-08-15. |


Estas tablas se crean automáticamente cuando se habilita el análisis de almacenamiento para una cuenta de almacenamiento. Se tiene acceso a ellas a través del espacio de nombres de la cuenta de almacenamiento, por ejemplo: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`

### Obtener acceso a los datos de las métricas

Se puede tener acceso a todos los datos de las tablas de las métricas utilizando las API del servicio Tabla, incluidas las API de .NET proporcionadas por la biblioteca administrada de Azure. El administrador de la cuenta de almacenamiento puede leer y eliminar entidades de tabla, pero no puede crearlas ni actualizarlas.

## Facturación para análisis de almacenamiento

El análisis de almacenamiento lo habilita el propietario de la cuenta de almacenamiento; no está habilitado de forma predeterminada. Todos los datos de métricas los escriben los servicios de una cuenta de almacenamiento. Como resultado, cada una de las operaciones de escritura realizadas por el análisis de almacenamiento es facturable. También lo es la cantidad de almacenamiento utilizado por los datos de métricas.

Son facturables las acciones siguientes realizadas por el análisis de almacenamiento:

- Solicitudes para crear blobs para el registro 

- Solicitudes para crear entidades de tabla para las métricas

Si ha configurado una directiva de retención de datos, no se le cobrarán las transacciones de eliminación cuando el análisis de almacenamiento elimine los antiguos datos de métricas y de registro. Sin embargo, las transacciones de eliminación desde un cliente sí son facturables. Para obtener más información acerca de las directivas de retención, consulte [Establecer una directiva de retención de datos de análisis de almacenamiento](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### Descripción de las solicitudes facturables

Las solicitudes realizadas al servicio de almacenamiento de una cuenta pueden ser facturables o no facturables. El análisis de almacenamiento registra cada solicitud realizada a un servicio, incluyendo un mensaje de estado que indica cómo se administró la solicitud. De igual forma, el análisis de almacenamiento guarda las métricas para un servicio y para las operaciones de la API de dicho servicio, incluidos los porcentajes y el recuento de algunos mensajes de estado. Todas estas características pueden ayudarle a analizar las solicitudes facturables, a llevar a cabo mejoras en la aplicación y a diagnosticar problemas en las solicitudes a los servicios. Para obtener más información sobre la facturación, consulte [Descripción de la facturación del almacenamiento de Azure: ancho de banda, transacciones y capacidad](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Al examinar los datos de análisis de almacenamiento, puede usar las tablas del tema [Operaciones y mensajes de estado registrados por el análisis de almacenamiento](https://msdn.microsoft.com/library/azure/hh343260.aspx) para determinar qué solicitudes son facturables. De esta manera, podrá comparar los datos de métricas y de registro con los mensajes de estado para ver si se le cobró por una solicitud determinada. También puede usar las tablas del tema anterior para investigar la disponibilidad de un servicio de almacenamiento o de una operación de API determinada.

## Pasos siguientes

### Configuración del análisis de almacenamiento
- [Supervisión de una cuenta de almacenamiento en el Portal de Azure](storage-monitor-storage-account.md)
- [Habilitar y configurar el análisis de almacenamiento](https://msdn.microsoft.com/library/hh360996.aspx)

### Registro del análisis de almacenamiento  
- [Acerca del registro del análisis de almacenamiento](https://msdn.microsoft.com/library/hh343262.aspx)
- [Formato del registro del análisis de almacenamiento](https://msdn.microsoft.com/library/hh343259.aspx)
- [Operaciones y mensajes de estado registrados por el análisis de almacenamiento](https://msdn.microsoft.com/library/hh343260.aspx)

### Métricas del análisis de almacenamiento
- [Acerca de las métricas del análisis de almacenamiento](https://msdn.microsoft.com/library/hh343258.aspx)
- [Esquema de las tablas de métricas del análisis de almacenamiento](https://msdn.microsoft.com/library/hh343264.aspx)
- [Operaciones y mensajes de estado registrados por el análisis de almacenamiento](https://msdn.microsoft.com/library/hh343260.aspx)  

<!---HONumber=AcomDC_0224_2016-->