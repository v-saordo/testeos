<properties
   pageTitle="Creación de un conjunto de registros y registros para una zona DNS | Microsoft Azure"
   description="Creación de registros host para DNS de Azure. Configuración de conjuntos de registros y registros mediante PowerShell"
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


# Creación de registros DNS


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

En el ejemplo siguiente se mostrará cómo crear registros y un conjunto de registros. Vamos a usar el tipo de registro DNS 'A'; para otros tipos de registros, consulte [Administración de registros DNS](dns-operations-recordsets.md)


### Paso 1

Cree un conjunto de registros y asígnelo a una variable $rs:

	PS C:\>$rs = New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60

El conjunto de registros tiene el nombre relativo “www” en la zona DNS “contoso.com”, por lo que el nombre completo de los registros será “www.contoso.com”. El tipo de registro es “A” y el valor de TTL es de 60 segundos.

>[AZURE.NOTE]Para crear un conjunto de registros en el vértice de la zona (en este caso, "contoso.com"), utilice el nombre de registro "@", incluidas las comillas. Esta es una convención común de DNS.

El conjunto de registros está vacío y tenemos que agregar registros para poder usar el conjunto de registros “www” recién creado.<BR>

### Paso 2

Agregue registros A de IPv4 al conjunto de registros “www” con la variable $rs asignada al crear el conjunto de registros en el paso 1:

	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.185.46
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.188.221

Agregar registros a un conjunto de registros con Add-AzureRmDnsRecordConfig es una operación sin conexión. Se actualiza únicamente la variable local $rs.

### Paso 3
Confirme los cambios introducidos en el conjunto de registros. Use Set-AzureRmDnsRecordSet para cargar los cambios en el conjunto de registros de DNS de Azure:


	Set-AzureRmDnsRecordSet -RecordSet $rs

Los cambios se han completado. Puede recuperar el conjunto de registros de DNS de Azure con Get-AzureRmDnsRecordSet:


	PS C:\> Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup


	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : MyAzureResourceGroup
	Ttl               : 3600
	Etag              : 68e78da2-4d74-413e-8c3d-331ca48246d9
	RecordType        : A
	Records           : {134.170.185.46, 134.170.188.221}
	Tags              : {}



También puede usar nslookup u otras herramientas DNS para consultar el nuevo conjunto de registros.

>[AZURE.NOTE]Al crear la zona, si aún no ha delegado el dominio en los servidores de nombres de DNS de Azure, será necesario especificar explícitamente la dirección del servidor de nombres de la zona.


	C:\> nslookup www.contoso.com ns1-01.azure-dns.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	Name:    www.contoso.com
	Addresses:  134.170.185.46
    	        134.170.188.221



## Pasos siguientes

[Administración de zonas DNS](dns-operations-dnszones.md)

[Administración de registros DNS](dns-operations-recordsets.md)<BR>

[Automatización de operaciones de Azure con el SDK de .NET](dns-sdk.md)
 

<!---HONumber=AcomDC_1125_2015-->