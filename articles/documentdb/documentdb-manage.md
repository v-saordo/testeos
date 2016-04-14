<properties 
	pageTitle="DocumentDB - Capacidad - Almacenamiento de documentos | Microsoft Azure" 
	description="Más información sobre el almacenamiento de datos y el almacenamiento de documentos DocumentDB y cómo escalar DocumentDB para satisfacer las necesidades de capacidad de la aplicación." 
	keywords="almacenamiento de documentos"
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/27/2016" 
	ms.author="mimig"/>

# Más información sobre almacenamiento de documentos y capacidad en DocumentDB
DocumentDB es un servicio de bases de datos NoSQL orientadas a documentos totalmente administrado y escalable para documentos JSON. Con la Base de datos de documentos, no tendrá que alquilar máquinas virtuales, implementar software, supervisar bases de datos o preocuparse por la recuperación ante desastres. Los ingenieros de Microsoft operan y supervisan de forma continua DocumentDB para ofrecer disponibilidad, rendimiento y protección de datos universales.

Puede empezar con DocumentDB mediante la [creación de una cuenta de base de datos](documentdb-create-account.md) a través del [Portal de Azure](https://portal.azure.com/). DocumentDB se ofrece en unidades de rendimiento y almacenamiento respaldado de unidad de estado sólido (SSD). Estas unidades se aprovisionan mediante la creación de colecciones de la base de datos dentro de su cuenta de base de datos. Cada colección incluye 10 GB de almacenamiento de base de datos con rendimiento reservado. Si se cambian los requisitos de rendimiento de su aplicación, lo cambia dinámicamente estableciendo el [nivel de rendimiento](documentdb-performance-levels.md) de cada colección.

Cuando la aplicación supera los niveles de rendimiento de una o varias colecciones, se limitarán las solicitudes para cada colección. Esto significa que algunas solicitudes de aplicación pueden tener éxito mientras que otras quedarán limitadas.

En este artículo se ofrece información general sobre los recursos y métricas disponibles para administrar la capacidad y planear el almacenamiento de datos.

## Cuenta de base de datos y recursos administrativos
Como suscriptor de Azure, puede aprovisionar una o más cuentas de base de datos de la Base de datos de documentos. Cada cuenta de base de datos viene con una cuota de recursos administrativos que incluyen bases de datos, usuarios y permisos. Estos recursos están sujetos a [cuotas y límites](documentdb-limits.md). Si necesita recursos administrativos adicionales, [póngase en contacto con el soporte técnico](documentdb-increase-limits.md).

El Portal de Azure ofrece métricas de uso para supervisar cuentas de base de datos, bases de datos y colecciones. Para más información, vea [Supervisión de una cuenta de DocumentDB](documentdb-monitor-accounts.md).

## Bases de datos con almacenamiento ilimitado de documentos
Una única base de datos de la Base de datos de documentos puede contener una cantidad prácticamente ilimitada de almacenamiento de documentos con particiones por colecciones. Las colecciones forman los dominios de transacción para los documentos que las contienen. Una base de datos de DocumentDB es elástica en tamaño, desde unos pocos GB hasta terabytes potenciales de almacenamiento de documentos respaldado y capacidad de proceso aprovisionada de SSD. A diferencia de la base de datos RDBMS tradicional, una base de datos en la Base de datos de documentos no está dirigida a un único equipo.

Con DocumentDB, a medida que escala sus aplicaciones, podrá crear más colecciones o bases de datos o ambas. Varias aplicaciones propias dentro de Microsoft usan DocumentDB a nivel de consumidor creando bases de datos de DocumentDB extremadamente grandes y cada una contenía cientos o miles de colecciones con terabytes de almacenamiento de documentos. Puede ampliar o reducir una base de datos agregando o eliminando colecciones para cumplir con los requisitos de escala de su aplicación.

## Colecciones de base de datos
Cada base de datos de la Base de datos de documentos puede contener una o más colecciones. Las colecciones funcionan como particiones de datos de alta disponibilidad para el procesamiento y almacenamiento de documentos. Cada colección puede almacenar documentos con esquema heterogéneo. Las capacidades de consulta e indización automática de DocumentDB le permiten filtrar y recuperar documentos con facilidad. Una colección proporciona el ámbito del almacenamiento de documentos y la ejecución de consultas. Una colección también es un dominio de transacciones para todos los documentos contenidos en la misma. A las colecciones se les asigna procesamiento según su nivel de rendimiento especificado. Esto se puede ajustar dinámicamente a través del Portal de Azure o en uno de los SDK.

Puede crear cualquier cantidad de colecciones para cumplir con los requisitos de escala de rendimiento y almacenamiento de datos de sus aplicaciones. Las colecciones se pueden crear a través del [Portal de Azure](https://portal.azure.com/) o mediante cualquiera de los [SDK de DocumentDB](documentdb-sdk-dotnet.md).

>[AZURE.NOTE] Cada colección admite el almacenamiento de hasta 10 GB de datos de documentos. Para almacenar conjuntos de datos grandes, debe crear una partición en varias colecciones. Vea [Creación de particiones de datos en DocumentDB con el SDK de .NET](documentdb-sharding.md) para más información.
 
## Solicitud de unidades y las operaciones de base de datos
DocumentDB permite un amplio conjunto de operaciones de base de datos, incluidas las consultas relacionales y jerárquicas con UDF, procedimientos almacenados y desencadenadores. Todo funciona con los documentos dentro de una colección de base de datos. El coste de procesamiento asociado a cada una de estas operaciones variará en función de la CPU, las E/S y la memoria que se necesite para completar la operación. En lugar de administrar y pensar sobre los recursos de hardware, puede pensar en una unidad de solicitud (RU) como una medida única para los recursos necesarios para realizar varias operaciones de la base de datos y dar servicio a una solicitud de la aplicación.

Las unidades de solicitud se aprovisionan y se asignan según el nivel de rendimiento establecido para una colección. A cada colección se le designa un nivel de rendimiento después de su creación. Este nivel de rendimiento determina la capacidad de procesamiento de una colección en unidades de solicitud por segundo. Los niveles de rendimiento se pueden ajustar durante la vida de una colección para adaptarse a las cambiantes necesidades de procesamiento y patrones de acceso de la aplicación. Para obtener más información, consulte [Niveles de rendimiento de DocumentDB](documentdb-performance-levels.md).

>[AZURE.IMPORTANT] Las colecciones son entidades facturables. El coste está determinado por el nivel de rendimiento asignado a la colección.

El consumo de la unidad de solicitud se evalúa como frecuencia por segundo. Para las aplicaciones que superan la frecuencia de unidad de solicitud aprovisionada para una colección, las solicitudes a esa colección se limitarán hasta que la frecuencia caiga por debajo del nivel reservado. Si la aplicación requiere un mayor nivel de rendimiento, puede ajustar el nivel de rendimiento de las colecciones existentes o distribuir las solicitudes de las aplicaciones en nuevas colecciones.

Una unidad de solicitud es una medida normalizada del costo de procesamiento de solicitudes. Una única unidad de solicitud representa la capacidad de procesamiento necesaria para leer (mediante un self-link) un solo documento JSON de 1 KB que consta de 10 valores de propiedad únicos. El cargo de la unidad de solicitud supone un nivel de coherencia establecido en el valor predeterminado de "Sesión" y todos los documentos se indizan automáticamente. Una solicitud para insertar, reemplazar o eliminar el mismo documento consumirá más procesamiento del servicio y, por tanto, más unidades de solicitud. Cada respuesta del servicio incluye un encabezado personalizado (x-ms-request-charge) que mide las unidades de solicitud usadas por la solicitud. Este encabezado también es accesible a través de los [SDK](documentdb-sdk-dotnet.md). En el SDK de .NET, RequestCharge es una propiedad del objeto ResourceResponse.

>[AZURE.NOTE] La línea de base de 1 unidad de solicitud de un documento de 1 KB corresponde a una operación GET sencilla mediante self link del documento.

Hay varios factores que afectan a las unidades de solicitud utilizadas por una operación en una cuenta de base de datos de DocumentDB. Entre estos factores se incluyen:

- Tamaño del documento. Cuando aumenta el tamaño del documento también aumentan las unidades utilizadas para leer o escribir los datos.
- Recuento de propiedades. Suponiendo que la indexación predeterminada de todas las propiedades, las unidades usadas para escribir un documento aumentarán conforme aumenta el recuento de propiedades.
- Coherencia de datos Al usar los niveles de coherencia de datos Alta y De uso vinculado, se usarán unidades adicionales para leer documentos.
- Propiedades indexadas. Propiedades indexadas: Una directiva de índice en cada colección determina qué propiedades se indexan de forma predeterminada. Puede reducir el consumo de unidades de solicitud limitando el número de las propiedades indexadas. 
- Indexación de documentos. De forma predeterminada, todos los documentos se indexan automáticamente, por tanto consumirá menos unidades de solicitud si no desea indexar algunos de los documentos.

Las consultas, procedimientos almacenados y desencadenadores consumen unidades de solicitud según la complejidad de las operaciones que se están llevando a cabo. Cuando desarrolla su aplicación, inspeccione el encabezado request charge para entender mejor cómo consumen las operaciones la capacidad de unidad de solicitud.

## Opción de nivel de coherencia y rendimiento
La selección del nivel de coherencia predeterminado tiene un impacto en la capacidad de proceso y en la latencia. Puede establecer el nivel de coherencia predeterminado mediante programación y a través del Portal de Azure. También puede reemplazar el nivel de coherencia en función de la solicitud. De forma predeterminada, el nivel de coherencia es el de la sesión que proporciona lecturas/escrituras monotónicas y lee las garantías de escritura. La coherencia de sesión es mayor para aplicaciones centradas en el usuario y proporciona un equilibrio ideal de intercambios de coherencia y rendimiento.

Para obtener instrucciones sobre cómo cambiar el nivel de coherencia en el Portal de Azure, consulte [Administración de una cuenta de DocumentDB](documentdb-manage-account.md#consistency). O bien, para obtener más información sobre los niveles de coherencia, consulte [Uso de los niveles de coherencia](documentdb-consistency-levels.md).

## Sobrecarga de índice y almacenamiento de documentos aprovisionados
Con cada colección creada se aprovisionan 10 GB de almacenamiento de documentos respaldados de SSD. Los 10 GB de almacenamiento de documentos incluyen más almacenamiento para el índice. De forma predeterminada, una colección de la Base de datos de documentos se configura para que indexe automáticamente todos los documentos sin solicitar de forma explícita ningún índice o esquema secundario. En función de las aplicaciones que usan DocumentDB, la sobrecarga de índice típica es de entre el 2 y el 20 %. La tecnología de indexación que utiliza la Base de datos de documentos garantiza que, a pesar de los valores de las propiedades, la sobrecarga de índice no supera el 80 % del tamaño de los documentos con la configuración predeterminada.

De forma predeterminada, la Base de datos de documentos indexa todos los documentos automáticamente. Sin embargo, en caso de que desee ajustar la sobrecarga de índice, puede elegir eliminar determinados documentos de la indexación en el momento de insertar o reemplazar un documento, según lo descrito en [Directivas de indexación de DocumentDB](documentdb-indexing-policies.md). Puede configurar una colección de la Base de datos de documentos para que excluya todos los documentos de la colección de la indexación. También puede configurar una colección de DocumentDB para indexar de manera selectiva solo ciertas propiedades o rutas de acceso con caracteres comodín de los documentos JSON, tal como se describe en [Configuración de la directiva de indexación de una colección](documentdb-indexing-policies.md#configuring-the-indexing-policy-of-a-collection). Excluir propiedades o documentos también mejora la capacidad de proceso de escritura; lo que significa que consumirá menos unidades de solicitud.
 
## Pasos siguientes
Para obtener instrucciones sobre cómo supervisar los niveles de rendimiento en el Portal de Azure, consulte [Supervisión de una cuenta de DocumentDB](documentdb-monitor-accounts.md).

Para obtener más información sobre la elección de los niveles de rendimiento para las colecciones, consulte [Niveles de rendimiento en DocumentDB](documentdb-performance-levels.md).
 

<!---HONumber=AcomDC_0128_2016-->