<properties 
   pageTitle="Administración de listas de control de acceso (ACL) para extremos mediante PowerShell"
   description="Aprenda a administrar las ACL con PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# Administración de listas de control de acceso (ACL) para extremos mediante PowerShell

Puede crear y administrar listas de control de acceso (ACL) de red para extremos mediante Azure PowerShell o en el Portal de administración. En este tema, encontrará procedimientos para realizar tareas comunes de ACL mediante PowerShell. Para obtener la lista de cmdlets de Azure PowerShell, consulte [Cmdlets de administración de Azure](http://go.microsoft.com/fwlink/?LinkId=317721). Para obtener más información acerca de las ACL, consulte [Acerca de las listas de control de acceso (ACL) de red](../virtual-networks-acl). Si quiere administrar la ACL usando el Portal de administración, consulte [Cómo establecer extremos en una máquina virtual](../virtual-machines-set-up-endpoints/).

## Administrar las ACL de red mediante Azure PowerShell

Puede usar cmdlets de Azure PowerShell para crear, quitar y configurar (establecer) listas de control de acceso (ACL) de red. Hemos incluido ejemplos de algunas de las formas en que puede configurar una ACL mediante PowerShell.

Para recuperar una lista completa de los cmdlets de PowerShell para ACL, puede utilizar cualquiera de los cmdlets siguientes:

	Get-Help *AzureACL*
	Get-Command -Noun AzureACLConfig

### Crear una ACL de red con reglas que permitan el acceso desde una subred remota

En el ejemplo siguiente se muestra una forma de crear una nueva ACL que contiene reglas. Esta ACL se aplica después a un extremo de la máquina virtual. Las reglas de ACL del ejemplo siguiente permitirán el acceso desde una subred remota. Para crear una nueva ACL de red con reglas de permiso para una subred remota, abra un ISE de Azure PowerShell. Copie y pegue el siguiente script, configure el script con sus propios valores y, a continuación, ejecute el script.

1. Cree el nuevo objeto de ACL de red.

		$acl1 = New-AzureAclConfig

1. Establezca una regla que permita el acceso desde una subred remota. En el ejemplo siguiente, se establece la regla *100* (que tiene prioridad sobre la regla 200 y otras superiores) para permitir el acceso de la subred remota *10.0.0.0/8* al extremo de máquina virtual. Reemplace los valores con sus propios requisitos de configuración. El nombre “SharePoint ACL config” se debe reemplazar con el nombre descriptivo que desee asignar a esta regla.

		Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
			–Action permit –RemoteSubnet "10.0.0.0/8" `
			–Description "SharePoint ACL config"

1. Para otras reglas adicionales, repita el cmdlet, reemplazando los valores con sus propios requisitos de configuración. Asegúrese de cambiar el orden del número de regla para reflejar el orden en que desea que se apliquen las reglas. El número de regla menor tiene prioridad sobre el número mayor.

		Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
			–Action permit –RemoteSubnet "157.0.0.0/8" `
			–Description "web frontend ACL config"

1. A continuación, puede crear un nuevo extremo (Add) o establecer la ACL para un extremo existente (Set). En este ejemplo, agregaremos un nuevo extremo de máquina virtual denominado “web” y actualizaremos el extremo de máquina virtual con los valores de ACL.

		Get-AzureVM –ServiceName $serviceName –Name $vmName `
		| Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
		| Update-AzureVM

1. A continuación, combine los cmdlets y ejecute el script. En este ejemplo, los cmdlets combinados serían similares a lo siguiente:

		$acl1 = New-AzureAclConfig
		Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
			–Action permit –RemoteSubnet "10.0.0.0/8" `
			–Description "Sharepoint ACL config"
		Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
			–Action permit –RemoteSubnet "157.0.0.0/8" `
			–Description "web frontend ACL config"
		Get-AzureVM –ServiceName $serviceName –Name $vmName `
		|Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
		|Update-AzureVM

### Quitar una regla de ACL de red que permita el acceso desde una subred remota

En el ejemplo siguiente se muestra una forma de quitar una regla de ACL de red. Para quitar una regla de ACL de red con reglas de permiso para una subred remota, abra un ISE de PowerShell de Azure. Copie y pegue el siguiente script, configure el script con sus propios valores y, a continuación, ejecute el script.

1. El primer paso consiste en obtener el objeto de ACL de red para el extremo de máquina virtual. Después quitará la regla de ACL. En este caso, la estamos quitando por el identificador de regla. Esto solo quitará el identificador de regla 0 de la ACL. No quita el objeto de ACL del extremo de máquina virtual. 

		Get-AzureVM –ServiceName $serviceName –Name $vmName `
		| Get-AzureAclConfig –EndpointName "web" `
		| Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1

1. A continuación, debe aplicar el objeto de ACL de red al extremo de máquina virtual y actualizar la máquina virtual.

		Get-AzureVM –ServiceName $serviceName –Name $vmName `
		| Set-AzureEndpoint –ACL $acl1 –Name "web" `
		| Update-AzureVM

### Quitar una ACL de red de un extremo de máquina virtual

En ciertas situaciones, puede que desee quitar un objeto de ACL de red de un extremo de máquina virtual. Para ello, abra un ISE de Azure PowerShell. Copie y pegue el siguiente script, configure el script con sus propios valores y, a continuación, ejecute el script.

		Get-AzureVM –ServiceName $serviceName –Name $vmName `
		| Remove-AzureAclConfig –EndpointName "web" `
		| Update-AzureVM

## Otras referencias

[¿Qué es una lista de control de acceso (ACL) de red?](../virtual-networks-acl)

[Configuración de la comunicación con la máquina virtual](http://go.microsoft.com/fwlink/?LinkId=303938)

<!---HONumber=AcomDC_1217_2015-->