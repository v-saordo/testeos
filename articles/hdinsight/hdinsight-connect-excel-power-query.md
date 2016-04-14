<properties
	pageTitle="Conexión de Excel a Hadoop con Power Query | Microsoft Azure"
	description="Vea cómo aprovechar las ventajas de los componentes de inteligencia empresarial y usar Power Query para Excel a fin de acceder a los datos almacenados en Hadoop en HDInsight."
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
	ms.topic="article"
	ms.date="01/28/2016"
	ms.author="jgao"/>


#Conexión de Excel a Hadoop con Power Query

Una de las características clave de la solución para Big Data de Microsoft es la integración de los componentes de Microsoft Business Intelligence (BI) con los clústeres de Hadoop en Azure HDInsight. Un ejemplo importante de esta integración es la capacidad de conectar Excel a la cuenta de almacenamiento de Azure que contiene los datos asociados a su clúster de Hadoop usando el complemento de Microsoft Power Query para Excel. En este artículo se describen las pautas para configurar y usar Power Query para consultar los datos asociados con un clúster de Hadoop administrado con HDInsight.

> [AZURE.NOTE] Aunque los pasos descritos en este artículo se pueden usar con un clúster de HDInsight basado en Linux o Windows, Windows es necesario para la estación de trabajo del cliente.

### Requisitos previos

Antes de empezar este artículo, debe tener lo siguiente:

- **Un clúster de HDInsight**. Para configurarlo, consulte [Introducción a HDInsight de Azure][hdinsight-get-started].
- **Una estación de trabajo** que ejecute Windows 7, Windows Server 2008 R2 o un sistema operativo posterior.
- **Office 2013 Professional Plus, Office 365 ProPlus, Excel 2013 Standalone u Office 2010 Professional Plus**.


## Instalación de Power Query

Se puede usar Power Query para importar datos desde diferentes orígenes en Microsoft Excel, donde se pueden habilitar herramientas de (BI) como PowerPivot y Power View. En concreto, Power Query puede importar datos ofrecidos o generados por un trabajo de Hadoop ejecutado en un clúster de HDInsight.

Descargue Microsoft Power Query para Excel desde el [Centro de descarga de Microsoft][powerquery-download] e instálelo.

## Importación de datos de HDInsight a Excel

El complemento de Power Query para Excel facilita la importación de datos desde el clúster de HDInsight hasta Excel, donde se pueden usar herramientas de BI como PowerPivot y Power Map para la inspección, el análisis y la presentación de los datos.

**Para importar datos desde un clúster de HDInsight**

1. Abra Excel.

2. Cree un libro vacío.

3. Haga clic en el menú **Power Query**, en **De Azure** y, luego, en **De HDInsight de Microsoft Azure **.

	![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

	**Nota**: Si no ve el menú **Power Query**, vaya a **Archivo** > **Opciones** > **Complementos** y seleccione **Complementos COM** en el cuadro desplegable **Administrador** situado al final de la página. Elija el botón **Go...** y compruebe que la casilla del complemento de Power Query para Excel esté activada.

	**Nota:** Power Query también permite importar datos de HDFS haciendo clic **De otros orígenes**.

3. En **Nombre de cuenta**, escriba el nombre de la cuenta de almacenamiento de blobs de Azure asociada con su clúster y, a continuación, haga clic en **Aceptar**. Esto puede ser una [cuenta de almacenamiento predeterminada](hdinsight-administer-use-management-portal.md#find-the-default-storage-account) o una cuenta de almacenamiento vinculada. El formato es *https://<StorageAccountName>.blob.core.windows.net/*.

4. En **Clave de cuenta**, escriba la clave de cuenta para la cuenta de almacenamiento de blobs y, a continuación, haga clic en **Guardar**. (Solo tendrá que hacer esto la primera vez que obtenga acceso a este almacén).

5. En el panel del **navegador** situado a la izquierda del Editor de consultas, haga doble clic en el nombre del contenedor de almacenamiento de blobs. De forma predeterminada, el nombre del contenedor es el mismo que el del clúster.

6. Busque **HiveSampleData.txt** en la columna **Nombre** (la ruta de acceso de la carpeta es **../hive/warehouse/hivesampletable/)**, y haga clic en **Binario** a la izquierda de HiveSampleData.txt. HiveSampleData.txt incluye todo el clúster. Opcionalmente, puede utilizar su propio archivo.

	![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]

7. Si quiere, puede cambiar el nombre de las columnas. Cuando haya terminado, haga clic en **Aplicar y cerrar**. Los datos se han cargado en el libro:

	![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## Pasos siguientes

En este artículo, ha aprendido a usar Power Query para recuperar datos de HDInsight en Excel. Del mismo modo, puede recuperar datos de HDInsight en Base de datos SQL de Azure. También se pueden cargar los datos en HDInsight. Para obtener más información, consulte los artículos siguientes:

* [Conexión de Excel a HDInsight con Microsoft Hive ODBC Driver][hdinsight-ODBC]
* [Carga de datos en HDInsight][hdinsight-upload-data]

[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689

<!---HONumber=AcomDC_0218_2016-->