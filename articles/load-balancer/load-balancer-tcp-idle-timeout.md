<properties 
   pageTitle="Configuración de tiempo de espera de inactividad de TCP del equilibrador de carga | Microsoft Azure"
   description="Configuración del tiempo de espera de inactividad de TCP del equilibrador de carga"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/19/2015"
   ms.author="joaoma" />

# Cambio de la configuración de tiempo de espera de inactividad de TCP para el equilibrador de carga

En su configuración predeterminada, el Equilibrador de carga de Azure tiene una configuración de 'tiempo de espera de inactividad' de 4 minutos.

Esto significa que si en las sesiones tcp o http tiene un período de inactividad superior al de tiempo de espera, no hay ninguna garantía de que se mantenga la conexión entre el cliente y el servicio.

Cuando se cierra la conexión, la aplicación cliente recibirá un mensaje de error similar a "Se ha terminado la conexión: una conexión que se esperaba que se mantuviera activa fue cerrada por el servidor".

Una práctica común para mantener la conexión activa durante un período más largo es usar TCP Keep-alive (puede encontrar ejemplos de .NET [aquí](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)).

Los paquetes se envían cuando no se detecta ninguna actividad en la conexión. Si se mantiene constante la actividad de la red, nunca se alcanza el valor de tiempo de espera de inactividad y la conexión se mantiene durante un largo período.

La idea es configurar TCP Keep-alive con un intervalo menor que el valor de tiempo de espera predeterminado para evitar que la conexión se pierda, o aumentar el valor de tiempo de espera de inactividad para que la sesión de conexión TCP permanezca conectada.

Aunque TCP Keep-alive funciona bien en escenarios donde no existen restricciones de batería, por lo general no resulta una opción válida para las aplicaciones móviles. El uso de TCP Keep-alive desde una aplicación móvil probablemente vaciará la batería del dispositivo más rápidamente.

Para admitir tales escenarios, hemos agregado compatibilidad con un tiempo de espera de inactividad configurable. Ahora puede establecerlo en una duración de entre 4 y 30 minutos. Esta configuración funciona solo para conexiones entrantes.

![tcptimeout](./media/load-balancer-tcp-idle-timeout/image1.png)


## Cambio de la configuración de tiempo de espera de inactividad en máquinas virtuales y servicios en la nube

- Configuración del tiempo de espera TCP en un extremo de una máquina virtual a través de PowerShell o de la API de administración de servicios
- Configurar el tiempo de espera de TCP para los conjuntos de extremos con equilibrio de carga a través de PowerShell o de la API de administración de servicios.
- Configuración del tiempo de espera de TCP para la IP pública a nivel de instancia
- Configure el tiempo de espera de TCP para los roles web y de trabajo a través del modelo de servicio.
 

>[AZURE.NOTE]Tenga en cuenta que algunos comandos solo se incluirán en el paquete más reciente de Azure PowerShell. Si no aparece el comando que desea, descargue un paquete más reciente de PowerShell.

 
### Configure el tiempo de espera de TCP para la IP pública a nivel de instancia en 15 minutos

	Set-AzurePublicIP –PublicIPName webip –VM MyVM -IdleTimeoutInMinutes 15

El valor de IdleTimeoutInMinutes es opcional. Si no se establece, el tiempo de espera predeterminado es de 4 minutos.

>[AZURE.NOTE]El intervalo de tiempo de espera aceptable está entre 4 y 30 minutos.
 
### Establecer el tiempo de espera de inactividad al crear un extremo de Azure en una máquina virtual

Para cambiar la configuración de tiempo de espera de un extremo

	Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
 
Recuperar la configuración de tiempo de espera de inactividad

	PS C:\> Get-AzureVM –ServiceName “MyService” –Name “MyVM” | Get-AzureEndpoint
	VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
	LBSetName : MyLoadBalancedSet
	LocalPort : 80
	Name : HTTP
	Port : 80
	Protocol : tcp
	Vip : 65.52.xxx.xxx
	ProbePath :
	ProbePort : 80
	ProbeProtocol : tcp
	ProbeIntervalInSeconds : 15
	ProbeTimeoutInSeconds : 31
	EnableDirectServerReturn : False
	Acl : {}
	InternalLoadBalancerName :
	IdleTimeoutInMinutes : 15
 
### Establecer el tiempo de espera de TCP en un conjunto de extremo de carga equilibrada

Si los extremos forman parte de un conjunto de extremo de carga equilibrada, el tiempo de espera de TCP se debe establecer en el conjunto de extremo de carga equilibrada:

	Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
 
### Cambio de la configuración de tiempo de espera de los servicios en la nube

Puede aprovechar el SDK de Azure para .NET para actualizar el servicio en la nube.

La configuración de extremo para los servicios en la nube se realiza en el archivo .csdef. Para actualizar el tiempo de espera de TCP para una implementación de servicios en la nube, se requiere una actualización de la implementación. Una excepción es si el tiempo de espera de TCP solo se especifica para una dirección IP pública. La configuración de IP pública se encuentra en el archivo .cscfg y se puede actualizar a través de la actualización de la implementación.

Los cambios de .csdef para la configuración de extremo son:

	<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
	  <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
	  </Endpoints>
	</WorkerRole>

Los cambios de .cscfg para el valor de tiempo de espera en las direcciones IP públicas son:

	<NetworkConfiguration>
 	 <VirtualNetworkSite name="VNet"/>
 	 <AddressAssignments>
    <InstanceAddress roleName="VMRolePersisted">
      <PublicIPs>
        <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
      </PublicIPs>
    </InstanceAddress>
 	 </AddressAssignments>
	</NetworkConfiguration>

## Ejemplo de API de REST

Puede configurar el tiempo de inactividad de TCP mediante la API de administración de servicios. Asegúrese de agregar el encabezado x-ms-version y que esté establecido en la versión 2014-06-01 o posterior.
 
Actualizar la configuración de los extremos de entrada de carga equilibrada especificados en todas las máquinas virtuales de una implementación
	
	Request

	POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>
<BR>

	Response

	<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
	<InputEndpoint>
	<LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
	<LocalPort>local-port-number</LocalPort>
	<Port>external-port-number</Port>
	<LoadBalancerProbe>
	<Path>path-of-probe</Path>
	<Port>port-assigned-to-probe</Port>
	<Protocol>probe-protocol</Protocol>
	<IntervalInSeconds>interval-of-probe</IntervalInSeconds>
	<TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
	</LoadBalancerProbe>
	<LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
	<Protocol>endpoint-protocol</Protocol>
	<IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
	<EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
	<EndpointACL>
	<Rules>
	<Rule>
	<Order>priority-of-the-rule</Order>
	<Action>permit-rule</Action>
	<RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
	<Description>description-of-the-rule</Description>
	</Rule>
	</Rules>
	</EndpointACL>
	</InputEndpoint>
	</LoadBalancedEndpointList>

## Pasos siguientes

[Información general sobre el equilibrador de carga interno](load-balancer-internal-overview.md)

[Introducción a la configuración de un equilibrador de carga accesible desde Internet](load-balancer-internet-getstarted.md)

[Configuración de un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

 

<!---HONumber=AcomDC_1125_2015-->