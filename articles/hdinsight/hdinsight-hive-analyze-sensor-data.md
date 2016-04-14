<properties
	pageTitle="Análisis de datos de sensor usando Hive y Hadoop | Microsoft Azure"
	description="Aprenda a analizar datos de sensor usando la Consola de consultas de Hive con HDInsight (Hadoop) y a visualizar los datos en Microsoft Excel con PowerView."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016" 
	ms.author="larryfr"/>

#Análisis de datos de sensor mediante la consola de consultas de Hive en Hadoop con HDInsight

Aprenda a analizar datos de sensor usando la consola de consultas de Hive con HDInsight (Hadoop) y a visualizar los datos en Microsoft Excel con Power View.

> [AZURE.NOTE] Los pasos de este tutorial solo se aplican a los clústeres de HDInsight basados en Windows.

En este ejemplo, usará Hive para procesar datos de historial producidos por sistemas de calefacción, ventilación y aire acondicionado (HVAC) para identificar sistemas que no pueden mantener de manera fiable una temperatura establecida . Aprenderá a:

- Crear tablas de HIVE para consultar datos almacenados en archivos de valores separados por comas (CSV).
- Crear consultas de HIVE para analizar los datos.
- Usar Microsoft Excel para conectarse a HDInsight (usando una conexión de base de datos abierta, ODBC) para recuperar los datos analizados.
- Usar Power View para visualizar los datos.

![Diagrama de la arquitectura de la solución](./media/hdinsight-hive-analyze-sensor-data/hvac-architecture.png)

##Requisitos previos

* Un clúster de HDInsight (Hadoop): consulte [Aprovisionamiento de clústeres de Hadoop en HDInsight](hdinsight-provision-clusters.md) para obtener información sobre la creación de un clúster.

* Microsoft Excel 2013

	> [AZURE.NOTE] Microsoft Excel se usa para la visualización de datos con [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=es-ES&rs=es-ES&ad=US).

* [Microsoft Hive ODBC Driver](http://www.microsoft.com/download/details.aspx?id=40886)

##Para ejecutar el ejemplo

1. Desde el explorador web, navegue a la siguiente dirección URL. Reemplace `<clustername>` por el nombre del clúster de HDInsight.

	 	https://<clustername>.azurehdinsight.net

	Cuando se le pida, autentíquese con el nombre de usuario y la contraseña de administrador que usó cuando aprovisionó este clúster.

2. En la página web que se abre, haga clic en la pestaña **Galería de introducción** y, a continuación, en la categoría **Soluciones con datos de ejemplo**, haga clic en el ejemplo **Análisis de datos de sensor**.

3. Siga las instrucciones que se proporcionan en la página web para finalizar el ejemplo.

<!---HONumber=AcomDC_0211_2016-->