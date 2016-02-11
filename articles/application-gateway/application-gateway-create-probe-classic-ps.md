<properties
   pageTitle="Crear un sondeo personalizado para la puerta de enlace de aplicaciones mediante PowerShell en el modelo de implementación clásica | Microsoft Azure"
   description="Aprenda a crear un sondeo personalizado para la puerta de enlace de aplicaciones mediante PowerShell en el modelo de implementación clásica."
   services="application-gateway"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/17/2015"
   ms.author="joaoma" />

# Creación de un sondeo personalizado para la Puerta de enlace de aplicaciones de Azure (clásica) mediante PowerShell


[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)].

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](application-gateway-create-probe-ps.md).


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


## Creación de una nueva puerta de enlace de aplicaciones

Para crear una puerta de enlace de aplicaciones:

1. Cree un recurso de la puerta de enlace de aplicaciones.
2. Cree un archivo de configuración XML o un objeto de configuración.
3. Confirme la configuración para el recurso de la puerta de enlace de aplicaciones recién creado.

### Crear un recurso de la puerta de enlace de aplicaciones

Para crear la puerta de enlace, use el cmdlet **New-AzureApplicationGateway**, reemplazando los valores por los suyos propios. Tenga en cuenta que la facturación de la puerta de enlace no se inicia en este momento. La facturación comienza en un paso posterior, cuando la puerta de enlace se ha iniciado correctamente.

En el ejemplo siguiente se crea una puerta de enlace de aplicaciones nueva mediante una red virtual denominada "testvnet1" y una subred denominada "subnet-1":


	PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 *Description*, *InstanceCount* y *GatewaySize* son parámetros opcionales.


Para validar que se creó la puerta de enlace, puede usar el cmdlet **Get-AzureApplicationGateway**.


	PS C:\> Get-AzureApplicationGateway AppGwTest
	Name          : AppGwTest
	Description   :
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Stopped
	VirtualIPs    : {}
	DnsName       :

>[AZURE.NOTE]El valor predeterminado de *InstanceCount* es 2, con un valor máximo de 10. El valor predeterminado de *GatewaySize* es Medium. Puede elegir entre Pequeño, Mediano y Grande.


 *VirtualIPs* y *DnsName* se muestran en blanco porque todavía no se ha iniciado la puerta de enlace. Se crearán una vez que la puerta de enlace esté en estado de ejecución.

## Configuración de una puerta de enlace de aplicaciones

La puerta de enlace de aplicaciones se puede configurar con un archivo XML o con un objeto de configuración.

## Configuración de una puerta de enlace de aplicaciones mediante XML

En el ejemplo siguiente, se usa un archivo XML para configurar todos los valores de la puerta de enlace de aplicaciones y confirmarlos en el recurso de dicha puerta de enlace.

### Paso 1  

Copie el texto siguiente y péguelo en el Bloc de notas.


	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations>
        <FrontendIPConfiguration>
            <Name>fip1</Name>
            <Type>Private</Type>
        </FrontendIPConfiguration>
    </FrontendIPConfigurations>    
	<FrontendPorts>
        <FrontendPort>
            <Name>port1</Name>
            <Port>80</Port>
        </FrontendPort>
    </FrontendPorts>
    <Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>pool1</Name>
            <IPAddresses>
                <IPAddress>1.1.1.1</IPAddress>
				<IPAddress>2.2.2.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>listener1</Name>
            <FrontendIP>fip1</FrontendIP>
	    <FrontendPort>port1</FrontendPort>
            <Protocol>Http</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>lbrule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>setting1</BackendHttpSettings>
            <Listener>listener1</Listener>
            <BackendAddressPool>pool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>


Edite los valores entre paréntesis de los elementos de configuración. Guarde el archivo con extensión .xml.

En el ejemplo siguiente se muestra cómo usar un archivo de configuración para configurar la puerta de enlace de aplicaciones para que equilibre la carga de tráfico HTTP en el puerto público 80 y envíe el tráfico de red al puerto back-end 80 entre dos direcciones IP mediante sondeo personalizado.

>[AZURE.IMPORTANT]El elemento de protocolo Http o Https distingue mayúsculas de minúsculas.


Se agrega un nuevo elemento de configuración <Probe> para configurar sondeos personalizados.

Los parámetros de configuración son:

- **Nombre**: nombre de referencia del sondeo personalizado.
- **Protocolo**: protocolo usado (los valores posibles son HTTP o HTTPS).
- **Host** y **Ruta**: dirección URL completa que invoca la puerta de enlace de aplicaciones para determinar el estado de la instancia. Por ejemplo: tiene el sitio web http://contoso.com/, así que el sondeo personalizado se puede configurar para "http://contoso.com/path/custompath.htm" de forma que las comprobaciones del sondeo tengan una respuesta HTTP correcta.
- **Intervalo**: configura las comprobaciones de intervalo de sondeo en segundos.
- **Tiempo de espera**: define el tiempo de espera de sondeo para una comprobación de respuesta HTTP.
- **UnhealthyThreshold**: el número de respuestas HTTP con error que es necesario para marcar la instancia de back-end como *incorrecto*.

Se hace referencia al nombre del sondeo en la configuración de <BackendHttpSettings> para asignar el grupo de back-end que usará la configuración de sondeo personalizado.

## Agregar una configuración de sondeo personalizado a una puerta de enlace de aplicaciones existente

El cambio de la configuración actual de una puerta de enlace de aplicaciones requiere tres pasos: obtener el archivo de configuración XML actual, modificarlo para que tenga un sondeo personalizado y configurar la puerta de enlace de aplicaciones con la nueva configuración XML.

### Paso 1

Obtenga el archivo XML mediante get-AzureApplicationGatewayConfig. De esta forma se exportará el XML de configuración que se debe modificar para agregar una configuración de sondeo.

	get-AzureApplicationGatewayConfig -Name <application gateway name> -Exporttofile "<path to file>"


### Paso 2

Abra el archivo XML en un editor de texto. Agregue una sección `<probe>` después de `<frontendport>`.

	<Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>

En la sección backendHttpSettings del XML, agregue el nombre del sondeo tal como se muestra en el ejemplo siguiente:

        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>

Guarde el archivo XML.


### Paso 3

Actualice la configuración de la puerta de enlace de aplicaciones con el nuevo archivo XML usando **AzureApplicationGatewayConfig Set**. De esta forma se actualizará la puerta de enlace de aplicaciones con la nueva configuración.

	set-AzureApplicationGatewayConfig -Name <application gateway name> -Configfile "<path to file>"


## Pasos siguientes

Si quiere configurar la descarga de Capa de sockets seguros (SSL), vea [Configure an application gateway for SSL offload](application-gateway-ssl.md) (Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante el modelo de implementación clásica).

Si quiere configurar una puerta de enlace de aplicaciones para usarla con el equilibrador de carga interno, vea [Creación de una puerta de enlace de aplicaciones con un equilibrador de carga interno (ILB)](application-gateway-ilb.md).

<!---HONumber=AcomDC_0114_2016-->