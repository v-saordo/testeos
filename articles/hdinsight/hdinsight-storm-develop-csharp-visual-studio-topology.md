<properties
   pageTitle="Topologías de Apache Storm con Visual Studio y C# | Microsoft Azure"
   description="Aprenda a crear topologías de Storm en C# mediante la creación de una topología de recuento de palabras simple en Visual Studio mediante las herramientas de HDInsight para Visual Studio."
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
   ms.date="02/05/2016"
   ms.author="larryfr"/>

# Desarrollo de topologías de C# para Apache Storm en HDInsight con herramientas de Hadoop para Visual Studio

Aprenda a crear una topología de Storm de C# mediante las herramientas de HDInsight para Visual Studio. Este tutorial le guía a través del proceso de creación de un nuevo proyecto de Storm en Visual Studio, probarlo localmente e implementarlo en un clúster de Apache Storm en HDInsight.

También aprenderá a crear topologías híbridas que usan componentes de C# y Java.

[AZURE.INCLUDE [windows-only](../../includes/hdinsight-windows-only.md)]

##Requisitos previos

-	Una de las siguientes versiones de Visual Studio

	-	Visual Studio 2012 con [Update 4](http://www.microsoft.com/download/details.aspx?id=39305)

	-	Visual Studio 2013 con [Update 4](http://www.microsoft.com/download/details.aspx?id=44921) o [Visual Studio Community 2013](http://go.microsoft.com/fwlink/?LinkId=517284)

	-	Visual Studio 2015 o [Visual Studio Community 2015](https://go.microsoft.com/fwlink/?LinkId=532606)

-	SDK de Azure 2.5.1 o posterior

-	Herramientas de HDInsight para Visual Studio: consulte [Introducción al uso de las herramientas de HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) para instalar y configurar las herramientas de HDInsight para Visual Studio.

    > [AZURE.NOTE] No se admite el uso de las herramientas de HDInsight para Visual Studio en Visual Studio Express

-	Clúster Apache Storm en HDInsight: vea [Introducción a Apache Storm en HDInsight](hdinsight-apache-storm-tutorial-get-started.md) para conocer los pasos para crear un clúster.

	> [AZURE.NOTE] Actualmente, las herramientas de HDInsight para Visual Studio solo admiten Storm en los clústeres de versiones 3.2 de HDInsight.

##Plantillas

Las herramientas de HDInsight para Visual Studio proporcionan las siguientes plantillas:

| Tipo de proyecto | Muestra |
| ------------ | ------------- |
| Storm Application | Un proyecto vacío de topología de Storm |
| Storm Azure SQL Writer Sample | Cómo escribir en Base de datos SQL de Azure |
| Storm DocumentDB Reader Sample | Cómo leer de DocumentDB de Azure |
| Storm DocumentDB Writer Sample | Cómo escribir en DocumentDB de Azure |
| Storm EventHub Reader Sample | Cómo leer en los centros de eventos de Azure |
| Storm EventHub Writer Sample | Cómo escribir en los centros de eventos de Azure |
| Storm HBase Reader Sample | Cómo leer de HBase en clústeres de HDInsight |
| Storm HBase Writer Sample | Cómo escribir en HBase en clústeres de HDInsight |
| Storm Hybrid Sample | Cómo utilizar un componente de Java |
| Storm Sample | Una topología de recuento de palabras básica |

> [AZURE.NOTE] Los ejemplos de lector y escritor de HBase usan la API de REST de HBase para comunicarse con un HBase en un clúster de HDInsight, no la API de Java de HBase.

En los pasos de este documento, usará el tipo de proyecto Storm Application básico para crear una nueva topología.

##Creación de una topología de C#

1.	Si todavía no tiene instalada la versión más reciente de las herramientas de HDInsight para Visual Studio, vea [Introducción al uso de las herramientas de HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

2.	Abra Visual Studio, seleccione **Archivo** > **Nuevo** y, después, **Proyecto**.

3.	En la pantalla **Nuevo proyecto**, expanda **Instalado** > **Plantillas** y seleccione **HDInsight**. En la lista de plantillas, seleccione **Aplicación de Storm**. En la parte inferior de la pantalla, escriba **WordCount** como nombre de la aplicación.

	![imagen](./media/hdinsight-storm-develop-csharp-visual-studio-topology/new-project.png)

4.	Cuando se haya creado el proyecto, debe tener los siguientes archivos:

	-	**Program.cs**: define la topología del proyecto. Tenga en cuenta que se crea una topología predeterminada que consta de un spout y un bolt de manera predeterminada.

	-	**Spout.cs**: un spout de ejemplo que emite números aleatorios

	-	**Bolt.cs**: un bolt de ejemplo que mantiene un recuento de los números emitidos por el spout.

	Como parte de la creación del proyecto, los [paquetes SCP.NET](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/) más recientes se descargarán de NuGet.

En las secciones siguientes, modificará este proyecto en una aplicación básica de WordCount.

###Implementación del spout

1.	Abra **Spout.cs**. Los spouts se usan para leer los datos en una topología de un origen externo. Los componentes principales de un spout son:

	-	**NextTuple**: llamado por Storm cuando se permite que el spout emita nuevas tuplas

	-	**Ack** (solo topología transaccional): controla las confirmaciones iniciadas por otros componentes de la topología para tuplas enviadas desde este spout. La confirmación de una tupla permite que el spout conozca que se ha procesado correctamente por componentes de bajada.

	-	**Fail** (solo topología transaccional): controla las tuplas que producen un error al procesar otros componentes de la topología. Esto ofrece la oportunidad de volver a emitir la tupla para que se pueda procesar de nuevo.

2.	Reemplace el contenido de la clase **Spout** por lo siguiente. Esto crea un spout que emite aleatoriamente una frase en la topología.

	```
	private Context ctx;
	private Random r = new Random();
	string[] sentences = new string[] {
	    "the cow jumped over the moon",
	    "an apple a day keeps the doctor away",
	    "four score and seven years ago",
	    "snow white and the seven dwarfs",
	    "i am at two with nature"
	};


	public Spout(Context ctx)
	{
	    // Set the instance context
	    this.ctx = ctx;


	    Context.Logger.Info("Generator constructor called");


	    // Declare Output schema
	    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
	    // The schema for the default output stream is
	    // a tuple that contains a string field
	    outputSchema.Add("default", new List<Type>() { typeof(string) });
	    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
	}


	// Get an instance of the spout
	public static Spout Get(Context ctx, Dictionary<string, Object> parms)
	{
	    return new Spout(ctx);
	}


	public void NextTuple(Dictionary<string, Object> parms)
	{
	    Context.Logger.Info("NextTuple enter");
	    // The sentence to be emitted
	    string sentence;


	    // Get a random sentence
	    sentence = sentences[r.Next(0, sentences.Length - 1)];
	    Context.Logger.Info("Emit: {0}", sentence);
	    // Emit it
	    this.ctx.Emit(new Values(sentence));


	    Context.Logger.Info("NextTuple exit");
	}


	public void Ack(long seqId, Dictionary<string, Object> parms)
	{
	    // Only used for transactional topologies
	}


	public void Fail(long seqId, Dictionary<string, Object> parms)
	{
	    // Only used for transactional topologies
	}
	```

	Dedique un momento a leer los comentarios para entender lo que hace este código.

###Implementación de los bolts

1.	Elimine el archivo **Bolt.cs** existente del proyecto.

2.	En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto y seleccione **Agregar** > **Nuevo elemento**. En la lista, seleccione **Bolt de Storm** y escriba **Splitter.cs** como nombre. Repita esto para crear un segundo bolt denominado **Counter.cs**.

	-	**Splitter.cs**: implementa un bolt que divide la frase en palabras individuales y emite una nueva secuencia de palabras.

	-	**Counter.cs**: implementa un bolt que cuenta cada palabra y emite una nueva secuencia de palabras y el recuento de cada palabra.

	> [AZURE.NOTE] Estos bolts simplemente leen y escriben en las secuencias, pero también puede usar un bolt para comunicarse con orígenes como una base de datos o un servicio.

3.	Abra **Splitter.cs**. Tenga en cuenta que tiene solo un método de forma predeterminada: **Execute**. Se llama cuando el bolt recibe una tupla para el procesamiento. En este caso, puede leer y procesar las tuplas entrantes y emitir tuplas salientes.

4.	Reemplace el contenido de la clase **Splitter** por el código siguiente:

	```
	private Context ctx;


	// Constructor
	public Splitter(Context ctx)
	{
	    Context.Logger.Info("Splitter constructor called");
	    this.ctx = ctx;


	    // Declare Input and Output schemas
	    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
	    // Input contains a tuple with a string field (the sentence)
	    inputSchema.Add("default", new List<Type>() { typeof(string) });
	    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
	    // Outbound contains a tuple with a string field (the word)
	    outputSchema.Add("default", new List<Type>() { typeof(string) });
	    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
	}


	// Get a new instance of the bolt
	public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
	{
	    return new Splitter(ctx);
	}


	// Called when a new tuple is available
	public void Execute(SCPTuple tuple)
	{
	    Context.Logger.Info("Execute enter");


	    // Get the sentence from the tuple
	    string sentence = tuple.GetString(0);
	    // Split at space characters
	    foreach (string word in sentence.Split(' '))
	    {
	        Context.Logger.Info("Emit: {0}", word);
	        //Emit each word
	        this.ctx.Emit(new Values(word));
	    }


	    Context.Logger.Info("Execute exit");
	}
	```

	Dedique un momento a leer los comentarios para entender lo que hace este código.

5.	Abra **Counter.cs** y reemplace el contenido de la clase por lo siguiente:

	```
	private Context ctx;


	// Dictionary for holding words and counts
	private Dictionary<string, int> counts = new Dictionary<string, int>();


	// Constructor
	public Counter(Context ctx)
	{
	    Context.Logger.Info("Counter constructor called");
	    // Set instance context
	    this.ctx = ctx;


	    // Declare Input and Output schemas
	    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
	    // A tuple containing a string field - the word
	    inputSchema.Add("default", new List<Type>() { typeof(string) });


	    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
	    // A tuple containing a string and integer field - the word and the word count
	    outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
	    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
	}


	// Get a new instance
	public static Counter Get(Context ctx, Dictionary<string, Object> parms)
	{
	    return new Counter(ctx);
	}


	// Called when a new tuple is available
	public void Execute(SCPTuple tuple)
	{
	    Context.Logger.Info("Execute enter");


	    // Get the word from the tuple
	    string word = tuple.GetString(0);
	    // Do we already have an entry for the word in the dictionary?
	    // If no, create one with a count of 0
	    int count = counts.ContainsKey(word) ? counts[word] : 0;
	    // Increment the count
	    count++;
	    // Update the count in the dictionary
	    counts[word] = count;


	    Context.Logger.Info("Emit: {0}, count: {1}", word, count);
	    // Emit the word and count information
	    this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));


	    Context.Logger.Info("Execute exit");
	}
	```

	Dedique un momento a leer los comentarios para entender lo que hace este código.

###Definición de la topología

Los spouts y bolts se organizan en un gráfico, que define cómo fluyen los datos entre componentes. Para esta topología, el gráfico es como sigue:

![imagen de cómo se organizan los componentes](./media/hdinsight-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

Las frases se emiten desde el spout, que se distribuyen a las instancias del bolt divisor. El bolt divisor divide las frases en palabras, que se distribuyen en el bolt contador.

Dado que el recuento de palabras se guarda localmente en la instancia del contador, queremos asegurarnos de que determinadas palabras fluyan a la misma instancia del bolt contador, para tener una única instancia que realiza el seguimiento de una palabra específica. Pero para el bolt divisor no importa qué bolt recibe la frase, por lo que simplemente queremos cargar frases de equilibrio en todas las instancias.

Abra **Program.cs**. El método importante aquí es **ITopologyBuilder**, que se usa para definir la topología que se envía a Storm. Reemplace el contenido de **ITopologyBuilder** por el código siguiente para implementar la topología descrita anteriormente:

```
    // Create a new topology named 'WordCount'
    TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount");

    // Add the spout to the topology.
    // Name the component 'sentences'
    // Name the field that is emitted as 'sentence'
    topologyBuilder.SetSpout(
        "sentences",
        Spout.Get,
        new Dictionary<string, List<string>>()
        {
            {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
        },
        1);
    // Add the splitter bolt to the topology.
    // Name the component 'splitter'
    // Name the field that is emitted 'word'
    // Use suffleGrouping to distribute incoming tuples
    //   from the 'sentences' spout across instances
    //   of the splitter
    topologyBuilder.SetBolt(
        "splitter",
        Splitter.Get,
        new Dictionary<string, List<string>>()
        {
            {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
        },
        1).shuffleGrouping("sentences");

    // Add the counter bolt to the topology.
    // Name the component 'counter'
    // Name the fields that are emitted 'word' and 'count'
    // Use fieldsGrouping to ensure that tuples are routed
    //   to counter instances based on the contents of field
    //   position 0 (the word). This could also have been
    //   List<string>(){"word"}.
    //   This ensures that the word 'jumped', for example, will always
    //   go to the same instance
    topologyBuilder.SetBolt(
        "counter",
        Counter.Get,
        new Dictionary<string, List<string>>()
        {
            {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
        },
        1).fieldsGrouping("splitter", new List<int>() { 0 });

    // Add topology config
    topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
    {
        {"topology.kryo.register","["[B"]"}
    });

    return topologyBuilder;
```

Dedique un momento a leer los comentarios para entender lo que hace este código.

##Envío de la topología

1.	En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto y seleccione **Enviar a Storm en HDInsight**.

	> [AZURE.NOTE] Si se le solicita, escriba las credenciales de inicio de sesión de su suscripción de Azure. Si tiene más de una suscripción, inicie sesión en la que contenga el clúster de Storm en HDInsight.

2.	Seleccione el clúster de Storm en HDInsight desde el menú desplegable **Clúster de Storm** y, después, seleccione **Enviar**. Puede supervisar si el envío es correcto mediante la ventana **Salida**.

3.	Cuando la topología se envíe correctamente, debe aparecer **topologías de Storm** del clúster. Seleccione la topología **WordCount** en la lista para consultar la información acerca de la topología en ejecución.

	> [AZURE.NOTE] También puede ver las **topologías de Storm** en el **Explorador de servidores**: expanda **Azure** > **HDInsight**, haga clic con el botón secundario en un clúster de Storm en HDInsight y seleccione **Ver topologías de Storm**.

	Use los vínculos de los spouts o bolts para ver información sobre estos componentes. Se abrirá una ventana nueva para cada elemento seleccionado.

4.	Desde la vista **Resumen de la topología**, haga clic en **Eliminar** para detener la topología.

	> [AZURE.NOTE] Las topologías de Storm continúan ejecutándose hasta que se desactiven o se elimine el clúster.

##Topología transaccional

La topología anterior no es transaccional. Los componentes de dentro de la topología no implementan ninguna funcionalidad para reproducir los mensajes si se produce un error de procesamiento de un componente de la topología. Para ver un ejemplo de topología transaccional, cree un nuevo proyecto y seleccione **Muestra de Storm** como tipo de proyecto.

Las topologías transaccionales implementan lo siguiente para que admitan la reproducción de datos:

-	**Almacenamiento en caché de metadatos**: el spout debe almacenar metadatos sobre los datos emitidos para que los datos puedan recuperarse y volver a emitirse si se produce un error. Dado que los datos que emite la muestra son pequeños, los datos sin procesar de cada tupla se almacenan en un diccionario para la reproducción.

-	**Ack**: cada bolt de la topología puede llamar a `this.ctx.Ack(tuple)` para confirmar si el proceso de una tupla se ha realizado correctamente. Una vez que todos los bolts han confirmado la tupla, se llama al método `Ack` del spout. Esto permite que el spout quite los datos almacenados en caché para la reproducción, ya que los datos se han procesado completamente.

-	**Fail**: cada bolt puede llamar a `this.ctx.Fail(tuple)` para indicar que se produjo un error al procesar una tupla. El error se propaga al método `Fail` del spout, donde la tupla puede reproducirse mediante metadatos almacenados en caché.

-	**Id. de secuencia**: al emitir una tupla, se puede especificar un identificador de secuencia. Debe ser un valor que identifique la tupla para el procesamiento de reproducción (Ack y Fail). Por ejemplo, el spout del proyecto **Muestra de Storm** utiliza los siguiente al emitir datos:

	```
	this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);
	```

	Esto emite una nueva tupla que contiene una frase en la secuencia de forma predeterminada, con el valor de identificador de secuencia contenido en **lastSeqId**. En este ejemplo, **lastSeqId** simplemente se incrementa con cada tupla emitida.

Como se muestra en el proyecto **Muestra de Storm**, se puede establecer si un componente es transaccional en tiempo de ejecución, según la configuración.

##Topologías híbridas

Las herramientas de HDInsight para Visual Studio también pueden utilizarse para crear topologías híbridas, donde algunos componentes son de C# y otros son de Java.

Para ver una topología híbrida de ejemplo, cree un nuevo proyecto y seleccione **Muestra híbrida de Storm**. Esto crea una muestra totalmente comentada que contiene varias topologías que muestran lo siguiente:

-	**Spout de Java** y **bolt de C#**: definidos en **HybridTopology\_javaSpout\_csharpBolt**

	-	Una versión transaccional se define en **HybridTopologyTx\_javaSpout\_csharpBolt**

-	**Spout de C#** y **bolt de Java**: definidos en **HybridTopology\_csharpSpout\_javaBolt**

	-	Una versión transaccional se define en **HybridTopologyTx\_csharpSpout\_javaBolt**

		> [AZURE.NOTE] Esta versión también muestra cómo usar código de Clojure desde un archivo de texto como un componente de Java.

Para cambiar entre la topología que se usa cuando se envía el proyecto, simplemente mueva la instrucción `[Active(true)]` a la topología que quiere usar antes de enviarla al clúster.

> [AZURE.NOTE] Todos los archivos de Java necesarios se ofrecen como parte de este proyecto en la carpeta **JavaDependency**.

Tenga en cuenta lo siguiente al crear y enviar una topología híbrida:

-	**JavaComponentConstructor** debe usarse para crear una nueva instancia de la clase de Java de un spout o bolt

-	**microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** debe usarse para serializar datos hacia y desde los componentes de Java, a partir de objetos de Java a JSON

-	Al enviar la topología al servidor, debe utilizar la opción **Configuraciones adicionales** para especificar las **rutas de acceso del archivo Java**. La ruta especificada debe ser el directorio que contiene los archivos JAR que contienen las clases de Java.

###Centros de eventos de Azure

La versión 0.9.4.203 de SCP.Net presenta una nueva clase y método específicos para trabajar con el spout del Centro de eventos (un spout de Java que lee desde el Centro de eventos.) Al crear una topología que utiliza este spout, utilice los métodos siguientes:

-	Clase **EventHubSpoutConfig**: crea un objeto que contiene la configuración para el componente spout

-	Método **TopologyBuilder.SetEventHubSpout**: agrega el componente spout del Centro de eventos a la topología

> [AZURE.NOTE] Aunque estos simplifican el trabajo con el spout del Centro de eventos en comparación con otros componentes de Java, deberá utilizar CustomizedInteropJSONSerializer para serializar los datos que produce el spout.

##Cómo actualizar SCP.NET

Las versiones recientes de SCP.NET admiten la actualización de paquetes a través de NuGet. Cuando está disponible una nueva actualización, recibirá una notificación de actualización. Para comprobar manualmente una actualización, siga estos pasos:

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Administrar paquetes NuGet**.

2. En el administrador de paquetes, seleccione **Actualizaciones**. Si hay disponible una actualización, se mostrará una lista. Haga clic en el botón **Actualizar** para que el paquete la instale.

> [AZURE.IMPORTANT] Si el proyecto se creó con una de las versiones anteriores de SCP.NET que no se usó NuGet para las actualizaciones de paquetes, debe realizar los pasos siguientes para actualizar a la nueva versión:
>
> 1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Administrar paquetes NuGet**.
> 2. Mediante el campo **Búsqueda** busque **Microsoft.SCP.Net.SDK** y agréguelo al proyecto.

##Solución de problemas

###Prueba de una topología localmente

Aunque es fácil implementar una topología en un clúster, en algunos casos puede que deba probar localmente una topología. Siga los pasos que se muestran a continuación para ejecutar y probar localmente la topología de ejemplo de este tutorial en su entorno de desarrollo.

> [AZURE.WARNING] Las pruebas locales solo funcionan para topologías básicas de C#. No debe utilizar las pruebas locales para las topologías híbridas o las topologías que utilizan varias secuencias porque recibirá errores.

1.	En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto y seleccione **Propiedades**. En las propiedades del proyecto, cambie el **tipo de salida** a **Aplicación de consola**.

	![tipo de salida](./media/hdinsight-storm-develop-csharp-visual-studio-topology/outputtype.png)

	> [AZURE.NOTE] No olvide cambiar el **tipo de salida** de nuevo a **Biblioteca de clases** antes de implementar la topología en un clúster.

2.	En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto y seleccione **Agregar** > **Nuevo elemento**. Seleccione **Clase** y escriba **LocalTest.cs** como nombre de clase. Por último, haga clic en **Agregar**.

3.	Abra **LocalTest.cs** y agregue la siguiente instrucción **using** en la parte superior:

	```
	using Microsoft.SCP;
	```

4.	Use lo siguiente como contenido de la clase **LocalTest**:

	```
	// Drives the topology components
	public void RunTestCase()
	{
	    // An empty dictionary for use when creating components
	    Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();


	    #region Test the spout
	    {
	        Console.WriteLine("Starting spout");
	        // LocalContext is a local-mode context that can be used to initialize
	        // components in the development environment.
	        LocalContext spoutCtx = LocalContext.Get();
	        // Get a new instance of the spout, using the local context
	        Spout sentences = Spout.Get(spoutCtx, emptyDictionary);


	        // Emit 10 tuples
	        for (int i = 0; i < 10; i++)
	        {
	            sentences.NextTuple(emptyDictionary);
	        }
	        // Use LocalContext to persist the data stream to file
	        spoutCtx.WriteMsgQueueToFile("sentences.txt");
	        Console.WriteLine("Spout finished");
	    }
	    #endregion


	    #region Test the splitter bolt
	    {
	        Console.WriteLine("Starting splitter bolt");
	        // LocalContext is a local-mode context that can be used to initialize
	        // components in the development environment.
	        LocalContext splitterCtx = LocalContext.Get();
	        // Get a new instance of the bolt
	        Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);


	        // Set the data stream to the data created by the spout
	        splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
	        // Get a batch of tuples from the stream
	        List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
	        // Process each tuple in the batch
	        foreach (SCPTuple tuple in batch)
	        {
	            splitter.Execute(tuple);
	        }
	        // Use LocalContext to persist the data stream to file
	        splitterCtx.WriteMsgQueueToFile("splitter.txt");
	        Console.WriteLine("Splitter bolt finished");
	    }
	    #endregion


	    #region Test the counter bolt
	    {
	        Console.WriteLine("Starting counter bolt");
	        // LocalContext is a local-mode context that can be used to initialize
	        // components in the development environment.
	        LocalContext counterCtx = LocalContext.Get();
	        // Get a new instance of the bolt
	        Counter counter = Counter.Get(counterCtx, emptyDictionary);


	        // Set the data stream to the data created by splitter bolt
	        counterCtx.ReadFromFileToMsgQueue("splitter.txt");
	        // Get a batch of tuples from the stream
	        List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
	        // Process each tuple in the batch
	        foreach (SCPTuple tuple in batch)
	        {
	            counter.Execute(tuple);
	        }
	        // Use LocalContext to persist the data stream to file
	        counterCtx.WriteMsgQueueToFile("counter.txt");
	        Console.WriteLine("Counter bolt finished");
	    }
	    #endregion
	}
	```

	Dedique un momento para leer los comentarios del código. Este código usa **LocalContext** para ejecutar los componentes en el entorno de desarrollo y conserva la secuencia de datos entre componentes de los archivos de texto en la unidad local.

5.	Abra **Program.cs** y agregue lo siguiente al método **Main**:

	```
	Console.WriteLine("Starting tests");
	System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
	// Initialize the runtime
	SCPRuntime.Initialize();


	//If we are not running under the local context, throw an error
	if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
	{
	    throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
	}
	// Create test instance
	LocalTest tests = new LocalTest();
	// Run tests
	tests.RunTestCase();
	Console.WriteLine("Tests finished");
	Console.ReadKey();
	```

6.	Guarde los cambios y presione **F5** o seleccione **Depurar** > **Iniciar depuración** para iniciar el proyecto. Debe aparecer una ventana de consola y el estado del registro a medida que progresen las pruebas. Cuando se muestre **Pruebas finalizadas**, presione cualquier tecla para cerrar la ventana.

7.	Use el **Explorador de Windows** para buscar el directorio que contiene el proyecto; por ejemplo, **C:\\Usuarios<su\_nombre\_de\_usuario>\\Documentos\\Visual Studio 2013\\Projects\\WordCount\\WordCount**. En este directorio, abra **Bin** y haga clic en **Depurar**. Debería ver los archivos de texto que se generaron cuando se ejecutaron las pruebas: sentences.txt, counter.txt y splitter.txt. Abra cada archivo de texto e inspeccione los datos.

	> [AZURE.NOTE] Los datos de cadena se almacenan como una matriz de valores decimales en estos archivos. Por ejemplo, [[97,103,111]] en el archivo **splitter.txt** es la palabra 'and'.

Aunque llevar a cabo las pruebas de una aplicación de recuento de palabras básica de forma local es bastante sencillo, el verdadero valor se observa cuando tiene una topología compleja que se comunica con orígenes de datos externos o realiza análisis de datos complejos. Cuando se trabaja en un proyecto de este tipo, puede que necesite establecer puntos de interrupción y recorrer el código en los componentes para aislar problemas.

> [AZURE.NOTE] Asegúrese de volver a establecer el **tipo de proyecto** en **Biblioteca de clases** antes de implementarlo en un clúster de Storm en HDInsight.

###Información del registro

Puede registrar fácilmente información de los componentes de la topología mediante `Context.Logger`. Por ejemplo, lo siguiente creará una entrada del registro informativo:

```
Context.Logger.Info("Component started");
```

Se puede ver la información registrada desde el **registro del servicio Hadoop**, que se encuentra en **Explorador de servidores**. Expanda la entrada para el clúster de Storm en HDInsight y, después, expanda **Registro del servicio Hadoop**. Por último, seleccione el archivo de registro que desea consultar.

> [AZURE.NOTE] Los registros se almacenan en la cuenta de almacenamiento de Azure que usa el clúster. Si se trata de una suscripción diferente de la que ha iniciado sesión con Visual Studio, debe iniciar sesión en la suscripción que contiene la cuenta de almacenamiento para ver esta información.

###Visualización de la información del error

Para ver los errores que se han producido en una topología en ejecución, siga estos pasos:

1.	En el **Explorador de servidores**, haga clic con el botón secundario en el clúster de Storm en HDInsight y seleccione **Ver topologías de Storm**.

2.	Para el **Spout** y los **Bolts**, la columna **Último error** tendrá información sobre el último error que ha ocurrido.

3.	Seleccione el **Id. de Spout** o **Id. de bolt** del componente del que se muestra un error. En la página de detalles que se muestra, se enumerará información adicional sobre el error en la sección **Errores** de la parte inferior de la página.

4.	Para obtener más información, seleccione un **Puerto** en la sección **Ejecutores** de la página para ver el registro de trabajo de Storm de los últimos minutos.

##Pasos siguientes

Ahora que ha aprendido a desarrollar e implementar topologías de Storm desde las herramientas de HDInsight para Visual Studio, obtenga más información sobre el [Procesamiento de eventos desde Centro de eventos de Azure con Storm en HDInsight](hdinsight-storm-develop-csharp-event-hub-topology.md).

Para ver un ejemplo de una topología de C# que divide los datos de una secuencia en varias secuencias, consulte [Ejemplo de Storm en C#](https://github.com/Blackmist/csharp-storm-example).

Para obtener más información acerca de cómo crear topologías de C#, visite [SCP.NET GettingStarted.md](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Para conocer más formas de trabajar con HDInsight y obtener más ejemplos de Storm en HDInsight, consulte lo siguiente:

**Apache Storm en HDInsight**

-	[Implementación y supervisión de topologías con Apache Storm en HDInsight](hdinsight-storm-deploy-monitor-topology.md)

-	[Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md)

**Apache Hadoop en HDInsight**

-	[Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

-	[Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)

-	[Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

**Apache HBase en HDInsight**

-	[Introducción a HBase en HDInsight](hdinsight-hbase-tutorial-get-started.md)

<!---HONumber=AcomDC_0211_2016-->