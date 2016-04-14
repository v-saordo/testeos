## Red virtual
Los recursos de redes virtuales (VNET) y de subredes le ayudarán a definir un límite de seguridad para las cargas de trabajo que se ejecuten en Azure. Una red virtual se caracteriza por una colección de espacios de direcciones, también denominados bloques CIDR.

>[AZURE.NOTE]Los administradores de red ya están familiarizados con la notación CIDR. Si no está familiarizado con el formato CIDR, [puede obtener más información](http://whatismyipaddress.com/cidr).

![Redes virtuales con varias subredes](./media/resource-groups-networking/Figure4.png)

Las Redes virtuales contienen las siguientes propiedades:

|Propiedad|Descripción|Valores de ejemplo|
|---|---|---|
|**addressSpace**|Es la colección de prefijos de direcciones que constituyen la red virtual en la notación CIDR|192\.168.0.0/16|
|**subredes**|Es la colección de subredes que constituyen la red virtual|Consulte la información acerca de las [subredes](#Subnets) que tiene a continuación.|
|**ipAddress**|Dirección IP asignada al objeto. Se trata de una propiedad de solo lectura.|104\.42.233.77|

### Subredes
Una subred es un recurso secundario de una red virtual que le ayudará a definir segmentos de espacios de direcciones dentro de un bloque CIDR mediante prefijos de direcciones IP. Las NIC pueden agregarse a subredes y conectarse a máquinas virtuales, lo cual le proporciona conectividad a varias cargas de trabajo.

Las subredes contienen las siguientes propiedades:

|Propiedad|Descripción|Valores de ejemplo|
|---|---|---|
|**addressPrefix**|Es el prefijo de dirección única que constituye la subred en la notación de CIDR|192\.168.1.0/24|
|**networkSecurityGroup**|Es el grupo de seguridad de red aplicado a la subred|Consulte [Grupos de seguridad de red](#Network-Security-Group)|
|**routeTable**|Es la tabla de ruta que se aplica a la subred|Consulte [Enrutamiento definido por el usuario](#Route-table)|
|**ipConfigurations**|Es la colección de objetos de configuración IP que usan las NIC conectadas a la subred|Consulte [Enrutamiento definido por el usuario](#Route-table)|


Red virtual de ejemplo en formato JSON:

	{
	    "name": "TestVNet",
	    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
	    "etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	    "type": "Microsoft.Network/virtualNetworks",
	    "location": "westus",
	    "tags": {
	        "displayName": "VNet"
	    },
	    "properties": {
	        "provisioningState": "Succeeded",
	        "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	        "addressSpace": {
	            "addressPrefixes": [
	                "192.168.0.0/16"
	            ]
	        },
	        "subnets": [
	            {
	                "name": "FrontEnd",
	                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
	                "etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	                "properties": {
	                    "provisioningState": "Succeeded",
	                    "addressPrefix": "192.168.1.0/24",
	                    "networkSecurityGroup": {
	                        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
	                    },
	                    "routeTable": {
	                        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
	                    },
	                    "ipConfigurations": [
	                        {
	                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
	                        },
	                        ...]
	                }
	            },
	            ...]
	    }
	}

### Recursos adicionales

- Obtenga más información acerca de las [Redes virtuales](virtual-networks-overview.md).
- Lea la [documentación de referencia de la API de REST](https://msdn.microsoft.com/library/azure/mt163650.aspx) para obtener información sobre las Redes virtuales.
- Lea la [documentación de referencia de la API de REST](https://msdn.microsoft.com/library/azure/mt163618.aspx) para obtener información sobre las subredes.

<!---HONumber=Oct15_HO3-->