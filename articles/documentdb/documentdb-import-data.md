<properties
	pageTitle="Herramienta de migración de base de datos para DocumentDB | Microsoft Azure"
	description="Obtenga información sobre cómo usar las herramientas de migración de datos de código abierto DocumentDB para importar datos a DocumentDB desde varios orígenes, incluidos archivos MongoDB, SQL Server, almacenamiento de tablas, Amazon DynamoDB, CSV y JSON. Conversión de CSV a JSON."
	keywords="csv a json, herramientas de migración de base de datos, convertir csv a json" 
	services="documentdb"
	authors="andrewhoh"
	manager="jhubbard"
	editor="monicar"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/29/2016"
	ms.author="anhoh"/>

# Importación de datos en DocumentDB con la herramienta de migración de base de datos

En este artículo se muestra cómo usar la herramienta de migración de datos de código abierto DocumentDB para importar datos a [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) desde diversos orígenes, incluidos archivos JSON, archivos CSV, SQL, MongoDB, almacenamiento de tablas de Azure, Amazon DynamoDB y colecciones de DocumentDB.

Después de leer este artículo, podrá responder a las preguntas siguientes:

-	¿Cómo puedo importar datos de archivos JSON a DocumentDB?
-	¿Cómo puedo importar datos de archivos CSV a DocumentDB?
-	¿Cómo puedo importar datos de SQL Server a DocumentDB?
-	¿Cómo puedo importar datos de MongoDB a DocumentDB?
-	¿Cómo puedo importar datos desde el almacenamiento de tablas de Azure a DocumentDB?
-	¿Cómo puedo importar datos desde Amazon DynamoDB a DocumentDB?
-	¿Cómo puedo importar datos de HBase a DocumentDB?
-	¿Cómo puedo migrar datos entre colecciones de DocumentDB?

##<a id="Prerequisites"></a>Requisitos previos ##

Antes de seguir las instrucciones del presente artículo, asegúrese de tener instalados los siguientes elementos:

- [Microsoft .NET Framework 4.51](https://www.microsoft.com/download/developer-tools.aspx) o superior.

##<a id="Overviewl"></a>Información general sobre la herramienta de migración de datos de DocumentDB ##

La herramienta de migración de datos de DocumentDB es una solución de código abierto que importa los datos a DocumentDB desde una variedad de orígenes, como:

- Archivos JSON
- MongoDB
- SQL Server
- Archivos CSV
- Almacenamiento de tablas de Azure
- Amazon DynamoDB
- HBase
- Colecciones de DocumentDB

Aunque la herramienta de importación incluye una interfaz gráfica de usuario (dtui.exe), también se pueden controlar desde la línea de comandos (dt.exe). De hecho, hay una opción para mostrar el comando asociado después de configurar una importación a través de la interfaz de usuario. Se pueden transformar datos tabulares de origen (por ejemplo, archivos de SQL Server o CSV) de tal forma que se pueden crear relaciones jerárquicas (subdocumentos) durante la importación. Siga leyendo para obtener más información acerca de las opciones de origen, las líneas de comandos de muestra para importar desde cada origen, las opciones de destino y la visualización de los resultados de importación.


##<a id="Install"></a>Instalación de la herramienta de migración de datos de DocumentDB

El código fuente de la herramienta de migración está disponible en GitHub en [este repositorio](https://github.com/azure/azure-documentdb-datamigrationtool) y una versión compilada se encuentra disponible en el [Centro de descarga de Microsoft](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d). Puede compilar la solución o simplemente descargar y extraer la versión compilada en un directorio de su elección. A continuación, ejecute:

- **Dtui.exe**: versión de interfaz gráfica de la herramienta
- **Dt.exe**: versión de línea de comandos de la herramienta

##<a id="JSON"></a>Importación de archivos JSON ##

La opción del importador de origen de archivos JSON permite importar uno o varios archivos JSON de un único documento o archivos JSON que contengan cada uno una matriz de documentos JSON. Cuando se agregan carpetas que contienen archivos JSON que se van a importar, existe la opción de buscar archivos en subcarpetas de forma recursiva.

![Captura de pantalla de opciones de origen de archivos JSON: herramientas de migración de base de datos](./media/documentdb-import-data/jsonsource.png)

Estos son algunos ejemplos de línea de comandos para importar archivos JSON:

	#Import a single JSON file
	dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Sessions /t.CollectionTier:S3

	#Import a directory of JSON files
	dt.exe /s:JsonFile /s.Files:C:\TESessions*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Sessions /t.CollectionTier:S3

	#Import a directory (including sub-directories) of JSON files
	dt.exe /s:JsonFile /s.Files:C:\LastFMMusic***.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Music /t.CollectionTier:S3

	#Import a directory (single), directory (recursive), and individual JSON files
	dt.exe /s:JsonFile /s.Files:C:\Tweets*.*;C:\LargeDocs***.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:subs /t.CollectionTier:S3

	#Import a single JSON file and partition the data across 4 collections
	dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionTier:S3

##<a id="MongoDB"></a>Importación desde MongoDB ##

La opción del importador de origen de MongoDB permite importar desde una colección de MongoDB individual y, opcionalmente, filtrar documentos mediante una consulta o modificar la estructura del documento con una proyección.

![Captura de pantalla de opciones de origen de MongoDB: comparación de documentdb y mongodb](./media/documentdb-import-data/mongodbsource.png)

La cadena de conexión tiene el formato estándar de MongoDB:

	mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>

> [AZURE.NOTE] Utilice el comando Verify para asegurarse de que se puede tener acceso a la instancia de MongoDB especificada en el campo de la cadena de conexión.

Escriba el nombre de la colección desde la que se importarán los datos. Opcionalmente, puede especificar o facilitar un archivo para una consulta (por ejemplo, {pop: {$gt:5000}}) o proyección (por ejemplo, {loc:0}) para filtrar los datos que se van a importar y darles forma.

Estos son algunos ejemplos de línea de comandos para importar desde MongoDB:

	#Import all documents from a MongoDB collection
	dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionTier:S3

	#Import documents from a MongoDB collection which match the query and exclude the loc field
	dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionTier:S3

##<a id="MongoDBExport"></a>Importación de archivos de exportación de MongoDB ##

La opción del importador de origen de archivos JSON de exportación de MongoDB permite importar uno o más archivos JSON generados a partir de la utilidad mongoexport.

![Captura de pantalla de opciones de origen de exportación de MongoDB: comparación de documentdb y mongodb](./media/documentdb-import-data/mongodbexportsource.png)

Cuando se agregan carpetas que contienen archivos JSON de exportación de MongoDB para importarlos, tiene la opción de buscar archivos en subcarpetas de forma recursiva:

A continuación se muestra un ejemplo de línea de comandos para importar desde archivos JSON de exportación de MongoDB:

	dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionTier:S3

##<a id="SQL"></a>Importación desde SQL Server ##

La opción del importador de origen SQL permite importar desde una base de datos SQL Server individual y, opcionalmente, filtrar los registros que se pueden importar utilizando una consulta. Además, puede modificar la estructura del documento especificando un separador de anidamiento (más en un momento determinado).

![Captura de pantalla de opciones de origen de SQL: herramientas de migración de base de datos](./media/documentdb-import-data/sqlexportsource.png)

El formato de la cadena de conexión es el formato de la cadena de conexión SQL estándar.

> [AZURE.NOTE] Utilice el comando Verify para asegurarse de que se puede tener acceso a la instancia de SQL Server especificada en el campo de la cadena de conexión.

La propiedad de separador de anidamiento se utiliza para crear relaciones jerárquicas (documentos secundarios) durante la importación. Considere la siguiente consulta SQL:

*select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as Address.AddressType, AddressLine1 as Address.AddressLine1, City as Address.Location.City, StateProvinceName as Address.Location.StateProvinceName, PostalCode as Address.PostalCode, CountryRegionName as Address.CountryRegionName from Sales.vStoreWithAddresses WHERE AddressType='Main Office'*

Que devuelve los resultados siguientes (parciales):

![Screenshot of SQL query results](./media/documentdb-import-data/sqlqueryresults.png)

Tenga en cuenta los alias como Address.AddressType y Address.Location.StateProvinceName. Al especificar un separador de anidamiento de “.”, la herramienta de importación crea subdocumentos Address y Address.Location durante la importación. Este es un ejemplo de un documento resultante en DocumentDB:

*{ "id": "956", "Name": "Finer Sales and Service", "Address": { "AddressType": "Main Office", "AddressLine1": "#500-75 O'Connor Street", "Location": { "City": "Ottawa", "StateProvinceName": "Ontario" }, "PostalCode": "K4B 1S2", "CountryRegionName": "Canada" } }*

Estos son algunos ejemplos de línea de comandos para importar desde SQL Server:

	#Import records from SQL which match a query
	dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionTier:S3

	#Import records from sql which match a query and create hierarchical relationships
	dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionTier:S3

##<a id="CSV"></a>Importación de archivos CSV - Conversión de CSV a JSON ##

La opción del importador de origen de archivos CSV le permite importar uno o varios archivos CSV. Cuando se agregan carpetas que contienen archivos CSV que se van a importar, existe la opción de buscar archivos en subcarpetas de forma recursiva.

![Captura de pantalla de opciones de origen de CSV](media/documentdb-import-data/csvsource.png)

De forma similar a lo que sucede con el origen SQL, la propiedad de separador de anidamiento se utiliza para crear relaciones jerárquicas (documentos secundarios) durante la importación. Tenga en cuenta las siguientes filas de datos y la fila de encabezado CSV:

![Captura de pantalla de registros de ejemplo de CSV: CSV a JSON](./media/documentdb-import-data/csvsample.png)

Tenga en cuenta los alias como DomainInfo.Domain\_Name y RedirectInfo.Redirecting. Al especificar un separador de anidamiento de “.”, la herramienta de importación crea subdocumentos DomainInfo y RedirectInfo durante la importación. Este es un ejemplo de un documento resultante en DocumentDB:

*{ "DomainInfo": { "Domain\_Name": "ACUS.GOV", "Domain\_Name\_Address": "http://www.ACUS.GOV" }, "Federal Agency": "Administrative Conference of the United States", "RedirectInfo": { "Redirecting": "0", "Redirect\_Destination": "" }, "id": "9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d" }*

La herramienta de importación intentará inferir la información de tipo de los valores sin comillas de los archivos CSV (los valores entre comillas se tratan siempre como cadenas). Los tipos se identifican en el siguiente orden: número, fecha y hora, booleano.

Hay otras dos cosas que debe saber sobre la importación de archivos CSV:

1.	De forma predeterminada, los valores sin comillas se recortan siempre en tabuladores y espacios, mientras que los valores entre comillas se mantienen como son. Este comportamiento se puede invalidar con la casilla Recortar valores entre comillas o la opción de línea de comandos /s.TrimQuoted.

2.	De forma predeterminada, un valor null sin comillas se trata como un valor null. Este comportamiento se puede invalidar (es decir, tratar a los valores null sin comillas como si fueran cadenas "null") con la casilla Tratar NULL sin comillas como cadena o la opción de línea de comandos /s.NoUnquotedNulls.


Este es un ejemplo de línea de comandos para la importación CSV:

	dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionTier:S3

##<a id="AzureTableSource"></a>Importación de almacenamiento de tablas de Azure ##

La opción del importador de código fuente de almacenamiento de tabla de Azure permite importar desde una tabla de almacenamiento de Azure individual y, opcionalmente, filtrar las entidades de tabla que se van a importar.

![Screenshot of Azure Table storage source options](./media/documentdb-import-data/azuretablesource.png)

El formato de la cadena de conexión de almacenamiento de tablas de Azure es:

	DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;

> [AZURE.NOTE] Utilice el comando Verify para asegurarse de que se puede tener acceso a la instancia de almacenamiento de tablas de Azure especificada en el campo de la cadena de conexión.

Escriba el nombre de la tabla de Azure desde la que se importarán los datos. Opcionalmente, puede especificar un [filtro](https://msdn.microsoft.com/library/azure/ff683669.aspx).

La opción del importador de origen de almacenamiento de tablas de Azure tiene las siguientes opciones adicionales:

1. Incluir campos internos
	2. All: incluir todos los campos internos (PartitionKey, RowKey y Timestamp)
	3. None: excluir todos los campos internos
	4. RowKey: incluir sólo el campo RowKey
3. Seleccionar columnas
	1. Los filtros de almacenamiento de tablas de Azure no admiten proyecciones. Si desea importar sólo las propiedades específicas de la entidad de tablas de Azure, agréguelas a la lista Seleccionar columnas. Se omitirán todas las demás propiedades de la entidad.

A continuación se muestra un ejemplo de línea de comandos para importar desde el almacenamiento de tablas de Azure:

	dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:metrics /t.CollectionTier:S3

##<a id="DynamoDBSource"></a>Importar desde Amazon DynamoDB

La opción de importador de origen Amazon DynamoDB permite importar de una tabla individual de Amazon DynamoDB individual y, opcionalmente, filtrar las entidades que desea importar. Se proporcionan varias plantillas para que configurar una importación resulte lo más fácil posible.

![Captura de pantalla de opciones de origen de Amazon DynamoD: herramientas de migración de base de datos](./media/documentdb-import-data/dynamodbsource1.png)

![Captura de pantalla de opciones de origen de Amazon DynamoD: herramientas de migración de base de datos](./media/documentdb-import-data/dynamodbsource2.png)

El formato de la cadena de conexión de Amazon DynamoDB es:

	ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;

> [AZURE.NOTE] Use el comando Verify para asegurarse de que se puede tener acceso a la instancia de Amazon DynamoDB especificada en el campo de la cadena de conexión.

A continuación se muestra un ejemplo de línea de comandos para importar desde Amazon DynamoDB:

	dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:catalogCollection /t.CollectionTier:S3

##<a id="BlobImport"></a>Importación de archivos desde el almacenamiento de blobs de Azure

El archivo JSON, archivo de exportación de MongoDB y opciones de importador de origen de archivo CSV permiten importar uno o más archivos del almacenamiento de blobs de Azure. Después de especificar una dirección URL de contenedor de blobs y una clave de cuenta, basta con proporcionar una expresión regular para seleccionar los archivos para importar.

![Captura de pantalla de opciones de origen de archivos Blob](./media/documentdb-import-data/blobsource.png)

Este es el ejemplo de línea de comandos para importar archivos JSON del almacenamiento de blobs de Azure:

	dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:doctest

##<a id="DocumentDBSource"></a>Importación desde DocumentDB ##

La opción del importador de origen de DocumentDB permite importar datos de una o varias colecciones de DocumentDB y, opcionalmente, filtrar los documentos mediante una consulta.

![Screenshot of DocumentDB source options](./media/documentdb-import-data/documentdbsource.png)

El formato de la cadena de conexión de DocumentDB es:

	AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;

> [AZURE.NOTE] Utilice el comando Verify para asegurarse de que se puede tener acceso a la instancia de DocumentDB especificada en el campo de la cadena de conexión.

Para realizar la importación desde una sola colección de DocumentDB, escriba el nombre de la colección de la que se van a importar los datos. Para realizar la importación desde varias colecciones de DocumentDB, especifique una expresión regular que coincida con uno o varios nombres de colección (por ejemplo, collección01 | collección02 | collección03). Opcionalmente, puede especificar o facilitar un archivo para una consulta para filtrar los datos que se van a importar y darles forma.

> [AZURE.NOTE] Dado que el campo de colecciones acepta expresiones regulares, si la importación se va a realizar de una sola colección cuyo nombre contenga caracteres de expresión regular, dichos caracteres deben incluir una secuencia de escape.

La opción del importador de origen de DocumentDB tiene las siguientes opciones avanzadas:

1. Incluir campos internos: especifica si se debe o no incluir propiedades del sistema de documentos de DocumentDB en la exportación (por ejemplo, \_rid, \_ts).
2. Número de reintentos en caso de error: especifica el número de veces que se intentará la conexión a DocumentDB en caso de errores transitorios (por ejemplo, interrupciones de conectividad de red).
3. Intervalo de reintento: especifica el tiempo de espera entre los reintentos de restablecimiento de conexión a DocumentDB en caso de errores transitorios (por ejemplo, interrupción de la conectividad de red).
4. Modo de conexión: especifica el modo de conexión para utilizarlo con DocumentDB. Las opciones disponibles son DirectTcp, DirectHttps y puerta de enlace. Los modos de conexión directa son más rápidos, mientras que el modo de puerta de enlace es compatible con el firewall, ya que sólo usa el puerto 443.

![Screenshot of DocumentDB source advanced options](./media/documentdb-import-data/documentdbsourceoptions.png)

> [AZURE.TIP] El modo de conexión predeterminado de la herramienta de importación es DirectTcp. Si experimenta problemas de firewall, cambie al modo de conexión puerta de enlace, ya que sólo requiere el puerto 443.


Estos son algunos ejemplos de línea de comandos para importar desde DocumentDB:

	#Migrate data from one DocumentDB collection to another DocumentDB collections
	dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /s.Collection:TEColl /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:TESessions /t.CollectionTier:S3

	#Migrate data from multiple DocumentDB collections to a single DocumentDB collection
	dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:singleCollection /t.CollectionTier:S3

	#Export a DocumentDB collection to a JSON file
	dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionTier:S3

##<a id="HBaseSource"></a>Importar desde HBase

La opción del importador de origen de HBase le permite importar datos de una tabla HBase y, opcionalmente, filtrar los datos. Se proporcionan varias plantillas para que configurar una importación resulte lo más fácil posible.

![Captura de pantalla de opciones de origen de HBase](./media/documentdb-import-data/hbasesource1.png)

![Captura de pantalla de opciones de origen de HBase](./media/documentdb-import-data/hbasesource2.png)

El formato de la cadena de conexión de HBase Stargate es:

	ServiceURL=<server-address>;Username=<username>;Password=<password>

> [AZURE.NOTE] Use el comando Verify para asegurarse de que se puede tener acceso a la instancia de HBase especificada en el campo de la cadena de conexión.

A continuación se muestra un ejemplo de línea de comandos para importar desde HBase:

	dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:hbaseimport

##<a id="DocumentDBBulkTarget"></a>Importación a DocumentDB (importación masiva)

El importador masivo de DocumentDB permite importar desde cualquier opción de origen disponible con un procedimiento almacenado de DocumentDB para que resulte más eficaz. La herramienta de importación admite la importación en una sola colección de DocumentDB, así como la importación de particiones, por medio de la que los datos se dividen en varias colecciones de DocumentDB. [Aquí](documentdb-partition-data.md) puede encontrar más información sobre las particiones de datos en DocumentDB. La herramienta creará, ejecutará y eliminará el procedimiento almacenado de las colecciones de destino.

![Screenshot of DocumentDB bulk options](./media/documentdb-import-data/documentdbbulk.png)

El formato de la cadena de conexión de DocumentDB es:

	AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;

> [AZURE.NOTE] Utilice el comando Verify para asegurarse de que se puede tener acceso a la instancia de DocumentDB especificada en el campo de la cadena de conexión.

Para realizar la importación en una sola colección, escriba el nombre de la colección en la que se van a importar los datos y haga clic en el botón Agregar. Para importar varias colecciones, escriba el nombre de cada colección individualmente o use la siguiente sintaxis para especificar varias colecciones: *collection\_prefix*[índice inicial - índice final]. Si va a especificar varias colecciones a través de esta sintaxis, tenga en cuenta lo siguiente:

1. Solo se admiten patrones de nombre de intervalo entero. Por ejemplo, la especificación de colección [0-3] generará las siguientes colecciones: colección0, colección1, colección2, colección3.
2. Puede usar una sintaxis abreviada: colección[3] generará el mismo conjunto de colecciones que el paso 1.
3. Se pueden proporcionar varias sustituciones. Por ejemplo, colección[0-1] [0-9] 20 nombres de colección con ceros delante (colección01,... 02... 03).

Una vez que especificados los nombres de colección, elija el nivel de precios deseado de las colecciones (S1, S2 o S3). Para obtener el mejor rendimiento de importación, seleccione S3. [Aquí](documentdb-performance-levels.md) puede obtener más información sobre los niveles de rendimiento de DocumentDB.

> [AZURE.NOTE] La configuración del nivel de rendimiento sólo se aplica a la creación de la colección. Si la colección especificada ya existe, no se modificará su nivel de precios.

Cuando se importa a varias colecciones, la herramienta de importación admite el particionamiento basado en hash. En este escenario, especifique la propiedad de documento que desea utilizar como clave de partición (si el valor de Clave de partición se deja en blanco, los documentos se particionarán aleatoriamente entre las colecciones de destino).

También puede especificar qué campo del origen de importación debe utilizarse como la propiedad de identificador de documentos DocumentDB durante la importación (tenga en cuenta que si los documentos no contienen esta propiedad, a continuación, la herramienta de importación generará un GUID como el valor de la propiedad del identificador).

Hay una serie de opciones avanzadas disponibles durante la importación. En primer lugar, mientras que la herramienta incluye un procedimiento predeterminado almacenado de importación masiva (BulkInsert.js), puede especificar su propio procedimiento almacenado de importación:

 ![Screenshot of DocumentDB bulk insert sproc option](./media/documentdb-import-data/bulkinsertsp.png)

Además, al importar los tipos de fecha (por ejemplo, desde SQL Server o MongoDB), puede elegir entre tres opciones de importación:

 ![Screenshot of DocumentDB date time import options](./media/documentdb-import-data/datetimeoptions.png)

-	Cadena: conservar como un valor de cadena
-	Tiempo: conservar como un valor de número de tiempo
-	Ambos: conservar los valores de cadena y de número Esta opción creará un subdocumento, por ejemplo "date\_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }


El importador masivo de DocumentDB tiene las siguientes opciones avanzadas adicionales:

1. Tamaño del lote: el tamaño de lote predeterminado de la herramienta es 50. Si los documentos que desea importar son grandes, considere la posibilidad de reducir el tamaño del lote. Si los documentos que desea importar son pequeños, considere la posibilidad de aumentar el tamaño del lote.
2. Tamaño máximo de script (bytes): el valor predeterminado máximo del script en la herramienta es de 512 KB.
3. Deshabilitar generación de Id. automática: si cada documento que se va a importar contiene un campo de Id., seleccione esta opción para aumentar el rendimiento. No se importarán los documentos que no contengan el campo de identificador exclusivo.
4. Actualización de documentos existentes: la opción predeterminada es no reemplazar los documentos existentes por conflictos de identificador. Si selecciona esta opción, permitirá que se sobrescriban los documentos existentes con identificadores que coinciden entre sí. Esta característica es útil para las migraciones de datos programada que actualizan los documentos existentes.
5. Número de reintentos en caso de error: especifica el número de veces que se intentará la conexión a DocumentDB en caso de errores transitorios (por ejemplo, interrupciones de conectividad de red).
6. Intervalo de reintento: especifica el tiempo de espera entre los reintentos de restablecimiento de conexión a DocumentDB en caso de errores transitorios (por ejemplo, interrupción de la conectividad de red).
7. Modo de conexión: especifica el modo de conexión para utilizarlo con DocumentDB. Las opciones disponibles son DirectTcp, DirectHttps y puerta de enlace. Los modos de conexión directa son más rápidos, mientras que el modo de puerta de enlace es compatible con el firewall, ya que sólo usa el puerto 443.

![Screenshot of DocumentDB bulk import advanced options](./media/documentdb-import-data/docdbbulkoptions.png)

> [AZURE.TIP] El modo de conexión predeterminado de la herramienta de importación es DirectTcp. Si experimenta problemas de firewall, cambie al modo de conexión puerta de enlace, ya que sólo requiere el puerto 443.

##<a id="DocumentDBSeqTarget"></a>Importación a DocumentDB (importación de registros secuenciales)

El importador de registros secuenciales de DocumentDB permite importar desde cualquiera de las opciones de origen disponibles según cada registro. Puede elegir esta opción si va a importar a una colección existente que ha alcanzado su cuota de procedimientos almacenados. La herramienta de importación admite la importación en una sola colección de DocumentDB, así como la importación de particiones, por medio de la que los datos se dividen en varias colecciones de DocumentDB. [Aquí](documentdb-partition-data.md) puede encontrar más información sobre las particiones de datos en DocumentDB.

![Screenshot of DocumentDB sequential record import options](./media/documentdb-import-data/documentdbsequential.png)

El formato de la cadena de conexión de DocumentDB es:

	AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;

> [AZURE.NOTE] Utilice el comando Verify para asegurarse de que se puede tener acceso a la instancia de DocumentDB especificada en el campo de la cadena de conexión.

Para realizar la importación en una sola colección, escriba el nombre de la colección en la que se van a importar los datos y haga clic en el botón Agregar. Para importar varias colecciones, escriba el nombre de cada colección individualmente o use la siguiente sintaxis para especificar varias colecciones: *collection\_prefix*[índice inicial - índice final]. Si va a especificar varias colecciones a través de esta sintaxis, tenga en cuenta lo siguiente:

1. Solo se admiten patrones de nombre de intervalo entero. Por ejemplo, la especificación de colección [0-3] generará las siguientes colecciones: colección0, colección1, colección2, colección3.
2. Puede usar una sintaxis abreviada: colección[3] generará el mismo conjunto de colecciones que el paso 1.
3. Se pueden proporcionar varias sustituciones. Por ejemplo, colección[0-1] [0-9] 20 nombres de colección con ceros delante (colección01,... 02... 03).

Una vez que especificados los nombres de colección, elija el nivel de precios deseado de las colecciones (S1, S2 o S3). Para obtener el mejor rendimiento de importación, seleccione S3. [Aquí](documentdb-performance-levels.md) puede obtener más información sobre los niveles de rendimiento de DocumentDB.

> [AZURE.NOTE] La configuración del nivel de rendimiento sólo se aplica a la creación de la colección. Si la colección especificada ya existe, no se modificará su nivel de precios.

Cuando se importa a varias colecciones, la herramienta de importación admite el particionamiento basado en hash. En este escenario, especifique la propiedad de documento que desea utilizar como clave de partición (si el valor de Clave de partición se deja en blanco, los documentos se particionarán aleatoriamente entre las colecciones de destino).

También puede especificar qué campo del origen de importación debe utilizarse como la propiedad de identificador de documentos DocumentDB durante la importación (tenga en cuenta que si los documentos no contienen esta propiedad, a continuación, la herramienta de importación generará un GUID como el valor de la propiedad del identificador).

Hay una serie de opciones avanzadas disponibles durante la importación. En primer lugar, al importar los tipos de fecha (por ejemplo, desde SQL Server o MongoDB), puede elegir entre tres opciones de importación:

 ![Screenshot of DocumentDB date time import options](./media/documentdb-import-data/datetimeoptions.png)

-	Cadena: conservar como un valor de cadena
-	Tiempo: conservar como un valor de número de tiempo
-	Ambos: conservar los valores de cadena y de número Esta opción creará un subdocumento, por ejemplo "date\_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

El importador de registros secuenciales de DocumentDB tiene estas opciones adicionales:

1. Número de solicitudes paralelas: la opción predeterminada es 2. Si los documentos que desea importar son pequeños, considere la posibilidad de aumentar el número de solicitudes paralelas. Tenga en cuenta que si este número es demasiado elevado, podrá haber limitaciones en la importación.
2. Deshabilitar generación de Id. automática: si cada documento que se va a importar contiene un campo de Id., seleccione esta opción para aumentar el rendimiento. No se importarán los documentos que no contengan el campo de identificador exclusivo.
3. Actualización de documentos existentes: la opción predeterminada es no reemplazar los documentos existentes por conflictos de identificador. Si selecciona esta opción, permitirá que se sobrescriban los documentos existentes con identificadores que coinciden entre sí. Esta característica es útil para las migraciones de datos programada que actualizan los documentos existentes.
4. Número de reintentos en caso de error: especifica el número de veces que se intentará la conexión a DocumentDB en caso de errores transitorios (por ejemplo, interrupciones de conectividad de red).
5. Intervalo de reintento: especifica el tiempo de espera entre los reintentos de restablecimiento de conexión a DocumentDB en caso de errores transitorios (por ejemplo, interrupción de la conectividad de red).
6. Modo de conexión: especifica el modo de conexión para utilizarlo con DocumentDB. Las opciones disponibles son DirectTcp, DirectHttps y puerta de enlace. Los modos de conexión directa son más rápidos, mientras que el modo de puerta de enlace es compatible con el firewall, ya que sólo usa el puerto 443.

![Screenshot of DocumentDB sequential record import advanced options](./media/documentdb-import-data/documentdbsequentialoptions.png)

> [AZURE.TIP] El modo de conexión predeterminado de la herramienta de importación es DirectTcp. Si experimenta problemas de firewall, cambie al modo de conexión puerta de enlace, ya que sólo requiere el puerto 443.

##<a id="IndexingPolicy"></a>Especificar una directiva de indexación al crear colecciones DocumentDB ##

Si permite que la herramienta de migración cree colecciones durante la importación, puede especificar la directiva de indización de las colecciones. En la sección de opciones avanzadas de las opciones de Importación masiva de DocumentDB y Registro secuencial de DocumentDB, vaya a la sección de la directiva de indización.

![Captura de pantalla de opciones avanzadas de política de indización DocumentDB](./media/documentdb-import-data/indexingpolicy1.png)

Mediante la opción avanzada de Directiva de indización, puede seleccionar un archivo de directiva de indización, especificar manualmente una directiva de indización o seleccionar de un conjunto de plantillas predeterminadas (haciendo clic con el botón derecho en el cuadro de texto de la directiva de indización).

La herramienta proporciona las plantillas de directiva siguientes:

- Predeterminada. Esta directiva es mejor cuando se realizan consultas de igualdad en cadenas y se usan consultas ORDER BY, de rango y de igualdad para números. Esta directiva tiene una sobrecarga de almacenamiento de índices menor que la de intervalo.
- Intervalo. Esta directiva es mejor si está usando consultas de ORDER BY, intervalo e igualdad en números y cadenas. Esta directiva tiene una mayor sobrecarga de almacenamiento de índice que la Predeterminada o Hash.


![Captura de pantalla de opciones avanzadas de política de indización DocumentDB](./media/documentdb-import-data/indexingpolicy2.png)

> [AZURE.NOTE] Si no especifica una directiva de indización, se aplicará la directiva predeterminada. Obtenga más información acerca de directivas de indización de DocumentDB [aquí](documentdb-indexing-policies.md).


## Exportación a archivos JSON

El exportador JSON de DocumentDB permite exportar cualquiera de las opciones de origen disponibles en un archivo JSON que contiene una matriz de documentos JSON. La herramienta controlará la exportación por usted, o puede ver el comando resultante de la migración y ejecutar el comando usted mismo. El archivo JSON resultante puede almacenarse localmente o en el almacenamiento de blobs de Azure.

![Captura de pantalla de opción de exportación de archivo local JSON de DocumentDB](./media/documentdb-import-data/jsontarget.png)

![Captura de pantalla de opción de exportación de almacenamiento de blob de Azure JSON de DocumentDB](./media/documentdb-import-data/jsontarget2.png)

Opcionalmente, puede optar por adornar el JSON resultante, lo que aumentará el tamaño del documento resultante al mismo tiempo que el contenido será más legibles.

	Standard JSON export
	[{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in DocumentDB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]

	Prettified JSON export
	[
 	{
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in DocumentDB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
	}]

## Configuración avanzada

En la pantalla Configuración avanzada, especifique la ubicación del archivo de registro en que desee que se escriban los errores. Las siguientes reglas se aplican a esta página:

1.	Si no se proporciona un nombre de archivo, todos los errores se devolverán a la página de resultados.
2.	Si se proporciona un nombre de archivo sin un directorio, el archivo se creará (o sobrescribirá) en el directorio del entorno actual.
3.	Si selecciona un archivo existente, este se sobrescribirá, ya que no hay opción de anexar.

A continuación, elija si desea registrar todos los mensajes de error, los críticos o ninguno. Por último, decida con qué frecuencia se actualizará el progreso en el mensaje de transferencia en pantalla.

	![Screenshot of Advanced configuration screen](./media/documentdb-import-data/AdvancedConfiguration.png)

## Confirmación de las opciones de importación y visualización de la línea de comandos

1. Después de especificar la información de origen y destino, y la configuración avanzada, revise el resumen de la migración y, opcionalmente, vea o copie el comando resultante de la migración (copiar el comando es útil para automatizar las operaciones de importación):

	![Screenshot of summary screen](./media/documentdb-import-data/summary.png)

	![Screenshot of summary screen](./media/documentdb-import-data/summarycommand.png)

2. Una vez que esté satisfecho con las opciones de origen y de destino, haga clic en **Importar**. El tiempo transcurrido, el número transferido y la información sobre los errores (si no se ha especificado un nombre de archivo en la configuración avanzada) se actualizarán mientras la importación está curso. Una vez que haya finalizado, puede exportar los resultados (por ejemplo, para tratar los errores de importación).

	![Screenshot of DocumentDB JSON export option](./media/documentdb-import-data/viewresults.png)

3. También puede iniciar una nueva importación, conservando la configuración existente (por ejemplo, elección de información de origen y de destino de la cadena de conexión, etc.) o restableciendo todos los valores.

	![Screenshot of DocumentDB JSON export option](./media/documentdb-import-data/newimport.png)

## Pasos siguientes

- Para obtener más información sobre DocumentDB, haga clic [aquí](http://azure.com/docdb).

<!---HONumber=AcomDC_0218_2016-->