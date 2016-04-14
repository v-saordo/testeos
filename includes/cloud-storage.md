#Administración de datos y análisis de negocios

Es tan importante administrar y analizar los datos que están en la nube como los que están en cualquier otro sitio. Para facilitarle estas tareas, Azure proporciona una gama de tecnologías aptas para trabajar con datos relacionales y no relacionales. Este artículo presenta todas estas opciones.

##Tabla de contenido      

- [Almacenamiento de blobs](#blob)
- [Ejecución de un DBMS en una máquina virtual](#dbinvm)
- [Base de datos SQL](#sqldb)
	- [SQL Data Sync](#datasync)
	- [SQL Data Reporting con máquinas virtuales](#datarpt)
- [Almacenamiento de tablas](#tblstor)
- [Hadoop](#hadoop)

## <a name="blob"></a>Almacenamiento de blobs

La palabra "blob" es la forma abreviada de "Binary Large Object" (objeto binario grande) y describe exactamente lo que es: una colección de información binaria. Si bien son simples, los blobs también son muy útiles. La [ilustración 1](#Fig1) muestra los fundamentos del almacenamiento de blobs de Azure.

<a name="Fig1"></a>![Diagrama de blobs][blobs]
 
**Ilustración 1: Almacenamiento de blobs de Azure almacena datos binarios (blobs) en contenedores.**

Para usar blobs, en primer lugar debe crear una *cuenta de almacenamiento* de Azure. Como parte de este proceso, debe especificar el centro de datos de Azure que almacenará los objetos que cree con esta cuenta. Cualquiera que sea su ubicación, todos los blobs que cree pertenecerán a algún contenedor de su cuenta de almacenamiento. Para tener acceso a un blob, una aplicación proporciona a una URL el formulario:

http://&lt;*StorageAccount*&gt;.blob.core.windows.net/&lt;*Container*&gt;/&lt;*BlobName*&gt;

&lt;*StorageAccount*&gt; es un identificador único que se asigna cuando se crea una nueva cuenta de almacenamiento, mientras que &lt;*Container*&gt; y &lt;*BlobName*&gt; son los nombres de un contenedor específico y un blob de dicho contenedor.

Azure proporciona dos tipos de blobs diferentes. Las opciones son las siguientes:

- Blobs en *bloques*, cada uno de los cuales puede contener hasta 200 gigabytes de datos. Tal y como recomienda su nombre, un blob en bloques está subdividido en un número de bloques indeterminado. Si se produce un error durante la transferencia de un blob en bloques, es posible reanudar la retransmisión con los bloques más recientes en vez de enviar de nuevo todo el blob. Los blobs en bloques constituyen un método de almacenamiento bastante general y son el tipo de blob más utilizado en la actualidad.

- Blobs en *páginas*, que pueden contener hasta un terabyte cada uno. Los blobs en páginas se han diseñado para proporcionar un acceso aleatorio y cada uno está dividido en un número de páginas indeterminado. Las aplicaciones tienen permiso de lectura y escritura de páginas individuales de forma aleatoria en el blob. En el modelo de ejecución Máquinas virtuales de Azure, por ejemplo, las máquinas virtuales creadas usan blobs en páginas a modo de almacenamiento permanente tanto para los discos de sistemas operativos como para los discos de datos.

Tanto si elige blobs en bloques como blobs en páginas, las aplicaciones pueden tener acceso a los datos del blob de diversas formas. Están disponibles las siguientes opciones:

- Directamente a través de un protocolo de acceso RESTful (por ejemplo, basado en HTTP). Tanto las aplicaciones de Azure como las aplicaciones externas, incluidas las aplicaciones ejecutadas en el entorno local, pueden usar esta opción.
- Mediante la biblioteca del Cliente de almacenamiento de Azure, que proporciona una interfaz más intuitiva para los desarrolladores sobre el protocolo de acceso a blobs de tipo RESTful. Una vez más, tanto las aplicaciones de Azure como las aplicaciones externas pueden tener acceso a blobs utilizando esta biblioteca.
- Mediante las unidades de Azure, opción que permite que una aplicación de Azure trate un blob en páginas como una unidad local con un sistema de archivos NTFS. Para la aplicación, el blob en páginas es similar a un sistema de archivos normal de Windows al que se accede mediante E/S de archivos estándar. De hecho, las lecturas y escrituras se envían al blob en páginas subyacente que implementa la unidad de Azure. 

Para protegerlos de errores de hardware y mejorar su disponibilidad, todos los blobs se replican en tres equipos de un centro de datos de Azure. La escritura en un blob actualiza las tres copias para que las lecturas posteriores no obtengan resultados incoherentes. También puede especificar que los datos de un blob se copien a otro centro de datos de Azure que esté ubicado en la misma región geográfica pero a una distancia mínima de 500 millas. Este proceso de copiado, denominado *replicación geográfica*, se realiza pocos minutos después de una actualización del blob y es útil para la recuperación ante desastres.

También es posible hacer públicos los datos de los blobs a través de la *Red de entrega de contenido (CDN)* de Azure. Mediante el almacenamiento en caché de las copias de los datos de los blobs en decenas de servidores de todo el mundo, la red CDN puede agilizar el acceso a la información de uso más frecuente.

Si bien son sencillos, los blobs son la elección adecuada en muchas situaciones. El almacenamiento y la transmisión de vídeo y audio son ejemplos obvios, así como las copias de seguridad y otros tipos de archivado de datos. Los desarrolladores también pueden usar blobs para mantener cualquier clase de datos no estructurados que deseen. Disponer de una forma directa de almacenar y tener acceso a datos binarios puede ser sorprendentemente útil.


## <a name="dbinvm"></a>Ejecución de un DBMS en una máquina virtual

Muchas aplicaciones actuales usan algún tipo de sistema de administración de bases de datos (DBMS). Los sistemas relacionales, como SQL Server, son la opción más utilizada, pero los enfoques no relacionales, comúnmente conocidos como tecnologías *NoSQL*, son cada día más populares. Para que las aplicaciones en la nube usen estas opciones de administración de datos, el modelo de ejecución Máquinas virtuales de Azure le permite ejecutar un sistema DBMS (relacional o NoSQL) en una máquina virtual. La [ilustración 2](#Fig2) muestra su apariencia en SQL Server.

<a name="Fig2"></a>![Diagrama de SQL Server en una máquina virtual][SQLSvr-vm]
 
**Ilustración 2: el modelo de ejecución de Máquinas virtuales de Azure permite ejecutar un DBMS en una máquina virtual, con persistencia proporcionada por los blobs.**

Tanto para los desarrolladores como para los administradores de las bases de datos, este escenario se asemeja en gran medida a la ejecución del mismo software en su propio centro de datos. En el ejemplo que aquí se muestra, entre otros posibles, se pueden usar casi todas las funciones de SQL Server y se proporciona un acceso total de administrador al sistema. Por supuesto, también tiene la responsabilidad de administrar el servidor de la base de datos, al igual que si se estuviera ejecutando en un entorno local.

Tal como se muestra en la [ilustración 2](#Fig2), las bases de datos parecen estar almacenadas en el disco local de la máquina virtual en que se ejecuta el servidor. Sin embargo, a un nivel más profundo, cada uno de esos discos se escribe en un blob de Azure. (Es similar a utilizar un SAN en su propio centro de datos, con un blob ejerciendo más o menos las funciones de un LUN). Al igual que con cualquier blob de Azure, los datos que contiene se replican tres veces en un centro de datos y, si el usuario lo solicita, se replica geográficamente a otro centro de datos ubicado en la misma región. También es posible usar opciones como la creación de reflejo de una base de datos SQL Server para mejorar su fiabilidad.

Otra forma de usar SQL Server en una máquina virtual consiste en crear una aplicación híbrida, en la que los datos se alojen en Azure mientras que la parte lógica de la aplicación se ejecuta en el entorno local. Por ejemplo, podría ser más lógico usar esta forma con las aplicaciones que se ejecutan en múltiples ubicaciones o en varios dispositivos móviles que deben compartir los mismos datos. Para facilitar la comunicación entre la base de datos en la nube y la parte lógica en el entorno local, una organización puede usar el modelo de ejecución Red virtual de Azure para crear una conexión de red privada virtual (VPN) entre un centro de datos de Azure y su propio centro de datos en el entorno local.


## <a name="sqldb"></a>Base de datos SQL

Para muchas personas, ejecutar un sistema DBMS en una máquina virtual es la primera opción que viene a la mente para administrar datos estructurados en la nube. Si bien no es la única posibilidad, tampoco es siempre la mejor opción. En algunos casos, es más lógico administrar los datos mediante un modelo de plataforma como servicio (PaaS). Azure proporciona una tecnología PaaS denominada Base de datos SQL, que le permite tratar los datos relacionales de este modo. La [ilustración 3](#Fig3) muestra esta opción.

<a name="Fig3"></a>![Diagrama de Base de datos SQL][SQL-db]
 
**Ilustración 3: Base de datos SQL proporciona un servicio de almacenamiento relacional PaaS compartido.**

El modelo de ejecución Base de datos SQL no proporciona a cada cliente su propia instancia física de SQL Server. En su lugar, ofrece un servicio multiinquilino, con un servidor de Base de datos SQL lógico para cada cliente. Todos los clientes comparten la capacidad de almacenamiento y proceso que proporciona el servicio. Y al igual que con el almacenamiento de blobs, todos los datos de Base de datos SQL se almacenan en tres equipos independientes dentro de un centro de datos de Azure, por lo que proporcionan a sus bases de datos una alta disponibilidad (HA) integrada.

Para una aplicación, el modelo de ejecución Base de datos SQL se asemeja en gran medida a SQL Server. Las aplicaciones pueden generar consultas de SQL con tablas relacionales, usar procedimientos almacenados en T-SQL y efectuar transacciones en múltiples tablas. Y dado que las aplicaciones tienen acceso a Base de datos SQL mediante el protocolo de secuencia de datos tubular (TDS), que es el mismo protocolo utilizado para tener acceso a SQL Server, pueden trabajar con datos mediante Entity Framework, AD.NET, JDBC y otras interfaces de acceso de datos conocidas.

Pero dado que Base de datos SQL es un servicio en la nube que se ejecuta en los centros de datos de Azure, no es necesario administrar ninguno de los aspectos físicos del sistema, como el uso del disco. Tampoco es necesario preocuparse de las actualizaciones de software o el tratamiento de otras tareas administrativas de bajo nivel. La organización de cada cliente sigue controlando su propia base de datos y, por supuesto, manteniendo sus esquemas e inicios de sesión de usuario, pero muchas tareas administrativas antes realizadas por personas ahora son automáticas.

Mientras que el modelo de ejecución Base de datos SQL se asemeja mucho a SQL Server en lo referente a las aplicaciones, no se comporta exactamente igual que la ejecución de un sistema DBMS en una máquina física o virtual. Puesto que se ejecuta en un hardware compartido, su rendimiento varía en función de la carga colocada en ese hardware por parte de todos sus clientes. Por este motivo, el rendimiento de lo que podría denominarse un procedimiento almacenado en Base de datos SQL podría variar de un día para otro.

Actualmente, el modelo de ejecución Base de datos SQL le permite crear una base de datos de hasta 150 gigabytes. Si necesita trabajar con bases de datos de mayor tamaño, el servicio proporciona una opción llamada *Federación*. Para ello, el administrador de una base de datos crea dos o más *miembros de la federación*, cada uno de los cuales es una base de datos independiente con su propio esquema. Los datos se extienden por estos miembros, proceso que suele denominarse *particionamiento*, y a cada miembro se le asigna una *clave de federación* distinta. Una aplicación genera una consulta SQL con estos datos especificando la clave de federación que identifica al miembro de la federación al que debe dirigirse la consulta. De este modo, es posible usar un modelo relacional tradicional con grandes cantidades de datos. Al igual que siempre, existen reglas; por ejemplo, ni las consultas ni las transacciones pueden ampliar los miembros de la federación. Pero si un servicio PaaS relacional es la mejor opción y estas reglas son aceptables, usar SQL Federation puede ser una buena solución.

El modelo de ejecución Base de datos SQL puede ser utilizado por aplicaciones que se estén ejecutando en Azure o en cualquier otro medio, como su propio centro de datos en el entorno local. Gracias a ello, resulta útil para aplicaciones en la nube que necesiten datos relacionales, así como aplicaciones en el entorno local que puedan beneficiarse del almacenamiento de datos en la nube. Una aplicación móvil podría utilizar el modelo de ejecución Base de datos SQL para administrar los datos relacionales compartidos, por ejemplo, al igual que podría utilizarlo una aplicación de inventario que se ejecute en múltiples distribuidores de todo el mundo.

Pensar en Base de datos SQL hace surgir un problema obvio (e importante): ¿Cuándo se debe ejecutar SQL Server en una máquina virtual y cuándo Base de datos SQL es una mejor opción? Como de costumbre, existen reglas, por lo que la designación del mejor modelo depende de sus requisitos.

Una forma sencilla de recordarlo consiste en considerar que el modelo de ejecución Base de datos SQL se utiliza para aplicaciones nuevas, mientras que SQL Server en una máquina virtual es la mejor opción para desplazar una aplicación existente en el entorno local a la nube. No obstante, también puede resultar útil reflexionar sobre esta decisión de manera más detallada. Por ejemplo, el modelo de ejecución Base de datos SQL es más fácil de usar, puesto que los procesos de instalación y administración son mínimos. Sin embargo, la ejecución de SQL Server en una máquina virtual puede tener un rendimiento más predecible, dado que no es un servicio compartido, y además admite bases de datos no federadas más grandes que las compatibles con el modelo de ejecución Base de datos SQL. No obstante, el modelo de ejecución Base de datos SQL proporciona una replicación integrada tanto de los datos como del procesamiento, por lo que proporciona eficazmente un sistema DBMS de alta disponibilidad con muy poco trabajo. Mientras que SQL Server le ofrece un mayor control un conjunto más amplio de opciones, el modelo de ejecución Base de datos SQL se configura con más facilidad y se administra con una cantidad de trabajo considerablemente inferior.

Finalmente, es importante destacar que el modelo de ejecución Base de datos SQL no es el único servicio de datos PaaS disponible en Azure. Los asociados de Microsoft ofrecen igualmente otras opciones. Por ejemplo, ClearDB ofrece un PaaS MySQL, mientras que Cloudant ofrece una opción NoSQL. Los servicios de datos PaaS son la solución adecuada en muchas situaciones, por lo que este modelo de administración de datos es una parte importante de Azure.


### <a name="datasync"></a>SQL Data Sync

Mientras que el modelo de ejecución Base de datos SQL mantiene tres copias de cada base de datos en un solo centro de datos de Azure, no copia automáticamente los datos entre los centros de datos de Azure. En su lugar, proporciona el servicio SQL Data Sync, que el usuario puede utilizar para ello. La [ilustración 4](#Fig4) muestra su apariencia.

<a name="Fig4"></a>![Diagrama de SQL Data Sync][SQL-datasync]
 
**Ilustración 4: SQL Data Sync sincroniza los datos en el modelo de ejecución Base de datos SQL con los datos en otros centros de datos de Azure y del entorno local.**

Tal y como muestra el diagrama, SQL Data Sync puede sincronizar datos en diferentes ubicaciones. Se supone que está ejecutando una aplicación en múltiples centros de datos de Azure, por ejemplo, con datos almacenados en Base de datos SQL. Puede usar SQL Data Sync para mantener los datos sincronizados. SQL Data Sync también puede sincronizar datos entre un centro de datos de Azure y una instancia de SQL Server que se esté ejecutando en un centro de datos del entorno local. Esto podría resultar útil para mantener tanto una copia local de los datos utilizados por las aplicaciones del entorno local como una copia en la nube, utilizada por las aplicaciones que se ejecutan en Azure. Y aunque no se muestra en la ilustración, SQL Data Sync también se puede utilizar para sincronizar datos entre los modelo de ejecución Base de datos SQL y SQL Server que se estén ejecutando en una máquina virtual en Azure o en cualquier otro sitio.

La sincronización puede ser bidireccional y permite determinar exactamente los datos sincronizados y la frecuencia de sincronización. (La sincronización entre bases de datos no es instantánea: siempre existe al menos una breve demora). Y aunque utiliza código, el establecimiento de la sincronización con SQL Data Sync está totalmente orientado a la configuración: no es necesario escribir código.


### <a name="datarpt"></a>SQL Data Reporting con máquinas virtuales

Cuando una base de datos contenga datos, es probable que alguien quiera crear informes con esos datos. Azure puede ejecutar SQL Server Reporting Services (SSRS) en el modelo de ejecución Máquinas virtuales de Azure, que equivale funcionalmente a ejecutar SQL Server Reporting Services en el entorno local. A partir de entonces puede usar SSRS para generar informes sobre los datos almacenados en un modelo de ejecución Base de datos SQL de Azure. La [ilustración 5](#Fig5) muestra el funcionamiento del proceso.

<a name="Fig5"></a>![Diagrama de SQL Reporting][SQL-report]
 
**Ilustración 5: la ejecución de SQL Server Reporting Services en Máquinas virtuales de Azure proporciona servicios de generación de informes de los datos contenidos en Base de datos SQL.**

Antes de que un usuario pueda ver un informe, es necesario que alguien defina la apariencia de ese informe (paso 1). Con SSRS en una máquina virtual, esto se puede hacer con una de dos herramientas: SQL Server Data Tools, que forma parte de SQL Server 2012, o su predecesor, Business Intelligence (BI) Development Studio. Al igual que con SSRS, las definiciones de estos informes se expresan en lenguaje RDL. Una vez creados los archivos RDL para un informe, se tienen que cargar en una máquina virtual en la nube (paso 2). La definición del informe ya está lista para el uso.

A continuación, un usuario de la aplicación obtiene acceso al informe (paso 3). Esta aplicación pasa esta solicitud a la máquina virtual de SSRS (paso 4), que contacta con el modelo de ejecución Base de datos SQL u otra fuente de datos para conseguir los datos que necesita (paso 5). SSRS usa estos datos y los archivos RDL correspondientes para generar el informe (paso 6) y, a continuación, devuelve el informe a la aplicación (paso 7), que se lo muestra al usuario (paso 8).

La incrustación de un informe en una aplicación, que es el escenario que se muestra aquí, no es la única opción. También es posible visualizar informes en el Administrador de informes en la máquina virtual, SharePoint en la máquina virtual o en otros medios. Los informes también se pueden combinar incluyendo en un informe un enlace a otro informe.

SSRS en una máquina virtual de Azure proporciona toda la funcionalidad de una solución de generación de informes en la nube. Los informes pueden usar cualquier fuente de datos que sea compatible con SSRS. Las aplicaciones y los informes pueden incluir código incrustado o ensamblados para que sean compatibles con comportamientos personalizados. La ejecución y generación de informes es rápida porque el contenido y el motor del servidor de informes se ejecutan juntos en el mismo servidor virtual.



## <a name="tblstor"></a>Almacenamiento de tablas

Los datos relacionales son útiles en muchas situaciones, pero no siempre son la elección adecuada. En el caso de que su aplicación necesite tener un acceso rápido y sencillo a cantidades muy grandes de datos poco estructurados, por ejemplo, una base de datos relacionales, podrían no funcionar bien. Es posible que una tecnología NoSQL sea la mejor opción.

El almacenamiento de tablas de Azure es un ejemplo de este tipo de modelo NoSQL. A pesar de su nombre, el almacenamiento de tablas no es compatible con tablas relacionales estándar. En su lugar, proporciona lo que se conoce como *almacén de claves/valores*, para asociar un conjunto de datos a una clave en particular y, a continuación, permitir el acceso de una aplicación a esos datos mediante el uso de la clave. La [ilustración 6](#Fig6) muestra los conceptos básicos.

<a name="Fig6"></a>![Diagrama del almacenamiento de tablas][SQL-tblstor]
 
**Ilustración 6: el almacenamiento de tablas de Azure es un almacén de claves/valores que proporciona un acceso rápido y sencillo a grandes cantidades de datos.**

Al igual que los blobs, cada tabla se asocia a una cuenta de almacenamiento de Azure. Las tablas también se denominan de un modo muy similar a los blobs, con una URL del formulario.

http://&lt;*StorageAccount*&gt;.table.core.windows.net/&lt;*TableName*&gt;

Tal y como muestra la ilustración, cada tabla está dividida en un número indeterminado de particiones, cada una de las cuales se puede almacenar en una máquina diferente. (Este es un formulario de particionamiento, al igual que con SQL Federation). Tanto las aplicaciones de Azure como las aplicaciones que se ejecutan en cualquier otro sitio pueden tener acceso a una tabla con el protocolo OData RESTful o la biblioteca del Cliente de almacenamiento de Azure.

Cada partición de una tabla incluye un número indeterminado de *entidades*, cada una de las cuales contiene 255 *propiedades*. Cada propiedad tiene un nombre, un tipo (como Binary, Bool, DateTime, Int, o String) y un valor. A diferencia del almacenamiento relacional, estas tablas no tienen esquemas fijos, por lo que las entidades diferentes de la misma tabla pueden contener propiedades con diferentes tipos. Una entidad podría tener solo una propiedad String que contuviera un nombre, por ejemplo, mientras que otra entidad de la misma tabla tuviera dos propiedades Int que contuvieran un número de identificación de cliente y una calificación de solvencia crediticia.

Para identificar una entidad en particular dentro de una tabla, existe una aplicación que proporciona la clave de esa entidad. La clave consta de dos partes: una *clave de partición*, que identifica una partición específica, y una *clave de fila*, que identifica una entidad dentro de esa partición. En la [ilustración 6](#Fig6), por ejemplo, el cliente solicita la entidad con clave de partición A y clave de fila 3 y el almacenamiento de tablas devuelve esa entidad, incluidas todas las propiedades que contiene.

Esta estructura permite que las tablas sean grandes (una tabla sencilla puede contener hasta 100 terabytes de datos), y proporciona un acceso rápido a los datos que contiene. No obstante, también tiene limitaciones. Por ejemplo, no admite las actualizaciones transaccionales que amplían las tablas ni las particiones en una única tabla. Solo es posible agrupar un conjunto de actualizaciones para una tabla en una transacción instantánea si todas las entidades involucradas están en la misma partición. Además, no es posible consultar a una tabla a partir del valor de sus propiedades ni existe compatibilidad de uniones en múltiples tablas. Y, a diferencia de las bases de datos relacionales, las tablas no son compatibles con los procedimientos almacenados.

El almacenamiento de tablas de Azure es una buena elección para las aplicaciones que necesitan tener un acceso rápido y económico a grandes cantidades de datos poco estructurados. Por ejemplo, una aplicación en Internet que almacene información de perfiles de muchos usuarios podría usar tablas. En una situación como esta, es importante tener un acceso rápido, y es probable que la aplicación no necesite todo el potencial de SQL. Privarse de esta función para conseguir más velocidad y tamaño puede, a veces, ser lo más lógico, por lo que el almacenamiento de tablas es la solución adecuada para algunos problemas.


## <a name="hadoop"></a>Hadoop

Las organizaciones han estado compilando almacenes de datos durante décadas. Estas recopilaciones de información, que suelen almacenarse en tablas relacionales, permiten a las personas trabajar con esos datos y aprender de ellos de muchas formas diferentes. Para ello, en SQL Server, por ejemplo, es habitual usar herramientas como SQL Server Analysis Services.

Pero supongamos que desea analizar datos no relacionales. Los datos pueden adoptar numerosas formas: información recopilada de sensores o etiquetas RFID, archivos de registro en granjas de servidores, datos procedentes de la secuencia de clics que se produce en aplicaciones web, imágenes de dispositivos de diagnóstico médico, etc. Estos datos también podrían ser demasiado grandes para ser utilizados de forma eficaz en un almacén de datos tradicional. Los problemas relacionados con datos de gran tamaño como estos, escasos tan solo hace unos pocos años, ahora son bastante habituales.

Para analizar este tipo de datos de gran tamaño, los agentes de nuestro sector coinciden ampliamente en una única solución: la tecnología de código abierto Hadoop. Hadoop se ejecuta en un clúster de máquinas físicas o virtuales para distribuir los datos con los que trabaja entre esas máquinas y procesarlos en paralelo. Cuantas más máquinas utiliza Hadoop, más rápido puede completar cualquier trabajo que deba realizar.

Este tipo de problema es un paso natural de la nube pública. En lugar de mantener un ejército de servidores en un entorno local que podría permanecer inactivo mucho tiempo, ejecutar Hadoop en la nube le permite crear máquinas virtuales (y pagar por ellas) solo cuando las necesita. Es más, una parte cada vez mayor de los datos de gran tamaño que desee analizar con Hadoop se crea directamente en la nube, por lo que se ahorra la molestia de tener que desplazarlos de un sitio a otro. Para ayudarle a explotar estas sinergias, Microsoft proporciona el servicio Hadoop en Azure. La [ilustración 7](#Fig7) muestra los componentes más importantes de este servicio.

<a name="Fig7"></a>![Diagrama de Hadoop][hadoop]

**Ilustración 7: Hadoop en Azure ejecuta trabajos de MapReduce que procesan los datos en paralelo mediante varias máquinas virtuales.**

Para usar Hadoop en Azure, debe solicitar primero esta plataforma en la nube para crear un clúster Hadoop, especificando el número de máquinas virtuales que necesita. La instalación de un clúster de Hadoop sin ayuda de nadie no es una tarea fácil, por lo que lo más práctico es dejar que Azure se encargue de ello. Cuando termine de trabajar con el clúster, tan solo tiene que apagarlo. No es necesario que pague por recursos informáticos que no está utilizando.

Una aplicación Hadoop, normalmente denominada un *trabajo*, usa un modelo de programación denominado *MapReduce*. Tal y como muestra la ilustración, la parte lógica de un trabajo de MapReduce se ejecuta simultáneamente en muchas máquinas virtuales. Al procesar los datos en paralelo, Hadoop puede analizar los datos mucho más deprisa que las soluciones basadas en máquinas independientes.

En Azure, los datos con los que utiliza un trabajo de MapReduce se mantienen almacenados normalmente en blobs. En los trabajos de MapReduce en Hadoop, sin embargo, se espera que los datos se almacenen en el *Sistema de archivos distribuido de Hadoop(HDFS)*. El sistema HDFS se asemeja en cierta medida al almacenamiento en blobs; por ejemplo, replica los datos en múltiples servidores físicos. En lugar de duplicar esta funcionalidad, Hadoop en Azure expone el almacenamiento en blobs a través de la API del sistema HDFS, tal y como muestra la ilustración. Mientras que la parte lógica de un trabajo de MapReduce cree que está teniendo un acceso ordinario a los archivos del sistema HDFS, el trabajo, en realidad, está utilizando los datos que se transfieren desde los blobs. Si se ejecutan varios trabajos con los mismos datos, Hadoop en Azure también permite copiar datos de blobs al sistema HDFS completo, que se ejecuta en las máquinas virtuales.

En la actualidad, los trabajos de MapReduce se escriben normalmente en Java, modelo compatible con Hadoop en Azure. Microsoft también ha agregado compatibilidad para crear trabajos de MapReduce en otros lenguajes, entre los que se incluyen C#, F#, y JavaScript. El objetivo es conseguir que esta tecnología de datos de gran tamaño sea más fácilmente accesible para un mayor grupo de desarrolladores.

Además del sistema HDFS y MapReduce, Hadoop incluye otras tecnologías que permiten a las personas analizar datos sin escribir manualmente un trabajo de MapReduce. Por ejemplo, Pig es un lenguaje de alto nivel que se ha diseñado para analizar datos de gran tamaño, mientras que Hive ofrece un lenguaje similar a SQL que se denomina HiveQL. Tanto Pig como Hive generan trabajos de MapReduce que procesan datos HDFS, pero ocultan esta complejidad a los usuarios. Los dos cuentan con Hadoop en Azure.

Microsoft también proporciona un controlador de HiveQL para Excel. Mediante un complemento de Excel, los analistas empresariales pueden crear consultas de HiveQL (y por lo tanto de trabajos de MapReduce) directamente desde Excel y, a continuación, procesarlas y visualizar los resultados con PowerPivot y otras herramientas de Excel. Hadoop en Azure incluye además otras tecnologías, como las bibliotecas de aprendizaje automático Mahout, el sistema de exploración de gráficos Pegasus, etc.

El análisis de datos de gran tamaño es importante, motivo por el cual Hadoop también es importante. Al proporcionar Hadoop como un servicio administrado en Azure, junto con vínculos a herramientas conocidas como Excel, Microsoft pretende conseguir que esta tecnología sea accesible para un mayor grupo de usuarios.

En términos generales, los datos de todos los tipos son importantes. Por este motivo, Azure incluye una gama de opciones para la administración de datos y el análisis de negocios. Sea cual sea la aplicación que está intentando crear, es probable que en esta plataforma en la nube encuentre algo que le resulte de utilidad.

[blobs]: ./media/cloud-storage/Data_01_Blobs.png
[SQLSvr-vm]: ./media/cloud-storage/Data_02_SQLSvrVM.png
[SQL-db]: ./media/cloud-storage/Data_03_SQLdb.png
[SQL-datasync]: ./media/cloud-storage/Data_04_SQLDataSync.png
[SQL-report]: ./media/cloud-storage/Data_05_SQLReporting.png
[SQL-tblstor]: ./media/cloud-storage/Data_06_TblStorage.png
[hadoop]: ./media/cloud-storage/Data_07_Hadoop.png

<!---HONumber=Oct15_HO3-->