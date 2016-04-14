<properties
	pageTitle="Crear una máquina virtual de Windows con Powershell | Microsoft Azure"
	description="Crear máquinas virtuales de Windows mediante Azure PowerShell y el modelo de implementación clásica"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/13/2015"
	ms.author="cynthn"/>

# Crear máquinas virtuales de Windows con PowerShell y el modelo de implementación clásica 

> [AZURE.SELECTOR]
- [Azure classic portal - Windows](virtual-machines-windows-tutorial-classic-portal.md)
- [Powershell - Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)
- [PowerShell - Linux](virtual-machines-ps-create-preconfigure-linux-vms.md)

<br>


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md).


En estos pasos se muestra cómo personalizar un conjunto de comandos de Azure PowerShell que creen y preconfiguren una máquina virtual de Azure basada en Windows mediante el uso de un enfoque de bloque de creación. Puede utilizar este proceso para crear un conjunto de comandos para una nueva máquina virtual basada en Windows rápidamente y expandir una implementación existente, o para crear varios conjuntos de comandos que creen rápidamente un entorno de profesionales de TI, o de desarrollo o pruebas.

En estos pasos se sigue un enfoque consistente en atar cabos para crear conjuntos de comandos de Azure PowerShell. Este enfoque puede ser útil si está familiarizado con PowerShell o desea conocer los valores que debe especificar para una configuración correcta. Los usuarios avanzados de PowerShell pueden tomar los comandos y sustituir sus propios valores de las variables (las líneas que comienzan con "$").

Para consultar el tema paralelo sobre máquinas virtuales basadas en Linux, consulte [Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Linux](virtual-machines-ps-create-preconfigure-linux-vms.md).


## Paso 1: Instalación de Azure PowerShell

Si aún no lo ha hecho, siga las instrucciones de [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md) para instalar Azure PowerShell en un equipo local. A continuación, abra un símbolo del sistema de Azure PowerShell.

## Paso 2: Establecimiento de la cuenta de suscripción y almacenamiento

Establezca su cuenta de suscripción y almacenamiento de Azure mediante la ejecución de estos comandos en el símbolo del sistema de Azure PowerShell. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < and >, por los nombres correctos.

	$subscr="<subscription name>"
	$staccount="<storage account name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Puede obtener el nombre de suscripción correcto con la propiedad SubscriptionName de la salida del comando **Get-AzureSubscription**. Puede obtener el nombre de cuenta de almacenamiento correcto de la propiedad “Label” de la salida del comando **Get-AzureStorageAccount**, una vez haya ejecutado el comando **Select-AzureSubscription**.

## Paso 3: Determinación de ImageFamily

A continuación, deberá determinar el valor de ImageFamily o Label para la imagen concreta que corresponda a la máquina virtual de Azure que desea crear. Puede obtener la lista de los valores de ImageFamily disponibles con este comando.

	Get-AzureVMImage | select ImageFamily -Unique

A continuación, se muestran algunos ejemplos de valores de ImageFamily para equipos basados en Windows:

- Windows Server 2012 R2 Datacenter
- Windows Server 2008 R2 SP1
- Windows Server Technical Preview
- SQL Server 2012 SP1 Enterprise en Windows Server 2012

Si encuentra la imagen que está buscando, abra una nueva instancia del editor de texto que prefiera o el entorno de scripting integrado de PowerShell (ISE). Copie lo siguiente en el nuevo archivo de texto o PowerShell ISE, sustituyendo el valor de ImageFamily.

	$family="<ImageFamily value>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

En algunos casos, el nombre de la imagen se encuentra en la propiedad Label en lugar del valor de ImageFamily. Si no encuentra la imagen que desea para el uso de la propiedad ImageFamily, obtenga un listado de las imágenes según su propiedad Label con este comando.

	Get-AzureVMImage | select Label -Unique

Si encuentra la imagen correcta con este comando, abra una nueva instancia del editor de texto de su elección o PowerShell ISE. Copie lo siguiente en el nuevo archivo de texto o PowerShell ISE, sustituyendo el valor de Label.

	$label="<Label value>"
	$image = Get-AzureVMImage | where { $_.Label -eq $label } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## Paso 4: Creación del conjunto de comandos

Cree el resto del conjunto de comandos copiando el conjunto de bloques adecuado en el nuevo archivo de texto o ISE y, a continuación, rellene los valores de las variables y quite los caracteres < and >. Consulte los dos [ejemplos](#examples) al final de este artículo para obtener una idea del resultado final.

Inicie el conjunto de comandos eligiendo uno de estos dos bloques de comandos (obligatorio).

Opción 1: Especificación de un nombre de máquina virtual y un tamaño.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Opción 2: Especificación de un nombre, un tamaño y un nombre del conjunto de disponibilidad.

	$vmname="<machine name>"
	$vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availset="<set name>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

Para los valores de InstanceSize de las máquinas virtuales de las series D, DS o G, consulte [Máquina virtual y tamaños de servicios en la nube de Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

De forma opcional, en un equipo de Windows independiente, especifique la cuenta y la contraseña del administrador local.

	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

 Elija una contraseña segura. Para comprobar su robustez, consulte [Comprobador de contraseñas: uso de contraseñas seguras](https://www.microsoft.com/security/pc-security/password-checker.aspx).

Opcionalmente, para agregar el equipo de Windows a un dominio de Active Directory existente, especifique la cuenta del administrador local y la contraseña, el dominio, y el nombre y contraseña de una cuenta de dominio.

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account."
	$cred2=Get-Credential –Message "Now type the name (not including the domain) and password of an account that has permission to add the machine to the domain."
	$domaindns="<FQDN of the domain that the machine is joining>"
	$domacctdomain="<domain of the account that has permission to add the machine to the domain>"
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domaindns

Para ver opciones adicionales de configuración previa de máquinas virtuales basadas en Linux, consulte la sintaxis del conjunto de parámetros **Windows** y **WindowsDomain** en [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx).

Si lo desea, asígnele una dirección IP concreta, conocida como una DIP estática.

	$vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

Puede comprobar que una dirección IP concreta está disponible con:

	Test-AzureStaticVNetIP –VNetName <VNet name> –IPAddress <IP address>

De forma opcional, asigne la máquina virtual a una subred concreta en una red virtual de Azure.

	$vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

Opcionalmente, agregue un único disco de datos a la máquina virtual.

	$disksize=<size of the disk in GB>
	$disklabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$hcaching="<Specify one: ReadOnly, ReadWrite, None>"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

Para un controlador de dominio de Active Directory, establezca $hcaching en "None".

Si lo desea, puede agregar la máquina virtual a un conjunto de equilibrio de carga existente para el tráfico externo.

	$port="<Specify one: tcp, udp>"
	$localport=<port number of the internal port>
	$pubport=<port number of the external port>
	$endpointname="<name of the endpoint>"
	$lbsetname="<name of the existing load-balanced set>"
	$probeprotocol="<Specify one: tcp, http>"
	$probeport=<TCP or HTTP port number of probe traffic>
	$probepath="<URL path for probe traffic>"
	$vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

Por último, elija uno de estos bloques de comandos necesarios para crear la máquina virtual.

Opción 1: Creación de la máquina virtual en un servicio en la nube existente.

	New-AzureVM –ServiceName "<short name of the cloud service>" -VMs $vm1

El nombre corto del servicio en la nube es el nombre que aparece en la lista de Servicios en la nube en el Portal de Azure clásico o en la lista de grupos de recursos en el Portal de Azure.

Opción 2: Creación de la máquina virtual en un servicio en la nube y la red virtual.

	$svcname="<short name of the cloud service>"
	$vnetname="<name of the virtual network>"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## Paso 5: Ejecución del conjunto de comandos

Revise el conjunto de comandos de Azure PowerShell creado en el editor de texto o PowerShell ISE, que consta de varios bloques de comandos del paso 4. Asegúrese de que ha especificado todas las variables necesarias y de que tengan los valores correctos. También asegúrese de que ha quitado todos los caracteres < and >.

Si está utilizando un editor de texto, copie el conjunto de comandos en el Portapapeles y, a continuación, haga clic con el botón derecho en el símbolo del sistema del Azure PowerShell abierto. Así, se emitirá el conjunto de comandos como una serie de comandos de PowerShell y se creará la máquina virtual de Azure. Como alternativa, ejecute el conjunto de comando en PowerShell ISE.

Si va a crear esta máquina virtual de nuevo o una similar, puede:

- Guardar este conjunto de comandos como archivo de script de PowerShell (*.ps1).
- Guarde este conjunto de comandos como un Runbook de automatización de Azure en la sección **Automatización** del Portal de Azure clásico.

## <a id="examples"></a>Ejemplos

Estos son dos ejemplos del uso de los pasos anteriores para crear conjuntos de comandos de Azure PowerShell que creen máquinas virtuales de Azure basadas en Windows.

### Ejemplo 1

Se necesita un conjunto de comandos de PowerShell que cree la máquina virtual inicial de un controlador de dominio de Active Directory que:

- Utilice la imagen de Windows Server 2012 R2 Datacenter.
- Tenga el nombre AZDC1.
- Sea un equipo independiente.
- Tenga un disco de datos adicional de 20 GB.
- Tenga la dirección IP estática 192.168.244.4.
- Se encuentre en la subred BackEnd de la red virtual AZDatacenter.
- Se encuentre en el servicio en la nube Azure-TailspinToys.

Este es el comando de Azure PowerShell correspondiente para crear esta máquina virtual, con líneas en blanco entre cada bloque para mejorar la legibilidad.

	$family="Windows Server 2012 R2 Datacenter"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vmname="AZDC1"
	$vmsize="Medium"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

	$disksize=20
	$disklabel="DCData"
	$lun=0
	$hcaching="None"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

### Ejemplo 2

Se necesita un conjunto de comandos de PowerShell que cree una máquina virtual para un servidor de línea de negocio que:

- Utilice la imagen de Windows Server 2012 R2 Datacenter.
- Tenga el nombre LOB1.
- Sea un miembro del dominio corp.contoso.com.
- Tenga un disco de datos adicional de 200 GB.
- Se encuentre en la subred FrontEnd de la red virtual AZDatacenter.
- Se encuentre en el servicio en la nube Azure-TailspinToys.

Este es el comando de Azure PowerShell correspondiente para crear esta máquina virtual.

	$family="Windows Server 2012 R2 Datacenter"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vmname="LOB1"
	$vmsize="Large"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account."
	$cred2=Get-Credential –Message "Now type the name (not including the domain) and password of an account that has permission to add the machine to the domain."
	$domaindns="corp.contoso.com"
	$domacctdomain="CORP"
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domaindns

	$vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

	$disksize=200
	$disklabel="LOBData"
	$lun=0
	$hcaching="ReadWrite"
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

	$svcname="Azure-TailspinToys"
	$vnetname="AZDatacenter"
	New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname


## Recursos adicionales

[Documentación sobre las máquinas virtuales](https://azure.microsoft.com/documentation/services/virtual-machines/)

[Preguntas más frecuentes sobre las máquinas virtuales de Azure](http://msdn.microsoft.com/library/azure/dn683781.aspx)

[Información general sobre las máquinas virtuales de Azure](http://msdn.microsoft.com/library/azure/jj156143.aspx)

[Instalación y configuración de Azure PowerShell](powershell-install-configure.md)

<!---HONumber=AcomDC_0204_2016-->