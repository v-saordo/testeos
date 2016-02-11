<properties
   pageTitle="Introducción a DNS de Azure | Microsoft Azure"
   description="Obtenga información sobre cómo crear zonas DNS para DNS de Azure. Se trata de instrucciones paso a paso para crear la primera zona DNS para empezar a hospedar sus dominios de DNS con PowerShell."
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

# Introducción a DNS de Azure con PowerShell


> [AZURE.SELECTOR]
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)
- [PowerShell](dns-getstarted-create-dnszone.md)

El dominio “contoso.com” puede contener una serie de registros DNS como “mail.contoso.com” (para un servidor de correo) y “www.contoso.com” (para un sitio web). Una zona DNS se usa para hospedar los registros DNS de un dominio concreto.<BR><BR> Para iniciar el hospedaje de un dominio, primero necesitamos crear una zona DNS. Todos los registros DNS creados para un dominio concreto se ubicarán dentro de una zona DNS del dominio.<BR><BR> En estas instrucciones se usa PowerShell para Microsoft Azure. Asegúrese de actualizar a la versión más reciente de PowerShell para Azure para usar los cmdlets de DNS de Azure. También se pueden ejecutar los mismos pasos mediante la interfaz de línea de comandos, la API de REST o el SDK de Microsoft Azure.<BR><BR>

Para poder administrar DNS de Azure con PowerShell para Azure, es necesario seguir estos pasos.


DNS de Azure usa el Administrador de recursos de Azure (ARM). Siga estas instrucciones para Azure PowerShell 1.0.0 y posteriores. Puede encontrar más información en [Uso de Windows PowerShell con el Administrador de recursos](powershell-azure-resource-manager.md).<BR><BR>


### Paso 1
Inicie sesión en la cuenta de Azure (se le pedirá autenticarse con sus credenciales).

	PS C:\> Login-AzureRmAccount

### Paso 2
Elija la suscripción de Azure que se va a usar.

	PS C:\> Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

Puede mostrar las suscripciones disponibles con 'Get-AzureRmSubscription'.

### Paso 3
Creación de un grupo de recursos (omitir este paso si se usa un grupo de recursos existente)

	PS C:\> New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Sin embargo, puesto que todos los recursos DNS son globales y no regionales, la elección de la ubicación del grupo de recursos no incide en DNS de Azure.

### Paso 4
El proveedor de recursos Microsoft.Network administra el servicio DNS de Azure. La suscripción a Azure debe estar registrada para usar este proveedor de recursos antes de utilizar DNS de Azure. Se trata de una operación única para cada suscripción.

	PS c:> Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network



## ETag y etiquetas

### Etag
Supongamos que dos personas o dos procesos tratan de modificar un registro DNS al mismo tiempo. ¿Cuál gana? ¿Y el ganador sabe que sólo ha sobrescrito cambios creados por otra persona?<BR><BR> DNS de Azure usa Etag para administrar de forma segura los cambios simultáneos realizados al mismo recurso. Cada recurso DNS (zona o conjunto de registros) tiene un valor de Etag asociado a él. Siempre que se recupera un recurso, también se recupera su Etag. Al actualizar un recurso, tiene la opción de devolver el valor de Etag para que DNS de Azure pueda comprobar dicho valor en las coincidencias de servidor. Habida cuenta de que cada actualización a un recurso conlleva la regeneración de Etag, una incoherencia de Etag indica que se ha producido un cambio simultáneo. Los valores de Etag también se usan al crear un recurso para asegurarse de que el recurso no existe aún.

De forma predeterminada, PowerShell para DNS de Azure utiliza Etags para bloquear los cambios simultáneos a zonas y conjuntos de registros. El elemento opcional “-Overwrite” se puede usar para suprimir las comprobaciones de Etag, en cuyo caso, se sobrescribirán todos los cambios simultáneos que se hayan producido.<BR><BR> En el nivel de la API de REST de DNS de Azure, los valores de Etag se especifican mediante los encabezados HTTP. Su comportamiento se indica en la siguiente tabla:

|Encabezado|Comportamiento|
|------|--------|
|None|PUT always succeeds (no Etag checks)|
|If-match <etag>|PUT only succeeds if resource exists and Etag matches|
|If-match * |PUT only succeeds if resources exists|
|If-none-match |* PUT only succeeds if resource does not exist|

### Etiquetas
Las etiquetas son diferentes de las Etag. Las etiquetas son una lista de pares nombre-valor que el Administrador de recursos de Azure utiliza para etiquetar los recursos con fines de facturación o agrupación. Para obtener más información sobre las etiquetas, consulte Uso de etiquetas para organizar los recursos de Azure. PowerShell para DNS de Azure es compatible con etiquetas en las zonas y en los conjuntos de registros especificados mediante el parámetro “-Tag” opcional. En el ejemplo siguiente se muestra cómo crear una zona DNS con dos etiquetas, “project = demo” y “env = test”:

	PS C:\> New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )


## Creación de una zona DNS

Una zona DNS se crea con el cmdlet New-AzureRmDnsZone. En el ejemplo siguiente, se creará una zona DNS denominada "contoso.com" en el grupo de recursos denominado “MyResourceGroup”:<BR>

	PS C:\> New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

>[AZURE.NOTE]En DNS de Azure, deben especificarse nombres de zona sin la terminación “.”. Por ejemplo, "contoso.com" en lugar de "contoso.com.".<BR>


Ya se ha creado la zona DNS en DNS de Azure. Al crear una zona DNS, también se crean los siguientes registros DNS:<BR>



- El registro “Inicio de autoridad” (SOA). Se encuentra en la raíz de cada zona DNS.
- Los registros de servidor de nombres (NS) autoritativo. Estos muestran qué servidores de nombres hospedan la zona. DNS de Azure usa un grupo de servidores de nombres, por lo que se pueden asignar diferentes servidores de nombres a zonas distintas en DNS de Azure. Consulte [Delegación de un dominio a DNS de Azure](dns-domain-delegation.md) para obtener más información.<BR>

Para ver estos registros, use Get-AzureRmDnsRecordSet:

	PS C:\> Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

	Name              : @
	ZoneName          : contoso.com
	ResourceGroupName : MyResourceGroup
	Ttl               : 3600
	Etag              : 2b855de1-5c7e-4038-bfff-3a9e55b49caf
	RecordType        : SOA
	Records           : {[ns1-01.azure-dns.com,msnhst.microsoft.com,900,300,604800,300]}
	Tags              : {}

	Name              : @
	ZoneName          : contoso.com
	ResourceGroupName : MyResourceGroup
	Ttl               : 3600
	Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
	RecordType        : NS
	Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                  ns4-01.azure-dns.info}
	Tags              : {}

>[AZURE.NOTE]Los conjuntos de registros de la raíz (o la “cúspide”) de una zona DNS usan “@” como el nombre del conjunto de registros.


Tras haber creado la primera zona DNS, puede probarla con herramientas DNS, como nslookup, dig o el [cmdlet Resolve-DnsName de PowerShell](https://technet.microsoft.com/library/jj590781.aspx).<BR>

Si aún no ha delegado el dominio para usar la nueva zona DNS en Azure, necesitará dirigir la consulta DNS directamente a uno de los servidores de nombres de la zona. Los servidores de nombres de la zona se ofrecen en los registros NS, como muestra Get-AzureRmDnsRecordSet más arriba. Asegúrese de sustituir los valores correctos de la zona en el siguiente comando.<BR>

		C:\> nslookup
		> set type=SOA
		> server ns1-01.azure-dns.com
		> contoso.com

		Server: ns1-01.azure-dns.com
		Address:  208.76.47.1

		contoso.com
        		primary name server = ns1-01.azure-dns.com
        		responsible mail addr = msnhst.microsoft.com
        		serial  = 1
        		refresh = 900 (15 mins)
        		retry   = 300 (5 mins)
        		expire  = 604800 (7 days)
        		default TTL = 300 (5 mins)

## Pasos siguientes

[Introducción a la creación de registros y conjuntos de registros](dns-getstarted-create-recordset.md)<BR> [Administración de zonas DNS](dns-operations-dnszones.md)<BR> [Administración de registros DNS](dns-operations-recordsets.md)<BR> [Automatización de operaciones de Azure con .NET SDK](dns-sdk.md)<BR> [Referencia de la API de REST del DNS de Azure](https://msdn.microsoft.com/library/azure/mt163862.aspx)
 

<!---HONumber=AcomDC_1210_2015-->