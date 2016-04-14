<properties 
   pageTitle="Creación de un equilibrador de carga interno mediante la CLI de Azure el modelo de implementación clásica | Microsoft Azure"
   description="Información sobre cómo crear un equilibrador de carga interno mediante la CLI de Azure en el modelo de implementación clásica"
   services="load-balancer"
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

# Primeros pasos en la creación de un equilibrador de carga interno (clásico) mediante la CLI de Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](load-balancer-get-started-ilb-arm-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


## Para crear un equilibrador de carga interno establecido para máquinas virtuales

Para crear un conjunto con equilibrio de carga interno y los servidores que enviarán su tráfico a él, debe hacer lo siguiente:

1. Crea una instancia de Equilibrio de carga interno que será el extremo del tráfico entrante que su carga se va a equilibrar entre los servidores de un conjunto con equilibrio de carga.

1. Agregue extremos correspondientes a las máquinas virtuales que van a recibir el tráfico entrante.

1. Configura los servidores que van a enviar el tráfico cuya carga se va a equilibrar para que lo hagan a la dirección IP virtual (VIP) de la instancia de Equilibrio de carga interno.

## Creación paso a paso de un equilibrador de carga interno mediante la CLI

Esta guía muestra cómo crear un equilibrador de carga interno basado en el escenario anterior.

1. Si nunca usaste la CLI de Azure, consulta [Instalar y configurar la CLI de Azure](xplat-cli.md) y sigue las instrucciones hasta el punto donde tienes que seleccionar tu cuenta y suscripción de Azure.

2. Ejecute el comando **azure config mode** para cambiar al modo clásico, como se muestra a continuación.

		azure config mode asm

	Resultado esperado:

		info:    New mode is asm


## Crear punto de conexión y conjunto de equilibrador de carga 

En el escenario se supone la presencia de las máquinas virtuales "DB1" y "DB2" en un servicio en la nube denominado "mytestcloud". Ambas máquinas virtuales usan una red virtual denominada mi "testvnet" con la subred "subnet-1".

Esta guía creará un conjunto de equilibrador de carga interno mediante el puerto 1433 como puerto privado y 1433 como puerto local.

Se trata de un escenario común donde hay máquinas virtuales de SQL en el back-end que usan un equilibrador de carga interno para garantizar que los servidores de base de datos no se exponen directamente mediante una dirección IP pública.


### Paso 1 

Crear un conjunto de equilibrador de carga interno mediante `azure network service internal-load-balancer add`.

	 azure service internal-load-balancer add -r mytestcloud -n ilbset -t subnet-1 -a 192.168.2.7

Parámetros usados:

**-r**: nombre del servicio en la nube<BR> **-n**: nombre del equilibrador de carga interno<BR> **-t**: nombre de la subred (misma subred de las máquinas virtuales que se va a agregar al equilibrador de carga interno)<BR> **-a**: (opcional) agregar una dirección IP privada estática<BR>

Para obtener más información, consulte `azure service internal-load-balancer --help`.
 
Puede comprobar las propiedades del equilibrador de carga interno mediante el comando `azure service internal-load-balancer list` *nombre de servicio en la nube*.

A continuación se sigue un ejemplo de la salida:

	azure service internal-load-balancer list my-testcloud
	info:    Executing command service internal-load-balancer list
	+ Getting cloud service deployment
	data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
	data:    ------  -------  ----------  -----------------------------
	data:    ilbset  Private  subnet-1    192.168.2.7
	info:    service internal-load-balancer list command OK


## Paso 2 

Configurar el conjunto del equilibrador de carga interno al agregar el primer punto de conexión. En este paso se asociará el punto de conexión, la máquina virtual y el puerto de sondeo al conjunto del equilibrador de carga interno.

	azure vm endpoint create db1 1433 -k 1433 tcp -t 1433 -r tcp -e 300 -f 600 -i ilbset

Parámetros usados:

**-k**: puerto de la máquina virtual local<BR> **-t**: puerto de sondeo<BR> **- r**: protocolo de sondeo<BR> **-e**: intervalo de sondeo en segundos<BR> **-f**: intervalo de tiempo de espera en segundos <BR> **-i**: nombre del equilibrador de carga interno <BR>


## Paso 3 

Comprobar la configuración del equilibrador de carga mediante el `azure vm show` *nombre de la máquina virtual*

	azure vm show DB1 

El resultado será:

		azure vm show DB1
	info:    Executing command vm show
	+ Getting virtual machines
	data:    DNSName "mytestcloud.cloudapp.net"
	data:    Location "East US 2"
	data:    VMName "DB1"
	data:    IPAddress "192.168.2.4"
	data:    InstanceStatus "ReadyRole"
	data:    InstanceSize "Standard_D1"
	data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
	data:    OSDisk hostCaching "ReadWrite"
	data:    OSDisk name "db1-DB1-0-201511120457370846"
	data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
	data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
	data:    OSDisk operatingSystem "Windows"
	data:    OSDisk iOType "Standard"
	data:    ReservedIPName ""
	data:    VirtualIPAddresses 0 address "137.116.64.107"
	data:    VirtualIPAddresses 0 name "db1ContractContract"
	data:    VirtualIPAddresses 0 isDnsProgrammed true
	data:    VirtualIPAddresses 1 address "192.168.2.7"
	data:    VirtualIPAddresses 1 name "ilbset"
	data:    Network Endpoints 0 localPort 5986
	data:    Network Endpoints 0 name "PowerShell"
	data:    Network Endpoints 0 port 5986
	data:    Network Endpoints 0 protocol "tcp"
	data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"	
	data:    Network Endpoints 0 enableDirectServerReturn false
	data:    Network Endpoints 1 localPort 3389
	data:    Network Endpoints 1 name "Remote Desktop"
	data:    Network Endpoints 1 port 60173
	data:    Network Endpoints 1 protocol "tcp"
	data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
	data:    Network Endpoints 1 enableDirectServerReturn false
	data:    Network Endpoints 2 localPort 1433
	data:    Network Endpoints 2 name "tcp-1433-1433"
	data:    Network Endpoints 2 port 1433
	data:    Network Endpoints 2 loadBalancerProbe port 1433
	data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
	data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
	data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
	data:    Network Endpoints 2 protocol "tcp"
	data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
	data:    Network Endpoints 2 enableDirectServerReturn false
	data:    Network Endpoints 2 loadBalancerName "ilbset"
	info:    vm show command OK


## Crear un punto de conexión de escritorio remoto para una máquina virtual

Puede crear un punto de conexión de escritorio remoto para reenviar el tráfico de red desde un puerto público a un puerto local para una máquina virtual específica mediante `azure vm endpoint create`.

	azure vm endpoint create web1 54580 -k 3389 


## Quitar máquina virtual del equilibrador de carga

Puede quitar una máquina virtual de un equilibrador de carga interno establecido eliminando el punto de conexión asociado. Una vez eliminado el punto de conexión, la máquina virtual deja de pertenecer al conjunto de equilibrador de carga.

 En el ejemplo anterior, puede quitar el punto de conexión creado para la máquina virtual "DB1" del equilibrador de carga interno "ilbset" mediante el comando `azure vm endpoint delete`.

	azure vm endpoint delete DB1 tcp-1433-1433


Para obtener más información, consulte `azure vm endpoint --help`.


## Pasos siguientes

[Configurar un modo de distribución del equilibrador de carga mediante la afinidad IP de origen](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0218_2016-->