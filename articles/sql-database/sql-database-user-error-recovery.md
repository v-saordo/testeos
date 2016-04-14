<properties 
   pageTitle="Recuperación de errores de usuario en Base de datos SQL" 
   description="Obtenga información acerca de cómo recuperarse de errores de los usuarios, datos dañados accidentalmente o una base de datos eliminada con la característica Restauración a un momento dado (PITR) de Base de datos SQL de Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="02/09/2016"
   ms.author="elfish"/>

# Recuperar una base de datos SQL de Azure de un error de usuario

Base de datos SQL de Azure ofrece dos capacidades básicas para recuperase de los errores de usuario o de la modificación no intencionada de los datos.

- Restauración a un momento dado 
- Restaurar la base de datos eliminada

Puede obtener más información acerca de estas capacidades en esta [publicación de blog](https://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/).

Base de datos SQL de Azure siempre se restaura en una base de datos nueva. Estas capacidades de restauración se ofrecen para todas las bases de datos de los niveles Basic, Standard y Premium.

##Restauración a un momento dado

En caso de un error de usuario o de modificación no intencionada de los datos, se puede usar Restauración a un momento dado para restaurar la base de datos a cualquier punto dado del período de retención de la base de datos.

Las bases de datos de la versión Basic tienen 7 días de retención, las de la versión Standard disponen de 14 días de retención y las de la versión Premium tienen 35 días de retención. Para obtener más información acerca de la retención de la base de datos, vea [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md).

> [AZURE.NOTE] Al restaurar una base de datos se crea una nueva base de datos. Es importante asegurarse de que el servidor en el que va a efectuar la restauración tenga suficiente capacidad DTU para la nueva base de datos. Puede solicitar un aumento de esta cuota [contactando con el soporte técnico](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/).

###Portal de Azure
> [AZURE.NOTE] Para bases de datos en grupos de bases de datos elásticas, el Portal de Azure solo admite punto de restauración a un momento dado en el mismo grupo. Si desea restaurar a un momento dado una base de datos de forma independiente, use la API de REST.

Para usar la restauración a un momento dado en el Portal de Azure, siga los pasos indicados a continuación.

1. Inicie sesión en el [portal de Azure](https://portal.Azure.com).
2. En el lado izquierdo de la pantalla, seleccione **EXAMINAR** y, a continuación, seleccione **Bases de datos SQL**.
3. Navegue hasta la base de datos y selecciónela.
4. En la parte superior de la hoja de la base de datos, seleccione **Restaurar**.
5. Especifique un nombre de base de datos y un momento dado y, a continuación, haga clic en **Crear**.
6. El proceso de restauración de base de datos se iniciará y se puede supervisar mediante **NOTIFICACIONES**, en el lado izquierdo de la pantalla.

###PowerShell
Use PowerShell para realizar una restauración a un momento dado mediante programación con el cmdlet [Start-AzureSqlDatabaseRestore](https://msdn.microsoft.com/library/dn720218.aspx?f=255&MSPPError=-2147217396). Para disponer de un tutorial detallado, [vea el vídeo de este procedimiento](https://azure.microsoft.com/documentation/videos/restore-a-sql-database-using-point-in-time-restore-with-microsoft-azure-powershell/).

> [AZURE.IMPORTANT] Este artículo contiene comandos para versiones de Azure PowerShell hasta, *pero sin incluir*, las versiones 1.0 y versiones posteriores. Puede comprobar la versión de Azure PowerShell con el comando **Get-Module azure | format-table version**.

		$Database = Get-AzureSqlDatabase -ServerName "YourServerName" –DatabaseName “YourDatabaseName”
		$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –PointInTime “2015-01-01 06:00:00”
		Get-AzureSqlDatabaseOperation –ServerName "YourServerName" –OperationGuid $RestoreRequest.RequestID
		 

###API de REST 
Use REST para realizar la restauración de la base de datos mediante programación. Para ello, cree la solicitud de restauración con la operación [Create Database](https://msdn.microsoft.com/library/azure/mt163685.aspx) y establezca el **modo de creación** en **PointInTimeRestore**.

##Restauración de una base de datos eliminada
En caso de que se elimine una base de datos, Base de datos SQL de Azure le permite restaurar la base de datos eliminada en el momento en que se eliminó. Base de datos SQL de Azure almacena la copia de seguridad de la base de datos eliminada durante el período de retención de la base de datos.

El período de retención de una base de datos eliminada lo determinan el nivel de servicio de la base de datos mientras esta existe, o bien el número de días en que existe la base de datos, el menor de estos dos valores. Para obtener más información acerca de la retención de la base de datos, lea la [información general de continuidad del negocio](sql-database-business-continuity.md).

> [AZURE.NOTE] Al restaurar una base de datos se crea una nueva base de datos. Es importante asegurarse de que el servidor en el que va a efectuar la restauración tenga suficiente capacidad DTU para la nueva base de datos. Puede solicitar un aumento de esta cuota [contactando con el soporte técnico](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/).

###Portal de Azure
Para restaurar una base de datos eliminada mediante el Portal de Azure, siga estos pasos indicados a continuación:

1. Inicie sesión en el [portal de Azure](https://portal.Azure.com).
2. En el lado izquierdo de la pantalla, seleccione **EXAMINAR** y, a continuación, seleccione **Servidores SQL**.
3. Navegue hasta el servidor y selecciónelo.
4. Desplácese hacia abajo hasta **Operaciones** en la hoja del servidor y haga clic en el icono **Bases de datos eliminadas**.
5. Seleccione la base de datos eliminada que desee restaurar.
6. Especifique un nombre de base de datos y haga clic en **Crear**.
7. El proceso de restauración de base de datos se iniciará y se puede supervisar mediante **NOTIFICACIONES**, en el lado izquierdo de la pantalla.

###PowerShell
Para restaurar una base de datos eliminada mediante PowerShell, use el cmdlet [Start-AzureSqlDatabaseRestore](https://msdn.microsoft.com/library/dn720218.aspx?f=255&MSPPError=-2147217396). Para disponer de un tutorial detallado, [vea un vídeo de este procedimiento](https://azure.microsoft.com/documentation/videos/restore-a-deleted-sql-database-with-microsoft-azure-powershell/).

1. Busque la base de datos eliminada y la fecha de eliminación en la lista de bases de datos eliminadas.
		
		Get-AzureSqlDatabase -RestorableDropped -ServerName "YourServerName"

2. Obtenga la base de datos eliminada específica e inicie la restauración.

		$Database = Get-AzureSqlDatabase -RestorableDropped -ServerName "YourServerName" –DatabaseName “YourDatabaseName” -DeletionDate "1/01/2015 12:00:00 AM""
		$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName “NewDatabaseName”
		Get-AzureSqlDatabaseOperation –ServerName "YourServerName" –OperationGuid $RestoreRequest.RequestID
		 

###API de REST 
Use REST para realizar la restauración de la base de datos mediante programación.

1.	Para enumerar todas las bases de datos eliminadas que se pueden restaurar, utilice la operación [Lista de bases de datos eliminadas que se pueden restaurar](http://msdn.microsoft.com/library/azure/dn509562.aspx).
	
2.	Para obtener los detalles de la base de datos eliminada que desea restaurar, utilice la operación [Obtener base de datos eliminada que se puede restaurar](http://msdn.microsoft.com/library/azure/dn509574.aspx).

3.	Inicie la restauración con la operación [Crear solicitud de restauración de base de datos](http://msdn.microsoft.com/library/azure/dn509571.aspx).
	
4.	Realice un seguimiento del estado de la restauración con la operación [Estado de operación de base de datos](http://msdn.microsoft.com/library/azure/dn720371.aspx).

<!---HONumber=AcomDC_0224_2016-->