<properties
    pageTitle="Desarrollo de bases de datos de C#: grupos de bases de datos elásticas | Microsoft Azure"
    description="Use técnicas de desarrollo de bases de datos de C# para crear un grupo de bases de datos elásticas de la Base de datos SQL de Azure, para así poder compartir recursos entre una gran cantidad de bases de datos."
    services="sql-database"
    keywords="base de datos de c#, desarrollo de sql"
    documentationCenter=""
    authors="stevestein"
    manager="jeffreyg"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="02/23/2016"
    ms.author="sstein"/>

# C&#x23; desarrollo de bases de datos: crear y configurar un grupo de bases de datos elásticas para una base de datos SQL

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-elastic-pool-csharp.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)


En este artículo se muestra cómo crear un [grupo de bases de datos elásticas](sql-database-elastic-pool.md) para bases de datos SQL desde una aplicación, mediante el uso de técnicas de desarrollo de bases de datos de C#.

> [AZURE.NOTE] Los grupos de bases de datos elásticas están actualmente en vista previa y solo estarán disponibles en servidores con bases de datos SQL V12. Si tiene un servidor de Base de datos SQL V11, puede [usar PowerShell para actualizar a V12 y crear un grupo](sql-database-upgrade-server-powershell.md) en un solo paso.

En los ejemplos usaremos la [Biblioteca de Base de datos SQL de Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql). Los fragmentos de código individuales se dividen por motivos de claridad y una aplicación de consola de ejemplo reúne todos los comandos en la sección de la parte inferior de este artículo.


> [AZURE.NOTE] La biblioteca de bases de datos SQL para .NET está actualmente en vista previa.



Si no tiene una suscripción a Azure, simplemente haga clic en la opción **PRUEBA GRATUITA** situada en la parte superior de esta página y, a continuación, vuelva a este artículo. Asimismo, para obtener una copia gratuita de Visual Studio, consulte la página [Descargas de Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs).

## Instalación de las bibliotecas necesarias

Obtenga las bibliotecas de administración necesarias instalando los siguientes paquetes mediante la [consola del Administrador de paquetes](http://docs.nuget.org/Consume/Package-Manager-Console) para el desarrollo en SQL:

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.Resources –Pre
    Install-Package Microsoft.Azure.Common.Authentication –Pre


## Configurar la autenticación con Azure Active Directory

Antes de comenzar a desarrollar SQL en C#, debe completar algunas tareas en el Portal de Azure. Primero, debe habilitar su aplicación para que tenga acceso a la API de REST configurando la autenticación necesaria.

Las [API de REST del Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx) usan Azure Active Directory para la autenticación, en lugar de los certificados usados por las API de REST de administración de servicios de Azure anteriores.

Para autenticar la aplicación de cliente basada en el usuario actual, primero debe registrar su aplicación en el dominio de AAD asociado a la suscripción en la que se han creado los recursos de Azure. Si se creó su suscripción de Azure con una cuenta de Microsoft en lugar de una cuenta profesional o educativa, ya tendrá un dominio de AAD predeterminado. El registro de la aplicación se puede realizar en el [portal clásico](https://manage.windowsazure.com/).

Para crear una nueva aplicación y registrarla en el directorio activo correcto, haga lo siguiente:

1. Desplace el menú del lado izquierdo para buscar el servicio de **Active Directory** y ábralo.

    ![Desarrollo de bases de datos SQL de C#: programa de instalación de Active Directory][1]

2. Seleccione el directorio para autenticar la aplicación y haga clic en su **Nombre**.

    ![Seleccione un directorio.][4]

3. En la página del directorio, haga clic en **APLICACIONES**.

    ![Haga clic en Aplicaciones.][5]

4. Haga clic en **AGREGAR** para crear una nueva aplicación.

    ![Haga clic en el botón Agregar: crear una aplicación de C#.][6]

5. Seleccione **Agregar una aplicación que está siendo desarrollada por mi organización**.

5. Asigne un **NOMBRE** a la aplicación y seleccione **APLICACIÓN DE CLIENTE NATIVO**.

    ![Agregar aplicación][7]

6. Proporcione un **URI DE REDIRECCIÓN**. No tiene que ser un extremo real, simplemente un URI válido.

    ![Agregar aplicación][8]

7. Finalice la creación de la aplicación, haga clic en **CONFIGURAR** y copie el **ID. DE CLIENTE** (lo necesitará en su código).

    ![Obtener el id. de cliente][9]


1. En la parte inferior de la página, haga clic en **Agregar aplicación**.
1. Seleccione **Aplicaciones de Microsoft**.
1. Seleccione **API de administración de servicios de Azure** y, después, complete el asistente.
2. Con la API seleccionada, deberá conceder los permisos específicos necesarios para obtener acceso a esta API, seleccionando **Acceder a la administración del servicio de Azure (vista previa)**.

    ![Establecer permisos][2]

2. Haga clic en **GUARDAR**.



### Identificar el nombre de dominio

Se requiere el nombre de dominio para su código. Para identificar de manera sencilla el nombre de dominio adecuado:

1. Vaya al [Portal de Azure](https://portal.azure.com).
2. Mantenga el puntero sobre su nombre en la esquina superior derecha y anote el dominio que aparece en la ventana emergente. Reemplace **domain.onmicrosoft.com** en el fragmento de código siguiente por el valor que encontrará en su cuenta.

    ![Identificar nombre de dominio][3]



**Recursos de AAD adicionales**

Encontrará información adicional sobre el uso de Azure Active Directory para la autenticación en [esta útil entrada de blog](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/).


### Recuperar el token de acceso para el usuario actual

La aplicación cliente debe recuperar el token de acceso de la aplicación para el usuario actual. La primera vez que un usuario ejecuta el código se le pedirá que escriba sus credenciales de usuario y el token resultante se almacenará en caché localmente. Las ejecuciones posteriores recuperarán el token de la memoria caché y solo pedirán al usuario que inicie sesión si el token ha expirado.


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

Con el Administrador de recursos, todos los recursos se deben crear en un grupo de recursos. Un grupo de recursos es un contenedor que incluye los recursos relacionados de una aplicación. Para crear un grupo de bases de datos elásticas necesita un servidor de Base de datos SQL de Azure en un grupo de recursos existente. Ejecute el siguiente código de C# para crear el grupo de recursos:


    // Create a resource management client
    ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken ));

    // Resource group parameters
    ResourceGroup resourceGroupParameters = new ResourceGroup()
    {
        Location = "South Central US"
    };

    //Create a resource group
    var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("resourcegroup-name", resourceGroupParameters);



## Creación de un servidor

Los grupos de bases de datos elásticas se encuentran en servidores de Base de datos SQL de Azure; así pues, el paso siguiente consiste en crear un servidor. El nombre de servidor debe ser único globalmente entre todos los servidores SQL de Azure de manera que obtendrá un error aquí si el nombre del servidor ya existe. También debe tener en cuenta que este comando puede tardar varios minutos en completarse. Para permitir que una aplicación pueda conectarse al servidor, también debe crear una regla de firewall en el servidor para así poder abrir el acceso desde la dirección IP del cliente.


    // Create a SQL Database management client
    SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* Subscription id*/, token.AccessToken));

    // Create a server
    ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ServerCreateOrUpdateProperties()
        {
            AdministratorLogin = "ServerAdmin",
            AdministratorLoginPassword = "P@ssword1",
            Version = "12.0"
        }
    };

    var serverResult = sqlClient.Servers.CreateOrUpdate("resourcegroup-name", "server-name", serverParameters);




## Creación de una regla de firewall para permitir el acceso al servidor

De forma predeterminada, un servidor no tiene ninguna regla de firewall, por lo que no se puede conectar con él desde cualquier ubicación. Para conectarse a un servidor o a cualquier base de datos del servidor, debe definir una [regla de firewall](sql-database-firewall-configure.md) que permita el acceso desde la dirección IP del cliente.

En el ejemplo siguiente, crearemos una regla de firewall de servidor que permitirá el acceso al servidor desde cualquier dirección IP. Se recomienda crear inicios de sesión y contraseñas de SQL adecuados para proteger la base de datos y no depender de reglas de firewall como defensa principal frente a las intrusiones. Para obtener más información, consulte [Administrar bases de datos e inicios de sesión en la Base de datos SQL de Azure](sql-database-manage-logins.md).


    // Create a firewall rule on the server to allow TDS connection
    FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
    {
        Properties = new FirewallRuleCreateOrUpdateProperties()
        {
            StartIpAddress = "0.0.0.0",
            EndIpAddress = "255.255.255.255"
        }
    };

    var firewallResult = sqlClient.FirewallRules.CreateOrUpdate("resourcegroup-name", "server-name", "FirewallRule1", firewallParameters);




Para permitir que otros servicios de Azure tengan acceso a un servidor, agregue una regla de firewall y establezca tanto StartIpAddress como EndIpAddress en 0.0.0.0. Tenga en cuenta que esta opción permite que el tráfico de Azure de *cualquier* suscripción de Azure tenga acceso al servidor.


## Creación de una base de datos

En el ejemplo siguiente se crea una nueva base de datos de nivel Básico; si existe una base de datos con el mismo nombre en el servidor, esa base de datos se actualizará.

        // Create a database

        // Retrieve the server on which the database will be created
        Server currentServer = sqlClient.Servers.Get("resourcegroup-name", "server-name").Server;

        // Create a database: configure create or update parameters and properties explicitly
        DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
        {
            Location = currentServer.Location,
            Properties = new DatabaseCreateOrUpdateProperties()
            {
                Edition = "Basic",
                RequestedServiceObjectiveName = "Basic",
                MaxSizeBytes = 2147483648,
                Collation = "SQL_Latin1_General_CP1_CI_AS"
            }
        };

        var dbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", newDatabaseParameters);



## Creación de un grupo de bases de datos elásticas

En el ejemplo siguiente se crea un nuevo grupo de bases de datos elásticas:



    // Create elastic pool: configure create or update parameters and properties explicitly
    ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = "Standard",
            Dtu = 100,  // alternatively set StorageMB, if both are specified they must agree based on the eDTU:storage ratio of the edition
            DatabaseDtuMin = 0,
            DatabaseDtuMax = 100
         }
    };

    // Create the pool
    var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);


## Actualizar un grupo de bases de datos elásticas

El ejemplo siguiente actualiza las características de rendimiento de un grupo de bases de datos elásticas existente:

    // Retrieve existing pool properties
    var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

    // Configure create or update parameters with existing property values, override those to be changed.
    ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = currentPool.Location,
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = currentPool.Properties.Edition,
            DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
            DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
            Dtu = (int)currentPool.Properties.Dtu,
            StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
        }
    };

    newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);



## Movimiento de una base de datos existente a un grupo de bases de datos elásticas

*Después de crear un grupo, también puede usar Transact-SQL para mover bases de datos ya existentes dentro y fuera de un grupo. Para obtener información detallada, consulte [Material de referencia del grupo de bases de datos elásticas: Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL).*

En el ejemplo siguiente se mueve una Base de datos SQL de Azure existente a un grupo:


    // Update database service objective to add the database to a pool

    // Retrieve current database properties
    currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation,
        }
    };

    // Update the database
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);




## Creación de una nueva base de datos en un grupo de bases de datos elásticas

*Después de crear un grupo, también puede usar Transact-SQL para crear nuevas bases de datos elásticas en el grupo. Para obtener información detallada, consulte [Material de referencia del grupo de bases de datos elásticas: Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL).*

En el ejemplo siguiente se crea una nueva base de datos directamente en un grupo:


    // Create a new database in the pool

    // Create a database: configure create or update parameters and properties explicitly
    DatabaseCreateOrUpdateParameters newPooledDatabaseParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentServer.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = 268435456000, // 250 GB,
            Collation = "SQL_Latin1_General_CP1_CI_AS"
        }
    };

    var poolDbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database2", newPooledDatabaseParameters);



## Enumerar todas las bases de datos en un grupo de bases de datos elásticas

En el ejemplo siguiente se enumeran todas las bases de datos de un grupo:

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }




## Aplicación de consola de ejemplo


    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    using System.Security;

    namespace AzureSqlDatabaseRestApiExamples
    {
    class Program
    {
        /// <summary>
        /// Prompts for user credentials when first run or if the cached credentials have expired.
        /// </summary>
        /// <returns>The access token from AAD.</returns>
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

        private static AuthenticationResult GetAccessTokenUsingUserCredentials(UserCredential userCredential)
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + "YOU.onmicrosoft.com" /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* application client ID from AAD*/,
                userCredential);

            return token;
        }
        private static SecureString convertToSecureString(string secret)
        {
            var secureStr = new SecureString();
            if (secret.Length > 0)
            {
                foreach (var c in secret.ToCharArray()) secureStr.AppendChar(c);
            }
            return secureStr;
        }

        static void Main(string[] args)
        {
            var token = GetAccessToken();

            // Who am I?
            Console.WriteLine("Identity is {0} {1}", token.UserInfo.GivenName, token.UserInfo.FamilyName);
            Console.WriteLine("Token expires on {0}", token.ExpiresOn);
            Console.WriteLine("");

            // Create a resource management client
            ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken));

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = "South Central US"
            };

            //Create a resource group
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("resourcegroup-name", resourceGroupParameters);

            Console.WriteLine("Resource group {0} create or update completed with status code {1} ", resourceGroupResult.ResourceGroup.Name, resourceGroupResult.StatusCode);

            //create a SQL Database management client
            TokenCloudCredentials tokenCredentials = new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* Subscription id*/, token.AccessToken);

            SqlManagementClient sqlClient = new SqlManagementClient(tokenCredentials);

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = "South Central US",
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = "ServerAdmin",
                    AdministratorLoginPassword = "P@ssword1",
                    Version = "12.0"
                }
            };

            var serverResult = sqlClient.Servers.CreateOrUpdate("resourcegroup-name", "server-name", serverParameters);

            var serverGetResult = sqlClient.Servers.Get("resourcegroup-name", "server-name");


            Console.WriteLine("Server {0} create or update completed with status code {1}", serverResult.Server.Name, serverResult.StatusCode);

            // Create a firewall rule on the server to allow TDS connection

            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };

            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate("resourcegroup-name", "server-name", "FirewallRule1", firewallParameters);

            Console.WriteLine("Firewall rule {0} create or update completed with status code {1}", firewallResult.FirewallRule.Name, firewallResult.StatusCode);


            var dbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", newDatabaseParameters);

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective {2} ", dbResponse.Database.Name, dbResponse.StatusCode, dbResponse.Database.Properties.ServiceObjective);


           // Create an elastic database pool
            var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

            Console.WriteLine("Elastic pool {0} create or update completed with status code {1}.", newPoolResponse.ElasticPool.Name, newPoolResponse.StatusCode);

            // Update pool: retrieve existing pool properties
            var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

            // Update pool: configure create or update parameters with existing property values, override those to be changed.
            ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = currentPool.Location,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = currentPool.Properties.Edition,
                    DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
                    DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
                    Dtu = (int)currentPool.Properties.Dtu,
                    StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
                }
            };
            newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

            Console.WriteLine("Elastic pool {0} create or update completed with status code {1}.", newPoolResponse.ElasticPool.Name, newPoolResponse.StatusCode);

            // Update a databases service objective to add the database to a pool

            // Update database: retrieve current database properties
            currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

            // Update database: configure create or update parameters with existing property values, override those to be changed.
            DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentDatabase.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = "Standard",
                    RequestedServiceObjectiveName = "ElasticPool",
                    ElasticPoolName = "ElasticPool1",
                    MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
                    Collation = currentDatabase.Properties.Collation,
                }
            };


            // Create a new database in the pool

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newPooledDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = "Standard",
                    RequestedServiceObjectiveName = "ElasticPool",
                    ElasticPoolName = "ElasticPool1",
                    MaxSizeBytes = 268435456000, // 250 GB,
                    Collation = "SQL_Latin1_General_CP1_CI_AS"
                }
            };

            var poolDbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database2", newPooledDatabaseParameters);

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective: {2}({3}) ", poolDbResponse.Database.Name, poolDbResponse.StatusCode, poolDbResponse.Database.Properties.ServiceObjective, poolDbResponse.Database.Properties.ElasticPoolName);
      }
    }







## Recursos adicionales


[Base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)

[API de administración de recursos de Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx)

[Referencias acerca del grupo de bases de datos elásticas](sql-database-elastic-pool-reference.md).


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-csharp/aad.png
[2]: ./media/sql-database-elastic-pool-csharp/permissions.png
[3]: ./media/sql-database-elastic-pool-csharp/getdomain.png
[4]: ./media/sql-database-elastic-pool-csharp/aad2.png
[5]: ./media/sql-database-elastic-pool-csharp/aad-applications.png
[6]: ./media/sql-database-elastic-pool-csharp/add.png
[7]: ./media/sql-database-elastic-pool-csharp/add-application.png
[8]: ./media/sql-database-elastic-pool-csharp/add-application2.png
[9]: ./media/sql-database-elastic-pool-csharp/clientid.png

<!---HONumber=AcomDC_0224_2016-->