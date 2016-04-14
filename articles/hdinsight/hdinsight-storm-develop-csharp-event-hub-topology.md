<properties
   pageTitle="Procesamiento de eventos desde centros de eventos con Storm en HDInsight | Microsoft Azure"
   description="Aprenda a procesar datos de Centros de eventos con una topología de Storm de C# creada en Visual Studio con las herramientas de HDInsight para Visual Studio."
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
   ms.date="01/28/2016"
   ms.author="larryfr"/>

# Procesamiento de eventos desde Centros de eventos de Azure con Storm en HDInsight (C#)

Los Centros de eventos de Azure le permiten procesar grandes volúmenes de datos provenientes de sitios web, aplicaciones y dispositivos. El spout de los Centros de eventos permite que Apache Storm en HDInsight analice fácilmente estos datos en tiempo real. También es posible escribir datos en Centros de eventos desde Storm usando el bolt de Centros de eventos.

En este tutorial, aprenderá a utilizar las herramientas de HDInsight para Visual Studio y el spout y bolt de Centros de eventos para crear dos topologías híbridas C#/Java:

* **EventHubWriter**: genera datos de manera aleatoria y los escribe en los Centros de eventos.

* **EventHubReader**: lee datos de los Centros de eventos y los almacena en el almacenamiento de tablas de Azure.

[AZURE.NOTE] Los pasos de este tutorial solo se aplican a los clústeres de HDInsight basados en Windows. Para obtener una versión de Java de este proyecto, que funcionará con un clúster basado en Windows o en Linux, vea [Procesamiento de eventos desde Centros de eventos de Azure con Storm en HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).

## Requisitos previos

* Un [clúster de Apache Storm en HDInsight](hdinsight-apache-storm-tutorial-get-started.md)

* Un [Centro de eventos de Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

* El [SDK .NET de Azure](http://azure.microsoft.com/downloads/)

* Las [Herramientas de HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)

## Proyecto completado

Puede descargar una versión completa del proyecto creado en este tutorial desde GitHub: [eventhub-storm-hybrid](https://github.com/Blackmist/eventhub-storm-hybrid). Sin embargo, deberá proporcionar ajustes de configuración siguiendo los pasos de este tutorial.

> [AZURE.NOTE] Cuando use el proyecto completado, debe usar el **Administrador de paquetes NuGet** para restaurar los paquetes que requiere esta solución.

## Spout y bolt de Centros de eventos

El spout y bolt de Centros de eventos son componentes de Java que le permiten trabajar con facilidad con los Centros de eventos desde Apache Storm. A pesar de que estos componentes están escritos en Java, las herramientas de HDInsight para Visual Studio le permiten crear topologías híbridas que combinan componentes de Java y C#.

El spout y bolt se distribuyen como un archivo Java (.jar) único llamado **eventhubs-storm-spout-0.9-jar-with-dependencies.jar**.

### Descarga del archivo .jar

La versión más reciente del archivo **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** está incluida en el proyecto <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">HDInsight Storm examples</a> que se encuentra en la carpeta **lib**. Para descargar el archivo, use uno de los siguientes métodos.

> [AZURE.NOTE] El spout y bolt se enviaron para su inclusión en el proyecto de Apache Storm. Para obtener más información, consulte <a href="https://github.com/apache/storm/pull/336/files">STORM-583: Initial check-in for storm-event hubs</a> en GitHub.

* **Descargue un archivo ZIP**: desde el sitio <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">HDInsight Storm examples</a>, seleccione **Download ZIP** en el panel derecho para descargar un archivo .zip que contiene el proyecto.

	![botón download zip](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)

	Cuando se haya descargado, puede extraer el archivo, que se encontrará en el directorio **lib**.

* **Clone el proyecto**: si tiene instalado <a href="http://git-scm.com/" target="_blank">Git</a>, use el siguiente comando para clonar de manera local el repositorio y, después, encuentre el archivo en el directorio **lib**.

		git clone https://github.com/hdinsight/hdinsight-storm-examples

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
</table>Después de crear los permisos, seleccione el icono **Guardar** en la parte inferior de la página. Con esto se crean las directivas de acceso compartido que se usarán para enviar (escritor) y escuchar (lector) a este Centro de eventos.

	![directivas](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. Después de guardar las directivas, use el **generador de claves de acceso compartido** que se encuentra en la parte inferior de la página para recuperar la clave para las directivas de **escritor** y **lector**. Guárdelas, ya que se usarán después.

## Configuración del almacenamiento de tabla

El almacenamiento de tabla se usará para contener los valores que se leen desde los Centros de eventos, porque puede ver fácilmente el almacenamiento de tabla desde Visual Studio mediante el **Explorador de servidores**. Lleve a cabo los siguientes pasos para crear un almacenamiento de tabla nuevo:

1. En el [Portal de Azure clásico](https://manage.windowsazure.com), seleccione **NUEVO** > **Servicios de datos** > **Almacenamiento** > **Creación rápida**.

	![almacenamiento de creación rápida](./media/hdinsight-storm-develop-csharp-event-hub-topology/storagecreate.png)

2. Escriba un **Nombre** para la cuenta de almacenamiento, seleccione una **Ubicación** y, después, haga clic en la **marca de verificación** para crear la cuenta de almacenamiento.

	> [AZURE.NOTE] Debe seleccionar la misma **Ubicación** de los Centros de eventos y del servidor de Storm en HDInsight para disminuir la latencia y los costos.

3. Cuando se aprovisione la cuenta de almacenamiento nueva, seleccione la cuenta y, después, use el vínculo **Administrar claves de acceso** que se encuentra en la parte inferior de la página para recuperar el **Nombre de la cuenta de almacenamiento** y la **Clave de acceso primaria**. Guarde esta información, porque se usará más adelante.

	![Claves de acceso](./media/hdinsight-storm-develop-csharp-event-hub-topology/managekeys.png)

## Creación de EventHubWriter

En esta sección, se creará una topología que escribe datos en Centros de eventos usando el bolt de Centros de eventos.

1. Si todavía no tiene instalada la versión más reciente de las herramientas de HDInsight para Visual Studio, consulte <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Introducción al uso de las herramientas de HDInsight para Visual Studio</a>.

2. Abra Visual Studio, seleccione **Archivo** > **Nuevo** y, después, **Proyecto**.

3. En la pantalla **Nuevo proyecto**, expanda **Instalado** > **Plantillas** y seleccione **HDInsight**. En la lista de plantillas, seleccione **Aplicación de Storm**. En la parte inferior de la pantalla, escriba **EventHubWriter** como el nombre de la aplicación.

	![imagen](./media/hdinsight-storm-develop-csharp-event-hub-topology/newproject.png)

4. Tras la creación del proyecto, debe tener los siguientes archivos:

	* **Program.cs**: define la topología del proyecto. Tenga en cuenta que se crea una topología predeterminada que consta de un spout y un bolt de manera predeterminada.

	* **Spout.cs**: un spout de ejemplo.

	* **Bolt.cs**: un bolt de ejemplo. Esto se eliminará porque usará el bolt de Centros de eventos para escribir en un Centro de eventos.

### Configuración

1. En el **Explorador de soluciones**, haga clic con el botón secundario en **EventHubWriter** y, a continuación, seleccione **Propiedades**.

2. En las propiedades del proyecto, seleccione **Configuración** y, después, seleccione **Este proyecto no contiene un archivo de configuración predeterminado. Haga clic aquí para crear uno.**

3. Escriba la siguiente configuración. Use la información para el Centro de eventos que creó anteriormente en la columna **Valor**.

	<table>
<tr><th style="text-align:left">Nombre</th><th style="text-align:left">Tipo</th><th style="text-align:left">Scope</th></tr>
<tr><td style="text-align:left">EventHubPolicyName</td><td style="text-align:left">cadena</td><td style="text-align:left">Application</td></tr>
<tr><td style="text-align:left">EventHubPolicyKey</td><td style="text-align:left">cadena</td><td style="text-align:left">Application</td></tr>
<tr><td style="text-align:left">EventHubNamespace</td><td style="text-align:left">cadena</td><td style="text-align:left">Application</td></tr>
<tr><td style="text-align:left">EventHubName</td><td style="text-align:left">cadena</td><td style="text-align:left">Application</td></tr>
<tr><td style="text-align:left">EventHubPartitionCount</td><td style="text-align:left">int</td><td style="text-align:left">Application</td></tr>
</table>

4. Guarde y cierre la página **Propiedades**.

### Definición de la topología

1. En el **Explorador de soluciones**, haga clic con el botón secundario en **Bolt.cs** y seleccione **Eliminar**. No necesita este archivo porque usa el bolt de Centros de eventos de Java.

2. Abra el archivo **Program.cs** y agregue el código siguiente inmediatamente después de la línea `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubWriter");`.

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		List<string> javaDeserializerInfo =
            new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer", "java.lang.String" };

	La primera línea lee el número de particiones desde las propiedades anteriormente definidas. La segunda línea define un deserializador que se usa para deserializar los datos JSON que genera el spout en un elemento `java.lang.String`, para que los componentes de Java puedan consumir los datos.

4. Encuentre el código siguiente:

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Reemplácelo por el siguiente:

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"Event"}}
            },
            partitionCount).
            DeclareCustomizedJavaDeserializer(javaDeserializerInfo);

	De este modo, se crea un spout y se usa el recuento de particiones de Centros de eventos como indicación de paralelismo de este componente. Con esto se debe crear una instancia del spout para cada partición.

	También asocia el deserializador creado previamente con el flujo de salida de este componente. Esto permite que el componente EventHubSpout de bajada consuma los datos que produce el spout de C#.

5. Agregue lo siguiente inmediatamente después del código anterior:

		JavaComponentConstructor constructor =
            JavaComponentConstructor.CreateFromClojureExpr(
            String.Format(@"(com.microsoft.eventhubs.bolt.EventHubBolt. (com.microsoft.eventhubs.bolt.EventHubBoltConfig. " +
            @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
            Properties.Settings.Default.EventHubPolicyName,
            Properties.Settings.Default.EventHubPolicyKey,
            Properties.Settings.Default.EventHubNamespace,
            "servicebus.windows.net", //suffix for servicebus fqdn
            Properties.Settings.Default.EventHubName,
			"true"));

	Con esto se crea un constructor nuevo para el bolt de Java, que se usa en tiempo de ejecución para configurar una instancia nueva del bolt. En este caso, se usa <a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a> para configurar el spout con la información de configuración de Centros de eventos que agregó anteriormente. Más específicamente, HDInsight utiliza este código en tiempo de ejecución para hacer lo siguiente:

	* Crear una instancia nueva de **com.microsoft.eventhubs.bolt.EventHubBoltConfig** con la información de Centros de eventos que proporcione.
	* Crear una instancia nueva de **com.microsoft.eventhubs.bolt.EventHubBolt**, que se pasa en la instancia **EventHubBoltConfig**.

6. Encuentre el código siguiente:

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	Reemplácelo por el siguiente:

		topologyBuilder.SetJavaBolt(
            "EventHubBolt",
            constructor,
            partitionCount).
            shuffleGrouping("Spout");

	Este código indica a la topología que utilice el elemento **JavaComponentConstructor** del paso anterior como el bolt. En esta topología, es posible que el componente se conozca con el nombre descriptivo de "EventHubBolt". La indicación de paralelismo se define en la cantidad de particiones para el Centro de eventos y se suscribe a datos generados por el spout ("Spout").

En este momento, ha terminado con el archivo **Program.cs**. La topología está definida, pero ahora debe modificar **Spout.cs** para que genere datos en un formato que el bolt de Centros de eventos puede utilizar.

> [AZURE.NOTE] Esta topología creará de forma predeterminada un proceso de trabajo, que es suficiente para los fines del ejemplo. Si pretende adaptar esto a un clúster de producción, debe agregar lo siguiente para cambiar el número de trabajos:

    StormConfig config = new StormConfig();
    config.setNumWorkers(1);
    topologyBuilder.SetTopologyConfig(config);


### Modificación del spout

El bolt de Centros de eventos espera un valor de cadena único, el que se redirigirá al Centro de eventos. En el siguiente ejemplo, modificará el archivo **Spout.cs** predeterminado para que se genere una cadena JSON.

1. En el **Explorador de soluciones**, abra **Spout.cs** y agregue lo siguiente al principio del archivo:

		using Newtonsoft.Json;
		using Newtonsoft.Json.Linq;

	Esto le permitirá trabajar con los datos JSON con mayor facilidad.
    
    > [AZURE.NOTE] El paquete JSON.NET ya debería estar instalado, ya que lo necesita el marco de trabajo de SCP.NET usado para las topologías de Storm de C#.

3. Encuentre el código siguiente:

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));

	Reemplácelo por el siguiente:

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
        this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer());

	Esto cambia la definición de los datos que crea el spout para usar datos **string** y el elemento **CustomizedInteropJSONSerializer** declarado anteriormente en la topología (en program.cs).

2. Reemplace el método **NextTuple** por lo siguiente:

		public void NextTuple(Dictionary<string, Object> parms)
        {
            JObject eventData = new JObject();
            eventData.Add("deviceId", r.Next(10));
            eventData.Add("deviceValue", r.Next());
            ctx.Emit(new Values(eventData.ToString(Formatting.None)));
        }

	Esto genera de manera aleatoria un identificador de dispositivo, un valor y, después, usa Json.NET para emitir un objeto JSON con estos valores.

3. Guarde el archivo **Spout.cs**.

En este punto, dispone de una topología básica que generará datos aleatorios y los almacenará en Centros de eventos mediante el uso del bolt de Centros de eventos. A continuación, creará el lector.

## Creación de EventHubReader

En esta sección, creará una topología que lee datos desde los Centros de eventos mediante el spout de Centros de eventos.

2. Abra Visual Studio, seleccione **Archivo** > **Nuevo** > **Proyecto**.

3. En la pantalla **Nuevo proyecto**, expanda **Instalado** > **Plantillas** y seleccione **HDInsight**. En la lista de plantillas, seleccione **Aplicación de Storm**. En la parte inferior de la pantalla, escriba **EventHubReader** como el nombre de la aplicación.

### Configuración

1. En el **Explorador de soluciones**, haga clic con el botón secundario en **EventHubReader** y, después, seleccione **Propiedades**.

2. En las propiedades del proyecto, seleccione **Configuración** y, después, seleccione **Este proyecto no contiene un archivo de configuración predeterminado. Haga clic aquí para crear uno.**

3. Escriba la siguiente configuración. Use la información para el Centro de eventos y la cuenta de almacenamiento que creó anteriormente en la columna **Valor**.

	<table>
<tr><th style="text-align:left">Nombre</th><th style="text-align:left">Tipo</th><th style="text-align:left">Scope</th></tr>
<tr><th style="text-align:left">EventHubPolicyName</th><th style="text-align:left">cadena</th><th style="text-align:left">Application</th></tr>
<tr><th style="text-align:left">EventHubPolicyKey</th><th style="text-align:left">cadena</th><th style="text-align:left">Application</th></tr>
<tr><th style="text-align:left">EventHubNamespace</th><th style="text-align:left">cadena</th><th style="text-align:left">Application</th></tr>
<tr><th style="text-align:left">EventHubName</th><th style="text-align:left">cadena</th><th style="text-align:left">Application</th></tr>
<tr><th style="text-align:left">EventHubPartitionCount</th><th style="text-align:left">int</th><th style="text-align:left">Application</th></tr>
<tr><th style="text-align:left">StorageConnection</th><th style="text-align:left">(cadena de conexión)</th><th style="text-align:left">Application</th></tr>
<tr><th style="text-align:left">TableName</th><th style="text-align:left">cadena</th><th style="text-align:left">Application</th></tr>
</table>En **TableName**, escriba el nombre de la tabla en la que quiere que se almacenen los eventos.

    En **StorageConnection**, escriba un valor de `DefaultEndpointsProtocol=https;AccountName=myAccount;AccountKey=myKey;`. Reemplace **myAccount** y **myKey** con el nombre y la clave de la cuenta de almacenamiento obtenidos anteriormente.

	La topología usará estos valores para comunicarse con Centros de eventos y el almacenamiento de tablas.

4. Guarde y cierre la página **Propiedades**.

### Definición de la topología

1. En el **Explorador de soluciones**, haga clic con el botón secundario en **Spout.cs** y seleccione **Eliminar**. No necesita este archivo porque usa el spout de Centros de eventos de Java.

2. Abra el archivo **Program.cs** y agregue el código siguiente inmediatamente después de la línea `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubReader");`:

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		EventHubSpoutConfig ehConfig = new EventHubSpoutConfig(
                Properties.Settings.Default.EventHubPolicyName,
                Properties.Settings.Default.EventHubPolicyKey,
                Properties.Settings.Default.EventHubNamespace,
                Properties.Settings.Default.EventHubName,
                partitionCount);

	Se lee el recuento de particiones y se asigna a una variable local. Se usará varias veces.

	`EventHubSpoutConfig` define la configuración del spout del Centro de eventos. En este caso, la información de configuración de los Centros de eventos que agregó anteriormente. Esto usa el spout del Centro de eventos de Java en segundo plano y creará una instancia nueva de **com.microsoft.eventhubs.spout.EventHubSpoutConfig** con la información de los Centros de eventos.

5. Encuentre el código siguiente:

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Reemplácelo por el siguiente:

        topologyBuilder.SetEventHubSpout(
            "EventHubSpout", 
            ehConfig, 
            partitionCount); 

	Esto indica la topología para crear un nuevo spout del Centro de eventos y usar `EventHubSpoutConfig` del paso anterior como configuración. "EventHubSpout" establece el nombre descriptivo del spout, y `partitionCount` se usa para establecer la sugerencia de paralelismo. En segundo plano, esto crea una nueva instancia del componente de java **com.microsoft.eventhubs.spout.EventHubSpout** con la información de configuración proporcionada.

2. Agregue lo siguiente inmediatamente después del código anterior:

         List<string> javaSerializerInfo = new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer" };

	Esto crea un serializador personalizado, que se usará para serializar la información que producen los componentes de Java (por ejemplo, el elemento EventHubSpout) en un formato JSON que los componentes de C# de bajada pueden usar.

3. Encuentre el código siguiente:

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	Reemplácelo por el siguiente:

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            partitionCount,
            true).
            DeclareCustomizedJavaSerializer(javaSerializerInfo).
            shuffleGrouping("EventHubSpout");

	Este código indica a la topología que utilice un bolt (definido en Bolt.cs). El serializador personalizado definido anteriormente se usa aquí para que este bolt pueda consumir datos producidos por componentes de Java de subida. En este caso, el elemento EventHubSpout.

    > [AZURE.IMPORTANT] El último parámetro de SetBolt, (con un valor de `true`,) permite la funcionalidad de confirmación para este bolt. Esto es necesario, ya que el componente EventHubSpout espera una confirmación para los datos que emite. Si no se devuelven mensajes de confirmación desde los componentes de bajada, el spout dejará de recibir después de procesar aproximadamente 1000 mensajes.

En este punto, ha terminado con el archivo **Program.cs**. Se definió la topología, pero ahora debe crear una clase auxiliar para escribir datos en el almacenamiento de tabla y, después, debe modificar **Bolt.cs** para que pueda comprender los datos que genera el spout.

> [AZURE.NOTE] Esta topología creará de forma predeterminada un proceso de trabajo, que es suficiente para los fines del ejemplo. Si pretende adaptar esto a un clúster de producción, debe agregar lo siguiente para cambiar el número de trabajos:

    StormConfig config = new StormConfig();
    config.setNumWorkers(1);
    topologyBuilder.SetTopologyConfig(config);


### Creación de una clase auxiliar

Al escribir datos en el almacenamiento de tabla, debe crear una clase que describe los datos que se escribirán.

1. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto **EventHubReader**, seleccione **Agregar** y, después, **Nueva clase**. Llame a la nueva clase **Devices.cs**.

2. Abra **Devices.cs** y reemplace el código predeterminado por lo siguiente:

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using Microsoft.WindowsAzure.Storage.Table;

		namespace EventHubReader
		{
		    class Device : TableEntity
		    {
		        public int value { get; set; }

		        public Device() { }
		        public Device(int id)
		        {
		            this.PartitionKey = id.ToString();
		            this.RowKey = System.Guid.NewGuid().ToString();
		        }
		    }
		}

	Esto crea entidades en el almacenamiento de tabla que se componen de una clave de partición (que se definirá en el identificador del dispositivo, que se lee desde Centro de eventos), una clave de fila única y un valor que se lee desde Centro de eventos. Cada entidad tendrá también una marca de tiempo, que se creará automáticamente cuando la entidad se inserte en la tabla.

### Modificación del bolt

1. En el **Explorador de soluciones**, expanda el proyecto **EventHubReader** y abra el archivo **Bolt.cs**. Agregue lo siguiente en la parte superior del archivo:

		using Newtonsoft.Json.Linq;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Table;

	Esto permite trabajar más fácilmente con datos JSON desde el bolt y escribir datos en almacenamiento de tabla.

2. Encuentre la instrucción `private int count;` y reemplácela por lo siguiente:

        private CloudTable table;

	Esto se usará al conectarse a la tabla.

4. Encuentre el código siguiente:

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));

	Reemplácelo por el siguiente:

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
        this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());

	Esto indica al bolt que recibirá un valor **string** en lugar de un **int** y que los datos se deben deserializar mediante el elemento **CustomizedInteropJSONDeserialzer** que se declaró en la topología anteriormente (en el archivo program.cs).

3. Agregue lo siguiente inmediatamente después del código anterior:

		CloudStorageAccount storageAccount = CloudStorageAccount.Parse(Properties.Settings.Default.StorageConnection);
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
        table = tableClient.GetTableReference(Properties.Settings.Default.TableName);
        table.CreateIfNotExists();

	Esto se conecta a la tabla **eventos** con la cadena de conexión anteriormente configurada. Si no existe, se creará.

2. Encuentre el método **Execute** y reemplácelo por lo siguiente:

		public void Execute(SCPTuple tuple)
        {
            Context.Logger.Info("Processing events");
            string eventValue = (string)tuple.GetValue(0);
            if (eventValue != null)
            {
                JObject eventData = JObject.Parse(eventValue);

                Device device = new Device((int)eventData["deviceId"]);
                device.value = (int)eventData["deviceValue"];

                TableOperation insertOperation = TableOperation.Insert(device);

                table.Execute(insertOperation);
                this.ctx.Ack(tuple);
            }
        }

	Esto utiliza Json.NET para analizar los datos JSON desde el spout y, después, recoge los campos **deviceId** y **deviceValue**. Se crea un nuevo objeto **Device**, usando el valor **deviceId** durante la inicialización para definir la clave de partición de la tabla. De este modo, se define el valor en **deviceValue** y, finalmente, la entidad se inserta en la tabla.

    Después de que la entidad se inserte en la tabla, se llama a `Ack()` para la tupla, para informar al spout de que se han procesado correctamente los datos.

    > [AZURE.IMPORTANT] El componente EventHubSpout espera una confirmación por cada tupla de los componentes de bajada, como este bolt. Si no se reciben mensajes de confirmación, el elemento EventHubSpout supondrá que se ha producido un error en el procesamiento de la tupla.

En este punto, dispone de una topología completa que leerá datos desde Centro de eventos y los almacenará en almacenamiento de tabla en una tabla llamada **events**.

## Implementación de las topologías

1. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto **EventHubReader** y seleccione **Enviar a Storm en HDInsight**.

	![enviar a storm](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. En la pantalla **Enviar topología**, seleccione su **clúster de Storm**. Expanda **Configuraciones adicionales**, seleccione **Rutas de acceso del archivo Java**, elija **...** y, después, seleccione el directorio que contiene el archivo **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** que descargó anteriormente. Finalmente, haga clic en **Enviar**.

	![Imagen del cuadro de diálogo de envío](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. Cuando se haya enviado la topología, aparecerá el **Visor de topologías de Storm**. Seleccione la topología **EventHubReader** en el panel de la izquierda para ver estadísticas de la topología. Actualmente, no debería ocurrir nada, puesto que todavía no se han escrito eventos en Centros de eventos.

	![vista de almacenamiento de ejemplo](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. En el **Explorador de solucione**s, haga clic con el botón secundario en el proyecto **EventHubWriter** y seleccione **Enviar a Storm en HDInsight**.

2. En la pantalla **Enviar topología**, seleccione su **clúster de Storm**. Expanda **Configuraciones adicionales**, seleccione **Rutas de acceso del archivo Java**, **...** y, después, seleccione el directorio que contiene el archivo **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** que descargó anteriormente. Finalmente, haga clic en **Enviar**.

5. Cuando se haya enviado la topología, actualice la lista de topologías en el **Visor de topologías de Storm** para comprobar que las dos se estén ejecutando en el clúster.

6. Cuando ambas topologías estén en ejecución, seleccione el **Explorador de servidores**, expanda **Azure** > **Almacenamiento** y seleccione la cuenta de almacenamiento que creó anteriormente. En la cuenta de almacenamiento, expanda **Tablas**. Finalmente, haga doble clic en la tabla **events** para abrirla. Debería ver los datos que se han almacenado en la tabla desde la topología **EventHubReader**.

	* La topología **EventHubWriter** genera los eventos y los escribe en el Centro de eventos.

	* A continuación, **EventHubReader** lee los eventos desde los Centros de eventos y los almacena en el almacenamiento de tabla, en la tabla **events**.

## Detención de las topologías

Para detener las topologías, seleccione cada topología en el **Visor de topologías de Storm** y haga clic en **Eliminar**.

![imagen de eliminación de una topología](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

## Notas

### Puntos de control

El elemento EventHubSpout envía periódicamente puntos de control de su estado al nodo Zookeeper, que almacena el desplazamiento actual de los mensajes leídos de la cola. Esto permite que el componente empiece a recibir mensajes en el desplazamiento almacenado en los escenarios siguientes:

* Se produce un error en la instancia del componente y se reinicia.

* Aumenta o reduce el clúster, mediante la incorporación o eliminación de nodos.

* Se elimina y reinicia la topología **con el mismo nombre**.

También puede exportar e importar los puntos de control persistentes a WASB (el almacenamiento de Azure que usa su clúster de HDInsight). Los scripts para hacerlo se encuentran en el clúster de HDInsight en Storm, en **c:\\apps\\dist\\storm-0.9.3.2.2.1.0-2340\\zkdatatool-1.0\\bin**.

>[AZURE.NOTE] El número de versión en la ruta de acceso puede ser diferente, ya que la versión de Storm instalada en el clúster puede cambiar en el futuro.

Los scripts en este directorio son:

* **stormmeta\_import.cmd**: importar todos los metadatos de Storm desde el contenedor de almacenamiento del clúster predeterminado a Zookeeper.

* **stormmeta\_export.cmd**: exportar todos los metadatos de Storm de Zookeeper en el contenedor de almacenamiento del clúster predeterminado.

* **stormmeta\_delete.cmd**: eliminar todos los metadatos de Storm de Zookeeper.

La exportación de una importación permite conservar los datos de puntos de control cuando deba eliminar el clúster, pero quiera reanudar el procesamiento desde el desplazamiento actual en el centro cuando vuelva a poner en línea un nuevo clúster.

> [AZURE.NOTE] Dado que los datos se conservan en el contenedor de almacenamiento predeterminado, el nuevo clúster **debe** utilizar la misma cuenta de almacenamiento y el mismo contenedor que el clúster anterior.

## Resumen

En este documento, ha aprendido a usar el spout y bolt de los Centros de eventos de Java desde una topología de C# para trabajar con datos en el Centro de eventos de Azure. Para obtener más información acerca de cómo crear topologías de C#, consulte lo siguiente

* [Desarrollo de topologías de C# para Apache Storm en HDInsight con Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

* [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md)
 

<!---HONumber=AcomDC_0204_2016-->