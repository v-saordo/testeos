<properties 
   pageTitle="Administración de conjuntos de registros y registros DNS en DNS de Azure | Microsoft Azure" 
   description="Administración de conjuntos de registros y registros DNS en DNS de Azure al hospedar dominios en DNS de Azure. Todos los comandos de PowerShell para operaciones en conjuntos de registros y registros." 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="carmon" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="en"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="01/21/2016"
   ms.author="joaoma"/>

# Cómo administrar registros DNS con PowerShell


> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


En esta guía se explica cómo administrar conjuntos de registros y registros de zonas DNS.

Es importante comprender la diferencia entre los conjuntos de registros de DNS y los registros DNS individuales. Un conjunto de registros es la colección de registros de una zona con el mismo nombre y el mismo tipo. Para obtener información, consulte [Descripción de los registros y los conjuntos de registros](../dns-getstarted-create-recordset#Understanding-record-sets-and-records).

## Creación de un conjunto de registros

Se crean conjuntos de registros mediante el cmdlet New-AzureDnsRecordSet. Deberá especificar el nombre del conjunto de registros, la zona, el período de vida (TTL) y el tipo de registro.

El nombre del conjunto de registros debe ser un nombre relativo, sin incluir el nombre de la zona. Por ejemplo, el nombre del conjunto de registros “www” en la zona “contoso.com” creará un conjunto de registros con el nombre completo “www.contoso.com”.

Para un conjunto de registros del vértice de la zona, utilice "@" como nombre de conjunto de registro, incluidas las comillas.Para un conjunto de registros del vértice de la zona, utilice "@" como nombre de conjunto de registro, incluidas las comillas. El nombre completo del conjunto de registros es igual al nombre de zona, en este caso "contoso.com".

DNS de Azure es compatible con todos los tipos de registro siguientes: A, AAAA, CNAME, MX, NS, SOA, SRV y TXT. Los conjuntos de registros de tipo SOA se crean automáticamente con cada zona; no se pueden crear por separado.

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name www -Zone $zone -RecordType A -Ttl 300 [-Tag $tags] [-Overwrite] [-Force]

Si un registro ya existe, el comando generará un error a menos que se use el elemento Overwrite. La opción “-Overwrite” generará un mensaje de confirmación, que puede suprimirse con el elemento -Force.

En el ejemplo anterior, la zona se especifica mediante un objeto de zona, que devuelve Get-AzureDnsZone o New-AzureDnsZone. Como alternativa, también puede especificar la zona por nombre de zona y nombre del grupo de recursos:

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name www –ZoneName contoso.com –ResourceGroupName MyAzureResourceGroup -RecordType A -Ttl 300 [-Tag $tags] [-Overwrite] [-Force]

New-AzureDnsRecordSet devuelve un objeto local que representa el conjunto de registros que se crea en DNS de Azure.

>[AZURE.IMPORTANT] Los conjuntos de registros CNAME coexisten con otros conjuntos de registros con el mismo nombre. Por ejemplo, no se puede crear un registro CNAME con el nombre relativo "www" y un registro A con el nombre relativo "www" al mismo tiempo. Habida cuenta de que el ápice de zona (nombre = “@”) siempre contiene los conjuntos de registros NS y SOA creados cuando se crea la zona, significa que no puede crear un conjunto de registros CNAME en el ápice de zona. Estas restricciones surgen de los estándares DNS; no son limitaciones de DNS de Azure.

### Registros de carácter comodín

DNS de Azure admite [registros de carácter comodín](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Estos se devuelven en cualquier consulta con un nombre coincidente (a menos que haya una coincidencia más próxima de un conjunto de registros que no sean de caracteres comodín).

Para crear un conjunto de registros de carácter comodín, use el nombre de conjunto de registros "*" o un nombre cuya primera etiqueta sea "*", por ejemplo, "*.foo".

Los conjuntos de registros de carácter comodín son compatibles con todos los tipos de registro, excepto NS y SOA.

## Recuperación de un conjunto de registros

Para recuperar un conjunto de registros existente, use “Get-AzureDnsRecordSet” y especifique el nombre relativo del conjunto de registros, el tipo de registro y la zona:

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name www –RecordType A -Zone $zone

Al igual que con New-AzureDnsRecordSet, el nombre de registro debe ser relativo, es decir, sin incluir el nombre de zona. La zona se puede especificar mediante un objeto de zona (como anteriormente) o por nombre de zona y nombre del grupo de recursos:

	PS C:\> $rs = Get-AzureRmDnsRecordSet –Name www –RecordType A -Zonename contoso.com -ResourceGroupName MyAzureResourceGroup

Get-AzureRmDnsRecordSet devuelve un objeto local que representa el conjunto de registros que se crea en DNS de Azure.

## Enumeración de conjuntos de registros
Si se omiten los parámetros –Name o –RecordType, también puede usarse Get-AzureDnsRecordSet para enumerar conjuntos de registros:

### Opción 1 

Mostrar todos los tipos de registros. Esto devolverá todos los conjuntos de registros, independientemente del nombre o tipo de registro:

	PS C:\> $list = Get-AzureRmDnsRecordSet -Zone $zone

### Opción 2 

Mostrar los conjuntos de registros de un tipo de registro determinado. Esta opción devolverá todos los conjuntos de registros que coincidan con el tipo de registro determinado (en este caso, los registros A):

	PS C:\> $list = Get-AzureRmDnsRecordSet –RecordType A -Zone $zone 

En ambos casos, la zona se puede especificar mediante un objeto de zona (como se muestra) o especificando los parámetros –ZoneName y –ResourceGroupName.

## Incorporación de un registro a un conjunto de registros

Los registros se agregan a los conjuntos de registros mediante el cmdlet Add-AzureRmDnsRecordConfig. Se trata de una operación sin conexión; solo cambia el objeto local que representa al conjunto de registros.

Los parámetros para agregar registros a un conjunto de registros varían según el tipo de conjunto de registros. Por ejemplo, cuando se utiliza un conjunto de registros de tipo “A”, sólo podrá especificar los registros con el parámetro “IPv4Address”.

Se pueden agregar más registros a cada conjunto de registros mediante llamadas adicionales a Add-AzureDnsRecordConfig. Puede agregar hasta 100 registros a cualquier conjunto de registros. Sin embargo, los conjuntos de registros de tipo CNAME pueden contener como máximo 1 registro, y un conjunto de registros no puede contener dos registros idénticos. Se pueden crear conjuntos de registros vacíos (sin ningún registro), pero no aparecen en los servidores de nombres DNS de Azure.

Cuando el conjunto de registros contenga la colección deseada de registros, debe confirmarse mediante el cmdlet Set-AzureDnsRecordSet, que reemplaza el conjunto de registros existente que se estableció en DNS de Azure por el conjunto de registros especificado. Los ejemplos siguientes muestran cómo crear un conjunto de registros de cada tipo de registro que contiene un único registro.

### Creación de un conjunto de registros A con un único registro

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone -Ttl 60
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

La secuencia de operaciones para crear un registro también puede “canalizarse”, pasando el objeto del conjunto de registros usando la canalización en lugar de como un parámetro. Por ejemplo:

	PS C:\> New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone -Ttl 60 | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### Creación de un conjunto de registros AAAA con un único registro

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -Zone $zone -Ttl 60
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Creación de un conjunto de registros CNAME con un único registro

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-cname" -RecordType CNAME -Zone $zone -Ttl 60
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Creación de un conjunto de registros MX con un único registro

En este ejemplo, se utiliza el nombre de conjunto de registros "@" para crear el registro MX en el vértice de la zona (por ejemplo, "contoso.com"). Esto es común para los registros MX.

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "@" -RecordType MX -Zone $zone -Ttl 60
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Creación de un conjunto de registros NS con un único registro

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -Zone $zone -Ttl 60
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Creación de un conjunto de registros SRV con un único registro

Al crear un registro SRV en la raíz de la zona, basta con especificar \_servicio y \_serviceprotocolo en el nombre del registro; no es necesario incluir también ‘.@’ en el nombre del registro

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -Zone $zone -Ttl 60
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Creación de un conjunto de registros TXT con un único registro

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -Zone $zone -Ttl 60
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

## Modificación de conjuntos de registros existentes

La modificación de conjuntos de registros existentes sigue un patrón similar a la creación de registros. La secuencia de operaciones es la siguiente:

1.	Recupere el conjunto de registros existente con Get-AzureDnsRecordSet.
2.	Modifique el conjunto de registros; para ello, agregue registros, elimine registros, cambie los parámetros de los registros o cambie el TTL del conjunto de registros. Estos cambios se realizan sin conexión; solo se cambia el objeto local que representa el conjunto de registros.
3.	Confirme los cambios mediante el cmdlet Set-AzureDnsRecordSet. Esto reemplaza el conjunto de registros existente en DNS de Azure con el conjunto de registros especificado.

Esto se muestra en los ejemplos siguientes:

### Actualización de un registro en un conjunto de registros existente

En este ejemplo, se cambiará la dirección IP de un registro A existente:

	PS C:\> $rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -Zone $zone 
	PS C:\> $rs.Records[0].Ipv4Address = "134.170.185.46"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs 

El cmdlet Set-AzureDnsRecordSet usa comprobaciones de "etag" para asegurarse de que no se sobrescriben cambios simultáneos. Utilice la marca “-Overwrite” para suprimir estas comprobaciones. Consulte Etags y etiquetas para obtener más información.

### Modificación de registros SOA

>[AZURE.NOTE] No puede agregar ni eliminar registros del conjunto de registros SOA creado automáticamente en el ápice de zona (nombre = “@”), pero puede modificar los parámetros del registro SOA y del TTL del conjunto de registros.

En el ejemplo siguiente se muestra cómo cambiar la propiedad “Email” del registro SOA:

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -Zone $zone
	PS C:\> $rs.Records[0].Email = "admin.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs 

### Modificación de los registros NS en el ápice de zona

>[AZURE.NOTE] No puede agregar, eliminar ni modificar los registros en el conjunto de registros NS creado automáticamente en el ápice de zona (nombre = “@”). El único cambio permitido es modificar el TTL del conjunto de registros.

En el ejemplo siguiente se muestra cómo cambiar la propiedad TTL del conjunto de registros NS:

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -Zone $zone
	PS C:\> $rs.Ttl = 300
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs 

### Incorporación de registros a un conjunto de registros existente

En este ejemplo, agregamos dos registros MX más al conjunto de registros existente:

	PS C:\> $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -Zone $zone
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs 

## Eliminación de un registro de un conjunto de registros existente

Pueden quitarse registros de un conjunto de registros con Remove-AzureDnsRecordConfig. Tenga en cuenta que todos los parámetros del registro que se va a eliminar deben coincidir exactamente con los del registro existente. Los cambios deben confirmarse con Set-AzureDnsRecordSet.

Al eliminar el último registro de un conjunto de registros, no se elimina el conjunto de registros. Consulte [Eliminación de un conjunto de registros](#delete-a-record-set) a continuación para obtener más información.


	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A –Zone $zone
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

La secuencia de operaciones para eliminar un registro de un conjunto de registros también puede “canalizarse”, pasando el objeto del conjunto de registros usando la canalización en lugar de como un parámetro. Por ejemplo:

	PS C:\> Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### Eliminación de un registro AAAA de un conjunto de registros

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA –Zone $zone
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Eliminación de un registro CNAME de un conjunto de registros

Puesto que un conjunto de registros CNAME puede contener como máximo un registro, al eliminar dicho registro, un conjunto de registros se quedará vacío.

	PS C:\> $rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME –Zone $zone	
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Eliminación de un registro MX de un conjunto de registros

	PS C:\> $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType 'MX' –Zone $zone	
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Eliminación de un registro NS de un conjunto de registros
	
	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -Zone $zone
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Eliminación de un registro SRV de un conjunto de registros

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -Zone $zone
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Eliminación de un registro TXT de un conjunto de registros

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -Zone $zone
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

## Eliminación de un conjunto de registros
Pueden eliminarse conjuntos de registros mediante el cmdlet Remove-AzureDnsRecordSet.

>[AZURE.NOTE] No se pueden eliminar conjuntos de registros SOA ni NS en el ápice de zona (nombre = “@”) que se crean automáticamente cuando se crea la zona. Se eliminarán automáticamente al eliminar la zona.

Utilice una de las dos opciones siguientes para eliminar un conjunto de registros:

### Opción 1

Especificar todos los parámetros por nombre:

	PS C:\> Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zonename "contoso.com" -ResourceGroupName MyAzureResourceGroup [-Force]

Se puede utilizar el elemento opcional “-Force” para suprimir el mensaje de confirmación.

### Opción 2

Especificar el conjunto de registros por nombre y tipo, especificar la zona por objeto:

	PS C:\> Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### Opción 3

Especificar el conjunto de registros por objeto:

	PS C:\> Remove-AzureRmDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

Al especificar el conjunto de registros con un objeto, las comprobaciones de “etag” pueden garantizar que no se eliminan los cambios simultáneos. La etiqueta opcional “-Overwrite”’ suprime estas comprobaciones. Consulte [Etags y etiquetas](../dns-getstarted-create-dnszone#Etags-and-tags) para obtener más información.

El objeto del conjunto de registros también puede canalizarse en lugar de pasarse como un parámetro:

	PS C:\> Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]

##Otras referencias

[Introducción a la creación de registros y conjuntos de registros](dns-getstarted-create-recordset.md)<BR> [Administración de zonas DNS](dns-operations-dnszones.md)<BR> [Automatización de operaciones con el SDK de .NET](dns-sdk.md)
 

<!---HONumber=AcomDC_0128_2016-->