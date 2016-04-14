## Grupo de seguridad de red (NSG)
Un recurso de grupo de seguridad de red permite la creación de un límite de seguridad para las cargas de trabajo mediante la implementación de reglas de permiso y denegación. Estas reglas pueden aplicarse a una máquina virtual, una NIC o una subred.

|Propiedad|Descripción|Valores de ejemplo|
|---|---|---|
|**subredes**|Lista de identificadores de subred a los cuales se aplica el grupo de seguridad de red.|/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd|
|**securityRules**|Lista de reglas de seguridad que constituyen el Grupo de seguridad de red|Consulte la [regla de seguridad](#Security-rule) que tiene a continuación|
|**defaultSecurityRules**|Lista de reglas de seguridad predeterminadas presentes en cada Grupo de seguridad de red|Consulte las [reglas de seguridad predeterminadas](#Default-security-rules) que tiene a continuación|

- **Regla de seguridad**: un grupo de seguridad de red puede contener varias reglas de seguridad definidas. Cada regla puede permitir o denegar distintos tipos de tráfico.

### Regla de seguridad
Una regla de seguridad es un recurso secundario de un Grupo de seguridad de red que contiene las siguientes propiedades.

|Propiedad|Descripción|Valores de ejemplo|
|---|---|---|
|**descripción**|Descripción de la regla|Permite el tráfico entrante para todas las máquinas virtuales en la subred X|
|**protocolo**|Protocolo que debe coincidir con la regla|TCP, UDP o *|
|**sourcePortRange**|Intervalo del puerto de origen que debe coincidir con la regla|80, 100-200, *|
|**destinationPortRange**|Intervalo del puerto de destino que debe coincidir con la regla|80, 100-200, *|
|**sourceAddressPrefix**|Prefijo de la dirección de origen que debe coincidir con la regla|10\.10.10.1, 10.10.10.0/24, VirtualNetwork|
|**destinationAddressPrefix**|Prefijo de la dirección de destino que debe coincidir con la regla|10\.10.10.1, 10.10.10.0/24, VirtualNetwork|
|**dirección**|Dirección del tráfico que debe coincidir con la regla|entrada o salida|
|**prioridad**|Prioridad de la regla. Las reglas se comprueban según su orden de prioridad; una vez se aplica una regla, no se prueban más reglas para realizar la coincidencia.|10, 100, 65000|
|**acceso**|Tipo de acceso que se debe aplicar si coincide con la regla|permitir o denegar|

Grupo de seguridad de red de ejemplo en formato JSON:

	{
	    "name": "NSG-BackEnd",
	    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd",
	    "etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	    "type": "Microsoft.Network/networkSecurityGroups",
	    "location": "westus",
	    "tags": {
	        "displayName": "NSG - Front End"
	    },
	    "properties": {
	        "provisioningState": "Succeeded",
	        "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	        "securityRules": [
	            {
	                "name": "rdp-rule",
	                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/rdp-rule",
	                "etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	                "properties": {
	                    "provisioningState": "Succeeded",
	                    "description": "Allow RDP",
	                    "protocol": "Tcp",
	                    "sourcePortRange": "*",
	                    "destinationPortRange": "3389",
	                    "sourceAddressPrefix": "Internet",
	                    "destinationAddressPrefix": "*",
	                    "access": "Allow",
	                    "priority": 100,
	                    "direction": "Inbound"
	                }
	            }
	        ],
	        "defaultSecurityRules": [
	            { [...],
	        "subnets": [
	            {
	                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
	            }
	        ]
	    }
	}

### Reglas de seguridad predeterminadas
Las reglas de seguridad predeterminadas tienen las mismas propiedades disponibles en las reglas de seguridad. Existen para proporcionar una conexión básica entre aquellos recursos que tengan los Grupos de seguridad de red aplicados. Asegúrese de saber qué [reglas de seguridad predeterminadas](./virtual-networks-nsg.md#Default-Rules) existen.

### Recursos adicionales

- Obtenga más información acerca de los [Grupos de seguridad de red](virtual-networks-nsg.md).
- Lea la [documentación de referencia de la API de REST](https://msdn.microsoft.com/library/azure/mt163615.aspx) para obtener información sobre los Grupos de seguridad de red.
- Lea la [documentación de referencia de la API de REST](https://msdn.microsoft.com/library/azure/mt163580.aspx) para obtener información sobre las reglas de seguridad.

<!---HONumber=Oct15_HO3-->
