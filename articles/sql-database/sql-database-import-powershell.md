<properties 
    pageTitle="Importar un archivo BACPAC para crear una base de datos SQL de Azure mediante PowerShell | Microsoft Azure" 
    description="Importar un archivo BACPAC para crear una base de datos SQL de Azure mediante PowerShell" 
    services="sql-database" 
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
    ms.date="02/05/2016"
    ms.author="sstein"/>

# Importar un archivo BACPAC para crear una base de datos SQL de Azure mediante PowerShell

**Base de datos única**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)

Este artículo proporciona instrucciones para crear una base de datos SQL de Azure importando un archivo BACPAC con PowerShell.

Un BACPAC es un archivo .bacpac que contiene datos y un esquema de base de datos. Para obtener detalles, vea el paquete de copia de seguridad (.bacpac) en [Aplicaciones del nivel de datos](https://msdn.microsoft.com/library/ee210546.aspx).

La base de datos se crea a partir de un BACPAC importado de un contenedor de blobs de almacenamiento de Azure. Si no dispone de un archivo .bacpac en el almacenamiento de Azure, puede crear uno siguiendo los pasos descritos en [Crear y exportar un archivo BACPAC de una base de datos SQL de Azure](sql-database-export-powershell.md).

> [AZURE.NOTE] La Base de datos SQL de Azure crea y mantiene automáticamente copias de seguridad para cada base de datos de usuario. Para obtener detalles, vea [Información general sobre la continuidad del negocio](sql-database-business-continuity.md).


Para importar una base de datos SQL, necesita lo siguiente:

- Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **PRUEBA GRATUITA** situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.
- Un archivo .bacpac (BACPAC) de la base de datos que quiere importar. El BACPAC debe estar en un contenedor de blobs de [cuenta de almacenamiento de Azure (clásica)](storage-create-storage-account.md).


> [AZURE.IMPORTANT] Este artículo contiene comandos para las versiones de Azure PowerShell, hasta la versión 1.0 (*sin incluir esta ni las posteriores*). Puede comprobar la versión de Azure PowerShell con el comando **Get-Module azure | format-table version**.



## Configuración de las credenciales y selección de la suscripción

En primer lugar debe establecer el acceso a su cuenta de Azure; por tanto, inicie PowerShell y luego ejecute el siguiente cmdlet. En la pantalla de inicio de sesión escriba el mismo correo electrónico y la misma contraseña que usa para iniciar sesión en el portal de Azure.

	Add-AzureAccount

Después de iniciar sesión correctamente, verá información en la pantalla que incluye el identificador con el que ha iniciado sesión y las suscripciones a Azure a las que tiene acceso.


### Selección de su suscripción a Azure

Para seleccionar la suscripción, necesita su id. de suscripción. Puede copiar la identificación de suscripción de la información mostrada en el paso anterior o, si dispone de varias suscripciones y necesita más detalles, puede ejecutar el cmdlet **Get-AzureSubscription** y copiar la información de suscripción que desee del conjunto de resultados. Cuando tenga su id. de suscripción, ejecute el siguiente cmdlet:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Después de ejecutar correctamente **Select-AzureSubscription** volverá al símbolo del sistema de PowerShell. Si tiene más de una suscripción, puede ejecutar **Get-AzureSubscription** y comprobar que la suscripción que ha seleccionado muestra **IsCurrent: True**.


## Configurar las variables para su entorno

Hay algunas variables que necesita para reemplazar los valores de ejemplo con los valores específicos de la base de datos y la cuenta de almacenamiento.

El nombre del servidor debe ser un servidor que exista actualmente en la suscripción seleccionada en el paso anterior y es el servidor en el que quiere crear la base de datos.

El nombre de la base de datos es el nombre que desea para la nueva base de datos.

    $ServerName = "servername"
    $DatabaseName = "databasename"


Las variables siguientes proceden de la cuenta de almacenamiento donde se encuentra su BACPAC. En el [Portal de Azure](https://portal.azure.com), vaya a su cuenta de almacenamiento para obtener estos valores. Para encontrar la clave de acceso principal, vaya a la hoja de su cuenta de almacenamiento y haga clic en **Toda la configuración** y, a continuación, en **Claves**.

El nombre de blob es el nombre de un archivo de .bacpac existente desde el que quiere crear la base de datos. Necesita incluir la extensión .bacpac.

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $BlobName = "filename.bacpac"
    $StorageKey = "primaryaccesskey"

## Crear un puntero a su cuenta de almacenamiento y servidor

Al ejecutar el cmdlet **Get-Credential** se abre una ventana en la que se le pide su nombre de usuario y contraseña. Escriba el inicio de sesión de administrador y la contraseña para el servidor SQL en el que quiere crear la base de datos ($ServerName de arriba), no el nombre de usuario y la contraseña para su cuenta de Azure.

    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential

    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx


## Importar la base de datos

Este comando envía una solicitud de importación de base de datos para el servicio. Según el tamaño de su base de datos, la operación de importación puede tardar algún tiempo en completarse.

    $importRequest = Start-AzureSqlDatabaseImport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    

## Supervisar el progreso de la restauración

Después de ejecutar **Start AzureSqlDatabaseImport**, puede comprobar el estado de la solicitud.

Al comprobar el estado inmediatamente después de realizar la solicitud, se suele devolver un estado **Pendiente** o **En ejecución**; además, se proporcionará un porcentaje actual completo para que pueda ejecutar esta operación varias veces hasta que vea el mensaje **Estado: completado** en la salida.

Al ejecutar este comando se le solicitará una contraseña. Escriba el inicio de sesión de administrador y la contraseña para su SQL server.


    Get-AzureSqlDatabaseImportExportStatus -RequestId $ImportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
 


## Importación de un script de PowerShell de Base de datos SQL


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ServerName = "servername"
    $DatabaseName = "nameofnewdatabase"

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $BlobName = "filename.bacpac"
    $StorageKey = "primaryaccesskey"
    
    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential
    
    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx
    
    $ImportRequest = Start-AzureSqlDatabaseImport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    
    Get-AzureSqlDatabaseImportExportStatus -RequestId $ImportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    

## Pasos siguientes

- [Conexión a la Base de datos SQL con SQL Server Management Studio y realización de una consulta de T-SQL de ejemplo](sql-database-connect-query-ssms.md)




## Recursos adicionales

- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Obtención de detalles de la recuperación ante desastres](sql-database-disaster-recovery-drills.md)
- [Documentación de Base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0211_2016-->