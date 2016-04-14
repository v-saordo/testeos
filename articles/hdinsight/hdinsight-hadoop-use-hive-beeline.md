<properties
   pageTitle="Uso de Beeline para trabajar con Hive en HDInsight (Hadoop) | Microsoft Azure"
   description="Obtenga información acerca de cómo usar SSH para conectarse a un clúster de Hadoop en HDInsight y, luego, enviar interactivamente consultas de Hive mediante el uso de Beeline. Beeline es una utilidad para trabajar con HiveServer2 sobre JDBC."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/16/2016"
   ms.author="larryfr"/>

#Uso de Hive con Hadoop en HDInsight con Beeline

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

En este artículo, aprenderá a usar Secure Shell (SSH) para conectarse a un clúster de HDInsight basado en Linux y, luego, a enviar de manera interactiva consultas de Hive usando la herramienta de línea de comandos de [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell).

> [AZURE.NOTE] Beeline usa JDBC para conectarse a Hive. Para obtener más información sobre el uso de JDBC con Hive, consulte [Conexión a Hive en Azure HDInsight con el controlador JDBC de Hive](hdinsight-connect-hive-jdbc-driver.md).

##<a id="prereq"></a>Requisitos previos

Para completar los pasos de este artículo, necesitará lo siguiente:

* Un clúster de Hadoop basado en Linux en HDInsight.

* Un cliente SSH. Linux, Unix y Mac OS deben incluir un cliente SSH. Los usuarios de Windows deben descargar un cliente similar [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Conexión con SSH

Conéctese con el nombre de dominio completo (FQDN) de su clúster de HDInsight mediante el comando SSH. El FQDN será el nombre que ha asignado al clúster, es decir, **.azurehdinsight.net**. Por ejemplo, lo siguiente debería conectarse a un clúster denominado **myhdinsight**.

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Si proporcionó una clave de certificado para la autenticación de SSH**, al crear el clúster de HDInsight, es posible que tenga que especificar la ubicación de la clave privada en el sistema cliente:

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Si proporcionó una contraseña para la autenticación de SSH**, al crear el clúster de HDInsight, tendrá que proporcionar la contraseña cuando se le solicite.

Para obtener más información sobre el uso de SSH con HDInsight, consulte [Uso de SSH con Hadoop basado en Linux en HDInsight desde Linux, OS X y Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###PuTTY (clientes basados en Windows)

Windows no proporciona ningún cliente SSH integrado. Se recomienda usar **PuTTY**, que se puede descargar en [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obtener más información sobre el uso de PuTTY, consulte [Uso de SSH con Hadoop basado en Linux en HDInsight desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="beeline"></a>Uso del comando de Beeline

1. Una vez conectado, use lo siguiente para obtener el nombre de host del nodo principal:

        hostname -f
    
    Guarde el nombre de host devuelto, ya que se utilizará más adelante al conectarse a HiveServer2 desde Beeline.
    
2. Inicie la CLI de Hive con el siguiente comando:

        beeline

2. En el símbolo del sistema de `beeline>`, use el siguiente código para conectarse al servicio HiveServer2. Reemplace __HOSTNAME__ por el nombre de host devuelto para el nodo principal anteriormente:

        !connect jdbc:hive2://HOSTNAME:10001/;transportMode=http admin

    Cuando se le pida, escriba la contraseña del administrador (admin) para el clúster de HDInsight. Una vez establecida la conexión, el símbolo del sistema cambiará a:
    
        jdbc:hive2://HOSTNAME:10001/>

3. Los comandos de Beeline suelen empiezan con un carácter `!`, por ejemplo `!help` muestra la ayuda. Sin embargo, con frecuencia se puede omitir `!`. Por ejemplo, `help` también funcionará.

    Si ve la ayuda, observará `!sql`, que se usa para ejecutar instrucciones de HiveQL. Sin embargo, HiveQL es de uso tan frecuente que puede omitir el `!sql` que le precede. Las dos instrucciones siguientes tienen exactamente los mismos resultados: la visualización de las tablas disponibles actualmente a través de Hive:
    
        !sql show tables;
        show tables;
    
    En un nuevo clúster, solo debe aparecer una tabla: __hivesampletable__.

4. Use el siguiente código para mostrar el esquema de hivesampletable:

        describe hivesampletable;
        
    Se devolverá la siguiente información:
    
        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    Esto muestra las columnas de la tabla. Aunque podríamos realizar algunas consultas en estos datos, vamos a crear esta vez una nueva tabla para demostrar cómo cargar datos en Hive y aplicar un esquema.
    
5. Escriba las siguientes instrucciones para crear una nueva tabla denominada **log4jLogs** con los datos de ejemplo proporcionados con el clúster de HDInsight:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Estas instrucciones realizan las acciones siguientes:

    * **DROP TABLE**: elimina la tabla y el archivo de datos si la tabla ya existe.
    * **CREATE EXTERNAL TABLE**: crea una tabla "externa" nueva en Hive. Las tablas externas solo almacenan la definición de Tabla en Hive. Los datos permanecen en la ubicación original.
    * **ROW FORMAT**: indica cómo se da formato a los datos de Hive. En este caso, los campos de cada registro se separan mediante un espacio.
    * **STORED AS TEXTFILE LOCATION**: indica a Hive dónde se almacenan los datos (el directorio example/data) y que se almacenan como texto.
    * **SELECT**: selecciona un número de todas las filas donde la columna **t4** contiene el valor **[ERROR]**. Esto debe devolver un valor de **3** ya que hay tres filas que contienen este valor.
    * **INPUT\_\_FILE\_\_NAME LIKE '%.log'**: indica a Hive que solo deberíamos devolver datos de archivos que terminan en .log. Normalmente, solo tendría datos con el mismo esquema dentro de la misma carpeta al realizar consultas con Hive; sin embargo, este archivo de registro de ejemplo se almacena con otros formatos de datos.

    > [AZURE.NOTE] Las tablas externas se deben usar cuando espera que un origen externo, como por ejemplo un proceso de carga de datos automático, u otra operación MapReduce, actualice los datos subyacentes, pero siempre desea que las consultas de Hive usen los datos más recientes.
    >
    > La eliminación de una tabla externa **no** elimina los datos, solamente la definición de tabla.
    
    El resultado de este comando debe ser similar al siguiente:
    
        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :
        
        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)
        
        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

4. Para salir de Beeline, use `!quit`.

##<a id="file"></a>Ejecución de un archivo de HiveQL

Beeline también se puede usar para ejecutar un archivo que contiene instrucciones de HiveQL. Use los pasos siguientes para crear un archivo y, luego, ejecútelo mediante Beeline.

1. Use el comando siguiente para crear un nuevo archivo denominado __query.hql__:

        nano query.hql
        
2. Cuando se abra el editor, use lo siguiente como contenido del archivo: Esta consulta creará una nueva tabla 'interna' denominada **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Estas instrucciones realizan las acciones siguientes:

    * **CREATE TABLE IF NOT EXISTS**: crea una tabla, si todavía no existe. Dado que la palabra clave **EXTERNAL** no se usa, se trata de una tabla interna, que se almacena en el almacenamiento de datos de Hive y es administrada por Hive.
    * **STORED AS ORC**: almacena los datos en el formato Optimized Row Columnar (ORC). Se trata de un formato altamente optimizado y eficiente para almacenar datos de Hive.
    * **INSERT OVERWRITE ... SELECT**: selecciona filas de la tabla **log4jLogs** que contienen **[ERROR]** y, a continuación, inserta los datos en la tabla **errorLogs**.
    
    > [AZURE.NOTE] A diferencia de las tablas externas, la eliminación de una tabla interna también eliminará los datos subyacentes.
    
3. Para guardar el archivo, use __Ctrl__+___\_X__, escriba __Y__ y, finalmente, presione __Entrar__.

4. Use el siguiente código para ejecutar el archivo mediante Beeline: Sustituya __HOSTNAME__ por el nombre obtenido anteriormente para el nodo principal, y __PASSWORD__ por la contraseña de la cuenta de administrador:

        beeline -u 'jdbc:hive2://HOSTNAME:10001/;transportMode=http' -n admin -p PASSWORD -f query.hql

5. Para comprobar que la tabla **errorLogs** se creó, inicie Beeline y conéctese a HiveServer2, luego use la siguiente instrucción para devolver todas las filas de **errorLogs**:

        SELECT * from errorLogs;

    Se deben devolver tres filas de datos y todas ellas deben contener **[ERROR]** en la columna t4.
    
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

##<a id="summary"></a>Resumen

Como puede ver, el comando de Beeline proporciona una manera fácil de ejecutar consultas de Hive de manera interactiva en un clúster de HDInsight.

##<a id="nextsteps"></a>Pasos siguientes

Para obtener información general acerca de Hive en HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

Para obtener información sobre otras formas en que puede trabajar con Hadoop en HDInsight:

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)

* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

Si usa Tez con Hive, consulte los siguientes documentos para la información de depuración:

* [Use the Tez UI on Windows-based HDInsight](hdinsight-debug-tez-ui.md) (Uso de la IU de Tez en HDInsight basado en Windows)

* [Use the Ambari Tez view on Linux-based HDInsight](hdinsight-debug-ambari-tez-view.md) (Uso de la vista Tez de Ambari en HDInsight basado en Linux)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

<!---HONumber=AcomDC_0218_2016-->