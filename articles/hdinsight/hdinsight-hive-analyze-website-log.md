<properties 
	pageTitle="Uso de Hive con Hadoop para el análisis de registros de sitios web | Microsoft Azure" 
	description="Aprenda a usar Hive con HDInsight para analizar registros de sitios web. Usará un archivo de registro como entrada en una tabla de HDInsight y HiveQL para consultar los datos." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/25/2016" 
	ms.author="nitinme"/>

# Uso de Hive con HDInsight para analizar registros de sitios web

Aprenda a usar HiveQL con HDInsight para analizar registros de un sitio web. El análisis de registros de sitios web se puede usar para segmentar su público en función de actividades parecidas, clasificar los visitantes a los sitios por datos demográficos, descubrir el contenido que ven, los sitios web de los que proceden, etc.

En este ejemplo, usará un clúster de HDInsight para analizar archivos de registro de sitios web a fin de conocer la frecuencia de las visitas al sitio web en un día desde sitios web externos en un día. También generará un resumen de errores de sitios web que experimentan los usuarios. Aprenderá a:

- Conectarse a un almacenamiento de blobs de Azure, que contiene archivos de registro de sitios web.
- Crear tablas de HIVE para consultar esos registros.
- Crear consultas de HIVE para analizar los datos.
- Usar Microsoft Excel para conectarse a HDInsight (usando conectividad abierta de base de datos, ODBC) para recuperar los datos analizados.

![HDI.Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

##Requisitos previos

- Debe aprovisionar un clúster de Hadoop en HDInsight de Azure. Para obtener instrucciones, consulte [Aprovisionamiento de clústeres de HDInsight][hdinsight-provision]. 
- Debe tener instalado Microsoft Excel 2013 o Excel 2010.
- Debe tener [Microsoft Hive ODBC Driver para](http://www.microsoft.com/download/details.aspx?id=40886) importar datos de Hive en Excel.


##Para ejecutar el ejemplo

1. En el [Portal de Azure](https://ms.portal.azure.com/), en el panel de inicio (si ancló el clúster allí), haga clic en el icono de clúster en el que quiera ejecutar el ejemplo.

2. En la hoja del clúster, en **Vínculos rápidos**, haga clic en el **panel del clúster** y luego, en la hoja del **panel del clúster**, haga clic en el **panel del clúster de HDInsight**. También puede abrir directamente el panel con la siguiente dirección URL:

	 	https://<clustername>.azurehdinsight.net
	
	Cuando se le pida, autentíquese con el nombre de usuario y la contraseña de administrador que usó cuando aprovisionó el clúster.
  
2. En la página web que se abre, haga clic en la pestaña **Galería de introducción** y, a continuación, en la categoría **Soluciones con datos de ejemplo**, haga clic en el ejemplo **Análisis del registro del sitio web**.

3. Siga las instrucciones que se proporcionan en la página web para finalizar el ejemplo.

##Pasos siguientes
Pruebe el siguiente ejemplo: [Análisis de datos de sensor usando Hive con HDInsight](hdinsight-hive-analyze-sensor-data.md).


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png
 

<!---HONumber=AcomDC_0302_2016-->