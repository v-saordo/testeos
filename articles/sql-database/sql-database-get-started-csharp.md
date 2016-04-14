<properties 
	pageTitle="Prueba de la Base de datos SQL: uso de C# para crear una Base de datos SQL | Microsoft Azure" 
	description="Pruebe la Base de datos SQL para desarrollar aplicaciones SQL y C# y crear una Base de datos SQL de Azure con C# mediante la biblioteca de Base de datos SQL para. NET." 
	keywords="probar sql,sql c#"   
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="data-management" 
   ms.date="01/22/2016"
   ms.author="sstein"/>

# Prueba de Base de datos SQL: Use C&#x23; para crear una Base de datos SQL con la biblioteca de Base de datos SQL para .NET 

**Base de datos única**

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)



Aprenda a usar los comandos C# para crear una Base de datos SQL de Azure con la [biblioteca de Base de datos SQL de Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql).

Probará la Base de datos SQL mediante la creación de una Base de datos única con SQL y C#. Para crear un grupo de bases de datos elásticas, consulte [Creación de un grupo de bases de datos elásticas](sql-database-elastic-pool-portal.md).

Los fragmentos de código individuales se dividen por motivos de claridad y una aplicación de consola de ejemplo reúne todos los comandos en la sección de la parte inferior de este artículo.

La biblioteca de Base de datos SQL de Azure para .NET ofrece una API basada en el [Administrador de recursos de Azure](../resource-group-overview.md) que encapsula la [API de REST de Base de datos SQL basada en el Administrador de recursos](https://msdn.microsoft.com/library/azure/mt163571.aspx). Esta biblioteca cliente sigue el patrón común para las bibliotecas de cliente basada en el Administrador de recursos. El Administrador de recursos requiere grupos de recursos y la autenticación con [Azure Active Directory](https://msdn.microsoft.com/library/azure/mt168838.aspx) (AAD).

<br>

> [AZURE.NOTE] La biblioteca de bases de datos SQL para .NET está actualmente en vista previa.

<br>

Necesitará lo siguiente para completar los pasos de este artículo:

- Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **PRUEBA GRATUITA** situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.
- Visual Studio. Para obtener una copia gratuita de Visual Studio, consulte la página [Descargas de Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs).


## Instalación de las bibliotecas necesarias

Para configurar una base de datos SQL con C#, obtenga las bibliotecas de administración necesarias, para lo que debe instalar los siguientes paquetes mediante la [consola del Administrador de paquetes](http://docs.nuget.org/Consume/Package-Manager-Console) en Visual Studio (**Herramientas** > **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes**):

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.Resources –Pre
    Install-Package Microsoft.Azure.Common.Authentication –Pre


## Configurar la autenticación con Azure Active Directory

En primer lugar, es preciso habilitar la aplicación cliente para que tenga acceso al servicio Base de datos SQL mediante la configuración de la autenticación requerida.

Para autenticar la aplicación de cliente basada en el usuario actual, primero debe registrar su aplicación en el dominio de AAD asociado a la suscripción en la que se han creado los recursos de Azure. Si se creó su suscripción de Azure con una cuenta de Microsoft en lugar de una cuenta profesional o educativa, ya tendrá un dominio de AAD predeterminado.

Para crear una nueva aplicación y registrarla en el directorio activo correcto, haga lo siguiente:

1. Vaya al [Portal de Azure clásico](https://manage.windowsazure.com/).
1. En el lado izquierdo, seleccione el servicio de **Active Directory** y luego seleccione el directorio para autenticar la aplicación y haga clic en su **nombre**.

    ![Prueba de la Base de datos SQL: configuración de Azure Active Directory (AAD).][1]

2. En la página del directorio, haga clic en **APLICACIONES**.

    ![La página del directorio con aplicaciones.][5]

4. Haga clic en **AGREGAR** para crear una nueva aplicación.

5. Seleccione **Agregar una aplicación que está siendo desarrollada por mi organización**.

5. Asigne un **NOMBRE** a la aplicación y seleccione **APLICACIÓN DE CLIENTE NATIVO**.

    ![Proporcione información sobre la aplicación C# de SQL.][7]

6. Proporcione un **URI DE REDIRECCIÓN**. No tiene que ser un extremo real, simplemente un URI válido.

    ![Agregue una dirección URL de redireccionamiento a la aplicación C# de SQL.][8]

7. Finalice la creación de la aplicación, haga clic en **CONFIGURAR** y copie el **ID. DE CLIENTE** (más adelante necesitará el id. de cliente en el código).

    ![Obtenga un identificador de cliente para la aplicación C# de SQL.][9]


1. En la parte inferior de la página, haga clic en **Agregar aplicación**.
1. Seleccione **Aplicaciones de Microsoft**.
1. Seleccione **API de administración de servicios de Azure** y, después, complete el asistente.
2. Con la API seleccionada, deberá conceder los permisos específicos necesarios para obtener acceso a esta API, seleccionando **Acceder a la administración del servicio de Azure (vista previa)**.

    ![Establezca permisos.][2]

2. Haga clic en **GUARDAR**.



### Identificar el nombre de dominio

Se requiere el nombre de dominio para su código. Para identificar de manera sencilla el nombre de dominio adecuado:

1. Vaya al [Portal de Azure](http://portal.azure.com).
2. Mantenga el puntero sobre su nombre en la esquina superior derecha y anote el dominio que aparece en la ventana emergente.

    ![Identifique el nombre de dominio.][3]





**Recursos de AAD adicionales**

Encontrará información adicional sobre el uso de Azure Active Directory para la autenticación en [esta útil entrada de blog](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/).


### Recuperar el token de acceso para el usuario actual 

La aplicación cliente debe recuperar el token de acceso de la aplicación para el usuario actual. La primera vez que un usuario ejecuta este código se le pedirá que escriba sus credenciales de usuario y el token resultante se almacenará en caché localmente. Las ejecuciones posteriores recuperarán el token de la memoria caché y solo pedirán al usuario que inicie sesión si el token ha expirado.

Este código devuelve una clase Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationResult que necesitará en los restantes fragmentos de código siguientes.

        private static AuthenticationResult GetAccessToken()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    clientId,
            new Uri(redirectUri) /* redirect URI */,
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }



> [AZURE.NOTE] En los ejemplos de este artículo se usa un formulario sincrónico de cada solicitud de API y se bloquea hasta que finaliza la llamada de REST en el servicio subyacente. Hay métodos asincrónicos disponibles.



## Crear un grupo de recursos

Con el Administrador de recursos, todos los recursos se deben crear en un grupo de recursos. Un grupo de recursos es un contenedor que incluye los recursos relacionados de una aplicación. Con Base de datos SQL de Azure el servidor de bases de datos debe crearse en un grupo de recursos existente.

        static void CreateResourceGroup()
        {
            creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);

            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = location,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
        }


## Creación de un servidor 

Las bases de datos SQL se incluye en servidores. El nombre de servidor debe ser único globalmente entre todos los servidores SQL de Azure de manera que obtendrá un error aquí si el nombre del servidor ya existe. También debe tener en cuenta que este comando puede tardar varios minutos en completarse.

    static void CreateServer()
    {
        // Create a SQL Database management client
        SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

        // Create a server
        ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
        {
            Location = location,
            Properties = new ServerCreateOrUpdateProperties()
            {
                AdministratorLogin = administratorLogin,
                AdministratorLoginPassword = administratorPassword,
                Version = serverVersion
            }
        };
            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
    }


### Creación de un administrador de servidores externo


    // Create a server external admin
    ServerAdministratorCreateOrUpdateParameters aadAdminParameters = new ServerAdministratorCreateOrUpdateParameters()
    {
        Properties = new ServerAdministratorCreateOrUpdateProperties()
        {
            AdministratorType = "ActiveDirectory",
            Login = "<login>",
            Sid = new Guid("<Azure AD admin sid>"),
            TenantId = new Guid("<Azure AD tenant id>")
        }
    };

    var adminResult = sqlClient.ServerAdministrators.CreateOrUpdate(resourceGroupName, serverName, "ActiveDirectory", aadAdminParameters);
    var adminGetResult = sqlClient.ServerAdministrators.Get(resourceGroupName, serverName, "ActiveDirectory");




## Creación de una regla de firewall para permitir el acceso al servidor

De forma predeterminada, no se puede conectar a un servidor desde cualquier ubicación. Para conectarse a un servidor o a cualquier base de datos del servidor, se debe definir una [regla de firewall](https://msdn.microsoft.com/library/azure/ee621782.aspx) que permita el acceso desde la dirección IP del cliente.

En el ejemplo siguiente se crea una regla que abre el acceso al servidor desde cualquier dirección IP. Se recomienda crear inicios de sesión y contraseñas de SQL adecuados para proteger la base de datos y no depender de reglas de firewall como defensa principal frente a las intrusiones.


        static void CreateFirewallRule()
        {
            // Create a firewall rule on the server 
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };
            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
        }




Para permitir que otros servicios de Azure tengan acceso a un servidor, agregue una regla de firewall y establezca tanto StartIpAddress como EndIpAddress en 0.0.0.0. Tenga en cuenta que esta opción permite que el tráfico de Azure de *cualquier* suscripción de Azure tenga acceso al servidor.


## Uso de C&#x23; para crear una base de datos SQL

El siguiente comando de C# creará una nueva base de datos SQL si no hay una base de datos con el mismo nombre en el servidor; si existe alguna base de datos con el mismo nombre, se actualizará.


        static void CreateDatabase()
        {
            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel,
                }
            };
            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
        }



## Aplicación de consola en C&#x23; de ejemplo

En el ejemplo siguiente se crea un grupo de recursos, un servidor, una regla de firewall y una base de datos SQL. La sección *Configurar la autenticación con Azure Active Directory* del principio de este artículo muestra dónde obtener los valores de las variables clientId, redirectUri y domainName.


    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    
    namespace SqlDbConsoleApp
    {
    class Program
    {
        // Get these values from the Azure portal
        static string subscriptionId = "<Azure subscription id>";
        static string clientId = "<Azure App clientId>";
        static string redirectUri = "<Azure App redirectURI>";
        static string domainName = "<domain>";

        // You create these values 
        static string resourceGroupName = "<your resource group name>";
        static string location = "<Azure data center location>";

        static string serverName = "<your server name>";
        static string administratorLogin = "<your server admin>";
        
        // store your password securely!
        static string administratorPassword = "<your server admin password>";
        static string serverVersion = "12.0";

        static string firewallRuleName = "<your firewall rule name>";

        static string databaseName = "dbfromcsharparticle";
        static string databaseEdition = "Basic"; // Basic, Standard, or Premium
        static string databasePerfLevel = "";

        static AuthenticationResult token;
        static Microsoft.Rest.TokenCredentials creds;

        static SqlManagementClient sqlClient;


        static void Main(string[] args)
        {
            Console.WriteLine("Logging in...");

            token = GetAccessToken();

            Console.WriteLine("Logged in as: " + token.UserInfo.DisplayableId);

            Console.WriteLine("Creating resource group...");
            CreateResourceGroup();

            sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            Console.WriteLine("Creating server...");
            CreateServer();

            Console.WriteLine("Creating firewall rule...");
            CreateFirewallRule();

            Console.WriteLine("Creating database...");

            DatabaseCreateOrUpdateResponse dbResponse = CreateDatabase();
            Console.WriteLine("Status: " + dbResponse.Status.ToString() 
                + " Code: " + dbResponse.StatusCode.ToString());

            Console.WriteLine("Press enter to exit...");
            Console.ReadLine();
        }

        static void CreateResourceGroup()
        {
            creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);

            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = location,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
        }

        static void CreateServer()
        {
            // Create a SQL Database management client
            //SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = location,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = administratorLogin,
                    AdministratorLoginPassword = administratorPassword,
                    Version = serverVersion
                }
            };
            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
        }

        static void CreateFirewallRule()
        {
            // Create a firewall rule on the server 
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    // replace with your client IP address
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };
            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
        }

        static DatabaseCreateOrUpdateResponse CreateDatabase()
        {
            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel,
                }
            };
            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
        }

        private static AuthenticationResult GetAccessToken()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    clientId,
            new Uri(redirectUri) /* redirect URI */,
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }
    }
    }




## Pasos siguientes
Ahora que probó la Base de datos SQL y configuró una base de datos con C#, está listo para los artículos siguientes:

- [Conexión a la Base de datos SQL con SQL Server Management Studio y realización de una consulta de T-SQL de ejemplo](sql-database-connect-query-ssms.md)

## Recursos adicionales

- [Base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)





<!--Image references-->
[1]: ./media/sql-database-get-started-csharp/aad.png
[2]: ./media/sql-database-get-started-csharp/permissions.png
[3]: ./media/sql-database-get-started-csharp/getdomain.png
[4]: ./media/sql-database-get-started-csharp/aad2.png
[5]: ./media/sql-database-get-started-csharp/aad-applications.png
[6]: ./media/sql-database-get-started-csharp/add.png
[7]: ./media/sql-database-get-started-csharp/add-application.png
[8]: ./media/sql-database-get-started-csharp/add-application2.png
[9]: ./media/sql-database-get-started-csharp/clientid.png

<!---HONumber=AcomDC_0302_2016-->