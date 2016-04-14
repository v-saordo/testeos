<properties 
   pageTitle="Configuración de un modo de distribución del equilibrador de carga | Microsoft Azure"
   description="Cómo configurar el modo de distribución del equilibrador de carga de Azure para admitir la afinidad de IP de origen"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="joaoma" />


# Modo de distribución del equilibrador de carga (afinidad de IP de origen)

Hemos introducido un nuevo modo de distribución llamado Afinidad de IP de origen (también conocido como afinidad de cliente o afinidad de IP de cliente). Para asignar el tráfico a los servidores disponibles, el Equilibrador de carga de Azure se puede configurar para usar una 2-tupla (IP de origen, IP de destino) o una 3-tupla (IP de origen, IP de destino, protocolo). Al usar la afinidad de IP de origen, las conexiones iniciadas desde el mismo equipo cliente van al mismo extremo DIP.

![equilibrador de carga basado en hash](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

La afinidad de IP de origen resuelve una incompatibilidad entre el Equilibrador de carga de Azure y la puerta de enlace de Escritorio remoto (RD). Ahora, puede crear una granja de servidores de puerta de enlace de Escritorio remoto en un solo servicio en la nube. Otro escenario de uso es la carga de contenido multimedia donde la carga de los datos reales tiene lugar a través de UDP pero el plano de control se consigue mediante TCP:

- Un cliente inicia primero una sesión TCP en la dirección pública con equilibrio de carga y se le dirige a una DIP específica; este canal se deja activo para supervisar el estado de conexión.
- Una nueva sesión UDP se inicia desde el mismo equipo cliente en el mismo extremo público con equilibrio de carga. Lo que se espera aquí, es que esta conexión también se dirija al mismo extremo DIP que la conexión TCP anterior, de modo que la carga de contenido multimedia se pueda ejecutar con un elevado rendimiento, al mismo tiempo que se mantiene también un canal de control a través de TCP.
 
Tenga en cuenta que si el conjunto con equilibrio de carga cambia (al quitar o agregar una máquina virtual), la distribución de las solicitudes de cliente se vuelve a calcular. No puede depender de nuevas conexiones desde sesiones de cliente existentes que terminan en el mismo servidor. Además, el uso del modo de distribución de afinidad de IP de origen puede ocasionar una distribución desigual del tráfico. Los clientes que se ejecutan detrás de servidores proxy pueden considerarse como una sola aplicación cliente.

El algoritmo de distribución usado para asignar el tráfico a los servidores disponibles es una 5-tupla (IP de origen, puerto de origen, IP de destino, puerto de destino, tipo de protocolo) . Dicho algoritmo solo proporciona adherencia dentro de una sesión de transporte. Los paquetes de la misma sesión TCP o UDP se dirigirán a la misma instancia de IP del centro de datos (DIP) tras el extremo con equilibrio de carga. Cuando el cliente se cierra y vuelve a abrir la conexión o inicia una nueva sesión desde la misma IP de origen, el puerto de origen cambia y provoca que el tráfico vaya hacia otro extremo DIP.

![equilibrador de carga basado en hash](./media/load-balancer-distribution-mode/load-balancer-distribution.png)


## Configuración de la afinidad de IP de origen para el equilibrador de carga
 
En las máquinas virtuales, puede usar PowerShell para cambiar la configuración de tiempo de espera:
 
Agregar un extremo de Azure a una máquina virtual y establecer el modo de distribución del equilibrador de carga

	Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM

>[AZURE.NOTE] LoadBalancerDistribution puede establecerse en sourceIP para equilibrio de carga de 2-tupla (IP de origen, IP de destino), en sourceIPProtocol para equilibrio de carga de 3-tupla (IP de origen, IP de destino, protocolo) o en ninguno si desea el comportamiento predeterminado de equilibrio de carga de tupla 5.


Recuperar una configuración de modo de distribución del equilibrador de carga de extremo

	PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

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
	LoadBalancerDistribution : sourceIP
 
Si el elemento LoadBalancerDistribution no está presente, el Equilibrador de carga de Azure usa el algoritmo predeterminado de 5-tupla.

 
### Establecer el modo de distribución en un conjunto de extremo de carga equilibrada

Si los extremos forman parte de un conjunto de extremos con equilibrio de carga, el modo de distribución debe establecerse en el conjunto de extremos con equilibrio de carga:

	Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocol TCP -ProbePort 8080 –LoadBalancerDistribution sourceIP

### Configuración de servicios en la nube para cambiar el modo de distribución

Puede aprovechar el SDK de Azure para .NET 2.5 (que se publicará en noviembre) para actualizar la configuración del extremo de servicios en la nube para los servicios en la nube que se crean en .csdef. Para actualizar el modo de distribución del equilibrador de carga para una implementación de servicios en la nube, se requiere una actualización de la implementación. Este es un ejemplo de los cambios de .csdef para la configuración de extremo:

	<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
  	<Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
  	</Endpoints>
	</WorkerRole>
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


## Ejemplo de API

Puede configurar la distribución del equilibrador de carga con la API de administración de servicios. Asegúrese de agregar el encabezado `x-ms-version` y que esté establecido en la versión `2014-09-01` o posterior.
 
Actualizar la configuración del conjunto de carga equilibrada especificado en una implementación

Ejemplo de solicitud

	POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet 

	x-ms-version: 2014-09-01 

	Content-Type: application/xml 

	<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"> 
	<InputEndpoint> 
	<LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName> 
	<LocalPort> local-port-number </LocalPort> 
	<Port> external-port-number </Port> 
	<LoadBalancerProbe> 
	<Port> port-assigned-to-probe </Port> 
	<Protocol> probe-protocol </Protocol> 
	<IntervalInSeconds> interval-of-probe </IntervalInSeconds> 
	<TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds> 
	</LoadBalancerProbe> 
	<Protocol> endpoint-protocol </Protocol> 
	<EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn> 
	<IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes> 
	<LoadBalancerDistribution>sourceIP</LoadBalancerDistribution> 
	</InputEndpoint> 
	</LoadBalancedEndpointList>

El valor de LoadBalancerDistribution puede ser sourceIP para la afinidad de 2-tupla, sourceIPProtocol para la afinidad de 3-tupla o ninguno (sin afinidad, es decir, 5-tupla).

	Response

	HTTP/1.1 202 Accepted 
	Cache-Control: no-cache 
	Content-Length: 0 
	Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0 
	x-ms-servedbyregion: ussouth2 
	x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af 
	Date: Thu, 16 Oct 2014 22:49:21 GMT

## Pasos siguientes

[Información general sobre el equilibrador de carga interno](load-balancer-internal-overview.md)

[Introducción a la configuración de un equilibrador de carga accesible desde Internet](load-balancer-internet-getstarted.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0218_2016-->