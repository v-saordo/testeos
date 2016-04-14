<properties 
   pageTitle="Introducción a la creación de un equilibrador de carga orientado a Internet en el modo clásico mediante PowerShell | Microsoft Azure"
   description="Obtenga información sobre cómo crear un equilibrador de carga orientado a Internet en el modo clásico con PowerShell."
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
   ms.date="01/21/2016"
   ms.author="joaoma" />

# Introducción a la creación de un equilibrador de carga orientado a Internet (clásico) en PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artículo trata sobre el modelo de implementación clásico. También puede [obtener información sobre cómo crear un equilibrador de carga orientado a Internet con el Administrador de recursos de Azure](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]



## Configurar el equilibrador de carga con PowerShell

Para configurar un equilibrador de carga con PowerShell, siga estos pasos:

1. Si es la primera vez que usa Azure PowerShell, vea [Instalación y configuración de Azure PowerShell](powershell-install-configure.md) y siga las instrucciones hasta el final para iniciar sesión en Azure y seleccionar su suscripción.


2. Después de crear una máquina virtual, puede usar cmdlets de PowerShell para agregar un equilibrador de carga a una máquina virtual dentro del mismo servicio en la nube.

En el ejemplo siguiente se agregará un conjunto de equilibrador de carga denominado "webfarm" al servicio en la nube "mytestcloud" (o myctestcloud.cloudapp.net), agregando los puntos de conexión para el equilibrador de carga a las máquinas virtuales denominadas "web1" y "web2". El equilibrador de carga recibe tráfico de red en el puerto 80 y equilibra la carga entre las máquinas virtuales definidas por el punto de conexión local (en este caso el puerto 80) mediante TCP.


### Paso 1
Crear un punto de conexión con equilibrio de carga para la primera máquina virtual "web1"

	Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

### Paso 2 

Crear otro punto de conexión para la segunda máquina virtual "web2" con el mismo nombre de conjunto de equilibrador de carga

	Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

## Quitar una máquina virtual de un equilibrador de carga

Puede usar Remove-AzureEndpoint para quitar un punto de conexión de la máquina virtual del equilibrador de carga.

	Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin| Update-AzureVM

## Pasos siguientes

También puede [empezar a crear un equilibrador de carga interno](load-balancer-get-started-ilb-classic-ps.md) y configurar el tipo de [modo de distribución](load-balancer-distribution-mode.md) para un comportamiento especifico del tráfico de red del equilibrador de carga.

Si la aplicación necesita mantener conexiones activas para servidores detrás de un equilibrador de carga, puede obtener más información acerca de la [configuración de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md). Le ayudará a conocer el comportamiento de conexión del tiempo de inactividad cuando se usa el Equilibrador de carga de Azure.

<!---HONumber=AcomDC_0128_2016-->