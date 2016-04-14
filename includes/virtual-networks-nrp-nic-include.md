## NIC
 
Un recurso de tarjeta de interfaz de red (NIC) proporciona conectividad de red a una subred existente en un recurso de red virtual. Aunque puede crear un NIC como un objeto independiente, debe asociarlo a otro objeto para, realmente, proporcionar conectividad. Una NIC se puede usar para conectar una VM a una subred, a una dirección IP pública o a un equilibrador de carga.

|Propiedad|Descripción|Valores de ejemplo|
|---|---|---|
|**virtualMachine**|VM a la que se asocia la NIC.|/subscriptions/{guid}/../Microsoft.Compute/virtualMachines/vm1|
|**macAddress**|Dirección MAC de la NIC|cualquier valor entre 4 y 30|
|**networkSecurityGroup**|Grupo de seguridad de red asociado a la NIC|/subscriptions/{guid}/../Microsoft.Network/networkSecurityGroups/myNSG1|
|**dnsSettings**|Configuración de DNS para la NIC|Consulte [PIP](#Public-IP-address).|

La tarjeta de interfaz de red, o NIC, representa una interfaz de red que se puede asociar a una máquina virtual (VM). Una máquina virtual puede tener una o varias NIC.

![NIC en una sola máquina virtual](./media/resource-groups-networking/Figure3.png)

### Configuraciones IP
Las NIC tienen un objeto secundario llamado **ipConfigurations** que contiene las propiedades siguientes:

|Propiedad|Descripción|Valores de ejemplo|
|---|---|---|
|**subnet**|Subred a la que está conectada la NIC|/subscriptions/{guid}/../Microsoft.Network/virtualNetworks/myvnet1/subnets/mysub1|
|**privateIPAddress**|Dirección IP de la NIC en la subred|10\.0.0.8|
|**privateIPAllocationMethod**|Método de asignación de direcciones IP|Dynamic o Static|
|**enableIPForwarding**|Si la NIC se puede usar para el enrutamiento|true o false|
|**primary**|Si la NIC es la NIC principal para la VM|true o false|
|**publicIPAddress**|PIP asociado a la NIC.|vea [Configuración de DNS](#DNS-settings)|
|**loadBalancerBackendAddressPools**|Grupos de direcciones de back-end a los que se asocia la NIC||
|**loadBalancerInboundNatRules**|Reglas NAT del equilibrador de carga de entrada a las que está asociada la NIC||

Dirección IP pública de ejemplo en formato JSON:

	{
	    "name": "lb-nic1-be",
	    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be",
	    "etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	    "type": "Microsoft.Network/networkInterfaces",
	    "location": "eastus",
	    "properties": {
	        "provisioningState": "Succeeded",
	        "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	        "ipConfigurations": [
	            {
	                "name": "NIC-config",
	                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/NIC-config",
	                "etag": "W/"0027f1a2-3ac8-49de-b5d5-fd46550500b1"",
	                "properties": {
	                    "provisioningState": "Succeeded",
	                    "privateIPAddress": "10.0.0.4",
	                    "privateIPAllocationMethod": "Dynamic",
	                    "subnet": {
	                        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet"
	                    },
	                    "loadBalancerBackendAddressPools": [
	                        {
	                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool"
	                        }
	                    ],
	                    "loadBalancerInboundNatRules": [
	                        {
	                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1"
	                        }
	                    ]
	                }
	            }
	        ],
	        "dnsSettings": { ... },
	        "macAddress": "00-0D-3A-10-F1-29",
	        "enableIPForwarding": false,
	        "primary": true,
	        "virtualMachine": {
	            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Compute/virtualMachines/web1"
	        }
	    }
	}

### Recursos adicionales

- Lea la [documentación de referencia de la API de REST](https://msdn.microsoft.com/library/azure/mt163579.aspx) para obtener información sobre las NIC.

<!---HONumber=Oct15_HO3-->