<properties 
   pageTitle="Establecimiento de una dirección IP privada interna estática"
   description="Descripción y administración de direcciones IP estáticas internas (DIP)"
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
   ms.date="12/07/2015"
   ms.author="telmos" />

# Establecimiento de una dirección IP privada interna estática
En la mayoría de los casos, no necesitará especificar una dirección IP interna estática para la máquina virtual. Las máquinas virtuales de una red virtual recibirán automáticamente una dirección IP interna dentro de un intervalo que especifique. Pero en algunos casos, tiene sentido especificar una dirección IP estática para una máquina virtual concreta. Por ejemplo, si la máquina virtual va a ejecutar DNS o será un controlador de dominio.

>[AZURE.NOTE]Una dirección IP interna estática permanece con la máquina virtual incluso a través de un estado de detención o desaprovisionamiento.

## Comprobación de que una dirección IP concreta está disponible
Para comprobar si la dirección IP *10.0.0.7* está disponible en una red virtual denominada *TestVnet*, ejecute el siguiente comando de PowerShell y compruebe el valor de *IsAvailable*:

	Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

	IsAvailable          : True
	AvailableAddresses   : {}
	OperationDescription : Test-AzureStaticVNetIP
	OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
	OperationStatus      : Succeeded

>[AZURE.NOTE]Si desea probar el comando anterior en un entorno seguro, siga las directrices de [Creación de una red virtual](../virtual-network/virtual-networks-create-vnet.md) para crear una máquina virtual denominada *TestVnet* y asegurarse de que utiliza el espacio de direcciones *10.0.0.0/8*.

## Especificación de una dirección IP interna estática al crear una máquina virtual
El siguiente script de PowerShell crea un nuevo servicio en la nube denominado *TestService*; a continuación, recupera una imagen de Azure, crea una máquina virtual denominada *TestVM* en el nuevo servicio en la nube con la imagen recuperada, establece que la máquina virtual esté en una subred llamada *Subnet-1* y establece *10.0.0.7* como una dirección IP interna estática para la máquina virtual:

	New-AzureService -ServiceName TestService -Location "Central US"
	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| Set-AzureSubnet –SubnetNames Subnet-1 `
	| Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
	| New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## Recuperación de la información de la IP interna estática para una máquina virtual
Para ver la información de la IP interna estática para la máquina virtual creada con este script, ejecute el siguiente comando de PowerShell y observe los valores de *IpAddress*:

	Get-AzureVM -Name TestVM -ServiceName TestService

	DeploymentName              : TestService
	Name                        : TestVM
	Label                       : 
	VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
	InstanceStatus              : Provisioning
	IpAddress                   : 10.0.0.7
	InstanceStateDetails        : Windows is preparing your computer for first use...
	PowerState                  : Started
	InstanceErrorCode           : 
	InstanceFaultDomain         : 0
	InstanceName                : TestVM
	InstanceUpgradeDomain       : 0
	InstanceSize                : Small
	HostName                    : rsR2-797
	AvailabilitySetName         : 
	DNSName                     : http://testservice000.cloudapp.net/
	Status                      : Provisioning
	GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
	ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
	PublicIPAddress             : 
	PublicIPName                : 
	NetworkInterfaces           : {}
	ServiceName                 : TestService
	OperationDescription        : Get-AzureVM
	OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
	OperationStatus             : OK

## Eliminación de una dirección IP interna estática de una máquina virtual
Para quitar la dirección IP interna estática agregada a la máquina virtual en el script anterior, ejecute el siguiente comando de PowerShell:
	
	Get-AzureVM -ServiceName TestService -Name TestVM `
	| Remove-AzureStaticVNetIP `
	| Update-AzureVM

## Incorporación de una dirección IP interna estática a una máquina virtual existente
Para agregar una dirección IP interna estática a la máquina virtual creada con el script anterior, ejecute el siguiente comando:

	Get-AzureVM -ServiceName TestService000 -Name TestVM `
	| Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
	| Update-AzureVM

## Pasos siguientes

[IP reservada](../virtual-networks-reserved-public-ip)

[IP pública a nivel de instancia (ILIP)](../virtual-networks-instance-level-public-ip)

[API de REST de IP reservada](https://msdn.microsoft.com/library/azure/dn722420.aspx)
 

<!---HONumber=AcomDC_1210_2015-->