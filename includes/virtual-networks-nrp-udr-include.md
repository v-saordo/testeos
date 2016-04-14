## Tablas de ruta
Los recursos de una tabla de ruta contienen rutas que se usan para definir la manera en la cual fluye el tráfico dentro de la infraestructura de Azure. Puede usar rutas definidas por el usuario (UDR) para enviar todo el tráfico de una subred determinada a un dispositivo virtual, como por ejemplo, un firewall o un sistema de detección de intrusos (IDS). Asimismo, puede asociar una tabla de ruta a las subredes.

Las tablas de ruta contienen las siguientes propiedades:

|Propiedad|Descripción|Valores de ejemplo|
|---|---|---|
|**rutas**|Son las rutas definidas por el usuario recopiladas en la tabla de ruta|Consulte las [rutas definidas por el usuario](#User-defined-routes)|
|**subredes**|Es la colección de subredes aplicadas a la tabla de ruta|consulte [subredes](#Subnets)|


### Rutas definidas por el usuario
Puede crear Rutas definidas por el usuario para especificar dónde se debería enviar el tráfico, según su dirección de destino. Una ruta se puede considerar como la definición de una puerta de enlace predeterminada, en función de la dirección de destino de un paquete de red.

Las Rutas definidas por el usuario contienen las siguientes propiedades:

|Propiedad|Descripción|Valores de ejemplo|
|---|---|---|
|**addressPrefix**|Prefijo de dirección o dirección IP completa del destino|192\.168.1.0/24, 192.168.1.101|
|**nextHopType**|Tipo de dispositivo al cual se enviará el tráfico|VirtualAppliance, puerta de enlace VPN, Internet|
|**nextHopIpAddress**|Dirección IP del próximo salto.|192\.168.1.4|


Tabla de ruta de ejemplo en formato JSON:

	{
	    "name": "UDR-BackEnd",
	    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd",
	    "etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	    "type": "Microsoft.Network/routeTables",
	    "location": "westus",
	    "properties": {
	        "provisioningState": "Succeeded",
	        "routes": [
	            {
	                "name": "RouteToFrontEnd",
	                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd/routes/RouteToFrontEnd",
	                "etag": "W/"v"",
	                "properties": {
	                    "provisioningState": "Succeeded",
	                    "addressPrefix": "192.168.1.0/24",
	                    "nextHopType": "VirtualAppliance",
	                    "nextHopIpAddress": "192.168.0.4"
	                }
	            }
	        ],
	        "subnets": [
	            {
	                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd"
	            }
	        ]
	    }
	}

### Recursos adicionales

- Obtenga más información acerca de las [Rutas definidas por el usuario](virtual-networks-udr-overview.md).
- Lea la [documentación de referencia de la API de REST](https://msdn.microsoft.com/library/azure/mt502549.aspx) para obtener información sobre las tablas de ruta.
- Lea la [documentación de la API de REST](https://msdn.microsoft.com/library/azure/mt502539.aspx) de las rutas definidas por el usuario (UDR).

<!---HONumber=Oct15_HO3-->