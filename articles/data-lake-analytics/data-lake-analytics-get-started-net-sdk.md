<properties 
   pageTitle="Introducción a Análisis de Azure Data Lake mediante .NET SDK | Azure" 
   description="Aprenda a usar .NET SDK para crear cuentas del Almacén de Data Lake, crear trabajos de Análisis de Data Lake y enviar trabajos escritos en U-SQL." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/14/2016"
   ms.author="edmaca"/>

# Tutorial: Introducción a Análisis de Azure Data Lake mediante .NET SDK

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Aprenda a usar Azure .NET SDK para crear cuentas de Análisis de Azure Data Lake, definir trabajos de Análisis de Data Lake en [U-SQL](data-lake-analytics-u-sql-get-started.md) y enviar trabajos a cuentas de Análisis de Data Lake. Para obtener más información acerca de Análisis de Data Lake, consulte [Información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).

En este tutorial desarrollará una aplicación de consola en C# que contiene un script U-SQL que lee un archivo de valores separados por tabulaciones (TSV) y lo convierte en un archivo de valores separados por comas (CSV). Para recorrer el mismo tutorial con otras herramientas compatibles, haga clic en las pestañas situadas en la parte superior de esta sección.

**Proceso básico de Análisis de Data Lake:**

![Diagrama de flujo de proceso de Análisis de Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-process.png)

1. Cree una cuenta de Análisis de Data Lake.
2. Prepare los datos de origen. Los trabajos de Análisis de Data Lake pueden leer datos desde cuentas del Almacén de Azure Data Lake o desde cuentas de almacenamiento de blobs de Azure.   
3. Desarrolle un script U-SQL.
4. Envíe un trabajo (script U-SQL) a la cuenta de Análisis de Data Lake. El trabajo lee desde el origen de datos, procesa los datos como se indica en el script U-SQL y después guarda la salida en una cuenta de Almacén de Data Lake o de almacenamiento de blobs.

##Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Visual Studio 2015, Visual Studio 2013 Update 4 o Visual Studio 2012 con Visual C++ instalado**.
- **SDK de Microsoft Azure para .NET versión 2.5 o posterior**. Instálelo usando el [instalador de plataforma web](http://www.microsoft.com/web/downloads/platform.aspx).
- **[Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs)**. 
- **Una cuenta de Análisis de Data Lake**. Consulte [Creación de una cuenta de Análisis de Azure Data Lake](data-lake-analytics-get-started-portal.md#create_adl_analytics_account).

	Data Lake Tools no permite crear cuentas de Análisis de Data Lake. Por lo tanto, tendrá que crearla mediante el Portal de Azure, Azure PowerShell, SDK para .NET o CLI de Azure.

##Creación de una aplicación de consola

En este tutorial, va a procesar algunos registros de búsqueda. El registro de búsqueda se puede almacenar en el Almacén de Data Lake o en el almacenamiento de blobs de Azure.

Se ha copiado un registro de búsqueda de ejemplo en un contenedor de Blob de Azure público. En la aplicación, descargará el archivo en la estación de trabajo y, a continuación, lo cargará en la cuenta predeterminada del Almacén de Data Lake.

**Para crear una aplicación**

1. Abra Visual Studio.
2. Cree una aplicación de consola en C#
3. Abra la consola de administración de paquetes NuGet y ejecute el siguiente comando:

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
        Install-Package Microsoft.Azure.Management.DataLake.AnalyticsCatalog -Pre
        Install-Package Microsoft.Azure.Management.DataLake.AnalyticsJob -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreFileSystem -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
        Install-Package WindowsAzure.Storage

4. Agregue un nuevo archivo al proyecto llamado **SampleUSQLScript.txt** y después pegue el siguiente script U-SQL. Los trabajos de Análisis de Data Lake se escriben en el lenguaje U-SQL. Para obtener más información sobre U-SQL, consulte [Introducción al lenguaje U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348) y [Referencia sobre el lenguaje U-SQL](data-lake-analytics-u-sql-get-started.md).

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	Este script U-SQL lee el archivo de datos de origen mediante **Extractors.Tsv()** y después crea un archivo csv mediante **Outputters.Csv()**.
    
    En el programa en C#, deberá preparar el archivo **/Samples/Data/SearchLog.tsv** y la carpeta **/Output/**.
	
	Es más fácil usar rutas de acceso relativas para los archivos almacenados en cuentas predeterminadas de Data Lake. También puede usar rutas de acceso absolutas. Por ejemplo:
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Debe usar rutas de acceso absolutas para acceder a los archivos de cuentas de almacenamiento vinculadas. La sintaxis de los archivos almacenados en la cuenta de Almacenamiento de Azure vinculada es:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE]Los contenedores de blobs de Azure con permisos de acceso de contenedores públicos o blobs públicos no se admiten actualmente.
       
       
5. En Program.cs, pegue el siguiente código:

        using System;
        using System.Security;
        using System.IO;
        
        using Microsoft.Azure;
        using Microsoft.Azure.Common.Authentication;
        using Microsoft.Azure.Common.Authentication.Models;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.Azure.Management.DataLake.StoreFileSystem;
        using Microsoft.Azure.Management.DataLake.StoreFileSystem.Models;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
        using Microsoft.Azure.Common.Authentication.Factories;
        
        using Microsoft.Azure.Management.DataLake.Analytics;
        using Microsoft.Azure.Management.DataLake.Analytics.Models;
        using Microsoft.Azure.Management.DataLake.AnalyticsJob;
        using Microsoft.Azure.Management.DataLake.AnalyticsJob.Models;
        
        using Microsoft.WindowsAzure.Storage.Blob;
        
        namespace data_lake_analytics_get_started
        {
            class Program
            {
        
                private const string AzureSubscriptionID = "<Your Azure Subscription ID>";
        
                private const string ResourceGroupName = "<Existing Azure Resource Group Name>"; //See the Get started using portal article
                private const string Location = "<Azure Data Center>";  //For example, EAST US 2
                private const string DataLakeStoreAccountName = "<Data Lake Store Account Name>"; // The application will create this account.
                private const string DataLakeAnalyticsAccountName = "<Data Lake Analytics Account Name>"; //The application will create this account.
        
                private const string LocalFolder = @"C:\tutorials\downloads";  //local folder with write permission for file transferring.
        
                private static DataLakeStoreManagementClient _dataLakeStoreClient;
                private static DataLakeStoreFileSystemManagementClient _dataLakeStoreFileSystemClient;
        
                private static DataLakeAnalyticsManagementClient _dataLakeAnalyticsClient;
                private static DataLakeAnalyticsJobManagementClient _dataLakeAnalyticsJobClient;
        
                static void Main(string[] args)
                {
                    var subscriptionId = new Guid(AzureSubscriptionID);
                    var _credentials = GetAccessToken();
        
                    _credentials = GetCloudCredentials(_credentials, subscriptionId);
                    _dataLakeStoreClient = new DataLakeStoreManagementClient(_credentials);
                    _dataLakeStoreFileSystemClient = new DataLakeStoreFileSystemManagementClient(_credentials);
                    _dataLakeAnalyticsClient = new DataLakeAnalyticsManagementClient(_credentials);
                    _dataLakeAnalyticsJobClient = new DataLakeAnalyticsJobManagementClient(_credentials);
        
                    //=========================
                    Console.WriteLine("Creating the Azure Data Lake Store account ...");
                    var storeAccountParameters = new DataLakeStoreAccountCreateOrUpdateParameters();
                    storeAccountParameters.DataLakeStoreAccount = new Microsoft.Azure.Management.DataLake.Store.Models.DataLakeStoreAccount
                    {
                        Name = DataLakeStoreAccountName,
                        Location = Location
                    };
                    _dataLakeStoreClient.DataLakeStoreAccount.Create(ResourceGroupName, storeAccountParameters);
        
                    //=========================
                    Console.WriteLine("Preparing the source data file ...");
        
                    // Download the source file from a public Azure Blob container.
                    CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://adltutorials.blob.core.windows.net/adls-sample-data/SearchLog.tsv"));
                    blob.DownloadToFile(LocalFolder + "SearchLog.tsv", System.IO.FileMode.Create);
        
                    // Create a folder in the default Data Lake Store account.
                    _dataLakeStoreFileSystemClient.FileSystem.Mkdirs("/Samples/Data/", DataLakeStoreAccountName, "777");
        
                    // Upload the source file to the default Data Lake Store account
                    var uploadParameters = new UploadParameters(LocalFolder + "SearchLog.tsv", "/Samples/Data/SearchLog.tsv", DataLakeStoreAccountName, isOverwrite: true);
                    var uploader = new DataLakeStoreUploader(uploadParameters, new DataLakeStoreFrontEndAdapter(DataLakeStoreAccountName, _dataLakeStoreFileSystemClient));
                    uploader.Execute();
        
                    //=========================
                    Console.WriteLine("Creating the Data Lake Analytics account ...");
                    var analyticsAccountParameters = new DataLakeAnalyticsAccountCreateOrUpdateParameters();
                    analyticsAccountParameters.DataLakeAnalyticsAccount = new DataLakeAnalyticsAccount
                    {
                        Name = DataLakeAnalyticsAccountName,
                        Location = Location,
                        Properties = new DataLakeAnalyticsAccountProperties()
                    };
        
                    //create a DataLakeStoreAccount object, add it to the analytics client and then set it as the default ADL Store account
                    Microsoft.Azure.Management.DataLake.Analytics.Models.DataLakeStoreAccount storeAccountObject = new Microsoft.Azure.Management.DataLake.Analytics.Models.DataLakeStoreAccount();
                    storeAccountObject.Name = DataLakeStoreAccountName;
                    analyticsAccountParameters.DataLakeAnalyticsAccount.Properties.DataLakeStoreAccounts.Add(storeAccountObject);
                    analyticsAccountParameters.DataLakeAnalyticsAccount.Properties.DefaultDataLakeStoreAccount = DataLakeStoreAccountName;
        
                    _dataLakeAnalyticsClient.DataLakeAnalyticsAccount.Create(ResourceGroupName, analyticsAccountParameters);
        
                    //=========================
                    Console.WriteLine("Submitting the job ...");
        
                    USqlProperties uSQLProperties = new USqlProperties
                    {
                        Type = JobType.USql,
                        Script = File.ReadAllText("SampleUSQLScript.txt")
                    };
        
                    JobInformation jobParameters = new JobInformation
                    {
                        JobId = Guid.NewGuid(),
                        Name = "myfirstdatalakeanalyticsjob",
                        Properties = uSQLProperties,
                        Type = JobType.USql,
                        DegreeOfParallelism = 1,
                        Priority = 1
                    };
        
                    _dataLakeAnalyticsJobClient.Jobs.Create(ResourceGroupName, DataLakeAnalyticsAccountName, new JobInfoBuildOrCreateParameters { Job = jobParameters });
        
                    Console.WriteLine(" Downloading results ...");
                    FileCreateOpenAndAppendResponse beginOpenResponse = _dataLakeStoreFileSystemClient.FileSystem.BeginOpen("/Output/SearchLog-from-Data-Lake.csv", DataLakeStoreAccountName, new FileOpenParameters());
                    FileOpenResponse openResponse = _dataLakeStoreFileSystemClient.FileSystem.Open(beginOpenResponse.Location);
                    System.IO.File.WriteAllBytes(LocalFolder + "SearchLog-from-Data-Lake.csv", openResponse.FileContents);
        
                    Console.WriteLine("Done");
                }
        
                public static SubscriptionCloudCredentials GetAccessToken(string username = null, SecureString password = null)
                {
                    var authFactory = new AuthenticationFactory();
        
                    var account = new AzureAccount { Type = AzureAccount.AccountType.User };
        
                    if (username != null && password != null)
                        account.Id = username;
        
                    var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                    return new TokenCloudCredentials(authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken);
                }
        
                public static SubscriptionCloudCredentials GetCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
                {
                    return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
                }
            }
        }

7. Presione **F5** para ejecutar la aplicación.

## Consulte también

- Para ver el mismo tutorial con otras herramientas, haga clic en los selectores de pestañas en la parte superior de la página.
- Para ver una consulta más compleja, consulte [Análisis de registros de sitios web mediante Análisis de Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
- Para empezar a desarrollar aplicaciones con U-SQL, consulte [Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para aprender U-SQL, consulte [Tutorial: Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md) y la página de [referencia sobre el lenguaje U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).
- Para las tareas de administración, consulte [Administración de Análisis de Azure Data Lake mediante el Portal de Azure](data-lake-analytics-manage-use-portal.md).
- Para obtener información general sobre Análisis de Data Lake, consulte [Información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).

<!---HONumber=AcomDC_0121_2016-->