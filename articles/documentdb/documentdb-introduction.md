<properties 
	pageTitle="Introducción a DocumentDB, una base de datos JSON | Microsoft Azure" 
	description="Obtenga información sobre Azure DocumentDB, una base de datos JSON NoSQL. Esta base de datos de documentos se ha creado para macrodatos, escalabilidad elástica y alta disponibilidad." 
	keywords="base de datos json, base de datos de documentos"
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
	ms.topic="get-started-article" 
	ms.date="02/16/2016" 
	ms.author="mimig"/>

# Introducción a DocumentDB: una base de datos de JSON NoSQL

DocumentDB es una base de datos de documentos NoSQL para soluciones de macrodatos que controlan datos JSON que requieren un escalado sencillo y una alta disponibilidad.

Una forma rápida de obtener información sobre esta base de datos de JSON y de verla en acción es seguir estos tres pasos:

1. Ver el vídeo de dos minutos [What is DocumentDB?](https://azure.microsoft.com/documentation/videos/what-is-azure-documentdb/) (¿Qué es DocumentDB?), que presenta las ventajas de usar DocumentDB.
2. Ver el vídeo de tres minutos[Create DocumentDB on Azure](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) (Creación de DocumentDB en Azure), que destaca cómo empezar a trabajar con DocumentDB mediante el Portal de Azure.
3. Visite [Query Playground](http://www.documentdb.com/sql/demo) (Área de juegos de consultas), donde puede recorrer distintas actividades para obtener información sobre la sofisticada funcionalidad de consulta disponible en DocumentDB. A continuación, diríjase a la pestaña Sandbox (Espacio aislado) y ejecute consultas SQL personalizadas y experimente con DocumentDB.

A continuación, vuelva a este artículo, donde profudizará y conocerá las respuestas a las preguntas siguientes:

-	[¿Qué es DocumentDB y qué valor ofrece a la nube y a las aplicaciones móviles?](#what-is-azure-documentdb)
-	[¿Cómo se administran los datos en DocumentDB y cómo se tiene acceso al servicio?](#data-management)
-	[¿Cómo puedo desarrollar aplicaciones con DocumentDB?](#develop)
-	[¿Cuáles son los pasos que debo seguir para crear una aplicación de DocumentDB?](#next-steps)  

## ¿Qué es la Base de datos de documentos de Azure?  

Las aplicaciones modernas producen, consumen y responden rápidamente a volúmenes de datos muy grandes. Estas aplicaciones evolucionan con mucha rapidez y lo mismo ocurre con el esquema de datos subyacente. En respuesta a esto, cada vez más desarrolladores optan por las bases de datos de documentos NoSQL libres de esquemas por ser soluciones sencillas, rápidas y escalables para almacenar y procesar datos, a la vez que permiten conservar la capacidad de iterar rápidamente sobre los modelos de datos de las aplicaciones y las fuentes de datos no estructuradas. Sin embargo, muchas bases de datos libres de esquemas no permiten realizar consultas complejas ni procesar transacciones, lo que dificulta la administración de datos avanzados. Esto es donde entra en juego la DocumentDB. Microsoft desarrolló DocumentDB para que cumpliera estos requisitos al administrar los datos de las aplicaciones actuales.

DocumentDB es un auténtico servicio de base de datos de documentos NoSQL sin esquemas diseñado para modernas aplicaciones web y móviles. La Base de datos de documentos ofrece de manera consistente capacidades de lectura y escritura rápidas, flexibilidad de esquemas, así como la capacidad de escalar fácilmente una base de datos en función de las necesidades. No asume ni requiere ningún esquema para los documentos de JSON que indexa. De forma predeterminada, indiza automáticamente todos los documentos de la base de datos y no espera ni requiere ningún esquema, ni tampoco la creación de índices secundarios. DocumentDB permite realizar consultas específicas complejas mediante lenguaje SQL. Asimismo, admite niveles de coherencia bien definidos y permite el uso de lenguaje JavaScript, además de procesar transacciones de varios documentos usando el modelo de programación conocido de procedimientos almacenados, desencadenadores y UDF.

Una base de datos de JSON, DocumentDB, admite de manera nativa documentos JSON, lo que permite una iteración sencilla de los esquemas de aplicaciones. Abarca la ubicuidad de JSON y JavaScript, lo que elimina la discordancia entre el esquema de base de datos y los objetos definidos de aplicación. La integración de JavaScript también permite a los desarrolladores ejecutar lógica de aplicación de manera eficaz y directa desde el motor de base de datos en una transacción de base de datos.

Azure DocumentDB ofrece las siguientes capacidades y ventajas clave:

-	**Consultas ad hoc con sintaxis SQL familiares:** almacene los diferentes documentos JSON en DocumentDB y consúltelos a través de una sintaxis SQL conocida. DocumentDB utiliza una tecnología de indexación estructurada mediante registros sin bloqueos y sumamente concurrente para indexar automáticamente el contenido de todo documento. Esto permite consultas enriquecidas en tiempo real sin necesidad de especificar consejos de esquemas, índices secundarios o vistas. Obtenga más información en [DocumentDB de consulta](documentdb-sql-query.md). 

-	**Ejecución de JavaScript en la base de datos**: expresa la lógica de las aplicaciones como procedimientos almacenados, desencadenadores y funciones definidas por el usuario (UDF) con JavaScript estándar. Esto permite que la lógica de su aplicación opere con datos sin preocuparse de la discordancia entre la aplicación y el esquema de la base de datos. DocumentDB proporciona una ejecución transaccional completa de la lógica de aplicaciones de JavaScript directamente dentro del motor de la base de datos. La integración profunda de JavaScript permite la ejecución de las operaciones INSERTAR, REEMPLAZAR, ELIMINAR y SELECCIONAR desde un programa JavaScript como una transacción aislada. Obtenga más información en [Programación de servidor DocumentDB](documentdb-programming.md).

-	**Niveles de coherencia ajustables**: seleccione entre cuatro niveles de coherencia bien definidos para lograr un equilibrio óptimo entre la coherencia y el rendimiento. Para las consultas y las operaciones de lectura, DocumentDB ofrece cuatro niveles de coherencia diferentes: fuerte, obsolescencia entrelazada, sesión y eventual. Estos niveles de coherencia bien definidos y pormenorizados le permiten realizar equilibrios profundos entre la coherencia, la disponibilidad y la latencia. Obtenga más información en [Uso de los niveles de coherencia para maximizar la disponibilidad y el rendimiento en DocumentDB](documentdb-consistency-levels.md).

-	**Completamente administrados**: elimine la necesidad de administrar recursos de bases de datos y máquinas. Como un servicio de Microsoft Azure completamente administrado, no es necesario que administre las máquinas virtuales, administrar y configurar software o realizar complejas actualizaciones a nivel de datos. Se realiza una copia de seguridad de cada base de datos además de protegerlas contra fallas regionales. Puede agregar fácilmente una cuenta de DocumentDB y aprovisionar capacidad a medida que la necesita, lo que le permite concentrarse en su aplicación en lugar de la operación y administración de la base de datos.

-	**Rendimiento y almacenamiento escalable de manera flexible**: escale o reduzca verticalmente su base de datos de JSON DocumentDB para cubrir sus necesidades de aplicaciones. El escalado se realiza a través de unidades detalladas (colecciones) del almacenamiento y rendimiento reservados en copia de seguridad en SSD. Puede escalar de manera flexible DocumentDB con un rendimiento predecible al crear más unidades, a medida que su aplicación crece.

-	**Diseño abierto**: comience rápidamente con las destrezas y herramientas existentes. La programación de DocumentDB es sencilla, asequible y no requiere la adopción de herramientas nuevas o de extensiones personalizadas de JSON o JavaScript. Puede tener acceso a todas las funciones de la base de datos incluido el procesamiento de CRUD, las consultas y JavaScript en una interfaz RESTful HTTP sencilla. DocumentDB abarca formatos, lenguajes y estándares actuales y, además, ofrece capacidades de base de datos de alto valor.

Puede utilizar DocumentDB para almacenar conjuntos de datos flexibles que requieren procesamiento transaccional y recuperación de la consulta. Los escenarios de aplicación pueden incluir datos de usuario para las aplicaciones web y móviles interactivas, además del almacenamiento, la recuperación y el procesamiento de datos JSON de la aplicación. Una base de datos puede almacenar cualquier número de documentos JSON, DocumentDB es ideal para aplicaciones que se ejecutan en Internet.

##<a name="data-management"></a>Recursos de Azure DocumentDB
Azure DocumentDB administra datos a través de recursos de bases de datos bien definidos. Estos recursos se replican para ofrecer elevados niveles de disponibilidad y solo se pueden direccionar a través de su URI lógico. DocumentDB ofrece un modelo de programación RESTful basado en HTTP sencillo para todos los recursos.

La cuenta de base de datos de DocumentDB es un espacio de nombres único que le proporciona acceso a Azure DocumentDB. Para poder crear una cuenta de base de datos, debe disponer de una suscripción de Azure, que proporciona acceso a una variedad de servicios de Azure.

Todos los recursos de DocumentDB se modelan y almacenan como documentos JSON. Los recursos se administran como elementos, que son documentos JSON que contienen metadatos, y como fuentes que son recopilaciones de elementos. Las fuentes respectivas contienen los conjuntos de elementos.

La imagen siguiente muestra las relaciones entre los recursos de DocumentDB:

![La relación jerárquica entre los recursos de DocumentDB, una base de datos JSON NoSQL][1]

Una cuenta de base de datos consta de un conjunto de bases de datos, cada una con varias recopilaciones. Cada recopilación puede contener, a su vez, procedimientos almacenados, desencadenadores, UDF, documentos y datos adjuntos relacionados. Una base de datos también tiene usuarios asociados, cada uno con un conjunto de permisos que proporciona acceso a diferentes recopilaciones, procedimientos almacenados, desencadenadores, UDF, documentos o datos adjuntos. Mientras que las bases de datos, los usuarios, permisos y las recopilaciones son recursos definidos por el sistema con esquemas conocidos, los documentos, procedimientos almacenados, desencadenadores, UDF y datos adjuntos contienen contenido JSON arbitrario y definido por el usuario.

##<a name="develop"></a> Desarrollo con Azure DocumentDB
DocumentDB expone recursos mediante la API de REST, que se puede invocar con cualquier lenguaje capaz de realizar solicitudes de HTTP/HTTPS. Además, la Base de datos de documentos ofrece bibliotecas de programación para varios lenguajes. Estas bibliotecas simplifican muchos aspectos de trabajar con la Base de datos de documentos de Azure al controlar detalles como el almacenamiento en caché de las direcciones, la administración de excepciones, los intentos automáticos, etc. Actualmente hay bibliotecas disponibles para los siguientes lenguajes y plataformas:

Descargar | Documentación
--- | ---
[.NET SDK](http://go.microsoft.com/fwlink/?LinkID=402989) | [Biblioteca de .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)
[SDK de Node.js](http://go.microsoft.com/fwlink/?LinkID=402990) | [Biblioteca de Node.js](http://azure.github.io/azure-documentdb-node/)
[SDK de Java](http://go.microsoft.com/fwlink/?LinkID=402380) | [Biblioteca de Java](http://azure.github.io/azure-documentdb-java/)
[SDK de JavaScript](http://go.microsoft.com/fwlink/?LinkID=402991) | [Biblioteca de JavaScript](http://azure.github.io/azure-documentdb-js/)
N/D | [SDK del servidor de JavaScript](http://azure.github.io/azure-documentdb-js-server/)
[SDK de Python](https://pypi.python.org/pypi/pydocumentdb) | [Biblioteca de Python](http://azure.github.io/azure-documentdb-python/)

Además de las operaciones básicas Crear, Leer, Actualizar y Eliminar, DocumentDB proporciona una sofisticada interfaz de consulta SQL para recuperar documentos JSON y soporte del servidor para la ejecución transaccional de la lógica de aplicaciones de JavaScript. Las interfaces de ejecución de consultas y script están disponibles a través de todas las bibliotecas de las plataformas además de las API REST.

### Consulta SQL
Azure DocumentDB permite la realización de consultas de documentos mediante lenguaje SQL, que se basa en el sistema de tipos de JavaScript, y con expresiones que admiten las consultas jerárquicas avanzadas. El lenguaje de consulta de DocumentDB es una interfaz sencilla pero poderosa para consultar documentos JSON. El lenguaje admite un subconjunto de gramática ANSI SQL e incorpora una integración profunda del objeto de JavaScript, matrices, construcción de objetos e invocación de funciones. DocumentDB proporciona su modelo de consulta sin ningún esquema explícito o consejo de indexación por parte del desarrollador.

Las funciones definidas por el usuario (UDF) se pueden registrar con DocumentDB a las que se hace referencia como parte de una consulta SQL, con lo que se extiende la gramática a una lógica de aplicación personalizada compatible. Estas UDF se crean como programas de JavaScript y se ejecutan en la base de datos.

Para desarrolladores. NET, DocumentDB también ofrece un proveedor de consultas LINQ como parte del [SDK de. NET](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx).

### Transacciones y ejecución de JavaScript
Azure DocumentDB permite crear lógica de aplicaciones como programas con nombres escritos completamente en JavaScript. Estos programas se registran para una recopilación y pueden emitir operaciones de base de datos en los documentos dentro de una recopilación determinada. JavaScript se puede registrar para la ejecución como desencadenador, procedimiento almacenado o función definida por el usuario. Los desencadenadores y procedimientos almacenados pueden crear, leer, actualizar y eliminar documentos; mientras que las funciones definidas por el usuario se ejecutan como parte de la lógica de ejecución de consulta sin acceso de escritura a la colección.

La ejecución de JavaScript en DocumentDB se modela de acuerdo con los conceptos admitidos por los sistemas de base de datos relacionales, con JavaScript como un reemplazo moderno de Transact-SQL. Toda la lógica de JavaScript se ejecuta en un entorno de transacciones ACID con aislamiento de la instantánea. Durante el transcurso de esta ejecución, si JavaScript lanza una excepción, entonces se cancela toda la transacción.

## Pasos siguientes
Si ya tiene una cuenta de Azure, puede empezar a trabajar con DocumentDB en el [Portal de Azure](https://portal.azure.com/#gallery/Microsoft.DocumentDB) mediante la [creación una cuenta de base de datos de DocumentDB](documentdb-create-account.md).

Si no tiene una cuenta de Azure, puede:

- Si es la primera vez que usa Azure, regístrese para una [prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/), que le proporciona 30 días y 200 USD para que pruebe todos los servicios de Azure. 
- Si tiene una suscripción a MSDN, puede recibir [150 USD en créditos gratis de Azure al mes](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) para usarlos en cualquier servicio de Azure. 

A continuación, cuando esté listo para obtener más información, visite nuestra [ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/documentdb/) para navegar por todos los recursos de aprendizaje disponibles.


[1]: ./media/documentdb-introduction/json-database-resources1.png
 

<!---HONumber=AcomDC_0302_2016-->