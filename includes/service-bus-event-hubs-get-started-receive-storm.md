## Recepción de mensajes con Apache Storm

[**Apache Storm**](https://storm.incubator.apache.org) es un sistema de cálculo distribuido en tiempo real que simplifica el procesamiento confiable de flujos de datos sin enlazar. Esta sección muestra cómo utilizar un emisor de Storm para Centros de eventos a fin de recibir eventos de los Centros de eventos. Con Apache Storm, se pueden dividir los eventos en varios procesos hospedados en distintos nodos. La integración de los Centros de eventos con Storm simplifica el consumo de eventos al comprobar de forma transparente el progreso mediante la instalación de Zookeeper de Storm, la administración de puntos de comprobación persistentes y las recepciones en paralelo de los Centros de eventos.

Para más información sobre los patrones de recepción de los Centros de eventos, vea la [Información general de los Centros de eventos][].

Este tutorial usa una instalación de [HDInsight Storm][], que integra el emisor de Centros de eventos que ya se encuentra disponible.

1. Siga el procedimiento descrito en [Introducción a HDInsight Storm](../hdinsight/hdinsight-storm-overview.md) para crear un clúster nuevo de HDInsight y conectarlo a través del Escritorio remoto.

2. Copie el archivo `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` en su entorno de desarrollo local. Contiene events-storm-spout.

3. Utilice el comando siguiente para instalar el paquete en el almacén Maven local. Esto permite agregarlo como referencia en el proyecto de Storm en un paso posterior.

		mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar

4. En Eclipse, cree un proyecto Maven nuevo (haga clic en **Archivo**, **Nuevo** y, a continuación, en **Proyecto**).

   ![][12]

5. Seleccione **Usar ubicación del área de trabajo predeterminada** y, a continuación, haga clic en **Siguiente**

6. Seleccione el arquetipo **maven-archetype-quickstart** y, a continuación, haga clic en **Siguiente**

7. Inserte un **GroupId** y **ArtifactId** y, a continuación, haga clic en **Finalizar**

8. En **pom.xml**, agregue las siguientes dependencias en el nodo `<dependency>`.

		<dependency>
			<groupId>org.apache.storm</groupId>
			<artifactId>storm-core</artifactId>
			<version>0.9.2-incubating</version>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>com.microsoft.eventhubs</groupId>
			<artifactId>eventhubs-storm-spout</artifactId>
			<version>0.9</version>
		</dependency>
		<dependency>
			<groupId>com.netflix.curator</groupId>
			<artifactId>curator-framework</artifactId>
			<version>1.3.3</version>
			<exclusions>
				<exclusion>
					<groupId>log4j</groupId>
					<artifactId>log4j</artifactId>
				</exclusion>
				<exclusion>
					<groupId>org.slf4j</groupId>
					<artifactId>slf4j-log4j12</artifactId>
				</exclusion>
			</exclusions>
			<scope>provided</scope>
		</dependency>

9. En la carpeta **src**, cree un archivo llamado **Config.properties** y copie el siguiente contenido, sustituyendo los valores siguientes:

		eventhubspout.username = ReceiveRule

		eventhubspout.password = {receive rule key}

		eventhubspout.namespace = ioteventhub-ns

		eventhubspout.entitypath = {event hub name}

		eventhubspout.partitions.count = 16

		# if not provided, will use storm's zookeeper settings
		# zookeeper.connectionstring=localhost:2181

		eventhubspout.checkpoint.interval = 10

		eventhub.receiver.credits = 10

	El valor de **eventhub.receiver.credits** determina cuántos eventos se procesan por lotes antes de liberarlos a la canalización de Storm. Por simplicidad, este ejemplo establece el valor en 10. En producción, se debe normalmente establecer en valores más altos; Por ejemplo, 1024.

10. Cree una clase nueva denominada **LoggerBolt** con el código siguiente:

		import java.util.Map;
		import org.slf4j.Logger;
		import org.slf4j.LoggerFactory;
		import backtype.storm.task.OutputCollector;
		import backtype.storm.task.TopologyContext;
		import backtype.storm.topology.OutputFieldsDeclarer;
		import backtype.storm.topology.base.BaseRichBolt;
		import backtype.storm.tuple.Tuple;

		public class LoggerBolt extends BaseRichBolt {
			private OutputCollector collector;
			private static final Logger logger = LoggerFactory
				      .getLogger(LoggerBolt.class);

			@Override
			public void execute(Tuple tuple) {
				String value = tuple.getString(0);
				logger.info("Tuple value: " + value);

				collector.ack(tuple);
			}

			@Override
			public void prepare(Map map, TopologyContext context, OutputCollector collector) {
				this.collector = collector;
				this.count = 0;
			}

			@Override
			public void declareOutputFields(OutputFieldsDeclarer declarer) {
				// no output fields
			}

		}

	Este elemento de Storm registra el contenido de los eventos recibidos. Esto se puede ampliar fácilmente para almacenar las tuplas en un servicio de almacenamiento. El [tutorial de análisis de sensores de HDInsight] usa este mismo enfoque para almacenar datos en HBase.

11. Cree una clase denominada **LogTopology** con el código siguiente:

		import java.io.FileReader;
		import java.util.Properties;
		import backtype.storm.Config;
		import backtype.storm.LocalCluster;
		import backtype.storm.StormSubmitter;
		import backtype.storm.generated.StormTopology;
		import backtype.storm.topology.TopologyBuilder;
		import com.microsoft.eventhubs.samples.EventCount;
		import com.microsoft.eventhubs.spout.EventHubSpout;
		import com.microsoft.eventhubs.spout.EventHubSpoutConfig;

		public class LogTopology {
			protected EventHubSpoutConfig spoutConfig;
			protected int numWorkers;

			protected void readEHConfig(String[] args) throws Exception {
				Properties properties = new Properties();
				if (args.length > 1) {
					properties.load(new FileReader(args[1]));
				} else {
					properties.load(EventCount.class.getClassLoader()
							.getResourceAsStream("Config.properties"));
				}

				String username = properties.getProperty("eventhubspout.username");
				String password = properties.getProperty("eventhubspout.password");
				String namespaceName = properties
						.getProperty("eventhubspout.namespace");
				String entityPath = properties.getProperty("eventhubspout.entitypath");
				String zkEndpointAddress = properties
						.getProperty("zookeeper.connectionstring"); // opt
				int partitionCount = Integer.parseInt(properties
						.getProperty("eventhubspout.partitions.count"));
				int checkpointIntervalInSeconds = Integer.parseInt(properties
						.getProperty("eventhubspout.checkpoint.interval"));
				int receiverCredits = Integer.parseInt(properties
						.getProperty("eventhub.receiver.credits")); // prefetch count
																	// (opt)
				System.out.println("Eventhub spout config: ");
				System.out.println("  partition count: " + partitionCount);
				System.out.println("  checkpoint interval: "
						+ checkpointIntervalInSeconds);
				System.out.println("  receiver credits: " + receiverCredits);

				spoutConfig = new EventHubSpoutConfig(username, password,
						namespaceName, entityPath, partitionCount, zkEndpointAddress,
						checkpointIntervalInSeconds, receiverCredits);

				// set the number of workers to be the same as partition number.
				// the idea is to have a spout and a logger bolt co-exist in one
				// worker to avoid shuffling messages across workers in storm cluster.
				numWorkers = spoutConfig.getPartitionCount();

				if (args.length > 0) {
					// set topology name so that sample Trident topology can use it as
					// stream name.
					spoutConfig.setTopologyName(args[0]);
				}
			}

			protected StormTopology buildTopology() {
				TopologyBuilder topologyBuilder = new TopologyBuilder();

				EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
				topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
						spoutConfig.getPartitionCount()).setNumTasks(
						spoutConfig.getPartitionCount());
				topologyBuilder
						.setBolt("LoggerBolt", new LoggerBolt(),
								spoutConfig.getPartitionCount())
						.localOrShuffleGrouping("EventHubsSpout")
						.setNumTasks(spoutConfig.getPartitionCount());
				return topologyBuilder.createTopology();
			}

			protected void runScenario(String[] args) throws Exception {
				boolean runLocal = true;
				readEHConfig(args);
				StormTopology topology = buildTopology();
				Config config = new Config();
				config.setDebug(false);

				if (runLocal) {
					config.setMaxTaskParallelism(2);
					LocalCluster localCluster = new LocalCluster();
					localCluster.submitTopology("test", config, topology);
					Thread.sleep(5000000);
					localCluster.shutdown();
				} else {
					config.setNumWorkers(numWorkers);
				    StormSubmitter.submitTopology(args[0], config, topology);
				}
			}

			public static void main(String[] args) throws Exception {
				LogTopology topology = new LogTopology();
				topology.runScenario(args);
			}
		}


	Esta clase crea un nuevo emisor de Centros de eventos, utilizando las propiedades del archivo de configuración para crear una instancia. Es importante tener en cuenta que este ejemplo crea tantas tareas de emisor como número de particiones hay en el Centro de eventos, para poder usar el paralelismo máximo permitido por ese Centro de eventos.

<!-- Links -->
[Información general de los Centros de eventos]: event-hubs-overview.md
[HDInsight Storm]: ../hdinsight/hdinsight-storm-overview.md
[tutorial de análisis de sensores de HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md

<!-- Images -->

[12]: ./media/service-bus-event-hubs-getstarted/create-storm1.png
[13]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp1.png
[14]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png

<!---HONumber=AcomDC_1217_2015-->