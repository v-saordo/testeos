<properties
   pageTitle="Uso de componentes de Python en una topología Storm en HDinsight | Microsoft Azure"
   description="Obtenga información acerca de cómo puede usar los componentes de Python con Apache Storm en HDInsight de Azure. Aprenderá a usar los componentes de Python desde una topología Storm basada en Clojure y una topología basada en Java."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="python"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/01/2016"
   ms.author="larryfr"/>

#Desarrollo de topologías Apache Storm con Python en HDInsight

Apache Storm admite varios lenguajes, e incluso le permite combinar componentes de varios lenguajes en una topología. En este documento, obtendrá información sobre cómo usar componentes de Python en las topologías Java y Storm basado en Clojure en HDInsight.

##Requisitos previos

* Python 2.7 o versiones posteriores

* Java JDK 1.7 o superior

* [Leiningen](http://leiningen.org/)

##Compatibilidad con varios lenguajes de Storm

Storm se diseñó para funcionar con componentes escritos con cualquier lenguaje de programación; sin embargo, para ello es necesario que los componentes comprendan cómo trabajar con la [definición de Thrift para Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Para Python, se proporciona un módulo como parte del proyecto de Apache Storm que le permite interactuar fácilmente con Storm. Puede encontrar este módulo en [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Como Apache Storm es un proceso de Java que se ejecuta en Java Virtual Machine (JVM), los componentes escritos en otros lenguajes se ejecutan como subprocesos. Los bits de Storm que se ejecutan en JVM se comunican con estos subprocesos mediante mensajes JSON enviados a través de stdin y stdout. Se puede encontrar más detalles sobre la comunicación entre los componentes en la documentación de [Multi-lang protocolo](https://storm.apache.org/documentation/Multilang-protocol.html) (Protocolo de varios lenguajes).

###La interfaz de usuario de Storm

El módulo de Storm (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py,) proporciona los bits necesarios para crear componentes de Python que funcionan con Storm.

Esto proporciona cosas como `storm.emit` para emitir tuplas, y `storm.logInfo` para escribir en los registros. Resulta conveniente echar un vistazo a este archivo y comprender lo que proporciona.

##Desafíos

Con el módulo __storm.py__, puede crear spouts de Python que consumen datos y bolts que procesan datos; sin embargo, la definición general de topología Storm que permite la comunicación entre los componentes se sigue escribiendo con Java o Clojure. Además, si usa Java, también debe crear componentes de Java que actúen como interfaz para los componentes de Python.

También, como los clústeres de Storm se ejecutan de forma distribuida, debe asegurarse de que los módulos que necesiten los componentes de Python estén disponibles en todos los nodos de trabajo del clúster. Storm no proporciona ninguna manera fácil de realizar esto con recursos de varios lenguajes, así que o bien deberá incluir todas las dependencias como parte del archivo jar en la topología, o instalar manualmente dependencias en cada nodo de trabajo del cluster.

Hay algunos proyectos que intentan superar estas limitaciones, como [Pyleus](https://github.com/Yelp/pyleus) y [Streamparse](https://github.com/Parsely/streamparse). Aunque ambos se pueden ejecutar en clústeres de HDInsight basados en Linux, no son el objetivo principal de este documento dado que es necesario personalizarlos durante la configuración del clúster y no se han probado completamente en clústeres de HDInsight. A final del documento se incluyen notas sobre el uso de estos marcos de trabajo con HDInsight.

###Definición de la topología Java frente a Clojure

De los dos métodos de la definición de una topología, Clojure es, sin duda, el más fácil y limpio dado que se puede hacer referencia directa a componentes de Python en la definición de la topología. En las definiciones de topologías basadas en Java, también debe definir componentes de Java que administren cosas como declarar los campos de las tuplas devueltas por los componentes de Python.

Ambos métodos se describen en este documento, junto con proyectos de ejemplo.

##Componentes de Python con una topología Java

> [AZURE.NOTE] Este ejemplo está disponible en [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) en el directorio de __JavaTopology__. Se trata de un proyecto basado en Maven. Si no está familiarizado con Maven,consulte [Desarrollo de topologías basadas en Java con Apache Storm en HDInsight](hdinsight-storm-develop-java-topology.md) para obtener más información sobre cómo crear un proyecto de Maven en una topología Storm.

Una topología basada en Java que usa Python (u otros componentes de lenguaje JVM) parece inicialmente que usa componentes de Java; pero si mira cada uno de los spouts o bolts de Java, verá código similar al siguiente:

    public SplitBolt() {
        super("python", "countbolt.py");
    }

Aquí es donde Java invoca a Python y ejecuta el script que contiene la lógica real del bolt. Los bolts y spouts de Java (en este ejemplo), simplemente declaran los campos de la tupla que emitirá el componente subyacente de Python.

Los archivos de Python reales se almacenan en el directorio `/multilang/resources` en este ejemplo. Se hace referencia al directorio `/multilang` en el archivo __pom.xml__:

<resources> <resource> <!-- Where the Python bits are kept --> <directory>${basedir} / multilang</directory> </resource> </resources>

Esto incluye todos los archivos de la carpeta `/multilang` del archivo jar que se generarán a partir de este proyecto.

> [AZURE.IMPORTANT] Tenga en cuenta que solo se especifica el directorio `/multilang` y no `/multilang/resources`. Storm no espera recursos de JVM en un directorio `resources`, por lo que se buscan ya internamente. La colocación de componentes en esta carpeta le permite simplemente hacer referencia por nombre en el código Java. Por ejemplo: `super("python", "countbolt.py");`. Otra manera de verlo es que Storm considera el directorio `resources` como la raíz (/) cuando se accede a recursos de varios lenguajes.
>
> En este proyecto de ejemplo, el `storm.py` módulo se incluye en el directorio `/multilang/resources`.

###Compilación y ejecución del proyecto

Para ejecutar este proyecto localmente, use el siguiente comando de Maven para compilar y ejecutar en modo local:

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

Use ctrl + c para terminar el proceso.

Para implementar el proyecto en un clúster de HDInsight mediante Apache Storm, siga estos pasos:

1. Cree un archivo uber-jar:

        mvn package

    Se creará un archivo denominado __WordCount--1.0 SNAPSHOT.jar__ en el directorio `/target` para este proyecto.

2. Cargue el archivo jar en el clúster de Hadoop mediante uno de los métodos siguientes:

    * En clústeres de HDInsight __basados en Linux__: use `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar` para copiar el archivo jar en el clúster, y sustituya USERNAME por el nombre de usuario de SSH y CLUSTERNAME por el nombre del clúster de HDInsight.

        Después de que el archivo ha terminado de cargarse, conéctese al clúster mediante SSH e inicie la topología con `storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount`

    * En clústeres de HDInsight __basados en Windows__: conéctese al panel de Storm; para ello, vaya a HTTPS://CLUSTERNAME.azurehdinsight.net/ en su explorador. Sustituya CLUSTERNAME por el nombre del clúster de HDInsight y proporcione el nombre y la contraseña de administrador cuando se le solicite.

        Mediante el formulario, realice las acciones siguientes:

        * __Jar File__ (Archivo jar): seleccione __Browse__ (Examinar) y luego seleccione el archivo __WordCount-1.0-SNAPSHOT.jar__.
        * __Class Name__ (Nombre de clase): escriba `com.microsoft.example.WordCount`.
        * __Additional Paramters__ (Parámetros adicionales): escriba un nombre descriptivo como `wordcount` para identificar la topología

        Por último, seleccione __Submit__ (Enviar) para iniciar la topología.

> [AZURE.NOTE] Cuando se ha iniciado una topología Storm, esta se ejecuta hasta que se detiene (termina). Para detener la topología, use el comando `storm kill TOPOLOGYNAME` desde la línea de comandos (por ejemplo, una sesión de SSH en un clúster de Linux), o bien, mediante la interfaz de usuario de Storm, seleccione la topología y, luego, seleccione el botón __Kill__ (Terminar).

##Componentes de Python con una topología Clojute

> [AZURE.NOTE] Este ejemplo está disponible en [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) en el directorio de __ClojureTopology__.

Esta topología se creó mediante [Leiningen](http://leiningen.org) para [crear un nuevo proyecto de Clojure](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project). Después de eso, se realizaron las siguientes modificaciones en el proyecto de scaffolding:

* __project.clj__: se agregaron dependencias para Storm y exclusiones para los elementos que pueden ocasionar problemas cuando se implementan en el servidor de HDInsight.
* __resources/resources__: Leiningen crea un directorio `resources` predeterminado, sin embargo, los archivos aquí almacenados parece que se agregan a la raíz del archivo jar creado con este proyecto, y Storm espera archivos en un subdirectorio llamado `resources`. Así que se agregó un subdirectorio y los archivos de Python se almacenan en `resources/resources`. En tiempo de ejecución, esto se considerará la raíz (/) para obtener acceso a los componentes de Python.
* __src/wordcount/core.clj__: este archivo contiene la definición de la topología y se hace referencia a él desde el archivo __project.clj__. Para obtener más información sobre el uso de Clojure para definir una topología Storm, consulte [Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html).

###Compilación y ejecución del proyecto

__Para compilar y ejecutar el proyecto localmente__, use el siguiente comando:

    lein do clean, run

Para detener la topología, use __Ctrl + C__.

__Para compilar un archivo uberjar e implementarlo en HDInsight__, siga estos pasos:

1. Cree un archivo uberjar que contenga la topología y las dependencias necesarias:

        lein uberjar

    Se creará un nuevo archivo llamado `wordcount-1.0-SNAPSHOT.jar` en el directorio `target\uberjar+uberjar`.
    
2. Use uno de los siguientes métodos para implementar y ejecutar la topología en un cluster de HDInsight:

    * __HDInsight basado en Linux__
    
        1. Copie el archivo en el nodo principal del clúster de HDInsight con `scp`. Por ejemplo:
        
                scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
                
            Reemplace USERNAME por un usuario de SSH de su clúster y CLUSTERNAME por el nombre del clúster de HDInsight.
            
        2. Después de que se ha copiado el archivo en el clúster, use SSH para conectarse al clúster y enviar el trabajo. Para obtener más información sobre el uso de SSH con HDInsight, consulte una de las siguientes secciones:
        
            * [Uso de SSH con HDInsight basado en Linux desde Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
            * [Uso de SSH con HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
            
        3. Después de que se ha conectado, use lo siguiente para iniciar la topología:
        
                storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
    
    * __HDInsight basado en Windows__
    
        1. Conéctese al panel de Storm; para ello, vaya a HTTPS://CLUSTERNAME.azurehdinsight.net/ en el explorador. Sustituya CLUSTERNAME por el nombre del clúster de HDInsight y proporcione el nombre y la contraseña de administrador cuando se le solicite.

        2. Mediante el formulario, realice las acciones siguientes:

            * __Jar File__ (Archivo jar): seleccione __Browse__ (Examinar) y luego seleccione el archivo __WordCount-1.0-SNAPSHOT.jar__.
            * __Class Name__ (Nombre de clase): escriba `wordcount.core`.
            * __Additional Paramters__ (Parámetros adicionales): escriba un nombre descriptivo como `wordcount` para identificar la topología

            Por último, seleccione __Submit__ (Enviar) para iniciar la topología.

> [AZURE.NOTE] Cuando se ha iniciado una topología Storm, esta se ejecuta hasta que se detiene (termina). Para detener la topología, use el comando `storm kill TOPOLOGYNAME` desde la línea de comandos (una sesión de SSH en un clúster de Linux), o bien, mediante la interfaz de usuario de Storm, seleccione la topología y luego seleccione el botón __Kill__ (Terminar).

##Marco Pyleus

[Pyleus](https://github.com/Yelp/pyleus) es un marco que intenta facilitar el uso de Python con Storm al proporcionar lo siguiente:

* __Definiciones de topología basada en YAML__: esto proporciona una manera más fácil para definir la topología, que no necesita conocimientos de Java o Clojure.
* __Serializador basado en MessagePack__: MessagePack se usa como la serialización predeterminada, en lugar de JSON. Como resultado, los componentes se envían mensajes de manera más rápida.
* __Administración de dependencias__: Virtualenv se usa para garantizar que las dependencias de Python se implementan en todos los nodos de trabajo. Para ello es necesario que Virtualenv esté instalado en los nodos de trabajo.

> [AZURE.IMPORTANT] Pyleus necesita Storm en el entorno de desarrollo. El uso de la distribución base de Apache Storm 0.9.3 parece dar lugar a archivos jar que son incompatibles con la versión de Storm que se proporciona con HDInsight. Así que en los siguientes pasos se usa el clúster de HDInsight como entorno de desarrollo.

Puede compilar correctamente las topologías Pyleus de ejemplo y usar el nodo principal de HDInsight como entorno de compilación:

1. Al aprovisionar un nuevo clúster de Storm en HDInsight, debe asegurarse de que Python Virtualenv esté presente en los nodos del clúster. Al crear un nuevo clúster de HDInsight basado en Linux, use la siguiente configuración de acción de script con [Personalización del clúster](hdinsight-hadoop-customize-cluster.md):

    * __Name__ (Nombre): basta con que proporcione aquí un nombre descriptivo.
    * \_\_ Script URI\_\_(URI de script): use `https://hditutorialdata.blob.core.windows.net/customizecluster/pythonvirtualenv.sh` como valor. Este script instalará Python Virtualenv en los nodos.
    
        > [AZURE.NOTE] También creará algunos directorios que más tarde se usarán con el marco Streamparse en este documento.
        
    * __Nimbus__: compruebe esta entrada para que el script se aplique a los nodos Nimbus (principales).
    * __Supervisor__: compruebe esta entrada para que el script se aplique a los nodos de supervisor (trabajo).
    
    Deje las demás entradas en blanco.

1. Después de que el clúster se ha creado, conéctese con SSH:

    * [Uso de SSH con HDInsight basado en Linux desde Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Uso de SSH con HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. En la conexión SSH, use lo siguiente para crear un nuevo entorno virtual e instalar Pyleus:

        virtualenv pyleus_venv
        source pyleus_venv
        pip install pyleus

3. Luego, descargue el repositorio git de Pyleus y compile el ejemplo de WordCount:

        sudo apt-get install git
        git clone https://github.com/Yelp/pyleus.git
        pyleus build pyleus/examples/word_count/pyleus_topology.yaml
    
    Cuando finalice la compilación, tendrá un nuevo archivo llamado `word_count.jar` en el directorio actual.
    
4. Para enviar la topología al clúster de Storm, use el comando siguiente:

        pyleus submit -n localhost word_count.jar
    
    El parámetro `-n` especifica el host de Nimbus. Puesto que estamos en el nodo principal, podemos usar `localhost`.
    
    También puede usar el comando `pyleus` para realizar otras acciones de Storm. Use el siguiente código para mostrar las topologías que se están ejecutando y luego termine la topología `word_count`:
    
        pyleus list -n localhost
        pyleus kill -n localhost word_count

##Marco Streamparse

[Streamparse](https://github.com/Parsely/streamparse) es un marco que intenta facilitar el uso de Python con Storm al proporcionar lo siguiente:

* __Scaffolding__: permite crear fácilmente el scaffolding de un proyecto y luego modificar los archivos para agregar su lógica.
* __Funciones Clojure DSL__: permiten reducir el nivel de detalle del uso de componentes de Python en una definición de topología Clojure.
* __Administración de dependencias__: Virtualenv se usa para garantizar que las dependencias de Python se implementan en todos los nodos de trabajo. Para ello es necesario que Virtualenv esté instalado en los nodos de trabajo.
* __Implementación remota__: Streamparse puede usar la automatización de SSH para implementar componentes en los nodos de trabajo y puede crear un túnel SSH para comunicarse con Nimbus. De este modo, puede implementar fácilmente desde su entorno de desarrollo en un clúster basado en Linux como HDInsight.

> [AZURE.IMPORTANT] Streamparse se basa en componentes que esperan [señales de Unix](https://en.wikipedia.org/wiki/Unix_signal), que no están disponibles en Windows. El entorno de desarrollo debe ser Linux, Unix o OS X y el clúster de HDInsight debe estar basado en Linux.

1. Al aprovisionar un nuevo clúster de Storm en HDInsight, debe asegurarse de que Python Virtualenv esté presente en los nodos del clúster. Al crear un nuevo clúster de HDInsight basado en Linux, use la siguiente configuración de acción de script con [Personalización del clúster](hdinsight-hadoop-customize-cluster.md):

    * __Name__ (Nombre): basta con que proporcione aquí un nombre descriptivo.
    * \_\_ Script URI\_\_(URI de script): use `https://hditutorialdata.blob.core.windows.net/customizecluster/pythonvirtualenv.sh` como valor. Este scrip instala Python Virtualenv en los nodos, y también crea directorios que se usan en Streamparse
    * __Nimbus__: compruebe esta entrada para que el script se aplique a los nodos Nimbus (principales).
    * __Supervisor__: compruebe esta entrada para que el script se aplique a los nodos de supervisor (trabajo).
    
    Deje las demás entradas en blanco.
    
    > [AZURE.WARNING] También debe usar una __clave pública__ para proteger al usuario de SSH en el clúster de HDInsight a fin de implementarlo de manera remota mediante Streamparse.
    >
    > Para obtener más información sobre el uso de SSH en HDInsight, consulte uno de los siguientes documentos:
    >
    > * [Uso de SSH con HDInsight basado en Linux desde Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    > * [Uso de SSH con HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Mientras se está aprovisionando el clúster, instale Streamparse en el entorno de desarrollo con el comando siguiente:

        pip install streamparse
        
3. Después de instalar Streamparse, use el comando siguiente para crear un proyecto de ejemplo:

        sparse quickstart wordcount
        
    Se creará un nuevo directorio llamado `wordcount` y se rellenará con un proyecto de ejemplo de recuento de palabras.

4. Cambie los directorios al directorio `wordcount` e inicie la topología en modo local:

        cd wordcount
        sparse run

    Use Ctrl+C para detener la topología.

###Implementación de la topología

Después de crear el clúster de HDInsight basado en Linux, siga estos pasos para implementar la topología en el clúster:

1. Use el comando siguiente para buscar los nombres de dominio completos de los nodos de trabajo del clúster:

        curl -u admin:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts | grep '"host_name" : "worker'
    
    De esta forma se recupera la información de los hosts del clúster, se canaliza a grep y se devuelve en las entradas de los nodos de trabajo. Debería ver resultados similares a los siguientes:
    
        "host_name" : "workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"
    
    Guarde la información `"workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"`, la usará en el siguiente paso.

2. Abra el archivo __config.json__ del directorio `wordcount` y cambie las siguientes entradas:

    * __user__: establezca esta entrada en el nombre de la cuenta de usuario de SSH que configuró para el clúster de HDInsight. Este nombre se usará para autenticarse en el clúster al implementar el proyecto
    * __nimbus__: establezca esta entrada en `CLUSTERNAME-ssh.azurehdinsight.net`. Reemplace CLUSTERNAME por el nombre del clúster. Este nombre se usa al comunicarse con el nodo Nimbus, que es el nodo principal del clúster.
    * __workers__: rellene la entrada de trabajos con los nombres de host de los nodos de trabajo que recuperó mediante curl. Por ejemplo:
    
        ```
"workers": [
    "workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net",
    "workernode1.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"
    ]
        ```
    
    * __virtualenv\_root__: establezca esta entrada en "/virtualenv".
    
    Así se configura el proyecto para el clúster de HDInsight, incluido el directorio `/virtualenv` que se creó durante el aprovisionamiento mediante la acción de script.

4. Dado que en la implementación de Streamparse en HDInsight se debe reenviar la autenticación mediante el nodo principal a los nodos de trabajo, `ssh-agent` se debe iniciar en la estación de trabajo. En la mayoría de los sistemas operativos, se inicia automáticamente. Use el siguiente comando para comprobar que se está ejecutando:

        echo "$SSH_AUTH_SOCK"
    
    Se devolverá una respuesta similar a la siguiente si `ssh-agent` se está ejecutando:
    
        /tmp/ssh-rfSUL1ldCldQ/agent.1792
    
    > [AZURE.NOTE] La ruta de acceso completa puede ser diferente según el sistema operativo. Por ejemplo, en OS X la ruta de acceso puede ser similar a `/private/tmp/com.apple.launchd.vq2rfuxaso/Listeners`. Pero se debe devolver alguna ruta si se está ejecutando el agente.
    
    Si no se devuelve nada, use el comando `ssh-agent` para iniciar el agente.
    
5. Compruebe que el agente conoce la clave que se usa para autenticarse en el servidor de HDInsight. Use el siguiente comando para mostrar las claves que están disponibles para el agente:

        ssh-add -L
    
    Se devuelven las claves privadas que se han agregado al agente. Puede comparar los resultados con el contenido de la clave privada que generó al crear una clave SSH para autenticarse en HDInsight.
    
    Si no se devuelve información o la información devuelta no coincide con la clave privada, use el siguiente código para agregar la clave privada al agente:
    
        ssh-add /path/to/key/file
    
    Por ejemplo: `ssh-add ~/.ssh/id_rsa`.

4. También debe configurar SSH para que sepa que se debe usar el reenvío en el clúster de HDInsight. Agregue lo siguiente a `~/.ssh/config`. Si este archivo no existe, debe crearlo y usar lo siguiente como contenido:

        Host *.azurehdinsight.net
          ForwardAgent yes
        
        Host *.internal.cloudapp.net
          ProxyCommand ssh CLUSTERNAME-ssh.azurehdinsight.net nc %h %p
    
    Reemplace CLUSTERNAME por el nombre del clúster de HDInsight.
    
    De esta manera, se configura el agente SSH en la estación de trabajo para permitir el reenvío de las credenciales de SSH mediante cualquier sistema *. azurehdinsight.net al que se conecte. En este caso, el nodo principal del clúster. A continuación, se configura el comando usado para redirigir el tráfico de SSH mediante proxy desde el nodo principal a los nodos de trabajo individuales (internal.cloudapp.net). Esto permite que Streamparse se conecte al nodo principal y, desde ahí, a cada uno de los nodos de trabajo, mediante la autenticación de clave de la cuenta SSH.
    
5. Por último, use el siguiente comando para enviar la topología desde el entorno de desarrollo local al clúster de HDInsight:

        sparse submit
    
    Como resultado se conectará al clúster de HDInsight, se implementará la topología y las dependencias de Python y luego se iniciará la topología.

##Pasos siguientes

En este documento, aprendió a usar los componentes de Python en una topología Storm. Consulte los siguientes documentos para ver otras maneras de usar Python con HDInsight.

* [Uso de Python para la transmisión de trabajos de MapReduce](hdinsight-hadoop-streaming-python.md)
* [Uso de funciones definidas por el usuario (UDF) de Python en Pig y Hive](hdinsight-python.md)

<!---HONumber=AcomDC_0204_2016-->