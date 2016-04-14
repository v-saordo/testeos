<properties
   pageTitle="Tendencias de Twitter con Apache Storm en HDInsight | Microsoft Azure"
   description="Aprenda a usar Trident para crear una topología de Apache Storm que determine las tendencias en Twitter, según los hash tags."
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

#Determinación de las tendencias de Twitter con Apache Storm en HDInsight

Aprenda a usar Trident para crear una topología de Storm que determine las tendencias (hash tags) en Twitter.

Trident es una abstracción de alto nivel que ofrece herramientas como uniones, agregaciones, agrupaciones, funciones y filtros. Además, Trident agrega primitivas para realizar el procesamiento incremental, con estado. En este ejemplo se muestra cómo crear una topología con un spout, una función y varias funciones integradas personalizadas que ofrece Trident.

> [AZURE.NOTE] Este ejemplo se basa principalmente en el ejemplo [Trident Storm](https://github.com/jalonsoramos/trident-storm) de Juan Alonso.

##Requisitos

* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Java y el JDK 1.7</a>

* <a href="http://maven.apache.org/what-is-maven.html" target="_blank">Maven</a>

* <a href="http://git-scm.com/" target="_blank">Git</a>

* Una cuenta de desarrollador de Twitter

##Descarga del proyecto

Use el código siguiente para clonar el proyecto de forma local.

	git clone https://github.com/Blackmist/TwitterTrending

##Topología

La topología de este ejemplo es la siguiente:

![topología](./media/hdinsight-storm-twitter-trending/trident.png)

> [AZURE.NOTE] Se trata de una vista muy simplificada de la topología. Se distribuirán varias instancias de los componentes entre los nodos del clúster.

El código de Trident que implementa la topología es como sigue:

	topology.newStream("spout", spout)
	    .each(new Fields("tweet"), new HashtagExtractor(), new Fields("hashtag"))
	    .groupBy(new Fields("hashtag"))
	    .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
	    .newValuesStream()
	    .applyAssembly(new FirstN(10, "count"))
		.each(new Fields("hashtag", "count"), new Debug());

Este código hace lo siguiente:

1. Crea un flujo nuevo desde el spout. El spout recupera los tweets de Twitter y los filtra por palabras clave específicas (love, music y coffee en este ejemplo).

2. Se utiliza HashtagExtractor, una función personalizada, para extraer hash tags de cada tweet. Estos se envían a la secuencia.

3. El flujo se agrupa por hash tag y se pasa a un agregador. Este agregador crea un recuento de cuántas veces se ha repetido cada hashtag. Estos datos se conservan en memoria. Por último, se emite un nuevo flujo que contiene el hash tag y el recuento.

4. Dado que solo estamos interesados en los hash tags más populares de un lote determinado de tweets, se aplica el ensamblado **FirstN** para que solo se devuelvan los 10 valores superiores, según el campo de recuento.

> [AZURE.NOTE] Además del spout y HashtagExtractor, estamos utilizando la funcionalidad integrada de Trident.
>
> Para obtener información sobre las operaciones integradas, consulte el <a href="https://storm.apache.org/apidocs/storm/trident/operation/builtin/package-summary.html" target="_blank">Paquete storm.trident.operation.builtin</a>.
>
> Para las implementaciones de Trident-state que no sea MemoryMapState, consulte lo siguiente:
>
> * <a href="https://github.com/fhussonnois/storm-trident-elasticsearch" target="_blank">Búsqueda elástica de Storm Trident</a>
>
> * <a href="https://github.com/kstyrc/trident-redis" target="_blank">trident-redis</a>

###El spout

El spout, **TwitterSpout**, usa <a href="http://twitter4j.org/en/" target="_blank">Twitter4j</a> para recuperar tweets desde Twitter. Se crea un filtro (en este ejemplo, love, music y coffee) y los tweets entrantes (estados) que coinciden con el filtro se almacenan en una cola de bloqueo vinculada. (Para obtener más información, consulte <a href="http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/LinkedBlockingQueue.html" target="_blank">Clase LinkedBlockingQueue</a>). Por último, los elementos se sacan de la cola y se emiten a la topología.

###El HashtagExtractor

Para extraer hash tags, se usa <a href="http://twitter4j.org/javadoc/twitter4j/EntitySupport.html#getHashtagEntities--" target="_blank">getHashtagEntities</a> para recuperar todos los hash tags contenidos en el tweet. Después se envían a la secuencia.

##Habilitación de Twitter

Siga estos pasos para registrar una nueva aplicación de Twitter y obtener la información del token de acceso y de consumidor necesaria para leer desde Twitter:

1. Vaya a <a href="https://apps.twitter.com" target="_blank">Aplicaciones de Twitter</a> y haga clic en el botón **Crear nueva aplicación**. Al rellenar el formulario, deje el campo **URL de devolución de llamada** vacío.

2. Cuando se cree la aplicación, haga clic en la pestaña **Claves y tokens de acceso**.

3. Copie la información de **Clave de consumidor** y **Secreto de consumidor**.

4. En la parte inferior de la página, seleccione **Crear mi token de acceso**, si no existen tokens. Cuando se hayan creado los tokens, copie la información del **Token de acceso** y del **Secreto del token de acceso**.

5. En el proyecto **TwitterSpoutTopology** anteriormente clonado, abra el archivo **resources/twitter4j.properties**, agregue la información recopilada en los pasos anteriores y, por último, guarde el archivo.

##Generación de la topología

Use el código siguiente para crear el proyecto:

		cd [directoryname]
		mvn compile

##Prueba de la topología

Use el comando siguiente para probar localmente la topología:

	mvn compile exec:java -Dstorm.topology=com.microsoft.example.TwitterTrendingTopology

Después de que se inicie la topología, debería ver la información de depuración que contiene los hash tags y recuentos que ha emitido la topología. La salida debe ser similar a la siguiente:

	DEBUG: [Quicktellervalentine, 7]
	DEBUG: [GRAMMYs, 7]
	DEBUG: [AskSam, 7]
	DEBUG: [poppunk, 1]
	DEBUG: [rock, 1]
	DEBUG: [punkrock, 1]
	DEBUG: [band, 1]
	DEBUG: [punk, 1]
	DEBUG: [indonesiapunkrock, 1]

##Pasos siguientes

Ahora que ha probado localmente la topología, descubra cómo implementar la topología: [Implementación y administración de topologías de Apache Storm en HDInsight](hdinsight-storm-deploy-monitor-topology.md).

También se puede interesar en los siguientes temas de Storm:

* [Desarrollo de las topologías de Java para Storm en HDInsight con Maven](hdinsight-storm-develop-java-topology.md)

* [Desarrollo de las topologías de C# para Storm en HDInsight con Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Para obtener más ejemplos de Storm para HDInsight:

* [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md)

<!---HONumber=AcomDC_0211_2016-->