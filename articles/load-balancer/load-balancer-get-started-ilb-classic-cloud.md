<properties 
   pageTitle="Creación de un equilibrador de carga interno para servicios en la nube con el modelo de implementación clásica | Microsoft Azure"
   description="Información sobre cómo crear un equilibrador de carga interno mediante PowerShell con el modelo de implementación clásica"
   services="load-balancer-ilb"
   documentationCenter="na"
   authors="joaoma"
   manager="carolz"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="joaoma" />

# Introducción a la creación de un equilibrador de carga interno (clásico) para servicios en la nube

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

<BR>

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](load-balancer-get-started-ilb-arm-ps.md).


## Configuración del equilibrador de carga interno para los servicios en la nube

El equilibrador de carga interno es compatible tanto con las máquinas virtuales como con los servicios en la nube. Un punto de conexión del equilibrador de carga interno creado en un servicio en la nube que está fuera de una red virtual regional solo será accesible dentro del servicio en la nube.

La configuración del equilibrador de carga interno se debe establecer durante la creación de la primera implementación en el servicio en la nube, como se muestra en el ejemplo siguiente.

>[AZURE.IMPORTANT] Un requisito previo para ejecutar los pasos siguientes es tener ya creada una red virtual para la implementación en la nube. Necesitarás el nombre de red virtual y el nombre de la subred para crear el Equilibrio de carga interno.

### Paso 1

Abre el archivo de configuración de servicio (.cscfg) para la implementación en la nube en Visual Studio y agrega la siguiente sección para crear el Equilibrio de carga interno en el último elemento «`</Role>`» para la configuración de red.




	<NetworkConfiguration>
	  <LoadBalancers>
	    <LoadBalancer name="name of the load balancer">
	      <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
	    </LoadBalancer>
	  </LoadBalancers>
	</NetworkConfiguration>


Vamos a agregar los valores para que el archivo de configuración de red muestre su aspecto. En el ejemplo, supongamos que creó una subred que se llama "test\_vnet" con una subred 10.0.0.0/24 denominada test\_subnet y una dirección IP estática 10.0.0.4. El equilibrador de carga se denominará testLB.

	<NetworkConfiguration>
	  <LoadBalancers>
	    <LoadBalancer name="testLB">
	      <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
	    </LoadBalancer>
	  </LoadBalancers>
	</NetworkConfiguration>

Para obtener más información sobre el esquema del equilibrador de carga, consulte [Agregar equilibrador de carga](https://msdn.microsoft.com/library/azure/dn722411.aspx).

### Paso 2


Cambia los archivos de definición (.csdef) para agregar extremos al Equilibrio de carga interno. Cuando se crea una instancia de rol, el archivo de definición de servicio agregará las instancias de rol al Equilibrio de carga interno.


	<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
	  <Endpoints>
	    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
	  </Endpoints>
	</WorkerRole>

Vamos a agregar los valores del archivo de definición de servicio siguiendo los mismos valores del ejemplo anterior.

	<WorkerRole name=WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
	  <Endpoints>
	    <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
	  </Endpoints>
	</WorkerRole>

La carga del tráfico de red se equilibrará mediante el equilibrador de carga testLB, en el que se usa el puerto 80 para las solicitudes entrantes y se envía a instancias de rol de trabajo también en el puerto 80.


## Pasos siguientes

[Configurar un modo de distribución del equilibrador de carga mediante la afinidad IP de origen](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0218_2016-->