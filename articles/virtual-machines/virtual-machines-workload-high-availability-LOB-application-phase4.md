<properties 
	pageTitle="Fase 4 de la aplicación de línea de negocio | Microsoft Azure" 
	description="Cree los servidores web y cargue la aplicación de línea de negocio en ellos en la fase 4 de la aplicación de línea de negocio en Azure." 
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

# Fase 4 de la carga de trabajo de aplicación de línea de negocio: Configuración de servidores web

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

En esta fase de la implementación de una aplicación de línea de negocio de alta disponibilidad en servicios de infraestructura de Azure, creará los servidores web y cargará la aplicación de línea de negocio en ellos.

Debe completar esta fase antes de pasar a la [fase 5](virtual-machines-workload-high-availability-LOB-application-phase5.md). Vea [Implementación de una aplicación de línea de negocio de alta disponibilidad en Azure](virtual-machines-workload-high-availability-LOB-application-overview.md) en todas las fases.

## Creación de máquinas virtuales de servidor web en Azure

Hay dos máquinas virtuales de servidor web en las que puede implementar aplicaciones ASP.NET o aplicaciones más antiguas que pueden hospedarse en Internet Information Services (IIS) 8 en Windows Server 2012 R2.

En primer lugar, configure el equilibrio de carga interno para que Azure distribuya el tráfico de cliente a la aplicación de línea de negocio de manera uniforme entre los dos servidores web. Esto requiere que especifique una instancia de equilibrio de carga que conste de un nombre y su propia dirección IP, y se asigne en el espacio de direcciones de subred que asignó a la red virtual de Azure.

> [AZURE.NOTE] El siguiente comando establece el uso de Azure PowerShell 1.0 y versiones posteriores. Para más información, vea [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Especifique los valores de las variables quitando los caracteres < and >. Tenga en cuenta que los siguientes conjuntos de comandos de Azure PowerShell usan los valores de las siguientes tablas:

- Tabla M, para las máquinas virtuales
- Tabla V, para la configuración de red virtual
- Tabla S, para la subred
- Tabla ST, para las cuentas de almacenamiento
- Tabla A, para los conjuntos de disponibilidad

Recuerde que definió la tabla M en [Fase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md) y las tablas V, S, ST y A en [Fase 1](virtual-machines-workload-high-availability-LOB-application-phase1.md).

Cuando proporcione todos los valores adecuados, ejecute el bloque resultante en el símbolo del sistema de PowerShell de Azure.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$privIP="<available IP address on the subnet>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	$frontendIP=New-AzureRMLoadBalancerFrontendIpConfig -Name WebServers-LBFE -PrivateIPAddress $privIP -SubnetId $vnet.Subnets[1].Id
	$beAddressPool=New-AzureRMLoadBalancerBackendAddressPoolConfig -Name WebServers-LBBE

	# This example assumes unsecured (HTTP-based) web traffic to the web servers.
	$healthProbe=New-AzureRMLoadBalancerProbeConfig -Name WebServersProbe -Protocol "TCP" -Port 80 -IntervalInSeconds 15 -ProbeCount 2
	$lbrule=New-AzureRMLoadBalancerRuleConfig -Name "WebTraffic" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "TCP" -FrontendPort 80 -BackendPort 80
	New-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "WebServersInAzure" -Location $locName -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe -FrontendIpConfiguration $frontendIP

Agregue un registro de direcciones DNS a la infraestructura DNS interna de su organización que resuelva el nombre de dominio completo de la aplicación de línea de negocio (por ejemplo, lobapp.corp.contoso.com) a la dirección IP asignada al equilibrador de carga interno (valor de $privIP en el anterior bloque de comandos de Azure PowerShell).

Use el siguiente bloque de comandos de PowerShell para crear las máquinas virtuales para los tres servidores.

Cuando proporcione todos los valores adecuados, ejecute el bloque resultante en el símbolo del sistema de Azure PowerShell.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$webLB=Get-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "WebServersInAzure"	
	
	# Use the standard storage account
	$saName="<Table ST – Item 2 – Storage account name column>"

	$vnetName="<Table V – Item 1 – Value column>"
	$beSubnetName="<Table S - Item 2 - Name column>"
	$avName="<Table A – Item 3 – Availability set name column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$backendSubnet=Get-AzureRMVirtualNetworkSubnetConfig -Name $beSubnetName -VirtualNetwork $vnet
	
	# Create the first web server virtual machine
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$avSet=Get-AzureRMAvailabilitySet -Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first web server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second web server virtual machine
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second SQL Server computer." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE] Dado que estas máquinas virtuales son para una aplicación de intranet, no se les asigna una dirección IP pública o una etiqueta de nombre de dominio DNS ni se exponen a Internet. Sin embargo, esto significa también que no se puede conectar a ellas desde el Portal de Azure. El botón **Conectar** no estará disponible cuando vea las propiedades de la máquina virtual.

Use el cliente de Escritorio remoto que prefiera y cree una conexión de Escritorio remoto para cada máquina virtual de servidor web. Use el DNS de la intranet o el nombre del equipo y las credenciales de la cuenta de administrador local.

Para cada máquina virtual que ejecute SQL Server, únala al dominio de Active Directory adecuado con estos comandos en el símbolo del sistema de Windows PowerShell.

	$domName="<Active Directory domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

Tenga en cuenta que debe proporcionar credenciales de cuenta de dominio después de escribir el comando **Add-Computer**.

Cuando se reinicien, vuelva a conectarse a ellas con una cuenta que tenga privilegios de administrador local.

En cada servidor web, instale y configure IIS.

1. Ejecute el Administrador del servidor y haga clic en **Agregar roles y características**.
2. En la página Antes de comenzar, haga clic en **Siguiente**.
3. En la página Seleccionar tipo de instalación, haga clic en **Siguiente**.
4. En la página Seleccionar servidor de destino, haga clic en **Siguiente**.
5. En la página Roles de servidor, haga clic en **Servidor web (IIS)** en la lista de **Roles**.
6. Cuando se le solicite, haga clic en **Agregar características** y después en **Siguiente**.
7. En la página Selección de características, haga clic en **Siguiente**.
8. En la página Servidor web (IIS), haga clic en **Siguiente**.
9. En la página Seleccionar servicios de rol, seleccione o desactive las casillas de los servicios que necesita para la aplicación de línea de negocio y haga clic en **Siguiente**. 10. En la página Confirmar selecciones de instalación, haga clic en **Instalar**.

## Implemente la aplicación de línea de negocio en las máquinas virtuales de servidor web.

Desde un equipo de la red local:

1.	Agregue los archivos de la aplicación de línea de negocio a los dos servidores web.
2.	Cree las bases de datos para su aplicación de línea de negocio en el clúster de SQL Server.
3.	Pruebe el acceso a la aplicación de línea de negocio y su funcionalidad.

Este diagrama representa la configuración resultante de la realización correcta de esta fase.

![](./media/virtual-machines-workload-high-availability-LOB-application-phase4/workload-lobapp-phase4.png)

## Paso siguiente

- Para completar la configuración de esta carga de trabajo, vaya a la [Fase 5](virtual-machines-workload-high-availability-LOB-application-phase5.md).

<!---HONumber=AcomDC_0128_2016-->