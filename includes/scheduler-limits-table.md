En la tabla siguiente se describe cada uno de los principales aceleradores, valores predeterminados, límites y cuotas de Programador de Azure.

|Recurso|Descripción de límites|
|---|---|
|**Tamaño del trabajo**|El tamaño máximo del trabajo es 16 K. Si una solicitud PUT o PATCH produce un trabajo mayor que estos límites, se devuelve un código de estado 400 Solicitud incorrecta.|
|**Tamaño de la dirección URL de la solicitud**|El tamaño máximo de la dirección URL de la solicitud es de 2048 caracteres.|
|**Tamaño del encabezado de agregado**|El tamaño máximo del encabezado de agregado es de 4096 caracteres.|
|**Recuento de encabezados**|El recuento máximo de encabezados es de 50 encabezados.|
|**Tamaño del cuerpo**|El tamaño máximo del cuerpo es de 8192 caracteres.|
|**Intervalo de periodicidad**|El intervalo máximo de periodicidad es de 18 meses.|
|**Tiempo hasta la hora de inicio**|El "tiempo hasta la hora de inicio" máximo es de 18 meses.|
|**Historial de trabajos**|El tamaño máximo del cuerpo de la respuesta que se almacena en el historial de trabajos es de 2048 bytes.|
|**Frecuencia**|La cuota máxima de frecuencia predeterminada es de 1 hora en una colección de trabajos gratuita y de 1 minuto en una colección de trabajos estándar. La frecuencia máxima se puede configurar en una colección de trabajos para que sea inferior al máximo. Todos los trabajos de la colección de trabajos se limitan al valor establecido en la colección de trabajos. Si intenta crear un trabajo con una frecuencia mayor que la frecuencia máxima en la colección de trabajos, la solicitud generará un error con un código de estado 409 Conflicto.|
|**Trabajos**|La cuota máxima de trabajos predeterminada es de 5 trabajos en una colección de trabajos gratuita y de 50 trabajos en una colección de trabajos estándar. El número máximo de trabajos se puede configurar en una colección de trabajos. Todos los trabajos de la colección de trabajos se limitan al valor establecido en la colección de trabajos. Si intenta crear un número de trabajos superior a la cuota máxima de trabajos, la solicitud genera un error con un código de estado 409 Conflicto.|
|**Retención de historial de trabajos**|El historial de trabajos se conserva hasta 2 meses o hasta las últimas 1000 ejecuciones.|
|**Retención de trabajos completados y con errores**|Los trabajos completados y con errores se conservan durante 60 días.|
|**Tiempo de espera**|Hay un tiempo de espera de solicitud estático (no configurable) de 30 segundos para las acciones de HTTP. Para operaciones de ejecución más larga, siga los protocolos HTTP asincrónicos; por ejemplo, devolver inmediatamente un 202 pero continuar trabajando en segundo plano.|

<!---HONumber=Oct15_HO3-->