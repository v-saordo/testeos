<properties 
   pageTitle="Operaciones en zonas DNS | Microsoft Azure" 
   description="Puede administrar zonas DNS con Azure Powershell. Actualización, eliminación y creación de zonas DNS en DNS de Azure" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/24/2015"
   ms.author="joaoma"/>

# Cómo administrar zonas DNS con PowerShell

> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)


En esta guía se explica cómo administrar zonas DNS. En ella se detallará la secuencia de operaciones que hay que realizar para administrar la zona DNS.


## Creación de una zona DNS

Para crear una zona DNS para hospedar un dominio, use el cmdlet New-AzureRmDnsZone:

		PS C:\> $zone = New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [–Tag $tags] 

La operación crea una nueva zona DNS de DNS de Azure y devuelve un objeto local que se corresponde con esa zona. También puede especificar una matriz de etiquetas del Administrador de recursos de Azure; para obtener más información, consulte [Etags y etiquetas](../dns-getstarted-create-dnszone.md#Etags-and-tags).

El nombre de la zona debe ser único en el grupo de recursos y la zona no debe existir aún, de lo contrario se producirá un error de la operación.

El mismo nombre de zona podrá reutilizarse en un grupo de recursos distinto o en una suscripción a Azure diferente. Cuando varias zonas comparten el mismo nombre, a cada instancia se le asignarán distintas direcciones de servidores de nombres, y solo se podrá delegar una instancia desde el dominio primario. Consulte [Delegación de un dominio a DNS de Azure](dns-domain-delegation.md) para obtener más información.

## Recuperación de una zona DNS

Para recuperar una zona DNS, use el cmdlet Get-AzureRmDnsZone:

		PS C:\> $zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

La operación devuelve un objeto de la zona DNS correspondiente a una zona existente en DNS de Azure. Este objeto contiene datos sobre la zona (por ejemplo, el número de conjuntos de registros), pero no contiene los conjuntos de registros.

## Enumeración de zonas DNS

Si se omite el nombre de la zona desde Get-AzureRmDnsZone, puede enumerar todas las zonas en un grupo de recursos:

	PS C:\> $zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup

Esta operación devuelve una matriz de objetos de la zona.

## Actualización de una zona DNS

Los cambios en los recursos de una zona DNS se pueden realizar con Set-AzureRmDnsZone. Con esta operación no se actualizan los conjuntos de registros DNS de la zona (consulte [Administración de registros DNS](dns-operations-recordsets.md)). Solo se utiliza para actualizar las propiedades de los recursos de la zona. Esto se limita actualmente a las “etiquetas” del Administrador de recursos de Azure para los recursos de la zona. Consulte [Etags y etiquetas](dns-getstarted-create-dnszone.md#Etags-and-tags) para obtener más información.

Utilice una de las dos opciones siguientes para actualizar las zonas DNS:

### Opción 1
 
Especificar la zona con la utilización del nombre de zona y el grupo de recursos:

	PS C:\> Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]

### Opción 2
Especificar la zona utilizando un objeto $zone desde Get-AzureRmDnsZone:

	PS C:\> $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> <..modify $zone.Tags here...>
	PS C:\> Set-AzureRmDnsZone -Zone $zone [-Overwrite]

Al usar Set-AzureRmDnsZone con un objeto $zone, se usarán las comprobaciones de “Etag” para garantizar que no se sobrescriban los cambios simultáneos. Puede usar el elemento opcional “-Overwrite” para suprimir estas comprobaciones. Consulte [Etags y etiquetas](dns-getstarted-create-dnszone.md#Etags-and-tags) para obtener más información.

## Eliminación de una zona DNS

Las zonas DNS pueden eliminarse con el cmdlet Remove-AzureRmDnsZone.
 
Antes de eliminar una zona DNS de DNS de Azure, deberá eliminar todos los conjuntos de registros, salvo los registros NS y SOA de la raíz de la zona que se crearon automáticamente cuando se creó la zona.

Utilice una de las dos opciones siguientes para eliminar una zona DNS:

### Opción 1

Especificar la zona con la utilización del nombre de zona y el nombre del grupo de recursos:

	PS C:\> Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force] 

Esta operación tiene un elemento opcional “-Force” que suprime el mensaje para confirmar que desea eliminar la zona DNS.

### Opción 2

Especificar la zona utilizando un objeto $zone desde Get-AzureRmDnsZone:

	PS C:\> $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureRmDnsZone -Zone $zone [-Force] [-Overwrite]

El elemento “-Force” es el mismo que en la opción 1.

Como con “Set-AzureRmDnsZone”, especificar la zona utilizando un objeto $zone permite realizar las comprobaciones de "etag" para asegurarse de que no se eliminan los cambios simultáneos. <BR> La etiqueta opcional “-Overwrite” suprime estas comprobaciones. Consulte [Etags y etiquetas](dns-getstarted-create-dnszone.md#Etags-and-tags) para obtener más información.

También se puede canalizar el objeto de la zona en lugar de pasarlo como parámetro:

	PS C:\> Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone [-Force] [-Overwrite]

## Pasos siguientes


[Administración de registros DNS](dns-operations-recordsets.md)

[Automatización de operaciones de Azure con el SDK de .NET](dns-sdk.md)

<!---HONumber=AcomDC_1125_2015-->