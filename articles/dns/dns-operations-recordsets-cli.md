<properties 
   pageTitle="Administración de registros y conjuntos de registros DNS en DNS de Azure | Microsoft Azure" 
   description="Administración de conjuntos de registros y registros DNS en DNS de Azure al hospedar dominios en DNS de Azure. Todos los comandos de CLI para operaciones en conjuntos de registros y registros." 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="en"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="01/21/2016"
   ms.author="joaoma"/>

# Cómo administrar registros DNS con CLI

> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)

En esta guía se explica cómo administrar conjuntos de registros y registros de zonas DNS.

>[AZURE.NOTE] DNS de Azure es un servicio exclusivo del Administrador de recursos de Azure. No tiene ninguna API de ASM. Por tanto, deberá asegurarse de que la CLI de Azure se ha configurado para usar el modo del Administrador de recursos, con el comando 'azure config mode arm'.

>Si ve "error: 'dns' is not an azure command", lo más probable se deba a que está usando CLI de Azure en modo ASM, no en el modo del Administrador de recursos.

Es importante comprender la diferencia entre los conjuntos de registros de DNS y los registros DNS individuales. Un conjunto de registros es la colección de registros de una zona con el mismo nombre y el mismo tipo. Para obtener información, consulte [Descripción de los registros y los conjuntos de registros](dns-getstarted-create-recordset.md#Understanding-record-sets-and-records).

## Creación de un conjunto de registros

Los conjuntos de registros se crean con el comando `azure network dns record-set create`. Deberá especificar el nombre del conjunto de registros, la zona, el período de vida (TTL) y el tipo de registro.

El nombre del conjunto de registros debe ser un nombre relativo, sin incluir el nombre de la zona. Por ejemplo, el nombre del conjunto de registros “www” en la zona “contoso.com” creará un conjunto de registros con el nombre completo “www.contoso.com”.

Para un conjunto de registros del vértice de la zona, utilice "@" como nombre de conjunto de registro, incluidas las comillas.Para un conjunto de registros del vértice de la zona, utilice "@" como nombre de conjunto de registro, incluidas las comillas. El nombre completo del conjunto de registros es igual al nombre de zona, en este caso "contoso.com".

DNS de Azure es compatible con todos los tipos de registro siguientes: A, AAAA, CNAME, MX, NS, SOA, SRV y TXT. Los conjuntos de registros de tipo SOA se crean automáticamente con cada zona; no se pueden crear por separado. Tenga en cuenta que [el tipo de registro SPF está en desuso por los estándares DNS en favor de la creación de registros SPF mediante el tipo de registro TXT](http://tools.ietf.org/html/rfc7208#section-3.1).

	azure network dns record-set create myresourcegroup contoso.com  www  A --ttl 300


>[AZURE.IMPORTANT] Los conjuntos de registros CNAME coexisten con otros conjuntos de registros con el mismo nombre. Por ejemplo, no se puede crear un registro CNAME con el nombre relativo "www" y un registro A con el nombre relativo "www" al mismo tiempo. Habida cuenta de que el ápice de zona (nombre = “@”) siempre contiene los conjuntos de registros NS y SOA creados cuando se crea la zona, significa que no puede crear un conjunto de registros CNAME en el ápice de zona. Estas restricciones surgen de los estándares DNS; no son limitaciones de DNS de Azure.

### Registros de carácter comodín

DNS de Azure admite [registros de carácter comodín](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Estos se devuelven en cualquier consulta con un nombre coincidente (a menos que haya una coincidencia más próxima de un conjunto de registros que no sean de caracteres comodín). Para crear un conjunto de registros de carácter comodín, use el nombre de conjunto de registros "*" o un nombre cuya primera etiqueta sea "*", por ejemplo, "*.foo".

Los conjuntos de registros de carácter comodín son compatibles con todos los tipos de registro, excepto NS y SOA.

## Recuperación de un conjunto de registros
Para recuperar un conjunto de registros existente, use `azure network dns record-set show` y especifique el grupo de recursos, el nombre de zona, el nombre relativo del conjunto de registros y el tipo de registro:

	azure network dns record-set show myresourcegroup contoso.com www A


## Enumeración de conjuntos de registros

Puede mostrar todos los registros de una zona DNS con el comando `azure network dns record-set list`:

### Opción 1 
Mostrar todos los tipos de registros. Esto devolverá todos los conjuntos de registros, independientemente del nombre o tipo de registro:

	azure network dns record-set list myresourcegroup contoso.com

### Opción 2 

Mostrar los conjuntos de registros de un tipo de registro determinado. Esta opción devolverá todos los conjuntos de registros que coincidan con el tipo de registro determinado (en este caso, los registros A):


	azure network dns record-set list myresourcegroup contoso.com A 

En ambos casos especificará el nombre del grupo de recursos y el nombre de zona.

## Incorporación de un registro a un conjunto de registros

Los registros se agregan a los conjuntos de registros mediante el comando `azure network dns record-set add-record`.

Los parámetros para agregar registros a un conjunto de registros varían según el tipo de conjunto de registros. Por ejemplo, cuando se usa un conjunto de registros de tipo "A", solo podrá especificar registros con el parámetro "-a `<IPv4 address>`".

Los ejemplos siguientes muestran cómo crear un conjunto de registros de cada tipo de registro que contiene un único registro.

### Creación de un conjunto de registros A con un único registro

Para crear un conjunto de registros, use `azure network dns record-set create` y especifique el grupo de recursos, el nombre de zona, el nombre relativo del conjunto de recursos, el tipo de registro y el período de vida (TTL):
	
	azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300

>[AZURE.NOTE] Si no se define el parámetro--ttl, el valor predeterminado es 4 (en segundos).


Después de crear el conjunto de registros A, agregue la dirección IPv4 al conjunto de registros con `azure network dns record-set add-record`:

	azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1 

### Creación de un conjunto de registros AAAA con un único registro

	azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

	azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"

### Creación de un conjunto de registros CNAME con un único registro

	azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300
	
	azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"

>[AZURE.NOTE] Los registros CNAME solo permite un único valor de cadena.

### Creación de un conjunto de registros MX con un único registro

En este ejemplo, se utiliza el nombre de conjunto de registros "@" para crear el registro MX en el vértice de la zona (por ejemplo, "contoso.com"). Esto es común para los registros MX.

	azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

	azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5


### Creación de un conjunto de registros NS con un único registro

	azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300
	
	azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com" 
	
### Creación de un conjunto de registros SRV con un único registro

Al crear un registro SRV en la raíz de la zona, basta con especificar \_servicio y \_serviceprotocolo en el nombre del registro; no es necesario incluir también ‘.@’ en el nombre del registro

	
	azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300 

	azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com" 

### Creación de un conjunto de registros TXT con un único registro

	azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

	azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record" 


## Modificación de conjuntos de registros existentes


Esto se muestra en los ejemplos siguientes:

### Actualización de un registro en un conjunto de registros existente

En este ejemplo, agregaremos otra dirección IP (1.2.3.4) a un conjunto de registros A existente (www):

	azure network dns record-set add-record  myresourcegroup contoso.com  A
	-a 1.2.3.4
	info:    Executing command network dns record-set add-record
	Record set name: www
	+ Looking up the dns zone "contoso.com"
	+ Looking up the DNS record set "www"
	+ Updating DNS record set "www"
	data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/a/www
	data:    Name                            : www	
	data:    Type                            : Microsoft.Network/dnszones/a
	data:    Location                        : global
	data:    TTL                             : 4
	data:    A records:
	data:        IPv4 address                : 192.168.1.1
	data:        IPv4 address                : 1.2.3.4
	data:
	info:    network dns record-set add-record command OK


Usará `azure network dns record-set delete-record` para quitar un valor existente de un conjunto de registros:
 
	azure network dns record-set delete-record myresourcegroup contoso.com www A -a 1.2.3.4
	info:    Executing command network dns record-set delete-record
	+ Looking up the DNS record set "www"
	Delete DNS record? [y/n] y
	+ Updating DNS record set "www"
	data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
	data:    Name                            : www
	data:    Type                            : Microsoft.Network/dnszones/A
	data:    Location                        : global
	data:    TTL                             : 4
	data:    A records:
	data:        IPv4 address                : 192.168.1.1
	data:
	info:    network dns record-set delete-record command OK



## Eliminación de un registro de un conjunto de registros existente

Se pueden quitar registros de un conjunto de registros mediante `azure network dns record-set delete-record`. Tenga en cuenta que el registro que se va a quitar debe coincidir exactamente con todos los parámetros de un registro existente.

Al eliminar el último registro de un conjunto de registros, no se elimina el conjunto de registros. Consulte [Eliminación de un conjunto de registros](#delete-a-record-set) a continuación para obtener más información.


	azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

	azure network dns record-set delete myresourcegroup contoso.com www A

### Eliminación de un registro AAAA de un conjunto de registros

	azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"

### Eliminación de un registro CNAME de un conjunto de registros

	azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com
	

### Eliminación de un registro MX de un conjunto de registros

	azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5

### Eliminación de un registro NS de un conjunto de registros
	
	azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"

### Eliminación de un registro SRV de un conjunto de registros

	azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com" 

### Eliminación de un registro TXT de un conjunto de registros

	azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"

## Eliminación de un conjunto de registros
Los conjuntos de registros pueden eliminarse mediante el cmdlet Remove-AzureDnsRecordSet.

>[AZURE.NOTE] No se pueden eliminar conjuntos de registros SOA ni NS en el ápice de zona (nombre = “@”) que se crean automáticamente cuando se crea la zona. Se eliminarán automáticamente al eliminar la zona.

En el ejemplo siguiente, el conjunto de registros A "test-a" se eliminará de la zona DNS contoso.com:

	azure network dns record-set delete myresourcegroup contoso.com  "test-a" A 

Se puede usar el modificador opcional “-q” para suprimir el mensaje de confirmación.


## Pasos siguientes

Después de crear la zona DNS y los registros, puede [delegar el dominio en DNS de Azure](dns-domain-delegation.md).<BR> Aprenda a [administrar zonas DNS](dns-operations-dnszones-cli.md) con CLI.<BR> También puede [automatizar operaciones mediante el SDK de .NET](dns-sdk.md) para codificar operaciones de DNS de Azure en la aplicación.

 

<!---HONumber=AcomDC_0128_2016-->