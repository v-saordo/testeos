<properties
  pageTitle="Conexión de un servicio en la nube a un controlador de dominio personalizado | Microsoft Azure"
  description="Aprenda a conectar los roles web o de trabajo a un dominio de AD personalizado mediante Powershell y la extensión de dominio de AD."
  services="cloud-services"
  documentationCenter=""
  authors="VMak"
  manager="timlt"
  editor=""/>

  <tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/05/2015"
    ms.author="adegeo"/>

# Conexión de los roles de Servicios en la nube de Azure a un controlador de dominio de AD personalizado que se hospeda en Azure

En primer lugar, vamos a configurar una red virtual (VNET) en Azure. A continuación, agregaremos un controlador de dominio de Active Directory (hospedado en una máquina virtual de Azure) a la red virtual. Después, agregaremos los roles de servicios en la nube existentes a la red virtual creada previamente y, a continuación, los conectaremos al controlador de dominio.

Antes de empezar, debemos tener en cuenta un par de cosas:

1.	En este tutorial se usa Powershell, por lo que debe asegurarse de tener Azure Powershell instalado y listo para usar. Para obtener ayuda con la configuración de Azure Powershell, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).

2.	El controlador de dominio de AD y las instancias de rol web o de trabajo deben estar en la red virtual.

Siga esta guía paso a paso y, en caso de que surja algún problema, deje un comentario a continuación. Nos pondremos en contacto con usted (siempre leemos los comentarios).

## Creación de una red virtual

Puede crear una red virtual en Azure mediante el Portal de Azure clásico o Powershell. En este tutorial, usaremos Powershell. Para crear una red virtual mediante el Portal de Azure clásico, consulte [Creación de una red virtual](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath;
```

## Creación de una máquina virtual

Una vez que haya completado la configuración de la red virtual, deberá crear un controlador de dominio de AD. En este tutorial, se va a configurar un controlador de dominio de AD en una máquina virtual de Azure.

Para ello, cree una máquina virtual a través de Powershell mediante los comandos siguientes.

```powershell
# Initialize variables

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = ‘<your-hosted-service>’
$vm1 = ‘<your-vm-name>’
$username = ‘<your-username>’
$password = ‘<your-password>’
$ affgrp = ‘<your- affgrp>’

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## Promoción de la máquina virtual a un controlador de dominio
Para configurar la máquina virtual como controlador de dominio de AD, deberá iniciar sesión en esta y configurarla.

Para iniciar sesión en la máquina virtual, puede obtener el archivo RDP a través de Powershell con los comandos siguientes.

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Una vez que haya iniciado sesión en la máquina virtual, configúrela como controlador de dominio de AD siguiendo la guía paso a paso de [Configuración del controlador de dominio AD de cliente](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## Incorporación del servicio en la nube a la red virtual

A continuación, deberá agregar la implementación del servicio en la nube a la red virtual que acaba de crear. Para ello, modifique el archivo cscfg del servicio en la nube agregando a este las secciones relevantes con Visual Studio o el editor de su elección.

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNET settings-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNET settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

A continuación, compile el proyecto de servicios en la nube e impleméntelo en Azure. Para obtener ayuda con la implementación del paquete de servicios en la nube en Azure, consulte [Creación e implementación de un servicio en la nube](cloud-services-how-to-create-deploy.md#deploy).

## Conexión de los roles de web y de trabajo al dominio

Una vez implementado el proyecto de servicio en la nube en Azure, conecte las instancias de rol al dominio de AD personalizado mediante la extensión de dominio de AD. Para agregar la extensión de dominio de AD a la implementación existente de servicios en la nube y unirse al dominio personalizado, ejecute los siguientes comandos en Powershell:

```powershell
# Initialize domain variables

$domain = ‘<your-domain-name>’;
$dmuser = ‘$domain<your-username>’;
$dmpswd = '<your-domain-password>';
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force;
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd);

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35;
```

Esto es todo.

Ahora, los servicios en la nube deben estar unidos al controlador de dominio personalizado. Si desea obtener más información sobre las distintas opciones disponibles para configurar la extensión de dominio de AD, use la ayuda de PS tal como se muestra a continuación.

```powershell
help Set-AzureServiceADDomainExtension;
help New-AzureServiceADDomainExtensionConfig;
```

También nos gustaría que nos enviara comentarios donde indique si le resultaría útil tener una extensión que promueva una máquina virtual a un controlador de dominio. En caso de ser así, infórmenos al respecto en la sección de comentarios.

<!---HONumber=AcomDC_0204_2016-->