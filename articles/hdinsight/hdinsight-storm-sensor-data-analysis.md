<properties
   pageTitle="Análisis de los datos de los sensores con Apache Storm y HBase | Microsoft Azure"
   description="Obtenga información acerca de cómo conectarse a Apache Storm con una red virtual. Utilice Storm con HBase para procesar los datos de sensores de un centro de eventos y visualizarlos con D3.js."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/28/2016"
   ms.author="larryfr"/>

# Análisis de datos de sensores con Apache Storm, Centro de eventos y HBase en HDInsight (Hadoop)

Aprenda a usar Apache Storm en HDInsight para procesar datos de sensores de los Centros de eventos de Azure y visualizarlos mediante D3.js. En este documento también se describe cómo usar una red virtual para conectar Storm en HDInsight con HBase en HDInsight y almacenar los datos de la topología en HBase.

> [AZURE.NOTE] La información contenida en este documento es específica de los clústeres Storm en HDInsight basados en Windows. Para obtener información sobre cómo trabajar con el Centro de eventos de Azure en Storm basado en Linux en HDInsight, vea [Procesamiento de eventos desde Centros de eventos de Azure con Storm en HDInsight](hdinsight-storm-develop-java-event-hub-topology.md)

## Requisitos previos

* Una suscripción de Azure. Consulte [Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Un [clúster de Apache Storm en HDInsight](hdinsight-apache-storm-tutorial-get-started.md)

* [Node.js](http://nodejs.org/): se usa con el panel web y para enviar datos de sensores al Centro de eventos.

* [Java y el JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

* [Maven](http://maven.apache.org/what-is-maven.html)

* [Git](http://git-scm.com/)

> [AZURE.NOTE] Java, el JDK, Maven y Git también están disponibles en el administrador de paquetes [Chocolatey NuGet](http://chocolatey.org/).

## Arquitectura

![diagrama de arquitectura](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

Este ejemplo consta de los siguientes componentes:

* **Centro de eventos de azure**: ofrece datos que se recopilan de los sensores. En este ejemplo, se ofrece una aplicación que genera datos falsos.

* **Storm en HDInsight**: ofrece procesamiento en tiempo real de datos desde el Centro de eventos.

* **HBase en HDInsight** (opcional): ofrece un almacén de datos NoSQL persistente.

* **Servicio de Red virtual de Azure** (opcional, necesario si se usa HBase): habilita las comunicaciones seguras entre el Storm en HDInsight y HBase en clústeres de HDInsight.

* **Sitio web del panel**: un panel de ejemplo que muestra datos en tiempo real.

	* El sitio web se implementa en Node.js, de forma que puede ejecutarse en cualquier sistema operativo cliente para realizar pruebas o se puede implementar en sitios web de Azure.

	* [Socket.io](http://socket.io/) se usa para la comunicación en tiempo real entre la topología Storm y el sitio web.

		> [AZURE.NOTE] Se trata de un detalle de implementación. Puede usar cualquier marco de comunicaciones, como SignalR o WebSockets sin procesar.

	* [D3.js](http://d3js.org/) se usa para representar los datos que se envían al sitio web.

La topología lee datos del Centro de eventos mediante la clase **com.microsoft.eventhubs.spout.EventHubSpout**, que se proporciona en el clúster de Storm en HDInsight. La comunicación con el sitio web se logra mediante [client.java socket.io](https://github.com/nkzawa/socket.io-client.java).

Opcionalmente, se consigue la comunicación con HBase mediante la clase [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/javadoc/apidocs/org/apache/storm/hbase/bolt/class-use/HBaseBolt.html), que se ofrece como parte de Storm.

A continuación, se muestra un diagrama de la topología.

![diagrama de topología](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [AZURE.NOTE] Se trata de una vista muy simplificada de la topología. En tiempo de ejecución, se crea una instancia de cada componente por cada partición del Centro de eventos que se está leyendo. Estas instancias se distribuyen entre los nodos del clúster y los datos se enrutan entre ellos como sigue:
>
> * Se equilibran las cargas de los datos del spout al analizador.
> * Los datos del analizador al panel y HBase (si se usa) se agrupan por Id. de dispositivo, para que los mensajes del mismo dispositivo fluyan siempre al mismo componente.

### Componentes

* **Spout del Centro de eventos**: el spout se ofrece como parte de los [Ejemplos de Storm de HDInsight](https://github.com/hdinsight/hdinsight-storm-examples) en GitHub.

* **ParserBolt.java**: los datos que emite el spout son JSON sin procesar y, en ocasiones, se emite más de un evento a la vez. Este bolt muestra cómo leer los datos que emite el spout, y los emite a una secuencia nueva como una tupla que contiene varios campos.

* **DashboardBolt.java**: esto muestra cómo usar la biblioteca de cliente de Socket.io para Java para enviar datos en tiempo real al panel web.

## Preparación del entorno

Antes de usar este ejemplo, debe crear un Centro de eventos de Azure, que la topología de Storm lea. También debe crear una topología de Storm en HDInsight, ya que el componente que se usa para leer los datos del Centro de eventos solo está disponible en el clúster.

> [AZURE.NOTE] Finalmente, el spout del Centro de eventos estará disponible desde Maven.

### Configuración del centro de eventos

Centro de eventos es el origen de datos para este ejemplo. Utilice los pasos siguientes para crear un centro de eventos.

1. En el [Portal de Azure clásico](https://manage.windowsazure.com), seleccione **NUEVO | Bus de servicio | Centro de eventos | Creación personalizada**.

2. En el cuadro de diálogo** Agregar un nuevo Centro de eventos**, escriba un **Nombre del centro de eventos**, seleccione la **Región** donde se va a crear el centro y cree un nuevo espacio de nombres o seleccione uno existente. Por último, haga clic en la flecha para continuar.

2. En el cuadro de diálogo **Configuración del Centro de eventos**, escriba los valores **Recuento de particiones** y **Retención de mensajes**. En este ejemplo, utilice un recuento de particiones de 10 y una retención de mensajes de 1.

3. Cuando se haya creado el Centro de eventos, seleccione el espacio de nombres y, después, seleccione **Centros de eventos**. Por último, seleccione el centro de eventos que ha creado antes.

4. Seleccione **Configurar** y cree dos directivas de acceso con la información siguiente.

	<table>
<tr><th>Nombre</th><th>Permisos</th></tr>
<tr><td>Dispositivos</td><td>Los métodos Send</td></tr>
<tr><td>Storm</td><td>Escuchar</td></tr>
</table>Después de crear los permisos, seleccione el icono **Guardar** de la parte inferior de la página. Se crean las directivas de acceso compartidas que se usarán para enviar mensajes a este centro, así como para leer mensajes de él.

5. Después de guardar las directivas, use el **Generador de claves de acceso compartido** en la parte inferior de la página para recuperar la clave de las directivas **dispositivos** y **storm**. Guárdelas porque las usará más adelante.

### Creación de un clúster de Storm en HDInsight

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/).

2. Haga clic en **HDInsight** en el panel de la izquierda y, después, haga clic en **+NUEVO** en la esquina inferior izquierda de la página.

3. Haga clic en el icono de HDInsight en la segunda columna y seleccione **Personalizado**.

4. En la página **Detalles del clúster**, escriba el nombre del nuevo clúster y seleccione **Storm** como **Tipo de clúster**. Haga clic en la flecha para continuar.

5. Especifique 1 como el número de **Nodos de datos** que se usarán en este clúster.

	> [AZURE.NOTE] Para minimizar el costo del clúster usado en este artículo, reduzca el **Tamaño del clúster** a 1 y elimine el clúster cuando haya terminado de usarlo.

6. Escriba el **Nombre de usuario** y la **Contraseña** del administrador y, después, haga clic en la flecha para continuar.

4. En **Cuenta de almacenamiento**, seleccione **Crear nuevo almacenamiento** o seleccione una cuenta de almacenamiento existente. Seleccione o escriba el **Nombre de cuenta** y el **Contenedor predeterminado** que se van a usar. Seleccione el icono de verificación de la parte inferior izquierda para crear el clúster de Storm.

## Descarga e instalación del elemento EventHubSpout

1. Descargue el [proyecto de ejemplos de Storm en HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/). Cuando se haya descargado, busque el archivo **lib/eventhubs/eventhubs-storm-spout-0.9-jar-with-dependencies.jar**.

2. Desde el símbolo del sistema, use el comando siguiente para instalar el archivo **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** en el almacén de Maven local. Esto permitirá agregarlo fácilmente como referencia al proyecto de Storm en un paso posterior.

		mvn install:install-file -Dfile=target/eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar

## Descarga y configuración del proyecto

Use lo siguiente para descargar el proyecto de GitHub.

	git clone https://github.com/Blackmist/hdinsight-eventhub-example

Cuando se haya completado el comando, tendrá la siguiente estructura de directorios:

	hdinsight-eventhub-example/
		TemperatureMonitor/ - this is the Java topology
			conf/
				Config.properties
				hbase-site.xml
			src/
			test/
			dashboard/ - this is the node.js web dashboard
			SendEvents/ - utilities to send fake sensor data

> [AZURE.NOTE] Este documento no profundiza en los detalles sobre el código incluido en este ejemplo; sin embargo, el código completo está comentado.

Abra el archivo **Config.properties** y agregue la información que uso anteriormente al crear el Centro de eventos. Guarde el archivo después de agregar esta información.

	eventhubspout.username = storm

	eventhubspout.password = <the key of the 'storm' policy>

	eventhubspout.namespace = <the event hub namespace>

	eventhubspout.entitypath = <the event hub name>

	eventhubspout.partitions.count = <the number of partitions for the event hub>

	## if not provided, will use storm's zookeeper settings
	## zookeeper.connectionstring=localhost:2181

	eventhubspout.checkpoint.interval = 10

	eventhub.receiver.credits = 1024

## Compilación y pruebas locales

Antes de las pruebas, debe iniciar el panel para ver el resultado de la topología y generar datos para almacenarlos en el Centro de eventos.

### Inicio de la aplicación web

1. Abra un nuevo símbolo de sistema o terminal, cambie los directorios a **hdinsight-eventhub-example/dashboard** y, después, use el siguiente comando para instalar las dependencias necesarias para la aplicación web:

		npm install

2. Use el comando siguiente para iniciar la aplicación web:

		node server.js

	Debe ver un mensaje similar al siguiente:

		Server listening at port 3000

2. Abra un explorador web y escriba ****http://localhost:3000/** como dirección. Debería ver una página similar a la siguiente:

	![panel web](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)

	Deje abierto este símbolo del sistema o terminal. Después de las pruebas, presione CTRL+C para detener el servidor web.

### Inicio de la generación de datos

> [AZURE.NOTE] Los pasos de esta sección usan Node.js para que se pueden ejecutar en cualquier plataforma. Para obtener ejemplos de otros lenguajes, consulte el directorio **SendEvents**.


1. Abra un nuevo símbolo de sistema o terminal, cambie los directorios a **hdinsight-eventhub-example/SendEvents/nodejs** y, después, use el siguiente comando para instalar las dependencias necesarias para la aplicación:

		npm install

2. Abra el archivo **app.js** en un editor de texto y agregue la información del Centro de eventos que obtuvo anteriormente:

		// ServiceBus Namespace
		var namespace = 'servicebusnamespace';
		// Event Hub Name
		var hubname ='eventhubname';
		// Shared access Policy name and key (from Event Hub configuration)
		var my_key_name = 'devices';
		var my_key = 'key';

2. Use el siguiente comando para insertar entradas nuevas en el Centro de eventos:

		node app.js

	Debe ver varias líneas de salida que contienen los datos enviados al Centro de eventos. Tendrán un aspecto similar al siguiente:

		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":0,"Temperature":7}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":1,"Temperature":39}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":2,"Temperature":86}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":3,"Temperature":29}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":4,"Temperature":30}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":5,"Temperature":5}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":6,"Temperature":24}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":7,"Temperature":40}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":8,"Temperature":43}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":9,"Temperature":84}

### Inicio de la topología

2. Inicie la topología de forma local mediante el comando siguiente

	mvn compile exec:java -Dstorm.topology=com.microsoft.examples.Temperature

	Esto iniciará la topología, leerá los archivos desde el Centro de eventos y los enviará al panel que se está ejecutando en los Sitios web de Azure. Debe ver que aparecen líneas en el panel web, parecidas a las siguientes.

	![panel con datos](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

3. Mientras se ejecuta el panel, use el comando `node app.js` de los pasos anteriores para enviar datos nuevos al panel. Dado que los valores de temperatura se generan aleatoriamente, el gráfico debe actualizarse para mostrar los valores nuevos.

3. Después de comprobar que esto funciona, presione CTRL+C para detener la topología. Para detener la aplicación SendEvent, seleccione la ventana y presione cualquier tecla. También puede presionar CTRL+C para detener el servidor web.

## Empaquetado e implementación de la topología en HDInsight

En su entorno de desarrollo, siga estos pasos para ejecutar la topología Temperature en el clúster de Storm en HDInsight.

### Publicación del panel del sitio web

1. Para implementar el panel en un sitio web de Azure, siga los pasos de [Compilación e implementación de un sitio web de Node.js en Azure](../app-service-web/web-sites-nodejs-develop-deploy-mac.md). Observe la dirección URL del sitio web, que será similar a **mywebsite.azurewebsites.net**.

2. Cuando se cree el sitio web, vaya al sitio en el Portal de Azure clásico y seleccione la pestaña **Configurar**. Habilite **Sockets web** y haga clic en **Guardar**, en la parte inferior de la página.

2. Abra **hdinsight-eventhub-example\\TemperatureMonitor\\src\\main\\java\\com\\microsoft\\examples\\bolts\\DashboardBolt.java** y cambie la línea siguiente para que señale a la dirección URL del panel publicado:

		socket = IO.socket("http://mywebsite.azurewebsites.net");

3. Guarde el archivo **DashboardBolt.java**.

### Empaquetado e implementación de la topología

1. Use el comando siguiente para crear un paquete JAR a partir del proyecto:

		mvn package

	Se creará un archivo denominado **TemperatureMonitor-1.0-SNAPSHOT.jar** en el directorio **target** del proyecto.

2. Siga los pasos de [Implementación y administración de topologías de Storm](hdinsight-storm-deploy-monitor-topology.md) para cargar e iniciar la topología en el clúster de Storm en HDInsight mediante el **panel Storm**.

3. Cuando se haya iniciado la topología, abra un explorador con el sitio web publicado en Azure y use el comando `node app.js` para enviar datos a un Centro de eventos. Debería ver que el panel web se actualiza para mostrar la información.

	![dashboard](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## Opcional: uso de HBase

Para usar Storm y HBase juntos, debe crear una Red virtual de Azure y, después, crear un clúster de Storm y HBase dentro de la red.

### Creación de una Red virtual de Azure (opcional)

Si tiene previsto usar HBase con este ejemplo, debe crear una Red virtual de Azure que contendrá un clúster de Storm en HDInsight y un clúster de HBase en HDInsight.

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).

2. En la parte inferior de la página, haga clic en **+NUEVO** > **Servicios de red** > **Red virtual** > **Creación rápida**.

3. Escriba o seleccione los valores siguientes:

	- **Nombre**: el nombre de la red virtual.

	- **Espacio de direcciones**: elija un espacio de direcciones para la red virtual que sea lo bastante grande para proporcionar direcciones para todos los nodos del clúster. De lo contrario, se producirá un error en el aprovisionamiento.

	- **Número máximo de máquinas virtuales**: elija uno de los números máximos de máquinas virtuales (VM).

	- **Ubicación**: la ubicación debe ser la misma que el clúster de HBase que va a crear

	- **Servidor DNS**: en este artículo se usa el servidor DNS interno que ofrece Azure, de modo que puede elegir **Ninguno**. También se admiten configuraciones de red más avanzadas con servidores DNS personalizados. Para obtener instrucciones detalladas, consulte [Resolución de nombres (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

4. Haga clic en **Crear una red virtual**. El nombre de la nueva red virtual aparecerá en la lista. Espere hasta que aparezca **Creado** en la columna de estado.

5. En el panel principal, haga clic en la red virtual que acaba de crear.

6. En la parte superior de la página, haga clic en **PANEL**.

7. En **vista rápida**, tome nota del **ID. DE RED VIRTUAL**. Lo necesitará al aprovisionar los clústeres de Storm y de HBase.

8. En la parte superior de la página, haga clic en **CONFIGURAR**.

9. En la parte inferior de la página, el nombre de subred predeterminada es **Subnet-1**. Use el botón **agregar subred** para agregar **Subnet-2**. Estas subredes albergarán los clústeres de Storm y de HBase.

	> [AZURE.NOTE] En este artículo, usaremos los clústeres con solo un nodo. Si está creando clústeres de varios nodos, debe comprobar el valor de **CIDR(RECUENTO DE DIRECCIONES)** de la subred que se usará para el clúster. El recuento de direcciones debe ser mayor que el número de nodos de trabajo más siete (Puerta de enlace: 2, Nodo principal: 2, Zookeeper: 3). Por ejemplo, si necesita un clúster de HBase de 10 nodos, el recuento de direcciones para la subred debe ser mayor que 17 (10+7). De lo contrario, se producirá un error en la implementación.
	>
	> Se recomienda encarecidamente designar una única subred para un clúster.

11. Haga clic en **Guardar** en la parte inferior de la página.

### Creación de un clúster de Storm y HBase en la red virtual

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/).

2. Haga clic en **HDInsight** en el panel de la izquierda y, después, haga clic en **+NUEVO** en la esquina inferior izquierda de la página.

3. Haga clic en el icono de HDInsight en la segunda columna y seleccione **Personalizado**.

4. En la página **Detalles del clúster**, escriba el nombre del nuevo clúster y seleccione **Storm** como **Tipo de clúster**. Haga clic en la flecha para continuar.

5. Especifique 1 como el número de **Nodos de datos** que se usarán en este clúster. En **Región/Red virtual**, seleccione la red virtual de Azure creada anteriormente. En **Subredes de la red virtual**, seleccione **Subnet-1**.

	> [AZURE.NOTE] Para minimizar el costo del clúster usado en este artículo, reduzca el **Tamaño del clúster** a 1 y elimine el clúster cuando haya terminado de usarlo.

6. Escriba el **Nombre de usuario** y la **Contraseña** del administrador y haga clic en la flecha para continuar.

4. En **Cuenta de almacenamiento**, seleccione **Crear nuevo almacenamiento** o seleccione una cuenta de almacenamiento existente. Seleccione o escriba el **Nombre de cuenta** y el **Contenedor predeterminado** que se van a usar. Seleccione el icono de verificación de la parte inferior izquierda para crear el clúster de Storm.

5. Repita estos pasos para crear un nuevo clúster de **HBase**. A continuación se muestran las diferencias principales:

	* **Tipo de clúster**: seleccione **HBase**

	* **Subredes de la red virtual**: seleccione **Subnet-2**.

	* **Storage Account**: debe usar un contenedor diferente al utilizado en el clúster de Storm.

### Detección del sufijo DNS de HBase

Para escribir en HBase desde el clúster de Storm, debe usar el nombre completo del clúster de HBase. Use el comando siguiente para detectar esta información:

	curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

En los datos de JSON devueltos, busque la entrada **"host\_name"**. Esta entrada contendrá el nombre de dominio completo (FQDN) de los nodos del clúster, por ejemplo:

	...
	"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
	...

La parte del nombre de dominio que comienza con el nombre del clúster es el sufijo DNS; por ejemplo, **mycluster.b1.cloudapp.net**.

### Habilitación del bolt de HBase

1. Abra **hdinsight-eventhub-example\\TemperatureMonitor\\conf\\hbase-site.xml** y reemplace las entradas `suffix` en la línea siguiente por el sufijo DNS que obtuvo anteriormente para el clúster de HBase. Después de realizar estos cambios, guarde el archivo.

		<value>zookeeper0.suffix,zookeeper1.suffix,zookeeper2.suffix</value>

	El bolt de HBase lo usará para comunicarse con el clúster de HBase.

1. Abra **hdinsight-eventhub-example\\TemperatureMonitor\\src\\main\\java\\com\\microsoft\\examples\\bolts** en un editor de texto y quite la marca de comentario de las líneas siguientes quitando los caracteres `//` del principio. Después de realizar este cambio, guarde el archivo.

		topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
    	  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());

	De esta forma se habilita el bolt de HBase.

	> [AZURE.NOTE] Solo debe habilitar el bolt de HBase al implementar en el clúster de Storm, no cuando se están realizando pruebas de forma local.

### Datos de Storm y HBase

Antes de ejecutar la topología, debe preparar HBase para que acepte los datos.

1. Use Escritorio remoto para conectarse al clúster de HBase.

2. En el escritorio, inicie la línea de comandos de HDInsight y escriba los comandos siguientes:

    cd %HBASE\_HOME% bin\\hbase shell

3. En el shell de HBase, escriba el comando siguiente para crear una tabla en la que se almacenarán los datos de los sensores:

    create 'SensorData', 'cf'

4. Escriba el comando siguiente para comprobar que la tabla no contiene datos:

    scan 'SensorData'

Cuando haya iniciado la topología en el clúster de Storm y se hayan procesado los datos, puede usar el comando `scan 'SensorData'` de nuevo para comprobar que los datos se han insertado en HBase.


## Pasos siguientes

Ahora ha aprendido a utilizar Storm para leer datos desde el Centro de eventos y mostrar información desde Storm en un panel externo mediante SignalR y D3.js. Si siguió los pasos opcionales, también ha aprendido cómo configurar HDInsight en una red virtual y cómo comunicarse entre una topología de Storm y HBase mediante el bolt de HBase.

* Para obtener más ejemplos de topologías de Storm con HDinsight, consulte:

    * [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md)

* Para obtener más información sobre Apache Storm, consulte el sitio de [Apache Storm](https://storm.incubator.apache.org/).

* Para obtener más información sobre HBase con HDInsight, consulte [Información general de HBase de HDInsight](hdinsight-hbase-overview.md).

* Para obtener más información sobre Socket.io, consulte el sitio de [socket.io](http://socket.io/).

* Para obtener más información sobre D3.js, consulte [D3.js: documentos controlados por datos](http://d3js.org/).

* Para obtener información sobre la creación de topologías en Java, consulte [Desarrollo de topologías de Java para Apache Storm en HDInsight](hdinsight-storm-develop-java-topology.md).

* Para obtener información sobre la creación de topologías en .NET, consulte [Desarrollo de topologías de C# para Apache Storm en HDInsight mediante Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://manage.windowsazure.com/

<!----HONumber=AcomDC_0204_2016-->