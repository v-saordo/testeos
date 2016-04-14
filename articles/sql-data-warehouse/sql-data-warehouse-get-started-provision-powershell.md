<properties
   pageTitle="Crear Almacenamiento de datos SQL mediante el uso de Powershell | Microsoft Azure"
   description="Creación de Almacenamiento de datos SQL con Powershell"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Creación de Almacenamiento de datos SQL con Powershell

> [AZURE.SELECTOR]
- [Portal de Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

## Obtención y ejecución de los cmdlets de Azure PowerShell.

> [AZURE.NOTE]  Para utilizar Microsoft Azure Powershell con Almacenamiento de datos SQL, debe descargar e instalar la versión más reciente de Azure PowerShell con los cmdlets de ARM. Puede comprobar la versión ejecutando `Get-Module -ListAvailable -Name Azure`. Este artículo se basa en Microsoft Azure PowerShell versión 1.0.3.

Si no tiene configurado PowerShell, debe descargarlo y configurarlo.

1. Para descargar el módulo Azure PowerShell, ejecute el [Instalador de plataforma web de Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409).
2. Para ejecutar el módulo, en la ventana de inicio, escriba **Windows PowerShell**.
3. Ejecute este cmdlet para iniciar sesión en el Administrador de recursos de Azure. Para más información, vea [Instalación y configuración de Azure PowerShell][].

	```
	Login-AzureRmAccount
	```

4. Seleccione la suscripción que desea utilizar para la sesión actual.

	```
	Get-AzureRmSubscription	-SubscriptionName "MySubscription" | Select-AzureRmSubscription
	```

## Creación de una base de datos de Almacenamiento de datos SQL.
Para implementar un Almacenamiento de datos SQL, utilice el cmdlet New-AzureRmSQLDatabase. Asegúrese de que cumple los siguientes requisitos previos antes de ejecutar el comando:

### Requisitos previos

- Azure SQL Server v 12 para hospedar la base de datos.
- Conocer el nombre del grupo de recursos de SQL Server.

### Comando de implementación

Este comando implementará una nueva base de datos en Almacenamiento de datos de SQL.

```
New-AzureRmSqlDatabase -RequestedServiceObjectiveName "<Service Objective>" -DatabaseName "<Data Warehouse Name>" -ServerName "<Server Name>" -ResourceGroupName "<ResourceGroupName>" -Edition "DataWarehouse"
```

En este ejemplo se implementa una nueva base de datos denominada "mynewsqldw1", con el nivel de objetivo de servicio "DW400", en el servidor llamado "sqldwserver1" que se encuentra en el grupo de recursos denominado "mywesteuroperesgp1".

```
New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw1" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse"
```

Los parámetros necesarios para este cmdlet son los siguientes:

 + **RequestedServiceObjectiveName**: la cantidad de DWU solicitada y con formato "DWXXX" que admite los siguientes valores: 100, 200, 300, 400, 500, 600, 1000, 1500, 1200 y 2000.
 + **DatabaseName**: el nombre del Almacenamiento de datos SQL que está creando.
 + **ServerName**: el nombre del servidor que se usa para la creación (tiene que ser V12).
 + **ResourceGroupName**: el grupo de recursos que está usando. Para obtener los grupos de recursos que estén disponibles en su suscripción, use Get-AzureResource.
 + **Edition**: tiene que establecer la edición como "DataWarehouse" para crear un Almacenamiento de datos SQL.

Para la referencia de comandos, consulte [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt619339.aspx).

Para las opciones de parámetros, consulte [Create Database (Azure SQL Data Warehouse)](https://msdn.microsoft.com/library/mt204021.aspx) (Creación de base de datos [Almacenamiento de datos SQL de Azure]).

## Pasos siguientes
Después de que su Almacenamiento de datos SQL termine el aprovisionamiento, puede [cargar datos de ejemplo][] o averiguar cómo [desarrollar][], [cargar][] o [migrar][].

Si está interesado en obtener más información sobre cómo administrar el Almacenamiento de datos SQL mediante la programación, consulte nuestra documentación de [Powershell][] o [API de REST][].



<!--Image references-->

<!--Article references-->
[migrar]: ./sql-data-warehouse-overview-migrate.md
[desarrollar]: ./sql-data-warehouse-overview-develop/.md
[cargar datos de ejemplo]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Powershell]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[API de REST]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[firewall rules]: ../sql-database/sql-database-configure-firewall-settings.md
[Instalación y configuración de Azure PowerShell]: ./powershell-install-configure.md

<!---HONumber=AcomDC_0309_2016-->