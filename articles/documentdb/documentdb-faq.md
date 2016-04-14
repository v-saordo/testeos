<properties 
	pageTitle="Preguntas de base de datos DocumentDB - Preguntas más frecuentes| Microsoft Azure" 
	description="Obtenga respuestas a las preguntas frecuentes sobre Azure DocumentDB, un servicio de base de datos de documentos NoSQL para JSON. Responda a preguntas de base de datos acerca de la capacidad, los niveles de rendimiento y escalado." 
	keywords="Database questions, frequently asked questions, documentdb, azure, Microsoft azure"
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/03/2015" 
	ms.author="mimig"/>


#Preguntas más frecuentes sobre DocumentDB

## Preguntas de bases de datos sobre los Fundamentos de Microsoft Azure DocumentDB

### ¿Qué es Azure DocumentDB? 
DocumentDB de Microsoft Azure es un servicio de base de datos de documentos NoSQL muy escalable que ofrece consultas enriquecidas a través de datos sin esquemas, ayuda a proporcionar un rendimiento configurable y confiable y favorece el desarrollo rápido, y todo gracias a una plataforma administrada respaldada por la potencia y el alcance de Microsoft Azure. DocumentDB es la solución adecuada para aplicaciones web y móviles cuando un rendimiento predecible, una baja latencia y un modelo de datos sin esquemas son requisitos clave. Ofrece flexibilidad de esquemas e indexación enriquecida a través de un modelo de datos JSON nativo, e incluye compatibilidad transaccional de varios documentos con JavaScript integrado.
  
Para obtener más preguntas, respuestas e instrucciones de bases de datos sobre la implementación y el uso de este servicio, consulte la [página de documentación de DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

### ¿Qué clase de base de datos es Base de datos de documentos?
DocumentDB es una base de datos orientada a documentos NoSQL que almacena datos en formato JSON. Es compatible con estructuras de datos independientes anidados que se pueden consultar mediante una [gramática de consultas SQL](documentdb-sql-query.md) enriquecida para DocumentDB. DocumentDB ofrece un procesamiento de transacciones de alto rendimiento del servidor JavaScript a través de [ procedimientos almacenados, desencadenadores y funciones definidas por el usuario](documentdb-programming.md). La base de datos admite también niveles de coherencia ajustables por el desarrollador con [niveles de rendimiento](documentdb-performance-levels.md) asociados.
 
### ¿Las bases de datos de DocumentDB tiene tablas como una base de datos relacional (RDBMS)?
No, DocumentDB almacena los datos en colecciones de documentos JSON. Para obtener información sobre los recursos de DocumentDB, consulte [Modelo de recursos y conceptos de DocumentDB](documentdb-resources.md).

### ¿Admiten las bases de datos de DocumentDB datos sin esquemas?
Sí, Base de datos de documentos permite que las aplicaciones almacenen documentos JSON arbitrarios sin definiciones ni sugerencias de esquemas. Los datos están disponibles inmediatamente para consulta a través de la interfaz de consulta SQL de Base de datos de documentos.

### ¿Admite Base de datos de documentos transacciones ACID?
Sí, Base de datos de documentos admite transacciones entre documentos expresadas como procedimientos almacenados y desencadenadores JavaScript. Las transacciones se limitan a una única colección y se ejecutan con semánticas ACID, como todo o nada, aisladas de otras que ejecutan a la vez código y solicitudes de usuario. Si se producen excepciones por la ejecución en el servidor de código de aplicación JavaScript, la transacción entera se revierte.

### ¿Cuáles son los casos de uso típicos de DocumentDB?  
DocumentDB es una buena opción para nuevas aplicaciones web y móviles en las que la escala, el rendimiento y la capacidad de consulta a través de datos sin esquemas son importantes. Se presta a un desarrollo rápido y admite la iteración continua de modelos de datos de aplicaciones. [Casos de uso comunes de DocumentDB](documentdb-use-cases.md) son las aplicaciones que administran contenido y datos generados por el usuario.

### ¿Es DocumentDB compatible con HIPAA?
DocumentDB no es compatible actualmente con HIPAA; sin embargo, la conversión en un servicio de Azure compatible con HIPAA se encuentra dentro del mapa de ruta. Para más información sobre Microsoft e HIPAA, vea [Ley de HIPAA/HITECH](https://www.microsoft.com/es-ES/TrustCenter/Compliance/HIPAA).

### ¿Cuáles son los límites de escala de DocumentDB?
Las cuentas de DocumentDB se pueden escalar en términos de almacenamiento y rendimiento agregando colecciones. Vea [Límites de DocumentDB](documentdb-limits.md) para ver las cuotas de servicio del número de colecciones. Si necesita colecciones adicionales, [póngase en contacto con el soporte técnico](documentdb-increase-limits.md) para que le aumenten la cuota de la cuenta.

### ¿Cuánto cuesta DocumentDB de Microsoft Azure?
Consulte la página [Detalles de precios de DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402317) para detalles. Los cargos por uso de DocumentDB están determinados por la cantidad de colecciones en uso, el número de horas en que las colecciones estuvieron en línea y el [nivel de rendimiento](documentdb-performance-levels.md) de cada colección.

### ¿Existe una prueba gratuita disponible?
Si es la primera vez que usa Azure, regístrese para obtener una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/), que le ofrece 30 días y 200 USD para que pruebe todos los servicios de Azure. Si tiene una suscripción a Visual Studio, puede recibir [150 USD en créditos gratis de Azure al mes](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) para usarlos en cualquier servicio de Azure.

### ¿Cómo puedo obtener ayuda adicional con DocumentDB?
En caso de que necesite ayuda, póngase rápidamente en contacto con nosotros en [Desbordamiento de la pila](http://stackoverflow.com/questions/tagged/azure-documentdb), los [foros para desarrolladores de MSDN de Azure DocumentDB](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB) o programe un [chat 1:1 con el equipo de ingeniería de DocumentDB](http://www.askdocdb.com/). Para mantenerse al día en las novedades y características más recientes de DocumentDB, síganos en [Twitter](https://twitter.com/DocumentDB).

## Configuración de DocumentDB de Microsoft Azure

### ¿Cómo me registro en DocumentDB de Microsoft Azure?
Microsoft Azure DocumentDB está disponible en el nuevo [Portal de Azure][azure-portal]. Primero debe registrarse para una suscripción a Microsoft Azure. Una vez hecho esto, puede agregar una cuenta de DocumentDB a su suscripción a Azure. Para instrucciones sobre cómo agregar una cuenta de DocumentDB, vea [Creación de una cuenta de base de datos de DocumentDB](documentdb-create-account.md).

### ¿Qué es una clave maestra?
Una clave maestra es un token de seguridad para acceder a todos los recursos de una cuenta. Los individuos con esta clave tienen acceso de lectura y escritura a todos los recursos de la cuenta de base de datos. Tenga cuidado cuando distribuya claves maestras. La clave maestra principal y la secundaria están disponibles en la hoja **Claves ** del [Portal de Azure][azure-portal]. Para más información sobre las claves, vea [Visualización, copia y regeneración de las claves de acceso](documentdb-manage-account.md#keys).

### ¿Cómo se crea una base de datos?
Puede crear bases de datos mediante el [Portal de Azure]() como se describe en [Creación de una base de datos de DocumentDB](documentdb-create-database.md), uno de los [SDK de DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx) o a través de las [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).

### ¿Qué es una colección?
Una colección es un contenedor de documentos JSON asociado a la lógica de aplicación de JavaScript. Las consultas y transacciones se limitan a colecciones. Puede almacenar un conjunto de documentos JSON heterogéneos en una sola colección, todos los cuales se indexan automáticamente.

Las colecciones también son las entidades de facturación de DocumentDB. Los cargos mensuales por uso de DocumentDB están determinados por la cantidad de colecciones en uso, el número de horas en que las colecciones estuvieron en línea y el [nivel de rendimiento](documentdb-performance-levels.md) de cada colección. Para obtener más información, consulte [Precios de DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/).

### ¿Hay límites en las bases de datos y colecciones?
Cada colección viene con una asignación de almacenamiento de base de datos y rendimiento aprovisionado en uno de los [niveles de rendimiento](documentdb-performance-levels.md) admitidos. También existen cuotas para cada recurso administrado por el servicio. Para una lista de todos los límites, vea [Límites de DocumentDB](documentdb-limits.md). Para solicitar un cambio en los límites de la cuenta, consulte [Solicitud de aumento de los límites de la cuenta de DocumentDB](documentdb-increase-limits.md).

### ¿Cómo se configuran los usuarios y los permisos?
Puede crear usuarios y permisos mediante uno de los [SDK de DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx) o a través de las [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).

## Preguntas de bases de datos sobre el desarrollo con Microsoft Azure DocumentDB

### ¿Cómo se empieza a desarrollar con DocumentDB?
Los [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) están disponibles para NET, Python, Node.js, JavaScript y Java. Los desarrolladores pueden aprovechar también las [API de HTTP RESTful](https://msdn.microsoft.com/library/azure/dn781481.aspx) para interactuar con recursos de DocumentDB desde una variedad de plataformas y lenguajes.

Ejemplos de los SDK de DocumentDB [.NET](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](https://github.com/Azure/azure-documentdb-node/tree/master/samples) y [Python](https://github.com/Azure/azure-documentdb-python) se encuentran disponibles en GitHub.

### ¿Admite DocumentDB SQL?
El lenguaje de consulta SQL de DocumentDB proporciona operadores relacionales y jerárquicos y una extensibilidad a través de JavaScript según las funciones definidas por el usuario (UDF). La gramática JSON permite el modelado de documentos JSON como árboles con etiquetas a modo de nodos de árbol, algo que utilizan las técnicas de indexación automática de DocumentDB y el dialecto de consulta SQL de DocumentDB. Para obtener información detallada sobre el uso de la gramática SQL, consulte el artículo [DocumentDB de consulta][query].

### ¿Qué tipos de datos admite DocumentDB?
Los tipos de datos primitivos admitidos en DocumentDB son los mismos que en JSON. JSON posee un sencillo sistema de tipos que consta de cadenas, números (doble precisión IEEE754) y valores booleanos (verdadero, falso y nulos). Tipos de datos más complejos como DateTime, Guid, Int64 y geometría se pueden representar tanto en JSON como en DocumentDB mediante la creación de objetos anidados con el operador { } y matrices con el operador [ ].

### ¿De qué modo Base de datos de documentos proporciona simultaneidad?
Base de datos de documentos admite control de simultaneidad optimista (OCC) a través de etiquetas de entidad HTTP o ETag. Cada recurso de Base de datos de documentos tiene una ETag, y los clientes de Base de datos de documentos incluyen su última versión de lectura en las solicitudes de escritura. Si la etiqueta ETag es actual, el cambio se confirma. Si el valor ha cambiado externamente, el servidor rechaza la escritura con un código de respuesta de error de condición previa HTTP 412. Los clientes deben leer la última versión del recurso y reintentar la solicitud.

### ¿Cómo se realizan transacciones en Base de datos de documentos?
Base de datos de documentos admite transacciones integradas en el lenguaje a través de procedimientos almacenados y desencadenadores JavaScript. Todas las operaciones de base de datos dentro de los scripts se ejecutan bajo el aislamiento de instantáneas limitadas a la colección. Una instantánea de las versiones de documento (etiquetas ETag) se toma al comienzo de la transacción y solo se confirma si el script se ejecuta correctamente. Si el JavaScript produce un error, la transacción se revierte. Vea [Programación de servidor DocumentDB](documentdb-programming.md) para más detalles.

### ¿Cómo se pueden insertar documentos en masa en DocumentDB? 
Existen tres maneras de insertar documentos de forma masiva en DocumentDB:

- La herramienta de migración de datos, como se describe en [Importación de datos en DocumentDB](documentdb-import-data.md).
- El Explorador de documentos en el Portal de Azure, como se describe en [Adición en masa de documentos con el Explorador de documentos](documentdb-view-json-document-explorer.md#BulkAdd).
- Procedimientos almacenados, como se describe en [Programación de servidor DocumentDB](documentdb-programming.md).

### ¿Admite Base de datos de documentos el almacenamiento en caché de vínculos de recursos?
Sí. Como Base de datos de documentos es un servicio RESTful, los vínculos de recursos son inmutables y se pueden almacenar en caché. Los clientes de DocumentDB pueden especificar un encabezado "If-None-Match" para las lecturas con relación a cualquier recurso como documento o colección y actualizar sus copias locales solo cuando la versión del servidor cambie.




[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
 

<!---HONumber=AcomDC_0128_2016-->