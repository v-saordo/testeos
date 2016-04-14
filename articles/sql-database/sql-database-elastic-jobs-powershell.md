<properties 
	pageTitle="Creación y administración de trabajos de base de datos elástica mediante PowerShell" 
	description="PowerShell usada para administrar grupos de Base de datos SQL de Azure" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="ddove"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2016" 
	ms.author="ddove;sidneyh" />

# Creación y administración de un grupo de bases de datos SQL elásticas mediante PowerShell

> [AZURE.SELECTOR]
- [Azure Classic Portal](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)



Las API de PowerShell para **Trabajos de base de datos elástica** permtein definir el grupo de bases de datos en las que se ejecutarán los scripts. Este artículo muestra cómo crear y administrar **trabajos de base de datos elástica** mediante cmdlets de PowerShell. Consulte [Información general sobre trabajos elásticos](sql-database-elastic-jobs-overview.md).

## Requisitos previos
* Una suscripción de Azure. Para obtener una prueba gratuita, vea [Prueba gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).
* Un conjunto de bases de datos creadas con las herramientas de base de datos elástica. Consulte [Introducción a las herramientas de base de datos elástica](sql-database-elastic-scale-get-started.md).
* Azure PowerShell. Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).
* Paquete PowerShell para **Trabajos de base de datos elástica**: consulte [Instalación de Trabajos de base de datos elástica](sql-database-elastic-jobs-service-installation.md).

### Selección de su suscripción a Azure

Para seleccionar la suscripción, necesitará el identificador de la suscripción (**-SubscriptionId**) o el nombre de la suscripción (**-SubscriptionName**). Si dispone de varias suscripciones, puede ejecutar el cmdlet **Get-AzureSubscription** y copiar la información de la suscripción que quiera del conjunto de resultados. Cuando tenga la información de la suscripción, ejecute el siguiente cmdlet para establecer esta suscripción como predeterminada, es decir, el destino para crear y administrar trabajos:

	Select-AzureSubscription -SubscriptionId {SubscriptionID}

Se recomienda el uso de [PowerShell ISE](https://technet.microsoft.com/library/dd315244.aspx) para desarrollar y ejecutar scripts de PowerShell en Trabajos de base de datos elástica.

## Objetos de Trabajos de base de datos elástica

En la tabla siguiente se enumeran todos los tipos de objetos de **Trabajos de base de datos elástica** con su descripción y las API de PowerShell relevantes.

<table style="width:100%">
  <tr>
    <th>Tipo de objeto</th>
    <th>Descripción</th>
    <th>API de PowerShell relacionadas</th>
  </tr>
  <tr>
    <td>Credential:</td>
    <td>Nombre de usuario y contraseña que se usará al conectarse a bases de datos para la ejecución de scripts o la aplicación de archivos DACPAC. <p>La contraseña se cifra antes de enviarla y almacenarla en la base de datos de Trabajos de base de datos elástica. El servicio Trabajos de base de datos elástica descifra la contraseña a través de la credencial que se creó y cargó desde el script de instalación.</td>
	<td><p>Get-AzureSqlJobCredential</p>
	<p>New-AzureSqlJobCredential</p><p>Set-AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Script</td>
    <td>Script de T-SQL que se va a usar para la ejecución transversal en las bases de datos El script tiene que crearse para que sea idempotente, puesto que el servicio volverá a intentar la ejecución del script tras errores.
	</td>
	<td>
	<p>Get-AzureSqlJobContent</p>
	<p>Get-AzureSqlJobContentDefinition</p>
	<p>New-AzureSqlJobContent</p>
	<p>Set-AzureSqlJobContentDefinition</p>
	</td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td>Paquete de <a href="https://msdn.microsoft.com/library/ee210546.aspx">aplicación de capa de datos</a> que se va a aplicar transversalmente a las bases de datos.

	</td>
	<td>
	<p>Get-AzureSqlJobContent</p>
	<p>New-AzureSqlJobContent</p>
	<p>Set-AzureSqlJobContentDefinition</p>
	</td>
  </tr>
  <tr>
    <td>Destino de la base de datos</td>
    <td>Nombre del servidor y la base de datos que apuntan a una Base de datos SQL de Azure.

	</td>
	<td>
	<p>Get-AzureSqlJobTarget</p>
	<p>New-AzureSqlJobTarget</p>
	</td>
  </tr>
  <tr>
    <td>Destino de mapa de particiones</td>
    <td>Combinación de un destino de base de datos y una credencial que se usará para determinar la información almacenada en un mapa de particiones de base de datos elástica.
	</td>
	<td>
	<p>Get-AzureSqlJobTarget</p>
	<p>New-AzureSqlJobTarget</p>
	<p>Set-AzureSqlJobTarget</p>
	</td>
  </tr>
<tr>
    <td>Destino de colección personalizada</td>
    <td>Grupo de bases de datos que se define para usar colectivamente en la ejecución.</td>
	<td>
	<p>Get-AzureSqlJobTarget</p>
	<p>New-AzureSqlJobTarget</p>
	</td>
  </tr>
<tr>
    <td>Destino secundario de colección personalizada</td>
    <td>Destino de base de datos al que se hace referencia en una colección personalizada.</td>
	<td>
	<p>Add-AzureSqlJobChildTarget</p>
	<p>Remove-AzureSqlJobChildTarget</p>
	</td>
  </tr>

<tr>
    <td>Trabajo</td>
    <td>
	<p>Definición de parámetros para un trabajo que puede usarse para desencadenar la ejecución o para cumplir una programación.</p>
	</td>
	<td>
	<p>Get-AzureSqlJob</p>
	<p>New-AzureSqlJob</p>
	<p>Set-AzureSqlJob</p>
	</td>
  </tr>

<tr>
    <td>Ejecución de trabajos</td>
    <td>
	<p>Contenedor de las tareas necesarias para cumplir la ejecución de un script o para aplicar un DACPAC a un destino con las credenciales para las conexiones de base de datos, donde los errores se controlan según una directiva de ejecución.</p>
	</td>
	<td>
	<p>Get-AzureSqlJobExecution</p>
	<p>Start-AzureSqlJobExecution</p>
	<p>Stop-AzureSqlJobExecution</p>
	<p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Ejecución de tareas de trabajo</td>
    <td>
	<p>Unidad funcional para completar un trabajo.</p>
	<p>Si una tarea de trabajo no se puede ejecutar correctamente, se registrará el mensaje de excepción resultante y se creará una nueva tarea de trabajo coincidente que se ejecutará según la directiva de ejecución especificada.</p></p>
	</td>
	<td>
	<p>Get-AzureSqlJobExecution</p>
	<p>Start-AzureSqlJobExecution</p>
	<p>Stop-AzureSqlJobExecution</p>
	<p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Directiva de ejecución de trabajos</td>
    <td>
	<p>Controla los tiempos de espera, los límites de reintentos y los intervalos entre reintentos de la ejecución de trabajos.</p>
	<p>Trabajos de base de datos elástica incluye una directiva de ejecución de trabajos predeterminada que, básicamente, producirá un número infinito de reintentos de errores de tareas de trabajo con retroceso exponencial de intervalos entre reintentos.</p>
	</td>
	<td>
	<p>Get-AzureSqlJobExecutionPolicy</p>
	<p>New-AzureSqlJobExecutionPolicy</p>
	<p>Set-AzureSqlJobExecutionPolicy</p>
	</td>
  </tr>

<tr>
    <td>Schedule</td>
    <td>
	<p>Especificación de tareas de duración definida para que la ejecución se produzca en un intervalo recurrente o en un momento dado.</p>
	</td>
	<td>
	<p>Get-AzureSqlJobSchedule</p>
	<p>New-AzureSqlJobSchedule</p>
	<p>Set-AzureSqlJobSchedule</p>
	</td>
  </tr>

<tr>
    <td>Desencadenadores de trabajo</td>
    <td>
	<p>Asignación entre un trabajo y una programación que desencadena la ejecución del trabajo según la programación.</p>
	</td>
	<td>
	<p>New-AzureSqlJobTrigger</p>
	<p>Remove-AzureSqlJobTrigger</p>
	</td>
  </tr>
</table>

## Tipos de grupo de trabajos de base de datos elástica admitidos
El trabajo ejecuta scripts de Transact-SQL (T-SQL) o la aplicación de archivos DACPAC en un grupo de bases de datos. Cuando se envía un trabajo para que se ejecute en un grupo de bases de datos, Trabajos de base de datos elástica se “expande” en trabajos secundarios, y cada uno realiza la ejecución solicitada en una sola base de datos del grupo.
 
Hay dos tipos de grupos que puede crear:

* Grupo [Mapa de particiones](sql-database-elastic-scale-shard-map-management.md): cuando se envía un trabajo con destino a un mapa de particiones, el trabajo consulta primero el mapa de particiones para determinar su conjunto actual de particiones y luego crea trabajos secundarios para cada partición del mapa de particiones.
* Grupo Colección personalizada: conjunto personalizado de bases de datos. Cuando un trabajo está destinado a una colección personalizada, crea trabajos secundarios para cada base de datos de la colección personalizada.

## Para establecer la conexión de Trabajos de base de datos elástica

Se debe establecer una conexión con la *base de datos de control* de trabajos antes de usar las API de trabajos. Al ejecutar este cmdlet se desencadena una ventana de credenciales emergente que solicita el nombre de usuario y la contraseña creados al instalar Trabajos de base de datos elástica. En todos los ejemplos que se ofrecen en este tema se supone que este primer paso ya se realizó.

Apertura de una conexión a Trabajos de base de datos elástica:

	Use-AzureSqlJobConnection -CurrentAzureSubscription 

## Credenciales cifradas en el servicio Trabajos de base de datos elástica

Las credenciales de la base de datos se pueden insertar en la *base de datos de control* de trabajos con su contraseña cifrada. Es preciso almacenar las credenciales para que los trabajos se puedan ejecutar después (mediante programaciones de trabajos).
 
El cifrado funciona a través de un certificado creado como parte del script de instalación. El script de instalación crea y carga el certificado en el servicio en la nube de Azure para descifrar las contraseñas almacenadas que están cifradas. Más adelante, el servicio en la nube de Azure almacena la clave pública en la *base de datos de control* de trabajos, lo que permite que la API de PowerShell o la interfaz del Portal de Azure clásico cifren una contraseña proporcionada sin que el certificado tenga que estar instalado localmente.
 
Las contraseñas de credenciales se cifran y se protegen de los usuarios mediante el acceso de solo lectura a los objetos de Trabajos de base de datos elástica. Pero es posible que usuarios malintencionados con acceso de lectura y escritura a los objetos de Trabajos de base de datos elástica extraigan una contraseña. Las credenciales están diseñadas para su reutilización entre ejecuciones de trabajos. Las credenciales se pasan a las bases de datos de destino al establecer conexiones. Como actualmente no hay ninguna restricción en las bases de datos de destino que se usan por cada credencial, un usuario malintencionado podría agregar como destino una base de datos que esté bajo el control del usuario malintencionado. Posteriormente, el usuario podría iniciar un trabajo destinado a esta base de datos para obtener la contraseña de la credencial.

Los procedimientos recomendados de seguridad para Trabajos de base de datos elástica son:

* Limitar el uso de las API a las personas de confianza.
* Las credenciales deben tener los privilegios mínimos necesarios para realizar la tarea de trabajo. Puede ver más información en este artículo de MSDN sobre SQL Server, [Autorización y permisos](https://msdn.microsoft.com/library/bb669084.aspx).

### Para crear una credencial cifrada para la ejecución de trabajos en bases de datos

Para crear una nueva credencial cifrada, el cmdlet [**Get-Credential**](https://technet.microsoft.com/library/hh849815.aspx) pedirá un nombre de usuario y una contraseña que pueda pasarse al cmdlet [**New-AzureSqlJobCredential**](https://msdn.microsoft.com/library/mt346063.aspx).

	$credentialName = "{Credential Name}"
	$databaseCredential = Get-Credential
	$credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
	Write-Output $credential

### Para actualizar las credenciales

Cuando las contraseñas cambian, use el cmdlet [**Set-AzureSqlJobCredential**](https://msdn.microsoft.com/library/mt346062.aspx) y establezca el parámetro **CredentialName**.

	$credentialName = "{Credential Name}"
	Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## Para definir un mapa de particiones de base de datos elástica de destino

Para ejecutar un trabajo en todas las bases de datos de un conjunto de particiones (creado con la [biblioteca cliente de Base de datos elástica](sql-database-elastic-database-client-library.md)), use un mapa de particiones como base de datos de destino. Este ejemplo requiere crear una aplicación con particiones con la biblioteca cliente de Base de datos elástica. Consulte [Introducción a las herramientas de Base de datos elástica](sql-database-elastic-scale-get-started.md).

###Creación de un administrador de mapas de particiones con la aplicación de ejemplo

En este ejemplo se crea un administrador de mapas de particiones junto con varias particiones y se insertan datos en las particiones.

1. Cree y ejecute la aplicación de ejemplo de **Introducción a las herramientas de base de datos elástica**. Siga los pasos hasta el paso 7 de la sección [Descarga y ejecución de la aplicación de ejemplo](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools). Al final del paso 7, verá la siguiente línea de comandos:

	![símbolo del sistema][1]

2.  En la ventana de comandos, escriba "1" y pulse **Entrar**. De esta forma, se creará el administrador de mapas de particiones y se agregarán dos particiones al servidor. A continuación, escriba "3" y pulse **Entrar**; repita la acción cuatro veces. De esta forma, se insertan las filas de datos de ejemplo en sus particiones.
  
3.  El [Portal de Azure](https://portal.azure.com) debe mostrar tres nuevas bases de datos en el servidor v12:

	![Confirmación de Visual Studio][2]

Cree un destino del mapa de particiones con el cmdlet [**New-AzureSqlJobCredential**](https://msdn.microsoft.com/library/mt346063.aspx). Se debe establecer la base de datos de administrador del mapa de particiones como base de datos de destino y luego especificar ese mapa de particiones como destino.

	$shardMapCredentialName = "{Credential Name}"
	$shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
	$shardMapDatabaseServerName = "{ShardMapServerName}"
	$shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
	$shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
	$shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
	Write-Output $shardMapTarget

## Crear un script T-SQL para su ejecución transversal en las bases de datos

Al crear scripts de T-SQL para su ejecución, es muy recomendable hacerlo de forma que sean [idempotentes](https://en.wikipedia.org/wiki/Idempotence) y resistentes a los errores. Trabajos de base de datos elástica volverá a intentar la ejecución de un script cada vez que la ejecución encuentra un error, independientemente de la clasificación del error.

Use el cmdlet [**New-AzureSqlJobContent**](https://msdn.microsoft.com/library/mt346085.aspx) para crear y guardar un script de ejecución y establezca los parámetros **-ContentName** y **-CommandText**.

	$scriptName = "Create a TestTable"

	$scriptCommandText = "
	IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
	BEGIN
		CREATE TABLE TestTable(
			TestTableId INT PRIMARY KEY IDENTITY,
			InsertionTime DATETIME2
		);
	END
	GO
	INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
	GO"

	$script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
	Write-Output $script

### Creación de un script desde un archivo
Si el script de T-SQL se define dentro de un archivo, use esto para importar el script:

	$scriptName = "My Script Imported from a File"
	$scriptPath = "{Path to SQL File}"
	$scriptCommandText = Get-Content -Path $scriptPath
	$script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
	Write-Output $script

### Para actualizar un script de T-SQL para su ejecución en distintas bases de datos  

El siguiente script de PowerShell actualiza el texto de los comandos T-SQL en un script existente.

Establecimiento de las siguientes variables para que reflejen la definición que se quiera del script que se va a establecer:

	$scriptName = "Create a TestTable"
	$scriptUpdateComment = "Adding AdditionalInformation column to TestTable"
	$scriptCommandText = "
	IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
	BEGIN
	CREATE TABLE TestTable(
		TestTableId INT PRIMARY KEY IDENTITY,
		InsertionTime DATETIME2
	);
	END
	GO

	IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
	BEGIN
    ALTER TABLE TestTable
    ADD AdditionalInformation NVARCHAR(400);
	END
	GO

	INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
	GO"

### Para actualizar la definición de un script existente

	Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

## Para crear un trabajo que ejecute un script en un mapa de particiones

El siguiente script de PowerShell inicia un trabajo para la ejecución de un script en cada partición de un mapa de particiones de escala elástica.

Establecimiento de las siguientes variables para que reflejen el script y el destino que se quiera:

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$shardMapServerName = "{Shard Map Server Name}"
	$shardMapDatabaseName = "{Shard Map Database Name}"
	$shardMapName = "{Shard Map Name}"
	$credentialName = "{Credential Name}"
	$shardMapTarget = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName 
	$job = New-AzureSqlJob -ContentName $scriptName -CredentialName $credentialName -JobName $jobName -TargetId $shardMapTarget.TargetId
	Write-Output $job

## Para ejecutar un trabajo 

Este script de PowerShell ejecuta un trabajo existente:

Actualice la variable siguiente para que refleje el nombre del trabajo que se quiera ejecutar:

	$jobName = "{Job Name}"
	$jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
	Write-Output $jobExecution
 
## Para recuperar el estado de ejecución de un único trabajo

Use el cmdlet [**Get-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346058.aspx) y establezca el parámetro **JobExecutionId** para ver el estado de una ejecución de trabajos.

	$jobExecutionId = "{Job Execution Id}"
	$jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
	Write-Output $jobExecution

Use el mismo cmdlet **Get-AzureSqlJobExecution** con el parámetro **IncludeChildren** para ver el estado de ejecuciones de trabajos secundarios, es decir, el estado específico de cada ejecución en cada base de datos destino del trabajo.

	$jobExecutionId = "{Job Execution Id}"
	$jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
	Write-Output $jobExecutions 

## Para ver el estado de varias ejecuciones de trabajos

El cmdlet [**Get-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346058.aspx) tiene varios parámetros opcionales que sirven para mostrar varias ejecuciones del trabajo, filtradas por los parámetros proporcionados. Aquí mostramos algunas de las posibles formas de usar Get-AzureSqlJobExecution:

Recuperar todas las ejecuciones de trabajos activos de nivel superior:

	Get-AzureSqlJobExecution

Recuperar todas las ejecuciones de trabajos de nivel superior, incluidas las ejecuciones de trabajos inactivos:

	Get-AzureSqlJobExecution -IncludeInactive

Recuperar todas las ejecuciones de trabajos secundarios de un identificador de ejecución de trabajos proporcionado, incluidas las ejecuciones de trabajos inactivos:

	$parentJobExecutionId = "{Job Execution Id}"
	Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId –IncludeInactive -IncludeChildren

Recuperar todas las ejecuciones de trabajos creadas con una combinación de programación y trabajo, incluidos los trabajos inactivos:

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Recuperar todos los trabajos que se destinan a un mapa de particiones especificado, incluidos los trabajos inactivos:

	$shardMapServerName = "{Shard Map Server Name}"
	$shardMapDatabaseName = "{Shard Map Database Name}"
	$shardMapName = "{Shard Map Name}"
	$target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
	Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive

Recuperar todos los trabajos que se destinan a una colección personalizada especificada, incluidos los trabajos inactivos:

	$customCollectionName = "{Custom Collection Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive
 
Recuperar la lista de ejecuciones de tareas de trabajos dentro de la ejecución de un trabajo específico:

	$jobExecutionId = "{Job Execution Id}"
	$jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
	Write-Output $jobTaskExecutions 

Recuperar los detalles de ejecución de tareas de trabajo:

Puede usar este script de PowerShell para ver los detalles de una ejecución de tareas de trabajo, lo que resulta especialmente útil al depurar errores de ejecución.

	$jobTaskExecutionId = "{Job Task Execution Id}"
	$jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
	Write-Output $jobTaskExecution

## Para recuperar errores dentro de las ejecuciones de tareas de trabajo

El objeto **JobTaskExecution** incluye una propiedad para el ciclo de vida de la tarea y una propiedad de mensaje. Si no se realiza correctamente la ejecución de tareas de un trabajo, la propiedad de ciclo de vida se establecerá en *Failed* y la propiedad de mensaje se establecerá en el mensaje de excepción resultante y la pila. Si un trabajo no se realiza correctamente, es importante ver los detalles de las tareas de trabajo que no se realizaron correctamente en un trabajo determinado.

	$jobExecutionId = "{Job Execution Id}"
	$jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
	Foreach($jobTaskExecution in $jobTaskExecutions) 
		{
		if($jobTaskExecution.Lifecycle -ne 'Succeeded')
    		{
        	Write-Output $jobTaskExecution
    		}
		}

## Para esperar a que se complete la ejecución de un trabajo

Este script de PowerShell sirve para esperar a que una tarea de trabajo se complete:

	$jobExecutionId = "{Job Execution Id}"
	Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## Crear una directiva de ejecución personalizada

Trabajos de base de datos elástica admite la creación de directivas de ejecución personalizadas que se pueden aplicar al iniciar trabajos.
  
Actualmente, las directivas de ejecución permiten definir:

* Nombre: identificador de la directiva de ejecución.
* Tiempo de espera del trabajo: tiempo total antes de que Trabajos de base de datos elástica cancele un trabajo.
* Intervalo de reintento inicial: intervalo de espera antes del primer reintento.
* Intervalo máximo de reintento: límite de intervalos de reintento que se usan.
* Coeficiente de retroceso de intervalo de reintento: coeficiente que se usa para calcular el siguiente intervalo entre reintentos. Se usa la siguiente fórmula: (intervalo de reintento inicial) * Math.pow ((coeficiente de retroceso de intervalo), (número de intentos de) - 2). 
* Número máximo de intentos: número máximo de reintentos para llevar a cabo un trabajo.

La directiva de ejecución predeterminada usa los valores siguientes:

* Nombre: directiva de ejecución predeterminada
* Tiempo de espera del trabajo: 1 semana
* Intervalo de reintento inicial: 100 milisegundos
* Intervalo máximo de reintento: 30 minutos
* Coeficiente de intervalo de reintento: 2
* Número máximo de intentos: 2.147.483.647

Crear la directiva de ejecución que quiera:

	$executionPolicyName = "{Execution Policy Name}"
	$initialRetryInterval = New-TimeSpan -Seconds 10
	$jobTimeout = New-TimeSpan -Minutes 30
	$maximumAttempts = 999999
	$maximumRetryInterval = New-TimeSpan -Minutes 1
	$retryIntervalBackoffCoefficient = 1.5
	$executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval 
	-RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
	Write-Output $executionPolicy

### Actualización de una directiva de ejecución personalizada

Actualizar la directiva de ejecución que se quiere actualizar:

	$executionPolicyName = "{Execution Policy Name}"
	$initialRetryInterval = New-TimeSpan -Seconds 15
	$jobTimeout = New-TimeSpan -Minutes 30
	$maximumAttempts = 999999
	$maximumRetryInterval = New-TimeSpan -Minutes 1
	$retryIntervalBackoffCoefficient = 1.5
	$updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
	Write-Output $updatedExecutionPolicy
 
## Cancelación de un trabajo

Trabajos de base de datos elástica admite solicitudes de cancelación de trabajos. Si Trabajos de base de datos elástica detecta una solicitud de cancelación para un trabajo que está ejecutándose en ese momento, intentará detener el trabajo.

Trabajos de base de datos elástica puede realizar una cancelación de dos formas distintas:

1. Cancelar las tareas actualmente en ejecución: si se detecta una cancelación mientras se ejecuta una tarea, se intentará cancelar el aspecto de la tarea que se está ejecutando actualmente. Por ejemplo: si hay una consulta de larga ejecución en curso en el momento en que se intenta realizar una cancelación, se intentará cancelar la consulta.
2. Cancelación de reintentos de tareas: si el subproceso de control detecta una cancelación antes de iniciar una tarea para su ejecución, evitará iniciar la tarea y declarará cancelada la solicitud.

Si se solicita una cancelación de trabajo para un trabajo primario, se respetará la solicitud de cancelación para el trabajo primario y todos los trabajos secundarios.
 
Para enviar una solicitud de cancelación, use el cmdlet [**Stop-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346053.aspx) y establezca el parámetro **JobExecutionId**.

	$jobExecutionId = "{Job Execution Id}"
	Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## Para eliminar un trabajo y el historial de trabajos de forma asincrónica

Trabajos de base de datos elástica admite la eliminación asincrónica de trabajos. Un trabajo se puede marcar para su eliminación y el sistema eliminará el trabajo y su historial de trabajos una vez completadas todas las ejecuciones de trabajos para ese trabajo. El sistema no cancelará automáticamente las ejecuciones de trabajos activos.

Invoque a [**Stop-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346053.aspx) para cancelar las ejecuciones de trabajos activas.

Para desencadenar la eliminación del trabajo, use el cmdlet [**Remove-AzureSqlJob**](https://msdn.microsoft.com/library/mt346083.aspx) y establezca el parámetro **JobName**.

	$jobName = "{Job Name}"
	Remove-AzureSqlJob -JobName $jobName
 
## Para crear una base de datos de destino personalizada

Puede definir bases de datos de destino personalizadas para la ejecución directa o para su inclusión en un grupo personalizado de bases de datos. Por ejemplo, como los **grupos de bases de datos elásticas** todavía no se admiten directamente mediante las API de PowerShell, puede crear una base de datos personalizada como destino y una colección de bases de datos personalizada como destino que englobe todas las bases de datos del grupo.

Establecimiento de las siguientes variables para que reflejen la información de base de datos que se quiera:

	$databaseName = "{Database Name}"
	$databaseServerName = "{Server Name}"
	New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## Para crear una colección de bases de datos de destino personalizada

Use el cmdlet [**New-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) para definir una colección de bases de datos personalizada de destino para permitir la ejecución en varias bases de datos definidas como destino. Después de crear un grupo de bases de datos, las bases de datos se pueden asociar con la colección personalizada de destino.

Establecimiento de las siguientes variables para que reflejen la configuración de destino de la colección personalizada que se quiera:

	$customCollectionName = "{Custom Database Collection Name}"
	New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### Para agregar bases de datos a una colección de bases de datos personalizada de destino

Para agregar una base de datos a una colección personalizada específica, use el cmdlet [**Add-AzureSqlJobChildTarget**](https://msdn.microsoft.comlibrary/mt346064.aspx).

	$serverName = "{Database Server Name}"
	$databaseName = "{Database Name}"
	$customCollectionName = "{Custom Database Collection Name}"
	Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### Revisión de las bases de datos incluidas en un destino de colección de bases de datos personalizada

Use el cmdlet [**Get-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) para recuperar las bases de datos secundarias en un destino de la colección de bases de datos personalizada.
 
	$customCollectionName = "{Custom Database Collection Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
	Write-Output $childTargets

### Creación de un trabajo para ejecutar un script transversalmente en un destino de colección de bases de datos personalizada

Use el cmdlet [**New-AzureSqlJob**](https://msdn.microsoft.com/library/mt346078.aspx) para crear un trabajo en un grupo de bases de datos definido por un destino de la colección de base de datos personalizada. Trabajos de base de datos elástica expande el trabajo en varios trabajos secundarios, cada uno correspondiente a una base de datos asociada al destino de la colección de bases de datos personalizada y garantiza que el script se ejecuta en cada una de las bases de datos. De nuevo, es importante que los scripts sean idempotentes para que sean resistentes a los reintentos.

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$customCollectionName = "{Custom Collection Name}"
	$credentialName = "{Credential Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
	Write-Output $job

## Recopilación de datos de una base de datos a otra

Puede usar un trabajo para ejecutar una consulta en un grupo de bases de datos y enviar los resultados a una tabla específica. La tabla se puede consultar a posteriori para ver los resultados de la consulta de cada base de datos. Esto ofrece un mecanismo asincrónico para ejecutar una consulta en numerosas bases de datos. Los intentos incorrectos se controlan automáticamente mediante reintentos.

La tabla de destino especificada se creará automáticamente si todavía no existe. La nueva tabla coincide con el esquema del conjunto de resultados devuelto. Si un script devuelve varios conjuntos de resultados, Trabajos de base de datos elástica solo enviará el primero a la tabla de destino.

El siguiente script de PowerShell ejecuta un script y recopila los resultados en una tabla especificada. Este script presupone que se creó un script T-SQL que genera un único conjunto de resultados y que se creó una colección de bases de datos personalizada de destino.

Este script usa el cmdlet [**Get-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx). Establezca los parámetros del script, las credenciales y el destino de ejecución:

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$executionCredentialName = "{Execution Credential Name}"
	$customCollectionName = "{Custom Collection Name}"
	$destinationCredentialName = "{Destination Credential Name}"
	$destinationServerName = "{Destination Server Name}"
	$destinationDatabaseName = "{Destination Database Name}"
	$destinationSchemaName = "{Destination Schema Name}"
	$destinationTableName = "{Destination Table Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName

### Para crear e iniciar un trabajo en escenarios de recolección de datos

Este script usa el cmdlet [**Start-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346055.aspx).
 
	$job = New-AzureSqlJob -JobName $jobName 
	-CredentialName $executionCredentialName 
	-ContentName $scriptName 
	-ResultSetDestinationServerName $destinationServerName 
	-ResultSetDestinationDatabaseName $destinationDatabaseName 
	-ResultSetDestinationSchemaName $destinationSchemaName 
	-ResultSetDestinationTableName $destinationTableName 
	-ResultSetDestinationCredentialName $destinationCredentialName 
	-TargetId $target.TargetId
	Write-Output $job
	$jobExecution = Start-AzureSqlJobExecution -JobName $jobName
	Write-Output $jobExecution

## Para programar un desencadenador de ejecución de trabajos

El siguiente script de PowerShell se usa para crear una programación periódica. Este script usa un intervalo de minutos, pero [**New-AzureSqlJobSchedule**](https://msdn.microsoft.com/library/mt346068.aspx) también admite los parámetros -DayInterval, -HourInterval, -MonthInterval y -WeekInterval. Se pueden crear programaciones que se ejecutan una sola vez pasando -OneTime.

Creación de una programación:

	$scheduleName = "Every one minute"
	$minuteInterval = 1
	$startTime = (Get-Date).ToUniversalTime()
	$schedule = New-AzureSqlJobSchedule 
    -MinuteInterval $minuteInterval 
	-ScheduleName $scheduleName 
	-StartTime $startTime 
	Write-Output $schedule

### Para desencadenar un trabajo para que se ejecute a la hora programada

Se puede definir un desencadenador de trabajo para que un trabajo se ejecute según una programación de tiempo. El siguiente script de PowerShell sirve para crear un desencadenador de trabajo.

Use [New-AzureSqlJobTrigger](https://msdn.microsoft.com/library/mt346069.aspx) y establezca las siguientes variables para que se correspondan con el trabajo y la programación deseados:

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	$jobTrigger = New-AzureSqlJobTrigger
	-ScheduleName $scheduleName
	–JobName $jobName
	Write-Output $jobTrigger

### Para quitar una asociación programada y detener la ejecución de un trabajo a la hora programada

Para suspender la ejecución de un trabajo recurrente a través de un desencadenador de trabajo, se puede quitar el desencadenador de trabajo. Quite un desencadenador de trabajo para detener un trabajo que se ejecute según una programación con el cmdlet [**Remove-AzureSqlJobTrigger**](https://msdn.microsoft.com/library/mt346070.aspx).

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	Remove-AzureSqlJobTrigger 
	-ScheduleName $scheduleName 
	-JobName $jobName

### Recuperación de desencadenadores de trabajo enlazados a una programación de tiempo

El siguiente script de PowerShell sirve para obtener y mostrar los desencadenadores de trabajo registrados para una programación de tiempo determinada.

	$scheduleName = "{Schedule Name}"
	$jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
	Write-Output $jobTriggers

### Para recuperar los desencadenadores enlazados a un trabajo 

Use [Get-AzureSqlJobTrigger](https://msdn.microsoft.com/library/mt346067.aspx) para obtener y mostrar las programaciones que contienen un trabajo registrado.

	$jobName = "{Job Name}"
	$jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
	Write-Output $jobTriggers

## Para crear una aplicación de capa de datos (DACPAC) para su ejecución en bases de datos

Para crear una DACPAC, consulte [Aplicaciones de capa de datos](https://msdn.microsoft.com/library/ee210546.aspx). Para implementar una DACPAC, use el [cmdlet New-AzureSqlJobContent](https://msdn.microsoft.com/library/mt346085.aspx). La DACPAC debe ser accesible para el servicio. Se recomienda cargar una DACPAC creada en Almacenamiento de Azure y crear una [firma de acceso compartido](../storage/storage-dotnet-shared-access-signature-part-1.md) para la DACPAC.

	$dacpacUri = "{Uri}"
	$dacpacName = "{Dacpac Name}"
	$dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
	Write-Output $dacpac

### Para actualizar una aplicación de capa de datos (DACPAC) para su ejecución en bases de datos

Las DACPAC existentes que se registren en Trabajos de base de datos elástica pueden actualizarse para que señalen a nuevos URI. Use el [**cmdlet Set-AzureSqlJobContentDefinition**](https://msdn.microsoft.com/library/mt346074.aspx) para actualizar el URI de una DACPAC registrada:

	$dacpacName = "{Dacpac Name}"
	$newDacpacUri = "{Uri}"
	$updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
	Write-Output $updatedDacpac

## Para crear un trabajo para aplicar una aplicación de capa de datos (DACPAC) en bases de datos

Una vez creada una DACPAC en Trabajos de base de datos elástica, puede crearse un trabajo que aplique transversalmente la DACPAC en un grupo de bases de datos. El siguiente script de PowerShell sirve para crear un trabajo DACPAC transversalmente en una colección personalizada de bases de datos:

	$jobName = "{Job Name}"
	$dacpacName = "{Dacpac Name}"
	$customCollectionName = "{Custom Collection Name}"
	$credentialName = "{Credential Name}"
	$target = Get-AzureSqlJobTarget 
	-CustomCollectionName $customCollectionName
	$job = New-AzureSqlJob 
	-JobName $jobName 
	-CredentialName $credentialName 
	-ContentName $dacpacName -TargetId $target.TargetId
	Write-Output $job 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->

<!---HONumber=AcomDC_0204_2016-->