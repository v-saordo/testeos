<properties
pageTitle="Uso del Almacén de Azure Data Lake con Apache Storm en HDInsight de Azure"
description="Aprenda a escribir datos en Almacén de Azure Data Lake desde una topología de Apache Storm en HDInsight. Este documento y el ejemplo asociado muestran cómo se puede usar el componente HdfsBolt para escribir en Almacén de Data Lake."
services="hdinsight"
documentationCenter="na"
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="01/28/2016"
ms.author="larryfr"/>

#Uso del Almacén de Azure Data Lake con Apache Storm con HDInsight

Almacén de Azure Data Lake es un servicio de almacenamiento en la nube compatible con HDFS que ofrece alto rendimiento, disponibilidad, durabilidad y confiabilidad de los datos. En este documento, aprenderá a usar una topología de Storm basada en Java para escribir datos en Almacén de Azure Data Lake mediante el componente [HdfsBolt](http://storm.apache.org/javadoc/apidocs/org/apache/storm/hdfs/bolt/HdfsBolt.html), que se ofrece como parte de Apache Storm.

> [AZURE.IMPORTANT] La topología de ejemplo usada en este documento se basa en componentes que se incluyen con clústeres de Storm en HDInsight y pueden que requiera modificaciones para que funcione con Azure Data Lake Store cuando se usa con otros clústeres de Apache Storm.

##Requisitos previos

* [Java JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) o superior
* [Maven 3.x](https://maven.apache.org/download.cgi)
* Una suscripción de Azure
* Clúster de Apache Storm en HDInsight En este documento se incluye información sobre cómo crear un clúster que pueda usar el Almacén de Azure Data Lake.

###Configuración de las variables de entorno

Pueden establecer las siguientes variables de entorno al instalar Java y el JDK en la estación de trabajo de desarrollo. Sin embargo, debe comprobar que existen y que contienen los valores correctos para su sistema.

* __JAVA\_HOME__: debe apuntar al directorio donde está instalado Java Runtime Environment (JRE). Por ejemplo, en una distribución de Unix o Linux, debe tener un valor similar a `/usr/lib/jvm/java-7-oracle`. En Windows, tendría un valor similar a `c:\Program Files (x86)\Java\jre1.7`.

* __PATH__: debe contener las rutas de acceso siguientes:

    * __JAVA\_HOME__ (o la ruta de acceso equivalente).
    
    * __JAVA\_HOME\\bin__ (o la ruta de acceso equivalente).
    
    * El directorio donde está instalado Maven

##Implementación de la topología

El ejemplo usado en este documento está escrito en Java y utiliza los siguientes componentes:

* __TickSpout__: genera los datos que usan otros componentes de la topología.

* __PartialCount__: recuentos de eventos generados por TickSpout.

* __FinalCount__: agrega datos de recuento de PartialCount.

* __ADLStoreBolt__: escribe datos en Almacén de Azure Data Lake mediante el componente [HdfsBolt](http://storm.apache.org/javadoc/apidocs/org/apache/storm/hdfs/bolt/HdfsBolt.html).

Está disponible como descarga en el proyecto que contiene esta topología [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

###Descripción de ADLStoreBolt

ADLStoreBolt es el nombre que usa la instancia de HdfsBolt en la topología que escribe en Azure Data Lake. No se trata de una versión especial de HdfsBolt creada por Microsoft; pero sí se basa en valores de configuración del sitio principal, así como los componentes de Hadoop que se incluye con HDInsight de Azure para la comunicación con Data Lake.

En concreto, cuando crea un clúster de HDInsight, puede asociarlo a un Almacén de Azure Data Lake. Escribe entradas en el sitio principal del Almacén Data Lake seleccionado, que se usan en componentes como hadoop-client y hadoop-hdfs para habilitar la comunicación con Almacén Data Lake.

> [AZURE.NOTE] Microsoft ha aportado código para los proyectos de Apache Storm y Apache Hadoop que permite la comunicación con Almacenamiento de blobs de Azure y Almacén Azure Data Lake Store, pero puede que esta funcionalidad no se incluya de manera predeterminada en otras distribuciones de Hadoop y Storm.

La configuración de HdfsBolt en la topología es como sigue:

    // 1. Create sync and rotation policies to control when data is synched
    //    (written) to the file system and when to roll over into a new file.
    SyncPolicy syncPolicy = new CountSyncPolicy(1000);
    FileRotationPolicy rotationPolicy = new FileSizeRotationPolicy(0.5f, Units.KB);
    // 2. Set the format. In this case, comma delimited
    RecordFormat recordFormat = new DelimitedRecordFormat().withFieldDelimiter(",");
    // 3. Set the directory name. In this case, '/stormdata/'
    FileNameFormat fileNameFormat = new DefaultFileNameFormat().withPath("/stormdata/");
    // 4. Create the bolt using the previously created settings,
    //    and also tell it the base URL to your Data Lake Store.
    // NOTE! Replace 'MYDATALAKE' below with the name of your data lake store.
    HdfsBolt adlsBolt = new HdfsBolt()
		.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")
      	.withRecordFormat(recordFormat)
      	.withFileNameFormat(fileNameFormat)
      	.withRotationPolicy(rotationPolicy)
      	.withSyncPolicy(syncPolicy);
    // 4. Give it a name and wire it up to the bolt it accepts data
    //    from. NOTE: The name used here is also used as part of the
    //    file name for the files written to Data Lake Store.
    builder.setBolt("ADLStoreBolt", adlsBolt, 1)
      .globalGrouping("finalcount");
      
Si está familiarizado con el uso de HdfsBolt, observará que toda la configuración es prácticamente estándar excepto en cuanto a la dirección URL. La dirección URL proporciona la ruta de acceso a la raíz de su Almacén de Azure Data Lake.

Puesto que la escritura en el almacén de Data Lake usa HdfsBolt, y solo se trata de un cambio de dirección URL, debe poder utilizar cualquier topología existente que escriba en HDFS o WASB con HdfsBolt y cambiarla fácilmente para que use Almacén de Azure Data Lake.

##Aprovisionamiento de un clúster de HDInsight con el Almacén de Data Lake

Cree un nuevo clúster de Storm en HDInsight siguiendo los pasos descritos en el documento [Use HDInsight with Data Lake Store using Azure (Uso de HDInsight con Almacén Data Lake mediante Azure)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Los pasos descritos en este documento le guiarán por la creación de un clúster de HDInsight y Almacén de Azure Data Lake.

> [AZURE.IMPORTANT] Al crear el clúster de HDInsight, debe seleccionar __Storm__ como tipo de clúster. El sistema operativo puede ser Windows o Linux.

##Compilación y empaquetado de la topología

1. Descargue el proyecto de ejemplo de [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) en su entorno de desarrollo.

2. Abra el archivo `StormToDataLake\src\main\java\com\microsoft\example\StormToDataLakeStore.java` en un editor y busque la línea que contiene `.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")`. Cambie __MYDATALAKE__ por el nombre del Almacén de Azure Data Lake que usó al crear el servidor de HDInsight.

3. Desde un símbolo del sistema, un terminal o la sesión del shell, cambie los directorios a la raíz del proyecto descargado y ejecute los siguientes comandos para compilar y empaquetar la topología.

        mvn compile
        mvn package
    
    Al finalizar la compilación y el empaquetado, habrá un nuevo directorio denominado `target`, que contiene un archivo denominado `StormToDataLakeStore-1.0-SNAPSHOT.jar`. Contiene la topología compilada.

##Implementación y ejecución en HDInsight basado en Linux

Si creó un clúster de Storm basado en Linux en HDInsight, siga los pasos indicados a continuación para implementar y ejecutar la topología.

1. Use el siguiente comando para copiar la topología en el clúster de HDInsight. Reemplace __USER__ por el nombre de usuario SSH que usó al crear el clúster. Reemplace __CLUSTERNAME__ por el nombre del clúster.

        scp target\StormToDataLakeStore-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToDataLakeStore-1.0-SNAPSHOT.jar
    
    Cuando se le solicite, escriba la contraseña que se usó al crear el usuario SSH del clúster. Si usó una clave pública en lugar de una contraseña, tal vez tenga que usar el parámetro `-i` para especificar la ruta de acceso a la correspondiente clave privada.
    
    > [AZURE.NOTE] Si usa un cliente Windows para el desarrollo, puede que no tenga un `scp` comando. En ese caso, puede usar `pscp`, que está disponible en [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

2. Una vez finalizada la carga, use lo siguiente para conectarse al clúster de HDInsight con SSH. Reemplace __USER__ por el nombre de usuario SSH que usó al crear el clúster. Reemplace __CLUSTERNAME__ por el nombre del clúster.

        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net

    Cuando se le solicite, escriba la contraseña que se usó al crear el usuario SSH del clúster. Si usó una clave pública en lugar de una contraseña, tal vez tenga que usar el parámetro `-i` para especificar la ruta de acceso a la correspondiente clave privada.
    
    > [AZURE.NOTE] Si usa un cliente Windows para el desarrollo, consulte [Conexión con HDInsight basado en Linux con SSH desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md) para obtener información sobre el uso de un cliente PuTTY para conectarse al clúster.
    
3. Después de que se ha conectado, use lo siguiente para iniciar la topología:

        storm jar StormToDataLakeStore-1.0-SNAPSHOT.jar com.microsoft.example.StormToDataLakeStore datalakewriter
    
    Se iniciará la topología con un nombre descriptivo de `datalakewriter`.

##Implementación y ejecución en HDInsight basado en Windows

1. Abra un explorador web y navegue a HTTPS://CLUSTERNAME.azurehdinsight.net, donde __CLUSTERNAME__ es el nombre del clúster de HDInsight. Cuando se le solicite, proporcione el nombre de usuario administrador (`admin`) y la contraseña que usó para esta cuenta cuando se creó el clúster.

2. En el panel de Storm, seleccione __Examinar__ en la lista desplegable __Archivo del producto__ y, luego, el archivo StormToDataLakeStore-1.0-SNAPSHOT.jar del directorio `target`. Use los siguientes valores para las demás entradas del formulario:

    * Nombre de clase: com.microsoft.example.StormToDataLakeStore
    * Parámetros adicionales: datalakewriter
    
    ![imagen del panel de storm](./media/hdinsight-storm-write-data-lake-store/submit.png)

3. Seleccione el botón __Enviar__ para cargar la topología e iniciarla. El campo de resultado situado debajo del botón __Enviar__ debe mostrar información similar a la siguiente cuando se haya iniciado la topología:

        Process exit code: 0
        Currently running topologies:
        Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
        -------------------------------------------------------------------
        datalakewriter       ACTIVE     68         8            10        

##Visualización de datos de salida

Hay varias maneras de ver los datos. En esta sección se utiliza el Portal de Azure y el comando `hdfs` para verlos.

> [AZURE.NOTE] Debe dejar que las topologías se ejecuten durante varios minutos antes de comprobar los datos de salida para que los datos se hayan sincronizado en varios archivos Almacén de Azure Data Lake.

* __Desde el [Portal de Azure](https://portal.azure.com)__: en el portal, seleccione el Almacén de Azure Data Lake que usó con HDInsight.

    > [AZURE.NOTE] Si no ancló el Almacén Data Lake al panel del portal de Azure, puede encontrarlo seleccionando __Examinar__ en la parte inferior de la lista de la izquierda, luego __Almacén Data Lake__ y, por último, el almacén.
    
    En los iconos en la parte superior del Almacén Data Lake, seleccione __Explorador de datos__.
    
    ![icono de exploración de datos](./media/hdinsight-storm-write-data-lake-store/dataexplorer.png)
    
    A continuación, seleccione la carpeta __stormdata__. Debe aparecer una lista de archivos de texto.
    
    ![archivos de texto](./media/hdinsight-storm-write-data-lake-store/stormoutput.png)
    
    Seleccione uno de los archivos para ver su contenido.

* __Desde el clúster__: si se ha conectado al clúster de HDInsight con SSH (clúster de Linux) o con Escritorio remoto (clúster de Windows), puede utilizar lo siguiente para ver los datos. Reemplace __DATALAKE__ por el nombre de su Almacén Data Lake.

        hdfs dfs -cat adl://DATALAKE.azuredatalakestore.net/stormdata/*.txt

    Esto concatenará los archivos de texto almacenados en el directorio y mostrará información similar a la siguiente:
    
        406000000
        407000000
        408000000
        409000000
        410000000
        411000000
        412000000
        413000000
        414000000
        415000000
        
##Detención de la topología

Las topologías de Storm se ejecutarán hasta que se detengan o se elimine el clúster. Use la información siguiente para detener las topologías.

__Para HDInsight basado en Linux__:

Desde una sesión de SSH en el clúster, use el siguiente comando:

    storm kill datalakewriter

__Para HDInsight basado en Windows__:

1. En el panel de Storm (https://CLUSTERNAME.azurehdinsight.net,), seleccione el vínculo __IU de Storm__ en la parte superior de la página.

2. Cuando se cargue la IU de Storm, seleccione el vínculo __datalakewriter__.

    ![vínculo a datalakewriter](./media/hdinsight-storm-write-data-lake-store/selecttopology.png)

3. En la sección __Acciones de topología__, seleccione __Terminar__ y luego Aceptar en el cuadro de diálogo que aparece.

    ![acciones de topología](./media/hdinsight-storm-write-data-lake-store/topologyactions.png)

##Pasos siguientes

Ahora que ha aprendido a usar Storm para escribir en Almacén de Azure Data Lake, descubra otros [Ejemplos de Storm para HDInsight](hdinsight-storm-example-topology.md).

<!---HONumber=AcomDC_0204_2016-->