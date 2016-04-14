<properties
   pageTitle="Información sobre Hadoop en HDInsight mediante la Galería de ejemplos | Microsoft Azure"
   description="Aprenda Hadoop rápidamente ejecutando aplicaciones de ejemplo desde la Galería de introducción de HDInsight. Use datos de ejemplo o proporcione los suyos propios."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.workload="big-data"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="02/04/2016"
   ms.author="jgao"/>

# Información sobre Hadoop con la Galería de introducción de HDInsight de Azure

La Galería de introducción de HDInsight proporciona una manera fácil y rápida de conocer Hadoop ejecutando aplicaciones de ejemplo en HDInsight. Algunos ejemplos incluyen datos de muestra. Puede proporcionar sus propios datos para el resto de los ejemplos. Actualmente, existen los siguientes seis ejemplos (pero pronto habrá más):

- Soluciones con los datos de Azure
	- Análisis de registro del sitio web de Microsoft Azure
	- Análisis de almacenamiento de Microsoft Azure
- Soluciones con datos de muestra
	- Análisis de datos del sensor
	- Análisis de tendencias de Twitter
	- Análisis del registro del sitio web
	- Recomendación de película de Mahout

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Información sobre Hadoop con la Galería de introducción de HDInsight](hdinsight-learn-hadoop-use-sample-gallery-v1.md)

![Soluciones de la Galería de introducción de HBase, Storm y Hadoop en HDInsight.][hdinsight.sample.gallery]

El vídeo siguiente muestra cómo ejecutar la muestra de análisis de tendencias de Twitter:

<center><a href="https://www.youtube.com/embed/7ePbHot1SN4">https://www.youtube.com/embed/7ePbHot1SN4></a></center>

Al panel se pueda acceder a través de http://<YourHDInsightClusterName>.azurehdinsight.net/ o desde el Portal de Azure.

**Para ejecutar un ejemplo de la Galería de introducción, siga estos pasos:**

1. Inicie sesión en el [Portal de Azure][azure.portal].
2. Haga clic en **Examinar todo** en el menú de la izquierda, haga clic en **Clústeres de HDInsight** y en el nombre del clúster.
3. Haga clic en **Panel** en el menú superior.
4. Escriba el nombre de usuario y la contraseña para el usuario HTTP (también denominado usuario del clúster).
6. Haga clic en **Galería de introducción** en la parte superior de la página.
7. Haga clic en una de las muestras. Cada muestra proporciona los pasos detallados para ejecutarla. La imagen siguiente presenta la muestra del análisis de tendencias de Twitter:

	![Ejemplo de análisis de tendencias de Twitter en HDInsight][hdinsight.twitter.sample]

## Pasos siguientes
Otras formas de aprender HDInsight son:

- [Mapa de aprendizaje de HDInsight][hdinsight.learn.map]
- [Infografía de HDInsight][hdinsight.infographic]

<!--Image references-->
[hdinsight.sample.gallery]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Getting-Started-Gallery.png
[hdinsight.twitter.sample]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Twitter-Trend-Analysis-sample.png

<!--Link references-->
[hdinsight.learn.map]: hdinsight-learn-map.md
[hdinsight.infographic]: http://go.microsoft.com/fwlink/?linkid=523960
[azure.portal]: https://portal.azure.com

<!---HONumber=AcomDC_0211_2016-->