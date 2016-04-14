<properties 
    pageTitle="Creación y exportación de un BACPAC de una base de datos SQL de Azure mediante PowerShell" 
    description="Creación y exportación de un BACPAC de una base de datos SQL de Azure mediante PowerShell" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/23/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Creación y exportación de un BACPAC de una base de datos SQL de Azure mediante PowerShell


> [AZURE.SELECTOR]
- [Azure portal](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


Este artículo proporciona instrucciones para exportar un archivo BACPAC de la base de datos SQL de Azure con PowerShell.

Un [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) es un archivo .bacpac que contiene datos y un esquema de base de datos. El caso de uso principal para un BACPAC es mover una base de datos de un servidor a otro, para [migrar una base de datos local a la nube](sql-database-cloud-migrate.md) y para archivar una base de datos existente en un formato abierto.

> [AZURE.NOTE] Los BACPAC no están diseñados para usarse en operaciones de copia de seguridad y restauración. La Base de datos SQL de Azure crea automáticamente copias de seguridad para cada base de datos de usuario. Para obtener detalles, vea [Información general sobre la continuidad del negocio](sql-database-business-continuity.md).


El BACPAC se exporta a un contenedor de blobs de almacenamiento de Azure que puede descargar cuando se completa la operación correctamente.


Para completar este artículo, necesitará lo siguiente:

- Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **CUENTA GRATUITA** situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.
- Una Base de datos SQL de Azure. Si no tiene una base de datos SQL, siga los pasos de este artículo para crear uno: [Creación de la primera Base de datos SQL de Azure](sql-database-get-started.md).
- Una [cuenta de Almacenamiento de Azure](../storage/storage-create-storage-account.md) con un contenedor de blobs para almacenar el BACPAC. Actualmente la cuenta de almacenamiento debe usar el modelo de implementación clásico; por tanto, elija **Clásica** al crear una cuenta de almacenamiento.
- Azure PowerShell. Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).



## Configuración de las credenciales y selección de la suscripción

En primer lugar debe establecer el acceso a su cuenta de Azure; por tanto, inicie PowerShell y luego ejecute el siguiente cmdlet. En la pantalla de inicio de sesión escriba el mismo correo electrónico y la misma contraseña que usa para iniciar sesión en el portal de Azure.

	Add-AzureAccount

Después de iniciar sesión correctamente, verá información en la pantalla que incluye el identificador con el que ha iniciado sesión y las suscripciones a Azure a las que tiene acceso.


### Selección de su suscripción a Azure

Para seleccionar la suscripción, necesitará el nombre o el identificador de suscripción (**-SubscriptionName**). Puede copiar el identificador de suscripción de la información mostrada en el paso anterior o, si dispone de varias suscripciones y necesita más detalles, puede ejecutar el cmdlet **Get-AzureSubscription** y copiar la información de suscripción que desee del conjunto de resultados. Cuando tenga su suscripción, ejecute el siguiente cmdlet:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Después de ejecutar correctamente **Select-AzureSubscription** volverá al símbolo del sistema de PowerShell. Si tiene más de una suscripción, puede ejecutar **Get-AzureSubscription** y comprobar que la suscripción que desea usar muestra **IsCurrent: True**.


## Configuración de las variables para su entorno específico

Hay algunas variables en las que es necesario reemplazar los valores de ejemplo por los valores específicos de la base de datos y la cuenta de almacenamiento.

Reemplace los nombres del servidor y la base de datos por el servidor y la base de datos que existen actualmente en su cuenta. En el nombre del blob, escriba el nombre del archivo BACPAC que se creará. Escriba el nombre que desee para el archivo BACPAC, pero debe incluir la extensión. bacpac.

    $ServerName = "servername"
    $DatabaseName = "nameofdatabasetoexport"
    $BlobName = "filename.bacpac"

En el [Portal de Azure](https://portal.azure.com), vaya a su cuenta de almacenamiento para obtener estos valores. Para encontrar la clave de acceso principal, haga clic en **Toda la configuración** y luego en **Claves** en la hoja de su cuenta de almacenamiento.

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $StorageKey = "primaryaccesskey"

## Creación de un puntero a su cuenta de almacenamiento y servidor

Al ejecutar el cmdlet **Get-Credential**, se abre una ventana en la que se le pide su nombre de usuario y contraseña. Escriba el inicio de sesión de administrador y la contraseña para SQL Server (NO el nombre de usuario y la contraseña para su cuenta de Azure).

    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential

    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx


## Exportación de la base de datos

Este comando envía una solicitud de exportación de base de datos al servicio. Según el tamaño de la base de datos, la operación de exportación puede tardar algún tiempo en completarse.

> [AZURE.IMPORTANT] Para garantizar un archivo BACPAC transaccionalmente coherente, primero debe [crear una copia de la base de datos](sql-database-copy-powershell.md) y después exportar dicha copia.


    $exportRequest = Start-AzureSqlDatabaseExport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    

## Supervisión del progreso de la operación de exportación

Después de ejecutar **Start-AzureSqlDatabaseExport**, puede comprobar el estado de la solicitud. Cuando se ejecuta esto justo después de la solicitud, normalmente devolverá **Estado: Pendiente** o **Estado: En ejecución** para que pueda ejecutarlo varias veces hasta que vea **Estado: Completado** en la salida.

Al ejecutar este comando se le solicitará una contraseña. Escriba la contraseña de administrador de su SQL server.


    Get-AzureSqlDatabaseImportExportStatus -RequestId $exportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    


## Exportación de un script de PowerShell de base de datos SQL


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ServerName = "servername"
    $DatabaseName = "databasename"
    $BlobName = "bacpacfilename"
    
    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $StorageKey = "primaryaccesskey"
    
    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential
    
    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx
    
    $exportRequest = Start-AzureSqlDatabaseExport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    
    Get-AzureSqlDatabaseImportExportStatus -RequestId $exportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    


## Pasos siguientes

- [Importar una base de datos SQL de Azure](sql-database-import-powershell.md)


## Recursos adicionales

- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Obtención de detalles de la recuperación ante desastres](sql-database-disaster-recovery-drills.md)
- [Documentación de la base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0224_2016-->