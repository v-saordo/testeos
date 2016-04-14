### Para comprobar la conexión mediante el uso del Portal de Azure

Para comprobar una conexión VPN en el Portal de Azure, vaya a **Puertas de enlace de red virtual** -> ***haga clic en el nombre de su puerta de enlace*** -> **Configuración** -> **Conexiones**. Para ver más información en la hoja **Conexión**, seleccione el nombre de la conexión.

### Para comprobar la conexión mediante el uso de PowerShell

También es posible comprobar que la conexión se realizó correctamente; para ello, utilice *Get-AzureRmVirtualNetworkGatewayConnection –Debug*. En el futuro, tendremos un cmdlet para esto. Puedes usar el siguiente ejemplo de cmdlet, configurando los valores para que coincidan con los tuyos. Cuando se le pida, seleccione *A* para poder ejecutar Todo.

	Get-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

 Cuando el cmdlet haya finalizado, desplázate para ver los valores. En el ejemplo siguiente, el estado de conexión aparece como *Connected* y pueden verse los bytes de entrada y salida.

	Body:
	{
	  "name": "localtovon",
	  "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/connections/loca
	ltovon",
	  "properties": {
	    "provisioningState": "Succeeded",
	    "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
	    "virtualNetworkGateway1": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworkGa
	teways/vnetgw1"
	    },
	    "localNetworkGateway2": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/localNetworkGate
	ways/LocalSite"
	    },
	    "connectionType": "IPsec",
	    "routingWeight": 10,
	    "sharedKey": "abc123",
	    "connectionStatus": "Connected",
	    "ingressBytesTransferred": 33509044,
	    "egressBytesTransferred": 4142431
	  }

<!---HONumber=AcomDC_0107_2016-->