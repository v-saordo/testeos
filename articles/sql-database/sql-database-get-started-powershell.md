<properties 
    pageTitle="Nueva configuración de Base de datos SQL con PowerShell | Microsoft Azure" 
    description="Aprenda a crear una nueva Base de datos SQL con PowerShell. Las tareas de configuración comunes de la base de datos pueden administrarse mediante los cmdlets de PowerShell." 
    keywords="creación de una nueva base de datos SQL,configuración de base de datos"
	services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="hero-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="01/20/2016"
    ms.author="sstein"/>

# Creación de una nueva Base de datos SQL y realización de tareas comunes de configuración de base de datos con los cmdlets de PowerShell 

**Base de datos única**

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)


Aprenda a crear una nueva Base de datos SQL y a realizar tareas comunes de configuración de base de datos con los cmdlets de PowerShell


Para ejecutar los cmdlets de PowerShell, necesitará tener Azure PowerShell instalado y en marcha. Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).

- Si necesita una suscripción a Azure, haga clic en la opción **PRUEBA GRATUITA** situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.


## Configuración de las credenciales y selección de la suscripción

Ahora que está ejecutando el módulo del Administrador de recursos de Azure, tendrá acceso a todos los cmdlets necesarios para crear una base de datos SQL.

Lo primero que debe hacer es establecer el acceso a su cuenta de Azure, de modo que ejecute el siguiente cmdlet y verá una pantalla de inicio de sesión para escribir sus credenciales. Use el mismo correo electrónico y la misma contraseña que usa para iniciar sesión en el portal de Azure.

	Add-AzureRmAccount

Después de iniciar sesión correctamente, verá información en la pantalla que incluye el identificador con el que ha iniciado sesión y las suscripciones a Azure a las que tiene acceso.


### Selección de su suscripción a Azure

Para seleccionar la suscripción, necesita su id. de suscripción. Puede copiar el nombre o el identificador del paso anterior, o bien, si dispone de varias suscripciones, puede ejecutar el cmdlet **Get-AzureRmSubscription** y copiar la información de suscripción deseada del conjunto de resultados. Cuando tenga su suscripción, ejecute el siguiente cmdlet:

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Después de ejecutar correctamente **Select-AzureRmSubscription**, volverá al símbolo del sistema de PowerShell. Si tiene más de una suscripción, puede ejecutar **Get-AzureRmSubscription** y comprobar que la suscripción que desea usar muestra **IsCurrent: True**.

## Configuración de la base de datos: creación de un grupo de recursos, un servidor y una regla de firewall

Ya dispone de acceso para ejecutar cmdlets en su suscripción de Azure seleccionada, por lo que el siguiente paso es establecer el grupo de recursos que contiene el servidor donde se creará la base de datos. Puede editar el comando siguiente para usar cualquier ubicación válida que elija. Ejecute **(Get-AzureRmLocation | where-object {$\_.Name -eq "Microsoft.Sql/servers" }).Locations** para obtener una lista de ubicaciones válidas.

Ejecute el comando siguiente para crear un nuevo grupo de recursos:

	New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "West US"

Después de crear correctamente el nuevo grupo de recursos, puede ver información en pantalla que incluye el elemento **ProvisioningState: Succeeded**.


### Creación de un servidor 

Las bases de datos SQL se crean en los servidores de Base de datos SQL de Azure. Ejecute **New-AzureRmSqlServer** para crear un nuevo servidor. Reemplace ServerName con el nombre de su servidor. Debe ser único para todos los servidores SQL de Azure, por lo que obtendrá un error aquí si el nombre del servidor ya existe. También debe tener en cuenta que este comando puede tardar varios minutos en completarse. Puede editar el comando para usar cualquier ubicación válida que elija, pero debe utilizar la misma ubicación que empleó para el grupo de recursos creado en el paso anterior.

	New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

Al ejecutar este comando, se abrirá una ventana para especificar el **Nombre de usuario** y la **Contraseña**. No especifique aquí sus credenciales de Azure, sino el nombre de usuario y contraseña serán las credenciales de administrador que desea crear para el nuevo servidor.

Se mostrarán los detalles del servidor tras crear el servidor correctamente.

### Configuración de una regla de firewall para permitir el acceso al servidor

Establezca una regla de firewall para tener acceso al servidor. Ejecute el comando siguiente, reemplazando las direcciones IP inicial y final con los valores válidos para el equipo.

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

Se mostrarán los detalles de la regla de firewall tras crear la regla correctamente.

Para permitir que otros servicios de Azure tengan acceso al servidor, agregue una regla de firewall y establezca tanto StartIpAddress como EndIpAddress en 0.0.0.0. Tenga en cuenta que esto permite que el tráfico de Azure de cualquier suscripción de Azure tenga acceso al servidor.

Para obtener más información, consulte [Firewall de Base de datos SQL de Azure](sql-database-firewall-configure.md).


## Crear una nueva base de datos SQL

Ahora ya dispone de un grupo de recursos, un servidor y una regla de firewall configurados para poder tener acceso al servidor.

El siguiente comando crea una nueva base de datos SQL (en blanco) en el nivel de servicio Standard con un nivel de rendimiento S1:


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


Se mostrarán los detalles de la base de datos tras crear la base de datos correctamente.

## Creación de un script de PowerShell de Base de datos SQL

    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    $ResourceGroupName = "resourcegroupname"
    $Location = "Japan West"
    
    $ServerName = "uniqueservername"
    
    $FirewallRuleName = "rule1"
    $FirewallStartIP = "192.168.0.0"
    $FirewallEndIp = "192.168.0.0"
    
    $DatabaseName = "database1"
    $DatabaseEdition = "Standard"
    $DatabasePerfomanceLevel = "S1"
    
    
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId
    
    $ResourceGroup = New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
    
    $Server = New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName -ServerName $ServerName -Location $Location -ServerVersion "12.0"
    
    $FirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $ServerName -FirewallRuleName $FirewallRuleName -StartIpAddress $FirewallStartIP -EndIpAddress $FirewallEndIp
    
    $SqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -Edition $DatabaseEdition -RequestedServiceObjectiveName $DatabasePerfomanceLevel
    
    $SqlDatabase
    


## Pasos siguientes
Después de crear una nueva Base de datos SQL y de realizar las tareas de configuración básica de la base de datos, está listo para lo siguiente:

- [Conexión a la Base de datos SQL con SQL Server Management Studio y realización de una consulta de T-SQL de ejemplo](sql-database-connect-query-ssms.md)


## Recursos adicionales

- [Base de datos SQL de Azure](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0302_2016-->