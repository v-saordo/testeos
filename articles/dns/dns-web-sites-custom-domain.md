<properties 
   pageTitle="Creación de registros DNS personalizados para una aplicación web | Microsoft Azure" 
   description="Creación de registros DNS de dominios personalizados para aplicaciones web mediante DNS de Azure. Paso a paso para verificar la propiedad del dominio mediante registros A o CNAME" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="carolz" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/24/2015"
   ms.author="joaoma"/>

# Creación de registros DNS para aplicaciones web en un dominio personalizado

Puede utilizar DNS de Azure para hospedar un dominio personalizado para aplicaciones web. Por ejemplo, imagine que va a crear una aplicación web de Azure y desea que los usuarios obtengan acceso a ella con la utilización de contoso.com o www.contoso.com como FQDN. En este escenario, deberá crear dos registros: un registro A raíz que apunta a contoso.com y un registro CNAME para el nombre World Wide Web, que apunta al registro A.

> [AZURE.NOTE]Tenga en cuenta que si crea un registro A para una aplicación web en Azure, este debe actualizarse manualmente si la dirección IP subyacente de la aplicación web cambia.

Para poder crear registros para el dominio personalizado, debe crear una zona DNS de DNS de Azure y delegar la zona del registrador a DNS de Azure. Para crear una zona DNS, siga los pasos descritos en [Introducción a DNS de Azure](../dns-getstarted-create-dnszone/#Create-a-DNS-zone). Para delegar su DNS a DNS de Azure, siga los pasos descritos en [Delegación de dominios en DNS de Azure](../dns-domain-delegation).
 
## Creación de un registro A para un dominio personalizado

Un registro A se usa para asignar un nombre a su dirección IP. En el ejemplo siguiente, asignaremos @ como un registro A a una dirección IPv4:

### Paso 1
 
Cree un registro y asígnelo a una variable $rs
	
	PS C:\>$rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 

### Paso 2

Agregue el valor IPv4 al conjunto de registros creado previamente “@” con la utilización de la variable $rs asignada. El valor de IPv4 asignado será la dirección IP de la aplicación web.

> [AZURE.NOTE]Para buscar la dirección IP para una aplicación web, siga los pasos descritos en [Configuración de un nombre de dominio personalizado en el Servicio de aplicaciones de Azure](../web-sites-custom-domain-name/#Find-the-virtual-IP-address)

	PS C:\> Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address <your web app IP address>

### Paso 3

Confirme los cambios introducidos en el conjunto de registros. Use Set-AzureRMDnsRecordSet para cargar los cambios al conjunto de registros en DNS de Azure:

	Set-AzureRMDnsRecordSet -RecordSet $rs

## Creación de un registro CNAME para el dominio personalizado

Suponiendo que el dominio ya está administrado por DNS de Azure (consulte [Delegación de dominios DNS](../dns-domain-delegation)), puede utilizar el ejemplo siguiente para crear un registro CNAME para contoso.azurewebsites.net:

### Paso 1

Abra PowerShell y cree un nuevo conjunto de registros CNAME y asígnelo a una variable $rs:

	PS C:\> $rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600
 
	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
	RecordType        : CNAME
	Records           : {}
	Tags              : {}

Esto creará un tipo de conjunto de registros CNAME con un "período de vida" de 600 segundos en la zona DNS denominada "contoso.com".

### Paso 2

Una vez creado el conjunto de registros CNAME, deberá crear un valor de alias que apuntará a la aplicación web.

Mediante la variable asignada previamente "$rs", puede usar el comando de PowerShell siguiente para crear el alias para la aplicación web contoso.azurewebsites.net.

	PS C:\> Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"
 
	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
	RecordType        : CNAME
	Records           : {contoso.azurewebsites.net}
	Tags              : {}

### Paso 3

Confirme los cambios mediante el cmdlet Set-AzureRMDnsRecordSet:

	PS C:\>Set-AzureRMDnsRecordSet -RecordSet $rs

Puede validar que el registro se ha creado correctamente consultando “www.contoso.com” con nslookup, como se muestra a continuación:

	PS C:\> nslookup
	Default Server:  Default
	Address:  192.168.0.1
 
	> www.contoso.com
	Server:  default server
	Address:  192.168.0.1
	 
	Non-authoritative answer:
	Name:    <instance of web app service>.cloudapp.net
	Address:  <ip of web app service>
	Aliases:  www.contoso.com
    contoso.azurewebsites.net
    <instance of web app service>.vip.azurewebsites.windows.net

## Creación de un registro awverify para aplicaciones web (solo registros A)

Si decide usar un registro A para la aplicación web, debe seguir un proceso de verificación para asegurarse de que es el titular del dominio personalizado. Este paso de comprobación se realiza mediante la creación de un registro CNAME especial denominado "awverify".

En el ejemplo siguiente, el registro "awverify" se creará para contoso.com para verificar la titularidad del dominio personalizado:

### Paso 1

	PS C:\> $rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "awverify" -RecordType "CNAME" -Ttl 600
 
	Name              : awverify
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
	RecordType        : CNAME
	Records           : {}
	Tags              : {}


### Paso 2

Una vez creado el conjunto de registros awverify, debe asignar el registro CNAME, establecer el alias para awverify.contoso.azurewebsites.net, como se muestra en el siguiente comando:

	PS C:\> Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"
 
	Name              : awverify
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
	RecordType        : CNAME
	Records           : {awverify.contoso.azurewebsites.net}
	Tags              : {}

### Paso 3

Confirme los cambios con el cmdlet Set-AzureRMDnsRecordSet, como se muestra en el comando siguiente:

	PS C:\>Set-AzureRMDnsRecordSet -RecordSet $rs

Ahora puede continuar con los pasos descritos en [Configurar un nombre de dominio personalizado en el servicio de aplicaciones de Azure](../web-sites-custom-domain-name) para configurar la aplicación web para que use un dominio personalizado.

## Otras referencias

[Administración de zonas DNS](../dns-operations-dnszones)

[Administración de registros DNS](../dns-operations-recordsets)

[Información general sobre el Administrador de tráfico](../traffic-manager-overview)

[Automatización de operaciones de Azure con el SDK de .NET](../dns-sdk)


 

<!---HONumber=AcomDC_1203_2015-->