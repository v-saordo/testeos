<properties
   pageTitle="Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante la implementación clásica | Microsoft Azure"
   description="En este artículo se ofrecen instrucciones para crear una puerta de enlace de aplicaciones con descarga SSL mediante el modelo de implementación clásica de Azure."
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
   ms.date="02/02/2016"
   ms.author="joaoma"/>

# Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante el modelo de implementación clásica

> [AZURE.SELECTOR]
-[Azure Classic PowerShell](application-gateway-ssl.md)
-[Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)

Puerta de enlace de aplicaciones de Azure puede configurarse para terminar la sesión Capa de sockets seguros (SSL) en la puerta de enlace para evitar las costosas tareas de descifrado SSL que tienen lugar en la granja de servidores web. La descarga SSL también simplifica la configuración del servidor front-end y la administración de la aplicación web.


## Antes de empezar

1. Instale la versión más reciente de los cmdlets de Azure PowerShell mediante el Instalador de plataforma web. Puede descargar e instalar la versión más reciente desde la sección **Windows PowerShell** de la página [Descargas](https://azure.microsoft.com/downloads/).
2. Compruebe que tiene una red virtual de trabajo con una subred válida. Asegúrese de que ninguna máquina virtual o implementación en la nube usan la subred. La Puerta de enlace de aplicaciones debe encontrarse en una subred de red virtual.
3. Los servidores que configurará para que usen la Puerta de enlace de aplicaciones deben existir, o bien sus puntos de conexión deben haberse creado en la red virtual o tener una dirección IP/VIP pública asignada.

Para configurar la descarga SSL en una puerta de enlace de aplicaciones, realice los pasos siguientes en el orden mostrado:

1. [Creación de una nueva puerta de enlace de aplicaciones](#create-a-new-application-gateway)
2. [Carga de certificados SSL](#upload-ssl-certificates)
3. [Configuración de la puerta de enlace](#configure-the-gateway)
4. [Establecimiento de la configuración de la puerta de enlace](#set-the-gateway-configuration)
5. [Inicio de la puerta de enlace](#start-the-gateway)
6. [Comprobación del estado de la puerta de enlace](#verify-the-gateway-status)


## Creación de una nueva puerta de enlace de aplicaciones

Para crear la puerta de enlace, use el cmdlet **New-AzureApplicationGateway**, reemplazando los valores por los suyos propios. Tenga en cuenta que la facturación de la puerta de enlace no se inicia en este momento. La facturación comienza en un paso posterior, cuando la puerta de enlace se ha iniciado correctamente.

Este ejemplo muestra el cmdlet en la primera línea, seguido de la salida.

	PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399

Para validar que se creó la puerta de enlace, puede usar el cmdlet **Get-AzureApplicationGateway**.

En el ejemplo, *Description*, *InstanceCount* y *GatewaySize* son parámetros opcionales. El valor predeterminado de *InstanceCount* es 2, con un valor máximo de 10. El valor predeterminado de *GatewaySize* es Medium. Small y Large son otros valores disponibles. *VirtualIPs* y *DnsName* se muestran en blanco porque todavía no se ha iniciado la puerta de enlace. Se crearán una vez que la puerta de enlace esté en estado de ejecución.

Este ejemplo muestra el cmdlet en la primera línea, seguido de la salida.

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


## Carga de certificados SSL

Use **Add-AzureApplicationGatewaySslCertificate** para cargar el certificado de servidor en formato *pfx* en la puerta de enlace de aplicaciones. El nombre del certificado es un nombre elegido por el usuario y debe ser único dentro de la puerta de enlace de aplicaciones. Este certificado se conoce con este nombre en todas las operaciones de administración de certificados en la puerta de enlace de aplicaciones.

Este ejemplo muestra el cmdlet en la primera línea, seguido de la salida. Reemplace los valores del ejemplo por los suyos propios.

	PS C:\> Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>

	VERBOSE: 5:05:23 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate
	VERBOSE: 5:06:29 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   21fdc5a0-3bf7-2c12-ad98-192e0dd078ef

A continuación, valide la carga del certificado. Use el cmdlet **Get-AzureApplicationGatewayCertificate**.

Este ejemplo muestra el cmdlet en la primera línea, seguido de la salida.

	PS C:\> Get-AzureApplicationGatewaySslCertificate AppGwTest

	VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate
	VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
	Name           : SslCert
	SubjectName    : CN=gwcert.app.test.contoso.com
	Thumbprint     : AF5ADD77E160A01A6......EE48D1A
	ThumbprintAlgo : sha1RSA
	State..........: Provisioned


## Configuración de la puerta de enlace

Una configuración de puerta de enlace de aplicaciones consta de varios valores. Los valores pueden ir juntos para construir la configuración.

Los valores son:

- **Grupo de servidores back-end**: lista de direcciones IP de los servidores back-end. Las direcciones IP que se enumeran deben pertenecer a la subred de la red virtual o ser una IP/VIP pública.
- **Configuración del grupo de servidores back-end**: cada grupo tiene una configuración como el puerto, el protocolo y la afinidad basada en cookies. Estos valores están vinculados a un grupo y se aplican a todos los servidores del grupo.
- **Puerto front-end**: este puerto es el puerto público que se abre en la puerta de enlace de aplicaciones. El tráfico llega a este puerto y después se redirige a uno de los servidores back-end.
- **Agente de escucha**: el agente de escucha tiene un puerto front-end, un protocolo (Http o Https, que distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se configura la descarga de SSL).
- **Regla**: enlaza el agente de escucha y el grupo de servidores back-end y define a qué grupo de servidores back-end se redirigirá el tráfico cuando se seleccione un agente de escucha concreto. Actualmente, solo se admite la regla *básica*. La regla *básica* es la distribución de carga round robin.

**Notas de configuración adicionales**

Para la configuración de certificados SSL, el protocolo de **HttpListener** debería cambiar a *Https* (con distinción entre mayúsculas y minúsculas). El elemento **SslCert** debe agregarse al elemento **HttpListener** con el valor establecido en el mismo nombre que se usa en la carga de la sección de certificados SSL anterior. El puerto front-end debe actualizarse a 443.

**Para habilitar la afinidad basada en cookies**: se puede configurar una Puerta de enlace de aplicaciones para asegurarse de que una solicitud de una sesión de cliente siempre se dirige a la misma máquina virtual en la granja de servidores web. Para ello es necesario inyectar una cookie de sesión que permite a la puerta de enlace dirigir el tráfico de forma adecuada. Para habilitar la afinidad basada en cookies, establezca **CookieBasedAffinity** en *Habilitado* en el elemento **BackendHttpSettings**.



Puede llevar a cabo la configuración mediante la creación de un objeto de configuración o usando un archivo XML de configuración. Para llevar a cabo la configuración usando un archivo XML de configuración, use el siguiente ejemplo.

**Ejemplo XML de configuración**


	    <?xml version="1.0" encoding="utf-8"?>
	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
	    <FrontendIPConfigurations />
	    <FrontendPorts>
	        <FrontendPort>
	            <Name>FrontendPort1</Name>
	            <Port>443</Port>
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
	            <FrontendPort>FrontendPort1</FrontendPort>
	            <Protocol>Https</Protocol>
	            <SslCert>GWCert</SslCert>
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

A continuación, establecerá la puerta de enlace de aplicaciones. Puede usar el cmdlet **Set-AzureApplicationGatewayConfig** con un objeto de configuración o con un archivo XML de configuración.


	PS C:\> Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

	VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig
	VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## Inicio de la puerta de enlace

Una vez configurada la puerta de enlace, use el cmdlet **Start-AzureApplicationGateway** para iniciarla. La facturación de una puerta de enlace de aplicaciones comienza después de que se haya iniciado correctamente.


**Nota:** el cmdlet **Start-AzureApplicationGateway** puede tardar hasta 15 o 20 minutos en finalizar.


	PS C:\> Start-AzureApplicationGateway AppGwTest

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b


## Comprobación del estado de la puerta de enlace

Use el cmdlet **Get-AzureApplicationGateway** para comprobar el estado de la puerta de enlace. Si **Start-AzureApplicationGateway** se realizó correctamente en el paso anterior, *State* debería ser Running y *VirtualIPs* y *DnsName* deben tener entradas válidas.

Este ejemplo muestra una puerta de enlace de aplicaciones que está operativa, en ejecución y lista para asumir el tráfico.

	PS C:\> Get-AzureApplicationGateway AppGwTest

	Name          : AppGwTest2
	Description   :
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Running
	VirtualIPs    : {23.96.22.241}
	DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net


## Pasos siguientes


Si desea obtener más información acerca de opciones de equilibrio de carga en general, vea:

- [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Administrador de tráfico de Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0218_2016-->