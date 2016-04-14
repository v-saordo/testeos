<properties
	pageTitle="Exploración de los datos de las tablas de Hive con consultas de Hive | Microsoft Azure"
	description="Exploración de los datos de las tablas de Hive con consultas de Hive."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm" 
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/08/2016"
	ms.author="hangzh;bradsev" />

# Exploración de los datos de las tablas de Hive con consultas de Hive 

## Introducción

Este documento proporciona scripts de Hive de ejemplo que se usan para explorar los datos de las tablas de Hive.

El **menú** a continuación vincula a temas que describen cómo usar herramientas para explorar los datos desde varios entornos de almacenamiento. Esta tarea es un paso en el proceso de análisis de Cortana (CAP).

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]

## Requisitos previos
En este artículo se supone que ha:

* Creado una cuenta de almacenamiento de Azure. Si necesita instrucciones, consulte [Creación de una cuenta de almacenamiento de Azure](../hdinsight-get-started.md#storage).
* Aprovisionado un clúster de Hadoop personalizado con el servicio HDInsight. Si necesita instrucciones, consulte [Personalización de clústeres de Hadoop de HDInsight de Azure para análisis avanzado](machine-learning-data-science-customize-hadoop-cluster.md).
* Se han cargado los datos en tablas de subárbol en clústeres de Hadoop de HDInsight de Azure. De no ser así, siga [Crear y cargar datos en tablas de Hive](machine-learning-data-science-move-hive-tables.md) para cargar los datos en tablas de Hive primero.
* Habilitado el acceso remoto al clúster. Si necesita instrucciones, consulte [Acceso al nodo principal del clúster de Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#headnode).
* Si necesita instrucciones sobre cómo enviar consultas de Hive, consulte [Envío de consultas de Hive](machine-learning-data-science-move-hive-tables.md#submit)

## Ejemplo de scripts de consulta de Hive para la exploración de datos

1. Obtención del número de observaciones por partición `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. Obtener el número de observaciones por día `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`

3. Obtención de los niveles de una columna de categorías `SELECT  distinct <column_name> from <databasename>.<tablename>`

4. Obtención del número de niveles de combinación de dos columnas de categorías `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. Obtención de la distribución para columnas numéricas `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. Extraer registros de la combinación de dos tablas

	    SELECT
			a.<common_columnname1> as <new_name1>,
			a.<common_columnname2> as <new_name2>,
    		a.<a_column_name1> as <new_name3>,
    		a.<a_column_name2> as <new_name4>,
    		b.<b_column_name1> as <new_name5>,
    		b.<b_column_name2> as <new_name6>
    	FROM
    		(
    		SELECT <common_columnname1>,
    			<common_columnname2>,
				<a_column_name1>,
				<a_column_name2>,
			FROM <databasename>.<tablename1>
			) a
			join
			(
			SELECT <common_columnname1>,
    			<common_columnname2>,
				<b_column_name1>,
				<b_column_name2>,
			FROM <databasename>.<tablename2>
			) b
			ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## Scripts de consulta adicionales para escenarios de datos de trayectos en taxi

También se ofrecen ejemplos de consultas que son específicos de escenarios de [datos de carreras de taxi de Nueva York](http://chriswhong.com/open-data/foil_nyc_taxi/) en el [repositorio de Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Estas consultas ya tienen el esquema de datos especificado y están listas para enviarse para su ejecución.

 

<!---HONumber=AcomDC_0211_2016-->