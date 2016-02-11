<properties 
   pageTitle="Creación de un clúster de HDInsight con el Almacén de Azure Data Lake mediante Azure PowerShell | Azure" 
   description="Use Azure PowerShell para crear y usar clústeres de Hadoop en HDInsight con Azure Data Lake" 
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
   ms.date="01/21/2016"
   ms.author="nitinme"/>

# Creación de un clúster de HDInsight con el Almacén de Data Lake mediante Azure PowerShell

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Using PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)


Aprenda a usar Azure PowerShell para configurar un clúster de HDInsight (Hadoop, HBase o Storm) con acceso a un Almacén de Azure Data Lake. Algunas consideraciones importantes sobre esta versión:

* **En clústeres de Hadoop y Storm (Windows y Linux)**, el almacén de Data Lake solo puede usarse como cuenta de almacenamiento adicional. La cuenta de almacenamiento predeterminada para los clústeres de este tipo seguirán Blobs de almacenamiento de Azure (WASB).

* **En clústeres HBase (Windows y Linux)**, el almacén de Data Lake puede usarse como almacenamiento predeterminado o almacenamiento adicional.


En este artículo, aprovisionamos un clúster de Hadoop con el Almacén de Data Lake como almacenamiento adicional.

La configuración de HDInsight para trabajar con el Almacén de Data Lake mediante PowerShell consta de los pasos siguientes:

* Creación de un Almacén de Azure Data Lake
* Configuración de la autenticación para el acceso basado en roles al Almacén de Data Lake
* Creación de un clúster de HDInsight con autenticación en el Almacén de Data Lake
* Ejecución de un trabajo de prueba en el clúster

## Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Habilite su suscripción de Azure** para la versión de vista previa pública del Almacén de Data Lake. Consulte las [instrucciones](data-lake-store-get-started-portal.md#signup).
- **Windows SDK**. Puede instalarlo desde [aquí](https://dev.windows.com/es-ES/downloads). Úselo para crear un certificado de seguridad.


##Instalar Azure PowerShell 1.0 y versiones posteriores

En primer lugar, debe desinstalar las versiones x 0.9 de Azure PowerShell. Para comprobar la versión de PowerShell instalada, ejecute el siguiente comando desde una ventana de PowerShell:

	Get-Module *azure*
	
Para desinstalar la versión anterior, ejecute **Programas y características** en el panel de control y quite la versión instalada si es anterior a PowerShell 1.0.

Hay dos opciones principales para instalar Azure PowerShell.

- [Galería de PowerShell](https://www.powershellgallery.com/). Ejecute los siguientes comandos en el ISE de PowerShell con privilegios elevados o en la consola de Windows PowerShell con privilegios elevados:

		# Install the Azure Resource Manager modules from PowerShell Gallery
		Install-Module AzureRM
		Install-AzureRM
		
		# Install the Azure Service Management module from PowerShell Gallery
		Install-Module Azure
		
		# Import AzureRM modules for the given version manifest in the AzureRM module
		Import-AzureRM
		
		# Import Azure Service Management module
		Import-Module Azure

	Para más información, vea [Galería de PowerShell](https://www.powershellgallery.com/).

- [Instalador de plataforma web de Microsoft (WebPI)](http://aka.ms/webpi-azps). Si tiene Azure PowerShell 0.9.x instalado, se le pedirá que desinstale 0.9.x. Si ha instalado módulos de Azure PowerShell desde la Galería de PowerShell, el programa de instalación requiere que los módulos se quiten antes de la instalación para garantizar un entorno coherente de PowerShell de Azure. Para instrucciones, consulte [Instalación de Azure PowerShell 1.0 mediante WebPI](https://azure.microsoft.com/blog/azps-1-0/).

WebPI recibirá actualizaciones mensuales. La Galería de PowerShell recibirá actualizaciones de forma continua. Si se encuentra cómodo con la instalación de la Galería de PowerShell, ese será el primer canal para obtener lo mejor y lo más reciente de Azure PowerShell.
 

## Creación de un Almacén de Azure Data Lake

Siga estos pasos para crear un Almacén de Data Lake.

1. En el escritorio, abra una nueva ventana de Azure PowerShell y escriba el siguiente fragmento. Cuando se le solicite que inicie sesión, asegúrese de iniciarla como uno de los administradores o como el propietario de la suscripción:

        # Log in to your Azure account
		Login-AzureRmAccount
        
		# List all the subscriptions associated to your account
		Get-AzureRmSubscription
		
		# Select a subscription 
		Set-AzureRmContext -SubscriptionId <subscription ID>

		# Register for Data Lake Store
		Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

	>[AZURE.NOTE] Si recibe un error similar a `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` al registrar el proveedor de recursos de Almacén de Data Lake, es posible que su suscripción no esté en la lista de permitidas de Almacén de Azure Data Lake. Asegúrese de habilitar su suscripción de Azure en la vista previa pública de Almacén Data Lake siguiendo estas [instrucciones](data-lake-store-get-started-portal.md#signup).

3. La cuenta de Almacén de Azure Data Lake se asocia con un grupo de recursos de Azure. Comience creando un grupo de recursos de Azure.

		$resourceGroupName = "<your new resource group name>"
    	New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

	![Creación de un grupo de recursos de Azure](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "Crear un grupo de recursos de Azure")

2. Cree una cuenta de Almacén de Azure Data Lake. El nombre de cuenta que especifique debe contener solo letras minúsculas y números.

		$dataLakeStoreName = "<your new Data Lake Store name>"
    	New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

	![Crear una cuenta de Azure Data Lake](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "Crear una cuenta de Azure Data Lake")

3. Compruebe que la cuenta se creó correctamente.

		Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

	El resultado debe ser **True**.

4. Cargue datos de ejemplo a Azure Data Lake. Los usaremos más adelante en este artículo para comprobar que se puede acceder a los datos desde un clúster de HDInsight. Si busca datos de ejemplo para cargar, puede obtener la carpeta **Ambulance Data** en el [repositorio Git de Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

		
		$myrootdir = "/"
		Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## Configuración de la autenticación para el acceso basado en roles al Almacén de Data Lake

Cada una de las suscripciones de Azure está asociada a un Azure Active Directory. Los usuarios y los servicios que acceden a los recursos de la suscripción mediante el Portal de Azure clásico o la API del Administrador de recursos de Azure deben autenticarse primero en ese Azure Active Directory. Para conceder acceso a servicios y suscripciones de Azure, se les asigna el rol adecuado en un recurso de Azure. Para los servicios, una entidad de servicio identifica al servicio en Azure Active Directory (AAD). En esta sección, se muestra cómo conceder a un servicio de aplicación, como HDInsight, acceso a un recurso de Azure (la cuenta de Almacén de Azure Data Lake creada antes) mediante la creación de una entidad de servicio para la aplicación y la asignación de roles a ella a través de Azure PowerShell.

Para configurar la autenticación de Active Directory para Azure Data Lake, debe realizar las siguientes tareas.

* Creación de un certificado autofirmado
* Creación de una aplicación en Azure Active Directory y una entidad de servicio

### Creación de un certificado autofirmado

Asegúrese de que tiene [Windows SDK](https://dev.windows.com/es-ES/downloads) instalado antes de continuar con los pasos de esta sección. También debe crear un directorio, como **C:\\mycertdir**, donde se creará el certificado.

1. En la ventana de PowerShell, vaya a la ubicación donde instaló Windows SDK (normalmente, `C:\Program Files (x86)\Windows Kits\10\bin\x86`) y use la utilidad [MakeCert][makecert] para crear un certificado autofirmado y una clave privada. Use los comandos siguientes.

		$certificateFileDir = "<my certificate directory>"
		cd $certificateFileDir
		$startDate = (Get-Date).ToString('MM/dd/yyyy')
		$endDate = (Get-Date).AddDays(365).ToString('MM/dd/yyyy')

		makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -b $startDate -e $endDate -r -len 2048

	Se le pedirá que escriba la contraseña de la clave privada. Después de que el comando se ejecute correctamente, debería ver los archivos **CertFile.cer** y **mykey.pvk** en el directorio de certificado especificado.

4. Emplee la utilidad [Pvk2Pfx][pvk2pfx] para convertir los archivos .pvk y .cer que MakeCert creó en un archivo .pfx. Ejecute el siguiente comando.

		pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

	Cuando se le pida, escriba la contraseña de la clave privada que especificó antes. El valor especificado para el parámetro **-po** es la contraseña asociada al archivo .pfx. Cuando el comando se complete correctamente, debería ver un archivo CertFile.pfx en el directorio de certificado especificado.

###  Creación de una aplicación en Azure Active Directory y una entidad de servicio

En esta sección, llevará a cabo los pasos para crear una entidad de servicio para una aplicación de Azure Active Directory, asignar un rol a la entidad de servicio y autenticarse como la entidad de servicio proporcionando un certificado. Ejecute los comandos siguientes para crear una aplicación en Azure Active Directory.

1. Pegue los siguientes cmdlets en la ventana de consola de PowerShell. Asegúrese de que el valor especificado para la propiedad **- DisplayName** sea único. Además, los valores de **-HomePage** e **-IdentiferUris** son valores de marcador de posición y no se comprueban. 

		$certificateFilePath = "$certificateFileDir\CertFile.pfx"
		
		$password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file
		
		$certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)
		
		$rawCertificateData = $certificatePFX.GetRawCertData()
		
		$credential = [System.Convert]::ToBase64String($rawCertificateData)

		$application = New-AzureRmADApplication `
					-DisplayName "HDIADL" ` 
					-HomePage "https://contoso.com" `
					-IdentifierUris "https://mycontoso.com" `
					-KeyValue $credential  `
					-KeyType "AsymmetricX509Cert"  `
					-KeyUsage "Verify"  `
					-StartDate $startDate  `
					-EndDate $endDate

		$applicationId = $application.ApplicationId

2. Cree una entidad de servicio con el identificador de aplicación.

		$servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId
		
		$objectId = $servicePrincipal.Id

3. Conceda a la entidad de servicio acceso al Almacén de Data Lake que creó antes.
		
		Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All

	En el aviso, escriba **Y** para confirmar.

## Creación de un clúster de HDInsight con autenticación en el Almacén de Data Lake

En esta sección, crearemos un clúster de Hadoop en HDInsight. En esta versión, el clúster de HDInsight y el Almacén de Data Lake deben estar en la misma ubicación (Este de EE. UU. 2).

1. Comience recuperando el identificador del inquilino de la suscripción. Lo necesitará más adelante.

		$tenantID = (Get-AzureRmContext).Tenant.TenantId

2. En esta versión, para un clúster de Hadoop, el Almacén de Data Lake solo sirve como almacenamiento adicional para el clúster. El almacenamiento predeterminado seguirá siendo los blobs de almacenamiento de Azure (WASB). En primer lugar, crearemos la cuenta de almacenamiento y los contenedores de almacenamiento necesarios para el clúster.

		# Create an Azure storage account
		$location = "East US 2"
		$storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
		
		New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS
 
		# Create an Azure Blob Storage container
		$containerName = "<ContainerName>"              # Provide a container name
		$storageAccountKey = Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		New-AzureStorageContainer -Name $containerName -Context $destContext

3. Cree el clúster de HDInsight. Use los siguientes cmdlets.

		# Set these variables
		$clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
		$clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
		$httpCredentials = Get-Credential
		$rdpCredentials = Get-Credential
		
		New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.2" -RdpCredential $rdpCredentials -RdpAccessExpiry (Get-Date).AddDays(14) -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

	Una vez que el cmdlet se complete correctamente, debería ver un resultado similar al siguiente:

		Name                      : hdiadlcluster
		Id                        : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/hdiadlgroup/providers/Mi
		                            crosoft.HDInsight/clusters/hdiadlcluster
		Location                  : East US 2
		ClusterVersion            : 3.2.7.707
		OperatingSystemType       : Windows
		ClusterState              : Running
		ClusterType               : Hadoop
		CoresUsed                 : 16
		HttpEndpoint              : hdiadlcluster.azurehdinsight.net
		Error                     :
		DefaultStorageAccount     :
		DefaultStorageContainer   :
		ResourceGroup             : hdiadlgroup
		AdditionalStorageAccounts : 

## Ejecución de trabajos de prueba en el clúster de HDInsight para usar el Almacén de Data Lake

Después de configurar un clúster de HDInsight, puede ejecutar trabajos de prueba en el clúster para probar que el clúster de HDInsight pueda acceder al Almacén de Data Lake. Para hacerlo, ejecutaremos un trabajo de Hive de ejemplo que crea una tabla con los datos de ejemplo que cargó antes en el Almacén de Data Lake.

### En un clúster de Linux

En esta sección, se usará SSH en el clúster y se ejecutarán una consulta de Hive de ejemplo. Windows no proporciona ningún cliente SSH integrado. Se recomienda usar **PuTTY**, que se puede descargar en [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obtener más información sobre el uso de PuTTY, consulte [Uso de SSH con Hadoop basado en Linux en HDInsight desde Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Una vez conectado, inicie la CLI de Hive con el siguiente comando:

    	hive

2. Mediante la CLI, especifique las siguientes instrucciones para crear una tabla denominada **vehicles** con los datos de ejemplo del Almacén de Data Lake:

		DROP TABLE vehicles;
		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
		SELECT * FROM vehicles LIMIT 10;

	Debería ver una salida similar a la siguiente:

		1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
		1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
		1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
		1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
		1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
		1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
		1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
		1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
		1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
		1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1


### En un clúster de Windows

Use los siguientes cmdlets para ejecutar la consulta de Hive. En esta consulta se crea una tabla con los datos del Almacén de Data Lake y después se ejecuta una consulta Select en la tabla creada.

	$queryString = "DROP TABLE vehicles;" + "CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://$dataLakeStoreName.azuredatalakestore.net:443/';" + "SELECT * FROM vehicles LIMIT 10;"
	
	$hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString

	$hiveJob = Start-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $httpCredentials

	Wait-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $httpCredentials

El resultado será el siguiente. El valor 0 de **ExitValue** en el resultado indica que el trabajo se completó correctamente.

	Cluster         : hdiadlcluster.
	HttpEndpoint    : hdiadlcluster.azurehdinsight.net
	State           : SUCCEEDED
	JobId           : job_1445386885331_0012
	ParentId        :
	PercentComplete :
	ExitValue       : 0
	User            : admin
	Callback        :
	Completed       : done

Recupere el resultado del trabajo mediante el siguiente cmdlet:

	Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $hiveJob.JobId -DefaultContainer $containerName -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -ClusterCredential $httpCredentials

El resultado del trabajo es similar a lo siguiente:

	1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
	1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
	1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
	1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
	1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
	1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
	1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
	1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
	1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
	1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1


## Acceso al Almacén de Data Lake mediante comandos de HDFS

Una vez que configure el clúster de HDInsight para que use el Almacén de Data Lake, puede usar los comandos de shell de HDFS para acceder al almacén.

### En un clúster de Linux

En esta sección, se usará SSH en el clúster y se ejecutarán los comandos de HDFS. Windows no proporciona ningún cliente SSH integrado. Se recomienda usar **PuTTY**, que se puede descargar en [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obtener más información sobre el uso de PuTTY, consulte [Uso de SSH con Hadoop basado en Linux en HDInsight desde Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Una vez conectado, utilice el siguiente comando del sistema de archivos HDFS para enumerar los archivos del Almacén de Data Lake.

	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

Se debería incluir el archivo que cargó antes en el Almacén de Data Lake.

	15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
	Found 1 items
	-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

También puede usar el comando `hdfs dfs -put` para cargar algunos archivos en el almacén de Data Lake y después usar `hdfs dfs -ls` para comprobar si los archivos se cargaron correctamente.


### En un clúster de Windows

1. Inicie sesión en el nuevo [Portal de Azure](https://portal.azure.com).

2. Haga clic en **Examinar**, en **Clústeres de HDInsight** y después en el clúster de HDInsight que creó.

3. En la hoja del clúster, haga clic en **Escritorio remoto** y después, en la hoja **Escritorio remoto**, haga clic en **Conectar**.

	![Conexión remota con un clúster de HDI](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.HDI.PS.Remote.Desktop.png "Crear un grupo de recursos de Azure")

	Cuando se le solicite, escriba las credenciales que proporcionó para el usuario de Escritorio remoto.

4. En la sesión remota, inicie Windows PowerShell y use los comandos del sistema de archivos de HDFS para enumerar los archivos en el Almacén de Azure Data Lake.

	 	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

	Se debería incluir el archivo que cargó antes en el Almacén de Data Lake.

		15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
		Found 1 items
		-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/vehicle1_09142014.csv

	También puede usar el comando `hdfs dfs -put` para cargar algunos archivos en el almacén de Data Lake y después usar `hdfs dfs -ls` para comprobar si los archivos se cargaron correctamente.

## Otras referencias

* [Aprovisionamiento de un clúster de HDInsight con el Almacén de Data Lake mediante el Portal de vista previa de Azure](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=AcomDC_0128_2016-->