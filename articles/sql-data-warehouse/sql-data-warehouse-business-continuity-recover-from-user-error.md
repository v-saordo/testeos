<properties
   pageTitle="Recuperación de una base de datos de un error de usuario en Almacenamiento de datos SQL | Microsoft Azure"
   description="Pasos para recuperar una base de datos de un error de usuario en Almacenamiento de datos SQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="02/17/2016"
   ms.author="sahajs;barbkess;sonyama"/>

# Recuperación de una base de datos de un error de usuario en Almacenamiento de datos SQL

Almacenamiento de datos SQL ofrece dos capacidades básicas de recuperación de errores de usuario que provocan daños en los datos o su eliminación de forma involuntaria:

- Restauración de una base de datos activa
- Restauración de una base de datos eliminada

Ambas capacidades restauran a una base de datos nueva del mismo servidor.

Hay dos API distintas que admiten una restauración de base de datos de Almacenamiento de datos SQL: Azure PowerShell y la API de REST. Puede utilizar cualquiera de ellas para tener acceso a la funcionalidad de restauración de Almacenamiento de datos SQL.

## Recuperación de una base de datos activa
En caso de errores de usuario que provocan la modificación no intencionada de los datos, puede restaurar la base de datos a cualquiera de los puntos de restauración dentro del período de retención. Las instantáneas de base de datos para una base de datos activa se producen al menos cada ocho horas y se conservan durante siete días.

### PowerShell

Use Azure PowerShell para realizar la restauración de la base de datos mediante programación. Para descargar el módulo Azure PowerShell, ejecute el [Instalador de plataforma web de Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Puede comprobar la versión ejecutando Get-Module -ListAvailable -Name Azure. Este artículo se basa en Microsoft Azure PowerShell versión 1.0.4.

Para restaurar una base de datos, use el cmdlet [Start-AzureSqlDatabaseRestore][].

1. Abra Windows PowerShell.
2. Conéctese a su cuenta de Azure y enumere todas las suscripciones asociadas a su cuenta.
3. Seleccione la suscripción que contiene la base de datos que se va a restaurar.
4. Lista de puntos de restauración de la base de datos (requiere el modo de administración de recursos de Azure).
5. Elija el punto de restauración deseado mediante RestorePointCreationDate.
6. Restaure la base de datos al punto de restauración deseado.
7. Supervise el progreso de la restauración.

```

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>" -ResourceGroupName "<YourResourceGroupName>").RestorePointCreationDate)[-10 .. -1]

	# Or for all restore points
	Get-AzureRmSqlDatabaseRestorePoints -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>" -ResourceGroupName "<YourResourceGroupName>"

# Pick desired restore point using RestorePointCreationDate
$PointInTime = "<RestorePointCreationDate>"

# Get the specific database name to restore
(Get-AzureRmSqlDatabase -ServerName "<YourServerName>" -ResourceGroupName "<YourResourceGroupName>").DatabaseName | where {$_ -ne "master" }
#or
Get-AzureRmSqlDatabase -ServerName "<YourServerName>" –ResourceGroupName "<YourResourceGroupName>"

# Restore database
$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceServerName "<YourServerName>" -SourceDatabaseName "<YourDatabaseName>" -TargetDatabaseName "<NewDatabaseName>" -PointInTime $PointInTime

# Monitor progress of restore operation
Get-AzureSqlDatabaseOperation -ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

Tenga en cuenta que si el servidor es foo.database.windows.net, use "foo" como -ServerName en los cmdlets de powershell anteriores.

### API de REST
Use REST para realizar la restauración de la base de datos mediante programación.

1. Obtenga la lista de puntos de restauración de base de datos mediante la operación para obtener puntos de restauración de base de datos.
2. Inicie la restauración con la operación [Crear solicitud de restauración de base de datos][].
3. Realice un seguimiento del estado de la restauración con la operación [Estado de operación de base de datos][].

Una vez finalizada la restauración, puede configurar la base de datos recuperada que va a usar siguiendo la guía [Finalización de una base de datos recuperada][].

## Recuperar una base de datos eliminada
En caso de que se elimine una base de datos, puede restaurarla al momento en que se eliminó. Almacenamiento de datos SQL de Azure toma una instantánea de la base de datos antes de eliminarla y la conserva durante siete días.

### PowerShell
Use Azure PowerShell para realizar la restauración de la base de datos eliminada mediante programación. Para descargar el módulo Azure PowerShell, ejecute el [Instalador de plataforma web de Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409).

Para restaurar una base de datos eliminada, use el cmdlet [Start-AzureSqlDatabaseRestore][].

1. Abra Microsoft Azure PowerShell.
2. Conéctese a su cuenta de Azure y enumere todas las suscripciones asociadas a su cuenta.
3. Seleccione la suscripción que contiene la base de datos eliminada que se va a restaurar.
4. Encontrar la base de datos y la fecha de eliminación en la lista de bases de datos eliminadas

```
Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>"
```

5. Obtenga la base de datos eliminada específica e inicie la restauración.

```
$Database = Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>" -DeletionDate "1/01/2015 12:00:00 AM"

$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName "<NewDatabaseName>"

Get-AzureSqlDatabaseOperation –ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

Tenga en cuenta que si el servidor es foo.database.windows.net, use "foo" como -ServerName en los cmdlets de powershell anteriores.

### API de REST
Use REST para realizar la restauración de la base de datos mediante programación.

1.	Para enumerar todas las bases de datos eliminadas que se pueden restaurar, utilice la operación [Lista de bases de datos eliminadas que se pueden restaurar][].
2.	Para obtener los detalles de la base de datos eliminada que desea restaurar, utilice la operación [Obtener base de datos eliminada que se puede restaurar][].
3.	Inicie la restauración con la operación [Crear solicitud de restauración de base de datos][].
4.	Realice un seguimiento del estado de la restauración con la operación [Estado de operación de base de datos][].

Una vez finalizada la restauración, puede configurar la base de datos recuperada que va a usar siguiendo la guía [Finalización de una base de datos recuperada][].


## Pasos siguientes
Para obtener información sobre las características de continuidad del negocio de otras ediciones de Base de datos SQL Azure, lea la [información general sobre la continuidad del negocio de Base de datos SQL de Azure][].


<!--Image references-->

<!--Article references-->
[información general sobre la continuidad del negocio de Base de datos SQL de Azure]: sql-database/sql-database-business-continuity.md
[Finalización de una base de datos recuperada]: sql-database/sql-database-recovered-finalize.md

<!--MSDN references-->
[Crear solicitud de restauración de base de datos]: http://msdn.microsoft.com/library/azure/dn509571.aspx
[Estado de operación de base de datos]: http://msdn.microsoft.com/library/azure/dn720371.aspx
[Obtener base de datos eliminada que se puede restaurar]: http://msdn.microsoft.com/library/azure/dn509574.aspx
[Lista de bases de datos eliminadas que se pueden restaurar]: http://msdn.microsoft.com/library/azure/dn509562.aspx
[Start-AzureSqlDatabaseRestore]: https://msdn.microsoft.com/library/dn720218.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0224_2016-->