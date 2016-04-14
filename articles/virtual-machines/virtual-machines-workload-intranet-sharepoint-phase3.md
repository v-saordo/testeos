<properties
	pageTitle="Fase 3 de la granja de SharePoint Server 2013 | Microsoft Azure"
	description="Cree los equipos y el clúster de SQL Server y habilite los grupos de disponibilidad en la fase 3 de la granja de SharePoint Server 2013 en Azure."
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

# Fase 3 de la carga de trabajo de la granja de servidores de intranet de SharePoint: Configuración de la infraestructura de servidor SQL

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

En esta fase de la implementación de una granja de servidores de SharePoint 2013 de solo de intranet con grupos de disponibilidad AlwaysOn de SQL Server en servicios de infraestructura de Azure, cree y configure los dos equipos de SQL Server y el equipo de nodo de la mayoría de clúster y, a continuación, combínelos en un clúster de Windows Server.

Debe completar esta fase antes de pasar a la [fase 4](virtual-machines-workload-intranet-sharepoint-phase4.md). Consulte [Implementación de SharePoint con grupos de disponibilidad AlwaysOn de SQL Server en Azure](virtual-machines-workload-intranet-sharepoint-overview.md) para ver todas las fases.

> [AZURE.NOTE]Estas instrucciones usan una imagen de SQL Server de la galería de imágenes de Azure y se le cargarán costes periódicos por el uso de la licencia de SQL Server. También es posible crear máquinas virtuales en Azure e instalar sus propias licencias de SQL Server, pero debe tener Software Assurance y License Mobility para usar su licencia de SQL Server en una máquina virtual, incluyendo una máquina virtual de Azure. Para obtener más información acerca de cómo instalar SQL Server en una máquina virtual, consulte [Instalación de SQL Server](https://msdn.microsoft.com/library/bb500469.aspx).

## Creación de máquinas virtuales de clúster de SQL Server en Azure

Hay dos máquinas virtuales de SQL Server. Un servidor SQL Server contiene la réplica de base de datos principal de un grupo de disponibilidad. El segundo servidor SQL Server contiene la réplica de copia de seguridad secundaria. La copia de seguridad se proporciona para garantizar una alta disponibilidad. Una máquina virtual adicional es para el nodo de la mayoría de clúster.

Utilice el siguiente bloque de comandos de PowerShell para crear las máquinas virtuales para los tres servidores. Especifique los valores de las variables quitando los caracteres < and >. Tenga en cuenta que este conjunto de comandos de PowerShell usa los valores de las siguientes tablas:

- Tabla M, para las máquinas virtuales
- Tabla V, para la configuración de red virtual
- Tabla S, para la subred
- Tabla ST, para las cuentas de almacenamiento
- Tabla A, para los conjuntos de disponibilidad

Recuerde que definió la Tabla M en [Fase 2: Configuración de controladores de dominio](virtual-machines-workload-intranet-sharepoint-phase2.md) y las Tablas V, S, ST y A en [Fase 1: Configuración de Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).

> [AZURE.NOTE]El siguiente comando establece el uso de Azure PowerShell 1.0 y versiones posteriores. Para más información, vea [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Cuando proporcione todos los valores adecuados, ejecute el bloque resultante en el símbolo del sistema de PowerShell de Azure.


	# Set up key variables
	$rgName="<your resource group name>"
	$locName="<Azure location of your resource group>"
	$saName="<Table ST – Item 1 – Storage account name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	$avName="<Table A – Item 2 – Availability set name column>"
	
	# Create the first SQL Server virtual machine
	$vmName="<Table M – Item 3 - Virtual machine name column>"
	$vmSize="<Table M – Item 3 - Minimum size column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for SQL data in GB>
	$diskLabel="<the label on the disk>"
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-SQLDataDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first SQL Server computer." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Enterprise -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second SQL Server virtual machine
	$vmName="<Table M – Item 4 - Virtual machine name column>"
	$vmSize="<Table M – Item 4 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for SQL data in GB>
	$diskLabel="<the label on the disk>"
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second SQL Server computer." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Enterprise -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the cluster majority node server
	$vmName="<Table M – Item 5 - Virtual machine name column>"
	$vmSize="<Table M – Item 5 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the cluster majority node server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE]Dado que estas máquinas virtuales son para una aplicación de intranet, no se les asigna una dirección IP pública o una etiqueta de nombre de dominio DNS ni se exponen a Internet. Sin embargo, esto significa también que no se puede conectar a ellas desde el Portal de Azure. El botón **Conectar** no estará disponible cuando vea las propiedades de la máquina virtual. Utilice el accesorio de conexión a Escritorio remoto u otra herramienta de Escritorio remoto para conectarse a la máquina virtual usando el nombre DNS de intranet o la dirección IP privada.

## Configuración de equipos de SQL Server

Para cada máquina virtual que ejecute SQL Server, use el cliente de escritorio remoto que elija y cree una conexión de escritorio remoto. Use el DNS de la intranet o el nombre del equipo y las credenciales de la cuenta de administrador local.

Para cada máquina virtual que ejecute SQL Server, únala al dominio de AD DS adecuado con estos comandos en el símbolo del sistema de Windows PowerShell.

	$domName="<AD DS domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

Tenga en cuenta que debe proporcionar credenciales de cuenta de dominio después de escribir el comando Add-Computer.

Cuando se reinicien, vuelva a conectarse a ellas con una cuenta que tenga privilegios de administrador local.

Para cada servidor SQL Server, haga lo siguiente:

1. Utilice el procedimiento [Inicio de sesión en una máquina virtual con una conexión a Escritorio remoto](virtual-machines-workload-intranet-sharepoint-phase2.md#logon) para iniciar sesión con las credenciales de la cuenta de administrador local.

2. Utilice el procedimiento [Para inicializar un disco vacío](virtual-machines-workload-intranet-sharepoint-phase2.md#datadisk) dos veces, una vez para cada servidor SQL Server para agregar el disco de datos adicional.

3. Ejecute los comandos siguientes en el símbolo del sistema de Windows PowerShell.

		md f:\Data
		md f:\Log
		md f:\Backup

4. Utilice el procedimiento [Para probar la conectividad](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn) para probar la conectividad en las ubicaciones de la red de la organización. Este procedimiento garantiza que la resolución de nombres DNS funciona correctamente (que la máquina virtual está configurada correctamente con los servidores DNS en la red virtual) y que se pueden enviar paquetes a la red virtual entre locales y desde ella.

Utilice el procedimiento siguiente dos veces, una vez para cada servidor SQL Server, con el fin de configurar el servidor SQL Server para usar la unidad F: en nuevas bases de datos y cuentas y permisos.

1.	En la pantalla de inicio, escriba **SQL Studio** y haga clic en **SQL Server 2014 Management Studio**.
2.	En **Conectar con el servidor**, haga clic en **Conectar**.
3.	En el panel izquierdo, haga clic con el botón secundario en el nodo superior (la instancia predeterminada con el nombre de la máquina) y haga clic en **Propiedades**.
4.	En **Propiedades del servidor**, haga clic en **Configuración de base de datos**.
5.	En **Ubicaciones predeterminadas de la base de datos**, establezca los siguientes valores:
- Para **Datos**, establezca la ruta de acceso en **f:\\Data**.
- Para **Registro**, establezca la ruta de acceso en **f:\\Log**.
- Para **Copia de seguridad**, establezca la ruta de acceso en **f:\\Backup**.
- solo las bases de datos nuevas utilizan estas ubicaciones.
6.	Haga clic en **Aceptar** para cerrar la ventana.
7.	En el panel izquierdo, expanda la **carpeta seguridad**.
8.	Haga clic con el botón derecho en **Inicios de sesión** y, a continuación, haga clic en **Nuevo inicio de sesión**.
9.	En **Nombre de inicio de sesión**, escriba *dominio*\\sp\_farm\_db en (en el que *dominio* es el nombre del dominio en el que se creó la cuenta de sp\_farm\_db).
10.	En **Seleccionar una página**, haga clic en **Roles de servidor**, en **sysadmin** y, a continuación, en **Aceptar**.
11.	Cierre SQL Server 2014 Management Studio.

Utilice el procedimiento siguiente dos veces, una vez para cada servidor SQL Server, para permitir conexiones a Escritorio remoto con la cuenta de sp\_farm\_db.

1.	En la pantalla de inicio, haga clic en **Este PC** y, a continuación, haga clic en **Propiedades**.
2.	En la ventana **Sistema**, haga clic en **Configuración remota**.
3.	En la sección **Escritorio remoto**, haga clic en **Seleccionar usuarios** y, a continuación, haga clic en **Agregar**.
4.	En **Escribir los nombres de objeto para seleccionar**, escriba [dominio]**\\sp\_farm\_db** y luego haga clic en **Aceptar** tres veces.

SQL Server requiere un puerto que los clientes utilizan para tener acceso al servidor de base de datos. También necesita puertos para conectar con SQL Server Management Studio y para administrar el grupo de alta disponibilidad. A continuación, ejecute el siguiente comando en un símbolo del sistema de Windows PowerShell de nivel de administrador dos veces, una vez para cada servidor SQL Server, para agregar una regla de firewall que permita el tráfico entrante al servidor SQL Server.

	New-NetFirewallRule -DisplayName "SQL Server ports 1433, 1434, and 5022" -Direction Inbound –Protocol TCP –LocalPort 1433,1434,5022 -Action Allow

Para cada una de las máquinas virtuales de servidor SQL Server, cierre la sesión como administrador local.

Para obtener más información acerca de la optimización del rendimiento de SQL Server en Azure, consulte [Prácticas recomendadas de rendimiento para SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-performance-best-practices.md). También puede deshabilitar el almacenamiento de redundancia geográfica (GRS) para la cuenta de almacenamiento de la granja de servidores de SharePoint y usar espacios de almacenamiento para optimizar la tasa IOPS.

## Configuración del servidor de nodos de mayoría de clúster

Utilice el procedimiento [Inicio de sesión en una máquina virtual con una conexión a Escritorio remoto](virtual-machines-workload-intranet-sharepoint-phase2.md#logon) para que el inicio de sesión del nodo de mayoría de clúster con las credenciales de la cuenta de administrador local.

En el nodo de mayoría de clúster, use el procedimiento [Para probar la conectividad](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn) para probar la conectividad en las ubicaciones de la red de la organización.

## Creación del clúster de servidores de Windows

Los grupos de disponibilidad AlwaysOn de SQL Server se basan en la característica de clústeres de conmutación por error de Windows Server (WSFC) de Windows Server. La característica permite a varias máquinas participar como un grupo en un clúster. Cuando se produce un error en una máquina, una segunda máquina está lista para ocupar su lugar. Por lo tanto, la primera tarea es habilitar la característica de clúster de conmutación por error en todas las máquinas que participan, que incluyen:

- El servidor SQL Server principal
- El servidor SQL Server secundario
- El nodo de mayoría de clúster

El clúster de conmutación por error requiere al menos tres máquinas virtuales. Dos de ellas hospedan SQL Server. La segunda VM de SQL Server es una réplica secundaria sincrónica que garantiza una pérdida de datos cero si se produce un error en la máquina principal. La tercera máquina no necesita hospedar SQL Server. El nodo de mayoría de clúster proporciona un cuórum en el WSFC. Dado que el clúster de WSFC se basa en un quórum para supervisar el estado, siempre debe haber una mayoría para asegurarse de que el clúster de WSFC está en línea. Si solo dos máquinas están en un clúster, y se produce un error en una, no puede haber ninguna mayoría cuando solo una de cada dos falla. Para obtener más información, consulte [Configuración de votación y modos de quórum de WSFC (SQL Server)](http://msdn.microsoft.com/library/hh270280.aspx).

Para ambos equipos de SQL Server y el nodo de la mayoría de clúster, ejecute el siguiente comando en un símbolo del sistema de Windows PowerShell de nivel de administrador.

	Install-WindowsFeature Failover-Clustering -IncludeManagementTools

Debido al comportamiento compatible que no es RFC actual por DHCP en Azure, puede producirse errores de creación de un clúster de conmutación por error de Windows Server (WSFC). Para obtener más información, busque "Comportamiento de clúster de WSFC en redes de Azure" de alta disponibilidad y recuperación ante desastres para SQL Server en Máquinas virtuales de Azure. Sin embargo, hay una solución alternativa. Para crear el clúster, siga estos pasos.

1.	Inicie sesión en la máquina virtual principal de SQL Server con la cuenta de **sp\_install**.
2.	Desde la pantalla de inicio, escriba **Conmutación por error** y, a continuación, haga clic en **Administrador de clústeres de conmutación por error**.
3.	En el panel izquierdo, haga clic con el botón derecho en **Administrador de clústeres de conmutación por error** y, a continuación, haga clic en **Crear clúster**.
4.	En la página Antes de comenzar, haga clic en **Siguiente**.
5.	En la página Seleccionar servidores, escriba el nombre de la máquina principal de SQL Server, haga clic en **Agregar** y, a continuación, haga clic en **Siguiente**.
6.	En la página de advertencia de validación, haga clic en **No. No necesito compatibilidad con Microsoft para este clúster y por tanto no deseo ejecutar las pruebas de validación. Al hacer clic en Siguiente, continúe con la creación del clúster** y luego haga clic en **Siguiente**.
7.	En la página Punto de acceso para administrar el clúster, en el cuadro de texto **Nombre del clúster**, escriba el nombre del clúster y haga clic en **Siguiente**.
8.	En la página de confirmación, haga clic en **Siguiente** para iniciar la creación del clúster.
9.	En la página de resumen, haga clic en **Finalizar**.
10.	En el panel izquierdo, haga clic en el nuevo clúster. En la sección **Recursos principales de clúster** del panel de contenido, abra el nombre del clúster de servidor. El recurso **Dirección IP** aparece en el estado **Error**. No se puede poner en conexión el recurso de dirección IP porque el clúster tiene asignada la misma dirección IP que el de la propia máquina. El resultado es una dirección duplicada.
11.	Haga clic con el botón secundario en el recurso **dirección IP** erróneo y, a continuación, haga clic en **Propiedades**.
12.	En el cuadro de diálogo **Propiedades de la dirección IP**, haga clic en **Dirección IP estática**.
13.	Escriba una dirección IP no usada en el intervalo de direcciones correspondiente a la subred donde se encuentra el servidor SQL Server y, a continuación, haga clic en **Aceptar**.
14.	Haga clic con el botón secundario en el recurso Dirección IP erróneo y, a continuación, haga clic en **Conectar**. Espere hasta que los recursos estén en línea. Cuando el recurso de nombre de clúster está en línea, actualiza el controlador de dominio con una cuenta de equipo de Active Directory (AD). Esta cuenta de AD se utiliza posteriormente para ejecutar el servicio de clúster del grupo de disponibilidad.
15.	Una vez creada la cuenta de AD, ponga el nombre de clúster sin conexión. Haga clic con el botón secundario en el nombre del clúster en **Recursos principales de clúster** y, a continuación, haga clic en **Poner sin conexión**.
16.	Para quitar la dirección IP del clúster, haga clic con el botón derecho en **Dirección IP**, haga clic en **Quitar** y, a continuación, haga clic en **Sí** cuando se le solicite. El recurso de clúster ya no puede estar en línea ya que depende el recurso de dirección IP. Sin embargo, un grupo de disponibilidad no depende de la dirección IP o el nombre del clúster para que funcione correctamente. Por lo tanto, el nombre de clúster puede dejarse sin conexión.
17.	Para agregar los nodos restantes en el clúster, haga clic con el botón secundario en el nombre del clúster en el panel izquierdo y luego haga clic en **Agregar nodo**.
18.	En la página Antes de comenzar, haga clic en **Siguiente**.
19.	En la página Seleccionar servidores, escriba el nombre y, a continuación, haga clic en **Agregar** para agregar el servidor SQL secundario y el nodo de mayoría de clúster en el clúster. Después de agregar los dos equipos, haga clic en **Siguiente**. Si no se puede agregar una máquina y el mensaje de error es "el Registro remoto no se está ejecutando", haga lo siguiente. Inicie sesión en la máquina, abra el complemento Servicios (services.msc) y habilite el Registro remoto. Para obtener más información, consulte [No se puede conectar al servicio de Registro remoto](http://technet.microsoft.com/library/bb266998.aspx).
20.	En la página de advertencia de validación, haga clic en **No. No necesito compatibilidad con Microsoft para este clúster y por tanto no deseo ejecutar las pruebas de validación. Al hacer clic en Siguiente, continúe con la creación del clúster** y luego haga clic en **Siguiente**.
21.	En la página de confirmación, haga clic en **Siguiente**.
22.	En la página de resumen, haga clic en **Finalizar**.
23.	En el panel izquierdo, haga clic en **Nodos**. Debería ver los tres equipos enumerados.

## Habilitación de grupos de disponibilidad AlwaysOn

El siguiente paso es habilitar los grupos de disponibilidad AlwaysOn con el Administrador de configuración de SQL Server. Tenga en cuenta que un grupo de disponibilidad en SQL Server difiere de un conjunto de disponibilidad de Azure. Un grupo de disponibilidad contiene bases de datos recuperables y con una alta disponibilidad. Un conjunto de disponibilidad de Azure asigna las máquinas virtuales en distintos dominios de error. Para obtener más información sobre dominios erróneos, consulte [Administración de la disponibilidad de las máquinas virtuales](virtual-machines-manage-availability.md).

Utilice estos pasos para habilitar grupos de disponibilidad AlwaysOn en SQL Server.

1.	Inicie sesión en el servidor SQL Server principal mediante la cuenta de **sp\_farm\_db** o alguna otra cuenta que tenga el rol de servidor de sysadmin en el servidor SQL Server.
2.	En la pantalla de inicio, escriba **Configuración de SQL Server** y, a continuación, haga clic en **Administrador de configuración de SQL Server**.
3.	En el panel izquierdo, haga clic en **Servicios de SQL Server**.
4.	En el panel de contenido, haga doble clic en **SQL Server (MSSQLSERVER)**.
5.	En **Propiedades de SQL Server (MSSQLSERVER)**, haga clic en la pestaña **Alta disponibilidad AlwaysOn**, seleccione **Habilitar los grupos de disponibilidad de AlwaysOn**, haga clic en **Aplicar** y luego haga clic en **Aceptar** cuando se le solicite. No cierre todavía la ventana Propiedades.
6.	Haga clic en la pestaña virtual-machines-manage-availability y escriba [Dominio]**\\sqlservice** en **Nombre de cuenta**. Escriba la contraseña de cuenta de sqlservice en **Contraseña** y **Confirmar contraseña** y luego haga clic en **Aceptar**.
7.	En la ventana de mensaje, haga clic en **Sí** para reiniciar el servicio de SQL Server.
8.	Inicie sesión en el servidor secundario de SQL y repita este proceso.

En el siguiente diagrama se muestra la configuración resultante de la realización correcta de esta fase con los nombres de equipo de marcador de posición.

![](./media/virtual-machines-workload-intranet-sharepoint-phase3/workload-spsqlao_03.png)

## Paso siguiente

- Para continuar con la configuración de esta carga de trabajo, vaya a la [Fase 4](virtual-machines-workload-intranet-sharepoint-phase4.md).

<!---HONumber=AcomDC_1217_2015-->