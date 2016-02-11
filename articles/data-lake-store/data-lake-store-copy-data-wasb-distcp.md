<properties 
   pageTitle="Copia de datos a y desde WASB en el Almacén de Data Lake mediante Distcp| Microsoft Azure"
   description="Use la herramienta Distcp para copiar datos a y desde los blobs de Almacenamiento de Azure al Almacén de Data Lake." 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/06/2016"
   ms.author="nitinme"/>

# Utilice Distcp para copiar datos entre los blobs de Almacenamiento de Azure y el Almacén de Data Lake

Una vez que haya creado un clúster de HDInsight que tenga acceso a una cuenta de Almacén de Data Lake, puede usar herramientas de ecosistema de Hadoop como Distcp para copiar datos **a y desde** un almacenamiento de clúster de HDInsight (WASB) en una cuenta de Almacén de Data Lake. En este artículo se ofrecen instrucciones sobre cómo lograr esto.

##Requisitos previos

Antes de empezar este artículo, debe tener lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Habilite su suscripción de Azure** para la versión de vista previa pública del Almacén de Data Lake. Consulte las [instrucciones](data-lake-store-get-started-portal.md#signup). 
- **Clúster de HDInsight de Azure** con acceso a una cuenta de Almacén de Data Lake. Vea [Aprovisionamiento de un clúster de HDInsight con el Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md). Asegúrese de habilitar el Escritorio remoto para el clúster.

## Utilice Distcp desde el Escritorio remoto (clúster de Windows) o SSH (clúster de Linux)

Un clúster de HDInsight incluye la utilidad Distcp, que puede utilizarse para copiar datos de orígenes diferentes en un clúster de HDInsight. Si ha configurado el clúster de HDInsight para utilizar el Almacén de Data Lake como almacenamiento adicional, la utilidad Distcp puede utilizarse también directamente y sin configuraciones adicionales para copiar datos a y desde una cuenta de Almacén de Data Lake. En esta sección veremos cómo usar la utilidad Distcp.

1. Si tiene un clúster de Windows, obtenga acceso remoto a un clúster de HDInsight que tenga acceso a una cuenta de Almacén de Data Lake. Para obtener instrucciones, vea [Conexión a los clústeres con RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp). En el Escritorio del clúster, abra la línea de comandos de Hadoop.

	Si tiene un clúster de Linux, use SSH para conectarse al clúster. Vea [Conexión a un clúster de HDInsight basado en Linux](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster). Ejecute los comandos desde el símbolo del sistema SSH.

3. Compruebe si puede tener acceso a los blobs de Almacenamiento de Azure (WASB). Ejecute el siguiente comando:

		hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

	Esto debería proporcionar una lista de contenido en el blob de almacenamiento.

4. De forma similar, compruebe si puede tener acceso a la cuenta del Almacén de Data Lake desde el clúster. Ejecute el siguiente comando:

		hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

	Esto debería proporcionar una lista de archivos o carpetas en la cuenta del Almacén de Data Lake.

5. Utilice Distcp para copiar datos desde WASB a una cuenta de Almacén de Data Lake.

		hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

	Esto copiará el contenido de la carpeta **/example/data/gutenberg/** de WASB a **/myfolder** en la cuenta del Almacén de Data Lake.

6. Asimismo, utilice Distcp para copiar datos de la cuenta de la cuenta del Almacén de Data Lake a WASB.

		hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

	Esto copiará el contenido de **/myfolder** de la cuenta del Almacén de Data Lake en la carpeta **/example/data/gutenberg/** de WASB.

## Consulte también

- [Copiar datos de los blobs de almacenamiento de Azure en el Almacén Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
- [Protección de los datos en el Almacén de Data Lake](data-lake-store-secure-data.md)
- [Uso de Análisis de Azure Data Lake con el Almacén de Data Lake](data-lake-analytics-get-started-portal.md)
- [Uso de HDInsight de Azure con el Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0107_2016-->