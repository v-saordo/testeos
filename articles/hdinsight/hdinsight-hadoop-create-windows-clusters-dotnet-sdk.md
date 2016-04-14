<properties
   pageTitle="Creación de clústeres de Hadoop basados en Windows en HDInsight con el SDK de .NET | Microsoft Azure"
   	description="Obtenga información acerca de cómo crear clústeres de HDInsight para HDInsight de Azure con el SDK de .NET."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/13/2016"
   ms.author="jgao"/>

# Creación de clústeres de Hadoop basados en Windows en HDInsight con el SDK de .NET

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]


Aprenda a crear clústeres de HDInsight con el SDK. NET. Para consultar otras herramientas y características de creación de clústeres, haga clic en la selección de pestaña de la parte superior de esta página o consulte los [métodos de creación de clústeres](hdinsight-provision-clusters.md#cluster-creation-methods).


###Requisitos previos:

Antes de empezar las instrucciones de este artículo, debe tener lo siguiente:

- Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Visual Studio 2013 o 2015

## Creación de clústeres
El SDK .NET de HDInsight proporciona bibliotecas de cliente .NET que facilitan el trabajo con HDInsight desde una aplicación .NET Framework. Siga las instrucciones siguientes para crear una aplicación de consola de Visual Studio y pegar el código para crear un clúster.

La aplicación requiere un grupo de recursos de Azure y la cuenta de almacenamiento predeterminada. En el [Anexo A](#appx-a-create-dependent-components) se proporciona un script de PowerShell para crear los componentes dependientes.

**Para crear una aplicación de consola de Visual Studio**

1. Cree una aplicación de consola en C# mediante Visual Studio.
2. Ejecute el siguiente comando Nuget en la Consola del administrador de paquetes NuGet:

		Install-Package Microsoft.Azure.Common.Authentication -Pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Management.Resources -Pre

6. En el Explorador de soluciones, haga doble clic en **Program.cs** para abrirlo, pegue el siguiente código y especifique valores para las variables:

		using System;
		using System.Security;
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;
		using Microsoft.Azure.Management.Resources;
		
		namespace CreateHDInsightCluster
		{
			class Program
			{
				private static HDInsightManagementClient _hdiManagementClient;
		
				private static Guid SubscriptionId = new Guid("<Azure Subscription ID>");
				private const string ExistingResourceGroupName = "<Azure Resource Group Name>";
				private const string ExistingStorageName = "<Default Storage Account Name>.blob.core.windows.net";
				private const string ExistingStorageKey = "<Default Storage Account Key>";
				private const string ExistingBlobContainer = "<Default Blob Container Name>";
				private const string NewClusterName = "<HDInsight Cluster Name>";
				private const int NewClusterNumNodes = 1;
				private const string NewClusterLocation = "EAST US 2";     // Must be the same as the default Storage account
				private const OSType NewClusterOsType = OSType.Windows;
				private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
				private const string NewClusterVersion = "3.2";
				private const string NewClusterUsername = "admin";
				private const string NewClusterPassword = "<HTTP User password>";
		
				static void Main(string[] args)
				{
					System.Console.WriteLine("Creating a cluster.  The process takes 10 to 20 minutes ...");
		
					var tokenCreds = GetTokenCloudCredentials();
					var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
					
					var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
					resourceManagementClient.Providers.Register("Microsoft.HDInsight");
					
					_hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
				
					var parameters = new ClusterCreateParameters
					{
						ClusterSizeInNodes = NewClusterNumNodes,
						UserName = NewClusterUsername,
						Password = NewClusterPassword,
						Location = NewClusterLocation,
						DefaultStorageAccountName = ExistingStorageName,
						DefaultStorageAccountKey = ExistingStorageKey,
						DefaultStorageContainer = ExistingBlobContainer,
						ClusterType = NewClusterType,
						OSType = NewClusterOsType
					};
		
					_hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, parameters);

                    System.Console.WriteLine("The cluster has been created. Press ENTER to continue ...");
                    System.Console.ReadLine();
                    
				}

				public static TokenCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
				{
					var authFactory = new AuthenticationFactory();
		
					var account = new AzureAccount { Type = AzureAccount.AccountType.User };
		
					if (username != null && password != null)
						account.Id = username;
		
					var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
		
					var accessToken =
						authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
							.AccessToken;
		
					return new TokenCloudCredentials(accessToken);
				}
		
				public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(TokenCloudCredentials creds, Guid subId)
				{
					return new TokenCloudCredentials(subId.ToString(), creds.Token);
		
				}
			}
		}

7. Presione **F5** para ejecutar la aplicación. Una ventana de consola se abrirá y mostrará el estado de la aplicación. También se le pedirá que escriba las credenciales de la cuenta de Azure. La creación del clúster de HDInsight puede durar varios minutos.



##Pasos siguientes
En este artículo, ha aprendido varias maneras de crear un clúster de HDInsight. Para obtener más información, consulte los artículos siguientes:

* [Introducción a HDInsight de Azure](hdinsight-hadoop-linux-tutorial-get-started.md): aprenda a empezar a trabajar con su clúster de HDInsight
* [Envío de trabajos de Hadoop mediante programación](hdinsight-submit-hadoop-jobs-programmatically.md): aprenda a enviar trabajos a HDInsight mediante programación
* [Documentación del SDK de HDInsight de Azure][hdinsight-sdk-documentation]\: descubra el SDK de HDInsight


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[ssisclustercreate]: http://msdn.microsoft.com/library/mt146774(v=sql.120).aspx
[ssisclusterdelete]: http://msdn.microsoft.com/library/mt146778(v=sql.120).aspx


##Anexo A: creación de componentes dependientes

Se puede usar el siguiente script de Azure PowerShell para crear los componentes dependientes necesarios para la aplicación de .NET en este tutorial.

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<Enter an Alias>" 
    #endregion

    ####################################
    # Service names and varialbes
    ####################################
    #region - service names
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    $location = "East US 2"
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ####################################
    # Connect to Azure
    ####################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - Create an HDInsight cluster
    ####################################
    # Create dependent components
    ####################################
    Write-Host "Creating a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS

    $defaultStorageAccountKey = Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName |  %{ $_.Key1 }
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageContext #use the cluster name as the container name

    Write-Host "Use the following names in your .NET application" -ForegroundColor Green
    Write-host "Resource Group Name: $resourceGroupName"
    Write-host "Default Storage Account Name: $defaultStorageAccountName"
    Write-host "Default Storage Account Key: $defaultStorageAccountKey"
    Write-host "Default Blob Container Name: $defaultBlobContainerName"

<!---HONumber=AcomDC_0302_2016-->