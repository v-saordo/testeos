<properties
	pageTitle="Escalamiento de servicios móviles respaldados por Base de datos SQL | Microsoft Azure"
	description="Obtenga información acerca de cómo diagnosticar y corregir problemas de escalabilidad en los servicios móviles con copia de seguridad por la base de datos SQL"
	services="mobile-services"
	documentationCenter=""
	authors="lindydonna"
	manager="dwrede"
	editor="mollybos"/>


<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/23/2016" 
	ms.author="donnam;ricksal"/>

# Escalamiento de servicios móviles respaldados por Base de datos SQL de Azure

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


Los Servicios móviles de Azure facilitan la puesta en marcha y creación de una aplicación que se conecta a un back-end hospedado en la nube que almacena datos en una base de datos SQL. A medida que la aplicación crece, el escalamiento de las instancias de servicio es tan sencillo como ajustar la configuración de escalamiento en el portal para agregar más recursos informáticos y de red. Sin embargo, el escalamiento de la base de datos SQL que respalda el servicio requiere cierto planeamiento y supervisión proactivos, ya que el servicio recibe más carga. Este documento le guiará a través de un conjunto de procedimientos recomendados para garantizar un excelente rendimiento continuado de los servicios móviles respaldados por SQL.

Este tema le guiará a través de estas secciones básicas:

1. [Diagnóstico de problemas](#Diagnosing)
2. [Indexación](#Indexing)
3. [Diseño de esquemas](#Schema)
4. [Diseño de consultas](#Query)
5. [Arquitectura del servicio](#Architecture)
6. [Solución avanzada de problemas](#Advanced)

<a name="Diagnosing"></a>
## Diagnóstico de problemas

Si sospecha que el servicio móvil tiene problemas cuando está sometido a carga, lo primero que tiene que comprobar es la pestaña **Panel del servicio** en el [Portal de Azure clásico]. A continuación se indican algunas de las cosas que puede comprobar aquí:

- Que los medidores de uso, incluidos los medidores **Llamadas a API** y **Dispositivos activos**, no superan la cuota
- Que el estado de **supervisión de extremos** indica que el servicio está activo (solamente disponible si el servicio utiliza el nivel estándar y la supervisión de extremos está activa)

Si cualquiera de los supuestos anteriores no se cumple, es recomendable ajustar la configuración de escalado en la pestaña *Escalar*. Si esta acción no resuelve el problema, puede investigar si Base de datos SQL de Azure puede ser la causa del problema. Las próximas secciones cubren algunos enfoques diferentes para diagnosticar qué puede estar funcionando mal.

### Elección del nivel de base de datos SQL correcto

Es importante comprender los diferentes niveles de base de datos que tiene a su disposición para garantizar que ha elegido el nivel correcto dadas las necesidades de su aplicación. Base de datos SQL Azure ofrece tres niveles de servicio:

- Básica
- Standard
- Premium


A continuación se indican algunas recomendaciones a la hora de seleccionar el nivel correcto de la base de datos:

- **Básico**: usar en el tiempo de desarrollo o para pequeños servicios de producción donde espera hacer las consultas de una en una a la base de datos.
- **Estándar**: usar para servicios de producción donde espera hacer varias consultas de base de datos simultáneamente.
- **Premium**: usar para grandes servicios de producción de escala con muchas consultas simultáneas, altas cargas de pico y baja latencia esperada para cada solicitud.

Para obtener más información sobre cuándo usar cada nivel, consulte [Razones para usar los nuevos niveles de servicio]

### Análisis de métricas de base de datos

Cuando se haya familiarizado con los diferentes niveles de base de datos, puede explorar las métricas de rendimiento de base de datos para ayudarnos a razonar el escalamiento dentro de los niveles y entre estos.

1. Inicie el [Portal de Azure clásico].
2. En la pestaña Servicios móviles, seleccione el servicio con el que desea trabajar.
3. Seleccione la pestaña **Configurar**.
4. Seleccione un nombre en **Base de datos SQL** en la sección **Configuración de base de datos**. Esta acción le llevará a la pestaña Base de datos SQL de Azure en el portal.
5. Navegue a la pestaña **Supervisar**.
6. Asegúrese de que se muestran las métricas correspondientes mediante el botón **Agregar métricas**. Entre las métricas se incluyen las siguientes:
    - *Porcentaje de CPU* (disponible solo en los niveles Básico, Estándar y Premium)

    - *Porcentaje de CPU* (disponible solo en los niveles Básico, Estándar y Premium)
    - *Porcentaje de E/S de registro* (disponible solo en los niveles Básico, Estándar y Premium)
    - *Almacenamiento* 
7. Inspeccione las métricas en la ventana de tiempo cuando el servicio experimente problemas. 

    ![Azure classic portal - SQL Database Metrics][PortalSqlMetrics]

Si una métrica supera una utilización del 80% durante un prolongado período de tiempo, esto podría indicar un problema de rendimiento. Para obtener información más detallada sobre la descripción de la utilización de bases de datos, consulte [Descripción del uso de recursos](http://msdn.microsoft.com/library/azure/dn369873.aspx#Resource).

Si las métricas indican que la base de datos está incurriendo en una alta utilización, plantéese **escalar verticalmente la base de datos a un nivel de servicio más alto** como primer paso de mitigación. Para resolver problemas inmediatamente, plantéese utilizar la pestaña **Escala** correspondiente a su base de datos para escalarla verticalmente. Esto provocará un aumento de su factura. ![Azure classic portal - SQL Database Scale][PortalSqlScale]

Tan pronto como pueda, plantéese llevar a cabo estos pasos de mitigación adicionales:

- **Ajustar la base de datos.** Con frecuencia, es posible reducir la utilización de la base de datos y evitar tener que escalar a un nivel más alto optimizando su base de datos.
- **Tener en cuenta la arquitectura del servicio.** Con frecuencia, la carga del servicio no se distribuye uniformemente a lo largo del tiempo, sino que contiene "picos" de alta demanda. En lugar de escalar la base de datos verticalmente para administrar los picos y tenerla infrautilizada durante períodos de baja demanda, suele ser posible ajustar la arquitectura del servicio para evitar tales picos o para administrarlos sin incurrir en visitas a la base de datos.

Las secciones restantes de este documento contienen una guía personalizada para ayudarle a implementar estas mitigaciones.


### Configuración de alertas

Suele resultar muy útil configurar alertas para métricas de base de datos clave como paso proactivo para garantizar que tiene tiempo suficiente para reaccionar en caso de agotamiento de recursos.

1. Navegue a la pestaña **Supervisión** correspondiente la base de datos para la que desea configurar alertas.
2. Asegúrese de que se muestran las métricas correspondientes tal y como se describió en la sección anterior.
3. Seleccione la métrica para la que desea establecer una alerta y elija **Agregar regla**.

    ![Azure Management Portal: alerta de SQL][PortalSqlAddAlert]

4. Proporcione un nombre y una descripción de la alerta ![Azure Management Portal: nombre y descripción de alerta de SQL][PortalSqlAddAlert2]

5. Especifique el valor para utilizar como umbral de alerta. Plantéese utilizar **80 %** para disponer de algún tiempo de reacción. Además, asegúrese de especificar una dirección de correo electrónico que supervise habitualmente.
 
    ![Azure Management Portal: límite y correo electrónico de alerta de SQL][PortalSqlAddAlert3]


Para obtener más información sobre el diagnóstico de problemas de SQL, consulte [Diagnóstico avanzado](#AdvancedDiagnosing) al final de este documento.

<a name="Indexing"></a>
## Indización

Cuando comience a observar problemas con el rendimiento de sus consultas, lo primero que debe investigar es el diseño de los índices. Los índices son importantes porque afectan directamente al modo en que el motor SQL ejecuta una consulta.

Por ejemplo, si a menudo necesita buscar un elemento por un campo determinado, debe plantearse agregar un índice para esa columna. De lo contrario, el motor SQL se verá obligado a realizar un examen de tabla y leer cada registro físico (o al menos la columna de consulta) y los registros podrían esparcirse considerablemente en el disco.

Por tanto, si utiliza las instrucciones WHERE o JOIN frecuentemente en determinadas columnas, debe asegurarse de que las indiza. Para obtener más información, consulte la sección [Creación de índices](#CreatingIndexes).

Si los índices son magníficos y los exámenes de tabla malísimos, ¿significa eso que debe indizar cada columna de la tabla, simplemente para estar seguro? La respuesta breve es "probablemente no". Los índices ocupan espacio y se sobrecargan a sí mismos: cada vez que hay una instrucción insert en una tabla, es necesario actualizar las estructuras de índice para cada una de las columnas indizadas. Consulte la siguiente información para obtener instrucciones sobre cómo elegir los índices de columna.

### Instrucciones sobre el diseño de índices

Tal y como se mencionó anteriormente, no siempre es mejor agregar más índices a una tabla porque los propios índices pueden ser costosos, tanto en términos de rendimiento como en sobrecarga de almacenamiento.

#### Consideraciones sobre consultas

- Considere la opción de agregar índices a columnas que se usan frecuentemente en predicados (por ejemplo, cláusulas WHERE) y en condiciones de combinación, mientras busca un equilibrio en las consideraciones sobre la base de datos que figuran más abajo.
- Escriba consultas que inserten o modifiquen tantas filas como sea posible en una sola instrucción, en lugar de utilizar varias consultas para actualizar las mismas filas. Cuando solamente hay una instrucción, el motor de base de datos puede optimizar mejor cómo mantiene los índices.

#### Consideraciones sobre la base de datos

Los números grandes de índices en una tabla afectan al rendimiento de las instrucciones INSERT, UPDATE, DELETE y MERGE porque todos los índices se deben ajustar apropiadamente como datos en los cambios de tabla.

- Para tablas **con un alto grado de actualización**, evite indizar columnas con alto grado de actualización.
- Para tablas **que no se actualizan frecuentemente** pero tienen grandes volúmenes de datos, use muchos índices. Esto puede mejorar el rendimiento de consultas que no modifican datos (como por ejemplo instrucciones SELECT) porque el optimizador de consultas tendrá más opciones para buscar el mejor método de acceso.

La indización de tablas pequeñas puede no ser óptima porque el optimizador de consultas puede tardar más tiempo en recorrer el índice buscando datos que en realizar un simple examen de tabla. Por tanto, pudiera ser que los índices en tablas pequeñas nunca fueran usados y mucho menos mantenidos como datos en los cambios de tabla.


<a name="CreatingIndexes"></a>
### Creación de índices

#### Back-end de JavaScript

Para establecer el índice para una columna en el back-end de JavaScript, lleve a cabo el siguiente procedimiento:

1. Abra el servicio móvil en el [Portal de Azure clásico].
2. Haga clic en la pestaña **Datos**.
3. Seleccione la tabla que desea modificar.
4. Haga clic en la pestaña **Columnas**.
5. Seleccione la columna. En la barra de comandos, haga clic en **Definir índice**.

	![Mobile Services Portal - Set Index][SetIndexJavaScriptPortal]

También puede quitar índices dentro de esta vista.

#### Back-end de .NET

Para definir un índice en Entity Framework, use el atributo `[Index]` en los campos que desea indizar. Por ejemplo:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }

		[Index]
        public bool Complete { get; set; }
    }

Para obtener más información sobre índices, consulte las [anotaciones de índice en Entity Framework][]. Para conocer más sugerencias sobre la optimización de índices, consulte [Indexación avanzada](#AdvancedIndexing) al final de este documento.

<a name="Schema"></a>
## Diseño de esquemas

A continuación se indican algunos problemas que conviene conocer cuando se seleccionan los tipos de datos para los objetos, lo que, a su vez, se convierte en el esquema de la base de datos SQL. Con frecuencia, el ajuste del esquema puede significar mejoras de rendimiento importantes porque SQL tiene formas optimizadas y personalizadas de administrar la indización y el almacenamiento para diferentes tipos de datos:

- **Utilice la columna de identificador proporcionada**. Cada tabla de servicio móvil cuenta con una columna de identificador predeterminada configurada como la clave principal y tiene un índice definido en ella. No es necesario crear una columna de identificador adicional.
- **Usar los tipos de datos correctos en el modelo.** Si sabe que una propiedad determinada del modelo será numérica o booleana, asegúrese de definirla de esa forma en el modelo en lugar de como una cadena. En el back-end de JavaScript, use literales como `true` en lugar de `"true"` y `5` en lugar de `"5"`. En el back-end de .NET, use los tipos `int` y `bool` cuando declare las propiedades del modelo. Esto permite a SQL crear el esquema correcto para esos tipos, lo que hace que las consultas sean más eficientes.

<a name="Query"></a>
## Diseño de consultas

A continuación se mencionan algunas directrices que debe tener en cuenta cuando ejecute consultas en una base de datos:

- **Ejecutar siempre operaciones de combinación en la base de datos.** Con frecuencia, necesitará combinar registros de dos o más tablas donde dichos registros comparten un campo común (también conocido como *combinación*). Esta operación puede ser ineficiente si se realiza de forma incorrecta ya que puede desplegar todas las entidades de ambas tablas y después iterar a través de todas ellas. Este tipo de operación se deja para la propia base de datos, pero a veces es fácil realizarlo equivocadamente en el cliente o en el código del servicio móvil.
    - No realice combinaciones en el código de la aplicación.
    - No realice combinaciones en el código del servicio móvil. Cuando utilice el back-end de JavaScript, sea consciente de que el [objeto table](http://msdn.microsoft.com/library/windowsazure/jj554210.aspx) no administra combinaciones. Asegúrese de usar el [objeto mssql](http://msdn.microsoft.com/library/windowsazure/jj554212.aspx) directamente para garantizar que la combinación tiene lugar en la base de datos. Para obtener más información, consulte [Unión de tablas relacionales](mobile-services-how-to-use-server-scripts.md#joins). Si usa el back-end de .NET y realiza consultas mediante LINQ, las combinaciones se administrarán automáticamente en el nivel de base de datos mediante Entity Framework.
- **Implementar paginación.** algunas veces, la ejecución de consultas en la base de datos puede provocar la devolución de un gran número de registros al cliente. Para minimizar el tamaño y la latencia de las operaciones, plantéese implementar paginación.

    - De forma predeterminada, el servicio móvil limitará cualquier consulta entrante a un tamaño de página de 50 y manualmente puede solicitar hasta 1.000 registros. Para obtener más información, consulte "Devolución de datos en páginas" para [Tienda Windows](mobile-services-windows-dotnet-how-to-use-client-library.md#paging), [iOS](mobile-services-ios-how-to-use-client-library.md#paging), [Android](mobile-services-android-how-to-use-client-library.md#paging), [HTML/JavaScript](mobile-services-html-how-to-use-client-library#paging) y [Xamarin](partner-xamarin-mobile-services-how-to-use-client-library.md#paging).
    - No hay un tamaño de página predeterminado para consultas realizadas desde el código del servicio móvil. Si la aplicación no implementa paginación, o como medida de protección, plantéese aplicar límites predeterminados a las consultas. En el back-end de JavaScript, use el operador **take** en el [objeto query](http://msdn.microsoft.com/library/azure/jj613353.aspx). Si usa el back-end de .NET, plantéese usar el [método Take] como parte de la consulta LINQ.  


Para obtener más información sobre la mejora del diseño de consultas, incluido cómo analizar planes de consulta, vea [Diseño avanzado de consultas](#AdvancedQuery) al final de este documento.

<a name="Architecture"></a>
## Arquitectura del servicio

Imagine un escenario en el que está a punto de enviar una notificación de inserción a todos sus clientes para comprobar algún contenido nuevo de la aplicación. Al pulsar la notificación, la aplicación se inicia, lo que posiblemente active una llamada al servicio móvil y una ejecución de consulta en la base de datos SQL. Como posiblemente millones de clientes realicen esta acción en un espacio de tiempo de unos pocos minutos, se generará un increíble aumento de carga SQL, que puede ser de órdenes de magnitud superior a la carga en estado estable de la aplicación. Se podría hacer frente a este problema escalando la aplicación a un nivel SQL más alto durante el pico y después volver a reducir el nivel de escala, pero esa solución requiere intervención manual y supone un aumento de coste. Con frecuencia, pequeños ajustes en la arquitectura del servicio móvil pueden compensar considerablemente la carga a la que los clientes someten a la base de datos SQL y eliminar picos problemáticos de demanda. Estas modificaciones suelen implementarse fácilmente con un mínimo impacto en la experiencia del cliente. A continuación se muestran algunos ejemplos:

- **Distribuir la carga a lo largo del tiempo.** Si controla el ritmo de determinados eventos (por ejemplo una notificación de inserción de difusión), que se espera que generen un pico de demanda, y el ritmo de los mismos no es crítico, plantéese distribuirlos a lo largo del tiempo. En el ejemplo anterior, quizás sea aceptable para los clientes de la aplicación que se les notifique del nuevo contenido de la aplicación en lotes durante el período de tiempo de un día en lugar de casi simultáneamente. Plantéese organizar los clientes en grupos, lo que le permitirá una entrega escalonada a cada lote. Si usa Centros de notificaciones, una forma sencilla de implementar esta estrategia es aplicar una etiqueta adicional para realizar un seguimiento del lote y después entregar una notificación de inserción a esa etiqueta. Para obtener más información sobre etiquetas, consulte [Uso de los Centros de notificaciones para enviar noticias de última hora](../notification-hubs/notification-hubs-windows-store-dotnet-send-breaking-news.md).
- **Usar almacenamiento de blobs y tablas siempre que sea apropiado.** Con frecuencia, el contenido que los clientes verán durante el pico es bastante estático y no necesita almacenarse en una base de datos SQL porque probablemente el usuario no necesite capacidades de consulta relacional sobre ese contenido. En ese caso, puede ser conveniente almacenar el contenido en almacenamiento de blobs o tablas. Puede acceder a blobs públicos en Almacenamiento de blobs directamente desde el dispositivo. Para acceder a blobs de una forma segura o usar Almacenamiento de tablas, necesitará recorrer una API personalizada de Servicios móviles para proteger la clave de acceso de almacenamiento. Para obtener más información, consulte [Carga de imágenes en el almacenamiento de Azure mediante Servicios móviles](../mobile-services/mobile-services-dotnet-backend-windows-universal-dotnet-upload-data-blob-storage.md).
- **Utilice una caché en memoria.** Otra alternativa es almacenar aquellos datos a los que habitualmente se accederá durante un pico de tráfico en una caché en memoria como [Caché de Azure](https://azure.microsoft.com/services/cache/). Esto significa que las solicitudes entrantes podrán capturar la información que necesitan de memoria, en lugar de realizar consultas repetidamente a la base de datos.

<a name="Advanced"></a>
## Solución avanzada de problemas
En esta sección se tratan algunas de las tareas de diagnóstico avanzado que pueden resultar de gran utilidad si los pasos realizados hasta ahora no han conseguido solucionar el problema por completo.

### Requisitos previos
Para realizar algunas de las tareas de diagnóstico que se describen en esta sección, debe tener acceso a una herramienta de administración para bases de datos SQL, como **SQL Server Management Studio** o la funcionalidad de administración integrada en el **Portal de Azure clásico**.

SQL Server Management Studio es una aplicación de Windows gratuita que ofrece las funciones más avanzadas. Si no tiene acceso a una máquina Windows (por ejemplo si utiliza un sistema Mac), plantéese aprovisionar una máquina virtual en Azure tal y como se muestra en [Creación de una máquina virtual que ejecuta Windows Server](../virtual-machines/virtual-machines-windows-tutorial.md) y después conéctese remotamente a ella. Si intenta utilizar la máquina virtual principalmente con la finalidad de ejecutar SQL Server Management Studio, una instancia de tipo **Básico A0** (anteriormente "Extrapequeño") debe ser suficiente.

El Portal de Azure clásico ofrece una experiencia de administración integrada que, aunque es más limitada, está disponible sin una instalación local.

Los siguientes pasos le guiarán a través del proceso de obtención de información de conexión para la base de datos SQL que respalda el servicio móvil y después a través del uso de una de las dos herramientas para conectarse a ella. Puede seleccionar cualquiera de las herramientas según sus preferencias.

#### Obtener información de conexión SQL
1. Inicie el [Portal de Azure clásico].
2. En la pestaña Servicios móviles, seleccione el servicio con el que desea trabajar.
3. Seleccione la pestaña **Configurar**.
4. Seleccione un nombre en **Base de datos SQL** en la sección **Configuración de base de datos**. Esta acción le llevará a la pestaña Base de datos SQL de Azure en el portal.
5. Seleccione **Configurar las reglas de firewall de Azure para esta dirección IP**.
6. Anote la dirección del servidor de la sección **Conectarse a la base de datos**, por ejemplo: *mcml4otbb9.database.windows.net*.

#### SQL Server Management Studio
1. Navegue a [Ediciones de SQL Server SQL - Express](http://www.microsoft.com/server-cloud/products/sql-server-editions/sql-server-express.aspx)
2. Busque la sección **SQL Server Management Studio** y seleccione el botón **Descargar** que hay debajo.
3. Complete los pasos de instalación hasta que pueda ejecutar la aplicación correctamente:

    ![SQL Server Management Studio][SSMS]

4. En el cuadro de diálogo **Conectar al servidor** escriba los siguientes valores:
    - Nombre del servidor: *dirección del servidor que obtuvo anteriormente*
    - Autenticación: *SQL Server Authentication*
    - Inicio de sesión: *inicio de sesión seleccionado al crear el servidor*
    - Contraseña: *contraseña de sesión seleccionada al crear el servidor*
5. Ahora se debe haber conectado.

#### Portal de administración de bases de datos SQL
1. En la pestaña Base de datos SQL de Azure correspondiente a su base de datos seleccione el botón **Administrar**
2. Configure la conexión con los siguientes valores:
    - Servidor: *debe estar predefinido en el valor correcto*
    - Base de datos: *dejar en blanco*
    - Nombre de usuario: *inicio de sesión seleccionado al crear el servidor*
    - Contraseña: *contraseña de sesión seleccionada al crear el servidor*
3. Ahora se debe haber conectado.

    ![Azure classic portal - SQL Database][PortalSqlManagement]

<a name="AdvancedDiagnosing" />
### Diagnósticos avanzados

Se pueden completar numerosas tareas de diagnóstico de forma fácil en el **Portal de Azure clásico**, pero las más avanzadas son solo posibles a través de **SQL Server Management Studio** o el **Portal de administración de bases de datos SQL**. Aprovecharemos las vistas de administración dinámica, un conjunto de vistas que se rellenan automáticamente con información de diagnóstico acerca de la base de datos. En esta sección se proporciona un conjunto de consultas que podemos ejecutar en estas vistas para examinar varias métricas. Para obtener más información, consulte [Supervisar Base de datos SQL de Azure mediante vistas de administración dinámica][].

Después de completar los pasos de la sección anterior para conectarse a su base de datos en SQL Server Management Studio, seleccione dicha base de datos en el **Explorador de objetos**. Expanda **Vistas** y **Vistas del sistema** mostrará una lista de vistas de administración. Para ejecutar las consultas siguientes, seleccione **Nueva consulta**, mientras ha seleccionado la base de datos en el **Explorador de objetos**, y después pegue la consulta y seleccione **Ejecutar**.

![SQL Server management Studio - dynamic management views][SSMSDMVs]

Alternativamente, si está usando el Portal de administración de bases de datos SQL, seleccione primero su base de datos y después **Nueva consulta**.

![SQL Database Management Portal - new query][PortalSqlManagementNewQuery]

Para ejecutar cualquiera de las consultas siguientes, péguela en la ventana y seleccione **Ejecutar**.

![SQL Database Management Portal - run query][PortalSqlManagementRunQuery]

#### Métricas avanzadas


El portal de administración crea determinadas métricas fácilmente disponibles si usa los niveles Básico, Estándar y Premium. Obtener estas y otras métricas resulta sencillo usando la vista de administración **[sys.resource\_stats](http://msdn.microsoft.com/library/dn269979.aspx)**, independientemente del nivel que esté usando. Considere la siguiente consulta:

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'todoitem_db'
    ORDER BY start_time DESC

> [AZURE.NOTE]
Ejecute esta consulta en la base de datos **master** del servidor; la vista **sys.resource\_stats** solo está presente en esa base de datos.

El resultado contendrá las siguientes mediciones útiles: CPU (% del límite del nivel), Almacenamiento (megabytes), Lecturas de datos físicos (% del límite del nivel), Escrituras en registro (% del límite del nivel), Memoria (% del límite del nivel), Recuento de trabajadores, Recuento de sesiones, etc.

#### Eventos de conectividad de SQL

La vista **[sys.event\_log](http://msdn.microsoft.com/library/azure/jj819229.aspx)** contiene detalles de eventos relacionados con la conectividad.

    select * from sys.event_log
    where database_name = 'todoitem_db'
    and event_type like 'throttling%'
    order by start_time desc

> [AZURE.NOTE]
Ejecute esta consulta en la base de datos **master** del servidor; la vista **sys.event\_log** solo está presente en esa base de datos.

<a name="AdvancedIndexing" ></a>
### Indización avanzada

Una tabla o vista puede contener los siguientes tipos de índices:

- **En clúster**. Un índice en clúster especifica cómo se almacenan físicamente los registros en el disco. Solamente debe haber un índice en clúster por tabla ya que las filas de datos se pueden ordenar ellas mismas en una sola disposición.

- **En no clúster**. Los índices no clúster se almacenan separadamente desde filas de datos y se usan para realizar una búsqueda basada en el valor de índice. Todos los índices no clúster de una tabla usan los valores de clave del índice en clúster como clave de búsqueda.

Para proporcionar una analogía con el mundo real, piense en un libro o manual técnico. El contenido de cada página es un registro, el número de página es el índice en clúster y el índice de temas de la parte posterior del libro es un índice no clúster. Cada entrada del índice de temas apunta al índice en clúster, el número de página.

> [AZURE.NOTE]
De forma predeterminada, el back-end de JavaScript de Servicios móviles de Azure establece **\_createdAt** como índice agrupado. Si quita esta columna o si desea un índice agrupado diferente, asegúrese de seguir las [instrucciones de diseño de índices agrupados](#ClusteredIndexes) que se indican a continuación. En el back-end de .NET, la clase `EntityData` define `CreatedAt` como un índice en clúster mediante la anotación `[Index(IsClustered = true)]`.

<a name="ClusteredIndexes"></a>
#### Directrices para el diseño de índices en clúster

Cada tabla debe tener un índice en clúster en la columna (o columnas, en el caso de una clave compuesta) con las siguientes propiedades:

- Narrow: usa un tipo de datos pequeño, o es una [clave compuesta][Primary and Foreign Key Constraints] de un número pequeño de columnas estrechas
- Unique, o mayoritariamente único
- Static: el valor no se cambia frecuentemente
- Ever-increasing
- (Opcional) Fixed-width
- (Opcional) nonnull

El motivo para la propiedad **narrow** es que los demás índices de una tabla usan los valores de clave del índice en clúster como claves de búsqueda. En el ejemplo de un índice de temas de la parte posterior de un libro, el índice en clúster es un número de página que, a su vez, es pequeño. Si, en su lugar, se incluye el título del capítulo en el clúster en índice, entonces el índice de temas sería mucho más largo, porque el valor de clave lo sería (nombre de capítulo, número de página) .

La clave debe tener la propiedad **static** y **ever-increasing** para evitar tener que mantener la ubicación física de los registros (lo que significa mover registros físicamente o, posiblemente, fragmentar el almacenamiento dividiendo las páginas donde se almacenan los registros).

El índice en clúster será más valioso para consultas que hagan lo siguiente:

- Devuelven un intervalo de valores usando operadores como BETWEEN, >, >=, < y <=.
	- Después de que se encuentre la fila con el primer valor usando el índice en clúster, las filas con valores indizados siguientes serán físicamente adyacentes con toda seguridad.
- Usen cláusulas JOIN; normalmente son columnas de clave externa.
- Usen cláusulas ORDER BY o GROUP BY.
	- Un índice en las columnas especificadas en la cláusula ORDER BY o GROUP BY puede eliminar la necesidad de que el motor de la base de datos ordene los datos, porque las filas ya están ordenadas. Esto mejora el rendimiento de las consultas.

#### Creación de índices en clúster en Entity Framework

Para establecer el índice en clúster en el back-end de .NET usando Entity Framework, establezca la propiedad `IsClustered` de la anotación. Por ejemplo, esta es la definición de `CreatedAt` en `Microsoft.WindowsAzure.Mobile.Service.EntityData`:

	[Index(IsClustered = true)]
	[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
	[TableColumnAttribute(TableColumnType.CreatedAt)]
	public DateTimeOffset? CreatedAt { get; set; }

#### Creación de índices en el esquema de base de datos

Para el back-end de JavaScript, solamente puede modificar el índice en clúster de una tabla cambiando el esquema de base de datos directamente, ya sea a través de SQL Server Management Studio o mediante el Portal de base de datos SQL de Azure.

Las siguientes guías describen cómo establecer un índice agrupado o no agrupado en clúster modificando el esquema de base de datos directamente:

- [Crear y modificar restricciones PRIMARY KEY][]
- [Crear índices no clúster][]
- [Crear índices clúster][]
- [Crear índices únicos][]

#### Búsqueda de los N mejores índices ausentes
Puede escribir consultas en vista de administración dinámica SQL que le ofrecerán información más detallada acerca del uso de los recursos de consultas individuales o le proporcionarán heurística sobre qué índices agregar. La siguiente consulta determina qué 10 índices ausentes producirían la mejora acumulativa anticipada más alta, en orden descendente, para consultas de usuario.

    SELECT TOP 10 *
    FROM sys.dm_db_missing_index_group_stats
    ORDER BY avg_total_user_cost * avg_user_impact * (user_seeks + user_scans)
    DESC;

La siguiente consulta de ejemplo ejecuta una combinación a través de estas tablas para obtener una lista de columnas que deben formar parte de cada índice ausente y calcula una "ventaja de índice" para determinar si se debe considerar el índice dado:

    SELECT * from
    (
        SELECT
        (user_seeks+user_scans) * avg_total_user_cost * (avg_user_impact * 0.01) AS index_advantage, migs.*
        FROM sys.dm_db_missing_index_group_stats migs
    ) AS migs_adv,
      sys.dm_db_missing_index_groups mig,
      sys.dm_db_missing_index_details mid
    WHERE
      migs_adv.group_handle = mig.index_group_handle and
      mig.index_handle = mid.index_handle
      AND migs_adv.index_advantage > 10
    ORDER BY migs_adv.index_advantage DESC;

Para obtener más información, consulte [Supervisión de Base de datos SQL de Azure mediante vistas de administración dinámica][] y [Vistas de administración dinámica de índices ausentes][].

<a name="AdvancedQuery" ></a>
### Diseño avanzado de consultas 

Con frecuencia, es difícil diagnosticar qué consultas son más costosas para la base de datos.


#### Búsqueda de las N mejores consultas

El siguiente ejemplo devuelve información acerca de las cinco consultas principales clasificadas en función del tiempo promedio de CPU. Este ejemplo agrega las consultas conforme a sus hash de consulta, por lo que las consultas lógicamente equivalentes se agrupan por sus consumos de recursos acumulativos.

	SELECT TOP 5 query_stats.query_hash AS "Query Hash",
	    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
	    MIN(query_stats.statement_text) AS "Statement Text"
	FROM
	    (SELECT QS.*,
	    SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
	    ((CASE statement_end_offset
	        WHEN -1 THEN DATALENGTH(st.text)
	        ELSE QS.statement_end_offset END
	            - QS.statement_start_offset)/2) + 1) AS statement_text
	     FROM sys.dm_exec_query_stats AS QS
	     CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
	GROUP BY query_stats.query_hash
	ORDER BY 2 DESC;

Para obtener más información, consulte [Supervisar Base de datos SQL de Azure mediante vistas de administración dinámica][]. Además de ejecutar la consulta, el **Portal de administración de bases de datos SQL** proporciona un buen atajo para ver estos datos; basta con seleccionar **Resumen** para la base de datos y, después, **Rendimiento de las consultas**:

![SQL Database Management Portal - query performance][PortalSqlManagementQueryPerformance]

#### Análisis del plan de consulta

Cuando haya identificado las consultas costosas o si está a punto de implementar código usando nuevas consultas y le gustaría investigar el rendimiento de las mismas, las herramientas ofrecen una ayuda magnífica para analizar el **plan de consulta**. El plan de consulta permite ver qué operaciones consumen la mayor parte del tiempo de la CPU y de recursos de E/S cuando se ejecuta una consulta SQL dada. Para analizar el plan de consulta en **SQL Server Management Studio**, use los botones de la barra de herramientas resaltados.

![SQL Server Management Studio - query plan][SSMSQueryPlan]

Para analizar el plan de consulta en el **Portal de administración de bases de datos SQL**, use los botones de la barra de herramientas resaltados.

![SQL Database Management Portal - query plan][PortalSqlManagementQueryPlan]

## Otras referencias

- [Documentación de Base de datos SQL de Azure][]
- [Rendimiento y escalado de Base de datos SQL de Azure][]
- [Solución de problemas de Base de datos SQL de Azure][]

### Indización

- [Conceptos básicos de los índices][]
- [Directrices generales para el diseño de índices][]
- [Directrices para el diseño de índices únicos][]
- [Directrices para el diseño de índices en clúster][]
- [Restricciones entre claves principales y claves externas][]
- [¿Cuánto cuesta esa clave?][]

### Entity Framework
- [Consideraciones de rendimiento para Entity Framework 5][]
- [Anotaciones de datos de Code First][]

<!-- IMAGES -->

[SSMS]: ./media/mobile-services-sql-scale-guidance/1.png
[PortalSqlManagement]: ./media/mobile-services-sql-scale-guidance/2.png
[PortalSqlMetrics]: ./media/mobile-services-sql-scale-guidance/3.png
[PortalSqlScale]: ./media/mobile-services-sql-scale-guidance/4.png
[PortalSqlAddAlert]: ./media/mobile-services-sql-scale-guidance/5.png
[PortalSqlAddAlert2]: ./media/mobile-services-sql-scale-guidance/6.png
[PortalSqlAddAlert3]: ./media/mobile-services-sql-scale-guidance/7.png
[SetIndexJavaScriptPortal]: ./media/mobile-services-sql-scale-guidance/set-index-portal-ui.png
[SSMSDMVs]: ./media/mobile-services-sql-scale-guidance/8.png
[PortalSqlManagementNewQuery]: ./media/mobile-services-sql-scale-guidance/9.png
[PortalSqlManagementRunQuery]: ./media/mobile-services-sql-scale-guidance/10.png
[PortalSqlManagementQueryPerformance]: ./media/mobile-services-sql-scale-guidance/11.png
[SSMSQueryPlan]: ./media/mobile-services-sql-scale-guidance/12.png
[PortalSqlManagementQueryPlan]: ./media/mobile-services-sql-scale-guidance/13.png

<!-- LINKS -->

[Portal de Azure clásico]: http://manage.windowsazure.com

[Documentación de Base de datos SQL de Azure]: http://azure.microsoft.com/documentation/services/sql-database/
[Managing SQL Database using SQL Server Management Studio]: http://go.microsoft.com/fwlink/p/?linkid=309723&clcid=0x409
[Supervisar Base de datos SQL de Azure mediante vistas de administración dinámica]: http://go.microsoft.com/fwlink/p/?linkid=309725&clcid=0x409
[Supervisión de Base de datos SQL de Azure mediante vistas de administración dinámica]: http://go.microsoft.com/fwlink/p/?linkid=309725&clcid=0x409
[Rendimiento y escalado de Base de datos SQL de Azure]: http://go.microsoft.com/fwlink/p/?linkid=397217&clcid=0x409
[Solución de problemas de Base de datos SQL de Azure]: http://msdn.microsoft.com/library/azure/ee730906.aspx
[Razones para usar los nuevos niveles de servicio]: http://msdn.microsoft.com/library/azure/dn369873.aspx#Reasons

[método Take]: http://msdn.microsoft.com/library/vstudio/bb503062(v=vs.110).aspx

<!-- MSDN -->
[Crear y modificar restricciones PRIMARY KEY]: http://technet.microsoft.com/library/ms181043(v=sql.105).aspx
[Crear índices clúster]: http://technet.microsoft.com/library/ms186342(v=sql.120).aspx
[Crear índices únicos]: http://technet.microsoft.com/library/ms187019.aspx
[Crear índices no clúster]: http://technet.microsoft.com/library/ms189280.aspx

[Primary and Foreign Key Constraints]: http://msdn.microsoft.com/library/ms179610(v=sql.120).aspx
[Restricciones entre claves principales y claves externas]: http://msdn.microsoft.com/library/ms179610(v=sql.120).aspx
[Conceptos básicos de los índices]: http://technet.microsoft.com/library/ms190457(v=sql.105).aspx
[Directrices generales para el diseño de índices]: http://technet.microsoft.com/library/ms191195(v=sql.105).aspx
[Directrices para el diseño de índices únicos]: http://technet.microsoft.com/library/ms187019(v=sql.105).aspx
[Directrices para el diseño de índices en clúster]: http://technet.microsoft.com/library/ms190639(v=sql.105).aspx

[Vistas de administración dinámica de índices ausentes]: http://technet.microsoft.com/library/ms345421.aspx

<!-- EF -->
[Consideraciones de rendimiento para Entity Framework 5]: http://msdn.microsoft.com/data/hh949853
[Anotaciones de datos de Code First]: http://msdn.microsoft.com/data/jj591583.aspx
[anotaciones de índice en Entity Framework]: http://msdn.microsoft.com/data/jj591583.aspx#Index

<!-- BLOG LINKS -->
[¿Cuánto cuesta esa clave?]: http://www.sqlskills.com/blogs/kimberly/how-much-does-that-key-cost-plus-sp_helpindex9/

<!---HONumber=AcomDC_0224_2016-->