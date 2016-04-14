<properties
   pageTitle="IP reservada | Microsoft Azure"
   description="Descripción y administración de las IP reservadas"
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

# Descripción general de una IP reservada
Las direcciones IP en Azure se dividen en dos categorías: dinámicas y reservadas. Las direcciones IP públicas administradas por Azure son dinámicas de forma predeterminada. Esto significa que la dirección IP usada para un determinado servicio en la nube (VIP) o para tener acceso a una máquina virtual o instancia de rol directamente (ILPIP) puede cambiar de vez en cuando, cuando los recursos se cierran o desasignan.

Para impedir que cambien las direcciones IP, puede reservar una dirección IP. La IP reservadas únicamente puede usarse como una VIP, lo que garantiza que la dirección IP para el servicio en la nube sea la misma incluso cuando se cierran o desasignan recursos. Además, se puede convertir direcciones IP dinámicas existentes utilizadas como una VIP en una IP reservada.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-network-ip-addresses-overview-arm.md).

Asegúrese de que comprende cómo las [direcciones IP](virtual-network-ip-addresses-overview-classic.md) funcionan en Azure.

## ¿Cuándo se necesita una IP reservada?
- **Para asegurarse de que la dirección IP está reservada en su suscripción**. Si desea reservar una dirección IP que no se libera de su suscripción bajo ninguna circunstancia, debe usar una dirección IP pública reservada.  
- **Desea que su IP permanezca con el servicio en la nube incluso en el estado detenido o desasignado (máquina virtual)**. Si desea tener acceso al servicio mediante una dirección IP que no cambie incluso cuando las máquinas virtuales en el servicio en la nube se detengan o desasignan.
- **Desea estar seguro de que el tráfico saliente de Azure usa una dirección IP predecible**. Es posible que haya configurado un firewall local para permitir solo tráfico procedente de direcciones IP específicas. Al reservar una dirección IP, sabrá la dirección IP de origen y no tendrá que actualizar las reglas de firewall debido a un cambio de IP.

## P+F
1. ¿Puedo usar una IP reservada para todos los servicios de Azure?  
  - Las IP reservadas solo pueden usarse para máquinas virtuales y roles de instancia del servicio en la nube que se hayan expuesto a través de una VIP.
1. ¿Cuántas direcciones IP reservadas puedo tener?  
  - En este momento, todas las suscripciones de Azure tienen autorización para usar 20 direcciones IP reservadas. Sin embargo, puede solicitar direcciones IP reservadas adicionales. Consulte la página [Límites y restricciones de suscripción](../azure-subscription-service-limits/) para obtener más información.
1. ¿Hay un cargo por las IP reservadas?
  - Para obtener información sobre los precios, consulte [Detalles de precios de las direcciones IP reservadas](http://go.microsoft.com/fwlink/?LinkID=398482).
1. ¿Cómo se reserva una dirección IP?
  - Puede usar PowerShell o la [API de REST de administración de Azure](https://msdn.microsoft.com/library/azure/dn722420.aspx) para reservar una dirección IP de una región determinada. Esta dirección IP reservada está asociada a su suscripción. No se puede reservar una dirección IP mediante el Portal de administración.
1. ¿Puedo usarla con redes virtuales basadas en grupos de afinidad?
  - Las IP reservadas solo se admiten en redes virtuales regionales. No se admiten para redes virtuales asociadas a grupos de afinidad. Para obtener más información acerca de cómo asociar una red virtual a una región o un grupo de afinidad, consulte [Redes virtuales regionales y grupos de afinidad](virtual-networks-migrate-to-regional-vnet.md).

## Administración de VIP reservadas

Para poder usar IP reservadas, debe agregarlo a su suscripción. Para crear una IP reservada del grupo de direcciones IP públicas disponibles en la ubicación *Centro de EE. UU.* , ejecute el siguiente comando de PowerShell:

	New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

Sin embargo, tenga en cuenta que no puede especificar qué IP se va a reservar. Para ver qué direcciones IP están reservadas en su suscripción, ejecute el siguiente comando de PowerShell y observe los valores de *ReservedIPName* y *Dirección*:

	Get-AzureReservedIP

	ReservedIPName       : MyReservedIP
	Address              : 23.101.114.211
	Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
	Label                :
	Location             : Central US
	State                : Created
	InUse                : False
	ServiceName          :
	DeploymentName       :
	OperationDescription : Get-AzureReservedIP
	OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
	OperationStatus      : Succeeded

Una vez reservada una IP, permanece asociada a su suscripción hasta que la elimine. Para eliminar la IP reservada que se muestra arriba, ejecute el siguiente comando de PowerShell:

	Remove-AzureReservedIP -ReservedIPName "MyReservedIP"

## Cómo reservar la dirección IP de un servicio en la nube existente

Puede reservar la dirección IP de un servicio en la nube existente mediante la adición del parámetro *-ServiceName*. Para reservar la dirección IP de un servicio en la nube *TestService* en la ubicación *Centro de EE. UU.*, ejecute el siguiente comando de PowerShell:

	New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService


## Asociación de una IP reservada a un nuevo servicio en la nube
El script siguiente crea una nueva dirección IP reservada y, a continuación, la asocia a un nuevo servicio en la nube denominado *TestService*.

	New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"

>[AZURE.NOTE] Cuando se crea una IP reservada para usarla con un servicio en la nube, todavía será necesario hacer referencia a la máquina virtual mediante *VIP:&lt;port number>* para las comunicaciones entrantes. Reservar una dirección IP no significa que pueda conectarse directamente a la máquina virtual. La IP reservada se asigna al servicio en la nube en el que se ha implementado la máquina virtual. Si desea conectarse directamente a una máquina virtual mediante la dirección IP, tendrá que configurar una dirección IP pública a nivel de instancia. Una dirección IP pública a nivel de instancia es un tipo de dirección IP pública (denominada ILPIP) que se asigna directamente a la máquina virtual. No se puede reservar. Consulte [Dirección IP pública a nivel de instancia (ILPIP)](../virtual-networks-instance-level-public-ip) para obtener más información.

## Eliminación de una IP reservada de una implementación en ejecución
Para quitar la IP reservada agregada al nuevo servicio creado en el script anterior, ejecute el siguiente comando de PowerShell:

	Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService

>[AZURE.NOTE] Quitar una IP reservada de una implementación en ejecución, no se elimina la reserva de la suscripción. Simplemente libera la dirección IP que será usada por otro recurso de la suscripción.

## Asociación de una IP reservada a una implementación en ejecución
El script siguiente crea un nuevo servicio en la nube denominado *TestService2* con una nueva máquina virtual denominada *TestVM2* y, a continuación, asocia la IP reservada existente denominada *MyReservedIP* al servicio en la nube.

	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| New-AzureVM -ServiceName TestService2 -Location "Central US"
	Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2

## Asociación de una IP reservada a un servicio en la nube mediante un archivo de configuración de servicio
También puede asociar una IP reservada a un servicio en la nube mediante un archivo de configuración de servicio (CSCFG). El xml de ejemplo siguiente muestra cómo configurar un servicio en la nube para que use una VIP reservada denominada *MyReservedIP*:

	<?xml version="1.0" encoding="utf-8"?>
	<ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
	  <Role name="WebRole1">
	    <Instances count="1" />
	    <ConfigurationSettings>
	      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
	    </ConfigurationSettings>
	  </Role>
	  <NetworkConfiguration>
	    <AddressAssignments>
	      <ReservedIPs>
	       <ReservedIP name="MyReservedIP"/>
	      </ReservedIPs>
	    </AddressAssignments>
	  </NetworkConfiguration>
	</ServiceConfiguration>

## Pasos siguientes

- Comprenda cómo funciona el [direccionamiento IP](virtual-network-ip-addresses-overview-classic.md) en el modelo de implementación clásico.

- Obtenga más información acerca de las [direcciones IP privadas reservadas](../virtual-networks-reserved-private-ip).

- Obtenga más información acerca de las [direcciones IP públicas de nivel de instancia (ILPIP)](../virtual-networks-instance-level-public-ip).

<!---HONumber=AcomDC_0302_2016-->