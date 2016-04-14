<properties
	pageTitle="Salidas de transformación de datos: Opciones de almacenamiento y análisis | Microsoft Azure"
	description="Obtenga información sobre salidas de transformación de datos de Análisis de transmisiones de destino para opciones de almacenamiento de datos Además, puede usar Power BI para los resultados del análisis."
	keywords="transformación de datos, resultados del análisis, opciones de almacenamiento de datos"
	services="stream-analytics,documentdb,sql-database,event-hubs,service-bus,storage"
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
	ms.date="03/02/2016"
	ms.author="jeffstok"/>

# Salidas de transformación de datos de Análisis de transmisiones de destino para herramientas de análisis y opciones de almacenamiento de datos

Al crear un trabajo de Análisis de transmisiones, tenga en cuenta cómo se consumirá la salida del trabajo. ¿Cómo se van a ver los resultados del trabajo de Análisis de transmisiones? ¿Qué herramientas se usan para mostrar los resultados del análisis de datos? ¿Es una opción de almacenamiento de datos un requisito?

Para habilitar diversos patrones de aplicación, Análisis de transmisiones de Azure ofrece distintas opciones para el almacenamiento y la visualización de los resultados del trabajo. Esto facilita la visualización de la salida del trabajo y proporciona flexibilidad en el consumo y almacenamiento de la salida del trabajo para el almacenamiento de datos y otros fines. Todos los elementos de salida que se configuren en el trabajo deben existir antes de que se inicie el trabajo y los eventos empiecen a fluir. Por ejemplo, si usa el almacenamiento de blobs como salida, el trabajo no creará una cuenta de almacenamiento automáticamente. Debe crearla el usuario antes de que se inicie el trabajo ASA.


## Base de datos SQL ##

Puede usarse [Base de datos SQL de Azure](https://azure.microsoft.com/services/sql-database/) como salida de datos que son relacionales por naturaleza o de aplicaciones que dependen del contenido hospedado en una base de datos relacional. Los trabajos de Análisis de transmisiones se escribirán en una tabla existente en una Base de datos SQL de Azure. Tenga en cuenta que el esquema de tabla debe coincidir exactamente con los campos y los tipos de salida del trabajo. En la tabla siguiente se enumeran los nombres de propiedad y su descripción para crear una salida de Base de datos SQL.

| Nombre de propiedad | Descripción |
|---------------|-------------|
| Alias de salida | Es el nombre descriptivo usado en las consultas para dirigir la salida de la consulta a esta base de datos. |
| Base de datos | El nombre de la base de datos a donde está enviando la salida |
| Nombre del servidor | El nombre del servidor Base de datos SQL de Azure |
| Nombre de usuario | El nombre del usuario con permiso para escribir en la base de datos |
| Password | La contraseña para conectarse a la base de datos |
| Tabla | El nombre de la tabla donde se escribirá la salida El nombre de tabla distingue mayúsculas de minúsculas y el esquema de esta tabla debe coincidir exactamente con el número de campos y tipos que va a generar la salida del trabajo. |

## Almacenamiento de blobs ##

Almacenamiento de blobs ofrece una solución rentable y escalable para almacenar grandes cantidades de datos no estructurados en la nube. Para obtener una introducción sobre el almacenamiento de blobs de Azure y su uso, consulte la documentación en [Uso de blobs](../storage/storage-dotnet-how-to-use-blobs.md).

En la tabla siguiente se enumeran los nombres de propiedad y su descripción para crear una salida de blob.

<table>
<tbody>
<tr>
<td>NOMBRE DE PROPIEDAD</td>
<td>DESCRIPCIÓN</td>
</tr>
<tr>
<td>Alias de salida</td>
<td>Es el nombre descriptivo usado en las consultas para dirigir la salida de la consulta a este almacenamiento de blobs.</td>
</tr>
<tr>
<td>Cuenta de almacenamiento</td>
<td>El nombre de la cuenta de almacenamiento a donde está enviando la salida</td>
</tr>
<tr>
<td>Clave de cuenta de almacenamiento</td>
<td>La clave secreta asociada con la cuenta de almacenamiento.</td>
</tr>
<tr>
<td>Contenedor de almacenamiento</td>
<td>Los contenedores proporcionan una agrupación lógica de los blobs almacenados en el servicio BLOB de Microsoft Azure. Cuando carga un blob al servicio BLOB, debe especificar un contenedor para ese blob.</td>
</tr>
<tr>
<td>Patrón del prefijo de la ruta de acceso [opcional]</td>
<td>La ruta de acceso de archivo para escribir los blobs dentro del contenedor especificado.<BR>Dentro de la ruta de acceso, puede elegir usar una o más instancias de las dos variables siguientes para especificar la frecuencia con la que se escriben los blobs:<BR>{date}, {time}<BR>Ejemplo 1: cluster1/logs/{date}/{time}<BR>Ejemplo 2: cluster1/logs/{date}</td>
</tr>
<tr>
<td>Formato de fecha [opcional]</td>
<td>Si el token de fecha se usa en la ruta de acceso de prefijo, puede seleccionar el formato de fecha en el que se organizan los archivos. Ejemplo: AAAA/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [opcional]</td>
<td>Si el token de hora se usa en la ruta de acceso de prefijo, puede seleccionar el formato de hora en el que se organizan los archivos. Actualmente, el único valor admitido es HH.</td>
</tr>
<tr>
<td>Formato de serialización de eventos</td>
<td>Formato de serialización para los datos de salida. Se admiten JSON, CSV y Avro.</td>
</tr>
<tr>
<td>Codificación</td>
<td>Si el formato CSV o JSON, debe especificarse una codificación. Por el momento, UTF-8 es el único formato de codificación compatible.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>Solo se aplica para la serialización de CSV. Análisis de transmisiones admite un número de delimitadores comunes para la serialización de datos CSV. Los valores admitidos son la coma, punto y coma, espacio, tabulador y barra vertical.</td>
</tr>
<tr>
<td>Formato</td>
<td>Solo se aplica para la serialización de JSON. Separado por líneas especifica que la salida se formateará de tal forma que cada objeto JSON esté separado por una línea nueva. Matriz especifica que la salida se formateará como una matriz de objetos JSON.</td>
</tr>
</tbody>
</table>

## Centro de eventos

[Centros de eventos](https://azure.microsoft.com/services/event-hubs/) es un servicio de introducción de eventos de publicación-suscripción altamente escalable. Puede recopilar millones de eventos por segundo. Un uso del Centro de eventos como salida es cuando la salida de un trabajo de Análisis de transmisiones es la entrada de otro trabajo de streaming.

Hay unos cuantos parámetros que son necesarios para configurar los flujos de datos del Centro de eventos como salida.

| Nombre de propiedad | Descripción |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de salida | Es el nombre descriptivo usado en las consultas para dirigir la salida de la consulta a este Centro de eventos. |
| Espacio de nombres de Bus de servicio | Un espacio de nombres del bus de servicio es un contenedor para un conjunto de entidades de mensajería. Al crear un nuevo Centro de eventos, también se crea un espacio de nombres del Bus de servicio. |
| Centro de eventos | El nombre de la salida del Centro de eventos |
| Nombre de la directiva del centro de eventos | La directiva de acceso compartido, que se puede crear en la pestaña Configuración de Centro de eventos. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. |
| Clave de la directiva de centro de eventos | La clave de acceso compartido usada para autenticar el acceso al espacio de nombres del Bus de servicio. |
| Columna Clave de partición [opcional] | Esta columna contiene la clave de partición para la salida del Centro de eventos. |
| Formato de serialización de eventos | Formato de serialización para los datos de salida. Se admiten JSON, CSV y Avro. |
| Codificación | Por el momento, UTF-8 es el único formato de codificación compatible para CSV y JSON. |
| Delimitador | Solo se aplica para la serialización de CSV. Análisis de transmisiones admite un número de delimitadores comunes para la serialización de datos en formato CSV. Los valores admitidos son la coma, punto y coma, espacio, tabulador y barra vertical. |
| Formato | Solo se aplica para el tipo JSON. Separado por líneas especifica que la salida se formateará de tal forma que cada objeto JSON esté separado por una línea nueva. Matriz especifica que la salida se formateará como una matriz de objetos JSON. |
## Power BI

Puede usarse [Power BI](https://powerbi.microsoft.com/) como salida para un trabajo de Análisis de transmisiones a fin de ofrecer una amplia experiencia de visualización de los resultados del análisis. Esta capacidad puede usarse con paneles operativos, generación de informes e informes basados en métricas.

> [AZURE.NOTE] En este momento, la creación y la configuración de salidas de Power BI solo se admiten en el Portal de Azure clásico.

### Autorización de una cuenta de Power BI

1.	Cuando se selecciona Power BI como salida en el Portal de administración de Azure, se le pedirá que autorice a un usuario de Power BI existente o que cree una nueva cuenta de Power BI.  

    ![Autorización de un usuario de Power BI](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)

2.	Cree una cuenta nueva si no aún no tiene una y después haga clic en Autorizar ahora. Aparecerá una pantalla similar a la siguiente.

    ![Cuenta de Power BI de Azure](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)

3.	En este paso, proporcione la cuenta profesional o educativa para la autorización de la salida de Power BI. Si aún no se ha registrado en Power BI, elija Registrarse ahora. La cuenta profesional o educativa que use para Power BI podría ser diferente de la cuenta de suscripción de Azure en la que ha iniciado sesión.

### Configuración de las propiedades de salida de Power BI

Cuando la cuenta de Power BI esté autenticada, puede configurar las propiedades de la salida de Power BI. La tabla siguiente muestra la lista de nombres de propiedad y su descripción para configurar la salida de Power BI.

| Nombre de propiedad | Descripción |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de salida | Es el nombre descriptivo usado en las consultas para dirigir la salida de la consulta a esta salida de Power BI. |
| Nombre del conjunto de datos | Proporcione un nombre del conjunto de datos que desea que tenga la salida de Power BI. |
| Nombre de la tabla | Proporcione un nombre de tabla en el conjunto de datos de la salida de Power BI. Actualmente, la salida de Power BI de trabajos de Análisis de transmisiones solo puede tener una tabla en un conjunto de datos. |
| Nombre de grupo | Para habilitar el uso compartido de datos con otros usuarios de Power BI, escriba los datos en grupos. Puede seleccionar los grupos dentro de su cuenta de Power BI o elegir "Mi área de trabajo" si no desea escribir en un grupo. Actualizar un grupo existente requiere renovar la autenticación de Power BI. |

Para visualizar un tutorial sobre la configuración de una salida de Power BI y un panel, consulte el artículo [Análisis de transmisiones de Azure y Power BI](stream-analytics-power-bi-dashboard.md).

> [AZURE.NOTE] No cree explícitamente el conjunto de datos y la tabla en el panel de Power BI. El conjunto de datos y la tabla se rellenarán automáticamente cuando se inicie el trabajo y este inicie el bombeo de la salida en Power BI. Observe que si la consulta de trabajo no genera ningún resultado, el conjunto de datos y la tabla no se creará. Tenga en cuenta asimismo que si Power BI ya cuenta con un conjunto de datos y una tabla con el mismo nombre que el proporcionado en este trabajo de Análisis de transmisiones, se sobrescribirán los datos existentes.

### Renovación de la autorización de Power BI

Hay una limitación temporal en la que el token de autenticación debe actualizarse manualmente cada 90 días para todos los trabajos con salida de Power BI. También necesitará volver a autenticar la cuenta de Power BI si su contraseña ha cambiado desde que se creó o autenticó por última vez su trabajo. Un síntoma de este problema es la ausencia de salidas de trabajos y un "error de autenticación de usuario" en los registros de operaciones:

  ![Error de token de actualización de Power BI](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)

Para resolver este problema, detenga su trabajo en ejecución y vaya a la salida de Power BI. Haga clic en el vínculo "Renovar autorización" y reinicie el trabajo desde la hora en que se detuvo por última vez para evitar la pérdida de datos.

  ![Autorización de renovación de Power BI](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)

## Almacenamiento de tablas

El [almacenamiento de tablas de Azure](../storage/storage-introduction.md) ofrece un tipo de almacenamiento de alta disponibilidad y escalabilidad masiva, de forma que las aplicaciones pueden escalarse automáticamente para ajustarse a la demanda de los usuarios. Almacenamiento de tablas es un almacén de claves/atributos NoSQL de Microsoft que puede aprovechar para datos estructurados con menos restricciones en el esquema. El almacenamiento de tablas de Azure puede usarse para almacenar datos con de persistencia y recuperación eficaz.

En la tabla siguiente se enumeran los nombres de propiedad y su descripción para crear una salida de tabla.

| Nombre de propiedad | Descripción |
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de salida | Es el nombre descriptivo usado en las consultas para dirigir la salida de la consulta a este almacenamiento de tablas. |
| Cuenta de almacenamiento | El nombre de la cuenta de almacenamiento a donde está enviando la salida |
| Clave de cuenta de almacenamiento | La clave de acceso asociada con la cuenta de almacenamiento. |
| Nombre de la tabla | El nombre de la tabla. La tabla se creará si no existe. |
| Partition Key | El nombre de la columna de salida que contiene la clave de partición. La clave de partición es un identificador único de la partición dentro de una tabla determinada que constituye la primera parte de la clave principal de la entidad. Es un valor de cadena que puede tener un tamaño de hasta 1 KB. |
| Row Key | El nombre de la columna de salida que contiene la clave de la fila. La clave de fila es un identificador único de una identidad dentro de una partición determinada. Forma la segunda parte de la clave principal de la entidad. La clave de fila es un valor de cadena que puede tener un tamaño de hasta 1 KB. |
| Tamaño de lote | El número de registros para una operación por lotes. Normalmente, el valor predeterminado es suficiente para la mayoría de los trabajos. Consulte [Especificaciones de la operación por lotes de tablas](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) para obtener más información sobre la modificación de esta configuración. |

## Colas del Bus de servicio

Las [colas de Bus de servicio](https://msdn.microsoft.com/library/azure/hh367516.aspx) ofrecen una entrega de mensajes según el modelo El primero en entrar es el primero en salir (FIFO) a uno o más consumidores de la competencia. Normalmente, se espera que los receptores reciban y procesen los mensajes en el orden temporal en el que se agregaron a la cola y solo un destinatario del mensaje recibe y procesa cada uno de los mensajes.

En la tabla siguiente se enumeran los nombres de propiedad y su descripción para crear una salida de cola.

| Nombre de propiedad | Descripción |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de salida | Es el nombre descriptivo usado en las consultas para dirigir la salida de la consulta a esta cola del Bus de servicio. |
| Espacio de nombres de Bus de servicio | Un espacio de nombres del bus de servicio es un contenedor para un conjunto de entidades de mensajería. |
| Nombre de la cola | El nombre de la cola del Bus de servicio |
| Nombre de la directiva de colas | Al crear una cola, también puede crear directivas de acceso compartidas en la pestaña Configurar cola. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. |
| Clave de la directiva de colas | La clave de acceso compartido usada para autenticar el acceso al espacio de nombres del Bus de servicio. |
| Formato de serialización de eventos | Formato de serialización para los datos de salida. Se admiten JSON, CSV y Avro. |
| Codificación | Por el momento, UTF-8 es el único formato de codificación compatible para CSV y JSON. |
| Delimitador | Solo se aplica para la serialización de CSV. Análisis de transmisiones admite un número de delimitadores comunes para la serialización de datos en formato CSV. Los valores admitidos son la coma, punto y coma, espacio, tabulador y barra vertical. |
| Formato | Solo se aplica para el tipo JSON. Separado por líneas especifica que la salida se formateará de tal forma que cada objeto JSON esté separado por una línea nueva. Matriz especifica que la salida se formateará como una matriz de objetos JSON. |

## Temas de Bus de servicio

Mientras que las colas de Bus de servicio proporcionan un método de comunicación individual del remitente al receptor, los [temas de Bus de servicio](https://msdn.microsoft.com/library/azure/hh367516.aspx) proporcionan una forma de comunicación de uno a muchos.

En la tabla siguiente se enumeran los nombres de propiedad y su descripción para crear una salida de tabla.

| Nombre de propiedad | Descripción |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de salida | Es el nombre descriptivo usado en las consultas para dirigir la salida de la consulta a este Tema del Bus de servicio. |
| Espacio de nombres de Bus de servicio | Un espacio de nombres del bus de servicio es un contenedor para un conjunto de entidades de mensajería. Al crear un nuevo Centro de eventos, también se crea un espacio de nombres del Bus de servicio. |
| Nombre del tema | Los temas son entidades de mensajería, similares a los Centros de eventos y colas. Están diseñados para recopilar secuencias de eventos de una serie de diferentes dispositivos y servicios. Cuando se crea un tema, se proporciona también un nombre específico. Los mensajes enviados a un Tema no estarán disponibles a menos que se cree una suscripción, así que asegúrese de que hay una o más suscripciones en el tema. |
| Nombre de la directiva de temas | Al crear una Tema, también puede crear directivas de acceso compartidas en la pestaña Configurar tema. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. |
| Clave de la directiva de temas | La clave de acceso compartido usada para autenticar el acceso al espacio de nombres del Bus de servicio. |
| Formato de serialización de eventos | Formato de serialización para los datos de salida. Se admiten JSON, CSV y Avro. |
| Codificación | Si el formato CSV o JSON, debe especificarse una codificación. Por el momento, UTF-8 es el único formato de codificación compatible. |
| Delimitador | Solo se aplica para la serialización de CSV. Análisis de transmisiones admite un número de delimitadores comunes para la serialización de datos en formato CSV. Los valores admitidos son la coma, punto y coma, espacio, tabulador y barra vertical. |

## DocumentDB

[DocumentDB de Azure](https://azure.microsoft.com/services/documentdb/) es un servicio de base de datos de documentos NoSQL totalmente administrado que ofrece consultas y transacciones a través de datos sin esquema, rendimiento predecible y confiable y desarrollo rápido.

En la tabla siguiente se enumeran los nombres de propiedad y su descripción para crear una salida de DocumentDB.

<table>
<tbody>
<tr>
<td>NOMBRE DE PROPIEDAD</td>
<td>DESCRIPCIÓN</td>
</tr>
<tr>
<td>Nombre de cuenta</td>
<td>Nombre de la cuenta de DocumentDB. Esto también puede ser el extremo de la cuenta.</td>
</tr>
<tr>
<td>Clave de cuenta</td>
<td>Clave de acceso compartido para la cuenta de DocumentDB.</td>
</tr>
<tr>
<td>Base de datos</td>
<td>Nombre de la base de datos de DocumentDB.</td>
</tr>
<tr>
<td>Patrón de nombre de colección:</td>
<td>Patrón de nombre de colección para las colecciones que se usará. El formato de nombre de la colección se pueden construir con el token opcional {partition}, donde las particiones comienzan desde 0.<BR>Por ejemplo, Las siguientes son entradas válidas:<BR>MyCollection{partition}<BR>MyCollection<BR>Tenga en cuenta que las colecciones deben existir antes de que el trabajo de Análisis de transmisiones se inicie y no se crearán automáticamente.</td>
</tr>
<tr>
<td>Partition Key</td>
<td>Nombre del campo en los eventos de salida que se utiliza para especificar la clave de partición de salida entre colecciones.</td>
</tr>
<tr>
<td>Id. de documento</td>
<td>Nombre del campo de los eventos de salida utilizado para especificar la clave principal en la que se basan las operaciones de inserción o actualización.</td>
</tr>
</tbody>
</table>


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

<!---HONumber=AcomDC_0302_2016-->