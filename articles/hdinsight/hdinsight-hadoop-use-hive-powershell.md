<properties
   pageTitle="Uso de Hive de Hadoop con PowerShell en HDInsight | Microsoft Azure"
   description="Use PowerShell para ejecutar consultas de Hive en Hadoop en HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/28/2016"
   ms.author="larryfr"/>

#Ejecución de consultas de Hive con PowerShell

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

En este documento se ofrece un ejemplo de uso de Azure PowerShell en el modo Grupo de recursos de Azure para ejecutar consultas de Hive en un clúster Hadoop en HDInsight.

> [AZURE.NOTE] Este documento no ofrece una descripción detallada de las instrucciones de HiveQL que se usan en los ejemplos. Para obtener información sobre el lenguaje HiveQL que se usa en este ejemplo, vea [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md).


**Requisitos previos**

Necesitará lo siguiente para completar los pasos de este artículo.

- **Un clúster de HDInsight de Azure (Hadoop en HDInsight) (basado en Windows o en Linux)**
- **Una estación de trabajo con Azure PowerShell**. Consulte [Instalar Azure PowerShell 1.0 y versiones posteriores](hdinsight-administer-use-powershell.md#install-azure-powershell-10-and-greater).

##Ejecución de consultas de Hive con Azure PowerShell

Azure PowerShell proporciona *cmdlets* que le permiten ejecutar de manera remota las consultas de Hive en HDInsight. De manera interna, esto se logra mediante llamadas REST a [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anteriormente llamado Templeton), que se ejecuta en el clúster de HDInsight.

Los siguientes cmdlets se utilizan al ejecutar las consultas de Hive en un clúster de HDInsight remoto:

* **Add-AzureRmAccount**: autentica a Azure PowerShell en la suscripción de Azure.

* **New-AzureRmHDInsightHiveJobDefinition**: crea una nueva *definición de trabajo* con las instrucciones HiveQL especificadas.

* **Start-AzureRmHDInsightJob**: envía la definición del trabajo a HDInsight, inicia el trabajo y devuelve un objeto de *trabajo* que se puede usar para comprobar el estado del trabajo.

* **Wait-AzureRmHDInsightJob**: usa el objeto de trabajo para comprobar el estado del trabajo. Esperará hasta que el trabajo se complete o se supere el tiempo de espera.

* **Get-AzureRmHDInsightJobOutput**: se usa para recuperar la salida del trabajo.

* **Invoke-AzureRmHDInsightHiveJob**: se usa para ejecutar instrucciones de HiveQL. Esto bloqueará la consulta completa, a continuación, devuelve los resultados.

* **Use-AzureRmHDInsightCluster**: establece el clúster actual para usar con el comando **Invoke-AzureRmHDInsightHiveJob**.

Los pasos siguientes muestran cómo usar estos cmdlets para ejecutar un trabajo en el clúster de HDInsight:

1. Mediante un editor, guarde el código siguiente como **hivejob.ps1**. Debe reemplazar **CLUSTERNAME** por el nombre de su clúster de HDInsight.

		#Specify the values
		$clusterName = "CLUSTERNAME"
		$creds=Get-Credential
        		
		# Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureRmAccount
		}

		#HiveQL
		$queryString = "DROP TABLE log4jLogs;" +
				       "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
				       "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"

		#Create an HDInsight Hive job definition
		$hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString 

		#Submit the job to the cluster
		Write-Host "Start the Hive job..." -ForegroundColor Green

		$hiveJob = Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $creds


		#Wait for the Hive job to complete
		Write-Host "Wait for the job to complete..." -ForegroundColor Green
		Wait-AzureRmHDInsightJob -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $creds

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }
		# Print the output
		Write-Host "Display the standard output..." -ForegroundColor Green
		Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $hiveJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. Abra un nuevo símbolo del sistema de **Azure PowerShell**. Cambie los directorios a la ubicación del archivo **hivejob.ps1** y, a continuación, utilice el siguiente comando para ejecutar el script:

		.\hivejob.ps1

    Cuando se ejecuta el script, se le pedirá que escriba las credenciales de cuenta de administrador/HTTPS del clúster. Puede que también se le pida que inicie la sesión en su suscripción de Azure.
    
7. Cuando el trabajo se complete, debe devolver información de manera similar a la siguiente:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Como se mencionó anteriormente **Invoke-Hive** puede utilizarse para ejecutar una consulta y esperar la respuesta. Utilice los comandos siguientes y reemplace **CLUSTERNAME** por el nombre del clúster:

        Use-AzureRmHDInsightCluster -ClusterName $clusterName -HttpCredential $creds
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }
        Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query @"
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
        SELECT * FROM errorLogs;
        "@

	El resultado tendrá un aspecto similar al siguiente:

		2012-02-03	18:35:34	SampleClass0	[ERROR]	incorrect	id
		2012-02-03	18:55:54	SampleClass1	[ERROR]	incorrect	id
		2012-02-03	19:25:27	SampleClass4	[ERROR]	incorrect	id

	> [AZURE.NOTE] Para consultas de HiveQL más extensas, puede usar el cmdlet **Here-Strings** de Azure PowerShell o archivos de script de HiveQL. El siguiente fragmento de código indica cómo usar el cmdlet **Invoke-Hive** para ejecutar un archivo de script de HiveQL. El archivo de script de HiveQL debe cargarse en wasb://.
	>
	> `Invoke-AzureRmHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
	>
	> Para obtener más información acerca de **Here-Strings**, vea <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Uso de Here-Strings de Windows PowerShell</a>.

##Solución de problemas

Si no se devuelve ninguna información cuando se completa el trabajo, pudo haberse producido un error durante el procesamiento. Para ver información de error para este trabajo, agregue lo siguiente al final del archivo **hivejob.ps1**, guárdelo y, a continuación, ejecútelo de nuevo.

	# Print the output of the Hive job.
	Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Se devuelve la información escrita en STDERR en el servidor cuando ejecute el trabajo y puede ayudar a determinar por qué se ha producido el error en el trabajo.

##Resumen

Como puede ver, Azure PowerShell proporciona una manera fácil de ejecutar consultas de Hive en un clúster de HDInsight, de supervisar el estado del trabajo y de recuperar la salida.

##Pasos siguientes

Para obtener información general acerca de Hive en HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

Para obtener información sobre otras maneras en que puede trabajar con Hadoop en HDInsight:

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)

* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0204_2016-->