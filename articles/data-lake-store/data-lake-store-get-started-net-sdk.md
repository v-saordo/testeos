<properties
   pageTitle="Uso de SDK de .NET del Almacén de Data Lake para desarrollar aplicaciones | Azure"
   description="Uso de SDK de .NET del Almacén de Azure Data Lake para desarrollar aplicaciones"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/07/2016"
   ms.author="nitinme"/>

# Introducción al Almacén de Azure Data Lake mediante SDK de .NET

> [AZURE.SELECTOR]
- [Uso del Portal](data-lake-store-get-started-portal.md)
- [Uso de PowerShell](data-lake-store-get-started-powershell.md)
- [Uso del SDK de .NET](data-lake-store-get-started-net-sdk.md)
- [Uso de la CLI de Azure](data-lake-store-get-started-cli.md)
- [Uso de Node.js](data-lake-store-manage-use-nodejs.md)

Aprenda a utilizar el SDK de .NET del Almacén de Azure Data Lake para crear una cuenta de Azure Data Lake y realizar operaciones básicas como crear carpetas, cargar y descargar archivos de datos, eliminar la cuenta, etc. Para obtener más información sobre Data Lake, consulte [Almacén de Azure Data Lake](data-lake-store-overview.md).

## Requisitos previos

* Visual Studio 2013 o 2015 Las instrucciones siguientes usan Visual Studio 2015.
* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Habilite su suscripción de Azure** para la versión de vista previa pública del Almacén de Data Lake. Consulte las [instrucciones](data-lake-store-get-started-portal.md#signup).
* Cree una aplicación de Azure Active Directory (AAD) y recupere el **Id. de cliente** y el **URI de respuesta**. Para más información acerca de las aplicaciones de AAD y de las instrucciones sobre cómo obtener un identificador de cliente, consulte [Creación de aplicación de Active Directory y entidad de servicio mediante el portal](../resource-group-create-service-principal-portal.md). El identificador URI de respuesta también estará disponible desde el portal una vez que la aplicación se haya creado.

## ¿Cómo se puede autenticar mediante Azure Active Directory?

El siguiente fragmento de código proporciona dos métodos de autenticación:

* **Interactivo**, en el que un usuario inicia sesión mediante la aplicación. Esto se implementa en el método `AuthenticateUser` en el fragmento de código que aparece a continuación.

* **No interactivo**, en el que la aplicación proporciona sus propias credenciales. Esto se implementa en el método `AuthenticateAppliaction` en el fragmento de código que aparece a continuación.

Aunque el fragmento de código siguiente proporciona los métodos para ambos enfoques, este artículo usa el método `AuthenticateUser`. Este método requiere que proporcione el identificador de cliente de la aplicación de AAD y el identificador URI de respuesta. El vínculo en el requisito previo proporciona instrucciones sobre cómo obtenerlos.

>[AZURE.NOTE] Si desea modificar el fragmento de código y usar el método `AuthenticateApplication` en su lugar, debe proporcionar también la clave de autenticación de cliente, además del identificador de cliente y del identificador URI de respuesta como entradas para el método. El artículo [Creación de aplicación de Active Directory y entidad de servicio mediante el portal](../resource-group-create-service-principal-portal.md) también proporciona información sobre cómo generar y recuperar la clave de autenticación de cliente.



## Creación de una aplicación .NET

1. Abra Visual Studio y cree una aplicación de consola.

2. En el menú **Archivo**, haga clic en **Nuevo** y, después, en **Proyecto**.

3. En **Nuevo proyecto**, escriba o seleccione los siguientes valores:

	| Propiedad | Valor |
	|----------|-----------------------------|
	| Categoría | Plantillas/Visual C#/Windows |
	| Plantilla | Aplicación de consola |
	| Nombre | CreateADLApplication |

4. Haga clic en **Aceptar** para crear el proyecto.

5. Agregue los paquetes de Nuget al proyecto.

	1. Haga clic con el botón derecho en el Explorador de soluciones y haga clic en **Administrar paquetes de NuGet**.
	2. En la pestaña **Administrador de paquetes de NuGet**, asegúrese de que la opción **Origen del paquete** está establecida como **nuget.org** y que la casilla **Incluir versión preliminar** está activada.
	3. Busque e instale los siguientes paquetes del Almacén de Data Lake:

		* `Microsoft.Azure.Management.DataLake.Store`
		* `Microsoft.Azure.Management.DataLake.StoreUploader`

		![Agregue un origen de Nuget](./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "Crear una nueva cuenta de Azure Data Lake")

	4. Instale también el paquete `Microsoft.IdentityModel.Clients.ActiveDirectory` para la autenticación de Azure Active Directory. No olvide *desactivar* la casilla **Incluir versión preliminar** para instalar una versión estable de este paquete.

		![Agregue un origen de Nuget](./media/data-lake-store-get-started-net-sdk/adl.install.azure.auth.png "Creación de una nueva cuenta de Azure Data Lake")


	5. Cierre el **Administrador de paquetes Nuget**.

7. Abra el archivo **Program.cs** y sustituya el bloque de código existente por el siguiente. Además, proporcione los valores de los parámetros que se mencionan en el fragmento de código, por ejemplo, **\_adlsAccountName**, **\_resourceGroupName** y reemplace los marcadores de posición para **APPLICATION-CLIENT-ID**, **APPLICATION-REPLY-URI** y **SUBSCRIPTION-ID**.

	Este código recorre el proceso de creación de una cuenta de Almacén de Data Lake, de creación de carpetas en el almacén, de la carga de archivos, de la descarga de archivos y finalmente de la eliminación de la cuenta. Si busca datos de ejemplo para cargar, puede obtener la carpeta **Ambulance Data** en el [repositorio Git de Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

        using System;
        using System.IO;
        using System.Security;
        using System.Text;
        using System.Collections.Generic;
        using System.Linq;

        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name for a NEW Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value. This resource group should already exist.
                    _location = "East US 2";

                    string localFolderPath = @"C:\local_path"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = @"C:\local_path\file.txt"; // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = remoteFolderPath + "file.txt";

                    // Authenticate the user
                    var tokenCreds = AuthenticateUser("common", "https://management.core.windows.net/",
                        "<APPLICATION-CLIENT-ID>", new Uri("https://<APPLICATION-REPLY-URI>")); // TODO: Replace bracketed values.

                    SetupClients(tokenCreds, "<SUBSCRIPTION-ID>"); // TODO: Replace bracketed value.

                    // Run sample scenarios
                    WaitForNewline("Authenticated.", "Creating NEW account.");
                    CreateAccount();
                    WaitForNewline("Account created.", "Creating a directory.");

                    // Create a directory in the Data Lake Store
                    CreateDirectory(remoteFolderPath);
                    WaitForNewline("Directory created.", "Showing directory info.");

                    // Get info about the directory in the Data Lake Store
                    var itemInfo = GetItemInfo(remoteFolderPath);
                    Console.WriteLine("Type: " + itemInfo.Type);
                    Console.WriteLine("Last modified (UTC): " +
                                      new DateTime(1970, 1, 1, 0, 0, 0, 0, DateTimeKind.Utc).AddMilliseconds(
                                          itemInfo.ModificationTime.Value));
                    WaitForNewline("Directory info shown.", "Uploading a file.");

                    // Upload a file to the Data Lake Store
                    UploadFile(localFilePath, remoteFilePath);
                    WaitForNewline("File uploaded.", "Listing files and directories.");

                    // List the files in the Data Lake Store
                    var itemList = ListItems(remoteFolderPath);
                    var fileMenuItems = itemList.Select(a => String.Format("{0,15} {1}", a.Type, a.PathSuffix));
                    Console.WriteLine(String.Join("\r\n", fileMenuItems));
                    WaitForNewline("Files and directories listed.", "Appending content to a file.");

                    // Append to a file in the Data Lake Store
                    AppendToFile(remoteFilePath, "123");
                    WaitForNewline("Content appended.", "Downloading a file.");

                    // Download a file from the Data Lake Store
                    DownloadFile(remoteFilePath, localFilePath);
                    WaitForNewline("File downloaded.", "Deleting account.");

                    // Delete account
                    DeleteAccount();
                    WaitForNewline("Account deleted. You can now exit.");
                }

                // Helper function to show status and wait for user input
                public static void WaitForNewline(string reason, string nextAction = "")
                {
                    if (!String.IsNullOrWhiteSpace(nextAction))
                    {
                        Console.WriteLine(reason + "\r\nPress ENTER to continue...");
                        Console.ReadLine();
                        Console.WriteLine(nextAction);
                    }
                    else
                    {
                        Console.WriteLine(reason + "\r\nPress ENTER to continue...");
                        Console.ReadLine();
                    }
                }

                // Authenticate the user with AAD through an interactive popup.
                // You need to have an application registered with AAD in order to authenticate.
                //   For more information and instructions on how to register your application with AAD, see:
                //   https://azure.microsoft.com/es-ES/documentation/articles/resource-group-create-service-principal-portal/
                public static TokenCredentials AuthenticateUser(string tenantId, string resource, string appClientId, Uri appRedirectUri, string userId = "")
                {
                    var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);

                    var tokenAuthResult = authContext.AcquireToken(resource, appClientId, appRedirectUri,
                        PromptBehavior.Auto, UserIdentifier.AnyUser);

                    return new TokenCredentials(tokenAuthResult.AccessToken);
                }

                // Authenticate the application with AAD through the application's secret key.
                // You need to have an application registered with AAD in order to authenticate.
                //   For more information and instructions on how to register your application with AAD, see:
                //   https://azure.microsoft.com/es-ES/documentation/articles/resource-group-create-service-principal-portal/
                public static TokenCredentials AuthenticateApplication(string tenantId, string resource, string appClientId, Uri appRedirectUri, SecureString clientSecret)
                {
                    var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);
                    var credential = new ClientCredential(appClientId, clientSecret);

                    var tokenAuthResult = authContext.AcquireToken(resource, credential);

                    return new TokenCredentials(tokenAuthResult.AccessToken);
                }

                //Set up clients
                public static void SetupClients(TokenCredentials tokenCreds, string subscriptionId)
                {
                    _adlsClient = new DataLakeStoreAccountManagementClient(tokenCreds);
                    _adlsClient.SubscriptionId = subscriptionId;

                    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
                    _adlsFileSystemClient.SubscriptionId = subscriptionId;
                }

                // Create account
                public static void CreateAccount()
                {
                    // Create ADLS account
                    var adlsParameters = new DataLakeStoreAccount(location: _location);
                    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);
                }

                // Delete account
                public static void DeleteAccount()
                {
                    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
                }

                // List all ADLS accounts within the subscription
                public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
                {
                    var response = _adlsClient.Account.List(_adlsAccountName);
                    var accounts = new List<DataLakeStoreAccount>(response);

                    while (response.NextPageLink != null)
                    {
                        response = _adlsClient.Account.ListNext(response.NextPageLink);
                        accounts.AddRange(response);
                    }

                    return accounts;
                }

                // Upload a file
                public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
                {
                    var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
                    var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
                    var uploader = new DataLakeStoreUploader(parameters, frontend);
                    uploader.Execute();
                }

                // Concatenate files
                public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
                {
                    _adlsFileSystemClient.FileSystem.Concat(destFilePath, srcFilePaths, _adlsAccountName);
                }

                // Get file or directory info
                public static FileStatusProperties GetItemInfo(string path)
                {
                    return _adlsFileSystemClient.FileSystem.GetFileStatus(path, _adlsAccountName).FileStatus;
                }

                // List files and directories
                public static List<FileStatusProperties> ListItems(string directoryPath)
                {
                    return _adlsFileSystemClient.FileSystem.ListFileStatus(directoryPath, _adlsAccountName).FileStatuses.FileStatus.ToList();
                }

                // Download file
                public static void DownloadFile(string srcPath, string destPath)
                {
                    var stream = _adlsFileSystemClient.FileSystem.Open(srcPath, _adlsAccountName);
                    var fileStream = new FileStream(destPath, FileMode.Create);

                    stream.CopyTo(fileStream);
                    fileStream.Close();
                    stream.Close();
                }

                // Append to file
                public static void AppendToFile(string path, string content)
                {
                    var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));

                    _adlsFileSystemClient.FileSystem.Append(path, stream, _adlsAccountName);
                }

                // Create a directory
                public static void CreateDirectory(string path)
                {
                    _adlsFileSystemClient.FileSystem.Mkdirs(path, _adlsAccountName);
                }
            }
        }


8. Compile y ejecute la aplicación. Siga las indicaciones para ejecutar y completar la aplicación.

## Otras formas de crear una cuenta de Almacén de Data Lake

- [Introducción al Almacén de Data Lake mediante el Portal](data-lake-store-get-started-portal.md)
- [Introducción al Almacén de Azure Data Lake mediante PowerShell](data-lake-store-get-started-powershell.md)
- [Introducción al Almacén de Data Lake mediante la CLI de Azure](data-lake-store-get-started-cli.md)


## Pasos siguientes

- [Protección de los datos en el Almacén de Data Lake](data-lake-store-secure-data.md)
- [Uso de Análisis de Azure Data Lake con el Almacén de Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Uso de HDInsight de Azure con el Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0309_2016-->