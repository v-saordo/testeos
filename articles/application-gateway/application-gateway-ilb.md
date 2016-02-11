<properties 
   pageTitle="Creación y configuración de una puerta de enlace de aplicaciones con el equilibrador de carga interno (ILB) en una red virtual | Microsoft Azure"
   description="Esta página proporciona instrucciones para configurar una puerta de enlace de aplicaciones de Azure con un extremo de equilibrio de carga interno"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="01/21/2016"
   ms.author="joaoma"/>

# Creación de una puerta de enlace de aplicaciones con un equilibrador de carga interno (ILB)

> [AZURE.SELECTOR]
- [Azure classic steps](application-gateway-ilb.md)
- [Resource Manager Powershell steps](application-gateway-ilb-arm.md)


La Puerta de enlace de aplicaciones se puede configurar con una IP virtual accesible desde Internet o con un punto de conexión interno no expuesto a Internet, lo que se conoce también como punto de conexión de Equilibrador de carga interno (ILB). Configurar la puerta de enlace con un ILB es útil para la línea interna de aplicaciones empresariales que no se exponen en Internet. También es útil para los servicios o niveles dentro de una aplicación de varios niveles que se asientan en un límite de seguridad no expuesto a Internet, pero siguen necesitando distribución de carga round robin, permanencia de sesión o terminación SSL. Este artículo le guiará por los pasos necesarios para configurar una puerta de enlace de la aplicaciones con un ILB.

## Antes de empezar

1. Instale la versión más reciente de los cmdlets de Azure PowerShell mediante el Instalador de plataforma web. Puede descargar e instalar la versión más reciente desde la sección **Windows PowerShell** de la [Página de descarga](https://azure.microsoft.com/downloads/).
2. Compruebe que tiene una red virtual que funciona con una subred válida.
3. Compruebe que dispone de servidores backend, ya sea en la red virtual o con una dirección IP virtual o dirección IP pública asignada.


Para crear una nueva puerta de enlace de aplicaciones, realice los pasos siguientes en el orden mostrado.

1. [Creación de una nueva puerta de enlace de aplicaciones](#create-a-new-application-gateway)
2. [Configuración de la puerta de enlace](#configure-the-gateway)
3. [Establecimiento de la configuración de la puerta de enlace](#set-the-gateway-configuration)
4. [Inicio de la puerta de enlace](#start-the-gateway)
4. [Comprobación de la puerta de enlace](#verify-the-gateway-status)



## Creación de una nueva puerta de enlace de aplicaciones:

**Para crear la puerta de enlace**, use el cmdlet `New-AzureApplicationGateway` y reemplace los valores por los suyos propios. Tenga en cuenta que la facturación de la puerta de enlace no se inicia en este momento. La facturación comienza en un paso posterior, cuando la puerta de enlace se ha iniciado correctamente.

	PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399

**Para validar** que la puerta de enlace se creó, puede usar el cmdlet `Get-AzureApplicationGateway`.

En el ejemplo, *Description*, *InstanceCount* y *GatewaySize* son parámetros opcionales. El valor predeterminado de *InstanceCount* es 2, con un valor máximo de 10. El valor predeterminado de *GatewaySize* es Medium. Small y Large son otros valores disponibles. *Vip* y *DnsName* se muestran en blanco porque todavía no se ha iniciado la puerta de enlace. Se crearán una vez que la puerta de enlace esté en estado de ejecución.

	PS C:\> Get-AzureApplicationGateway AppGwTest

	VERBOSE: 4:39:39 PM - Begin Operation:
	Get-AzureApplicationGateway VERBOSE: 4:39:40 PM - Completed 
	Operation: Get-AzureApplicationGateway
	Name: AppGwTest	
	Description: 
	VnetName: testvnet1 
	Subnets: {Subnet-1} 
	InstanceCount: 2 
	GatewaySize: Medium 
	State: Stopped 
	VirtualIPs: 
	DnsName:


## Configuración de la puerta de enlace

Una configuración de puerta de enlace de aplicaciones consta de varios valores. Los valores pueden ir juntos para construir la configuración.
 
Los valores son:

- **Grupo de servidores de backend:** lista de direcciones IP de los servidores backend. Las direcciones IP mostradas deben pertenecer a la subred de red virtual o deben ser una dirección IP virtual o dirección IP pública. 
- **Configuración del grupo de servidores back-end**: cada grupo tiene una configuración como el puerto, el protocolo y la afinidad basada en cookies. Estos valores están vinculados a un grupo y se aplican a todos los servidores del grupo.
- **Puerto front-end:** este puerto es el puerto público abierto en la puerta de enlace de aplicaciones. El tráfico llega a este puerto y, a continuación, se redirige a uno de los servidores backend.
- **Agente de escucha**: la escucha tiene un puerto front-end, un protocolo (Http o Https, estos distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se configura la descarga de SSL). 
- **Regla:** enlaza el agente de escucha y el grupo de servidores backend, y define a qué grupo de servidores backend se debe dirigir el tráfico cuando llega a un agente de escucha determinado. Actualmente, solo se admite la regla *básica*. La regla *basic* regla es la distribución de carga round robin.

Puede llevar a cabo la configuración mediante la creación de un objeto de configuración o usando un archivo XML de configuración. Para llevar a cabo la configuración usando un archivo XML de configuración, use el siguiente ejemplo.



Tenga en cuenta lo siguiente:


- El elemento *FrontendIPConfigurations* describe los detalles del ILB relevantes para configurar la Puerta de enlace de aplicaciones con un ILB. 

- El elemento *Type* de la dirección IP del front-end debe establecerse en 'Private'

- El elemento *StaticIPAddress* debe establecerse en la dirección IP interna deseada en el que la puerta de enlace recibirá el tráfico. Tenga en cuenta que el elemento *StaticIPAddress* es opcional. Si no se establece, se elige una dirección IP interna disponible de la subred implementada.

- El valor del elemento *Name* especificado en *FrontendIPConfiguration* se debe usar en el elemento *FrontendIP* de HTTPListener para hacer referencia a FrontendIPConfiguration.

 **Ejemplo XML de configuración**

 

		<?xml version="1.0" encoding="utf-8"?>
		<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
			<FrontendIPConfigurations>
				<FrontendIPConfiguration>
					<Name>fip1</Name> 
					<Type>Private</Type> 
					<StaticIPAddress>10.0.0.10</StaticIPAddress> 
				</FrontendIPConfiguration>
			</FrontendIPConfigurations>
		    <FrontendPorts>
		        <FrontendPort>
		            <Name>FrontendPort1</Name>
		            <Port>80</Port>
		        </FrontendPort>
		    </FrontendPorts>
		    <BackendAddressPools>
		        <BackendAddressPool>
		            <Name>BackendPool1</Name>
		            <IPAddresses>
		                <IPAddress>10.0.0.1</IPAddress>
		                <IPAddress>10.0.0.2</IPAddress>
		            </IPAddresses>
		        </BackendAddressPool>
		    </BackendAddressPools>
		    <BackendHttpSettingsList>
		        <BackendHttpSettings>
		            <Name>BackendSetting1</Name>
		            <Port>80</Port>
		            <Protocol>Http</Protocol>
		            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
		        </BackendHttpSettings>
		    </BackendHttpSettingsList>
		    <HttpListeners>
		        <HttpListener>
		            <Name>HTTPListener1</Name>
					<FrontendIP>fip1</FrontendIP>
		            <FrontendPort>FrontendPort1</FrontendPort>
		            <Protocol>Http</Protocol>
		        </HttpListener>
		    </HttpListeners>
		    <HttpLoadBalancingRules>
		        <HttpLoadBalancingRule>
		            <Name>HttpLBRule1</Name>
		            <Type>basic</Type>
		            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
		            <Listener>HTTPListener1</Listener>
		            <BackendAddressPool>BackendPool1</BackendAddressPool>
		        </HttpLoadBalancingRule>
		    </HttpLoadBalancingRules>
		</ApplicationGatewayConfiguration>
	


## Establecimiento de la configuración de la puerta de enlace

A continuación, establecerá la puerta de enlace de aplicaciones. Puede usar el cmdlet `Set-AzureApplicationGatewayConfig` con un objeto de configuración o con un archivo XML de configuración.

	PS C:\> Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

	VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
	VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## Inicio de la puerta de enlace

Una vez configurada la puerta de enlace, use el cmdlet `Start-AzureApplicationGateway` para iniciarla. La facturación de una puerta de enlace de aplicaciones comienza después de que se haya iniciado correctamente.


**Nota:** el cmdlet `Start-AzureApplicationGateway` puede tardar hasta 15-20 minutos en completarse.
   
	PS C:\> Start-AzureApplicationGateway AppGwTest 

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## Comprobación del estado de la puerta de enlace

Use el cmdlet `Get-AzureApplicationGateway` para comprobar el estado de la puerta de enlace. Si *Start-AzureApplicationGateway* se realizó correctamente en el paso anterior, State debe ser *Running*, y Vip y DnsName deben tener entradas válidas. Este ejemplo muestra el cmdlet en la primera línea, seguido de la salida. En este ejemplo, la puerta de enlace se está ejecutando y está lista para asumir tráfico.

**Nota**: la Puerta de enlace de aplicaciones está configurada para aceptar tráfico en el extremo del ILB configurado: 10.0.0.10 en este ejemplo.

	PS C:\> Get-AzureApplicationGateway AppGwTest 

	VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway 
	VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
	Name          : AppGwTest
	Description   : 
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Running
	VirtualIPs    : {10.0.0.10}
	DnsName       : appgw-b2a11563-2b3a-4172-a4aa-226ee4c23eed.cloudapp.net

## Pasos siguientes


Si desea obtener más información acerca de opciones de equilibrio de carga en general, vea:

- [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Administrador de tráfico de Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0128_2016-->