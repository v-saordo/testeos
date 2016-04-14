<properties 
	pageTitle="Actualizar a la Base de datos SQL V12 de Azure mediante PowerShell | Microsoft Azure" 
	description="Explicación acerca de cómo actualizar a la Base de datos SQL V12 de Azure, incluyendo cómo actualizar las bases de datos de tipo Web y Business y cómo actualizar un servidor V11 migrando sus bases de datos directamente a un grupo de bases de datos elásticas mediante PowerShell." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/23/2016" 
	ms.author="sstein"/>

# Actualización a la Base de datos SQL V12 de Azure mediante PowerShell


> [AZURE.SELECTOR]
- [Azure portal](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


La versión V12 de la Base de datos SQL es la versión más reciente, por lo que le recomendamos que realice esta actualización para la Base de datos SQL V12. La versión V12 de la Base de datos SQL le ofrece muchas más [ventajas con respecto a la versión anterior](sql-database-v12-whats-new.md), entre las que se incluyen:

- Compatibilidad mejorada con SQL Server.
- Rendimiento de tipo Premium mejorado y nuevos niveles de rendimiento.
- [Grupos de bases de datos elásticas](sql-database-elastic-pool.md).

Este artículo proporciona instrucciones para actualizar los servidores y las bases de datos existentes de la Base de datos SQL V11 a la versión V12 de la misma.

Durante el proceso de actualización a la versión V12, podrá actualizar cualquier base de datos de tipo Web y Business a un nuevo nivel de servicio, por lo que se incluyen instrucciones para actualizar este tipo de bases de datos.

Asimismo, la migración a un [grupo de bases de datos elásticas](sql-database-elastic-pool.md) puede resultarle más rentable que actualizar a niveles de rendimiento individuales (planes de tarifas) de bases de datos únicas. De igual manera, los grupos le permitirán simplificar la administración de sus bases de datos, ya que realmente solo necesita administrar la configuración de rendimiento del grupo, en vez de dedicarse a administrar independientemente los niveles de rendimiento de las bases de datos individuales. Si tiene bases de datos en varios servidores, puede considerar la posibilidad de colocarlas todas en el mismo servidor y aprovecharse de las ventajas que supondría implementarlas en un grupo.

Además, puede migrar de forma automática y con facilidad las bases de datos de los servidores V11 a grupos de bases de datos elásticas, siguiendo los pasos de este artículo.

Tenga en cuenta que las bases de datos permanecerán en línea y seguirán funcionando durante toda la operación de actualización. En el momento en que realice la transición al nuevo nivel de rendimiento, es posible que se produzca una caída temporal de las conexiones a la base de datos, aunque no durarán mucho; normalmente oscilarán entre los 90 segundos y los 5 minutos. Si su aplicación tiene una [gestión de errores temporal para terminaciones de conexiones](sql-database-connect-central-recommendations.md), entonces es suficiente con que tome precauciones contra las interrupciones de la conexión al final de la actualización.

Recuerde que la actualización a la Base de datos SQL V12 no se puede deshacer. Después de realizar la actualización, no podrá revertir el servidor a la versión V11.

Después de realizar la actualización a V12, las [recomendaciones de nivel de servicio](sql-database-service-tier-advisor.md) y las [recomendaciones de grupos elásticos](sql-database-elastic-pool-portal.md#step-2-choose-a-pricing-tier) no estarán disponibles de forma inmediata; para ello, deberá esperar a que el servicio evalúe las cargas de trabajo en el nuevo servidor. El historial de recomendaciones del servidor V11 no se aplica a los servidores V12, así que no se conservará.

## Preparación de la actualización

- **Actualizar todas las bases de datos de tipo Web y Business**: consulte la sección [Actualizar todas las bases de datos de tipo Web y Business](sql-database-v12-upgrade.md#upgrade-all-web-and-business-databases) que tiene a continuación o use [PowerShell para actualizar las bases de datos y el servidor](sql-database-upgrade-server-powershell.md).
- **Revisar y suspender la replicación geográfica**: si la Base de datos SQL de Azure está configurada para replicación geográfica, debe documentar la configuración actual y [detener la replicación geográfica](sql-database-geo-replication-portal.md#remove-secondary-database). Una vez completada la actualización, debe volver a configurar la base de datos para la replicación geográfica.
- **Abra los siguientes puertos si tiene clientes en una máquina virtual de Azure**: si el programa cliente se conecta a la Base de datos SQL V12, mientras el cliente se ejecuta en una máquina virtual (VM) de Azure, debe abrir los intervalos de puerto 11000 a 11999 y de 14000 a 14999 en la máquina virtual. Para obtener más información, consulte [Puertos de la Base de datos SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md).


## Requisitos previos 

Para actualizar un servidor a V12 con PowerShell, deberá tener Azure PowerShell instalado y en ejecución y, dependiendo de la versión, es posible que tenga que cambiarlos en el modo de administrador de recursos para acceder a los cmdlets de PowerShell del Administrador de recursos de Azure.

Para ejecutar los cmdlets de PowerShell, necesitará tener Azure PowerShell instalado y en marcha. Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).


## Configuración de las credenciales y selección de la suscripción

Para ejecutar los cmdlets de PowerShell en su suscripción de Azure debe establecer el acceso a su cuenta de Azure. Ejecute lo siguiente y aparecerá una pantalla de inicio de sesión para especificar sus credenciales. Use el mismo correo electrónico y la misma contraseña que usa para iniciar sesión en el portal de Azure.

	Add-AzureRmAccount

Después de iniciar sesión correctamente, se mostrará información en la pantalla que incluye el identificador con el que ha iniciado sesión y las suscripciones a Azure a las que tiene acceso.

Para seleccionar la suscripción con la que quiera trabajar, necesita el identificador de la suscripción (**-SubscriptionId**) o el nombre de la suscripción (**-SubscriptionName**). Puede copiar el nombre o el identificador del paso anterior, o bien, si dispone de varias suscripciones, puede ejecutar el cmdlet **Get-AzureRmSubscription** y copiar la información de suscripción deseada del conjunto de resultados.

Ejecute el siguiente cmdlet con la información de suscripción para establecer la suscripción actual:

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Los siguientes comandos se ejecutarán en la suscripción que acaba de seleccionar.

## Obtener recomendaciones

Para obtener la recomendación para la actualización del servidor, ejecute el siguiente cmdlet:

    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1” 

Para obtener más información, consulte las [Recomendaciones acerca del grupo de bases de datos elásticas de Base de datos SQL de Azure](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations) y las [Recomendaciones acerca del nivel de precios de Base de datos SQL de Azure](sql-database-service-tier-advisor.md).



## Iniciar la actualización

Para iniciar la actualización del servidor, ejecute el siguiente cmdlet:

    Start-AzureRmSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


Cuando ejecute este comando, comenzará el proceso de actualización. Puede personalizar la salida de la recomendación y ofrecer la recomendación editada para este cmdlet.


## Actualización de un servidor


    # Adding the account
    #
    Add-AzureRmAccount
    
    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION' 
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP' 
    $ServerName = 'YOUR_SERVER' 
    
    # Selecting the right subscription 
    # 
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName 
    
    # Getting the upgrade recommendations 
    #
    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    
    # Starting the upgrade process 
    #
    Start-AzureRmSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## Asignación de actualización personalizada

Si las recomendaciones no son adecuadas para su servidor y el caso de negocios, puede elegir cómo se actualizan las bases de datos y asignarlas a bases de datos únicas o elásticas.

Los parámetros ElasticPoolCollection y DatabaseCollection son opcionales:
    
    # Creating elastic pool mapping
    #
    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties 
    $elasticPool.DatabaseDtuMax = 100 
    $elasticPool.DatabaseDtuMin = 0 
    $elasticPool.Dtu = 800 
    $elasticPool.Edition = "Standard" 
    $elasticPool.DatabaseCollection = ("DB1", “DB2”, “DB3”, “DB4”) 
    $elasticPool.Name = "elasticpool_1" 
    $elasticPool.StorageMb = 800 
     
    # Creating single database mapping for 2 databases. DBMain1 mapped to S0 and DBMain2 mapped to S2
    #
    $databaseMap1 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties 
    $databaseMap1.Name = "DBMain1" 
    $databaseMap1.TargetEdition = "Standard" 
    $databaseMap1.TargetServiceLevelObjective = "S0"
    
    $databaseMap2 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties 
    $databaseMap2.Name = "DBMain2" 
    $databaseMap2.TargetEdition = "Standard" 
    $databaseMap2.TargetServiceLevelObjective = "S2"
     
    # Starting the upgrade
    #
    Start-AzureRmSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -Version 12.0 -DatabaseCollection @($databaseMap1, $databaseMap2) -ElasticPoolCollection @($elasticPool) 

    

## Supervisión de las bases de datos después de actualizar a la Base de datos SQL V12


Después de la actualización, es recomendable que supervise la base de datos de forma activa para asegurarse de que todas las aplicaciones se están ejecutando correctamente y que tienen el rendimiento adecuado; de esta manera, podrá optimizar el uso según sea necesario.

Además de supervisar las bases de datos individuales, también puede supervisar los grupos de bases de datos elásticas [mediante el portal](sql-database-elastic-pool-portal.md#monitor-and-manage-an-elastic-database-pool) o mediante [PowerShell](sql-database-elastic-pool-powershell.md#monitoring-elastic-databases-and-elastic-database-pools).


**Datos de consumo de recursos**: para las bases de datos de niveles Básico, Estándar y Premium, tiene disponibles datos de consumo de recursos a través de una vista de administración dinámica (DMV) denominada [sys.dm\_ db\_ resource\_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) en la base de datos del usuario. Esta vista de administración dinámica proporciona información de consumo de recursos casi en tiempo real a intervalos de 15 segundos para la hora de funcionamiento anterior. El consumo de porcentaje de DTU para un intervalo se calcula como el consumo de porcentaje máximo de las dimensiones de CPU, E/S y de registro. Esta es una consulta para calcular el consumo medio de porcentaje de DTU durante la última hora:

    SELECT end_time
    	 , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

Información de supervisión adicional:

- [Guía de rendimiento de Base de datos SQL de Azure para bases de datos individuales](http://msdn.microsoft.com/library/azure/dn369873.aspx).
- [Consideraciones de precio y rendimiento para un grupo de bases de datos elásticas](sql-database-elastic-pool-guidance.md).
- [Supervisión de Base de datos SQL de Azure con vistas de administración dinámica](sql-database-monitoring-with-dmvs.md)



**Alertas:** configure “Alertas” en el Portal de Azure para recibir una notificación cuando el consumo de DTU de una base de datos actualizada alcance un determinado nivel. Las alertas de la base de datos pueden configurarse en el Portal de Azure con diferentes métricas de rendimiento como DTU, CPU, E/S y registro. Vaya a la base de datos y seleccione **Reglas de alerta** en la hoja **Configuración**.

Por ejemplo, puede configurar una alerta de correo electrónico en "Porcentaje de DTU" si el valor de porcentaje medio de DTU supera el 75 % en los últimos 5 minutos. Consulte [Recibir notificaciones de alerta](../azure-portal/insights-receive-alert-notifications.md) para obtener más información acerca de cómo configurar las notificaciones de alerta.



## Pasos siguientes

- [Cree un grupo de bases de datos elásticas](sql-database-elastic-pool-portal.md) y agregue algunas o todas las bases de datos al grupo.
- [Cambio del nivel de servicio y del nivel de rendimiento de la base de datos](sql-database-scale-up.md).



## Información relacionada

- [Get-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Start-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)

<!---HONumber=AcomDC_0224_2016-->