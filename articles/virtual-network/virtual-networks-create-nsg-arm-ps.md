<properties 
   pageTitle="Cómo crear NSG en Azure Resource Manager mediante PowerShell | Microsoft Azure"
   description="Aprenda a crear e implementar NSG en Azure Resource Manager mediante PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="telmos" />

# Cómo crear NSG en el Administrador de recursos mediante PowerShell

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artículo trata sobre el modelo de implementación del Administrador de recursos. También puede [crear grupos de seguridad de red en el modelo de implementación clásica](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

En los siguientes comandos de PowerShell de ejemplo se presupone que ya se ha creado un entorno simple según el escenario anterior. Si desea ejecutar los comandos tal y como aparecen en este documento, cree primero el entorno de prueba mediante la implementación de [esta plantilla](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd), haga clic en **Implementar en Azure**, reemplace los valores de parámetro predeterminados si es necesario y siga las instrucciones del portal.

## Creación del grupo de seguridad de red para la subred front-end
Para crear un grupo de seguridad de red denominado *NSG-FrontEnd* según el escenario anterior, siga estos pasos.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Si es la primera vez que usa Azure PowerShell, vea [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md) y siga las instrucciones hasta el final para iniciar sesión en Azure y seleccionar su suscripción.

3. Cree una regla de seguridad que permita el acceso desde Internet al puerto 3389.

		$rule1 = New-AzureRmNetworkSecurityRuleConfig -Name rdp-rule -Description "Allow RDP" `
		    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
		    -SourceAddressPrefix Internet -SourcePortRange * `
		    -DestinationAddressPrefix * -DestinationPortRange 3389

4. Cree una regla de seguridad que permita el acceso desde Internet al puerto 80.

		$rule2 = New-AzureRmNetworkSecurityRuleConfig -Name web-rule -Description "Allow HTTP" `
		    -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
		    -SourceAddressPrefix Internet -SourcePortRange * `
		    -DestinationAddressPrefix * -DestinationPortRange 80

5. Agregue las reglas creadas anteriormente a un nuevo grupo de seguridad de red denominado **NSG-FrontEnd**.

		$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName TestRG -Location westus -Name "NSG-FrontEnd" `
			-SecurityRules $rule1,$rule2

6. Compruebe las reglas creadas en el grupo de seguridad de red.

		$nsg

	Resultado que solo muestra las reglas de seguridad:

		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule",
		                           "Description": "Allow RDP",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "3389",
		                           "SourceAddressPrefix": "Internet",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 100,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         },
		                         {
		                           "Name": "web-rule",
		                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule",
		                           "Description": "Allow HTTP",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "80",
		                           "SourceAddressPrefix": "Internet",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 101,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]

6. Asocie el grupo de seguridad de red creado anteriormente a la subred *FrontEnd*.

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
		Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
			-AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $nsg

	El resultado solo muestra la configuración de subred *FrontEnd*; observe el valor de la propiedad **NetworkSecurityGroup**:

		Subnets           : [
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [
		                          {
		                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
		                          },
		                          {
		                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
		                          }
		                        ],
		                        "NetworkSecurityGroup": {
		                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                        },
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      }

>[AZURE.WARNING] La salida del comando anterior muestra el contenido del objeto de configuración de red virtual, que solo existe en el equipo donde se ejecuta PowerShell. Debe ejecutar el cmdlet `Set-AzureRmVirtualNetwork` para guardar esta configuración en Azure.

7. Guardar la nueva configuración de red virtual en Azure.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

	Resultado que solo muestra una parte del grupo de seguridad de red:

		"NetworkSecurityGroup": {
		  "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		}

## Creación del grupo de seguridad de red para la subred back-end
Para crear un grupo de seguridad de red denominado *NSG-BackEnd* según el escenario anterior, siga estos pasos.

1. Crear una regla de seguridad que permita el acceso desde la subred front-end al puerto 1433 (puerto predeterminado utilizado por SQL Server).

		$rule1 = New-AzureRmNetworkSecurityRuleConfig -Name frontend-rule -Description "Allow FE subnet" `
		    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
		    -SourceAddressPrefix 192.168.1.0/24 -SourcePortRange * `
		    -DestinationAddressPrefix * -DestinationPortRange 1433

4. Crear una regla de seguridad que bloquee el acceso a Internet.

		$rule2 = New-AzureRmNetworkSecurityRuleConfig -Name web-rule -Description "Block Internet" `
		    -Access Deny -Protocol * -Direction Outbound -Priority 200 `
		    -SourceAddressPrefix * -SourcePortRange * `
		    -DestinationAddressPrefix Internet -DestinationPortRange *

5. Agregue las reglas creadas anteriormente a un nuevo grupo de seguridad de red denominado **NSG-BackEnd**.

		$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName TestRG -Location westus `-Name "NSG-BackEnd" `
			-SecurityRules $rule1,$rule2

6. Asocie el grupo de seguridad de red creado anteriormente a la subred *Back-end*.

		Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name BackEnd `
			-AddressPrefix 192.168.2.0/24 -NetworkSecurityGroup $nsg

	El resultado solo muestra la configuración de la subred *BackEnd*, por lo que deberá fijarse en el valor de la propiedad **NetworkSecurityGroup**:

		Subnets           : [
                      {
                        "Name": "BackEnd",
                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                        "AddressPrefix": "192.168.2.0/24",
                        "IpConfigurations": [...],
                        "NetworkSecurityGroup": {
                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
                        },
                        "RouteTable": null,
                        "ProvisioningState": "Succeeded"
                      }

7. Guardar la nueva configuración de red virtual en Azure.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

<!---HONumber=AcomDC_0224_2016-->