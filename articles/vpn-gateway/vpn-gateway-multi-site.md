<properties 
   pageTitle="Conexión de varios sitios locales a una red virtual con una puerta de enlace de VPN"
   description="Este artículo le guiará por la conexión de varios sitios locales a una red virtual con una puerta de enlace de VPN para el modelo de implementación clásica."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/17/2015"
   ms.author="cherylmc" />

# Conexión de varios sitios locales a una red virtual

Este artículo se aplica a la conexión de varios sitios locales a una red virtual creada con el modelo de implementación clásica (conocido también como Administración de servicios).

**Información sobre los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]Cuando tenemos un artículo con pasos para redes virtuales creadas mediante el modelo del Administrador de recursos, se realizará la vinculación desde esta página.

## Acerca de la conexión

Puede conectar varios sitios locales a una única red virtual. Esto resulta especialmente atractivo para crear soluciones híbridas en la nube. La creación de una conexión de varios sitios en la puerta de enlace de red virtual de Azure es muy similar a la creación de otras conexiones de sitio a sitio. De hecho, puede utilizar una puerta de enlace de VPN de Azure existente, siempre que la puerta de enlace sea dinámica (basada en ruta).

Si ya se ha conectado una puerta de enlace estática a la red virtual, puede cambiar el tipo de puerta de enlace a dinámico sin tener que recompilar la red virtual para dar cabida a varios sitios. Antes de cambiar el tipo de enrutamiento, asegúrese de que la puerta de enlace de VPN local admite configuraciones de VPN basadas en ruta.

![VPN de varios sitios](./media/vpn-gateway-multi-site/IC727363.png)

## Puntos que se deben tener en cuenta

**No podrá usar el Portal de Azure clásico para realizar cambios en esta red virtual.** Para esta versión, tendrá que realizar cambios en el archivo de configuración de red en lugar de usar el Portal de Azure clásico. Si realiza cambios en el Portal de Azure clásico, sobrescribirán la configuración de referencia a varios sitios para esta red virtual. Debe sentirse bastante cómodo al usar el archivo de configuración de red en el momento en el que complete el procedimiento de varios sitios. Sin embargo, si hay más personas que trabajan en la configuración de red, tendrá que asegurarse de que todos conocen esta limitación. Esto no significa que no pueda usar el Portal de Azure clásico. Puede usarlo para todo lo demás menos para hacer cambios de configuración en esta red virtual concreta.

## Antes de empezar

Antes de comenzar la configuración, compruebe que dispone de lo siguiente:

- Una suscripción de Azure. Si todavía no tiene una suscripción de Azure, puede activar sus [beneficios de suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o bien registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Hardware VPN compatible para cada ubicación local. Consulte [Acerca de los dispositivos VPN para conectividad de red virtual](http://go.microsoft.com/fwlink/p/?linkid=615099) para comprobar si el dispositivo que quiere usar es un dispositivo que se sabe que es compatible.

- Una dirección IP IPv4 pública orientada externamente para cada dispositivo VPN. La dirección IP no se puede ubicar detrás de un NAT. Esto es un requisito.

- La versión más reciente de los cmdlets de Azure PowerShell Puede descargar e instalar la versión más reciente desde la sección Windows PowerShell de la página [Descargas](https://azure.microsoft.com/downloads/).

- Alguna persona con experiencia en configuración de hardware de VPN No podrá usar scripts de VPN generados automáticamente desde el Portal de Azure clásico para configurar los dispositivos de VPN. Esto significa que tendrá un conocimiento amplio de cómo configurar el dispositivo VPN o trabajar con alguien que lo tenga.

- Los intervalos de dirección IP que desea usar para la red virtual (si aún no ha creado uno).

- Los intervalos de direcciones IP para cada uno de los sitios de red locales a los que se va a conectar. Tendrá que asegurarse de que los intervalos de dirección IP para cada uno de los sitios de red locales a los que desea conectarse no se solapan. De lo contrario, el Portal de Azure clásico o la API de REST rechazarán la configuración que se carga. Por ejemplo, si dispone de dos sitios de red locales que contienen el intervalo de dirección IP 10.2.3.0/24 y dispone de un paquete con una dirección de destino 10.2.3.3, Azure no sabrá a qué sitio desea enviar el paquete porque se solapan los intervalos de dirección. Para evitar problemas de enrutamiento, Azure no loe permite cargar un archivo de configuración que disponga de intervalos que se solapan.

## Creación de la red virtual y la puerta de enlace

1. **Cree una VPN de sitio a sitio con una puerta de enlace de enrutamiento dinámico (basado en ruta).** Si ya tiene una, estupendo. Puede pasar a [Exportación de la configuración de la red virtual](#export). De lo contrario, haga lo siguiente:

	**Si ya tiene una red virtual de sitio a sitio, pero tiene una puerta de enlace de enrutamiento estático (basada en directivas):** Paso 1: Cambie el tipo de puerta de enlace a enrutamiento dinámico. Una VPN de varios sitios requiere una puerta de enlace de enrutamiento dinámico. Para cambiar el tipo de puerta de enlace, tendrá que eliminar primero la puerta de enlace existente y, a continuación, crear una nueva. Para obtener instrucciones, vea [Cambio de un tipo de enrutamiento de puerta de enlace de VPN](vpn-gateway-configure-vpn-gateway-mp.md/#how-to-change-your-vpn-gateway-type). Paso 2: Configure la nueva puerta de enlace y cree un túnel de VPN. Para obtener instrucciones, consulte [Configuración de una puerta de enlace de VPN en el Portal de Azure clásico](vpn-gateway-configure-vpn-gateway-mp.md). En primer lugar, cambie el tipo de puerta de enlace a enrutamiento dinámico.

	**Si no dispone de una red virtual de sitio a sitio:** Paso 1: Cree una red virtual de sitio a sitio con estas instrucciones: [Creación de una red virtual con una conexión de VPN de sitio a sitio en el Portal de Azure clásico](vpn-gateway-site-to-site-create.md). Paso 2: Configure una puerta de enlace de enrutamiento dinámico con estas instrucciones: [Configuración de una puerta de enlace de VPN](vpn-gateway-configure-vpn-gateway-mp.md). Asegúrese de seleccionar **enrutamiento dinámico** como tipo de puerta de enlace.

2. **<a name="export"></a>Exportación de la configuración de la red virtual.** Para exportar el archivo de configuración de red, vea [Para exportar la configuración de red](../virtual-network/virtual-networks-using-network-configuration-file.md). El archivo que exporte se usará para configurar los ajustes de varios sitios.

3. **Abra el archivo de configuración de red.** Abra el archivo de configuración de red que descargó en el último paso. Use el editor xml que desee. El archivo debe tener un aspecto similar al siguiente:

		<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
		  <VirtualNetworkConfiguration>
		    <LocalNetworkSites>
		      <LocalNetworkSite name="Site1">
		        <AddressSpace>
		          <AddressPrefix>10.0.0.0/16</AddressPrefix>
		          <AddressPrefix>10.1.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
		      </LocalNetworkSite>
		      <LocalNetworkSite name="Site2">
		        <AddressSpace>
		          <AddressPrefix>10.2.0.0/16</AddressPrefix>
		          <AddressPrefix>10.3.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
		      </LocalNetworkSite>
		    </LocalNetworkSites>
		    <VirtualNetworkSites>
		      <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
		        <AddressSpace>
		          <AddressPrefix>10.20.0.0/16</AddressPrefix>
		          <AddressPrefix>10.21.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <Subnets>
		          <Subnet name="FE">
		            <AddressPrefix>10.20.0.0/24</AddressPrefix>
		          </Subnet>
		          <Subnet name="BE">
		            <AddressPrefix>10.20.1.0/24</AddressPrefix>
		          </Subnet>
		          <Subnet name="GatewaySubnet">
		            <AddressPrefix>10.20.2.0/29</AddressPrefix>
		          </Subnet>
		        </Subnets>
		        <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="Site1">
		              <Connection type="IPsec" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
		      </VirtualNetworkSite>
		    </VirtualNetworkSites>
		  </VirtualNetworkConfiguration>
		</NetworkConfiguration>

4. **Agregue varias referencias a sitios al archivo de configuración de red**. Cuando agregue o quite la información de referencia del sitio, realizará cambios de configuración en ConnectionsToLocalNetwork/LocalNetworkSiteRef. Si se agrega una nueva referencia a sitio local se activa Azure para la creación de un nuevo túnel. En el ejemplo siguiente, la configuración de red es para una conexión de sitio único.

		<Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

	Para agregar referencias a sitios adicionales (creación de una configuración de varios sitios), simplemente agregue líneas adicionales "LocalNetworkSiteRef", tal como se muestra en el ejemplo siguiente:

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

5. **Guarde el archivo de configuración de red e impórtelo.** Para importar el archivo de configuración de red, vea [Para importar la configuración de red](../virtual-network/../virtual-network/virtual-networks-using-network-configuration-file.md#export-and-import-virtual-network-settings-using-the-management-portal). Cuando importe este archivo con los cambios, se agregarán nuevos túneles. Los túneles usarán la puerta de enlace dinámica que creó anteriormente.

6. **Descargue las claves compartidas previamente para los túneles de VPN.** Una vez que se han agregado los nuevos túneles, use el cmdlet de PowerShell Get-AzureVNetGatewayKey para obtener las claves compartidas previamente IPsec/IKE para cada túnel.

	Por ejemplo:

		Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"

		Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"

	Si lo prefiere, también puede usar la API de REST de *Obtención de la clave compartida de puerta de enlace de la red virtual* para obtener claves compartidas previamente.

## Comprobación de las conexiones

**Compruebe el estado del túnel de varios sitios.** Después de descargar las claves para cada túnel, querrá comprobar las conexiones. Use *Get-AzureVnetConnection* para obtener una lista de túneles de redes virtuales, tal como se muestra en el siguiente ejemplo. VNet1 es el nombre de la red virtual.

		Get-AzureVnetConnection -VNetName VNET1
		
		ConnectivityState         : Connected
		EgressBytesTransferred    : 661530
		IngressBytesTransferred   : 519207
		LastConnectionEstablished : 5/2/2014 2:51:40 PM
		LastEventID               : 23401
		LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
		LastEventTimeStamp        : 5/2/2014 2:51:40 PM
		LocalNetworkSiteName      : Site1
		OperationDescription      : Get-AzureVNetConnection
		OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
		OperationStatus           : Succeeded
		
		ConnectivityState         : Connected
		EgressBytesTransferred    : 789398
		IngressBytesTransferred   : 143908
		LastConnectionEstablished : 5/2/2014 3:20:40 PM
		LastEventID               : 23401
		LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
		LastEventTimeStamp        : 5/2/2014 2:51:40 PM
		LocalNetworkSiteName      : Site2
		OperationDescription      : Get-AzureVNetConnection
		OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
		OperationStatus           : Succeeded

## Pasos siguientes

Para más información sobre las puertas de enlace de VPN, consulte [Acerca de las puertas de enlace de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

<!---HONumber=AcomDC_0128_2016-->