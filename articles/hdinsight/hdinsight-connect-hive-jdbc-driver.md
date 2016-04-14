<properties
 pageTitle="Usar JDBC para realizar consultas Hive en HDInsight de Azure"
 description="Aprenda a utilizar JDBC para conectarse a Hive en HDInsight de Azure y ejecutar consultas en datos almacenados en la nube de forma remota."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="java"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="02/01/2016"
 ms.author="larryfr"/>

#Conexión a Hive en HDInsight de Azure con el controlador JDBC de Hive

[AZURE.INCLUDE [Selector ODBC-JDBC](../../includes/hdinsight-selector-odbc-jdbc.md)]

En este documento, aprenderá utilizar JDBC desde una aplicación Java para enviar consultas de Hive de forma remota a un clúster de HDInsight. Para obtener más información sobre la interfaz JDBC de Hive, consulte[HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

##Requisitos previos

Para completar los pasos de este artículo, necesitará lo siguiente:

* Hadoop en un clúster de HDInsight. Funcionarán clústeres para Linux o para Windows.

* El [Kit de desarrolladores de Java (JDK) versión 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html), o superior.

* [Apache Maven](https://maven.apache.org). Maven es un sistema de creación de proyectos para proyectos de Java que utiliza el proyecto asociado a este artículo.

##Cadena de conexión

Las conexiones de JDBC a un clúster de HDInsight en Azure se realizan en el puerto 443 y el tráfico se protege mediante SSL. La puerta de enlace pública tras la que se encuentran los clústeres redirige el tráfico al puerto que HiveServer2 escucha. Por consiguiente, una cadena de conexión sería como la siguiente:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

##Autenticación

Al establecer la conexión, es preciso especificar el nombre y la contraseña de administrador del clúster de HDInsight. Estos autentican la solicitud a la puerta de enlace. Por ejemplo, el siguiente código Java abre una nueva conexión con la cadena de conexión, el nombre de administrador y la contraseña:

    DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);

##Consultas

Una vez establecida la conexión, se puede ejecutar consultas en Hive. Por ejemplo, el siguiente código Java ejecuta una cláusula __SELECT__ de una tabla, lo que limita los resultados a sólo tres filas y, a continuación, muestra los resultados:

    sql = "SELECT querytime, market, deviceplatform, devicemodel, state, country from " + tableName + " LIMIT 3";
    stmt2 = conn.createStatement();
    System.out.println("\nRetrieving inserted data:");

    res2 = stmt2.executeQuery(sql);

    while (res2.next()) {
      System.out.println( res2.getString(1) + "\t" + res2.getString(2) + "\t" + res2.getString(3) + "\t" + res2.getString(4) + "\t" + res2.getString(5) + "\t" + res2.getString(6));
    }

##Proyecto de ejemplo de Java

Un ejemplo de uso de un cliente de Java para realizar una consulta Hive en HDInsight está disponible en [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Siga las instrucciones del repositorio para crear y ejecutar el ejemplo.

##Pasos siguientes

Ahora que aprendió a usar JDBC para que funcione con Hive, utilice los siguientes vínculos para explorar otras formas de trabajar con HDInsight de Azure.

* [Carga de datos en HDInsight](hdinsight-upload-data.md)
* [Uso de Hive con HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con HDInsight](hdinsight-use-pig.md)
* [Uso de trabajos de MapReduce con HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0204_2016-->