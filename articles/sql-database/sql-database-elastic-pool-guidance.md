<properties 
	pageTitle="Consideraciones sobre precios y rendimiento para grupos de bases de datos elásticas de Base de datos SQL de Azure" 
	description="Un grupo de bases de datos elásticas es una colección de recursos disponibles que comparte un grupo de bases de datos elásticas. Este documento ofrece orientación para ayudarle a evaluar la idoneidad de usar un grupo de bases de datos elásticas para un grupo de base de datos." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/26/2016" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Consideraciones de precio y rendimiento para un grupo de bases de datos elásticas


Evalúe si usar un grupo de bases de datos elásticas para un grupo de bases de datos es rentable según los patrones de uso de la base de datos y las diferencias de precios entre un grupo de bases de datos elásticas y bases de datos únicas. También se proporciona orientación adicional para ayudar a determinar el tamaño actual del grupo necesario para un conjunto de bases de datos SQL existente.

- Para obtener información general de grupos de base de datos elásticas, vea [Grupos de bases de datos elásticas de Base de datos SQL](sql-database-elastic-pool.md).
- Para obtener información detallada sobre los grupos de bases de datos elásticas, vea [Referencia de grupos de bases de datos elásticas de Base de datos SQL](sql-database-elastic-pool-reference.md).


> [AZURE.NOTE] Los grupos de bases de datos elásticas están actualmente en vista previa y solo estarán disponibles en servidores con Base de datos SQL V12.

## Grupos de bases de datos elásticas

Los ISV de SaaS desarrollan aplicaciones basadas en los niveles superiores de datos de la escala que constan de varias bases de datos. Un patrón de aplicación común es proporcionar a cada cliente su propia base de datos. Sin embargo, cada cliente tiene patrones de uso variables e impredecibles y resulta difícil predecir los requisitos de recursos de cada usuario de base de datos. Por lo tanto, un ISV puede realizar un aprovisionamiento excesivo de los recursos con un gasto considerable para garantizar un rendimiento y unos tiempos de repuesta favorables para todas las bases de datos. El ISV también puede gastar menos y arriesgar en una experiencia de rendimiento insuficiente para sus clientes.

Los grupos de bases de datos elásticas en Base de datos SQL de Azure permiten a los ISV de SaaS optimizar el rendimiento del precio para un grupo de bases de datos dentro de un presupuesto prescrito a la vez que se ofrece elasticidad de rendimiento para cada base de datos. Los grupos de bases de datos elásticas permiten al ISV adquirir unidades de transacción de base de datos elástica (eDTU) para un grupo compartido entre varias bases de datos con el fin de dar cabida a períodos impredecibles de uso por bases de datos individuales. El requisito de eDTU para un grupo se determina mediante el uso agregado de sus bases de datos. La cantidad de eDTU disponibles para el grupo se controla mediante el presupuesto del ISV. Los grupos de bases de datos elásticas facilitan al ISV razonar el impacto del presupuesto en el rendimiento y viceversa para su grupo. El ISV simplemente agrega bases de datos al grupo, establece el número garantizado o la capacidad de eDTU necesarias para las bases de datos y luego establece la eDTU del grupo según el presupuesto. Mediante el uso de grupos de bases de datos elásticas, los ISV pueden aumentar de forma eficiente su servicio a partir de un método Lean Startup hasta un negocio con madurez a una escala cada vez mayor.
  


## Cuándo considerar un grupo de bases de datos elásticas

Los grupos de bases de datos elásticas son apropiados para un amplio número de bases de datos con patrones de utilización específicos. Para una base de datos determinada, este patrón está caracterizado por una utilización media baja con picos de utilización relativamente poco frecuentes.

Cuantas más bases de datos pueda agregar a un grupo, mayores ahorros habrá. Según su patrón de uso de la aplicación, es posible ver los ahorros con tan solo dos bases de datos S3.

Las siguientes secciones le ayudarán a comprender cómo evaluar si la recopilación específica de bases de datos se beneficiará del uso de un grupo de bases de datos elásticas. Los ejemplos usan grupos de bases de datos elásticas Estándar, pero también se aplican los mismos principios a grupos Básico y Premium.

### Evaluación de los patrones de utilización de base de datos

La siguiente ilustración muestra un ejemplo de una base de datos que está mucho tiempo inactiva, pero que también tiene picos periódicos de actividad. Se trata de un patrón de utilización que es apropiado para un grupo de bases de datos elásticas:
 
   ![una base de datos][1]

Para el período de una hora que aparece a continuación, DB1 llega a las 90 DTU, pero el uso medio es inferior a 5 DTU. Se requiere un nivel de rendimiento S3 para ejecutar esta carga de trabajo en una única base de datos. Sin embargo, esto hace que la mayoría de los recursos no se use durante períodos de baja actividad.

Un grupo de bases de datos elásticas permite que estas DTU sin usar se compartan entre varias bases de datos y de esa manera se reduce la cantidad total de DTU que se necesitan y el coste general.

Según el ejemplo anterior, suponga que existen bases de datos adicionales con patrones de utilización similares como DB1. En las siguientes dos figuras, la utilización de 4 bases de datos y 20 bases de datos se estratifican en el mismo gráfico para mostrar la naturaleza de no solapamiento de la utilización con el paso del tiempo:

   ![cuatro bases de datos][2]

   ![veinte bases de datos][3]

La utilización de la DTU agregada en las 20 bases de datos se muestra con la línea negra en la ilustración anterior. Esto muestra que la utilización de DTU agregada nunca supera las 100 DTU e indica que las 20 bases de datos pueden compartir 100 eDTU en este período. El resultado es una reducción multiplicada por 20 en las DTU y una reducción del precio 13 veces menor en comparación con la colocación de cada base de datos en los niveles de rendimiento S3 para bases de datos únicas.


Este ejemplo es ideal por las siguientes razones:

- Existen grandes diferencias entre la utilización de picos y la utilización media por base de datos.  
- La utilización de picos para cada base de dato se produce en puntos de tiempo distintos. 
- Las eDTU se comparten entre un gran número de base de datos.


El precio de un grupo de bases de datos elásticas es una función de las eDTU del grupo. Aunque el precio unitario de una eDTU para un grupo es 1,5 veces mayor que el de una DTU para una base de datos única, **las eDTU de grupo pueden compartirse entre varias bases de datos por lo que, en muchos casos, el número total de eDTU que se necesitan es menor**. Estas distinciones de precio y uso compartido de la eDTU son la base de la posibilidad de ahorro en el precio que pueden proporcionar los grupos.

<br>

Las siguientes reglas generales relacionadas con el número de bases de datos y la utilización de bases de datos ayudan a garantizar que un grupo de bases de datos elásticas ofrece costes reducidos en comparación con el uso de niveles de rendimiento de las bases de datos únicas.


### Número mínimo de bases de datos

Si la suma de las DTU de los niveles de rendimiento de las bases de datos únicas supera en más de 1,5 al de las eDTU necesarias para el grupo, es más rentable usar un grupo elástico. Para saber los tamaños disponibles, vea [Límites de almacenamiento y de eDTU para grupos de bases de datos elásticas y bases de datos elásticas](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases).


***Ejemplo***<br> Se requieren 2 bases de datos S3 o 15 bases de datos S0 como mínimo para que un grupo de bases de datos elásticas de 100 eDTU sea más rentable que usar niveles de rendimiento para bases de datos únicas.



### Número máximo de bases de datos de picos simultáneamente

Cuando se comparten eDTU, no todas las bases de datos de un grupo pueden usar simultáneamente las eDTU hasta el límite disponible al usar niveles de rendimiento para bases de datos únicas. Cuantas menos bases de datos con un pico simultáneo haya, más bajo puede establecerse el número de eDTU de grupo y más rentable resulta. En general, no más de los 2/3 (o el 67%) de las bases de datos del grupo deben alcanzar el límite de eDTU establecido como pico de forma simultánea.

***Ejemplo***<br> Para reducir los costos de 3 bases de datos S3 de un grupo de 200 eDTU, como mucho 2 de estas bases de datos pueden alcanzar simultáneamente el pico de uso máximo. De lo contrario, si más de 2 de estas 4 bases de datos S3 establecen simultáneamente el pico, tendría que establecerse un tamaño del grupo en más de 200 eDTU. Además, si el tamaño del grupo se cambia a más de 200 eDTU, sería necesario agregar más bases de datos S3 al grupo para que los costes siguieran siendo inferiores a los niveles de rendimiento para bases de datos únicas.


Tenga en cuenta que este ejemplo no tiene en cuenta la utilización de otras bases de datos en el grupo. Si en un momento determinado se están usando todas las bases de datos, menos de los 2/3 (o el 67%) de las bases de datos podrán alcanzar simultáneamente el pico de uso.


### Utilización de DTU por base de datos

Una gran diferencia entre el pico y la utilización media de una base de datos indica largos períodos de poca utilización y breves períodos de uso intenso. Este patrón de uso es ideal para compartir recursos entre bases de datos. Debe considerarse utilizar una base de datos para un grupo cuando su uso máximo es aproximadamente 1,5 veces mayor que su uso medio.

    
***Ejemplo***<br> Una base de datos S3 con un pico de 100 DTU que de media usa 67 DTU o menos es una buena candidata para compartir eDTU en un grupo de bases de datos elásticas. También una base de datos S1 con un pico de 20 DTU que de media usa 13 DTU o menos es una buena candidata para un grupo de bases de datos elásticas.
    

## Heurística para comparar la diferencia de precio entre un grupo de bases de datos elásticas y bases de datos únicas 

La siguiente heurística puede ayudar a estimar si un grupo de bases de datos elásticas es más rentable que usar bases de datos únicas por separado.

1. Calcule las eDTU necesarias para el grupo de la siguiente forma:
    
    MAX(*Número total de BD* * *promedio de uso de DTU por BD*, *número de BD con picos simultáneos* * *Uso pico de DTU por BD*)

2. Seleccione el menor valor de eDTU disponible para el grupo que sea superior al calculado en el paso 1. Para saber las opciones de eDTU disponibles, vea los valores válidos de eDTU que aparecen en [Límites de almacenamiento y de eDTU para grupos de bases de datos elásticas y bases de datos elásticas](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases).


3. Cálculo del precio del grupo de la siguiente forma:

    precio del grupo = *nº de eDTU del grupo* * *precio unitario de eDTU del grupo*

    Vea [Base de datos SQL Precios](https://azure.microsoft.com/pricing/details/sql-database/) para obtener información sobre los precios.


4. Compare el precio del grupo del paso 3 con el precio de uso de los niveles de rendimiento adecuados para bases de datos únicas.



## Determinación del mejor tamaño de eDTU de grupo para bases de datos SQL existentes 

El mejor tamaño para un grupo de bases de datos elásticas depende de las eDTU agregadas y los recursos de almacenamiento necesarios para todas las bases de datos del grupo. Esto implica determinar la cantidad mayor de las siguientes dos cantidades:

* Número máximo de DTU utilizado por todas las bases de datos en el grupo.
* Número máximo de bytes de almacenamiento utilizado por todas las bases de datos en el grupo. 

Para saber los tamaños disponibles, vea [Límites de almacenamiento y de eDTU para grupos de bases de datos elásticas y bases de datos elásticas](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases).


### Use Service Tier Advisor (STA) y Vistas de administración dinámica (DMV) para ver las recomendaciones de tamaño   

El STA y las DMV ofrecen distintas opciones de herramientas y capacidades para determinar el tamaño de un grupo de bases de datos elásticas. Independientemente de la opción de herramienta usada, la estimación del tamaño solo debe usarse para la evaluación inicial y la creación de grupos de bases de datos elásticas. Una vez que se crea un grupo, el uso de recursos debe supervisarse con precisión y la configuración de rendimiento del grupo debe aumentarse o reducirse según sea necesario.

**STA**<br>STA es una herramienta integrada en el [Portal de Azure](https://portal.azure.com) que evalúa automáticamente el historial de uso de los recursos de bases de datos en un servidor de Base de datos SQL existente y recomienda una configuración apropiada para el grupo de bases de datos elásticas. Para obtener más información, vea [Recomendaciones de plan de tarifas de grupo de bases de datos elásticas](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations).

**Herramienta de determinación de tamaño de DMV**<br>La herramienta de determinación de tamaño de DMV se ofrece como script de PowerShell y permite personalizar las estimaciones del tamaño de un grupo de bases de datos elásticas para las bases de datos existentes de un servidor.

### Elección entre la herramienta DMV y STA 

Seleccione la herramienta apropiada para el análisis de la aplicación específica. La siguiente tabla resume las diferencias entre estas dos herramientas de establecimiento de tamaño:

| Capacidad | STA | DMV |
| :--- | :--- | :--- |
| Granularidad de las muestras de datos utilizadas para el análisis. | 15 segundos | 15 segundos |
| Tiene en cuenta las diferencias de precio entre un grupo y los niveles de rendimiento de bases de datos únicas. | Sí | No |
| Permite personalizar la lista de las bases de datos analizados dentro de un servidor. | No | Sí |
| Permite personalizar el período que se utiliza en el análisis. | No | Sí |


### Estimación del tamaño del grupo elástico mediante STA  

STA evalúa el historial de uso de las bases de datos y recomienda un grupo de bases de datos elásticas cuando sea más rentable que el uso de niveles de rendimiento para bases de datos individuales. Si se recomienda un grupo, la herramienta proporciona una lista de las bases de datos recomendadas, así como la cantidad recomendada de eDTU del grupo y la configuración de eDTU mín./máx. para cada base de datos elástica. Para que una base de datos se considere una candidata para un grupo, debe tener una existencia mínima de siete días.

STA está disponible en el portal cuando se agrega un grupo de bases de datos elásticas a un servidor existente. Si las recomendaciones para un grupo de bases de datos elásticas están disponibles para ese servidor, se muestran en la hoja de creación Grupo de bases de datos elásticas. Los clientes siempre pueden cambiar las configuraciones recomendadas para crear sus propios grupos de bases de datos elásticas.

Para obtener más información, vea [Recomendaciones de nivel de precios de grupo de bases de datos elásticas](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations).

### Estimación del tamaño de grupos elásticos con vista de administración dinámica (DMV) 

La DMV [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) mide el uso de recursos de una base de datos individual. Este DMV proporciona CPU, IO, registro y utilización de registros de una base de datos como un porcentaje del límite del nivel de rendimiento de la base de datos. Este dato puede usarse para calcular la utilización de DTU de una base de datos en un intervalo determinado de 15 segundos.

Se puede calcular el uso eDTU agregado de un grupo de bases de datos elásticas en un intervalo de 15 segundos agregando el uso de eDTU de todas las bases de datos candidatas durante ese período. Según los objetivos de rendimiento específicos, puede tener sentido descartar un pequeño porcentaje de datos de muestra. Por ejemplo, puede aplicarse un valor de percentil 99 de eDTU agregadas en todos los intervalos de tiempo para excluir los valores atípicos y proporcionar una eDTU de grupo de bases de datos elásticas que incluya el 99 % de los intervalos de tiempo muestreados.

## Script de PowerShell para calcular el uso de DTU agregadas de bases de datos

A continuación se ofrece un script de ejemplo de PowerShell para calcular los valores agregados de DTU en bases de datos de usuario de un servidor de Base de datos SQL.

El script solo recopila datos cuando está en ejecución. Para una carga de trabajo de producción típica, debe ejecutar el script para al menos un día, aunque una semana e incluso más ofrecerá probablemente una estimación más apropiada. Ejecute el script para una duración que represente la carga de trabajo típica de las bases de datos.

> [AZURE.IMPORTANT] Debe mantener la ventana de PowerShell abierta cuando ejecute el script. No cierre la ventana de PowerShell hasta que haya ejecutado el script durante una cantidad de tiempo suficiente y capturado suficientes datos para representar la carga de trabajo típica que divide los tiempos de uso normales y de picos.

### Requisitos previos de script 

Instale lo siguientes antes de ejecutar el script:

- Las últimas [herramientas de línea de comandos de Powershell](http://go.microsoft.com/?linkid=9811175&clcid=0x409).
- El [paquete de características de SQL Server 2014](https://www.microsoft.com/download/details.aspx?id=42295).


### Detalles del script


Puede ejecutar el script desde la máquina local o una máquina virtual en la nube. Cuando lo ejecute desde la máquina local, es posible que se produzcan cargos de salida de datos porque el script tiene que descargar datos de las bases de datos de destino. A continuación se muestra la estimación de volumen de datos según el número de bases de datos de destino y la duración de la ejecución del script. Para ver los costes de transferencia de datos de Azure, consulte [Detalles de precios de transferencia de datos](https://azure.microsoft.com/pricing/details/data-transfers/).
       
 -     1 base de datos por hora = 38 KB
 -     1 base de datos por día = 900 KB
 -     1 base de datos por semana = 6 MB
 -     100 bases de datos por día = 90 MB
 -     500 bases de datos por semana = 3 GB

El script excluye determinadas bases de datos que no son buenas candidatas para la oferta de vista previa pública actual del nivel de grupo elástico estándar. Si necesita excluir bases de datos adicionales del servidor de destino, puede cambiar el script para cumplir con los criterios. De forma predeterminado, el script no compila información para lo siguiente:

* Bases de datos elásticas (bases de datos ya en un grupo elástico).
* La base de datos maestra del servidor.

El script necesita una base de datos de salida para almacenar datos intermedios para el análisis. Puede usar una nueva base de datos o una existente. Aunque no se requiere técnica mente que la herramienta se ejecute, la base de datos de salida debe estar en un servidor distinto para evitar el impacto en el resultado del análisis. Sugiera que el nivel de rendimiento de la base de datos de salida sea al menos S0 o superior. Cuando recopile una duración larga de datos para un número grande de bases de datos, puede considerar actualizar la base de datos de salida a un nivel de rendimiento superior.

El script tiene que proporcionarle las credenciales para conectarse al servidor de destino (el candidato del grupo de bases de datos elásticas) con el nombre de servidor completo como “abcdef.database.windows.net”. Actualmente, el script no es compatible con el análisis de varios servidores a la vez.


Después de enviar valores para el conjunto inicial de parámetros, se le pedirá que inicie sesión en la cuenta de Azure. Esto es para iniciar sesión en el servidor de destino, no en el servidor de base de datos de salida.
	
Si detecta las siguientes advertencias cuando ejecute el script, puede ignorarlas:

- ADVERTENCIA: El cmdlet Switch-AzureMode está en desuso.
- ADVERTENCIA: No se pudo obtener la información de servicio de SQL Server. Se produjo un error al intentar conectar con VMI en 'Microsoft.Azure.Commands.Sql.dll' con el siguiente error: El servidor RPC no está disponible.

Cuando el script se completa, proporcionará el número estimado de eDTU necesarias para que un grupo elástico contenga todas las bases de datos candidatas en el servidor de destino. Esta DTU estimada puede usarse para crear y configurar un grupo de bases de datos elásticas que contenga estas bases de datos. Una vez que se crea el grupo y las bases de datos se trasladan al grupo, hay que supervisarlo estrechamente durante algunos días y deben realizarse los ajustes a la configuración de la eDTU de grupo que se requieran.

> [AZURE.IMPORTANT] Este script contiene comandos para las versiones 1.0 y posteriores (*pero no incluidas*) de Azure PowerShell. Puede comprobar la versión de Azure PowerShell con el comando **Get-Module azure | format-table version**. Para obtener más información detallada, consulte [Degradación del cmdlet Switch-AzureMode en Azure PowerShell](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell).


    
    param (
    [Parameter(Mandatory=$true)][string]$AzureSubscriptionName, # Azure Subscription name - can be found on the Azure portal: https://portal.azure.com/
    [Parameter(Mandatory=$true)][string]$ResourceGroupName, # Resource Group name - can be found on the Azure portal: https://portal.azure.com/
    [Parameter(Mandatory=$true)][string]$servername, # full server name like "abcdefg.database.windows.net"
    [Parameter(Mandatory=$true)][string]$username, # user name
    [Parameter(Mandatory=$true)][string]$serverPassword, # password
    [Parameter(Mandatory=$true)][string]$outputServerName, # metrics collection database for analysis. full server name like "zyxwvu.database.windows.net"
    [Parameter(Mandatory=$true)][string]$outputdatabaseName,
    [Parameter(Mandatory=$true)][string]$outputDBUsername,
    [Parameter(Mandatory=$true)][string]$outputDBpassword,
    [Parameter(Mandatory=$true)][int]$duration_minutes # How long to run. Recommend to run for the period of time when your typical workload is running. At least 10 mins.
    )
    
    Add-AzureAccount 
    Select-AzureSubscription $AzureSubscriptionName
    Switch-AzureMode AzureResourceManager
    
    $server = Get-AzureSqlServer -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
    
    # Check version/upgrade status of the server
    $upgradestatus = Get-AzureSqlServerUpgrade -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
    $version = ""
    if ([string]::IsNullOrWhiteSpace($server.ServerVersion)) 
    {
    $version = $upgradestatus.Status
    }
    else
    {
    $version = $server.ServerVersion
    }
    
    # For Elastic database pool candidates, we exclude master, and any databases that are already in a pool. You may add more databases to the excluded list below as needed
    $ListOfDBs = Get-AzureSqlDatabase -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName | Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool")}
    
    $outputConnectionString = "Data Source=$outputServerName;Integrated Security=false;Initial Catalog=$outputdatabaseName;User Id=$outputDBUsername;Password=$outputDBpassword"
    $destinationTableName = "resource_stats_output"
    
    # Create a table in output database for metrics collection
    $sql = "
    IF  NOT EXISTS (SELECT * FROM sys.objects 
    WHERE object_id = OBJECT_ID(N'$($destinationTableName)') AND type in (N'U'))
    
    BEGIN
    Create Table $($destinationTableName) (database_name varchar(128), slo varchar(20), end_time datetime, avg_cpu float, avg_io float, avg_log float, db_size float);
    Create Clustered Index ci_endtime ON $($destinationTableName) (end_time);
    END
    TRUNCATE TABLE $($destinationTableName);
    "
    Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 120 
    
    # waittime (minutes) is interval between data collection queries in the loop below.
    $Waittime = 10
    $end_Time = [DateTime]::UtcNow
    $start_time = $end_time.AddMinutes(-$Waittime)
    $finish_time = $end_Time.AddMinutes($duration_minutes)
    
    While ($end_time -lt $finish_time)
    {
    Write-Host "Collecting metrics..." 
    foreach ($db in $ListOfDBs)
    {
    if ($version -in ("12.0", "Completed")) # for V12 databases 
    {
    $sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
    $sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceLevelObjectiveName)';"
    $sql+= "
    Declare @DTU_cap int, @db_size float;
    Select @DTU_cap = CASE @SLO 
    WHEN 'Basic' THEN 5
    WHEN 'S0' THEN 10
    WHEN 'S1' THEN 20
    WHEN 'S2' THEN 50
    WHEN 'S3' THEN 100
    WHEN 'P1' THEN 125
    WHEN 'P2' THEN 250
    WHEN 'P3' THEN 1000
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 FROM sys.dm_db_partition_stats
    SELECT @dbname as database_name, @SLO, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
    as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
    WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
    " 
    }
    else
    {
    $sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
    $sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceLevelObjectiveName)';"
    $sql+= "
    Declare @DTU_cap int, @db_size float;
    Select @DTU_cap = CASE @SLO 
    WHEN 'Basic' THEN 5
    WHEN 'S0' THEN 10
    WHEN 'S1' THEN 20
    WHEN 'S2' THEN 50
    WHEN 'P1' THEN 100
    WHEN 'P2' THEN 200
    WHEN 'P3' THEN 800
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 from sys.dm_db_partition_stats
    SELECT @dbname as database_name, @SLO, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
    as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
    WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
    " 
    }
    
    $result = Invoke-Sqlcmd -ServerInstance $servername -Database $db.DatabaseName -Username $username -Password $serverPassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 3600 
    #bulk copy the metrics to output database
    $bulkCopy = new-object ("Data.SqlClient.SqlBulkCopy") $outputConnectionString 
    $bulkCopy.BulkCopyTimeout = 600
    $bulkCopy.DestinationTableName = "$destinationTableName";
    $bulkCopy.WriteToServer($result);
    
    }
    
    $start_time = $start_time.AddMinutes($Waittime)
    $end_time = $end_time.AddMinutes($Waittime)
    Write-Host $start_time
    Write-Host $end_time
    do {
    Start-Sleep 1
       }
    until (([DateTime]::UtcNow) -ge $end_time)
    }
    
    Write-Host "Analyzing the collected metrics...."
    # Analysis query that does aggregation of the resource metrics to calculate pool size.
    $sql1 = 'Declare @DTU_Perf_99 as float, @DTU_Storage as float;
    WITH group_stats AS
    (
    SELECT end_time, SUM(db_size) AS avg_group_Storage, SUM(avg_cpu) AS avg_group_cpu, SUM(avg_io) AS avg_group_io,SUM(avg_log) AS avg_group_log
    FROM resource_stats_output 
    WHERE slo LIKE '
    
    $sql2 = '
    GROUP BY end_time
    )
    -- calculate aggregate storage and DTUs for all DBs in the group
    , group_DTU AS
    (
    SELECT end_time, avg_group_Storage, 
    (SELECT Max(v)
       FROM (VALUES (avg_group_cpu), (avg_group_log), (avg_group_io)) AS value(v)) AS avg_group_DTU
    FROM group_stats
    )
    -- Get top 1 percent of the storage and DTU utilization samples.
    , top1_percent AS (
    SELECT TOP 1 PERCENT avg_group_Storage, avg_group_dtu FROM group_dtu ORDER BY [avg_group_DTU] DESC
    )
    
    -- Max and 99th percentile DTU for the given list of databases if converted into an elastic pool. Storage is increased by factor of 1.25 to accommodate for future growth. Currently storage limit of the pool is determined by the amount of DTUs based on 1GB/DTU.
    --SELECT MAX(avg_group_Storage)*1.25/1024.0 AS Group_Storage_DTU, MAX(avg_group_dtu) AS Group_Performance_DTU, MIN(avg_group_dtu) AS Group_Performance_DTU_99th_percentile FROM top1_percent;
    SELECT @DTU_Storage = MAX(avg_group_Storage)*1.25/1024.0, @DTU_Perf_99 = MIN(avg_group_dtu) FROM top1_percent;
    IF @DTU_Storage > @DTU_Perf_99 
    SELECT ''Total number of DTUs dominated by storage: '' + convert(varchar(100), @DTU_Storage)
    ELSE 
    SELECT ''Total number of DTUs dominated by resource consumption: '' + convert(varchar(100), @DTU_Perf_99)'
    
    #check if there are any web/biz edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'shared%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "Shared*")
    {
    write-host "`nWeb/Business edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'Shared%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]}
    }
    
    #check if there are any basic edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'Basic%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "Basic*")
    {
    write-host "`nBasic edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'Basic%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]} 
    }
    
    #check if there are any standard edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'S%' AND slo NOT LIKE 'Shared%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "S*")
    {
    write-host "`nStandard edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'S%' AND slo NOT LIKE 'Shared%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]}
    }
    
    #check if there are any premium edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'P%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "P*")
    {
    write-host "`nPremium edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'P%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]}
    }
        

## Resumen

No todas las bases de datos únicas son candidatas óptimas para los grupos de bases de datos elásticas. Las bases de datos con patrones de uso que se caractericen por una utilización media baja y picos de utilización relativamente poco frecuentes son candidatas excelentes para los grupos de bases de datos elásticas. Los patrones de uso de aplicaciones son dinámicos. Por lo tanto, use la información y las herramientas descritas en este artículo para realizar una evaluación inicial para ver si un grupo de bases de datos elásticas es una buena opción para algunas de las bases de datos o para todas. Este artículo es tan solo el punto de partida para ayudarle a tomar la decisión de si el grupo de bases de datos elásticas es apropiado para usted. Recuerde que debe seguir supervisando el historial de uso de recursos (con STA o DMV) y continuar reevaluando constantemente los niveles de rendimiento de todas las bases de datos. Tenga en cuenta que resulta muy fácil agregar bases de datos a grupos de bases de datos elásticas, así como quitarlas, y si dispone de un gran número de bases de datos, puede contar con varios grupos de distintos tamaños en los que puede dividir las bases de datos.



<!--Image references-->
[1]: ./media/sql-database-elastic-pool-guidance/one-database.png
[2]: ./media/sql-database-elastic-pool-guidance/four-databases.png
[3]: ./media/sql-database-elastic-pool-guidance/twenty-databases.png

<!---HONumber=AcomDC_0302_2016-->