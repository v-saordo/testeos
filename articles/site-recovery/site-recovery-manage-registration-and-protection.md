<properties
	pageTitle="Quitar servidores y deshabilitar la protección | Microsoft Azure" 
	description="En este artículo se describe cómo anular el registro de servidores desde un almacén de Site Recovery y deshabilitar la protección para máquinas virtuales y servidores físicos." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="02/22/2016" 
	ms.author="raynew"/>

# Quitar servidores y deshabilitar la protección

El servicio Azure Site Recovery contribuye a su estrategia de continuidad empresarial y recuperación ante desastres (BCDR) mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores físicos. Las máquinas se pueden replicar a Azure o a un centro de datos secundario local. Para obtener una introducción rápida, lea [¿Qué es Azure Site Recovery?](site-recovery-overview.md)

## Información general

Este artículo describe cómo anular el registro de servidores desde el almacén de Site Recovery y cómo deshabilitar la protección para máquinas virtuales protegidas por Site Recovery.

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Anulación del registro de un servidor VMM

El registro de un servidor VMM se anula desde un almacén mediante la eliminación del servidor en la pestaña **Servidores** del Portal de Azure Site Recovery. Observe lo siguiente:

-  **Servidor VMM conectado**: se recomienda que anular el registro del servidor VMM cuando está conectado a Azure. Esto garantiza que la configuración en el servidor VMM local y los servidores VMM asociados a él (servidores VMM que contienen las nubes que se asignan a las nubes en el servidor que desea eliminar) se limpien adecuadamente. Se recomienda quitar un servidor no conectado solo si hay un problema permanente con la conectividad.
- **Servidor VMM no conectado**: si el servidor VMM no está conectado cuando se elimina, necesitará ejecutar un script manualmente para realizar la limpieza. El script está disponible en la [galería de Microsoft](https://gallery.technet.microsoft.com/scriptcenter/Cleanup-Script-for-Windows-95101439). Anote el identificador de VMM del servidor para completar el proceso de limpieza manual.
- **Servidor VMM en clúster**: si necesita anular el registro de un servidor VMM que está implementando en un clúster, haga lo siguiente:

	- Si el servidor está conectado, elimine el servidor VMM conectado en la pestaña **Servidores**. Para desinstalar el proveedor en el servidor, inicie sesión en cada nodo del clúster y desinstálelo desde el Panel de Control. Ejecute el script de limpieza indicado en la sección anterior en todos los nodos pasivos del clúster para eliminar las entradas de registro.
	- Si el servidor no está conectado, deberá ejecutar el script de limpieza en todos los nodos del clúster.

### Anulación del registro de un servidor VMM desconectado

En el servidor VMM que desea quitar:

1. Anule el registro del servidor VMM desde el portal de Azure.
2. En el servidor VMM, descargue el script de limpieza.
3. Abra PowerShell con la ejecución como opción Ejecutar como administrador para cambiar la directiva de ejecución para el ámbito predeterminado (LocalMachine).
4. Siga las instrucciones del script. 

En los servidores VMM que tienen nubes que se emparejan con nubes en el servidor que está quitando:

1. Ejecute el script de limpieza y siga los pasos 2 a 4.
2. Especifique el identificador de VMM para el servidor VMM que se ha eliminado del registro. 
3. Este script quitará la información de registro para el servidor VMM y la información de emparejamiento en la nube.


## Anulación del registro de un servidor Hyper-V en un sitio de Hyper-V

Cuando se implementa Azure Site Recovery para proteger las máquinas virtuales ubicadas en un servidor Hyper-V en un sitio de Hyper-V (sin ningún servidor VMM) puede anular el registro de un servidor Hyper-V desde un almacén como sigue:

1. Deshabilite la protección para las máquinas virtuales ubicadas en el servidor VMM.
2. En la pestaña **Servidores** en el Portal de Azure Site Recovery, seleccione el servidor > Eliminar. El servidor no tiene que estar conectado a Azure para hacerlo.
3. Ejecute el siguiente script para limpiar la configuración en el servidor y anular su registro del almacén. 

	    pushd .
	    try
	    {
		     $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
		     $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
		     $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
		     $isAdmin=$principal.IsInRole($administrators)
		     if (!$isAdmin)
		     {
		        "Please run the script as an administrator in elevated mode."
		        $choice = Read-Host
		        return;       
		     }
	
		    $error.Clear()    
			"This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
			$choice =  Read-Host
		
			if (!($choice -eq 'Y' -or $choice -eq 'y'))
			{
			"Stopping cleanup."
			return;
			}
		
			$serviceName = "dra"
			$service = Get-Service -Name $serviceName
			if ($service.Status -eq "Running")
		    {
				"Stopping the Azure Site Recovery service..."
				net stop $serviceName
			}
		
		    $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
			$registrationPath = $asrHivePath + '\Registration'
			$proxySettingsPath = $asrHivePath + '\ProxySettings'
		    $draIdvalue = 'DraID'
	    
		    if (Test-Path $asrHivePath)
		    {
		        if (Test-Path $registrationPath)
		        {
		            "Removing registration related registry keys."	
			        Remove-Item -Recurse -Path $registrationPath
		        }
	
		        if (Test-Path $proxySettingsPath)
	        {
			        "Removing proxy settings"
			        Remove-Item -Recurse -Path $proxySettingsPath
		        }
	
		        $regNode = Get-ItemProperty -Path $asrHivePath
		        if($regNode.DraID -ne $null)
		        {            
		            "Removing DraId"
		            Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
		        }
			    "Registry keys removed."
		    }
	
		    # First retrive all the certificates to be deleted
			$ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
			# Open a cert store object
			$store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
			$store.Open('ReadWrite')
			# Delete the certs
			"Removing all related certificates"
		    foreach ($cert in $ASRcerts)
		    {
			    $store.Remove($cert)
		    }
	    }catch
	    {	
		    [system.exception]
		    Write-Host "Error occured" -ForegroundColor "Red"
		    $error[0] 
		    Write-Host "FAILED" -ForegroundColor "Red"
	    }
	    popd


## Detención de la protección de máquina virtual de Hyper-V

Si desea detener la protección de una máquina virtual de Hyper-V, deberá quitar la protección para ella. Dependiendo de cómo quite la protección deberá borrar la configuración de la protección manualmente en el equipo.

### Eliminación de la protección

1. En la pestaña **Máquinas virtuales** de las propiedades de la nube, seleccione la máquina virtual > **Quitar**.
2. En la página **Confirmar eliminación de máquina Virtual** tiene dos opciones:

	- **Deshabilitar la protección**: si habilita y guarda esta opción, la máquina virtual ya no estará protegida por Site Recovery. La configuración de protección de la máquina virtual se limpiará automáticamente.
	- **Quitar del almacén**: si selecciona esta opción, solo se eliminará la máquina virtual del almacén de Site Recovery. La configuración de protección local de la máquina virtual no se verá afectada. Tendrá que borrar la configuración manualmente para quitar la configuración de protección, la máquina virtual de la suscripción de Azure y la configuración de protección que tiene que borrar manualmente mediante las instrucciones siguientes.

Si opta por eliminar la máquina virtual y sus discos duros, se quitarán de la ubicación de destino.

### Limpieza de la configuración de protección manual (entre sitios VMM)

Si seleccionó **Detener administración de máquina virtual**, limpie la configuración manualmente:

1. En el servidor principal, ejecute este script desde la consola VMM para limpiar la configuración de la máquina virtual principal. En la consola VMM, haga clic en el botón PowerShell para abrir la consola VMM PowerShell. Reemplace SQLVM1 por el nombre de la máquina virtual.

	     $vm = get-scvirtualmachine -Name "SQLVM1"
	     Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. En el servidor VMM secundario, ejecute este script para limpiar la configuración de la máquina virtual secundaria:

	    $vm = get-scvirtualmachine -Name "SQLVM1"
	    Remove-SCVirtualMachine -VM $vm -Force

3. En el servidor VMM secundario, después de ejecutar el script, actualice las máquinas virtuales en el servidor host de Hyper-V para que se vuelva a detectar la máquina virtual secundaria en la consola VMM.
4. Los pasos anteriores borrarán el servidor VMM único de configuración de replicación. Si desea quitar la replicación de máquina virtual para la máquina virtual. Deberá realizar los siguientes pasos en la máquina virtual tanto principal como secundaria. Ejecute el siguiente script para quitar la replicación y reemplace SQLVM1 por el nombre de la máquina virtual.

	    Remove-VMReplication –VMName “SQLVM1”


### Limpieza de la configuración de protección manual (entre sitios VMM locales y Azure)

1. En el servidor VMM de origen, ejecute este script para limpiar la configuración de la máquina virtual principal:

	    $vm = get-scvirtualmachine -Name "SQLVM1"
	    Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. Los pasos anteriores borrarán el servidor VMM único de configuración de replicación. Una vez haya quitado la replicación en el servidor VMM, asegúrese de quitar la replicación para la máquina virtual que se ejecuta en el servidor host de Hyper-V con este script. Reemplace SQLVM1 por el nombre de la máquina virtual y host01.contoso.com por el nombre del servidor host de Hyper-V.

	    $vmName = "SQLVM1"
	    $hostName  = "host01.contoso.com"
	    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
	    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
	    $replicationService.RemoveReplicationRelationship($vm.__PATH)

### Limpieza de la configuración de protección manual (entre sitios Hyper-V y Azure)

1. En el servidor host de Hyper-V de origen, utilice este script para quitar la replicación de la máquina virtual. Reemplace SQLVM1 por el nombre de la máquina virtual.

	    $vmName = "SQLVM1"
	    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
	    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
	    $replicationService.RemoveReplicationRelationship($vm.__PATH)

## Detención de la protección de una máquina virtual de VMware o de un servidor físico

Si desea detener la protección de una máquina virtual de VMware o de un servidor físico, deberá quitar la protección para ella. Dependiendo de cómo quite la protección deberá borrar la configuración de la protección manualmente en el equipo.

### Eliminación de la protección

1. En la pestaña **Máquinas virtuales** de las propiedades de la nube, seleccione la máquina virtual > **Quitar**.
2. En **Quitar máquina virtual**, seleccione una de las opciones:

	- **Deshabilitar la protección (uso para la recuperación de detalles y cambio del tamaño de volumen)**: solo verá y podrá habilitar esta opción si ha realizado lo siguiente:
		- **Cambio del tamaño del volumen de la máquina virtual**: cuando cambia el tamaño de un volumen, la máquina virtual entra en un estado crítico. En este caso, seleccione esta opción. Deshabilita la protección mientras conserva los puntos de recuperación en Azure. Cuando se vuelve a habilitar la protección de la máquina, se transferirán los datos para el volumen cuyo tamaño ha cambiado a Azure.
		- **Ejecutar una conmutación por error**: una vez que ha probado su entorno ejecutando una conmutación por error de máquinas virtuales de VMware locales o en servidores físicos en Azure, seleccione esta opción para empezar a proteger las máquinas virtuales locales de nuevo. Esta opción deshabilita cada máquina virtual y, a continuación, deberá volver a habilitar la protección. Observe lo siguiente:
			- La deshabilitación de la máquina virtual con esta configuración no afecta a la máquina virtual de réplica en Azure.
			- No debe desinstalar el servicio de movilidad de la máquina virtual.
	
	- **Deshabilitar la protección**: si habilita y guarda esta opción, la máquina ya no estará protegida por Site Recovery. La configuración de protección de la máquina se borrará automáticamente.
	- **Quitar del almacén**: si selecciona esta opción, solo se quitará la máquina del almacén de Site Recovery. La configuración de la protección local de la máquina no se verá afectada. Para quitar la configuración en el equipo y quitar la máquina virtual de la suscripción de Azure, necesitará borrar la configuración desinstalando el servicio de movilidad.
	
		![Eliminación de opciones](./media/site-recovery-manage-registration-and-protection/remove-vm.png)

<!---HONumber=AcomDC_0224_2016-->