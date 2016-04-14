<properties 
	pageTitle="Código de archivo de evento de XEvent para Base de datos SQL | Microsoft Azure" 
	description="Brinda PowerShell y Transact-SQL para un ejemplo de código de dos fases que muestra el destino de archivo de evento en un evento extendido en Base de datos SQL de Azure. Almacenamiento de Azure es una parte necesaria en este escenario." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor="" 
	tags=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/09/2015" 
	ms.author="genemi"/>


# Código de destino del archivo de evento para eventos extendidos en Base de datos SQL


Desea tener un ejemplo de código completo de una manera eficaz para capturar y notificar información de un evento extendido.


En Microsoft SQL Server, el [destino de archivo de evento](http://msdn.microsoft.com/library/ff878115.aspx) se usa para almacenar los resultados de un evento en un archivo del disco duro local. Sin embargo, esos archivos no están disponibles para Base de datos SQL de Azure. En su lugar, usamos el servicio de Almacenamiento de Azure para admitir el destino de archivo de evento.


Este tema presenta un ejemplo de código de dos fases:


- PowerShell, para crear un contenedor de Almacenamiento de Azure en la nube.

- Transact-SQL:
 - Para asignar el contenedor de Almacenamiento de Azure a un destino de archivo de evento.
 - Para crear e iniciar la sesión del evento, etc.


## Requisitos previos


- Una cuenta y una suscripción de Azure. Puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).


- Cualquier base de datos donde pueda crear una tabla.
 - De manera opcional, puede [crear una base de datos de **AdventureWorksLT** de demostración](sql-database-get-started.md) en cuestión de minutos.


- SQL Server Management Studio (ssms.exe), la versión preliminar de agosto de 2015 o una versión posterior. Puede descargar la versión más reciente de ssms.exe desde:
 - El tema titulado [Descarga de SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).
 - [Un vínculo directo a la descarga.](http://go.microsoft.com/fwlink/?linkid=616025)
 - Microsoft recomienda actualizar periódicamente el archivo ssms.exe. En algunos casos, ssms.exe se actualizará de manera mensual.


- Debe tener instalados los [módulos de Azure PowerShell](http://go.microsoft.com/?linkid=9811175).
 - Los módulos brindan comandos como - **New-AzureStorageAccount**.


## Fase 1: código de PowerShell para el contenedor de Almacenamiento de Azure


Esta es la fase 1 del ejemplo de código de dos fases.

El script comienza con comandos que realizan un limpieza después de una posible ejecución anterior y está diseñado para volverse a ejecutar.



1. Pegue el script de PowerShell en un editor de texto simple, como Notepad.exe, y guárdelo como archivo con extensión **.ps1**.

2. Inicie PowerShell ISE como administrador.

3. En el símbolo del sistema, escriba<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>y, luego, presione Entrar.

4. En PowerShell ISE, abra el archivo **.ps1**. Ejecute el script.

5. El script primero inicia una ventana nueva donde se inicia sesión en Azure.
 - Si vuelve a ejecutar el script sin interrumpir la sesión, tiene la opción conveniente de marcar el comando **Add-AzureAccount** como comentario.


![PowerShell ISE, con el módulo de Azure instalado, está preparado para ejecutar el script.][30_powershell_ise]


&nbsp;


```
## TODO: Before running, find all 'TODO' and make each edit!

#--------------- 1 -----------------------


# You can comment out or skip this Add-AzureAccount command after the first run.
# Current PowerShell environment retains the successful outcome.

'Expect a pop-up window in which you log in to Azure.'


Add-AzureAccount

#-------------- 2 ------------------------


'
TODO: Edit the values assigned to these variables, especially the first few!
'

$subscriptionName       = 'YOUR_SUBSCRIPTION_NAME'
$policySasExpiryTime = '2016-01-28T23:44:56Z'
$policySasStartTime  = '2015-08-01'


$storageAccountName     = 'gmstorageaccountxevent'
$storageAccountLocation = 'West US'
$contextName            = 'gmcontext'
$containerName          = 'gmcontainerxevent'
$policySasToken      = 'gmpolicysastoken'


# Leave this value alone, as 'rwl'.
$policySasPermission = 'rwl'

#--------------- 3 -----------------------


# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script. 

'Choose an existing subscription for the current PowerShell environment.'


Select-AzureSubscription -SubscriptionName $subscriptionName


#-------------- 4 ------------------------


'
Clean-up the old Azure Storage Account after any previous run, 
before continuing this new run.'


If ($storageAccountName)
{
    Remove-AzureStorageAccount -StorageAccountName $storageAccountName
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString()

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...'

New-AzureStorageAccount `
    -StorageAccountName $storageAccountName `
    -Location           $storageAccountLocation

[System.DateTime]::Now.ToString()

[System.Media.SystemSounds]::Beep.Play()


'
Get the primary access key for your storage account.
'


$primaryAccessKey_ForStorageAccount = `
    (Get-AzureStorageKey `
        -StorageAccountName $storageAccountName).Primary

"`$primaryAccessKey_ForStorageAccount = $primaryAccessKey_ForStorageAccount"

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
'

#--------------- 6 -----------------------


# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
'

$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $primaryAccessKey_ForStorageAccount


'Create a container within the storage account.
'


$containerObjectInStorageAccount = New-AzureStorageContainer `
    -Name    $containerName `
    -Context $context


'Create a security policy to be applied to the SAS token.
'

New-AzureStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime 

'
Generate a SAS token for the container.
'
Try
{
    $sasTokenWithPolicy = New-AzureStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken
}
Catch 
{
    $Error[0].Exception.ToString()
}

#-------------- 7 ------------------------


'Display the values that YOU must edit into the Transact-SQL script next!:
'

"storageAccountName: $storageAccountName"
"containerName:      $containerName"
"sasTokenWithPolicy: $sasTokenWithPolicy"

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
'

'
(Later, return here to delete your Azure Storage account. See the preceding - Remove-AzureStorageAccount -StorageAccountName $storageAccountName)'

'
Now shift to the Transact-SQL portion of the two-part code sample!'

# EOFile
```


&nbsp;


Anote los valores con nombre que el script de PowerShell imprime cuando finaliza. Debe editar esos valores en el script de Transact-SQL que sigue en la fase 2.


## Fase 2: código de Transact-SQL que usa el contenedor de Almacenamiento de Azure


- En la fase 1 de este ejemplo de código, ejecutó un script de PowerShell para crear un contenedor de Almacenamiento de Azure.
- A continuación, en la fase 2, el siguiente script de Transact-SQL debe usar el contenedor.


El script comienza con comandos que realizan un limpieza después de una posible ejecución anterior y está diseñado para volverse a ejecutar.


El script de PowerShell imprimió algunos valores con nombre cuando finalizó. Debe editar el script de Transact-SQL para que use esos valores. Busque **TODO** en el script de Transact-SQL para ubicar los puntos de edición.


1. Abra SQL Server Management Studio (ssms.exe).

2. Conéctese a la base de datos de Base de datos SQL de Azure.

3. Haga clic para abrir un nuevo panel de consulta.

4. Pegue el siguiente script de Transact-SQL en el panel de consulta.

5. Busque cada **TODO** en el script y haga las ediciones correspondientes.

6. Guarde y, luego, ejecute el script.


&nbsp;


```
---- TODO: First, run the PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.


SET NOCOUNT ON;

GO


----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.


IF EXISTS
	(SELECT * FROM sys.objects
		WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
	DROP TABLE gmTabEmployee;
END
GO


CREATE TABLE gmTabEmployee
(
	EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
	EmployeeId           int                not null  identity(1,1),
	EmployeeKudosCount   int                not null  default 0,
	EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO gmTabEmployee ( EmployeeDescr )
	VALUES ( 'Jane Doe' );
GO


------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).


IF NOT EXISTS
	(SELECT * FROM sys.symmetric_keys
		WHERE symmetric_key_id = 101)
BEGIN
	CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO


IF EXISTS
	(SELECT * FROM sys.database_scoped_credentials
		-- TODO: Assign AzureStorageAccount name, and the associated Container name.
		WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
	DROP DATABASE SCOPED CREDENTIAL
		-- TODO: Assign AzureStorageAccount name, and the associated Container name.
		[https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO


CREATE
	DATABASE SCOPED
	CREDENTIAL
		-- use '.blob.',   and not '.queue.' or '.table.' etc.
		-- TODO: Assign AzureStorageAccount name, and the associated Container name.
		[https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
	WITH
		IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
		-- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
		SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
	;
GO


------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
	(SELECT * from sys.database_event_sessions
		WHERE name = 'gmeventsessionname240b')
BEGIN
	DROP
		EVENT SESSION
			gmeventsessionname240b
	    ON DATABASE;
END
GO


CREATE
	EVENT SESSION
		gmeventsessionname240b
	ON DATABASE

	ADD EVENT
		sqlserver.sql_statement_starting
			(
			ACTION (sqlserver.sql_text)
			WHERE statement LIKE 'UPDATE gmTabEmployee%'
			)
	ADD TARGET
		package0.event_file
			(
			-- TODO: Assign AzureStorageAccount name, and the associated Container name.
			-- Also, tweak the .xel file name at end, if you like.
			SET filename =
				'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
			)
	WITH
		(MAX_MEMORY = 10 MB,
		MAX_DISPATCH_LATENCY = 3 SECONDS)
	;
GO


------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
	EVENT SESSION
		gmeventsessionname240b
	ON DATABASE
	STATE = START;
GO


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
	SET EmployeeKudosCount = EmployeeKudosCount + 2
	WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
	SET EmployeeKudosCount = EmployeeKudosCount + 13
	WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO


ALTER
	EVENT SESSION
		gmeventsessionname240b
	ON DATABASE
	STATE = STOP;
GO


-------------- Step 5.  Select the results. ----------

SELECT
		*, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
		CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
	FROM
		sys.fn_xe_file_target_read_file
			(
				-- TODO: Fill in Storage Account name, and the associated Container name.
				'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
				null, null, null
			);
GO


-------------- Step 6.  Clean up. ----------

DROP
	EVENT SESSION
		gmeventsessionname240b
	ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
	-- TODO: Assign AzureStorageAccount name, and the associated Container name.
	[https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
	;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzureStorageAccount to delete your Azure Storage account!';
GO
```


&nbsp;


Si el destino no se adjunta cuando ejecuta el script, debe detener la sesión de evento y reiniciarla:


```
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


&nbsp;


## Salida


Cuando se complete el script de Transact-SQL, haga clic en una celda bajo el encabezado de la columna **event\_data\_XML**. Aparece un elemento **<event>** que muestra una instrucción UPDATE.

Este es un elemento **<event>** que se generó durante la prueba:


&nbsp;


```
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```





&nbsp;


## Conversión del ejemplo de código para ejecutarlo en SQL Server


Suponga que desea ejecutar el ejemplo de Transact-SQL anterior en Microsoft SQL Server.


- Para mantener la simplicidad, desearía reemplazar completamente el uso del contenedor de Almacenamiento de Azure por un archivo simple, como **C:\\myeventdata.xel**. El archivo se escribiría en el disco duro local del equipo donde se hospeda SQL Server.


- No necesitaría ninguna clase de instrucción Transact-SQL para **CREATE MASTER KEY** y **CREATE CREDENTIAL**.


- En la instrucción **CREATE EVENT SESSION**, en su cláusula **ADD TARGET**, reemplazaría el valor Http asignado en **filename=** con una cadena de ruta de acceso completa, como **C:\\myfile.xel**.
 - No es necesario utilizar ninguna cuenta de Almacenamiento de Azure.


## Más información


El tema principal de los eventos extendidos en Base de datos SQL de Azure es:

- [Eventos extendidos en Base de datos SQL](sql-database-xevent-db-diff-from-svr.md) es el tema principal para los eventos extendidos en Base de datos SQL de Azure.
 - Compara los aspectos de los eventos extendidos que son distintos entre Base de datos SQL de Azure y Microsoft SQL Server.


- [Código de destino de búfer en anillo para eventos extendidos en Base de datos SQL](sql-database-xevent-code-ring-buffer.md), que proporciona un ejemplo de código similar, rápido y simple, pero que se utiliza más en pruebas breves y es menos eficaz para una actividad de mayor tamaño.


Si desea obtener más información sobre las cuentas y los contenedores del servicio de Almacenamiento de Azure, consulte:

- [Uso del almacenamiento de blobs de .NET](storage-dotnet-how-to-use-blobs.md/)
- [Asignar nombres y hacer referencia a contenedores, blobs y metadatos](http://msdn.microsoft.com/library/azure/dd135715.aspx)
- [Trabajar con el contenedor raíz](http://msdn.microsoft.com/library/azure/ee395424.aspx)


<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

<!---HONumber=AcomDC_0128_2016-->