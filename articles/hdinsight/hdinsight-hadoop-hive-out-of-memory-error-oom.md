<properties
	pageTitle="Error de memoria insuficiente (OOM) - Configuración de Hive | Microsoft Azure"
	description="Solucionar un error de memoria insuficiente (OOM) desde una consulta de Hive en Hadoop en HDInsight. El escenario de cliente es una consulta entre numerosas tablas de gran tamaño."
	keywords="error de memoria insuficiente, OOM, configuración de Hive"
	services="hdinsight"
	documentationCenter=""
	authors="rashimg"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="12/10/2015"
	ms.author="rashimg;cgronlun"/>

# Solucionar un error de memoria insuficiente (OOM) con la configuración de memoria de Hive en Hadoop en Azure HDInsight

Uno de los problemas que suelen tener nuestros clientes es recibir un error de memoria insuficiente mientras usan Hive. En este artículo se describen un escenario de cliente y la configuración de Hive recomendada para solucionar el problema.

## Escenario: Consulta de Hive entre tablas de gran tamaño

Un cliente ejecutó la consulta siguiente con Hive.

	SELECT
		COUNT (T1.COLUMN1) as DisplayColumn1,
		…
		…
		….
	FROM
		TABLE1 T1,
		TABLE2 T2,
		TABLE3 T3,
		TABLE5 T4,
		TABLE6 T5,
		TABLE7 T6
	where (T1.KEY1 = T2.KEY1….
		…
		…

Algunos matices de esta consulta:

* T1 es un alias para una tabla de gran tamaño, TABLE1, que cuenta con numerosos tipos de columna de CADENA.
* Otras tablas no son tan grandes, pero tienen un gran número de columnas.
* Todas las tablas se unen entre sí, en algunos casos con varias columnas en TABLE1, etc.

Cuando el cliente ejecutó la consulta con Hive en MapReduce en un clúster A3 de 24 nodos, esta se ejecutó en aproximadamente 26 minutos. El cliente observó los mensajes de advertencia siguientes al ejecutarse la consulta con Hive en MapReduce:

	Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
	Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Como la consulta terminó de ejecutarse en alrededor de 26 minutos, el cliente hizo caso omiso de estas advertencias y, en su lugar, empezó a centrarse en cómo mejorar aún más el rendimiento de esta consulta.

El cliente consultó [Optimizar consultas de Hive para Hadoop en HDInsight](hdinsight-hadoop-optimize-hive-query.md) y decidió usar el motor de ejecución de Tez. Tras ejecutarse la misma consulta con la configuración de Tez habilitada, la consulta se ejecutó durante 15 minutos y después produjo el siguiente error:

	Status: Failed
	Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
	org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
	org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
	org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
	org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
	org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
	org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
	Caused by: java.lang.OutOfMemoryError: Java heap space

El cliente decidió entonces usar una VM más grande (es decir, D12), pensando que tendría más espacio de montón. Aun así, el cliente seguía viendo el error. El cliente se puso en contacto con el equipo de HDInsight para obtener ayuda en la depuración de este problema.

## Depurar el error de memoria insuficiente

Nuestros equipos de soporte técnico e ingeniería localizaron uno de los problemas que hicieron que el error de memoria insuficiente fuera un [problema conocido descrito en Apache JIRA](https://issues.apache.org/jira/browse/HIVE-8306). En la descripción de JIRA:

	When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesnt take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

Confirmamos que **hive.auto.convert.join.noconditionaltask** efectivamente se estableció en **true** buscando en el archivo hive-site.xml:

	<property>
    	<name>hive.auto.convert.join.noconditionaltask</name>
    	<value>true</value>
    	<description>
      		Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
      		If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
      		specified size, the join is directly converted to a mapjoin (there is no conditional task).
    	</description>
  	</property>

Basada en la advertencia y en JIRA, nuestra hipótesis era que Map Join era la causa del error de memoria insuficiente del espacio de montón de Java. Por ello, profundizamos más en este problema.

Como se explica en la entrada de blog [Configuración de memoria de Hadoop Yarn en HDInsight](http://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), cuando se usa el motor de ejecución de Tez, el espacio de montón utilizado pertenece en realidad al contenedor de Tez. Consulte la siguiente imagen, que describe la memoria del contenedor de Tez.

![Diagrama de memoria del contenedor de Tez: Error de memoria insuficiente de Hive](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)


Como sugiere la entrada de blog, los dos valores de configuración de memoria siguientes definen la memoria del contenedor del montón: **hive.tez.container.size** y **hive.tez.java.opts**. En nuestra experiencia, la excepción de memoria insuficiente no significa que el tamaño del contenedor sea demasiado pequeño. Significa que el tamaño del montón de Java (hive.tez.java.opts) es demasiado pequeño. Por tanto, siempre que vea memoria insuficiente, puede intentar aumentar **hive.tez.java.opts**. Si es necesario, es posible que deba aumentar **hive.tez.container.size**. El porcentaje del valor de **java.opts** debe ser de aproximadamente el 80% de **container.size**.

> [AZURE.NOTE]El valor de **hive.tez.java.opts** siempre debe ser menor que **hive.tez.container.size**.

Como una máquina D12 tiene 28 GB de memoria, se optó por usar un tamaño de contenedor de 10 GB (10 240 MB) y asignar el 80% a java.opts. Esto se llevó a cabo en la consola de Hive con el valor siguiente:

	SET hive.tez.container.size=10240
	SET hive.tez.java.opts=-Xmx8192m

Basada en estos valores, la consulta se ejecutó correctamente en menos de diez minutos.

## Conclusión: Errores de memoria insuficiente y tamaño del contenedor

Recibir un error de memoria insuficiente no significa necesariamente que el tamaño del contenedor sea demasiado pequeño. En su lugar, debe configurar las opciones de memoria para que aumente el tamaño del montón y sea de al menos el 80% del tamaño de memoria del contenedor.

<!---HONumber=AcomDC_1217_2015-->