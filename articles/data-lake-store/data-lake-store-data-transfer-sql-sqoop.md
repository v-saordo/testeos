<properties 
   pageTitle="Copia de datos entre Almacén de Data Lake y Base de datos SQL de Azure mediante Sqoop | Microsoft Azure"
   description="Uso de Sqoop para copiar datos entre Almacén de Data Lake y Base de datos SQL de Azure" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/29/2016"
   ms.author="nitinme"/>

# Copia de datos entre Almacén de Data Lake y Base de datos SQL de Azure mediante Sqoop

Aprenda a usar Apache Sqoop para importar y exportar datos entre Base de datos SQL de Azure y Almacén de Data Lake.
 

## ¿Qué es Sqoop?

Las aplicaciones de macrodatos son una opción natural para procesar datos no estructurados y semiestructurados, como registros y archivos. Pero también puede que sea necesario procesar datos estructurados que se almacenan en bases de datos relacionales.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) es una herramienta diseñada para transferir datos entre bases de datos relacionales y un repositorio de macrodatos, como Almacén de Data Lake. Puede usarla para importar datos desde un sistema de administración de bases de datos relacionales (RDBMS) como Base de datos SQL de Azure a Almacén de Data Lake. Después, puede transformar y analizar los datos mediante cargas de trabajo de macrodatos y exportar los datos a un RDBMS. En este tutorial, use una Base de datos SQL de Azure como la base de datos relacional para importar y exportar.
 

## Requisitos previos

Antes de empezar este artículo, debe tener lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Habilite su suscripción de Azure** para la versión de vista previa pública del Almacén de Data Lake. Consulte las [instrucciones](data-lake-store-get-started-portal.md#signup). 
- **Clúster de HDInsight de Azure** con acceso a una cuenta de Almacén de Data Lake. Consulte [Creación de un clúster de HDInsight con Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md). En este artículo se supone que tiene un clúster de HDInsight Linux con acceso a Almacén de Data Lake.
- **Base de datos de SQL Azure**. Para obtener instrucciones sobre cómo crear una, vea [Creación de una Base de datos SQL de Azure](../sql-database/sql-database-get-started.md).

## Creación de tablas de ejemplo en la Base de datos SQL de Azure

1. Para empezar, cree dos tablas de ejemplo en la Base de datos SQL de Azure. Use [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) o Visual Studio para conectarse a la Base de datos SQL de Azure y ejecute las consultas siguientes:

	**Crear Tabla1**

		CREATE TABLE [dbo].[Table1]( 
	    [ID] [int] NOT NULL, 
	    [FName] [nvarchar](50) NOT NULL, 
	    [LName] [nvarchar](50) NOT NULL, 
	 	CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
			( 
		   		[ID] ASC 
			) 
		) ON [PRIMARY] 
		GO

	**Crear Tabla2**

		CREATE TABLE [dbo].[Table2]( 
	    [ID] [int] NOT NULL, 
	    [FName] [nvarchar](50) NOT NULL, 
	    [LName] [nvarchar](50) NOT NULL, 
	 	CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
			( 
		   		[ID] ASC 
			) 
		) ON [PRIMARY] 
		GO

2. En **Tabla1**, agregue algunos datos de ejemplo. Deje **Tabla2** vacía. Los datos de **Tabla1** se importarán a Almacén de Data Lake. Luego se importarán los datos de Almacén de Data Lake a **Tabla2**. Ejecute el siguiente fragmento de código:

		 
		INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson'); 
  

## Uso de Sqoop en un clúster de HDInsight con acceso a Almacén de Data Lake

Un clúster de HDInsight ya tiene los paquetes de Sqoop disponibles. Si ha configurado el clúster de HDInsight para utilizar Almacén de Data Lake como almacenamiento adicional, puede usar Sqoop (sin cambios de configuración) para importar o exportar datos entre una base de datos relacional (en este ejemplo, Base de datos SQL de Azure) y una cuenta de Almacén de Data Lake.

1. Para este tutorial, se supone que ha creado un clúster de Linux, por lo que debe usar SSH para conectarse al clúster. Vea [Conexión a un clúster de HDInsight basado en Linux](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster).

2. Compruebe que puede acceder a la cuenta de Almacén de Data Lake desde el clúster. Ejecute el siguiente comando desde el símbolo del sistema de SSH:

		
		hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net/

	Esto debería proporcionar una lista de archivos o carpetas en la cuenta del Almacén de Data Lake.

### Importación de datos de Base de datos SQL de Azure a Almacén de Data Lake

3. Navegue al directorio donde están disponibles los paquetes de Sqoop. Normalmente, se encuentran en `/usr/hdp/<version>/sqoop/bin`. 

4. Importe datos de **Tabla1** a la cuenta de Almacén de Data Lake. Use la sintaxis siguiente:

		
		sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

	Tenga en cuenta que el marcador de posición **sql-database-server-name** representa el nombre del servidor donde se ejecuta la Base de datos SQL de Azure. El marcador de posición **sql-database-name** representa el nombre de la base de datos real.

	Por ejemplo,

		
		sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1

5. Compruebe que los datos se han transferido a la cuenta de Almacén de Data Lake. Ejecute el siguiente comando:

		
		hdfs dfs -ls adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/

	Debería ver la siguiente salida.

		
		-rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
		-rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
		-rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
		-rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
		-rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

	Cada archivo **part-m-*** corresponde a una fila de la tabla de origen, **Table1**. Puede ver el contenido de los archivos part-m-* que desea comprobar.


### Exportación de datos de Almacén de Data Lake a Base de datos SQL de Azure

6. Exporte los datos de la cuenta de Almacén de Data Lake a la tabla vacía, **Tabla2**, en la Base de datos SQL de Azure. Use la sintaxis siguiente.

		
		sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

	Por ejemplo,

		
		sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

6. Compruebe que los datos se han cargado en la tabla de Base de datos SQL. Use [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) o Visual Studio para conectarse a la Base de datos SQL de Azure y ejecute las consultas siguientes.

		
		SELECT * FROM TABLE2

	El resultado debe ser el siguiente.

	 	ID  FName   LName
		------------------
		1	Neal	Kell
		2	Lila	Fulton
		3	Erna	Myers
		4	Annette	Simpson

## Consulte también

- [Copiar datos de los blobs de almacenamiento de Azure en el Almacén Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
- [Protección de los datos en el Almacén de Data Lake](data-lake-store-secure-data.md)
- [Uso de Análisis de Azure Data Lake con el Almacén de Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Uso de HDInsight de Azure con el Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0302_2016-->