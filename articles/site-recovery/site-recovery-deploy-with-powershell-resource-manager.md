<properties
	pageTitle="Protección de servidores en Azure con Azure PowerShell con el Administrador de recursos de Azure | Microsoft Azure"
	description="Automatizar la protección de servidores en Azure con Azure Site Recovery mediante PowerShell y el Administrador de recursos de Azure."
	services="site-recovery"
	documentationCenter=""
	authors="bsiva"
	manager="abhiag"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="12/09/2015"
	ms.author="bsiva"/>

# Azure Site Recovery con PowerShell y el Administrador de recursos de Azure


## Información general

Azure Site Recovery contribuye a su estrategia de continuidad de negocio y recuperación ante desastres (BCDR) mediante la organización de la replicación, la conmutación por error y la recuperación de máquinas virtuales en varios escenarios de implementación. Para obtener una lista completa de los escenarios de implementación, consulte [Información general sobre Azure Site Recovery](site-recovery-overview.md).

Azure PowerShell es un módulo que ofrece cmdlets para administrar Azure mediante Windows PowerShell. Puede funcionar con dos tipos de módulos: el módulo de perfil de Azure o el módulo de Administrador de recursos de Azure (ARM).

Los cmdlets de PowerShell de Azure Site Recovery (ASR) que están disponibles con Azure PowerShell para ARM le permiten proteger y recuperar los servidores en Azure.

En este artículo se describe, con la ayuda de un ejemplo, cómo usar Windows PowerShell ® junto con ARM para implementar Azure Site Recovery con el fin de configurar y orquestar la protección de los servidores. El ejemplo que se usa en este artículo muestra cómo proteger, realizar conmutaciones por error y recuperar máquinas virtuales en un host de Hyper-V en Azure, mediante Azure PowerShell con ARM.

> [AZURE.NOTE] Actualmente, los cmdlets de PowerShell de Azure Site Recovery permiten realizar configuraciones en escenarios de sitio VMM a sitio VMM, sitio VMM a sitio de Hyper-V y Azure, y sitio de Hyper-V a Azure. Próximamente se agregará compatibilidad con otros escenarios de ASR.

No es necesario ser un experto en PowerShell para leer este artículo, pero en él se da por hecho que conoce los conceptos básicos, como módulos, cmdlets y sesiones. Para obtener más información acerca de Windows PowerShell, consulte [Introducción a Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).
- Lea más sobre el [uso de Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md)


## Principales características

- Proteger los servidores en Azure, realizar la conmutación por error y recuperarlos en Azure IaaS (ARM) o IaaS (clásico)
- Los asociados de Microsoft que forman parte del programa proveedor de soluciones en la nube (CSP), pueden configurar y administrar la protección de los servidores de sus clientes en la suscripción CSP de cliente (suscripción de inquilino)

## Antes de comenzar

Asegúrese de que tiene preparados estos requisitos previos:

- Necesitará una cuenta de [Microsoft Azure](https://azure.microsoft.com/). Puede comenzar con una [evaluación gratuita](pricing/free-trial/). También puede leer sobre los precios del [Administrador de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
- Necesitará Azure PowerShell 1.0. Para obtener información acerca de esta versión y cómo instalarla, consulte [Azure PowerShell 1.0](https://azure.microsoft.com/).
- Tiene que tener los módulos [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) y [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/) instalados. Puede obtener las últimas versiones de estos módulos en la [Galería de PowerShell](https://www.powershellgallery.com/)

Este artículo muestra con la ayuda de un ejemplo cómo usar Azure PowerShell con ARM para configurar y administrar la protección de los servidores. El ejemplo utilizado en este artículo muestra cómo proteger una máquina virtual que se ejecuta en un host de Hyper-V en Azure y los requisitos previos que siguen son específicos de este ejemplo. Para un conjunto más amplio de requisitos para los distintos escenarios de ASR, consulte la documentación correspondiente a cada escenario.

- Necesitará un host de Hyper-V que ejecute Windows Server 2012 R2 que contenga una o más máquinas virtuales.
- Los servidores de Hyper-V deben estar conectados a Internet, directamente o a través de un proxy.
- Las máquinas virtuales que quiere proteger deben cumplir los [requisitos previos para las máquinas virtuales](site-recovery-best-practices.md#virtual-machines).
	

## Paso 1: Inicio de sesión en su cuenta de Azure.


1. Abra una consola de PowerShell y ejecute este comando para a la cuenta de Azure. El cmdlet abrirá una página web que le solicitará las credenciales de cuenta.

    	Login-AzureRmAccount

	Como alternativa, también puede incluir las credenciales de cuenta como un parámetro en el cmdlet `Login-AzureRmAccount` utilizando el parámetro `-Credential`.
	
	Si usted es un asociado CSP que trabaja en nombre de un inquilino, tendrá que especificar al cliente como inquilino usando su TenantID o su nombre de dominio principal de inquilino

		Login-AzureRmAccount -Tenant "fabrikam.com"

2. Una cuenta puede tener varias suscripciones, por lo que deberá asociar la suscripción que desee usar con la cuenta.

    	Select-AzureRmSubscription -SubscriptionName $SubscriptionName

3.  Compruebe que su suscripción está registrada para usar los proveedores de Azure para Servicios de recuperación y Site Recovery con los comandos siguientes:

	- `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`
	-  `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

	Si RegistrationState está establecido en el valor Registrado en la salida de los dos comandos anteriores, puede continuar con el paso 2. Si no es así, tiene que registrar al proveedor que falta en su suscripción.


	Para registrar al proveedor de Azure para Site Recovery haga lo siguiente:

    	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery
	
	De forma similar, si utiliza los cmdlets de Servicios de recuperación por primera vez en su suscripción, es necesario que registre al proveedor de Azure para Servicios de recuperación. Antes de hacer esto, tiene que habilitar primero el acceso al proveedor de Servicios de recuperación en su suscripción con la ejecución del comando siguiente:

		Register-AzureRmProviderFeature -FeatureName betaAccess -ProviderNamespace Microsoft.RecoveryServices

	>[AZURE.TIP] Tras finalizar correctamente el comando anterior, la habilitación del acceso al proveedor de Servicios de recuperación de su suscripción puede tardar hasta una hora. Los intentos de registrar al proveedor de Servicios de recuperación de su suscripción con el comando `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices` pueden no funcionar mientras tanto. Si esto ocurre, espere una hora y vuelva a intentarlo.

	Una vez que haya habilitado el acceso al proveedor de Servicios de recuperación en su suscripción, registre al proveedor en su suscripción con la ejecución del comando siguiente:

		Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices

	Compruebe que los proveedores se registraron correctamente mediante los comandos `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` y `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

	

## Paso 2: Configuración del almacén de Servicios de recuperación

1. Cree un grupo de recursos ARM en el que podrá crear el almacén o use un grupo de recursos existente. Puede crear un nuevo grupo de recursos ARM mediante el siguiente comando:

		New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo  

	donde la variable $ResourceGroupName contiene el nombre del grupo de recursos ARM que desea crear y la variable $Geo contiene la región de Azure en la que se va a crear el grupo de recursos (por ejemplo: Sur de Brasil).

	Puede obtener una lista de grupos de recursos ARM en su suscripción con el cmdlet `Get-AzureRmResourceGroup`.

2. Cree un nuevo almacén de Servicios de recuperación de Azure como sigue:

		$vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

	Puede recuperar una lista de los almacenes existentes con el cmdlet `Get-AzureRmRecoveryServicesVault`.

> [AZURE.NOTE] Si desea realizar operaciones en los almacenes de ASR creados mediante el portal clásico o el módulo PowerShell de Administración de servicios de Azure, puede recuperar una lista de dichos almacenes con el cmdlet `Get-AzureRmSiteRecoveryVault`. Se recomienda que para todas las operaciones nuevas se cree un nuevo almacén de Servicios de recuperación Los almacenes de Site Recovery que ha creado anteriormente continuarán siendo compatibles, pero no tendrán las características más recientes.

## Paso 3: Generación de una clave de registro de almacén

1. Genere y descargue una clave de registro de almacén para el almacén.

    	Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -Path $path
2. Importe el archivo de configuración de almacén descargado para establecer el contexto de almacén
 
		Import-AzureRmSiteRecoveryVaultSettingsFile -Path $path 

## Paso 4: Creación de un sitio de Hyper-V y generación de una nueva clave de registro de almacén para el sitio.

1. Cree un nuevo sitio de Hyper-V como sigue:
		
		$sitename = "MySite"                #Specify site friendly name
		New-AzureRmSiteRecoverySite -Name $sitename

	Este cmdlet inicia un trabajo de ASR para crear el sitio y devuelve un objeto de trabajo de ASR. Espere a que el trabajo se complete y compruebe que se ha hecho correctamente.

	Puede recuperar el objeto de trabajo de ASR y, por tanto, comprobar el estado actual del estado del trabajo mediante el cmdlet Get-AzureRmSiteRecoveryJob 
2. Genere y descargue una clave de registro para el sitio:

		$SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
		Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path
	
	Copie la clave descargada en el host de Hyper-V. Necesitará la clave para registrar el host de Hyper-V en el sitio

	
## Paso 5: Instalación del proveedor de Azure Site Recovery y del agente de Servicios de recuperación de Azure en su host de Hyper-V

1. Descargue el instalador de la versión más reciente del proveedor desde [aquí](https://aka.ms/downloaddra)
2. Ejecute el programa de instalación en el host de Hyper-V y al final de la instalación continúe con el paso de registro
3. Proporcione la clave de registro descargada del sitio cuando se le solicite, y complete el registro del host de Hyper-V en el sitio
4. Compruebe que el host de Hyper-V se haya registrado en el sitio mediante lo siguiente:

		$server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname 

## Paso 6: Creación de una directiva de replicación y su asociación con el contenedor de protección

1. Cree una directiva de replicación:

		$ReplicationFrequencyInSeconds = "300";    	#options are 30,300,900
		$PolicyName = “replicapolicy”
		$Recoverypoints = 6            		#specify the number of recovery points
		$storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id 

		$PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

	Compruebe el trabajo devuelto para asegurarse de que la creación de la directiva de replicación se realiza correctamente.

	>[AZURE.IMPORTANT] La cuenta de almacenamiento especificada debe estar en la misma región de Azure que el almacén de servicios de recuperación y debe tener habilitada la replicación geográfica.
	>
	> - Si la cuenta de almacenamiento de recuperación especificada es del tipo Almacenamiento de Azure (clásico), la conmutación por error de las máquinas protegidas recuperará la máquina en Azure IaaS (clásico)
	> - Si la cuenta de almacenamiento de recuperación especificada es del tipo Almacenamiento de Azure (ARM), la conmutación por error de las máquinas protegidas recuperará la máquina en Azure IaaS (ARM)
	
 
2. Obtenga el contenedor de protección correspondiente al sitio:
		
		$protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3.	Inicie la asociación del contenedor de protección con la directiva de replicación:

		$Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName
		$associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

	Espere a que el trabajo de asociación se complete y asegúrese de que se ha hecho correctamente

##Paso 7: Habilitación de la protección para las máquinas virtuales

1. Obtenga la entidad de protección correspondiente a la máquina virtual que desea proteger:
		
		$VMFriendlyName = "Fabrikam-app"                    #Name of the VM 
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

2. Inicie la protección de la máquina virtual:
		
		$Ostype = "Windows"                                 # "Windows" or "Linux"
		$DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

	>[AZURE.IMPORTANT] La cuenta de almacenamiento especificada debe estar en la misma región de Azure que el almacén de servicios de recuperación y debe tener habilitada la replicación geográfica.
	>
	> - Si la cuenta de almacenamiento de recuperación especificada es del tipo Almacenamiento de Azure (clásico), la conmutación por error de las máquinas protegidas recuperará la máquina en Azure IaaS (clásico)
	> - Si la cuenta de almacenamiento de recuperación especificada es del tipo Almacenamiento de Azure (ARM), la conmutación por error de las máquinas protegidas recuperará la máquina en Azure IaaS (ARM)

	> Si la máquina virtual que se protege tiene más de un disco conectado, tendrá que especificar el disco del sistema operativo con el parámetro OSDiskName.

3. Espere a que las máquinas virtuales lleguen a un estado protegido después de la replicación inicial. Esto tardará un poco, dependiendo de factores como la cantidad de datos replicados y el ancho de banda del canal de subida disponible de Azure. Los valores State y StateDescription del trabajo se actualizarán como sigue, cuando la máquina virtual alcance un estado protegido.

		
		PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

		PS C:\> $DRjob | Select-Object -ExpandProperty State
		Succeeded

		PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
		Completed

4. Actualice las propiedades de recuperación como el tamaño de rol de VM y red de Azure para asociar a la NIC de la máquina virtual tras la conmutación por error.

		PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

		PS C:\> $VMFriendlyName = "Fabrikam-App"

		PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

		PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

		PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

		PS C:\> $UpdateJob


		Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
		ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
		                   s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
		Type             : Microsoft.RecoveryServices/vaults/replicationJobs
		JobType          : UpdateVmProperties
		DisplayName      : Update the virtual machine
		ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
		State            : Succeeded
		StateDescription : Completed
		StartTime        : 10-12-2015 17:55:53 +00:00
		EndTime          : 10-12-2015 17:55:54 +00:00
		TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
		TargetObjectType : ProtectionEntity
		TargetObjectName : Fabrikam-App
		AllowedActions   : {Restart}
		Tasks            : {UpdateVmPropertiesTask}
		Errors           : {}



## Paso 8: Ejecución de una conmutación por error de prueba

1. Ejecute un trabajo de conmutación por error de prueba:
		
		$nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group
		
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

		$TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id

2. Compruebe que la máquina virtual de prueba se crea en Azure (el trabajo de conmutación por error de prueba está suspendido, después de crear la máquina virtual de prueba en Azure. El trabajo se completará limpiando los artefactos creados al reanudar el trabajo, como se muestra en el paso siguiente)

3. Complete la conmutación por error de prueba

    	$TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob

<!---HONumber=AcomDC_0302_2016-->