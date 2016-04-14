<properties
   pageTitle="Procesamiento de eventos desde centros de eventos con Storm en HDInsight con Java | Azure"
   description="Obtenga información sobre cómo procesar los datos de los Centros de eventos con una topología Java de Storm creada con Maven."
   services="hdinsight,notification hubs"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/01/2016"
   ms.author="larryfr"/>

# Procesamiento de eventos desde Centros de eventos de Azure con Storm en HDInsight (Java)

Los Centros de eventos de Azure le permiten procesar grandes volúmenes de datos provenientes de sitios web, aplicaciones y dispositivos. El spout de los Centros de eventos permite que Apache Storm en HDInsight analice fácilmente estos datos en tiempo real. También es posible escribir datos en Centros de eventos desde Storm usando el bolt de Centros de eventos.

En este tutorial, obtendrá información sobre cómo usar el spout y bolt de Centros de eventos para leer y escribir datos en una topología de Storm basada en Java.

## Requisitos previos

* Un clúster de Apache Storm en HDInsight Use uno de los siguientes artículos de introducción para crear un clúster:

    - Un [clúster basado en Linux](hdinsight-apache-storm-tutorial-get-started-linux.md): seleccione esta opción si desea usar SSH para trabajar con el clúster de clientes Windows, Unix, OS X o Linux

    - Un [clúster basado en Windows](hdinsight-apache-storm-tutorial-get-started.md): seleccione esta opción si desea usar PowerShell para trabajar con el clúster de clientes Windows

    > [AZURE.NOTE] La única diferencia entre los tipos de dos clústeres es si se usa SSH para enviar la topología al clúster o un formulario web.

* Un [Centro de eventos de Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

* [Oracle Java Developer Kit (JDK) versión 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) o equivalente, como [OpenJDK](http://openjdk.java.net/)

* [Maven](https://maven.apache.org/download.cgi): Maven es un sistema de compilación de proyectos para proyectos de Java.

* Un editor de texto o un entorno de desarrollo integrado (IDE) de Java

	> [AZURE.NOTE] El editor o IDE puede tener una funcionalidad específica para trabajar con Maven que no se trata en este documento. Para obtener información sobre las capacidades de su entorno de edición, consulte la documentación del producto que esté utilizando.

 * Un cliente SSH. Para obtener más información sobre el uso de SSH con HDInsight, consulte uno de los siguientes artículos:

    - [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

* Un cliente de SCP. Esto se ofrece con todos los sistemas Linux, Unix y OS X. Para clientes Windows se recomienda PSCP, que está disponible en la [página de descarga de PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##Descripción del ejemplo

El ejemplo [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) contiene dos topologías:

__com.microsoft.example.EventHubWriter__ escribe datos aleatorios en un Centro de eventos de Azure. Los datos los genera un spout y son un identificador de dispositivo y un valor del dispositivo aleatorios. Por lo tanto simula un hardware que emite un identificador de cadena y un valor numérico.

__com.microsoft.example.EventHubReader__ lee los datos desde Centro de eventos (es decir, los datos escritos por EventHubWriter) y los almacena en HDFS (WASB en este caso, ya que esto se escribió y se probó con HDInsight de Azure) en el directorio /devicedata.

Con el formato de los datos como un documento JSON antes de escribir en el concentrador de eventos y cuando los lee el lector se analiza de JSON a de tuplas. El formato de la dirección JSON es el siguiente:

    { "deviceId": "unique identifier", "deviceValue": some value }

La razón para usar un documento JSON para almacenar los datos en Centro de eventos es por lo que sabemos cuál es el formato, en lugar de confiar en los mecanismos internos de formato de Spout y bolt de Centro de eventos.

###Configuración de proyecto

El archivo **POM.xml** contiene información de configuración para este proyecto Maven. Las partes interesantes son:

####La dependencia de Spout de EventHubs Storm

    <dependency>
      <groupId>com.microsoft.eventhubs</groupId>
      <artifactId>eventhubs-storm-spout</artifactId>
      <version>0.9.3</version>
    </dependency>

Esto agrega una dependencia para el paquete eventhubs-storm-spout, que contiene tanto un spout para la lectura de Centros de eventos y un bolt para escribir en los mismos.

> [AZURE.NOTE] Este paquete no está disponible en Maven y se instala manualmente en el repositorio local e Maven en un paso posterior.

####Los componentes HdfsBolt y WASB

El HdfsBolt se usa normalmente para almacenar datos en el sistema de archivos distribuidos Hadoop (HDFS). Sin embargo, los clústeres de HDInsight usan Almacenamiento de Azure (WASB) como almacén de datos predeterminado, por lo que es necesario cargar varios componentes que permiten a HdfsBolt comprender el sistema de archivos WASB.

      <!--HdfsBolt stuff -->
      <dependency>
        <groupId>org.apache.storm</groupId>
        <artifactId>storm-hdfs</artifactId>
        <exclusions>
          <exclusion>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
          </exclusion>
          <exclusion>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-hdfs</artifactId>
          </exclusion>
        </exclusions>
        <version>0.9.3</version>
      </dependency>
      <!--
     This is a temporary workaround to make HdfsBolt work with WASB through hadoop-azure project.
     For now, we have to build hadoop-client, hadoop-hdfs and hadoop-azure from Hadoop trunk
     (which defaults to 3.0.0-SNAPSHOT version). And push those jars and dependencies to local
     mvn repo (take a look at push_lib_mvn.ps1).

     Once Hadoop 2.7 is released, we can just switch to that version.
     Note that hadoop-azure is added to Hadoop on Hadoop 2.7.
     -->
     <dependency>
       <groupId>org.apache.hadoop</groupId>
       <artifactId>hadoop-client</artifactId>
       <version>3.0.0-SNAPSHOT</version>
     </dependency>
     <dependency>
       <groupId>org.apache.hadoop</groupId>
       <artifactId>hadoop-hdfs</artifactId>
       <version>3.0.0-SNAPSHOT</version>
     </dependency>
     <dependency>
       <groupId>org.apache.hadoop</groupId>
       <artifactId>hadoop-azure</artifactId>
       <version>3.0.0-SNAPSHOT</version>
     </dependency>
     <dependency>
       <groupId>org.apache.hadoop</groupId>
       <artifactId>hadoop-common</artifactId>
       <version>3.0.0-SNAPSHOT</version>
       <exclusions>
         <exclusion>
           <groupId>org.slf4j</groupId>
           <artifactId>slf4j-log4j12</artifactId>
         </exclusion>
       </exclusions>
     </dependency>
     <dependency>
       <groupId>com.microsoft.windowsazure.storage</groupId>
       <artifactId>microsoft-windowsazure-storage-sdk</artifactId>
       <version>0.6.0</version>
     </dependency>

> [AZURE.NOTE] Los paquetes para habilitar WASB no están disponibles en el repositorio de Maven y se instalarán manualmente en un paso posterior.

####El complemento maven-compiler-plugin

    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>2.3.2</version>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>

Le dice a Maven que el proyecto se debe compilar con compatibilidad para Java 7, que es lo que sirve para los clústeres de HDInsight.

####El complemento maven-shade-plugin

      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.3</version>
        <configuration>
          <!-- Keep us from getting a can't overwrite file error -->
          <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
          </transformers>
          <!-- Keep us from getting a bad signature error -->
          <filters>
            <filter>
                <artifact>*:*</artifact>
                <excludes>
                    <exclude>META-INF/*.SF</exclude>
                    <exclude>META-INF/*.DSA</exclude>
                    <exclude>META-INF/*.RSA</exclude>
                </excludes>
            </filter>
        </filters>
        </configuration>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
          </execution>
        </executions>
      </plugin>

Se usa para empaquetar la solución en un uber jar que contiene el código del proyecto y las dependencias requeridas. También se usa para:

* Cambiar el nombre de los archivos de licencia para las dependencias: si no se hace, se puede producir un error en tiempo de ejecución en clústeres de HDInsight basados en Windows.

* Excluir seguridad o firmas: si no se hace, se puede producir un error en tiempo de ejecución en el clúster de HDInsight

####El complemento exec-maven-plugin

    <plugin>
      <groupId>org.codehaus.mojo</groupId>
      <artifactId>exec-maven-plugin</artifactId>
      <version>1.2.1</version>
      <executions>
        <execution>
        <goals>
          <goal>exec</goal>
        </goals>
        </execution>
      </executions>
      <configuration>
        <executable>java</executable>
        <includeProjectDependencies>true</includeProjectDependencies>
        <includePluginDependencies>false</includePluginDependencies>
        <classpathScope>compile</classpathScope>
        <mainClass>${storm.topology}</mainClass>
      </configuration>
    </plugin>

Permite ejecutar fácilmente la topología de forma local en el entorno de desarrollo usando el siguiente comando:

    mvn compile exec:java -Dstorm.topology=<CLASSNAME>

Por ejemplo: `mvn compile exec:java -Dstorm.topology=com.microsoft.example.EventHubWriter`.

####La sección de recursos

    <resources>
      <resource>
        <directory>${basedir}/conf</directory>
        <filtering>false</filtering>
        <includes>
          <include>EventHubs.properties</include>
          <include>core-site.xml</include>
        </includes>
      </resource>
    </resources>

Esto define los recursos necesarios para el proyecto:

- **EventHubs.properties**: contiene información que se usa para conectarse a un Centro de eventos de Azure
- **core-site.xml**: contiene información sobre Almacenamiento de Azure que usa el clúster de HDInsight.

Tiene que rellenar ambos con información sobre el clúster de concentrador de eventos y HDInsight.

##Configuración de las variables de entorno

Pueden establecer las siguientes variables de entorno al instalar Java y el JDK en la estación de trabajo de desarrollo. Sin embargo, debe comprobar que existen y que contienen los valores correctos para su sistema.

* **JAVA\_HOME**: debe apuntar al directorio donde está instalado Java Runtime Environment (JRE). Por ejemplo, en una distribución de Unix o Linux, debe tener un valor similar a `/usr/lib/jvm/java-7-oracle`. En Windows, tendría un valor similar a `c:\Program Files (x86)\Java\jre1.7`.

* **PATH**: debe contener las rutas de acceso siguientes:

	* **JAVA\_HOME** (o la ruta de acceso equivalente).

	* **JAVA\_HOME\\bin** (o la ruta de acceso equivalente).

	* El directorio donde está instalado Maven

## Configuración del centro de eventos

Centros de eventos es el origen de datos para este ejemplo. Utilice los pasos siguientes para crear un centro de eventos.

1. En el [Portal de Azure clásico](https://manage.windowsazure.com), seleccione **NUEVO** > **Bus de servicio** > **Centro de eventos** > **Creación personalizada**.

2. En la pantalla **Agregar un nuevo centro de eventos**, escriba un **Nombre del centro de eventos**, seleccione la **Región** donde se va a crear el centro y cree un nuevo espacio de nombres o seleccione uno existente. Haga clic en la **Flecha** para continuar.

	![página 1 del asistente](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

	> [AZURE.NOTE] Debe seleccionar la misma **Ubicación** de su servidor Storm en HDInsight para disminuir la latencia y los costos.

2. En la pantalla **Configuración del centro de eventos**, escriba los valores **Recuento de particiones** y **Retención de mensajes**. En este ejemplo, utilice un recuento de particiones de 10 y una retención de mensajes de 1. Anote el recuento de particiones, porque necesitará más adelante este valor.

	![página 2 del asistente](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. Una vez se ha creado el concentrador de eventos, seleccione el espacio de nombres, seleccione **Concentradores de eventos** y, después, seleccione el concentrador de eventos que creó anteriormente.

4. Seleccione **Configurar** y cree dos directivas de acceso con la información siguiente:

	<table>
	<tr><th>Nombre</th><th>Permisos</th></tr>
	<tr><td>Escritor</td><td>Los métodos Send</td></tr>
	<tr><td>Lector</td><td>Escuchar</td></tr>
	</table>

	Después de crear los permisos, seleccione el icono **Guardar** en la parte inferior de la página. Con esto se crean las directivas de acceso compartido que se usarán para enviar (escritor) y escuchar (lector) a este Centro de eventos.

	![directivas](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. Después de guardar las directivas, use el **generador de claves de acceso compartido** que se encuentra en la parte inferior de la página para recuperar la clave para las directivas de **escritor** y **lector**. Guárdelas, ya que se usarán después.

## Descarga y compilación del proyecto.

1. Descargue el proyecto de GitHub: [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub). Puede descargar el paquete como un archivo zip o usar [git](https://git-scm.com/) para clonar el proyecto de forma local.

2. Use los comandos siguientes para instalar paquetes incluidos en el proyecto en el repositorio local de Maven. Estos habilitan el spout y bolt de Centro de eventos, así como la capacidad de usar HdfsBolt para escribir en Almacenamiento de Azure (WASB).

		mvn -q install:install-file -Dfile=lib/eventhubs/eventhubs-storm-spout-0.9.3-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9.3 -Dpackaging=jar

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-azure-3.0.0-SNAPSHOT.jar

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-client-3.0.0-SNAPSHOT.jar

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-hdfs-3.0.0-SNAPSHOT.jar

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-common-3.0.0-SNAPSHOT.jar -DpomFile=lib/hadoop/hadoop-common-3.0.0-SNAPSHOT.pom

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-project-dist-3.0.0-SNAPSHOT.pom -DpomFile=lib/hadoop/hadoop-project-dist-3.0.0-SNAPSHOT.pom

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-project-3.0.0-SNAPSHOT.pom -DpomFile=lib/hadoop/hadoop-project-3.0.0-SNAPSHOT.pom

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-main-3.0.0-SNAPSHOT.pom -DpomFile=lib/hadoop/hadoop-main-3.0.0-SNAPSHOT.pom

	> [AZURE.NOTE] Si usa Powershell, es posible que tenga que poner los parámetros `-D` entre comillas. Por ejemplo: `"-Dfile=lib/hadoop/hadoop-main-3.0.0-SNAPSHOT.pom"`.

	Estos archivos provienen de https://github.com/hdinsight/hdinsight-storm-examples, por lo que puede encontrar las últimas versiones ahí.

3. Para compilar y empaquetar el proyecto, use lo siguiente:

        mvn package

    Esto descargará las dependencias requeridas, compilación y, a continuación, el paquete del proyecto. La salida se almacenará en el directorio __/target__ como __EventHubExample-1.0-SNAPSHOT.jar__.

## Implementación de las topologías

El jar creado por este proyecto contiene dos topologías; __com.microsoft.example.EventHubWriter__ y __com.microsoft.example.EventHubReader__. La topología de EventHubWriter debe iniciarse en primer lugar, ya que escribe eventos en Centro de eventos que luego se leen en EventHubReader.

###Si se usa un clúster basado en Linux

1. Use SCP para copiar el paquete jar en su clúster de HDInsight. Reemplace USERNAME con el usuario SSH para el clúster. Reemplace CLUSTERNAME por el nombre del clúster de HDInsight:

        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    Si usó una contraseña para la cuenta SSH, se le pedirá que escriba la contraseña. Si usó una clave SSH con la cuenta, puede que necesite usar el parámetro `-i` para especificar la ruta de acceso al archivo de clave. Por ejemplo: `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`.

    > [AZURE.NOTE] Si el cliente es una estación de trabajo de Windows, puede que no tenga instalado un comando de SCP. Se recomienda PSCP que puede descargarse desde la [página de descarga de PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

    Este comando copiará el archivo en el directorio principal del usuario SSH en el clúster.

1. Cuando termine de cargar el archivo, use SSH para conectarse con el clúster de HDInsight. Reemplace **USERNAME** por el nombre de su inicio de sesión de SSH. Reemplace **CLUSTERNAME** por el nombre del clúster de HDInsight.

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [AZURE.NOTE] Si usó una contraseña para la cuenta SSH, se le pedirá que escriba la contraseña. Si usó una clave SSH con la cuenta, puede que necesite utilizar el parámetro `-i` para especificar la ruta de acceso al archivo de clave. En el ejemplo siguiente se cargará la clave privada desde `~/.ssh/id_rsa`:
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

    Si usa PuTTY, escriba `CLUSTERNAME-ssh.azurehdinsight.net` en el campo __Host name (o IP address)__ (Nombre de Host o Dirección IP) y, a continuación, haga clic en __Open__ (Abrir) para conectarse. Se le pedirá que escriba el nombre de cuenta de SSH.

    > [AZURE.NOTE] Si usó una contraseña para la cuenta SSH, se le pedirá que escriba la contraseña. Si usó una clave SSH con la cuenta, puede que necesite seguir los pasos a continuación para seleccionar la clave:
    >
    > 1. En **Category** (Categoría), expanda **Connection** (Conexión), **SSH** y, a continuación, seleccione **Auth** (Autenticar).
    > 2. Haga clic en **Browse** (Examinar) y seleccione el archivo .ppk que contiene su clave privada.
    > 3. Haga clic en __Open__ (Abrir) para conectarse.

2. Use el comando siguiente para iniciar las topologías.

        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubWriter writer
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubReader reader

    Esto iniciará las topologías y les dará un nombre descriptivo de "lectura" y "escritura".

3. Espere un minuto o dos para que las topologías escriban y lean los eventos desde Centro de eventos y, a continuación, use el siguiente comando para comprobar que EventHubReader está almacenando los datos en el almacenamiento de HDInsight:

        hadoop fs -ls /devicedata

    Esto debería devolver una lista de archivos similar a la siguiente:

        -rw-r--r--   1 storm supergroup      10283 2015-08-11 19:35 /devicedata/wasbbolt-14-0-1439321744110.txt
        -rw-r--r--   1 storm supergroup      10277 2015-08-11 19:35 /devicedata/wasbbolt-14-1-1439321748237.txt
        -rw-r--r--   1 storm supergroup      10280 2015-08-11 19:36 /devicedata/wasbbolt-14-10-1439321760398.txt
        -rw-r--r--   1 storm supergroup      10267 2015-08-11 19:36 /devicedata/wasbbolt-14-11-1439321761090.txt
        -rw-r--r--   1 storm supergroup      10259 2015-08-11 19:36 /devicedata/wasbbolt-14-12-1439321762679.txt

    > [AZURE.NOTE] Algunos archivos pueden mostrar un tamaño de 0, ya que EventHubReader los creó pero aún no tienen datos almacenados.

    Puede ver el contenido de estos archivo mediante el siguiente comando:

        hadoop fs -text /devicedata/*.txt

    Esto devolverá un valor similar al siguiente:

        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574

    La primera columna contiene el valor de identificador de dispositivo y la segunda el valor del dispositivo.

4. Use el comando siguiente para detener las topologías.

        storm kill reader
        storm kill writer

###Si se usa un clúster basado en Windows

1. En el explorador, abra https://CLUSTERNAME.azurehdinsight.net. Cuando se le pida, escriba las credenciales de administrador para el clúster de HDInsight. Llegará al Panel de Storm.

2. Use la lista desplegable __Jar File__ (Archivo Jar) para examinar y seleccionar el archivo EventHubExample-1.0-SNAPSHOT.jar desde el entorno de compilación.

3. Para __Class Name__ (Nombre de clase), escriba `com.mirosoft.example.EventHubWriter`.

4. Para __Additional Parameters__ (Parámetros adicionales), escriba `writer`. Por último, haga clic en __Submit__ (Enviar) para cargar el archivo jar e iniciar la topología de EventHubWriter.

5. Una vez que se inicie la topología, use el formulario para iniciar EventHubReader:

    * __Jar File__ (Archivo Jar): seleccione el archivo EventHubExample-1.0-SNAPSHOT.jar que se cargó previamente
    * __Class Name__ (Nombre de clase): escriba `com.microsoft.example.EventHubReader`.
    * __Additional Parameters__ (Parámetros adicionales): escriba `reader`.

    Haga clic en Submit (Enviar) para iniciar la topología de EventHubReader.

6. Espere unos minutos para permitir a las topologías generar eventos y almacenarlos a continuación, en Almacenamiento de Azure, a continuación, seleccione la pestaña __Query Console__ (Consola de consultas) situada en la parte superior de la página del __Panel de Storm__.

7. En __Query Console__ (Consola de consultas), seleccione __Hive Editor__ (Editor Hive) y reemplace el valor predeterminado `select * from hivesampletable` con lo siguiente:

        create external table devicedata (deviceid string, devicevalue int) row format delimited fields terminated by ',' stored as textfile location 'wasb:///devicedata/';
        select * from devicedata limit 10;

    Haga clic en __Select__ (Seleccionar) para ejecutar la consulta. Esto devolverá a través de EventHubReader, 10 filas de los datos escritos en Almacenamiento de Azure (WASB). Una vez terminada la consulta, debería ver datos similares a los siguientes:

        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574

8. Seleccione el __Panel de Storm__ en la parte superior de la página, a continuación, seleccione __IU de Storm__. Desde __IU de Storm__, seleccione el vínculo de la topología de __lectura__ y, a continuación, use el botón __Kill__ para detener la topología. Repita el proceso para la topología de __escritura__.



### Puntos de control

El elemento EventHubSpout envía periódicamente puntos de control de su estado al nodo Zookeeper, que almacena el desplazamiento actual de los mensajes leídos de la cola. Esto permite que el componente empiece a recibir mensajes en el desplazamiento almacenado en los escenarios siguientes:

* Se produce un error en la instancia del componente y se reinicia.

* Aumenta o reduce el clúster, mediante la incorporación o eliminación de nodos.

* Se elimina y reinicia la topología **con el mismo nombre**.

####En clústeres de HDInsight basados en Windows

También puede exportar e importar los puntos de control persistentes a WASB (Almacenamiento de Azure que usa su clúster de HDInsight). Los scripts para hacerlo se encuentran en el clúster de HDInsight en Storm, en **c:\\apps\\dist\\storm-0.9.3.2.2.1.0-2340\\zkdatatool-1.0\\bin**.

>[AZURE.NOTE] El número de versión en la ruta de acceso puede ser diferente, ya que la versión de Storm instalada en el clúster puede cambiar en el futuro.

Los scripts en este directorio son:

* **stormmeta\_import.cmd**: importar todos los metadatos de Storm desde el contenedor de almacenamiento del clúster predeterminado a Zookeeper.

* **stormmeta\_export.cmd**: exportar todos los metadatos de Storm de Zookeeper en el contenedor de almacenamiento del clúster predeterminado.

* **stormmeta\_delete.cmd**: eliminar todos los metadatos de Storm de Zookeeper.

La exportación de una importación permite conservar los datos de puntos de control cuando deba eliminar el clúster, pero quiera reanudar el procesamiento desde el desplazamiento actual en el centro cuando vuelva a poner en línea un nuevo clúster.

> [AZURE.NOTE] Dado que los datos se conservan en el contenedor de almacenamiento predeterminado, el nuevo clúster **debe** utilizar la misma cuenta de almacenamiento y el mismo contenedor que el clúster anterior.

##Solución de problemas

Si no ve los archivos que se almacenan en la ubicación /devicedata (bien usando el comando `hadoop fs -ls /devicedata` o el comando de Hive en Query Console) use la interfaz de usuario de Storm para buscar los errores devueltos por las topologías.

Para obtener más información sobre el uso de IU de Storm, vea los siguientes temas:

* Si está usando Storm __basado en Linux__ en un clúster de HDInsight, consulte [Implementación y administración de topologías de Apache Storm en HDInsight basado en Linux](hdinsight-storm-deploy-monitor-topology-linux.md)

* Si está usando Storm __basado en Windows__ en un clúster de HDInsight, consulte [Implementación y administración de topologías de Apache Storm en HDInsight basado en Windows](hdinsight-storm-deploy-monitor-topology-linux.md)

##Pasos siguientes

* [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md)

<!---HONumber=AcomDC_0302_2016-->