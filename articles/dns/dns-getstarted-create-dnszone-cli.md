<properties
   pageTitle="Introducción a DNS de Azure con CLI | Microsoft Azure"
   description="Aprenda a crear zonas DNS para DNS de Azure paso a paso para empezar a hospedar el dominio DNS con la CLI."
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="joaoma"/>

# Introducción a DNS de Azure con CLI


> [AZURE.SELECTOR]
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)
- [PowerShell](dns-getstarted-create-dnszone.md)


El dominio "contoso.com" puede contener una serie de registros DNS como "mail.contoso.com" (para un servidor de correo) y "www.contoso.com" (para un sitio web). Una zona DNS se usa para hospedar los registros DNS de un dominio concreto.

Para iniciar el hospedaje de un dominio, primero debe crear una zona DNS. Todos los registros DNS creados para un dominio concreto se ubicarán dentro de una zona DNS del dominio.

En estas instrucciones se usa la CLI de Microsoft Azure. Asegúrese de actualizar a la CLI de Azure más reciente para utilizar los comandos de DNS de Azure.

## Configuración de la CLI de Azure

### Paso 1

Instale la CLI de Azure. Puede instalar la CLI de Azure para Windows, Linux o MAC. Para poder administrar DNS de Azure con la CLI de Azure, es necesario completar estos pasos. Puede consultar mas información disponible en [Instalación de la CLI de Azure](../xplat-cli-install.md). Puede encontrar todos los comandos para el proveedor de la red en CLI con el comando siguiente:

	Azure network


>[AZURE.IMPORTANT] Los comandos DNS requieren la CLI de Azure versión 0.9.8 o superior. Escriba `azure -v` para comprobar qué versión de la CLI de Azure está instalada en su equipo.
 
### Paso 2

La DNS de Azure usa el Administrador de recursos de Azure. Asegúrese de cambiar la CLI para usar DNS y los comandos ARM.

	Azure config mode arm

### Paso 3

Inicie sesión en la cuenta de Azure.

    Azure login -u "username"

Se le pedirá que se autentique con sus credenciales. Tenga en cuenta que solo puede usar cuentas de ORGID.

### Paso 4
Elección de la suscripción de Azure que se va a usar.

    Azure account set "subscription name"

Para ver una lista de las suscripciones disponibles, utilice el comando "azure account list".

### Paso 5

Creación de un grupo de recursos (omitir este paso si se usa un grupo de recursos existente)

    Azure group create -n myresourcegroup --location "West US"

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Sin embargo, puesto que todos los recursos DNS son globales y no regionales, la elección de la ubicación del grupo de recursos no incide en DNS de Azure.

### Paso 6

El proveedor de recursos Microsoft.Network administra el servicio DNS de Azure. La suscripción a Azure debe estar registrada para usar este proveedor de recursos antes de utilizar DNS de Azure. Se trata de una operación única para cada suscripción.

	Azure provider register --namespace Microsoft.Network

## Etiquetas

Las etiquetas son diferentes de las Etag. Las etiquetas son una lista de pares nombre-valor que el Administrador de recursos de Azure utiliza para etiquetar los recursos con fines de facturación o agrupación. Para obtener más información sobre las etiquetas, consulte [Uso de etiquetas para organizar los recursos de Azure](resource-group-using-tags.md). La CLI de DNS de Azure es compatible con las etiquetas en las zonas y en los conjuntos de registros especificados mediante el parámetro "-Tag" opcional. En el ejemplo siguiente se muestra cómo crear una zona DNS con dos etiquetas, “project = demo” y “env = test”:

	Azure network dns zone create -n contoso.com -g myresourcegroup -t "project=demo";"env=test"

## Creación de una zona DNS

Una zona DNS se crea con el comando `azure network dns zone create`. En el ejemplo siguiente, se crea una zona DNS denominada "contoso.com" en el grupo de recursos denominado "MyResourceGroup":

    Azure network dns zone create -n contoso.com -g myresourcegroup


>[AZURE.NOTE] En DNS de Azure, deben especificarse nombres de zona sin una terminación '.', por ejemplo, "contoso.com" en lugar de "contoso.com.".


Ya se ha creado la zona DNS en DNS de Azure. Al crear una zona DNS, también se crean los siguientes registros DNS:

- El registro “Inicio de autoridad” (SOA). Se encuentra en la raíz de cada zona DNS.
- Los registros de servidor de nombres (NS) autoritativo. Estos muestran qué servidores de nombres hospedan la zona. DNS de Azure usa un grupo de servidores de nombres, por lo que se pueden asignar diferentes servidores de nombres a zonas distintas en DNS de Azure. Consulte [Delegación de un dominio a DNS de Azure](dns-domain-delegation.md) para obtener más información.

Para ver estos registros, utilice "azure network dns-record-set show":

	Usage: network dns record-set show <resource-group> <dns-zone-name> <name> <type>


En el ejemplo siguiente, al ejecutar el comando con el grupo de recursos "myresourcegroup", el nombre del conjunto de registros "@" (para un registro raíz) y el tipo "SOA" se produce el siguiente resultado:
 

	azure network dns record-set show myresourcegroup "contoso.com" "@" SOA
	info:    Executing command network dns-record-set show
	+ Looking up the DNS record set "@"
	data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/SOA
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    SOA record:
	data:      Email                         : msnhst.microsoft.com
	data:      Expire time                   : 604800
	data:      Host                          : edge1.azuredns-cloud.net
	data:      Minimum TTL                   : 300
	data:      Refresh time                  : 900
	data:      Retry time                    : 300
	data:                                    :
<BR> Para ver los registros NS creados, use el comando siguiente:

	azure network dns record-set show myresourcegroup "contoso.com" "@" NS
	info:    Executing command network dns-record-set show
	+ Looking up the DNS record set "@"
	data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/NS
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    NS records
	data:        Name server domain name     : ns1-05.azure-dns.com
	data:        Name server domain name     : ns2-05.azure-dns.net
	data:        Name server domain name     : ns3-05.azure-dns.org
	data:        Name server domain name     : ns4-05.azure-dns.info
	data:
	info:    network dns-record-set show command OK

>[AZURE.NOTE] Los conjuntos de registros de la raíz (o la "cúspide") de una zona DNS usan "@" como el nombre del conjunto de registros.

Tras haber creado la primera zona DNS, puede probarla con herramientas DNS, como nslookup, DIG o el **cmdlet Resolve-DnsName de PowerShell**. Si aún no ha delegado el dominio para usar la nueva zona DNS en Azure, deberá dirigir la consulta de DNS directamente a uno de los servidores de nombres de la zona. Los servidores de nombres de su zona se proporcionan en los registros de NS, como se muestra con el comando "azure network dns-record-set show" mencionado anteriormente. Asegúrese de sustituir los valores correctos de su zona en el comando que aparece a continuación.

En el ejemplo siguiente se usa DIG para consultar el dominio contoso.com con los servidores de nombres asignados para la zona DNS. La consulta tiene que señalar a un servidor de nombres para el que se usó `@<name server for the zone>` y el nombre de zona con DIG.

	 <<>> DiG 9.10.2-P2 <<>> @ns1-05.azure-dns.com contoso.com
	(1 server found)
	global options: +cmd
 	Got answer:
	->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
 	flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
 	WARNING: recursion requested but not available

 	OPT PSEUDOSECTION:
 	EDNS: version: 0, flags:; udp: 4000
  	QUESTION SECTION:
	contoso.com.                        IN      A

 	AUTHORITY SECTION:
	contoso.com.         300     IN      SOA     edge1.azuredns-cloud.net.
	msnhst.microsoft.com. 6 900 300 604800 300

	Query time: 93 msec
	SERVER: 208.76.47.5#53(208.76.47.5)
	WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
	MSG SIZE  rcvd: 120

## Pasos siguientes

Después de crear una zona DNS, deberá crear [conjuntos de registros y registros](dns-getstarted-create-recordset-cli.md) para iniciar la resolución de nombres para el dominio de Internet.<BR> También puede aprender a [ administrar zonas DNS](dns-operations-dnszones-cli.md) y sus operaciones.<BR> Aprenda a [ administrar registros DNS](dns-operations-recordsets-cli.md) y [automatizar operaciones de Azure con el SDK de .NET](dns-sdk.md)<BR> [Referencia de la API de REST a DNS de Azure.](https://msdn.microsoft.com/library/azure/mt163862.aspx)

<!---HONumber=AcomDC_0218_2016-->