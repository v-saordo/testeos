<properties 
   pageTitle="Configuración de una conexión de puerta de enlace de VPN punto a sitio a una Red virtual de Azure | Microsoft Azure"
   description="Conéctese de forma segura a la Red virtual de Azure mediante la creación de una conexión VPN de punto a sitio. Esta configuración es útil cuando se necesita una conexión entre locales desde una ubicación remota sin usar un dispositivo VPN y se puede utilizar con configuraciones de red híbrida. En este artículo, se incluyen instrucciones de PowerShell para redes virtuales que se crearon con el modelo de implementación del Administrador de recursos."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/19/2016"
   ms.author="cherylmc" />

# Configuración de una conexión punto a sitio a una red virtual mediante PowerShell

> [AZURE.SELECTOR]
- [PowerShell - Resource Manager](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Portal - Classic](vpn-gateway-point-to-site-create.md)

Una configuración punto a sitio permite crear una conexión segura a la red virtual desde un equipo cliente, de forma individual. Se establece una conexión VPN al iniciar la conexión desde el equipo cliente. La conexión punto a sitio es una solución excelente cuando desea conectarse a la red virtual desde una ubicación remota, como desde una casa o una conferencia, o si solo tiene unos pocos clientes que necesitan conectarse a una red virtual. Las conexiones punto a sitio no requieren un dispositivo VPN o una dirección IP pública para que funcionen. Para obtener más información acerca de las conexiones punto a sitio, consulte [Preguntas frecuentes sobre la puerta de enlace de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) y [Acerca de las conexiones entre locales](vpn-gateway-cross-premises-options.md).

Este artículo se aplica a las redes virtuales y a las puertas de enlace de VPN creadas con el modelo de implementación del **Administrador de recursos de Azure**. Si desea configurar una conexión punto a sitio para una red virtual que se creó con Administración de servicios (también conocido como el modelo de implementación clásica), consulte [Configuración de una conexión VPN de punto a sitio a una red virtual](vpn-gateway-point-to-site-create.md).

[AZURE.INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Acerca de esta configuración

En este escenario, creará una red virtual con una conexión punto a sitio. Una conexión punto a sitio consta de los siguientes elementos: una red virtual con una puerta de enlace, un archivo .cer de certificado raíz cargado, un certificado de cliente y la configuración de VPN en el lado cliente.

Utilizaremos los siguientes valores para esta configuración:

- Nombre: **TestVNet**, con los espacios de direcciones **192.168.0.0/16** y **10.254.0.0/16**. Tenga en cuenta que puede utilizar más de un espacio de direcciones para una red virtual.
- Nombre de subred: **FrontEnd**, con **192.168.1.0/24**
- Nombre de subred: **BackEnd**, con **10.254.1.0/24**
- Nombre de subred: **GatewaySubnet**, con **192.168.200.0/24**. El nombre de subred *GatewaySubnet* es obligatorio para que funcione la puerta de enlace. 
- Grupo de direcciones de clientes de VPN: **172.16.201.0/24**. Los clientes de VPN que se conectan a la red virtual con esta conexión punto a sitio recibirán una dirección IP de este grupo.
- Suscripción: si tiene más de una suscripción, compruebe que tiene la correcta.
- Grupo de recursos: **TestRG**
- Ubicación: **Este de EE. UU.**
- Servidor DNS: **Dirección IP** del servidor DNS que desea usar para la resolución de nombres.
- Nombre de GW: **GW**
- Nombre de dirección IP pública: **GWIP**
- VpnType: **RouteBased**


## Antes de comenzar

- Compruebe que tiene una suscripción a Azure. Si todavía no tiene una suscripción de Azure, puede activar sus [beneficios de suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o bien registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
	
- Necesitará instalar los cmdlets de PowerShell del Administrador de recursos de Azure (1.0.2 o posterior). Consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para obtener más información sobre cómo instalar los cmdlets de PowerShell.

## Configurar una conexión punto a sitio para Azure

1. En la consola de PowerShell, inicie sesión en su cuenta de Azure. El cmdlet le pedirá las credenciales de inicio de sesión para la cuenta de Azure. Después de iniciar la sesión, se descarga la configuración de la cuenta a fin de ponerla a disposición para Azure PowerShell.

		Login-AzureRmAccount 

2. Obtenga una lista de las suscripciones de Azure.

		Get-AzureRmSubscription

3. Especifique la suscripción que desea usar.

		Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. En esta configuración, las siguientes variables de PowerShell se declaran con los valores que desea utilizar. Los valores declarados se utilizan en los scripts de ejemplo. Declare los valores que desea utilizar. Use el ejemplo siguiente y sustituya los valores que aparecen con sus valores cuando sea necesario.

		$VNetName  = "TestVNet"
		$FESubName = "FrontEnd"
		$BESubName = "Backend"
		$GWSubName = "GatewaySubnet"
		$VNetPrefix1 = "192.168.0.0/16"
		$VNetPrefix2 = "10.254.0.0/16"
		$FESubPrefix = "192.168.1.0/24"
		$BESubPrefix = "10.254.1.0/24"
		$GWSubPrefix = "192.168.200.0/26"
		$VPNClientAddressPool = "172.16.201.0/24"
		$RG = "TestRG"
		$Location = "East US"
		$DNS = "8.8.8.8"
		$GWName = "GW"
		$GWIPName = "GWIP"
		$GWIPconfName = "gwipconf"
    	$P2SRootCertName = "ARMP2SRootCert.cer"

5. Cree un nuevo grupo de recursos.

		New-AzureRmResourceGroup -Name $RG -Location $Location

6. Cree las configuraciones de subred para la red virtual con los nombres *FrontEnd*, *BackEnd* y *GatewaySubnet*. Tenga en cuenta que estos prefijos deben ser parte del espacio de direcciones de la red virtual declarados anteriormente.

		$fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
		$besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
		$gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix

7. Creación de la red virtual. Tenga en cuenta que el servidor DNS especificado debe ser un servidor DNS que pueda resolver los nombres de los recursos a los que se conecta. En este ejemplo, usamos una dirección IP pública, pero aquí querrá ingresar sus propios valores.

		New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS

8. Especifique las variables de la red virtual que acaba de crear.

		$vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

9. Solicite una dirección IP pública asignada de forma dinámica. Esta dirección IP es necesaria para que la puerta de enlace funciona correctamente. Más adelante, conectará la puerta de enlace con la configuración de dirección IP de puerta de enlace.
		
		$pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
		$ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
		
10. Cargue un archivo .cer de certificado raíz a Azure. Puede utilizar un certificado raíz desde el entorno de certificados empresarial o puede usar un certificado raíz autofirmado. Puede cargar hasta 20 certificados raíz. Para obtener instrucciones para crear un certificado raíz autofirmado mediante *makecert*, consulte [Trabajar con certificados raíz autofirmados para las configuraciones de punto a sitio](vpn-gateway-certificates-point-to-site.md). Tenga en cuenta que el archivo .cer no puede contener la clave privada del certificado raíz.
	
	A continuación, podemos ver un ejemplo de cómo se ve. La parte difícil de cargar los datos del certificado público es que debe copiar y pegar toda la cadena, sin espacios. De lo contrario, la carga no funcionará. En este paso, deberá utilizar su propio archivo .cer de certificado. No intente copiar y pegar el ejemplo que aparece a continuación.

		$MyP2SRootCertPubKeyBase64 = "MIIDUzCCAj+gAwIBAgIQRggGmrpGj4pCblTanQRNUjAJBgUrDgMCHQUAMDQxEjAQBgNVBAoTCU1pY3Jvc29mdDEeMBwGA1UEAxMVQnJrIExpdGUgVGVzdCBSb290IENBMB4XDTEzMDExOTAwMjQxOFoXDTIxMDExOTAwMjQxN1owNDESMBAGA1UEChMJTWljcm9zb2Z0MR4wHAYDVQQDExVCcmsgTGl0ZSBUZXN0IFJvb3QgQ0EwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQC7SmE+iPULK0Rs7mQBO/6a6B6/G9BaMxHgDGzAmSG0Qsyt5e08aqgFnPdkMl3zRJw3lPKGha/JCvHRNrO8UpeAfc4IXWaqxx2iBipHjwmHPHh7+VB8lU0EJcUe7WBAI2n/sgfCwc+xKtuyRVlOhT6qw/nAi8e5don/iHPU6q7GCcnqoqtceQ/pJ8m66cvAnxwJlBFOTninhb2VjtvOfMQ07zPP+ZuYDPxvX5v3nd6yDa98yW4dZPuiGO2s6zJAfOPT2BrtyvLekItnSgAw3U5C0bOb+8XVKaDZQXbGEtOw6NZvD4L2yLd47nGkN2QXloiPLGyetrj3Z2pZYcrZBo8hAgMBAAGjaTBnMGUGA1UdAQReMFyAEOncRAPNcvJDoe4WP/gH2U+hNjA0MRIwEAYDVQQKEwlNaWNyb3NvZnQxHjAcBgNVBAMTFUJyayBMaXRlIFRlc3QgUm9vdCBDQYIQRggGmrpGj4pCblTanQRNUjAJBgUrDgMCHQUAA4IBAQCGyHhMdygS0g2tEUtRT4KFM+qqUY5HBpbIXNAav1a1dmXpHQCziuuxxzu3iq4XwnWUF1OabdDE2cpxNDOWxSsIxfEBf9ifaoz/O1ToJ0K757q2Rm2NWqQ7bNN8ArhvkNWa95S9gk9ZHZLUcjqanf0F8taJCYgzcbUSp+VBe9DcN89sJpYvfiBiAsMVqGPc/fHJgTScK+8QYrTRMubtFmXHbzBSO/KTAP5rBTxse88EGjK5F8wcedvge2Ksk6XjL3sZ19+Oj8KTQ72wihN900p1WQldHrrnbixSpmHBXbHr9U0NQigrJp5NphfuU5j81C8ixvfUdwyLmTv7rNA7GTAD"
		$p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $MyP2SRootCertPubKeyBase64

11. Cree la puerta de enlace de red virtual para su red virtual. GatewayType debe ser Vpn y VpnType debe ser RouteBased.

		New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Standard -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## Configuración de cliente

Cada cliente que se conecta a Azure con la conexión punto a sitio debe cumplir con lo siguiente: El cliente de VPN debe estar configurado para conectarse y el cliente debe tener instalado un certificado de cliente. Los paquetes de configuración de cliente de VPN están disponibles para los clientes de Windows. Consulte las [Preguntas más frecuentes sobre la puerta de enlace de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) para obtener más información.

1. Descargue el paquete de configuración de cliente de VPN. En este paso, utilice el siguiente ejemplo para descargar el paquete de configuración de cliente.

		Get-AzureRmVpnClientPackage -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

	El cmdlet de PowerShell devolverá un vínculo de dirección URL. Copie y pegue este vínculo en un explorador web para descargar el paquete a su equipo. A continuación, aparece un ejemplo del aspecto que tendrá la dirección URL en cuestión.

    	"https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
	
2. Genere e instale los certificados de cliente (*.pfx) que se creen a partir del certificado raíz en los equipos cliente. Puede utilizar cualquier método de instalación que le resulte cómodo. Si está usando un certificado raíz autofirmado y no está familiarizado con cómo hacerlo, puede consultar [Trabajar con certificados raíz autofirmados para las configuraciones de punto a sitio](vpn-gateway-certificates-point-to-site.md).

3. Para conectarse a su red virtual, en el equipo cliente, navegue a las conexiones VPN y ubique la conexión VPN que acaba de crear. Tendrá el mismo nombre que su red virtual. Haga clic en **Conectar**. Es posible que aparezca un mensaje emergente que haga referencia al uso del certificado. Si esto ocurre, haga clic en **Continuar** para usar privilegios elevados.

4. En la página de estado **Conexión**, haga clic en **Conectar** para iniciar la conexión. Si ve una pantalla para **Seleccionar certificado**, compruebe que el certificado de cliente que se muestra es el que desea utilizar para conectarse. Si no es así, use la flecha de la lista desplegable para seleccionar el certificado correcto y, a continuación, haga clic en **Aceptar**.

5. La conexión debería establecerse. Para comprobar la conexión, utilice el siguiente procedimiento.

## Comprobar la conexión

1. Para comprobar que la conexión VPN está activa, abra un símbolo del sistema con privilegios elevados y ejecute *ipconfig/all*.

2. Vea los resultados. Observe que la dirección IP que recibió es una de las direcciones dentro del grupo de direcciones de cliente de VPN punto a sitio que especificó en la configuración. Los resultados deben ser algo parecido a esto:
    
		PPP adapter VNetEast:
			Connection-specific DNS Suffix .:
			Description.....................: VNetEast
			Physical Address................:
			DHCP Enabled....................: No
			Autoconfiguration Enabled.......: Yes
			IPv4 Address....................: 172.16.201.3(Preferred)
			Subnet Mask.....................: 255.255.255.255
			Default Gateway.................:
			NetBIOS over Tcpip..............: Enabled

## Agregar o quitar un certificado raíz

Los certificados se usan para autenticar a los clientes VPN para VPN de punto a sitio. Los siguientes pasos le enseñarán a agregar y quitar certificados raíz.

### Agregar un certificado raíz

Puede agregar hasta 20 certificados raíz a Azure. Siga los siguientes pasos para agregar un certificado raíz.

1. Crear y preparar el nuevo certificado raíz para su carga.

		$P2SRootCertName2 = "ARMP2SRootCert2.cer"
		$MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

2. Cargar el nuevo certificado raíz. Tenga en cuenta que sólo puede agregar un certificado raíz a la vez.

		Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2

3. Puede comprobar que el nuevo certificado se agregó correctamente mediante el siguiente cmdlet.

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName

### Quitar un certificado raíz

Puede quitar un certificado raíz de Azure. Al quitar un certificado raíz, los certificados de cliente que se generaron a partir del certificado raíz no podrán conectarse a Azure a través de punto a sitio hasta que instalen un certificado de cliente que se genere a partir de un certificado raíz que sea válido en Azure.

1. Quitar un certificado raíz.

		Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

 
2. Use el siguiente cmdlet para comprobar que el certificado se quitó correctamente.

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName

## Administrar la lista de certificados de cliente revocados

Puede revocar certificados de cliente. La lista de revocación de certificados permite denegar de forma selectiva la conectividad de punto a sitio basada en certificados de cliente individuales. Mientras que al quitar un certificado raíz de Azure, se revocará el acceso para todos los certificados de cliente generados y firmados por el certificado raíz revocado, al revocar un certificado de cliente podrá quitar el acceso a un certificado determinado. Lo más habitual es usar el certificado raíz para administrar el acceso a nivel de equipo u organización, mientras que los certificados de cliente revocados se usan para un control de acceso específico para usuarios individuales.

### Revocar un certificado de cliente

1. Obtener la huella digital del certificado de cliente que se va a revocar.

		$RevokedClientCert1 = "ClientCert1"
		$RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"

2. Agregar la huella digital a la lista de huellas digitales revocadas.

		Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

3. Compruebe que la huella digital se agregó a la lista de revocación de certificados. Debe agregar una huella digital cada vez.

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### Restablecer un certificado de cliente

Puede restablecer un certificado de cliente quitando la huella digital de la lista de certificados de cliente revocados.

1.  Quite la huella digital de la lista de huellas digitales de certificados de cliente revocados.

		Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

2. Compruebe si la huella digital se quita de la lista revocada.

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## Pasos siguientes

Puede agregar una máquina virtual a la red virtual. Consulte [Creación de una máquina virtual que ejecuta Windows en el Portal de Azure](../virtual-machines/virtual-machines-windows-tutorial.md) para ver los pasos.

<!---HONumber=AcomDC_0218_2016-->