<properties 
   pageTitle="Recuperación ante desastres de Base de datos SQL" 
   description="Obtenga información sobre cómo recuperar una base de datos tras un corte en el suministro eléctrico o un error con las capacidades de la replicación geográfica activa, replicación geográfica estándar y restauración geográfica de Base de datos SQL de Azure." 
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

# Recuperación de una base de datos SQL de Azure tras una interrupción

Base de datos SQL de Azure ofrece las siguientes capacidades para recuperarse de un corte en el suministro eléctrico:

- Replicación geográfica activa [(blog)](http://azure.microsoft.com/blog/2014/07/12/spotlight-on-sql-database-active-geo-replication/)
- Replicación geográfica estándar [(blog)](http://azure.microsoft.com/blog/2014/09/03/azure-sql-database-standard-geo-replication/)
- Restauración geográfica [(blog)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)
- Nuevas capacidades de replicación geográfica [(blog)](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)

Para obtener información acerca de cómo prepararse ante desastres y sobre cuándo se debe recuperar la base de datos, visite la página [Diseño para la continuidad del negocio](sql-database-business-continuity-design.md).

##Cuándo iniciar la recuperación 

La operación de recuperación repercute en la aplicación. Este proceso requiere cambiar la cadena de conexión SQL y puede provocar la pérdida de datos permanente. Por lo tanto debe realizarse solo cuando la duración de la interrupción pueda durar más que el RTO de su aplicación. Cuando se implementa la aplicación en producción, deberá supervisar con frecuencia el estado de la aplicación. Para ello, use los siguientes puntos de datos para garantizar la recuperación:

1. Error de conectividad permanente de la capa de aplicación en la base de datos.
2. El Portal de Azure muestra una alerta sobre una incidencia en una región con un gran impacto.

> [AZURE.NOTE] Una vez recuperada la base de datos, podrá configurarla para su uso. Para ello, siga los pasos descritos en la guía [Configuración de una base de datos recuperada](#postrecovery).

## Conmutación por error de la base de datos secundaria de replicación geográfica
> [AZURE.NOTE] Debe configurarla para obtener una base de datos secundaria que pueda usar para la conmutación por error. La replicación geográfica estándar solo está disponible para las bases de datos Standard y Premium. Obtenga información acerca de [cómo configurar la replicación geográfica](sql-database-business-continuity-design.md)

###Portal de Azure
Utilice el Portal de Azure para terminar la relación de copia continua con la base de datos secundaria de replicación geográfica.

1. Inicie sesión en el [Portal de Azure](https://portal.Azure.com).
2. En el lado izquierdo de la pantalla, seleccione **EXAMINAR** y, a continuación, seleccione **Bases de datos SQL**.
3. Desplácese hasta la base de datos y selecciónela. 
4. En la parte inferior de la hoja de la base de datos, seleccione el **Mapa de replicación geográfica**.
4. En la sección **Secundarias**, haga clic con el botón derecho en la fila con el nombre de la base de datos que desea recuperar y seleccione **Conmutación por error**.

###PowerShell
Use PowerShell para iniciar la conmutación por error de la base de datos secundaria de replicación geográfica mediante el cmdlet [AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx).
		
		$database = Get-AzureRMSqlDatabase –DatabaseName "mydb” –ResourceGroupName "rg2” –ServerName "srv2”
		$database | Set-AzureRMSqlDatabaseSecondary –Failover -AllowDataLoss

###API de REST 
Use REST para iniciar la conmutación por error a una base de datos secundaria mediante programación.

1. Obtenga el vínculo de replicación de una base de datos secundaria específica mediante la operación[Obtener vínculo de replicación](https://msdn.microsoft.com/library/mt600778.aspx).
2. Realice la conmutación por error de la base de datos secundaria mediante el elemento [Establecer la base de datos secundaria como primaria](https://msdn.microsoft.com/library/mt582027.aspx), permitiendo la pérdida de datos. 

## Recuperación mediante la restauración geográfica

En el caso de una interrupción en una base de datos, es posible recuperar la base de datos desde la última copia de seguridad con redundancia geográfica mediante la restauración geográfica.

> [AZURE.NOTE] Al recuperar una base de datos se crea una nueva base de datos. Es importante asegurarse de que el servidor en el que va a efectuar la recuperación tenga suficiente capacidad DTU para la nueva base de datos. Puede solicitar un aumento de esta cuota [contactando con el soporte técnico](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/).

###Portal de Azure (recuperación de una base de datos independiente)
Para restaurar una Base de datos SQL mediante la restauración geográfica en el Portal de Azure, siga estos pasos.

1. Inicie sesión en el [portal de Azure](https://portal.Azure.com).
2. En el lado izquierdo de la pantalla, seleccione **NUEVO** y, a continuación, seleccione **Datos y almacenamiento**. Luego seleccione **Base de datos SQL**.
2. Seleccione **COPIA DE SEGURIDAD** como origen y, a continuación, seleccione la copia de seguridad con redundancia geográfica que desea recuperar.
3. Especifique el resto de propiedades de la base de datos y, a continuación, haga clic en **Crear**.
4. El proceso de restauración de base de datos se iniciará y se puede supervisar mediante **NOTIFICACIONES**, en el lado izquierdo de la pantalla.

###Portal de Azure (recuperación de un grupo de bases de datos elásticas)
Para restaurar una base de datos SQL en un grupo de bases de datos elásticas mediante la restauración geográfica a través del portal, siga las instrucciones que se muestran a continuación.

1. Inicie sesión en el [portal de Azure](https://portal.Azure.com).
2. En el lado izquierdo de la pantalla, seleccione **Examinar** y, a continuación, seleccione **Grupos elásticos de SQL**.
3. Seleccione el grupo con en el que quiere realizar la restauración geográfica de la base de datos.
4. En la parte superior de la hoja del grupo elástico, seleccione **Crear base de datos**.
5. Seleccione **COPIA DE SEGURIDAD** como origen y, a continuación, seleccione la copia de seguridad con redundancia geográfica que desea recuperar.
6. Especifique el resto de propiedades de la base de datos y, a continuación, haga clic en **Crear**.
7. El proceso de restauración de base de datos se iniciará y se puede supervisar mediante **NOTIFICACIONES**, en el lado izquierdo de la pantalla.

###PowerShell 
> [AZURE.NOTE] Actualmente la restauración geográfica con PowerShell solo admite restaurar en una base de datos independiente. Para la restauración geográfica en un grupo de bases de datos elásticas, use el [Portal de Azure](https://portal.Azure.com).

Para restaurar una Base de datos SQL mediante la restauración geográfica mediante PowerShell, inicie una solicitud de restauración geográfica con el cmdlet [start-AzureSqlDatabaseRecovery](https://msdn.microsoft.com/library/azure/dn720224.aspx).

		$Database = Get-AzureSqlRecoverableDatabase -ServerName "ServerName" –DatabaseName “DatabaseToBeRecovered"
		$RecoveryRequest = Start-AzureSqlDatabaseRecovery -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –TargetServerName “TargetServerName”
		Get-AzureSqlDatabaseOperation –ServerName "TargetServerName" –OperationGuid $RecoveryRequest.RequestID

###API de REST 

Use REST para realizar la recuperación de la base de datos mediante programación.

1.	Obtenga la lista de bases de datos recuperables mediante la operación [Enumerar bases de datos recuperables](http://msdn.microsoft.com/library/azure/dn800984.aspx).
	
2.	Obtenga la base de datos que desea recuperar mediante la operación [Obtener base de datos recuperable](http://msdn.microsoft.com/library/azure/dn800985.aspx).
	
3.	Cree la solicitud de recuperación mediante la operación [Crear solicitud de recuperación de base de datos](http://msdn.microsoft.com/library/azure/dn800986.aspx).
	
4.	Realice un seguimiento del estado de la recuperación mediante la operación [Estado de la operación de base de datos](http://msdn.microsoft.com/library/azure/dn720371.aspx).
 
## Configure su base de datos después de realizar la recuperación<a name="postrecovery"></a>

Esta es una lista de comprobación de las tareas que puede usar para preparar la producción de la base de datos recuperada.

### Actualización de cadenas de conexión

Compruebe que las cadenas de conexión de la aplicación apuntan a la base de datos recién recuperada. Actualice las cadenas de conexión si se aplica cualquiera de las siguiente situaciones:

  + La base de datos recuperada utiliza un nombre diferente del nombre de la base de datos de origen
  + La base de datos recuperada está en un servidor diferente del servidor de origen

Para obtener más información acerca de cómo cambiar las cadenas de conexión, consulte [Conexiones a la Base de datos SQL de Azure: recomendaciones principales ](sql-database-connect-central-recommendations.md).
 
### Modificación de reglas de firewall
Compruebe las reglas de firewall tanto en el nivel de servidor como en el nivel de base de datos y asegúrese de que están habilitadas las conexiones desde los equipos cliente o Azure al servidor y a la base de datos recién recuperada. Para obtener más información, consulte [Configuración del firewall (Base de datos SQL de Azure)](sql-database-configure-firewall-settings.md).

### Comprobación de inicios de sesión de servidor y usuarios de base de datos

Compruebe si todos los inicios de sesión que usa la aplicación existen en el servidor que hospeda la base de datos recuperada. Vuelva a crear los inicios de sesión que falten y concédales los permisos adecuados en la base de datos recuperada. Para obtener más información, consulte [Administración de bases de datos, inicios de sesión y usuarios en Base de datos SQL de Microsoft Azure](sql-database-manage-logins.md).

Compruebe si cada usuario de la base de datos recuperada está asociado a un inicio de sesión de servidor válido. Utilice la instrucción ALTER USER para asignar un inicio de sesión de servidor válido al usuario. Para obtener más información, consulte [ALTER USER](http://go.microsoft.com/fwlink/?LinkId=397486).


### Configuración de alertas de telemetría

Compruebe si la configuración de las reglas de alerta existente se ha asignado a la base de datos recuperada. Actualice la configuración si se da cualquiera de las situaciones siguientes:

  + La base de datos recuperada utiliza un nombre diferente del nombre de la base de datos de origen
  + La base de datos recuperada está en un servidor diferente del servidor de origen

Para obtener más información sobre las reglas de alerta de las bases de datos, consulte [Recepción de notificaciones de alerta](insights-receive-alert-notifications.md) y [Estado del servicio de seguimiento](insights-service-health.md).


### Habilitar auditoría

Si se requiere una auditoría para tener acceso a una base de datos, será preciso habilitar Auditoría tras la recuperación de la base de datos. Un buen indicador de que es necesaria una auditoría es que las aplicaciones cliente usen cadenas de conexión seguras en un patrón de *.database.secure.windows.net. Para obtener más información, consulte [Introducción a la auditoría de la Base de datos SQL](sql-database-auditing-get-started.md).

<!---HONumber=AcomDC_0211_2016-->