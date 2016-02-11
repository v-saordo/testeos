<properties 
   pageTitle="Automatización de operaciones de conjuntos de registros y DNS con el SDK de .NET | Microsoft Azure" 
   description="Uso del SDK de .NET para automatizar todas las operaciones DNS para DNS de Azure." 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/19/2015"
   ms.author="joaoma"/>
# Creación de conjuntos de registros y zonas DNS con el SDK de .NET
Puede automatizar las operaciones para crear, eliminar o actualizar zonas, conjuntos de registros y registros DNS mediante el SDK de DNS con la biblioteca de administración de DNS de .NET. Un proyecto completo de Visual Studio se encuentra disponible [aquí](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip).

## Paquetes de NuGet y declaraciones de espacio de nombres
Para poder utilizar el cliente DNS, es necesario instalar el paquete de NuGet “Biblioteca de administración de DNS de Azure” y agregar los espacios de nombres de administración de DNS al proyecto. Vaya a Visual Studio, abra un proyecto existente o un proyecto nuevo y vaya a Herramientas en la consola del Administrador de paquetes de NuGet. Descargue la biblioteca de administración de DNS de Azure:

	using Microsoft.Azure;
	using Microsoft.Azure.Management.Dns;
	using Microsoft.Azure.Management.Dns.Models;

## Inicialización del cliente de administración de DNS

El cliente de administración de DNS contiene los métodos y las propiedades necesarios para administrar los conjuntos de registros y las zonas DNS. Para que el cliente pueda tener acceso a la suscripción, es necesario configurar los permisos correctos y generar un token AWT; consulte "Autenticación de solicitudes del Administrador de recursos de Azure" para obtener más información.

	// get a token for the AAD application (see linked article for code)
	string jwt = GetAToken();

	// make the TokenCloudCredentials using subscription ID and token
	TokenCloudCredentials tcCreds = new TokenCloudCredentials(subID, jwt);

	// make the DNS management client
	DnsManagementClient dnsClient = new DnsManagementClient(tcCreds);

## Creación o actualización de una zona DNS

Para crear una zona DNS, se crea un objeto de zona y se pasa a dnsClient.Zones.CreateOrUpdate. Como las zonas DNS no están vinculadas a una región específica, la ubicación se establece en "global".<BR>

Creación de una zona DNS:

	// create a DNS zone
	Zone z = new Zone("global");
	z.Properties = new ZoneProperties();
	z.Tags.Add("dept", "shopping");
	z.Tags.Add("env", "production");
	ZoneCreateOrUpdateParameters zoneParams = new ZoneCreateOrUpdateParameters(z);
	ZoneCreateOrUpdateResponse responseCreateZone = 
	dnsClient.Zones.CreateOrUpdate("myresgroup", "myzone.com", zoneParams);


DNS de Azure admite la simultaneidad optimista denominada [Etags](dns-getstarted-create-dnszone.md#Etags-and-tags). La Etag es una propiedad de la zona e IfNoneMatch es una propiedad de ZoneCreateOrUpdateParameters.

## Creación o actualización de registros DNS
Los registros DNS se administran como un conjunto de registros. Un conjunto de registros es el conjunto de registros con el mismo nombre y el tipo de registro dentro de una zona. Para crear o actualizar un conjunto de registros, se crea un objeto RecordSet y se pasa a dnsClient.RecordSets.CreateOrUpdate. Tenga en cuenta que el nombre del conjunto de registros es relativo al nombre de la zona en lugar de ser el nombre DNS completo. Una vez más, la ubicación se establece en "global".
    
Creación de algunos conjuntos de registros

	// make some records sets
	RecordSet rsWwwA = new RecordSet("global");
	rsWwwA.Properties = new RecordProperties(3600);
	rsWwwA.Properties.ARecords = new List<ARecord>();
	rsWwwA.Properties.ARecords.Add(new ARecord("1.2.3.4"));
	rsWwwA.Properties.ARecords.Add(new ARecord("1.2.3.5"));
	RecordCreateOrUpdateParameters recordParams = 
								new RecordCreateOrUpdateParameters(rsWwwA);
	RecordCreateOrUpdateResponse responseCreateA = 
								dnsClient.RecordSets.CreateOrUpdate("myresgroup", 
	"myzone.com", "www", RecordType.A, recordParams);
	
    
DNS de Azure admite la simultaneidad optimista [Etags](dns-getstarted-create-dnszone.md#Etags-and-tags). La Etag es que una propiedad del conjunto de registros e IfNoneMatch es una propiedad de RecordSetCreateOrUpdateParameters.

## Obtener zonas y conjuntos de registros
Las colecciones de conjuntos de registros y zonas ofrecen la posibilidad de obtener conjuntos de registros y zonas, respectivamente Los conjuntos de registros se identifican por su tipo y su nombre y por la zona (y el grupo de recursos) en que se encuentran. Las zonas se identifican por su nombre y el grupo de recursos en que se encuentran.

	ZoneGetResponse getZoneResponse = 
	dnsClient.Zones.Get("myresgroup", "myzone.com");
	RecordGetResponse getRSResp = 
	dnsClient.RecordSets.Get("myresgroup", 
	"myzone.com", "www", RecordType.A);

##Enumeración de zonas y conjuntos de registros

Para mostrar las zonas, utilice el método List en la colección Zones. Para mostrar los conjuntos de registros utilice los métodos List o ListAll en la colección RecordSets. El método List difiere del método ListAll en que sólo devuelve los conjuntos de registros del tipo especificado.

En el ejemplo siguiente se ilustra cómo obtener una lista de zonas y conjuntos de registros DNS:


	ZoneListResponse zoneListResponse = dnsClient.Zones.List("myresgroup", new ZoneListParameters());
	foreach (Zone zone in zoneListResponse.Zones)
	{
    	RecordListResponse recordSets = 
                 			dnsClient.RecordSets.ListAll("myresgroup", "myzone.com", new RecordSetListParameters());

    // do something like write out each record set
	}
## Pasos siguientes

[Proyecto de ejemplo del SDK de Visual Studio](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip)

<!---HONumber=AcomDC_1125_2015-->