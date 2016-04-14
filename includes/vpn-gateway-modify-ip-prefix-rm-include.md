Si tienes que cambiar los prefijos de sitio local, usa las instrucciones siguientes. Se proporcionan dos conjuntos de instrucciones y dependen de si ya creaste o no la conexión VPN de puerta de enlace.

### Agregar o quitar los prefijos sin una conexión de Puerta de enlace de VPN


- **Para agregar** prefijos de dirección adicionales a un sitio local que creó, pero que todavía no dispone de una conexión de puerta de enlace de VPN, use el ejemplo siguiente.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- **Para quitar** un prefijo de dirección de un sitio local que no dispone de una conexión VPN, use el ejemplo siguiente. Omita los prefijos que ya no necesite. En este ejemplo, ya no necesitamos el prefijo 20.0.0.0/24 (del ejemplo anterior), por lo que se actualizará el sitio local y se excluirá ese prefijo.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### Agregar o quitar los prefijos con una conexión de Puerta de enlace de VPN

Si creaste la conexión VPN y quieres agregar o quitar los prefijos de dirección IP contenidos en el sitio local, tendrás que realizar los pasos siguientes en orden. Esto provocará un tiempo de inactividad en la conexión de VPN, ya que tendrás que eliminar y volver a crear la puerta de enlace. Sin embargo, como solicitaste una dirección IP para la conexión, no tendrás que volver a configurar el enrutador VPN local a menos que decidas cambiar los valores que usaste anteriormente.

 
1. Quitar la conexión de puerta de enlace. 
2. Modificar los prefijos de tu sitio local. 
3. Crear una nueva conexión de puerta de enlace. 

Puedes usar el ejemplo siguiente como guía.


	$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

	Remove-AzureRmVirtualNetworkGatewayConnection -Name vnetgw1 -ResourceGroupName testrg

	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
	Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
	
	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

<!---HONumber=AcomDC_0107_2016-->