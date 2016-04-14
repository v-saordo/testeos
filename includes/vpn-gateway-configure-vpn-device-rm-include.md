
Si desea configurar el dispositivo VPN, necesitará la dirección IP pública de la puerta de enlace de red virtual para configurar el dispositivo VPN local. Trabaje con el fabricante del dispositivo para obtener información de configuración específica y configure el dispositivo. Consulte los [dispositivos VPN](vpn-gateway-about-vpn-devices.md) para obtener más información sobre los dispositivos VPN que funcionan bien con Azure.

Para buscar la dirección IP pública de la puerta de enlace de red virtual con PowerShell, utilice el ejemplo siguiente:

	Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG

También puede utilizar el Portal de Azure para ver la dirección IP pública de la puerta de enlace de red virtual. Vaya a **Puertas de enlace de red virtual** y, luego, haga clic en el nombre de su puerta de enlace.

<!---HONumber=AcomDC_0107_2016-->