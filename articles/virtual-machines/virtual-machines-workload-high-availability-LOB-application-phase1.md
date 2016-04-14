<properties 
	pageTitle="Fase 1 de la aplicación de línea de negocio | Microsoft Azure" 
	description="Cree la red virtual y otros elementos de la infraestructura de Azure en la fase 1 de la aplicación de línea de negocio en Azure." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/21/2016" 
	ms.author="josephd"/>

# Fase 1 de la carga de trabajo de aplicación de línea de negocio: Configuración de Azure
 
[AZURE.INCLUDE [learn-about-deployment-models-rm-include](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.
 
En esta fase de la implementación de una aplicación de línea de negocio de alta disponibilidad solo de intranet en los servicios de infraestructura de Azure, creará la infraestructura de red y almacenamiento de Azure. Debe completar esta fase antes de pasar a la [fase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md). Vea [Implementación de una aplicación de línea de negocio de alta disponibilidad en Azure](virtual-machines-workload-high-availability-LOB-application-overview.md) en todas las fases.

Azure debe contar con los siguientes componentes de red básicos:

- Una red virtual entre locales con una subred para hospedar las máquinas virtuales de Azure
- Dos cuentas de almacenamiento de Azure para almacenar imágenes de disco de VHD y discos de datos adicionales
- Tres conjuntos de disponibilidad

## Antes de empezar

Antes de empezar a configurar los componentes de Azure, rellene las tablas siguientes. Para que le resulte más fácil seguir los procedimientos para configurar Azure, imprima esta sección y anote la información necesaria, o copie esta sección en un documento y rellene los campos.

Para la configuración de la red virtual (VNet), rellene la tabla V.

Elemento | Elemento configuración | Descripción | Valor 
--- | --- | --- | --- 
1\. | Nombre de red virtual | Nombre que se va a asignar a la red virtual de Azure (por ejemplo, SPFarmNet). | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | Ubicación de la red virtual | Centro de datos Azure que contendrá la red virtual. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | Nombre de la red local | Nombre que se va a asignar a la red de su organización. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
4\. | Dirección IP del dispositivo VPN | Dirección IPv4 pública de la interfaz del dispositivo VPN en Internet. Trabaje con su departamento de TI para determinar esta dirección. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
5\. | Espacio de direcciones de red virtual | Espacio de direcciones (definido en un prefijo de dirección privada único) para la red virtual. Trabaje con su departamento de TI para determinar este espacio de direcciones. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
6\. | Primer servidor DNS para la red virtual | Cuarta dirección IP posible para el espacio de direcciones de la segunda subred de la red virtual (vea la tabla S). Trabaje con su departamento de TI para determinar esta dirección. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
7\. | Segundo servidor DNS para la red virtual | Quinta dirección IP posible para el espacio de direcciones de la subred de la red virtual (vea la tabla S). Trabaje con su departamento de TI para determinar esta dirección. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
8\. | Clave compartida de IPsec | Cadena alfanumérica aleatoria de 32 caracteres que se usará para autenticar ambos lados de la conexión VPN de sitio a sitio. Trabaje con el departamento de seguridad o de TI para determinar el valor de esta clave. Como alternativa, vea [Creación de una cadena aleatoria para una clave previamente compartida IPsec](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx).| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabla V: Configuración de red virtual entre locales**

Rellene la tabla S para la subred de esta solución.

- Para la primera subred, determine un espacio de direcciones de 29 bits (con una longitud de prefijo /29) para la subred de puerta de enlace de Azure.
- Para la segunda subred, especifique un nombre descriptivo, un espacio de direcciones IP único basado en el espacio de direcciones de la red virtual y una finalidad descriptiva. 

Trabaje con el departamento de TI para determinar estos espacios de direcciones a partir del espacio de direcciones de la red virtual. Ambos espacios de direcciones deben estar en formato de enrutamiento de interdominios sin clases (CIDR), también conocido como formato de prefijo de red. Un ejemplo es 10.24.64.0/20.

Elemento | Nombre de subred | Espacio de direcciones de subred | Propósito 
--- | --- | --- | --- 
1\. | Subred de puerta de enlace | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | Subred que usan las máquinas virtuales de puerta de enlace de Azure.
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabla S: Subredes de la red virtual**

> [AZURE.NOTE] Esta arquitectura predefinida usa una sola subred por motivos de simplicidad. Si desea superponer un conjunto de filtros de tráfico para emular el aislamiento de subred, puede usar los [grupos de seguridad de red](virtual-networks-nsg.md) de Azure.

Para los dos servidores DNS locales que desea usar al configurar inicialmente los controladores de dominio de la red virtual, rellene tabla D. Asigne a cada servidor DNS un nombre descriptivo y una dirección IP única. No es necesario que este nombre descriptivo coincida con el nombre de host o el nombre de equipo del servidor DNS. Tenga en cuenta que se muestran dos entradas en blanco, pero puede agregar más. Trabaje con su departamento de TI para determinar esta lista.

Elemento | Dirección IP del servidor DNS 
--- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 

**Tabla D: Servidores DNS locales**

Para enrutar paquetes de la red virtual de Azure a la red de su organización a través de la conexión VPN de sitio a sitio, debe configurar la red virtual con una red local que contenga una lista con los espacios de direcciones (en notación CIDR) de todas las ubicaciones accesibles en la red local de su organización. La lista de espacios de direcciones que definen la red local debe ser única y no debe solaparse con el espacio de direcciones usado para otras redes virtuales u otras redes locales.

Para el conjunto de espacios de direcciones de la red local, rellene la tabla L. Observe que aparecen tres entradas en blanco, pero normalmente se necesitan más. Trabaje con su departamento de TI para determinar esta lista de espacios de direcciones.

Elemento | Espacio de direcciones de la red local 
--- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabla L: Prefijos de dirección de la red local**

En primer lugar, inicie un símbolo del sistema de Azure PowerShell.

> [AZURE.NOTE] El siguiente comando establece el uso de Azure PowerShell 1.0 y versiones posteriores. Para más información, vea [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

En primer lugar, inicie un símbolo del sistema de Azure PowerShell e inicie sesión en su cuenta.

	Login-AzureRMAccount

Obtenga el nombre de la suscripción con el comando siguiente.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Establezca su suscripción a Azure. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < and >, por los nombres correctos.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Cree un grupo de recursos para la aplicación de línea de negocio. Para determinar el nombre único del grupo de recursos, utilice este comando para enumerar los grupos de recursos existente.

	Get-AzureRMResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Cree un nuevo grupo de recursos con estos comandos.

	$rgName="<resource group name>"
	$locName="<an Azure location, such as West US>"
	New-AzureRMResourceGroup -Name $rgName -Location $locName

Las máquinas virtuales basadas en Administrador de recursos requieren una cuenta de almacenamiento basada en Administrador de recursos.

Elemento | Nombre de la cuenta de almacenamiento | Propósito 
--- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | Cuenta de almacenamiento Premium que usan las máquinas virtuales de SQL Server.
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | Cuenta de almacenamiento Estándar que usan las demás máquinas virtuales de la carga de trabajo. 

**Tabla ST: cuentas de almacenamiento**

Necesitará estos nombres al crear las máquinas virtuales en las fases 2, 3 y 4.

Debe seleccionar un nombre único global para cada cuenta de almacenamiento que contenga solo letras minúsculas y números. Puede usar este comando para enumerar las cuentas de almacenamiento existentes.

	Get-AzureRMStorageAccount | Sort StorageAccountName | Select StorageAccountName

Para crear la primera cuenta de almacenamiento, ejecute estos comandos.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<Table ST – Item 1 - Storage account name column>"
	New-AzureRMStorageAccount -Name $saName -ResourceGroupName $rgName –Type Premium_LRS -Location $locName

Para crear la segunda cuenta de almacenamiento, ejecute estos comandos.

	$saName="<Table ST – Item 2 - Storage account name column>"
	New-AzureRMStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

Cree la red virtual de Azure que hospedará la aplicación de línea de negocio.

	$rgName="<name of your new resource group>"
	$locName="<Azure location of the new resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$vnetAddrPrefix="<Table V – Item 5 – Value column>"
	$lobSubnetName="<Table S – Item 2 – Subnet name column>"
	$lobSubnetPrefix="<Table S – Item 2 – Subnet address space column>"
	$gwSubnetPrefix="<Table S – Item 1 – Subnet address space column>"
	$dnsServers=@( "<Table D – Item 1 – DNS server IP address column>", "<Table D – Item 2 – DNS server IP address column>" )
	$gwSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $gwSubnetPrefix
	$lobSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name $lobSubnetName -AddressPrefix $lobSubnetPrefix
	New-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix $vnetAddrPrefix -Subnet $gwSubnet,$lobSubnet -DNSServer $dnsServers

Use estos comandos para crear las puertas de enlace para la conexión VPN de sitio a sitio.

	$vnetName="<Table V – Item 1 – Value column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	
	# Attach a virtual network gateway to a public IP address and the gateway subnet
	$publicGatewayVipName="LOBAppPublicIPAddress"
	$vnetGatewayIpConfigName="LOBAppPublicIPConfig"
	New-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$publicGatewayVip=Get-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName
	$vnetGatewayIpConfig=New-AzureRMVirtualNetworkGatewayIpConfig -Name $vnetGatewayIpConfigName -PublicIpAddressId $publicGatewayVip.Id -SubnetId $vnet.Subnets[0].Id

	# Create the Azure gateway
	$vnetGatewayName="LOBAppAzureGateway"
	$vnetGateway=New-AzureRMVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $rgName -Location $locName -GatewayType Vpn -VpnType RouteBased -IpConfigurations $vnetGatewayIpConfig
	
	# Create the gateway for the local network
	$localGatewayName="LOBAppLocalNetGateway"
	$localGatewayIP="<Table V – Item 4 – Value column>"
	$localNetworkPrefix=@( <comma-separated, double-quote enclosed list of the local network address prefixes from Table L, example: "10.1.0.0/24", "10.2.0.0/24"> )
	$localGateway=New-AzureRMLocalNetworkGateway -Name $localGatewayName -ResourceGroupName $rgName -Location $locName -GatewayIpAddress $localGatewayIP -AddressPrefix $localNetworkPrefix
	
	# Define the Azure virtual network VPN connection
	$vnetConnectionName="LOBAppS2SConnection"
	$vnetConnectionKey="<Table V – Item 8 – Value column>"
	$vnetConnection=New-AzureRMVirtualNetworkGatewayConnection -Name $vnetConnectionName -ResourceGroupName $rgName -Location $locName -ConnectionType IPsec -SharedKey $vnetConnectionKey -VirtualNetworkGateway1 $vnetGateway -LocalNetworkGateway2 $localGateway

Configure el dispositivo VPN local para conectarse a la puerta de enlace de VPN de Azure. Para más información, vea [Configuración del dispositivo VPN](../virtual-networks/vpn-gateway-configure-vpn-gateway-mp.md#configure-your-vpn-device).

Para configurar el dispositivo VPN local, necesitará lo siguiente:

- La dirección IPv4 pública de la puerta de enlace de VPN para la red virtual en la presentación del comando **Get-AzureRMPublicIpAddress -Name $publicGatewayVipName -ResourceGroupName $rgName**.
- Clave precompartida de IPsec para la conexión VPN de sitio a sitio (Tabla V– Elemento 8 – Columna Valor).

A continuación, asegúrese de que el espacio de direcciones de la red virtual sea accesible desde la red local. Esto se hace normalmente agregando una ruta correspondiente al espacio de direcciones de la red virtual a su dispositivo VPN y, a continuación, anunciando esa ruta al resto de la infraestructura de enrutamiento de la red de su organización. Trabaje con su departamento de TI para determinar cómo hacerlo.

Defina los nombres de tres conjuntos de disponibilidad. Rellene la tabla A.

Elemento | Propósito | Nombre del conjunto de disponibilidad 
--- | --- | --- 
1\. | Controladores de dominio | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | Servidores SQL Server | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | Servidores web | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabla A: Nombre del conjunto de disponibilidad**

Necesitará estos nombres al crear las máquinas virtuales en las fases 2, 3 y 4.

Cree estos nuevos conjuntos de disponibilidad con estos comandos de Azure PowerShell.

	$rgName="<your new resource group name>"
	$locName="<the Azure location for your new resource group>"
	$avName="<Table A – Item 1 – Availability set name column>"
	New-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 2 – Availability set name column>"
	New-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 3 – Availability set name column>"
	New-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

Esta es la configuración resultante de la realización correcta de esta fase.

![](./media/virtual-machines-workload-high-availability-LOB-application-phase1/workload-lobapp-phase1.png)

## Paso siguiente

- Para continuar con la configuración de esta carga de trabajo, vaya a la [Fase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md).

<!---HONumber=AcomDC_0128_2016-->