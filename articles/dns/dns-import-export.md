<properties
   pageTitle="Importación y exportación de un archivo de zona de dominio con DNS de Azure | Microsoft Azure"
   description="Más información sobre cómo importar y exportar un archivo de zona DNS en DNS de Azure mediante la CLI de Azure"
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
   ms.date="01/11/2016"
   ms.author="jonatul"/>

# Importación y exportación de un archivo de zona DNS


Esta guía explica cómo importar y exportar archivos de zona DNS en DNS de Azure.

## Introducción a la migración de zona DNS

Un archivo de zona DNS es un archivo de texto que contiene los detalles de cada registro DNS de la zona. Sigue un formato estándar, lo que es apropiado para transferir los registros DNS entre distintos sistemas DNS. Usar un archivo de zona es una manera rápida, fiable y cómoda de transferir una zona DNS hacia o desde DNS de Azure.

DNS de Azure admite la importación de archivos de zona y la exportación a través de la CLI de Azure. En este artículo se explica cómo usar esta característica.

La CLI de Azure es una herramienta de línea de comandos multiplataforma para administrar servicios de Azure. Está disponible para las plataformas Windows, Mac y Linux en la [página de descargas de Azure](https://azure.microsoft.com/downloads/). Esta compatibilidad multiplataforma es especialmente importante para la importación y exportación de archivos de zona porque el software de servidor de nombre más común, [BIND](https://www.isc.org/downloads/bind/), normalmente se ejecuta en Linux.

## Obtención del archivo de zona DNS existente

Antes de importar el archivo de zona DNS a DNS de Azure, tendrá que obtener una copia del archivo de zona. El origen de este archivo variará en función de dónde se hospede la zona DNS. Por ejemplo:

- Si la zona DNS se hospeda en un servicio de terceros (como un registrador de dominios, un proveedor de servicios de hospedaje DNS dedicado o un proveedor de nube alternativo), ese servicio debe ofrecer la posibilidad de descargar el archivo de zona DNS.
-	Si la zona DNS se hospeda con el servidor DNS de Windows, los archivos de zona pueden encontrarse de forma predeterminada en la carpeta **'%systemroot%\\system32\\dns'** carpeta. También se muestra la ruta de acceso completa de cada archivo de zona en la pestaña "General" de la consola de administración del servicio DNS.
-	Si la zona DNS se hospeda con BIND, la ubicación del archivo de zona para cada zona se especifica en el archivo de configuración de BIND **'named.conf'**. 

>[AZURE.NOTE]Los archivos de zona descargados de 'GoDaddy' tienen un formato ligeramente no estándar, que es preciso corregir antes de importar los archivos de zona en DNS de Azure. En concreto, los nombres DNS en la sección RDATA de cada registro DNS se especifican como nombres completos, pero sin la terminación '.', lo que significa que otros sistemas DNS los interpretan como nombres relativos. Debe editar el archivo de zona para anexar el carácter final '.' a estos nombres antes de importarlos en DNS de Azure.

## Importación de un archivo de zona DNS a DNS de Azure

El formato del comando de CLI de Azure para importar una zona DNS es como sigue: `azure network dns zone import [options] <resource group> <zone name> <zone file name>` donde:

- **resource group**: es el nombre del grupo de recursos para la zona DNS de Azure.
- **zone name**: es el nombre de la zona.
- **zone file name**: es la ruta de acceso o el nombre del archivo de zona que se va a importar.

Si no existe una zona con este nombre en el grupo de recursos, se creará automáticamente. Si la zona ya existe, los conjuntos de registros importados se combinarán con conjuntos de registros existentes. Para sobrescribir los conjuntos de registros existentes, use la opción '--force' en su lugar. A continuación se ofrece información detallada sobre [cómo se combinan los conjuntos de registros](#merging-with-existing-data).

Para comprobar el formato de un archivo de zona sin importarlo realmente, utilice la opción '--parse-only'.

## Paso a paso para importar un archivo de zona a DNS de Azure

Veamos un ejemplo. Suponga que desea importar un archivo de zona para la zona "contoso.com".

### Paso 1
Inicio de sesión en la suscripción de Azure mediante CLI de Azure

	azure login

### Paso 2
Seleccione la suscripción en la que quiere crear la zona DNS.

	azure account set <subscription name>

### Paso 3
DNS de Azure es un Administrador de recursos de Azure (ARM): solo servicio, CLI de Azure debe cambiarse al modo ARM.

	azure config mode arm

### Paso 4
Antes de usar el servicio DNS de Azure, debe registrar la suscripción para utilizar el proveedor de recursos Microsoft.Network (ésta operación se realiza solo una vez por suscripción).

	azure provider register Microsoft.Network

### Paso 5 
Si todavía no tiene una, debe crear también un grupo de recursos de ARM.
	
	azure group create myresourcegroup westeurope

### Paso 6	
Para importar la zona 'contoso.com' desde el archivo 'contoso.com.txt' a una nueva zona DNS en el grupo de recursos 'myresourcegroup', ejecute el comando `azure network dns zone import`.

	azure network dns zone import myresourcegroup contoso.com contoso.com.txt

Este comando carga el archivo de zona, lo analiza y ejecuta una serie de comandos en el servicio DNS de Azure para crear la zona y todos los conjuntos de registros de la zona.

Notificará el progreso en la ventana de consola, así como los errores o advertencias. Puesto que los conjuntos de registros se crean en serie, puede tardar unos minutos para importar un archivo de zona de gran tamaño.

## Comprobación de la zona DNS después de la importación

Puede mostrar los registros mediante el siguiente comando de CLI de Azure (también puede hacerlo a través de PowerShell mediante `Get-AzureRmDnsRecordSet`).

	azure network dns record-set list myresourcegroup contoso.com

También puede utilizar 'nlookup' para comprobar la resolución de nombres para los registros. Puesto que la zona no está delegada todavía debe especificar explícitamente los servidores de nombres DNS de Azure correctos. En el ejemplo siguiente se muestra cómo recuperar los nombres de servidor de nombre asignados a la zona y consultar el registro 'www' con 'nslookup':

	C:\>azure network dns record-set show myresourcegroup contoso.com @ NS
	info:    Executing command network dns record-set show
	+ Looking up the DNS Record Set "@" of type "NS"
	data:    Id: /subscriptions/…/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/NS
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    NS records
	data:        Name server domain name     : ns1-01.azure-dns.com
	data:        Name server domain name     : ns2-01.azure-dns.net
	data:        Name server domain name     : ns3-01.azure-dns.org
	data:        Name server domain name     : ns4-01.azure-dns.info
	data:
	info:    network dns record-set show command OK

	C:\> nslookup www.contoso.com ns1-01.azure-dns.com

	Server: ns1-01.azure-dns.com
	Address:  40.90.4.1

	Name:    www.contoso.com
	Addresses:  134.170.185.46
            134.170.188.221

Una vez que haya comprobado que la zona se ha importado correctamente, tendrá que [actualizar la delegación DNS](dns-domain-delegation.md) para que apunte a los servidores de nombres DNS de Azure.

## Combinación con datos existentes

Al importar un archivo de zona se creará una nueva zona DNS de Azure si aún no existe. Si la zona ya existe, los conjuntos de registros de la zona deben combinarse con los conjuntos de registros existentes. El comportamiento de combinación es el siguiente:

1.	De forma predeterminada, se combinan los conjuntos de registros nuevos y existentes. Los registros idénticos dentro de un conjunto de registros combinado se desduplican.
2.	También puede especificar la opción '--force' para que el proceso de importación reemplace los conjuntos de registros existentes por nuevos conjuntos de registros. No se quitarán los conjuntos de registros existentes que no tengan un registro correspondiente en el archivo de zona importado.
3.	Cuando se combinan conjuntos de registros, se utiliza el TTL de los conjuntos de registros existentes. Cuando se usa '--force', se utiliza el TTL del nuevo conjunto de registros.
4.	Los parámetros SOA (excepto 'host') siempre se toman del archivo de zona importado, independientemente del uso de '--force'. De forma similar, para el conjunto de registros NS en el ápice de zona, el TTL siempre se toma del archivo de zona importado.
5.	Un registro CNAME importado no reemplazará un registro CNAME existente con el mismo nombre, a menos que se especifique el parámetro '--force'.
6.	Cuando surge un conflicto entre un registro CNAME y otro registro del mismo nombre pero de distinto tipo (independientemente de lo que sea existente y nuevo), se conserva el registro existente. Esto sucede con independencia del uso de '--force'.

## Detalles técnicos adicionales
Las notas siguientes ofrecen detalles técnicos adicionales sobre el proceso de importación de zona:

1.	La directiva $TTL es opcional y se admite. Cuando no se indique ninguna directiva $TTL, los registros sin un TTL explícito se importarán con un TTL predeterminado de 3600 segundos. Cuando dos registros del mismo conjunto de registros especifiquen diferentes TTL, se usa el valor más bajo.
2.	La directiva $ORIGIN es opcional y se admite. Cuando no se establezca ninguna $ORIGIN, el valor predeterminado que se usará es el nombre de la zona según lo especificado en la línea de comandos (más la terminación '.').
3.	No se admiten las directivas $INCLUDE y $GENERATE.
4.	Se admiten los tipos de registro siguientes: A, AAAA, CNAME, MX, NS, SOA, SRV y TXT.  
5.	DNS de Azure crea automáticamente el registro SOA cuando se crea una zona. Al importar un archivo de zona, se toman todos los parámetros SOA del archivo de zona, excepto el parámetro 'host', que utiliza el valor proporcionado por DNS de Azure. Esto se debe a que este parámetro debe hacer referencia al servidor de nombres principal proporcionado por DNS de Azure.
6.	DNS de Azure también crea automáticamente el registro NS establecido en el ápice de zona al crear la zona. Solo se importa el TTL de este conjunto de registros. Estos registros contienen los nombres de servidor de nombres que proporciona DNS de Azure y, por tanto, los valores contenidos en el archivo de zona importado no sobrescriben los datos del registro.
7.	En la vista previa, DNS de Azure solo admite registros TXT de cadena única, por lo que los registros TXT de cadena múltiple se concatenarán y se truncarán a 255 caracteres.

## Exportación de un archivo de zona DNS de DNS de Azure

El formato del comando de CLI de Azure para importar una zona DNS es como sigue: `azure network dns zone export [options] <resource group> <zone name> <zone file name>` donde:

- **resource group**: es el nombre del grupo de recursos para la zona DNS de Azure.
- **zone name**: es el nombre de la zona.
- **zone file name**: es la ruta de acceso o el nombre del archivo de zona que se va a exportar.

## Paso a paso para exportar un archivo de DNS de Azure
 
Como en la importación de zona, hay que iniciar sesión primero, elegir la suscripción y configurar la CLI de Azure para usar el modo 'ARM':

### Paso 1
Inicio de sesión en la suscripción de Azure mediante CLI de Azure

	azure login

### Paso 2
Seleccione la suscripción en la que quiere crear la zona DNS.

	azure account set <subscription name>

### Paso 3
DNS de Azure es un Administrador de recursos de Azure (ARM): solo servicio, CLI de Azure debe cambiarse al modo ARM.

	azure config mode arm
### Paso 4
Para exportar la zona de DNS de Azure existente 'contoso.com' en el grupo de recursos 'myresourcegroup' al archivo 'contoso.com.txt' (en la carpeta actual), ejecute `azure network dns zone export`.

	azure network dns zone export myresourcegroup contoso.com contoso.com.txt

Este comando llamará al servicio DNS de Azure para enumerar los conjuntos de registros de la zona y exportar los resultados a un archivo de zona compatible con BIND.

<!---HONumber=AcomDC_0114_2016-->