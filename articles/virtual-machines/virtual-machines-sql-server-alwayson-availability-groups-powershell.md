<properties
	pageTitle="Configuración de Grupos de disponibilidad AlwaysOn en una máquina virtual de Azure | Microsoft Azure"
	description="En este tutorial se usan los recursos creados con el modelo de implementación clásica, y se usa PowerShell para crear un grupo de disponibilidad AlwaysOn en Azure."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="12/04/2015"
	ms.author="jroth" />

# Configuración de Grupos de disponibilidad AlwaysOn en la máquina virtual de Azure (PowerShell)

> [AZURE.SELECTOR]
- [Portal - Resource Manager](virtual-machines-sql-server-alwayson-availability-groups-gui-arm.md)
- [Portal - Classic](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [PowerShell - Classic](virtual-machines-sql-server-alwayson-availability-groups-powershell.md)

<br/>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


Las máquinas virtuales (VM) de Azure pueden ayudar a los administradores de bases de datos a reducir el costo de un sistema de alta disponibilidad de SQL Server Este tutorial muestra cómo implementar un grupo de disponibilidad mediante SQL Server AlwaysOn de extremo a extremo dentro de un entorno de Azure. Al final del tutorial, la solución SQL Server AlwaysOn en Azure constará de los siguientes elementos:

- Una red virtual que contiene varias subredes, incluida una subred front-end y back-end

- Un controlador de dominio con un dominio de Active Directory (AD)

- Dos máquinas virtuales de SQL Server implementadas en la subred back-end y unidas al dominio de AD

- Un clúster WSFC de tres nodos con el modelo de cuórum Mayoría de nodo

- Un grupo de disponibilidad con dos réplicas de confirmación sincrónica de una base de datos de disponibilidad

Este escenario se elige por simplicidad, no para rentabilizar los costos, ni por cualquier otro de los factores en Azure. Por ejemplo, puede minimizar el número de máquinas virtuales para un grupo de disponibilidad de dos réplicas para ahorrar horas de cálculo en Azure con el controlador de dominio como el testigo de recurso compartido de archivos de cuórum en un clúster WSFC de dos nodos. Este método reduce el recuento de máquinas virtuales en uno respecto a la configuración anterior.

Este tutorial está concebido para mostrarle los pasos necesarios para configurar la solución descrita anteriormente sin profundizar en los detalles de cada paso. Por tanto, en lugar de mostrar los pasos de configuración de la interfaz gráfica de usuario (GUI), usa el script de PowerShell para llevarle rápidamente a través de cada paso. Se da por hecho lo siguiente:

- Ya tiene una cuenta de Azure con la suscripción de la máquina virtual.

- Tiene instalados los [cmdlets de Azure PowerShell](..\powershell-install-configure.md).

- Ya sabe cómo aprovisionar una máquina virtual de SQL Server desde la galería de máquina virtual mediante la interfaz gráfica de usuario (GUI). Para obtener más información, consulte [Aprovisionamiento de una máquina virtual de SQL Server en Azure](virtual-machines-provision-sql-server.md).

- Ya tiene un conocimiento sólido de grupos de disponibilidad AlwaysOn para soluciones locales. Para obtener más información, consulte [Grupos de disponibilidad AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## Conexión a la suscripción de Azure y creación de la Red virtual

1. En una ventana de PowerShell del equipo local, importe el módulo de Azure, descargue un archivo de configuración de publicación en el equipo y conecte la sesión de PowerShell a su suscripción de Azure importando la configuración de publicación descargada.

		Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
		Get-AzurePublishSettingsFile
		Import-AzurePublishSettingsFile <publishsettingsfilepath>

	El comando **AzurePublishgSettingsFile Get** genera automáticamente un certificado de administración con las descargas de Azure en el equipo. Se abrirá un explorador automáticamente y se le solicitará que escriba las credenciales de la cuenta de Microsoft para su suscripción de Azure. El archivo descargado **.publishsettings** contiene toda la información que necesita para administrar la suscripción de Azure. Después de guardar este archivo en un directorio local, impórtelo mediante el comando **Import-AzurePublishSettingsFile**.

	>[AZURE.NOTE] El archivo .publishsettings contiene sus credenciales (sin codificar) que se usan para administrar sus suscripciones y servicios de Azure. El procedimiento recomendado para este archivo consiste en almacenarlo temporalmente fuera de los directorios de origen (por ejemplo en la carpeta Bibliotecas\\Documentos) y, a continuación, eliminarlo cuando la importación se haya completado. Un usuario malintencionado que obtuviera acceso al archivo .publishsettings podría modificar, crear y eliminar sus servicios de Azure.

1. Defina una serie de variables que usará para crear la infraestructura de TI en la nube.

		$location = "West US"
		$affinityGroupName = "ContosoAG"
		$affinityGroupDescription = "Contoso SQL HADR Affinity Group"
		$affinityGroupLabel = "IaaS BI Affinity Group"
		$networkConfigPath = "C:\scripts\Network.netcfg"
		$virtualNetworkName = "ContosoNET"
		$storageAccountName = "<uniquestorageaccountname>"
		$storageAccountLabel = "Contoso SQL HADR Storage Account"
		$storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
		$winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
		$sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
		$dcServerName = "ContosoDC"
		$dcServiceName = "<uniqueservicename>"
		$availabilitySetName = "SQLHADR"
		$vmAdminUser = "AzureAdmin"
		$vmAdminPassword = "Contoso!000"
		$workingDir = "c:\scripts"

	Preste atención a lo siguiente para asegurarse de que los comandos se ejecutarán correctamente después:

	- Las variables **$storageAccountName** y **$dcServiceName** tienen que ser únicas porque se usan para identificar la cuenta de almacenamiento en la nube y el servidor en la nube, respectivamente, en Internet.

	- Los nombres especificados para las variables **$affinityGroupName** y **$virtualNetworkName** se configuran en el documento de configuración de red virtual que usará más adelante.

	- **$sqlImageName** especifica el nuevo nombre de la imagen de máquina virtual que contiene SQL Server 2012 Service Pack 1 Enterprise Edition.

	- Para mayor simplicidad, **Contoso!000** es la misma contraseña que se usa en todo el tutorial.

1. Cree un grupo de afinidad.

		New-AzureAffinityGroup `
			-Name $affinityGroupName `
			-Location $location `
			-Description $affinityGroupDescription `
			-Label $affinityGroupLabel

1. Cree una red virtual importando un archivo de configuración.

		Set-AzureVNetConfig `
			-ConfigurationPath $networkConfigPath

	El archivo de configuración contiene el siguiente documento XML. En resumen, especifica una red virtual denominada **ContosoNET** en el grupo de afinidad denominado **ContosoAG**, y tiene el espacio de direcciones **10.10.0.0/16** y dos subredes, **10.10.1.0/24** y **10.10.2.0/24**, que son la subred front-end y back-end, respectivamente. La subred front-end es donde puede colocar las aplicaciones cliente como Microsoft SharePoint, y la subred back es donde colocará las máquinas virtuales de SQL Server. Si cambia las variables **$affinityGroupName** y **$virtualNetworkName** anteriores, también tiene que cambiar los nombres correspondientes a continuación.

		<NetworkConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
		  <VirtualNetworkConfiguration>
		    <Dns />
		    <VirtualNetworkSites>
		      <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
		        <AddressSpace>
		          <AddressPrefix>10.10.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <Subnets>
		          <Subnet name="Front">
		            <AddressPrefix>10.10.1.0/24</AddressPrefix>
		          </Subnet>
		          <Subnet name="Back">
		            <AddressPrefix>10.10.2.0/24</AddressPrefix>
		          </Subnet>
		        </Subnets>
		      </VirtualNetworkSite>
		    </VirtualNetworkSites>
		  </VirtualNetworkConfiguration>
		</NetworkConfiguration>

1. Cree una cuenta de almacenamiento que está asociada con el grupo de afinidad que creó y establézcala como cuenta de almacenamiento actual en su suscripción.

		New-AzureStorageAccount `
			-StorageAccountName $storageAccountName `
			-Label $storageAccountLabel `
			-AffinityGroup $affinityGroupName
		Set-AzureSubscription `
			-SubscriptionName (Get-AzureSubscription).SubscriptionName `
			-CurrentStorageAccount $storageAccountName

1. Cree el servidor DC en el nuevo servicio en la nube y conjunto de disponibilidad.

		New-AzureVMConfig `
			-Name $dcServerName `
			-InstanceSize Medium `
			-ImageName $winImageName `
			-MediaLocation "$storageAccountContainer$dcServerName.vhd" `
			-DiskLabel "OS" |
			Add-AzureProvisioningConfig `
				-Windows `
				-DisableAutomaticUpdates `
				-AdminUserName $vmAdminUser `
				-Password $vmAdminPassword |
				New-AzureVM `
					-ServiceName $dcServiceName `
					–AffinityGroup $affinityGroupName `
					-VNetName $virtualNetworkName

	Esta serie de comandos canalizados hacen lo siguiente:

	- **New-AzureVMConfig** crea una configuración de máquina virtual.

	- **Add-AzureProvisioningConfig** proporciona los parámetros de configuración de un servidor de Windows independiente.

	- **Add-AzureDataDisk** agrega el disco de datos que usará para almacenar datos de Active Directory, con la opción establecida en None.

	- **New-AzureVM** crea un nuevo servicio en la nube y una nueva máquina virtual de Azure en el nuevo servicio en la nube.

1. Espere a que la nueva máquina virtual esté completamente aprovisionada y descargue el archivo de escritorio remoto en el directorio de trabajo. Puesto que la nueva máquina virtual de Azure tarda mucho tiempo en aprovisionarse, el bucle continúa sondeando la nueva máquina virtual hasta que esté lista para su uso.

		$VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

		While ($VMStatus.InstanceStatus -ne "ReadyRole")
		{
		    write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
		    Start-Sleep -Seconds 15
		    $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
		}

		Get-AzureRemoteDesktopFile `
		    -ServiceName $dcServiceName `
		    -Name $dcServerName `
		    -LocalPath "$workingDir$dcServerName.rdp"

El servidor DC ya está aprovisionado correctamente. A continuación, configurará el dominio de Active Directory en este servidor DC. Deje abierta la ventana de PowerShell en el equipo local. Se usará más adelante para crear las dos máquinas virtuales de SQL Server.

## Configuración del controlador de dominio

1. Conéctese al servidor DC iniciando el archivo de escritorio remoto. Use el nombre de usuario AzureAdmin y la contraseña del administrador del equipo **Contoso!000**, que especificó al crear la nueva máquina virtual.

1. Abra una ventana de Azure PowerShell en modo de administrador.

1. Ejecute el siguiente comando **DCPROMO. EXE** para configurar el dominio **corp.contoso.com** con los directorios de datos en la unidad M.

		dcpromo.exe `
			/unattend `
			/ReplicaOrNewDomain:Domain `
			/NewDomain:Forest `
			/NewDomainDNSName:corp.contoso.com `
			/ForestLevel:4 `
			/DomainNetbiosName:CORP `
			/DomainLevel:4 `
			/InstallDNS:Yes `
			/ConfirmGc:Yes `
			/CreateDNSDelegation:No `
			/DatabasePath:"C:\Windows\NTDS" `
			/LogPath:"C:\Windows\NTDS" `
			/SYSVOLPath:"C:\Windows\SYSVOL" `
			/SafeModeAdminPassword:"Contoso!000"

	Una vez completado el comando, la máquina virtual se reinicia automáticamente.

1. Conéctese de nuevo al servidor DC iniciando el archivo de escritorio remoto. Esta vez, inicie sesión como **CORP\\Administrador**.

1. Abra una ventana de PowerShell en modo de administrador e importe el módulo Active Directory para PowerShell mediante el comando siguiente:

		Import-Module ActiveDirectory

1. Ejecute los comandos siguientes para agregar tres usuarios al dominio.

		$pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
		New-ADUser `
			-Name 'Install' `
			-AccountPassword  $pwd `
			-PasswordNeverExpires $true `
			-ChangePasswordAtLogon $false `
			-Enabled $true
		New-ADUser `
			-Name 'SQLSvc1' `
			-AccountPassword  $pwd `
			-PasswordNeverExpires $true `
			-ChangePasswordAtLogon $false `
			-Enabled $true
		New-ADUser `
			-Name 'SQLSvc2' `
			-AccountPassword  $pwd `
			-PasswordNeverExpires $true `
			-ChangePasswordAtLogon $false `
			-Enabled $true

	**CORP\\Install** se usa para configurar todo lo relacionado con las instancias de servicio de SQL Server, el clúster de WSFC y el grupo de disponibilidad. **CORP\\SQLSvc1** y **CORP\\SQLSvc2** se usan como cuentas de servicio de SQL Server para las dos máquinas virtuales de SQL Server.

1. A continuación, ejecute los comandos siguientes para proporcionar **CORP\\Install** los permisos para crear objetos de equipo en el dominio.

		Cd ad:
		$sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
		$guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
		$ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
		$corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
		$acl = Get-Acl $corp
		$acl.AddAccessRule($ace1)
		Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

	El GUID especificado anteriormente es el GUID para el tipo de objeto del equipo. La cuenta **CORP\\Install** necesita los permisos **Leer todas las propiedades** y **Crear objeto de equipo** para crear los objetos de Active Directory para el clúster WSFC. El permiso **Leer todas las propiedades** ya se concede a CORP\\Install de forma predeterminada, por lo que no es necesario concederlo explícitamente. Para obtener más información sobre los permisos necesarios para crear el clúster WSFC, consulte [Guía paso a paso de clústeres de conmutación por error: configurar cuentas en Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

	Ahora que ha terminado de configurar Active Directory y los objetos de usuario, creará dos máquinas virtuales de SQL Server y las unirá a este dominio.

## Creación de las máquinas virtuales de SQL Server

1. Siga usando la ventana de PowerShell que está abierta en el equipo local. Defina las variables adicionales siguientes:

		$domainName= "corp"
		$FQDN = "corp.contoso.com"
		$subnetName = "Back"
		$sqlServiceName = "<uniqueservicename>"
		$quorumServerName = "ContosoQuorum"
		$sql1ServerName = "ContosoSQL1"
		$sql2ServerName = "ContosoSQL2"
		$availabilitySetName = "SQLHADR"
		$dataDiskSize = 100
		$dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"

	La dirección IP **10.10.0.4** normalmente se asigna a la primera máquina virtual que se crea en la subred **10.10.0.0/16** de la red virtual de Azure. Debe comprobar que esta es la dirección del servidor DC mediante la ejecución de **IPCONFIG**.

1. Ejecute los siguientes comandos canalizados para crear la primera máquina virtual del clúster de WSFC, denominado **ContosoQuorum**:

		New-AzureVMConfig `
			-Name $quorumServerName `
			-InstanceSize Medium `
			-ImageName $winImageName `
			-MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
			-AvailabilitySetName $availabilitySetName `
			-DiskLabel "OS" |
			Add-AzureProvisioningConfig `
				-WindowsDomain `
				-AdminUserName $vmAdminUser `
				-Password $vmAdminPassword `
				-DisableAutomaticUpdates `
				-Domain $domainName `
				-JoinDomain $FQDN `
				-DomainUserName $vmAdminUser `
				-DomainPassword $vmAdminPassword |
				Set-AzureSubnet `
					-SubnetNames $subnetName |
					New-AzureVM `
						-ServiceName $sqlServiceName `
						–AffinityGroup $affinityGroupName `
						-VNetName $virtualNetworkName `
						-DnsSettings $dnsSettings

	Tenga en cuenta lo siguiente con respecto al comando anterior:

	- **New-AzureVMConfig** crea una configuración de máquina virtual con el nombre del conjunto de disponibilidad deseado. Las máquinas virtuales subsiguientes se crean con el mismo nombre de conjunto de disponibilidad para que se unan al mismo conjunto de disponibilidad.

	- **Add-AzureProvisioningConfig** une la máquina virtual al dominio de Active Directory local que creó.

	- **Set-AzureSubnet** coloca la máquina virtual en la subred back-end.

	- **New-AzureVM** crea un nuevo servicio en la nube y una nueva máquina virtual de Azure en el nuevo servicio de nube. El parámetro **DnsSettings** especifica que el servidor DNS para los servidores en el nuevo servicio en la nube tiene la dirección IP **10.10.0.4**, que es la dirección IP del servidor DC. Este parámetro es necesario para que las nuevas máquinas virtuales en el servicio en la nube se unan al dominio de Active Directory correctamente. Sin este parámetro, debe establecer manualmente los valores de IPv4 en la máquina virtual para que use el servidor DC como servidor DNS principal después de aprovisionar la máquina virtual y, a continuación, unir la máquina virtual al dominio de Active Directory.

1. Ejecute los siguientes comandos canalizados para crear las máquinas virtuales de SQL Server, denominadas **ContosoSQL1** y **ContosoSQL2**.

		# Create ContosoSQL1...
		New-AzureVMConfig `
		    -Name $sql1ServerName `
		    -InstanceSize Large `
		    -ImageName $sqlImageName `
		    -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
		    -AvailabilitySetName $availabilitySetName `
		    -HostCaching "ReadOnly" `
		    -DiskLabel "OS" |
		    Add-AzureProvisioningConfig `
		        -WindowsDomain `
		        -AdminUserName $vmAdminUser `
		        -Password $vmAdminPassword `
		        -DisableAutomaticUpdates `
		        -Domain $domainName `
		        -JoinDomain $FQDN `
		        -DomainUserName $vmAdminUser `
		        -DomainPassword $vmAdminPassword |
		        Set-AzureSubnet `
		            -SubnetNames $subnetName |
		            Add-AzureEndpoint `
		                -Name "SQL" `
		                -Protocol "tcp" `
		                -PublicPort 1 `
		                -LocalPort 1433 |
		                New-AzureVM `
		                    -ServiceName $sqlServiceName

		# Create ContosoSQL2...
		New-AzureVMConfig `
		    -Name $sql2ServerName `
		    -InstanceSize Large `
		    -ImageName $sqlImageName `
		    -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
		    -AvailabilitySetName $availabilitySetName `
		    -HostCaching "ReadOnly" `
		    -DiskLabel "OS" |
		    Add-AzureProvisioningConfig `
		        -WindowsDomain `
		        -AdminUserName $vmAdminUser `
		        -Password $vmAdminPassword `
		        -DisableAutomaticUpdates `
		        -Domain $domainName `
		        -JoinDomain $FQDN `
		        -DomainUserName $vmAdminUser `
		        -DomainPassword $vmAdminPassword |
		        Set-AzureSubnet `
		            -SubnetNames $subnetName |
		            Add-AzureEndpoint `
		                -Name "SQL" `
		                -Protocol "tcp" `
		                -PublicPort 2 `
		                -LocalPort 1433 |
		                New-AzureVM `
		                    -ServiceName $sqlServiceName

	Tenga en cuenta lo siguiente con respecto a los comandos anteriores:

	- **New-AzureVMConfig** usa el mismo nombre de conjunto de disponibilidad que el servidor DC y usa la imagen de SQL Server 2012 Service Pack 1 Enterprise Edition en la galería de máquina virtual. También establece el disco del sistema operativo en solo Caching de lectura (sin Caching de escritura). Se recomienda migrar los archivos de base de datos a un disco de datos independiente que adjunte a la máquina virtual y configure sin Caching de lectura o de escritura. Sin embargo, lo mejor es quitar el servicio Caching de escritura en el disco del sistema operativo, ya que no se puede quitar el Caching de lectura en el disco del sistema operativo.

	- **Add-AzureProvisioningConfig** une la máquina virtual al dominio de Active Directory local que creó.

	- **Set-AzureSubnet** coloca la máquina virtual en la subred back-end.

	- **Add-AzureEndpoint** agrega extremos de acceso para que las aplicaciones cliente puedan tener acceso a estas instancias de servicios de SQL Server en Internet. Se proporcionan puertos diferentes para ContosoSQL1 y ContosoSQL2.

	- **New-AzureVM** crea la nueva máquina virtual de SQL Server en el mismo servicio en la nube que ContosoQuorum. Si desea que las máquinas virtuales estén en el mismo conjunto de disponibilidad, tiene que colocarlas en el mismo servicio en la nube.

1. Espere a que cada máquina virtual esté completamente aprovisionada y descargue su archivo de escritorio remoto en el directorio de trabajo. El bucle for recorre las tres nuevas máquinas virtuales y ejecuta los comandos dentro de las llaves de nivel superior para cada uno de ellos.

		Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
		{
		    write-host "Waiting for " $VM.Name "..."

		    # Loop until the VM status is "ReadyRole"
		    While ($VM.InstanceStatus -ne "ReadyRole")
		    {
		        write-host "  Current Status = " $VM.InstanceStatus
		        Start-Sleep -Seconds 15
		        $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
		    }

		    write-host "  Current Status = " $VM.InstanceStatus

		    # Download remote desktop file
		    Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
		}

	Las máquinas virtuales de SQL Server están aprovisionadas y en ejecución, pero se instalan con SQL Server con las opciones predeterminadas.

## Inicialización de las máquinas virtuales de clúster WSFC

En esta sección, deberá modificar los tres servidores que usará en el clúster de WSFC y la instalación de SQL Server. Concretamente:

- (Todos los servidores) Tiene que instalar la característica **Failover Clustering**.

- (Todos los servidores) Tiene que agregar **CORP\\Install** como **administrador** de la máquina.

- (ContosoSQL1 y ContosoSQL2 solamente) Tendrá que agregar **CORP\\Install** como un rol de **sysadmin** en la base de datos predeterminada.

- (ContosoSQL1 y ContosoSQL2 solamente) Tendrá que agregar **NT AUTHORITY\\System** como inicio de sesión con los permisos siguientes:

	- Modificar cualquier grupo de disponibilidad

	- Conectar SQL

	- Ver estado del servidor

- (Solo ContosoSQL1 y ContosoSQL2) El protocolo **TCP** ya está habilitado en la máquina virtual de SQL Server. Sin embargo, tendrá que abrir el firewall para el acceso remoto de SQL Server.

De este modo, estará listo para la empezar. A partir de **ContosoQuorum**, siga los pasos que se indican a continuación:

1. Conéctese a **ContosoQuorum** iniciando los archivos de escritorio remoto. Use el nombre de usuario **AzureAdmin** y la contraseña **Contoso!000** del administrador del equipo que especificó al crear las máquinas virtuales.

1. Compruebe que los equipos se han unido correctamente a **corp.contoso.com**.

1. Espere a que la instalación de SQL Server termine de ejecutar las tareas automatizadas de inicialización antes de continuar .

1. Abra una ventana de Azure PowerShell en modo de administrador.

1. Instale la característica de Windows Clúster de conmutación por error.

		Import-Module ServerManager
		Add-WindowsFeature Failover-Clustering

1. Agregue **CORP\\Install** como administrador local.

		net localgroup administrators "CORP\Install" /Add

1. Cierre sesión en ContosoQuorum. Ya ha terminado con este servidor.

		logoff.exe

A continuación, inicialice **ContosoSQL1** y **ContosoSQL2**. Siga los pasos a continuación, que son idénticos para ambas máquinas virtuales de SQL Server.

1. Conéctese a las dos máquinas virtuales de SQL Server a través del inicio de los archivos de escritorio remoto. Use el nombre de usuario **AzureAdmin** y la contraseña **Contoso!000** del administrador del equipo que especificó al crear las máquinas virtuales.

1. Compruebe que los equipos se han unido correctamente a **corp.contoso.com**.

1. Espere a que la instalación de SQL Server termine de ejecutar las tareas automatizadas de inicialización antes de continuar .

1. Abra una ventana de Azure PowerShell en modo de administrador.

1. Instale la característica de Windows Clúster de conmutación por error.

		Import-Module ServerManager
		Add-WindowsFeature Failover-Clustering

1. Agregue **CORP\\Install** como administrador local

		net localgroup administrators "CORP\Install" /Add

1. Importe el proveedor de PowerShell SQL Server.

		Set-ExecutionPolicy -Execution RemoteSigned -Force
		Import-Module -Name "sqlps" -DisableNameChecking

1. Agregue **CORP\\Install** como el rol sysadmin para la instancia predeterminada de SQL Server.

		net localgroup administrators "CORP\Install" /Add
		Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."

1. Agregue **NT AUTHORITY\\System** como inicio de sesión con los tres permisos anteriormente descritos.

		Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
		Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
		Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
		Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."

1. Abra el firewall para el acceso remoto de SQL Server.

		netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP

1. Cierre sesión en ambas máquinas virtuales.

		logoff.exe

Finalmente, está listo para configurar el grupo de disponibilidad. Se usará el proveedor de PowerShell de SQL Server para realizar todo el trabajo en **ContosoSQL1**.

## Configuración del recurso de grupo de disponibilidad

1. Conéctese de nuevo a **ContosoSQL1** iniciando los archivos de escritorio remoto. En lugar de iniciar sesión con la cuenta de la máquina, inicie sesión con **CORP\\Install**.

1. Abra una ventana de Azure PowerShell en modo de administrador.

1. Defina las siguientes variables:

		$server1 = "ContosoSQL1"
		$server2 = "ContosoSQL2"
		$serverQuorum = "ContosoQuorum"
		$acct1 = "CORP\SQLSvc1"
		$acct2 = "CORP\SQLSvc2"
		$password = "Contoso!000"
		$clusterName = "Cluster1"
		$timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
		$db = "MyDB1"
		$backupShare = "\\$server1\backup"
		$quorumShare = "\\$server1\quorum"
		$ag = "AG1"

1. Importe el proveedor PowerShell de SQL Server.

		Set-ExecutionPolicy RemoteSigned -Force
		Import-Module "sqlps" -DisableNameChecking

1. Cambie la cuenta de servicio de SQL Server para ContosoSQL1 a CORP\\SQLSvc1.

		$wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
		$wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
		$svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
		$svc1.Stop()
		$svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
		$svc1.Start();
		$svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Cambie la cuenta de servicio de SQL Server para ContosoSQL2 a CORP\\SQLSvc2.

		$wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
		$wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
		$svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
		$svc2.Stop()
		$svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
		$svc2.Start();
		$svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Descargue **CreateAzureFailoverCluster.ps1** desde [Creación del clúster de WSFC para grupos de disponibilidad AlwaysOn en la máquina virtual de Azure](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) en el directorio de trabajo local. Usará este script para ayudarle a crear un clúster funcional de WSFC. Para obtener información importante sobre cómo WSFC interactúa con la red de Azure, consulte [Alta disponibilidad y recuperación ante desastres para SQL Server en Máquinas virtuales de Azure](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).

1. Cambie al directorio de trabajo y cree el clúster de WSFC con el script descargado.

		Set-ExecutionPolicy Unrestricted -Force
		.\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"

1. Habilite los grupos de disponibilidad AlwaysOn para las instancias predeterminadas de SQL Server en **ContosoSQL1** y **ContosoSQL2**.

		Enable-SqlAlwaysOn `
		    -Path SQLSERVER:\SQL\$server1\Default `
		    -Force
		Enable-SqlAlwaysOn `
		    -Path SQLSERVER:\SQL\$server2\Default `
		    -NoServiceRestart
		$svc2.Stop()
		$svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
		$svc2.Start();
		$svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Cree un directorio de copia de seguridad y conceda permisos a las cuentas de servicio de SQL Server. Usará este directorio para preparar la base de datos de disponibilidad en la réplica secundaria.

		$backup = "C:\backup"
		New-Item $backup -ItemType directory
		net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
		icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")

1. Cree una base de datos en **ContosoSQL1** denominado **MyDB1**, realice tanto una copia de seguridad completa como una copia de seguridad de registros y restáurelas en **ContosoSQL2** con la opción ** WITH NORECOVERY **.

		Invoke-SqlCmd -Query "CREATE database $db"
		Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
		Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
		Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
		Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery

1. Cree los extremos del grupo de disponibilidad en las máquinas virtuales de SQL Server y establezca los permisos adecuados en los extremos.

		$endpoint =
		    New-SqlHadrEndpoint MyMirroringEndpoint `
		    -Port 5022 `
		    -Path "SQLSERVER:\SQL\$server1\Default"
		Set-SqlHadrEndpoint `
		    -InputObject $endpoint `
		    -State "Started"
		$endpoint =
		    New-SqlHadrEndpoint MyMirroringEndpoint `
		    -Port 5022 `
		    -Path "SQLSERVER:\SQL\$server2\Default"
		Set-SqlHadrEndpoint `
		    -InputObject $endpoint `
		    -State "Started"

		Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
		Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
		Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
		Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2

1. Cree las réplicas de disponibilidad.

		$primaryReplica =
		    New-SqlAvailabilityReplica `
		    -Name $server1 `
		    -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
		    -AvailabilityMode "SynchronousCommit" `
		    -FailoverMode "Automatic" `
		    -Version 11 `
		    -AsTemplate
		$secondaryReplica =
		    New-SqlAvailabilityReplica `
		    -Name $server2 `
		    -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
		    -AvailabilityMode "SynchronousCommit" `
		    -FailoverMode "Automatic" `
		    -Version 11 `
		    -AsTemplate

1. Por último, cree el grupo de disponibilidad y una la réplica secundaria al grupo de disponibilidad.

		New-SqlAvailabilityGroup `
		    -Name $ag `
		    -Path "SQLSERVER:\SQL\$server1\Default" `
		    -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
		    -Database $db
		Join-SqlAvailabilityGroup `
		    -Path "SQLSERVER:\SQL\$server2\Default" `
		    -Name $ag
		Add-SqlAvailabilityDatabase `
		    -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
		    -Database $db

## Pasos siguientes
Ha implementado correctamente SQL Server AlwaysOn creando un grupo de disponibilidad en Azure. Para configurar un agente de escucha para este grupo de disponibilidad, consulte [Configuración del agente de escucha ILB de los grupos de disponibilidad AlwaysOn en Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md).

Para obtener más información sobre el uso de SQL Server en Azure, consulte [SQL Server en Máquinas virtuales de Azure](../articles/virtual-machines/virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_0211_2016-->