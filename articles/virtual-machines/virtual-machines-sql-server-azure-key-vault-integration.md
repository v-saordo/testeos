<properties 
	pageTitle="Configuración de la integración de Almacén de claves de Azure para SQL Server en máquinas virtuales de Azure (implementación clásico)"
	description="Aprenda a automatizar la configuración de cifrado de SQL Server para su uso con Almacén de claves de Azure. En este tema se explica cómo usar la integración de Almacén de claves de Azure con la creación de máquinas virtuales de SQL Server en el modelo de implementación clásico." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="12/17/2015"
	ms.author="jroth"/>

# Configuración de la integración de Almacén de claves de Azure para SQL Server en máquinas virtuales de Azure (implementación clásico)

> [AZURE.SELECTOR]
- [Resource Manager](virtual-machines-sql-server-azure-key-vault-integration-resource-manager.md)
- [Classic](virtual-machines-sql-server-azure-key-vault-integration.md)

## Información general
SQL Server tiene varias características de cifrado, como el [cifrado de datos transparente (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), el [cifrado de nivel de columna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) y la [copia de seguridad cifrada](https://msdn.microsoft.com/library/dn449489.aspx). Estas formas de cifrado requieren administrar y almacenar las claves criptográficas que se usan para el cifrado. El servicio de Almacén de claves de Azure (AKV) está diseñado para mejorar la seguridad y la administración de estas claves en una ubicación segura y con gran disponibilidad. El [conector de SQL Server](http://www.microsoft.com/download/details.aspx?id=45344) permite que SQL Server use estas claves de Almacén de claves de Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.

Si se ejecuta SQL Server con equipos locales, hay una serie de [pasos a seguir para tener acceso a Almacén de claves de Azure desde el equipo de SQL Server local](https://msdn.microsoft.com/library/dn198405.aspx). Pero para SQL Server en las máquinas virtuales de Azure, puede ahorrar tiempo usando la característica *Integración de Almacén de claves de Azure*. Con algunos cmdlets de Azure PowerShell para habilitar esta característica, puede automatizar la configuración necesaria para que una máquina virtual de SQL tenga acceso a su Almacén de claves.

Cuando se habilita esta característica, automáticamente se instala el conector de SQL Server, se configura el proveedor EKM para obtener acceso a Almacén de claves de Azure y se crea la credencial para que pueda tener acceso a su almacén. Si examinamos los pasos descritos en la documentación local que se mencionó anteriormente, puede ver que esta característica automatiza los pasos 2 y 3. Lo único que aún tiene que hacer manualmente es crear el Almacén de claves y las claves. Desde allí, se automatiza toda la configuración de la máquina virtual de SQL. Cuando esta característica haya completado el programa de instalación, puede ejecutar instrucciones de T-SQL para empezar a cifrar sus bases de datos o copias de seguridad como lo haría normalmente.

[AZURE.INCLUDE [Preparación de la integración de AKV](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## Configuración de la integración de AKV
Use PowerShell para configurar la integración de Almacén de claves de Azure. Las secciones siguientes proporcionan una visión general de los parámetros necesarios y, a continuación, un script de PowerShell de ejemplo.

### Parámetros de entrada
En la tabla siguiente se enumeran los parámetros necesarios para ejecutar el script de PowerShell en la sección siguiente.

|Parámetro|Descripción|Ejemplo|
|---|---|---|
|**$akvURL**|**La dirección URL del Almacén de claves**|"https://contosokeyvault.vault.azure.net/"|
|**$spName**|**Nombre de entidad de servicio**|"fde2b411-33d5-4e11-af04eb07b669ccf2"|
|**$spSecret**|**Secreto de entidad de servicio**|"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="|
|**$credName**|**Nombre de credencial**: la integración de AKV crea una credencial en SQL Server, permitiendo el acceso de la máquina virtual al Almacén de claves. Elija un nombre para esta credencial.|"mycred1"|
|**$vmName**|**Nombre de la máquina virtual**: el nombre de una máquina virtual de SQL creada anteriormente.|"myvmname"|
|**$serviceName**|**Nombre de servicio**: nombre del servicio en la nube que está asociado a la máquina virtual de SQL.|"mycloudservicename"|

### Habilitación de la integración de AKV con PowerShell
El cmdlet **New-AzureVMSqlServerKeyVaultCredentialConfig** crea un objeto de configuración para la característica de integración de Almacén de claves de Azure. **Set-AzureVMSqlServerExtension** configura esta integración con el parámetro **KeyVaultCredentialSettings**. Los pasos siguientes muestran cómo usar estos comandos.

1. En Azure PowerShell, configure primero los parámetros de entrada con los valores específicos como se describe en las secciones anteriores de este tema. El siguiente script es un ejemplo:
	
		$akvURL = "https://contosokeyvault.vault.azure.net/"
		$spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
		$spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
		$credName = "mycred1"
		$vmName = "myvmname"
		$serviceName = "mycloudservicename"
2.	A continuación, use el siguiente script para configurar y habilitar la integración de AKV.
	
		$secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
		$akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
		Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

La extensión del agente de Iaas de SQL actualizará la máquina virtual de SQL con esta nueva configuración.

[AZURE.INCLUDE [Siguientes pasos de integración de AKV](../../includes/virtual-machines-sql-server-akv-next-steps.md)]

<!---HONumber=AcomDC_1223_2015-->