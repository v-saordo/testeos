<properties 
	pageTitle="Desinstalación de la herramienta de trabajo de bases de datos elásticas" 
	description="Desinstalación de la herramienta de trabajo de bases de datos elásticas" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/23/2016" 
	ms.author="ddove; sidneyh"/>

#Desinstalación de componentes de Trabajos de base de datos elástica.
Los componentes de **Trabajos de base de datos elástica** se pueden desinstalar mediante el Portal o PowerShell.

##Desinstalación de componentes de Trabajos de base de datos elástica mediante el Portal de Azure

1. Abra el [Portal de Azure](https://ms.portal.azure.com/).
2. Navegue hasta la suscripción que contiene componentes de **Trabajos de base de datos elástica**, es decir, la suscripción en la que se instalaron los componentes de Trabajos de base de datos elástica.
3. Haga clic en **Examinar** y haga clic en **Grupos de recursos**.
4. Seleccione el grupo de recursos denominado "\_\_ElasticDatabaseJob".
5. Elimine el grupo de recursos.

##Desinstalación de componentes de Trabajos de base de datos elástica mediante PowerShell

1.	Inicie una ventana de comandos de Microsoft Azure PowerShell y navegue hasta el subdirectorio tools en la carpeta Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x: escriba **cd tools**.

		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

2.	Ejecute el script .\\UninstallElasticDatabaseJobs.ps1 de PowerShell.

		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\UninstallElasticDatabaseJobs.ps1
		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\UninstallElasticDatabaseJobs.ps1

O simplemente, ejecute el siguiente script, suponiendo que se usaron valores predeterminados en la instalación de los componentes:

		$ResourceGroupName = "__ElasticDatabaseJob"
		Switch-AzureMode AzureResourceManager
		
		$resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
		if(!$resourceGroup)
		{
		    Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job components are uninstalled."
		    return
		}
		
		Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
		Remove-AzureResourceGroup -Name $ResourceGroupName -Force
		Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job compoennts are now uninstalled."

## Pasos siguientes

Para reinstalar Trabajos de base de datos elástica, vea [Instalación del servicio Trabajos de base de datos elástica](sql-database-elastic-jobs-service-installation.md).

Si desea obtener más información sobre Trabajos de base de datos elástica, vea [Información general de Trabajos de base de datos elástica](sql-database-elastic-jobs-overview.md).

<!--Image references-->

<!---HONumber=AcomDC_0224_2016-->