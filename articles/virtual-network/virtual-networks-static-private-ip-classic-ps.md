<properties 
   pageTitle="Establecimiento de una IP privada estática en el modo clásico con PowerShell | Microsoft Azure"
   description="Descripción de las IP privadas estáticas (DIP) y su administración en el modo clásico y PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="telmos" />

# Configuración de una dirección IP privada estática (clásica) en PowerShell

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artículo trata sobre el modelo de implementación clásico. También puede [administrar la dirección IP privada estática en el modelo de implementación del Administrador de recursos](virtual-networks-static-private-ip-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

En los siguientes comandos de PowerShell de ejemplo se presupone que ya se ha creado un entorno simple. Si desea ejecutar los comandos que aparecen en este documento, cree primero el entorno de prueba descrito en [creación de una red virtual](virtual-networks-create-vnet-classic-netcfg-ps.md).

## Comprobación de que una dirección IP concreta está disponible
Para comprobar si la dirección IP *192.168.1.101* está disponible en una red virtual denominada *TestVnet*, ejecute el siguiente comando de PowerShell y compruebe el valor de *IsAvailable*:

	Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 

Resultado esperado:

	IsAvailable          : True
	AvailableAddresses   : {}
	OperationDescription : Test-AzureStaticVNetIP
	OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
	OperationStatus      : Succeeded

## Especificación de una dirección IP privada estática al crear una VM
El siguiente script de PowerShell crea un nuevo servicio en la nube denominado *TestService*; a continuación, recupera una imagen de Azure, crea una máquina virtual denominada *DNS01* en el nuevo servicio en la nube con la imagen recuperada, establece que la máquina virtual esté en una subred llamada *Front-end* y establece *192.168.1.7* como una dirección IP privada estática para la VM:

	New-AzureService -ServiceName TestService -Location "Central US"
	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name DNS01 -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| Set-AzureSubnet –SubnetNames FrontEnd `
	| Set-AzureStaticVNetIP -IPAddress 192.168.1.7 `
	| New-AzureVM -ServiceName "TestService" –VNetName TestVNet

Resultado esperado:

	WARNING: No deployment found in service: 'TestService'.
	OperationDescription OperationId                          OperationStatus
	-------------------- -----------                          ---------------
	New-AzureService     fcf705f1-d902-011c-95c7-b690735e7412 Succeeded      
	New-AzureVM          3b99a86d-84f8-04e5-888e-b6fc3c73c4b9 Succeeded  

## Recuperación de la información de la dirección IP privada estática para una VM
Para ver la información de la dirección IP privada estática para la máquina virtual creada con este script, ejecute el siguiente comando de PowerShell y observe los valores de *IpAddress*:

	Get-AzureVM -Name DNS01 -ServiceName TestService

Resultado esperado:

	DeploymentName              : TestService
	Name                        : DNS01
	Label                       : 
	VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
	InstanceStatus              : Provisioning
	IpAddress                   : 192.168.1.7
	InstanceStateDetails        : Windows is preparing your computer for first use...
	PowerState                  : Started
	InstanceErrorCode           : 
	InstanceFaultDomain         : 0
	InstanceName                : DNS01
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

## Eliminación de una dirección IP privada estática de una VM
Para quitar la dirección IP privada estática agregada a la máquina virtual en el script anterior, ejecute el siguiente comando de PowerShell:
	
	Get-AzureVM -ServiceName TestService -Name DNS01 `
	| Remove-AzureStaticVNetIP `
	| Update-AzureVM

Resultado esperado:

	OperationDescription OperationId                          OperationStatus
	-------------------- -----------                          ---------------
	Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded

## Adición de una dirección IP privada estática a una VM existente
Para agregar una dirección IP privada estática a la VM creada con el script anterior, ejecute el siguiente comando:

	Get-AzureVM -ServiceName TestService -Name DNS01 `
	| Set-AzureStaticVNetIP -IPAddress 192.168.1.7 `
	| Update-AzureVM

Resultado esperado:

	OperationDescription OperationId                          OperationStatus
	-------------------- -----------                          ---------------
	Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 

## Pasos siguientes

- Obtenga más información acerca de las [direcciones IP públicas reservadas](../virtual-networks-reserved-public-ip).
- Obtenga información sobre las [direcciones IP públicas a nivel de instancia (ILPIP)](../virtual-networks-instance-level-public-ip).
- Consulte las [API de REST de IP reservada](https://msdn.microsoft.com/library/azure/dn722420.aspx).

<!---HONumber=AcomDC_0211_2016-->