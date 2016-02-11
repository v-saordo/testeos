<properties
   pageTitle="Creación de un conjunto de registros y registros para una zona DNS con CLI | Microsoft Azure"
   description="Creación de registros host para DNS de Azure. Configuración de conjuntos de registros y registros mediante CLI"
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="Adinah"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/20/2016"
   ms.author="joaoma"/>


# Creación de registros CON DNS

> [AZURE.SELECTOR]
- [Azure CLI](dns-getstarted-create-recordset-cli.md)
- [PowerShell](dns-getstarted-create-recordset.md)



Después de crear la zona DNS, deberá agregar los registros DNS para su dominio. Para ello, primero debe saber qué son los registros y los conjuntos de registros DNS.


## Descripción de los registros y los conjuntos de registros
Cada registro DNS tiene un nombre y un tipo.

Un nombre _completo_ incluye el nombre de zona, mientras que un nombre _relativo_ no lo incluye. Por ejemplo, el nombre de registro relativo "www" en la zona "contoso.com" proporciona el nombre de registro completo "www.contoso.com".

>[AZURE.NOTE]En DNS de Azure, los registros se especifican mediante la utilización de nombres relativos.

Los registros se presentan en distintos tipos en función de los datos que contienen. El tipo más común es un registro "A", que asigna un nombre a una dirección IPv4. Otro tipo es un registro "MX", que asigna un nombre a un servidor de correo.

DNS de Azure es compatible con todos los tipos de registro DNS comunes: A, AAAA, CNAME, MX, NS, SOA, SRV y TXT. (Tenga en cuenta que [los registros SPF deben crearse mediante el tipo de registro TXT](http://tools.ietf.org/html/rfc7208#section-3.1)).

En ocasiones, necesitará crear más de un registro DNS con un nombre y un tipo concretos. Por ejemplo, supongamos que el sitio web www.contoso.com se hospeda en dos direcciones IP diferentes. Esto requiere dos registros A diferentes, uno para cada dirección IP:

	www.contoso.com.		3600	IN	A	134.170.185.46
	www.contoso.com.		3600	IN	A	134.170.188.221

Este es un ejemplo de un conjunto de registros. Un conjunto de registros es la colección de registros DNS de una zona con el mismo nombre y el mismo tipo. La mayoría de conjuntos de registros contienen un registro único, pero es habitual encontrar ejemplos como el anterior, en el que un conjunto de registros contiene más de un registro. (Los conjuntos de registros del tipo SOA y CNAME son una excepción, los estándares DNS no permiten varios registros con el mismo nombre para estos tipos).

El período de vida, o TTL, especifica durante cuánto tiempo los clientes almacenan cada registro en caché antes de volver a consultarlo. En el ejemplo anterior, el TTL es 3600 segundos o 1 hora. El TTL se especifica para el conjunto de registros, no para cada registro, por lo que se utiliza el mismo valor para todos los registros de ese conjunto de registros.

>[AZURE.NOTE]DNS de Azure administra los registros DNS con la utilización de conjuntos de registros.



## Creación de registros y conjuntos de registros 

En el ejemplo siguiente se mostrará cómo crear registros y un conjunto de registros. Vamos a usar el tipo de registro DNS 'A'; para otros tipos de registros, consulte [Administración de registros DNS](dns-operations-recordsets-cli.md)


### Paso 1

Creación de un conjunto de registros:

	Usage: network dns record-set create <resource-group> <dns-zone-name> <name> <type> <ttl>

	azure network dns record-set create myresourcegroup  contoso.com  www A  60

El conjunto de registros tiene el nombre relativo “www” en la zona DNS “contoso.com”, por lo que el nombre completo de los registros será “www.contoso.com”. El tipo de registro es “A” y el valor de TTL es de 60 segundos.

>[AZURE.NOTE]Para crear un conjunto de registros en el vértice de la zona (en este caso, "contoso.com"), utilice el nombre de registro "@", incluidas las comillas. Esta es una convención común de DNS.

El conjunto de registros está vacío y tenemos que agregar registros para poder usar el conjunto de registros “www” recién creado.<BR>

### Paso 2

Agregue registros de IPv4 A al conjunto de registros "www" con el comando siguiente:

	Usage: network dns record-set add-record <resource-group> <dns-zone-name> <record-set-name> <type>

	azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 134.170.185.46
	

Los cambios se han completado. Puede recuperar el conjunto de registros desde DNS de Azure con "azure network dns-record-set show":


	azure network dns record-set show myresourcegroup "contoso.com" www A
	
	info:    Executing command network dns-record-set show
	+ Looking up the DNS record set "www"
	data:    Id                              : /subscriptions/########################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
	data:    Name                            : www
	data:    Type                            : Microsoft.Network/dnszones/A
	data:    Location                        : global
	data:    TTL                             : 300
	data:    A records:
	data:        IPv4 address                : 134.170.185.46
	data:
	info:    network dns record-set show command OK


También puede usar nslookup u otras herramientas DNS para consultar el nuevo conjunto de registros.

>[AZURE.NOTE]Al crear la zona, si aún no ha delegado el dominio en los servidores de nombres de DNS de Azure, será necesario especificar explícitamente la dirección del servidor de nombres de la zona.


	C:\> nslookup www.contoso.com ns1-01.azure-dns.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	Name:    www.contoso.com
	Addresses:  134.170.185.46
    	        



## Pasos siguientes
[Administración de zonas DNS](dns-operations-dnszones-cli.md)

[Administración de registros DNS](dns-operations-recordsets-cli.md)<BR>

[Automatización de operaciones de Azure con el SDK de .NET](dns-sdk.md)
 

<!---HONumber=AcomDC_0121_2016-->