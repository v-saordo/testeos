<properties
	pageTitle="Fase 4 de la granja de SharePoint Server 2013 | Microsoft Azure"
	description="Cree las máquinas virtuales del servidor de SharePoint y una nueva granja de SharePoint en la fase 4 de la granja de SharePoint Server 2013 en Azure."
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
	ms.date="12/11/2015"
	ms.author="josephd"/>

# Fase 4 de la carga de trabajo de la granja de servidores de intranet de SharePoint: Configuración de servidores de SharePoint

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

En esta fase de la implementación de una granja de servidores de SharePoint 2013 de solo intranet con grupos de disponibilidad AlwaysOn de SQL Server en servicios de infraestructura de Azure, creará los niveles web y de aplicaciones de la granja de servidores de SharePoint y creará la granja de servidores con el Asistente para configuración de SharePoint.

Debe completar esta fase antes de pasar a la [fase 5](virtual-machines-workload-intranet-sharepoint-phase5.md). Consulte [Implementación de SharePoint con grupos de disponibilidad AlwaysOn de SQL Server en Azure](virtual-machines-workload-intranet-sharepoint-overview.md) para ver todas las fases.

## Creación de máquinas virtuales de servidor de SharePoint en Azure

Existen cuatro máquinas virtuales de servidor de SharePoint. Dos máquinas virtuales de servidor de SharePoint son para los servidores web front-end y dos son para la administración y el hospedaje de aplicaciones de SharePoint. Dos servidores de SharePoint en cada nivel de proporcionan una alta disponibilidad.

En primer lugar, configure el equilibrio de carga interno para que Azure distribuya el tráfico de cliente de manera uniforme entre los dos servidores web front-end. Esto requiere que especifique una instancia de equilibrio de carga que conste de un nombre y su propia dirección IP, obtenidos del espacio de direcciones de subred que asignó a la red virtual de Azure.

> [AZURE.NOTE]El siguiente comando establece el uso de Azure PowerShell 1.0 y versiones posteriores. Para obtener más información, consulte [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Especifique los valores de las variables quitando los caracteres < and >. Tenga en cuenta que los siguientes conjuntos de comandos de Azure PowerShell usan los valores de las siguientes tablas:

- Tabla M, para las máquinas virtuales
- Tabla V, para la configuración de red virtual
- Tabla S, para la subred
- Tabla ST, para las cuentas de almacenamiento
- Tabla A, para los conjuntos de disponibilidad

Recuerde que definió la Tabla M en [Fase 2: Configuración de controladores de dominio](virtual-machines-workload-intranet-sharepoint-phase2.md) y las Tablas V, S, ST y A en [Fase 1: Configuración de Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).

Cuando proporcione todos los valores adecuados, ejecute el bloque resultante en el símbolo del sistema de PowerShell de Azure.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$privIP="<available IP address on the subnet>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	$frontendIP=New-AzureRMLoadBalancerFrontendIpConfig -Name SharePointWebServers-LBFE -PrivateIPAddress $privIP -SubnetId $vnet.Subnets[1].Id
	$beAddressPool=New-AzureRMLoadBalancerBackendAddressPoolConfig -Name SharePointWebServers-LBBE

	# This example assumes unsecured (HTTP-based) web traffic to the web servers.
	$healthProbe=New-AzureRMLoadBalancerProbeConfig -Name WebServersProbe -Protocol "TCP" -Port 80 -IntervalInSeconds 15 -ProbeCount 2
	$lbrule=New-AzureRMLoadBalancerRuleConfig -Name "WebTraffic" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "TCP" -FrontendPort 80 -BackendPort 80
	New-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "SharePointWebServersInAzure" -Location $locName -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe -FrontendIpConfiguration $frontendIP

A continuación, agregue un registro de dirección DNS para la infraestructura DNS interna que resuelva el nombre de dominio completo de la granja de servidores de SharePoint (por ejemplo, spfarm.corp.contoso.com) a la dirección IP asignada al equilibrador de carga interno (el valor de $privIP en el bloque de comandos anterior de Azure PowerShell).

Use el siguiente bloque de comandos de Azure PowerShell para crear las máquinas virtuales para los cuatro servidores de SharePoint. Cuando proporcione todos los valores adecuados, ejecute el bloque resultante en el símbolo del sistema de PowerShell de Azure.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$saName="<Table ST – Item 2 – Storage account name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	$avName="<Table A – Item 3 – Availability set name column>"
	
	# Create the first application server
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first application server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

	# Create the second application server
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second application server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

	# Change the availability set
	$avName="<Table A – Item 4 – Availability set name column>"

	# Set up key variables
	$beSubnetName="<Table S - Item 2 - Name column>"
	$webLB=Get-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "SharePointWebServersInAzure"	
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$backendSubnet=Get-AzureRMVirtualNetworkSubnetConfig -Name $beSubnetName -VirtualNetwork $vnet
	
	# Create the first front end web server virtual machine
	$vmName="<Table M – Item 8 - Virtual machine name column>"
	$vmSize="<Table M – Item 8 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$avSet=Get-AzureRMAvailabilitySet -Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first front end web server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second front end web server virtual machine
	$vmName="<Table M – Item 9 - Virtual machine name column>"
	$vmSize="<Table M – Item 9 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second front end web server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE]Dado que estas máquinas virtuales son para una aplicación de intranet, no se les asigna una dirección IP pública o una etiqueta de nombre de dominio DNS ni se exponen a Internet. Sin embargo, esto significa también que no se puede conectar a ellas desde el Portal de Azure. El botón **Conectar** no estará disponible cuando vea las propiedades de la máquina virtual.

Use el cliente de Escritorio remoto que prefiera y cree una conexión de Escritorio remoto para cada máquina virtual. Use el DNS de la intranet o el nombre del equipo y las credenciales de la cuenta de administrador local.

Para cada máquina virtual, únala al dominio de Active Directory adecuado con estos comandos en el símbolo del sistema de Windows PowerShell.

	$domName="<Active Directory domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

Tenga en cuenta que debe proporcionar credenciales de cuenta de dominio después de escribir el comando **Add-Computer**.

Después del reinicio, use el procedimiento [Inicio de sesión en una máquina virtual con una conexión a Escritorio remoto](virtual-machines-workload-intranet-sharepoint-phase2.md#logon) cuatro veces, una vez para cada servidor de SharePoint, para iniciar sesión con las credenciales de cuenta [Domain]\\sp\_farm\_db. Ha creado estas credenciales en [Fase 2: configuración de controladores de dominio](virtual-machines-workload-intranet-sharepoint-phase2.md).

Use el procedimiento [Para probar la conectividad](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn) cuatro veces, uno para cada servidor de SharePoint, para probar la conectividad en las ubicaciones de la red de la organización.

> [AZURE.NOTE]Los servidores de SharePoint se crean a partir de la imagen de la versión de evaluación de SharePoint Server 2013. Necesita convertir la instalación para que utilice una clave de licencia de minorista o de licencia por volumen para las ediciones Standard o Enterprise de SharePoint Server 2013.

## Configuración de la granja de servidores de SharePoint

Siga estos pasos para configurar el primer servidor de SharePoint en la granja de servidores:

1.	Desde el escritorio del primer servidor de aplicaciones de SharePoint, haga doble clic en **Asistente para configuración de productos de SharePoint 2013**. Cuando se le pida que permita al programa realizar cambios en el equipo, haga clic en **Sí**.
2.	En la página **Productos de SharePoint**, haga clic en **Siguiente**.
3.	Aparece un cuadro de diálogo **Asistente para configuración de productos de SharePoint** que le advierte que se reiniciarán o restablecerán los servicios (como IIS). Haga clic en **Sí**.
4.	En la página **Conectar a una granja de servidores**, seleccione **Crear una nueva granja de servidores** y después haga clic en **Siguiente**.
5.	En la página **Especificar las opciones de la base de datos de configuración**:
 - En **Servidor de base de datos**, escriba el nombre del servidor de la base de datos principal.
 - En **Nombre de usuario**, escriba [dominio]** \\sp\_farm\_db** (creado en [Fase 2: Configuración de controladores de dominio](virtual-machines-workload-intranet-sharepoint-phase2.md)). Recuerde que la cuenta de sp\_farm\_db tiene privilegios de sysadmin en el servidor de la base de datos.
 - En **Contraseña**, escriba la contraseña de la cuenta de sp\_farm\_db.
6.	Haga clic en **Siguiente**.
7.	En la página **Especificar configuración de seguridad del conjunto de servidores**, escriba una frase de contraseña dos veces. Registre la frase de contraseña y almacénela en una ubicación segura para futuras referencias. Haga clic en **Siguiente**.
8.	En la página **Configurar la aplicación web de Administración central de SharePoint**, haga clic en **Siguiente**.
9.	Aparecerá la página **Finalizando el Asistente para configuración de Productos de SharePoint**. Haga clic en **Siguiente**.
10.	Aparecerá la página **Configurando Productos de SharePoint**. Espere hasta que se complete el proceso de configuración, aproximadamente 8 minutos.
11.	Después de configurar correctamente la granja de servidores, haga clic en **Finalizar**. Se iniciará el nuevo sitio web de administración.
12.	Para empezar a configurar la granja de servidores de SharePoint, haga clic en **Iniciar el Asistente**.

Realice el procedimiento siguiente en el segundo servidor de aplicaciones de SharePoint y los dos servidores web front-end:

1.	En el escritorio, haga doble clic en ** Asistente para la configuración de productos de SharePoint 2013**. Cuando se le pida que permita al programa realizar cambios en el equipo, haga clic en **Sí**.
2.	En la página **Productos de SharePoint**, haga clic en **Siguiente**.
3.	Aparece un cuadro de diálogo **Asistente para configuración de productos de SharePoint** que le advierte que se reiniciarán o restablecerán los servicios (como IIS). Haga clic en **Sí**.
4.	En la página **Conectar a una granja de servidores**, haga clic en **Conectar con un conjunto de servidores existente** y después haga clic en **Siguiente**.
5.	En la página **Especificar las opciones de la base de datos de configuración**, escriba el nombre del servidor de base de datos principal en **Servidor de base de datos** y después haga clic en **Recuperar nombres de base de datos**.
6.	Haga clic en **SharePoint\_Config** en la lista de nombres de la base de datos y después haga clic en **Siguiente**.
7.	En la página **Especificar configuración de seguridad del conjunto de servidores**, escriba la frase de contraseña del procedimiento anterior. Haga clic en **Siguiente**.
8.	Aparecerá la página **Finalizando el Asistente para configuración de Productos de SharePoint**. Haga clic en **Siguiente**.
9.	En la página **Configuración realizada correctamente**, haga clic en **Finalizar**.

Cuando SharePoint crea la granja de servidores, configura un conjunto de inicios de sesión de servidor en la máquina virtual principal de SQL Server. SQL Server 2012 introduce el concepto de usuarios con contraseñas para bases de datos independientes. La base de datos almacena todos los metadatos de la base de datos y la información de usuario, y un usuario que se define en esta base de datos no necesita tener un inicio de sesión correspondiente. La información de esta base de datos se replica mediante el grupo de disponibilidad y está disponible después de una conmutación por error. Para obtener más información, consulte [Bases de datos independientes](http://go.microsoft.com/fwlink/p/?LinkId=262794).

Sin embargo, las bases de datos de SharePoint no son bases de datos independientes de forma predeterminada. Por lo tanto, deberá configurar manualmente el servidor de base de datos secundario para que tenga el mismo conjunto de inicios de sesión para las cuentas de granja de servidores de SharePoint que el servidor de base de datos principal. Puede realizar esta sincronización desde SQL Server Management Studio conectándose a ambos servidores al mismo tiempo.

Tras concluir la instalación inicial, existen más opciones de configuración para las capacidades de la granja de servidores de SharePoint. Para obtener más información, consulte [Planificación para SharePoint 2013 en los servicios de infraestructura de Azure](http://msdn.microsoft.com/library/dn275958.aspx).

Esta es la configuración resultante de la realización correcta de esta fase.

![](./media/virtual-machines-workload-intranet-sharepoint-phase4/workload-spsqlao_04.png)

## Paso siguiente

- Para continuar con la configuración de esta carga de trabajo, vaya a la [Fase 5](virtual-machines-workload-intranet-sharepoint-phase5.md).

<!---HONumber=AcomDC_1217_2015-->