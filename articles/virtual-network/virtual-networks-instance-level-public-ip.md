<properties 
   pageTitle="Dirección IP pública de nivel de instancia (ILPIP) | Microsoft Azure"
   description="Descripción de ILPIP (PIP) y su administración"
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
   ms.date="02/10/2016"
   ms.author="telmos" />

# Dirección IP pública de nivel de instancia
Una IP pública de nivel de instancia (ILPIP) es una dirección IP pública que se puede asignar directamente a la máquina virtual o a la instancia de rol, en lugar de a un servicio en la nube en el que reside la máquina virtual o la instancia de rol. Esto no reemplaza a la dirección VIP (Virtual IP o IP virtual) que está asignada al servicio en la nube. Es más bien una dirección IP adicional que puede usar para conectarse directamente a la máquina virtual o instancia de rol.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-network-ip-addresses-overview-arm.md).

Asegúrese de que comprende cómo las [direcciones IP](virtual-network-ip-addresses-overview-classic.md) funcionan en Azure.

>[AZURE.NOTE] Antes, a la ILPIP se la llamaba PIP, que significa Public IP (dirección IP pública).

![Diferencia entre ILPIP y VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Como se muestra en la Ilustración 1, al servicio en la nube se accede mediante una VIP, mientras que las máquinas virtuales individuales normalmente son accesibles mediante VIP:&lt;número de puerto&gt;. Al asignar una ILPIP a una máquina virtual específica, se puede tener acceso directamente a esa máquina virtual mediante esa dirección IP.

Cuando se crea un servicio en la nube en Azure, los registros de DNS A correspondientes se crean automáticamente para permitir el acceso al servicio mediante un nombre de dominio completo (FQDN) en lugar de usar la VIP real. Se produce el mismo proceso para la ILPIP, lo que permite el acceso a la máquina virtual o instancia de rol mediante el FQDN en lugar de la ILPIP. Por ejemplo, si crea un servicio en la nube denominado *contosoadservice* y configura un rol web denominado *contosoweb* con dos instancias, Azure registrará los siguientes registros A para las instancias:

- contosoweb\_IN\_0.contosoadservice.cloudapp.net
- contosoweb\_IN\_1.contosoadservice.cloudapp.net 

>[AZURE.NOTE] Solo puede asignar una ILPIP para cada máquina virtual o instancia de rol. Puede usar hasta 5 ILPIP por suscripción. Por ahora, no se admiten ILPIP para máquinas virtuales con varias NIC.

## ¿Por qué debo solicitar una ILPIP?
Si desea que pueda conectarse a la máquina virtual o instancia de rol mediante una dirección IP asignada directamente a ella, en lugar de usar la VIP:&lt;número de puerto&gt; del servicio en la nube, solicite una ILPIP para la máquina virtual o la instancia de rol. - **FTP pasivo**: Por tener una ILPIP en la máquina virtual, puede recibir tráfico en cualquier puerto, no tendrá que abrir un extremo para recibir tráfico. Esto habilita escenarios como FTP pasivo donde los puertos se seleccionan dinámicamente. - **IP saliente**: El tráfico saliente procedente de la máquina virtual sale con la ILPIP como origen y esto identifica de forma única a la máquina virtual en entidades externas.

## Solicitud de una ILPIP durante la creación de máquinas virtuales
El script de PowerShell siguiente crea un nuevo servicio en la nube denominado *FTPService*, a continuación, recupera una imagen de Azure y crea una máquina virtual denominada *FTPInstance* usando la imagen recuperada, establece la máquina virtual para usar una ILPIP y agrega la máquina virtual al nuevo servicio:

	New-AzureService -ServiceName FTPService -Location "Central US"
	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

## Recuperación de información de ILPIP para una máquina virtual
Para ver la información de ILPIP para la máquina virtual creada con este script, ejecute el siguiente comando de PowerShell y observe los valores de *PublicIPAddress* y *PublicIPName*:

	Get-AzureVM -Name FTPInstance -ServiceName FTPService

	DeploymentName              : FTPService
	Name                        : FTPInstance
	Label                       : 
	VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
	InstanceStatus              : ReadyRole
	IpAddress                   : 100.74.118.91
	InstanceStateDetails        : 
	PowerState                  : Started
	InstanceErrorCode           : 
	InstanceFaultDomain         : 0
	InstanceName                : FTPInstance
	InstanceUpgradeDomain       : 0
	InstanceSize                : Small
	HostName                    : FTPInstance
	AvailabilitySetName         : 
	DNSName                     : http://ftpservice888.cloudapp.net/
	Status                      : ReadyRole
	GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
	ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
	PublicIPAddress             : 104.43.142.188
	PublicIPName                : ftpip
	NetworkInterfaces           : {}
	ServiceName                 : FTPService
	OperationDescription        : Get-AzureVM
	OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
	OperationStatus             : OK

## Supresión de una ILPIP de una máquina virtual
Para quitar la ILPIP agregada a la máquina virtual en el script anterior, ejecute el siguiente comando de PowerShell:
	
	Get-AzureVM -ServiceName FTPService -Name FTPInstance `
	| Remove-AzurePublicIP `
	| Update-AzureVM

## Agregación de una ILPIP a una máquina virtual existente
Para agregar una ILPIP a la máquina virtual creada con el script anterior, ejecute el siguiente comando:

	Get-AzureVM -ServiceName FTPService -Name FTPInstance `
	| Set-AzurePublicIP -PublicIPName ftpip2 `
	| Update-AzureVM

## Asociación de una ILPIP a una máquina virtual mediante un archivo de configuración de servicio
También puede asociar una ILPIP a una máquina virtual mediante un archivo de configuración de servicio (CSCFG). El xml de ejemplo siguiente muestra cómo configurar un servicio en la nube para que use una IP reservada denominada *MyReservedIP* como ILPIP para una instancia de rol:
	
	<?xml version="1.0" encoding="utf-8"?>
	<ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
	  <Role name="WebRole1">
	    <Instances count="1" />
	    <ConfigurationSettings>
	      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
	    </ConfigurationSettings>
	  </Role>
      <NetworkConfiguration>
	    <VirtualNetworkSite name="VNet"/>
	    <AddressAssignments>
	      <InstanceAddress roleName="VMRolePersisted">
	        <Subnets>
	          <Subnet name="Subnet1"/>
	          <Subnet name="Subnet2"/>
	        </Subnets>
	        <PublicIPs>
	          <PublicIP name="MyReservedIP" domainNameLabel="MyReservedIP" />
	        </PublicIPs>
	      </InstanceAddress>
	    </AddressAssignments>
	  </NetworkConfiguration>
	</ServiceConfiguration>

## Pasos siguientes

- Comprenda cómo el [direccionamiento IP](virtual-network-ip-addresses-overview-classic.md) funciona en el modelo de implementación clásico.

- Obtenga información acerca de las [direcciones IP reservadas](../virtual-networks-reserved-public-ip).
 

<!---HONumber=AcomDC_0218_2016-->