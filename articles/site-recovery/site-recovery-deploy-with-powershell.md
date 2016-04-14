<properties
	pageTitle="Replicar máquinas virtuales de Hyper-V en nubes de VMM con Azure Site Recovery y PowerShell | Microsoft Azure"
	description="Obtenga información sobre cómo automatizar la replicación de máquinas virtuales de Hyper-V en nubes de VMM con Site Recovery y PowerShell."
	services="site-recovery"
	documentationCenter=""
	authors="csilauraa"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/14/2015"
	ms.author="lauraa"/>

# Replicar máquinas virtuales de Hyper-V en nubes VMM con Azure Site Recovery y PowerShell


## Información general

Azure Site Recovery contribuye a su estrategia de continuidad de negocio y recuperación ante desastres (BCDR) mediante la organización de la replicación, la conmutación por error y la recuperación de máquinas virtuales en varios escenarios de implementación. Para obtener una lista completa de los escenarios de implementación, consulte [Información general sobre Azure Site Recovery](site-recovery-overview.md).

En este artículo se muestra cómo usar PowerShell para automatizar tareas comunes que debe realizar al configurar Azure Site Recovery para replicar máquinas virtuales Hyper-V en nubes de VMM de System Center en el almacenamiento de Azure.

El artículo incluye los requisitos previos para el escenario y muestra cómo configurar un almacén de Site Recovery, instalar Azure el proveedor de Azure Site Recovery en el servidor VMM de origen, registrar el servidor en el almacén, agregar una cuenta de almacenamiento de Azure, instalar al agente Servicios de recuperación de Azure en servidores host de Hyper-V, configurar protección de las nubes VMM que se aplicará a todas las máquinas virtuales protegidas y, a continuación, habilitar la protección de esas máquinas virtuales. Se termina comprobando la conmutación por error para asegurarse de que todo funciona según lo esperado.

Si tiene problemas al configurar este escenario, publique sus preguntas en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Antes de comenzar

Asegúrese de que tiene preparados estos requisitos previos:

### Requisitos previos de Azure

- Necesitará una cuenta de [Microsoft Azure](https://azure.microsoft.com/). Puede comenzar con una [evaluación gratuita](pricing/free-trial/).
- Necesitará una cuenta de almacenamiento de Azure para almacenar los datos replicados. La cuenta debe tener habilitada la replicación geográfica. Además, debe estar en la misma región que el almacén de Azure Site Recovery y estar asociada a la misma suscripción. [Más información sobre Almacenamiento de Azure](../storage/storage-introduction.md).
- Deberá asegurarse de que las máquinas virtuales que quiere proteger cumplen los [requisitos previos de máquina virtual de Azure](site-recovery-best-practices.md#virtual-machines).

### Requisitos previos de VMM
- Necesitará un servidor VMM que se ejecute en System Center 2012 R2.
- Necesitará al menos una nube en el servidor VMM que desea proteger. La nube debe contener:
	- Uno o más grupos de hosts de VMM
	- Uno o más servidores host de Hyper-V o clústeres en cada grupo de hosts.
	- Una o más máquinas virtuales en el servidor de Hyper-V de origen.

### Requisitos previos de Hyper-V

- Los servidores host de Hyper-V deben estar ejecutando al menos Windows Server 2012 con el rol Hyper-V y tener instaladas las actualizaciones más recientes.
- Si está ejecutando Hyper-V en un clúster, tenga en cuenta que ese agente de clúster no se crea automáticamente si tiene un clúster basado en una dirección IP estática. Tendrá que configurar manualmente el agente de clúster. Para ello, en Administrador de servidores > Administrador de clústeres de conmutación por error, conéctese al clúster, haga clic en **Configurar rol** y seleccione **Agente de réplica de Hyper-V** en la pantalla **Seleccionar rol** del Asistente para alta disponibilidad. 
- Cualquier servidor o clúster del host de Hyper-V para el que desee administrar la protección debe incluirse en una nube de VMM.

### Requisitos previos de asignación de redes
Al proteger las máquinas virtuales en los mapas de asignación de redes de Azure entre redes de VM en el servidor de VMM de origen y las redes de Azure de destino para habilitar lo siguiente:

- Todas las máquinas con conmutación por error en la misma red pueden conectarse entre sí, independientemente del plan de recuperación en el que se encuentran.
- Si se configura una puerta de enlace de red en la red Azure de destino, las máquinas virtuales se pueden conectar a otras máquinas virtuales locales.
- Si no configura la asignación de redes, solo las máquinas virtuales con conmutación por error en el mismo plan de recuperación podrán conectarse entre sí después de la conmutación por error en Azure.

Si desea implementar la asignación de redes, necesitará lo siguiente:

- Las máquinas virtuales que desea proteger en el servidor VMM de origen deben estar conectadas a una red de máquina virtual. Esa red debe estar vinculada a una red lógica asociada con la nube.
- Una red de Azure a la que pueden conectarse máquinas virtuales replicadas después de la conmutación por error. Seleccionará esta red en el momento de la conmutación por error. La red debe estar en la misma región que su suscripción de Azure Site Recovery.
- [Obtenga más información](site-recovery-network-mapping.md) sobre la asignación de redes:

###Requisitos previos de PowerShell
Asegúrese de que tiene Azure PowerShell listo para usar. Si ya usa PowerShell, necesitará actualizar a la versión 0.8.10 o posterior. Para información sobre cómo configurar PowerShell, vea [Instalación y configuración de Azure PowerShell](powershell-install-configure.md). Una vez que haya configurado PowerShell, puede ver todos los cmdlets disponibles para el servicio [aquí](https://msdn.microsoft.com/library/dn850420.aspx).

Para sugerencias que puedan ayudarle a usar los cmdlets, por ejemplo, cómo se controlan normalmente los valores de parámetro, las entradas y salidas en Azure PowerShell, vea [Introducción de cmdlets de Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

## Paso 1: Establecimiento de la suscripción 

En PowerShell, ejecute estos cmdlets:

```
$UserName = "<user@live.com>"
$Password = "<password>"
$AzureSubscriptionName = "prod_sub1"

$SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
$Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $securePassword
Add-AzureAccount -Credential $Cred;
$AzureSubscription = Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

```

Reemplace los elementos dentro de "< >" por su información específica.

## Paso 2: Creación de un almacén de recuperación del sitio

En PowerShell, reemplace los elementos dentro de "< >" por su información específica y ejecute estos comandos:

```

$VaultName = "<testvault123>"
$VaultGeo  = "<Southeast Asia>"
$OutputPathForSettingsFile = "<c:>"

```


```
New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
$vault = Get-AzureSiteRecoveryVault -Name $VaultName;

```

## Paso 3: Generación de una clave de registro de almacén

Generación de una clave de registro en el almacén. Después de descargar el proveedor de Azure Site Recovery y de instalarlo en el servidor VMM, usará esta clave para registrar el servidor VMM en el almacén.

1.	Obtenga el archivo de configuración de almacén y establezca el contexto:
	
	```
	
	$VaultName = "<testvault123>"
	$VaultGeo  = "<Southeast Asia>"
	$OutputPathForSettingsFile = "<c:>"
	
	$VaultSetingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;
	
	```
	
2.	Establezca el contexto de almacén ejecutando los comandos siguientes:
	
	```
	
	$VaultSettingFilePath = $vaultSetingsFile.FilePath 
	$VaultContext = Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSettingFilePath -ErrorAction Stop
	
	```

## Paso 4: Instalación del proveedor de Azure Site Recovery

1.	En el equipo VMM, cree un directorio ejecutando el comando siguiente:
	
	```
	
	pushd C:\ASR\
	
	```
	
2.	Extraiga los archivos mediante el proveedor descargado ejecutando comando siguiente
	
	```
	
	AzureSiteRecoveryProvider.exe /x:. /q
	
	```
	
3.	Instale el proveedor mediante los siguientes comandos:
	
	```
	
	.\SetupDr.exe /i
	
	```
	
	```
	
	$installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
	do
	{
		$isNotInstalled = $true;
		if(Test-Path $installationRegPath)
		{
			$isNotInstalled = $false;
		}
	}While($isNotInstalled)
	
	```
	
	Espere hasta que la instalación se complete.
	
4.	Registre el servidor en el almacén con el siguiente comando:
	
	```
	
	$BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
	pushd $BinPath
	$encryptionFilePath = "C:\temp"
	.\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
	
	```
	
## Paso 5: Creación de una cuenta de almacenamiento de Azure

Si no tiene una cuenta de almacenamiento de Azure, cree una cuenta de replicación geográfica habilitada ejecutando el comando siguiente:

```

$StorageAccountName = "teststorageacc1"
$StorageAccountGeo  = "Southeast Asia"

New-AzureStorageAccount -StorageAccountName $StorageAccountName -Label $StorageAccountName -Location $StorageAccountGeo;

```

Tenga en cuenta que la cuenta de almacenamiento debe estar en la misma región que el servicio Azure Site Recovery y estar asociada a la misma suscripción.


## Paso 6: Instalación del agente de los Servicios de recuperación de Azure

En el portal de Azure, instale el agente de los Servicios de recuperación de Azure en cada servidor host de Hyper-V situado en las nubes VMM que desea proteger.

Ejecute el siguiente comando en todos los hosts de VMM:

```

marsagentinstaller.exe /q /nu

```


## Paso 7: Configuración de la protección de la nube

1.	Cree un perfil de protección en la nube en Azure ejecutando el comando siguiente:
	
	```
	
	$ReplicationFrequencyInSeconds = "300";
	$ProfileResult = New-AzureSiteRecoveryProtectionProfileObject -ReplicationProvider HyperVReplica -RecoveryAzureSubscription $AzureSubscriptionName -RecoveryAzureStorageAccount $StorageAccountName -ReplicationFrequencyInSeconds 	$ReplicationFrequencyInSeconds;
	
	```
	
2.	Obtenga un contenedor de protección ejecutando los comandos siguientes:
	
	```
	
	$PrimaryCloud = "testcloud"
	$protectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $PrimaryCloud;	
	
	```
	
3.	Inicie la asociación del contenedor de protección con la nube:
	
	```
	
	$associationJob = Start-AzureSiteRecoveryProtectionProfileAssociationJob -ProtectionProfile $profileResult -PrimaryProtectionContainer $protectionContainer;		
	
	```
	
4.	Cuando haya finalizado el trabajo, ejecute el siguiente comando:

	```
	
	$job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
	if($job -eq $null -or $job.StateDescription -ne "Completed")
	{
		$isJobLeftForProcessing = $true;
	}
	
	```

5.	Cuando haya finalizado el procesamiento, ejecute el siguiente comando:

	```
	Do
	{
		$job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
		Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
		if($job -eq $null -or $job.StateDescription -ne "Completed")
		{
			$isJobLeftForProcessing = $true;
		}
		
		if($isJobLeftForProcessing)
		{
			Start-Sleep -Seconds 60
		}
	}While($isJobLeftForProcessing)
		
	```

Para comprobar la finalización de la operación, siga los pasos en [Supervisión de la actividad](#monitor).

## Paso 8: Configuración de la asignación de red
Antes de comenzar la asignación de red, compruebe que las máquinas virtuales en el servidor de VMM de origen están conectadas a una red de VM. Además, cree una o varias redes virtuales de Azure. Tenga en cuenta que pueden asignarse varias redes de VM a una sola red de Azure.

Tenga en cuenta que si la red de destino tiene varias subredes y una de estas subredes tiene el mismo nombre que la subred en la que se encuentra la máquina virtual de origen, la máquina virtual de réplica se conectará a esa subred de destino después de la conmutación por error. Si no hay ninguna subred de destino con un nombre coincidente, la máquina virtual se conectará a la primera subred de la red.

El primer comando obtiene los servidores para el almacén actual de Azure Site Recovery. El comando almacena los servidores de Microsoft Azure Site Recovery en la variable de matriz $Servers.

```
$Servers = Get-AzureSiteRecoveryServer

```

El segundo comando obtiene la red de recuperación del sitio para el primer servidor de la matriz $Servers. El comando almacena las redes en la variable $Networks.

```

$Networks = Get-AzureSiteRecoveryNetwork -Server $Servers[0]

```

El tercer comando obtiene las suscripciones de Azure mediante el cmdlet Get-AzureSubscription y, a continuación, almacena ese valor en la variable $Subscriptions.

```

$Subscriptions = Get-AzureSubscription

```

El cuarto comando obtiene redes virtuales de Azure mediante el cmdlet Get-AzureVNetSite y, a continuación, ese valor en la variable $AzureVmNetworks.

```

$AzureVmNetworks = Get-AzureVNetSite

```

El cmdlet final crea una asignación entre la red principal y la red de la máquina virtual de Azure. El cmdlet especifica la red principal como el primer elemento de $Networks. El cmdlet especifica una red de máquina virtual como el primer elemento de $AzureVmNetworks mediante su identificador. El comando incluye el identificador de suscripción de Azure.

```

PS C:\> New-AzureSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureSubscriptionId $Subscriptions[0].SubscriptionId -AzureVMNetworkId $AzureVmNetworks[0].Id

```

## Paso 9: Habilitación de la protección para las máquinas virtuales

Una vez que los servidores, las nubes y las redes se configuran correctamente, puede habilitar la protección para las máquinas virtuales en la nube. Tenga en cuenta lo siguiente:

Las máquinas virtuales deben cumplir [Requisitos previos de las máquinas virtuales de Azure](site-recovery-best-practices.md#virtual-machines).

Para habilitar la protección del sistema operativo y el disco del sistema operativo, deben establecerse las propiedades de la máquina virtual. Al crear una máquina virtual en VMM con una plantilla de máquina virtual puede establecer la propiedad. También puede establecer estas propiedades para máquinas virtuales existentes en las pestañas **General** y **Configuración del hardware** de las propiedades de la máquina virtual. Si no ve estas propiedades en VMM, podrá configurarlas en el portal de Azure Site Recovery.


	
1.	Para habilitar la protección, ejecute el siguiente comando para obtener el contenedor de protección:
		
	```
	
	$ProtectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $CloudName
	
	```
	
2. Obtenga una entidad de protección (VM) ejecutando los comandos siguientes:
		
	```
	
	$protectionEntity = Get-AzureSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer
		
	```
		
3. Habilite DR para VM ejecutando el comando siguiente:

	```
	
	$jobResult = Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity 	-Protection Enable -Force
	
	```
	
## Prueba de la implementación

Para probar la implementación puede realizar una prueba de conmutación por error para una máquina virtual individual o crear un plan de recuperación que incluya numerosas máquinas virtuales y realizar una conmutación por error de prueba para el plan. La conmutación por error de prueba simula su mecanismo de conmutación por error y recuperación en una red aislada. Observe lo siguiente:

- Si después de la conmutación por error desea conectarse a la máquina virtual de Azure mediante Escritorio remoto, habilite Conexión a Escritorio remoto en la máquina virtual antes de ejecutar la prueba.
- Después de la conmutación por error, usará una dirección IP pública para conectarse a la máquina virtual de Azure mediante Escritorio remoto. Si desea realizar esto, asegúrese de no tener ninguna directiva de dominio que impida que se conecte a una máquina virtual mediante una dirección pública.

Para comprobar la finalización de la operación, siga los pasos en [Supervisión de la actividad](#monitor).

### Creación de un plan de recuperación

1. Cree un archivo .xml como plantilla para su plan de recuperación mediante los datos siguientes y, a continuación, guárdelo como "C:\\RPTemplatePath.xml".
2. Cambie RecoveryPlan node Id, Name, PrimaryServerId y SecondaryServerId.
3. Cambie ProtectionEntity node PrimaryProtectionEntityId (vmid desde VMM).
4. Puede agregar más máquinas virtuales añadiendo más nodos ProtectionEntity.
	
	```
	
	<#
	<?xml version="1.0" encoding="utf-16"?>
	<RecoveryPlan Id="d0323b26-5be2-471b-addc-0a8742796610" Name="rp-test" 	PrimaryServerId="9350a530-d5af-435b-9f2b-b941b5d9fcd5" 	SecondaryServerId="21a9403c-6ec1-44f2-b744-b4e50b792387" Description="" 	Version="V2014_07">
	  <Actions />
	  <ActionGroups>
	    <ShutdownAllActionGroup Id="ShutdownAllActionGroup">
	      <PreActionSequence />
	      <PostActionSequence />
	    </ShutdownAllActionGroup>
	    <FailoverAllActionGroup Id="FailoverAllActionGroup">
	      <PreActionSequence />
	      <PostActionSequence />
	    </FailoverAllActionGroup>
	    <BootActionGroup Id="DefaultActionGroup">
	      <PreActionSequence />
	      <PostActionSequence />
	      <ProtectionEntity PrimaryProtectionEntityId="d4c8ce92-a613-4c63-9b03-	cf163cc36ef8" />
	    </BootActionGroup>
	  </ActionGroups>
	  <ActionGroupSequence>
	    <ActionGroup Id="ShutdownAllActionGroup" ActionId="ShutdownAllActionGroup" 	Before="FailoverAllActionGroup" />
	    <ActionGroup Id="FailoverAllActionGroup" ActionId="FailoverAllActionGroup" 	After="ShutdownAllActionGroup" Before="DefaultActionGroup" />
	    <ActionGroup Id="DefaultActionGroup" ActionId="DefaultActionGroup" After="FailoverAllActionGroup"/>
	  </ActionGroupSequence>
	</RecoveryPlan>
	#>
	
	```
	
4. Complete los datos en la plantilla:
	
	```
	
	$TemplatePath = "C:\RPTemplatePath.xml";
	
	```
	
5. Creación de RecoveryPlan:
	
	```
	
	$RPCreationJob = New-AzureSiteRecoveryRecoveryPlan -File $TemplatePath -WaitForCompletion;
	
	```
	
### Ejecución de una conmutación por error de prueba

1.	Obtenga el objeto RecoveryPlan ejecutando el comando siguiente:
	
	```
	
	$RPObject = Get-AzureSiteRecoveryRecoveryPlan -Name $RPName;
	
	```
	
2.	Inicie la conmutación por error de prueba ejecutando el siguiente comando:
	
	```
	
	$jobIDResult = Start-AzureSiteRecoveryTestFailoverJob -RecoveryPlan $RPObject -Direction PrimaryToRecovery;
	
	```
	
## <a name=monitor></a> Supervisión de la actividad

Utilice los comandos siguientes para supervisar la actividad. Tenga en cuenta que debe esperar entre los trabajos para que el procesamiento finalice.

```

Do
{
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
        	$isJobLeftForProcessing = $true;
        }

	if($isJobLeftForProcessing)
        {
        	Start-Sleep -Seconds 60
        }
}While($isJobLeftForProcessing)

```


## Pasos siguientes

[Obtenga más información](https://msdn.microsoft.com/library/dn850420.aspx) sobre cmdlets de PowerShell de Azure Site Recovery. </a>.

<!---HONumber=AcomDC_0211_2016-->