<properties
	pageTitle="Muestreo de datos en tablas de HDInsight Hive de Azure | Microsoft Azure"
	description="Reducción del muestreo de datos en tablas de HDInsight Hive (Hadopop) de Azure"
	services="machine-learning,hdinsight"
	documentationCenter=""
	authors="bradsev,hangzh-msft"
	manager="paulettm" 
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/07/2016"
	ms.author="hangzh;bradsev" />

# Muestreo de datos en tablas de HDInsight Hive de Azure

## Introducción

En este artículo, se describe cómo reducir la muestra de datos almacenados en tablas de HDInsight Hive de Azure mediante consultas de Hive. Se explican tres métodos de muestreo normalmente utilizados:

* Muestreo aleatorio uniforme 
* Muestreo aleatorio por grupos 
* Muestreo estratificado

**¿Por qué realizar un muestreo de los datos?** Si el conjunto de datos que pretende analizar es grande, suele ser una buena idea reducirlo a un tamaño más pequeño, pero representativo, que sea más manejable. Esto facilita la comprensión y exploración de los datos, y el diseño de características. Su rol en el proceso de análisis de Cortana es permitir la rápida creación de prototipos de las funciones de procesamiento de datos y de los modelos de aprendizaje automático.

El **menú** a continuación está vinculado a temas que describen cómo realizar un muestreo de datos desde varios entornos de almacenamiento.

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Esta tarea de muestreo es un paso del [proceso de Cortana Analytics (CAP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## Cómo enviar consultas de Hive
Las consultas de subárbol se pueden enviar desde la consola de línea de comandos de Hadoop del nodo principal del clúster de Hadoop. Para ello, inicie sesión en el nodo principal del clúster de Hadoop, abra la consola de la línea de comandos de Hadoop y envíe las consultas de Hive desde allí. Para obtener instrucciones sobre el envío de consultas de Hive en la consola de línea de comandos de Hadoop, consulte [Envío de consultas de Hive](machine-learning-data-science-process-hive-tables.md#submit).

## <a name="uniform"></a> Muestra aleatoria uniforme
El muestreo aleatorio uniforme implica que cada fila del conjunto de datos tiene la misma probabilidad de muestreo. Esto se puede implementar añadiendo un campo adicional de rand() al conjunto de datos en la consulta "select" interna, y en la consulta "select" externa esa condición en ese campo aleatorio.

Aquí se muestra una consulta de ejemplo:

	SET sampleRate=<sample rate, 0-1>;
	select
		field1, field2, …, fieldN
	from
		(
		select
			field1, field2, …, fieldN, rand() as samplekey
		from <hive table name>
		)a
	where samplekey<='${hiveconf:sampleRate}'

En este caso, `<sample rate, 0-1>` especifica la proporción de registros que los usuarios quieren usar como muestra.

## <a name="group"></a> Muestreo aleatorio por grupos

Cuando se realiza un muestreo de datos de categoría, podría querer incluir o excluir todas las instancias de algún valor concreto de una variable de categoría. Esto es lo que significa "muestreo por grupos". Por ejemplo, si tiene una variable de categoría "Estado", que tiene como valores NY, MA, CA, NJ, PA, etc., querrá que los registros de un mismo estado estén siempre juntos, ya están muestreados o no.

Aquí se muestra una consulta de ejemplo que realiza un muestreo por grupo:

	SET sampleRate=<sample rate, 0-1>;
    select
		b.field1, b.field2, …, b.catfield, …, b.fieldN
	from
		(
		select
			field1, field2, …, catfield, …, fieldN
		from <table name>
		)b
	join
		(
		select
			catfield
		from
			(
			select
				catfield, rand() as samplekey
			from <table name>
			group by catfield
			)a
		where samplekey<='${hiveconf:sampleRate}'
		)c
	on b.catfield=c.catfield

## <a name="stratified"></a>Muestreo estratificado

El muestreo aleatorio se estratifica con respecto a una variable de categoría cuando las muestras obtenidas tienen valores de esa categoría que se encuentran en la misma proporción que en la población original de la que se obtuvieron las muestras. En el mismo ejemplo que el anterior, suponga que los datos tienen subpoblaciones según los estados; por ejemplo, NJ tiene 100 observaciones, NY tiene 60 observaciones y WA tiene 300 observaciones. Si especifica que la tasa de muestreo estratificado sea 0,5, la muestra obtenida debería tener aproximadamente 50, 30 y 150 observaciones de NJ, NY y WA, respectivamente.

Aquí se muestra una consulta de ejemplo:

	SET sampleRate=<sample rate, 0-1>;
    select
		field1, field2, field3, ..., fieldN, state
	from
		(
		select
			field1, field2, field3, ..., fieldN, state,
			count(*) over (partition by state) as state_cnt,
      		rank() over (partition by state order by rand()) as state_rank
      	from <table name>
		) a
	where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Para obtener información sobre los métodos de muestreo más avanzados que están disponibles en Hive, consulte [Manual de lenguaje: muestreo](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).
 

<!---HONumber=AcomDC_0211_2016-->