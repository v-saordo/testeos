<properties
	pageTitle="Uso de Sqoop de Hadoop en HDInsight basado en Linux| Microsoft Azure"
	description="Aprenda a ejecutar Sqoop para importar y exportar entre un clúster de Hadoop basado en Linux en HDInsight y una base de datos SQL de Azure."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="larryfr"/>

#Uso de Sqoop con Hadoop en HDInsight (SSH)

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Aprenda a utilizar Sqoop para importar y exportar entre un clúster de HDInsight basado en Linux y la base de datos de SQL Server o la base de datos SQL de Azure.

> [AZURE.NOTE] En los pasos de este artículo se usa SSH para conectarse a un clúster de HDInsight basado en Linux. Los clientes de Windows también pueden utilizar Azure PowerShell para trabajar con Sqoop en los clústeres basados en Linux, tal como se documenta en [Uso de Sqoop con Hadoop en HDInsight (PowerShell)](hdinsight-use-sqoop.md).

##¿Qué es Sqoop?

A pesar de que Hadoop es una opción natural para procesar datos no estructurados y datos semiestructurados, como registros y archivos, es posible que también sea necesario procesar datos estructurados almacenados en bases de datos relacionales.

[Sqoop][sqoop-user-guide-1.4.4] es una herramienta diseñada para transferir datos entre clústeres de Hadoop y las bases de datos relacionales. Puede usarla para importar datos desde un sistema de administración de bases de datos relacionales (RDBMS) como SQL Server, MySQL u Oracle en el sistema de archivos distribuidos Hadoop (HDFS), transformar los datos de Hadoop con MapReduce o Hive y, a continuación, exportar los datos en un RDBMS. En este tutorial, usará una base de datos de SQL Server como base de datos relacional.

Para ver las versiones de Sqoop compatibles con los clústeres de HDInsight, consulte [Novedades en las versiones de clústeres proporcionadas por HDInsight][hdinsight-versions].


##Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Estación de trabajo**: un equipo con un cliente SSH.

- **Azure CLI**: para obtener más información, consulte [Instalación y configuración de Azure CLI](../xplat-cli-install.md)

- **Clúster de HDInsight basado en Linux**: para obtener instrucciones acerca del aprovisionamiento del clúster, consulte [Introducción al uso de HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) o [Aprovisionamiento de clústeres de HDInsight][hdinsight-provision].

- **Base de datos SQL de Azure**: en este documento se proporcionan instrucciones para crear una base de datos SQL de ejemplo. Para obtener más información sobre la base de datos SQL, consulte [Introducción al uso de la base de datos SQL de Azure][sqldatabase-get-started].

* **SQL Server**: los pasos descritos en este documento también se pueden usar, con algunas modificaciones, con SQL Server; Pero el clúster de HDInsight y SQL Server deben estar en la misma Red virtual de Azure. Para obtener más información sobre los requisitos específicos para usar este artículo con SQL Server, consulte la sección [Uso de SQL Server](#using-sql-server).

##Descripción del escenario

Un clúster de HDInsight incluye algunos datos de ejemplo. Usará una tabla de Hive denominada **hivesampletable** que hace referencia al archivo de datos ubicado en **wasb:///hive/warehouse/hivesampletable**. La tabla contiene algunos datos del dispositivo móvil. El esquema de dicha tabla es el siguiente:

| Campo | Tipo de datos |
| ----- | --------- |
| clientid | cadena |
| querytime | cadena |
| market | cadena |
| deviceplatform | cadena |
| devicemake | cadena |
| devicemodel | cadena |
| state | cadena |
| country | cadena |
| querydwelltime | double |
| sessionid | bigint |
| sessionpagevieworder | bigint |

Primero exportará **hivesampletable** a la base de datos SQL de Azure o SQL Server en una tabla denominada **mobiledata** y, a continuación, volverá a importar la tabla en HDInsight en **wasb:///tutorials/usesqoop/importeddata**.

##Creación de una base de datos

1. Abra un terminal o un símbolo del sistema y use el comando siguiente para crear un nuevo servidor de base de datos SQL de Azure:

        azure sql server create <adminLogin> <adminPassword> <region>

    Por ejemplo, `azure sql server create admin password "West US"`.

    Cuando finalice el comando, recibirá una respuesta similar a lo siguiente:

        info:    Executing command sql server create
        + Creating SQL Server
        data:    Server Name i1qwc540ts
        info:    sql server create command OK

    > [AZURE.IMPORTANT] Tenga en cuenta el nombre del servidor que devuelve este comando. Este es el nombre corto del servidor de la base de datos SQL que se creó. El nombre de dominio completo (FQDN) es **&lt;shortname&gt;.database.windows.net**.

2. Utilice el siguiente comando para crear una base de datos denominada **sqooptest** en el servidor de la base de datos SQL:

        azure sql db create [options] <serverName> sqooptest <adminLogin> <adminPassword>

    Esto devolverá el mensaje "Aceptar" cuando termine.

	> [AZURE.NOTE] Si recibe un error en el que se indica que no tiene acceso, puede que necesite agregar la dirección IP de la estación de trabajo del cliente en el firewall de la base de datos SQL mediante el siguiente comando:
	>
	> `azure sql firewallrule create [options] <serverName> <ruleName> <startIPAddress> <endIPAddress>`

##Creación de una tabla

> [AZURE.NOTE] Hay muchas maneras de conectarse a la base de datos SQL para crear una tabla. En los siguientes pasos se utiliza [FreeTDS](http://www.freetds.org/) desde el clúster de HDInsight.

1. Use SSH para conectarse al clúster de HDInsight basado en Linux. La dirección que se usará al conectarse es `CLUSTERNAME-ssh.azurehdinsight.net` y el puerto es `22`.

	Para obtener más información sobre el uso de SSH para conectarse a HDInsight, vea los siguientes documentos:

    * **Clientes de Linux, Unix y OS X**: vea [Conectarse a un clúster de HDInsight basado en Linux desde Linux, OS X o Unix](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)

    * **Clientes de Windows**: vea [Conectarse a un clúster de HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)

3. Use el siguiente comando para instalar FreeTDS:

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. Una vez se ha instalado FreeTDS, use el comando siguiente para conectarse al servidor de la base de datos SQL que creó anteriormente:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Recibirá un resultado similar al siguiente.

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. En el símbolo del sistema `1>`, introduzca las líneas siguientes:

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])
        GO
        CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
        GO

    Cuando se haya especificado la instrucción `GO`, se evaluarán las instrucciones anteriores. En primer lugar, se crea la tabla **mobiledata** y, a continuación, se agrega un índice agrupado a ella (es necesario para la base de datos SQL).

    Para comprobar que se ha creado la tabla, utilice lo siguiente:

        SELECT * FROM information_schema.tables
        GO

    Debería ver una salida similar a la siguiente:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

8. Para salir de la utilidad tsql, escriba `exit` en el símbolo del sistema `1>`.

##Exportación de Sqoop

2. Utilice el siguiente comando para crear un vínculo a JDBC Driver para SQL Server desde el directorio de la biblioteca de Sqoop. Esto permite a Sqoop utilizar este controlador para comunicarse con la base de datos SQL:

        sudo ln /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /usr/hdp/current/sqoop-client/lib/sqljdbc41.jar

3. Utilice el siguiente comando para comprobar que Sqoop puede ver la base de datos SQL:

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    Esto debe devolver una lista de bases de datos, incluida la base de datos **sqooptest** que creó anteriormente.

4. Utilice el siguiente comando para exportar los datos de **hivesampletable** a la tabla **mobiledata**:

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasb:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1

    Esto indica a Sqoop que debe conectarse a la base de datos SQL, la base de datos **sqooptest**, y exportar los datos de **wasb:///hive/warehouse/hivesampletable** (archivos físicos para *hivesampletable*) en la tabla **mobiledata**.

5. Una vez completado el comando, utilice lo siguiente para conectarse a la base de datos mediante TSQL:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Una vez conectado, utilice las instrucciones siguientes para comprobar que los datos se exportaron a la tabla **mobiledata**:

        SELECT * FROM mobiledata
        GO

    Debería ver una lista de los datos de la tabla. Escriba `exit` para salir de la utilidad de tsql.

##Importación de Sqoop

1. Use lo siguiente para importar datos desde la tabla **mobiledata** de la base de datos SQL al directorio **wasb:///tutorials/usesqoop/importeddata** en HDInsight:

        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

    Los datos importados tendrán campos separados por un carácter de tabulación y las líneas terminarán con un carácter de nueva línea.

2. Una vez completada la importación, use el siguiente comando para enumerar los datos en el nuevo directorio:

        hadoop fs -text wasb:///tutorials/usesqoop/importeddata/part-m-00000

##Uso de SQL Server

También puede utilizar Sqoop para importar y exportar datos de SQL Server, tanto en el centro de datos como en una máquina Virtual hospedada en Azure. Las diferencias entre el uso de la base de datos SQL y SQL Server son:

* HDInsight y SQL Server deben estar en la misma red virtual de Azure

    > [AZURE.NOTE] HDInsight solo admite redes virtuales basadas en la ubicación y actualmente no funciona con redes virtuales basadas en grupos de afinidad.

    Cuando use SQL Server en el centro de datos, debe configurar la red virtual como de *sitio a sitio* o de *punto a sitio*.

    > [AZURE.NOTE] En el caso d las redes virtuales de **punto a sitio**, SQL Server debe ejecutarse en la aplicación de configuración de clientes VPN, que se encuentra disponible en el **Panel** de la configuración de red virtual de Azure.

    Para obtener más información sobre la creación y la configuración de una red virtual, consulte [Tareas de configuración de la red virtual](../services/virtual-machines/).

* SQL Server estar configurado para permitir la autenticación SQL. Para obtener más información, consulte [Elegir un modo de autenticación](https://msdn.microsoft.com/ms144284.aspx).

* Es posible que tenga que configurar SQL Server para aceptar conexiones remotas. Para obtener más información, consulte [Solución de problemas de conexión al motor de base de datos de SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)

* Debe crear la base de datos **sqooptest** en SQL Server con una utilidad como **SQL Server Management Studio** o **tsql**. Los pasos para utilizar Azure CLI solo funcionan para la base de datos SQL de Azure

    Las instrucciones de TSQL para crear la tabla **mobiledata** son similares a las que se utilizan para la base de datos SQL, a excepción de la creación de un índice agrupado. Esto no es necesario para SQL Server:

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])

* Cuando se conecta a SQL Server desde HDInsight, es posible que deba utilizar la dirección IP de SQL Server a menos que haya configurado un Sistema de nombres de dominio (DNS) para resolver los nombres de la Red virtual de Azure. Por ejemplo:

        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

##Pasos siguientes

Ahora ya ha aprendido a usar Sqoop. Para obtener más información, consulte:

- [Uso de Oozie con HDInsight][hdinsight-use-oozie]: use la acción Sqoop en un flujo de trabajo de Oozie.
- [Análisis de la información de retraso de vuelos con HDInsight][hdinsight-analyze-flight-data]: use Hive para analizar la información de retraso de los vuelos y luego use Sqoop para exportar los datos a una base de datos SQL de Azure.
- [Carga de datos en HDInsight][hdinsight-upload-data]: busque otros métodos para cargar datos en HDInsight o el almacenamiento de blobs de Azure.



[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

<!---HONumber=AcomDC_0218_2016-->