<properties
   pageTitle="Navegación y selección de imágenes de máquina virtual | Microsoft Azure"
   description="Obtenga información sobre cómo determinar el publicador, la oferta y la SKU de las imágenes al crear una máquina virtual de Azure con el modelo de implementación del Administrador de recursos."
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   />

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="command-line-interface"
   ms.workload="infrastructure"
   ms.date="12/08/2015"
   ms.author="rasquill"/>

# Seleccione y navegue por imágenes de máquina virtual de Azure con PowerShell y la CLI de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.




## Tabla de imágenes más usadas


| PublisherName | Oferta | SKU |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| OpenLogic | CentOS | 7 |
| OpenLogic | CentOS | 7\.1 |
| CoreOS | CoreOS | Versión beta |
| CoreOS | CoreOS | Stable |
| MicrosoftDynamicsNAV | DynamicsNAV | 2015 |
| MicrosoftSharePoint | MicrosoftSharePointServer | 2013 |
| Microsoft | Oracle-Database-12c-Weblogic-Server-12c | Standard |
| Microsoft | Oracle-Database-12c-Weblogic-Server-12c | Enterprise |
| MicrosoftSQLServer | WS2012R2 SQL2014 | Enterprise-Optimized-for-DW |
| MicrosoftSQLServer | WS2012R2 SQL2014 | Enterprise-Optimized-for-OLTP |
| Canonical | UbuntuServer | 12\.04.5-LTS |
| Canonical | UbuntuServer | 14\.04.2-LTS |
| Microsoft Windows Server | Windows Server | Centro de datos de 2012 |
| Microsoft Windows Server | Windows Server | Centro de datos de 2012-R2 |
| Microsoft Windows Server | Windows Server | 2008-R2-SP1 |
| Microsoft Windows Server | Windows Server | Windows-Server-Technical-Preview |
| MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials |
| MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 |


## Azure CLI

> [AZURE.NOTE]En este artículo se describe cómo navegar y seleccionar imágenes de máquina virtual, con una instalación reciente de la CLI de Azure o de Azure PowerShell. Como requisito previo, tendrá que cambiar al modo de administrador de recursos. Con la CLI de Azure, especifique el modo escribiendo `azure config mode arm`.

La manera más fácil y rápida de buscar una imagen para usar con `azure vm quick-create` o para crear un archivo de plantilla de grupo de recursos es llamar al comando `azure vm image list` y pasar la ubicación, el nombre del publicador (no distingue mayúsculas de minúsculas) y una oferta (si la conoce). Por ejemplo, la lista siguiente es solo un ejemplo breve (muchas listas son bastante largas) si sabe que "Canonical" es un publicador de la oferta "UbuntuServer".

    azure vm image list westus canonical ubuntuserver
    info:    Executing command vm image list
    warn:    The parameter --sku if specified will be ignored
    + Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
    data:    Publisher  Offer         Sku          Version          Location  Urn
    data:    ---------  ------------  -----------  ---------------  --------  --------------------------------------------------
    data:    canonical  ubuntuserver  12.04-DAILY  12.04.201504201  westus    canonical:ubuntuserver:12.04-DAILY:12.04.201504201
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201302250  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201302250
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201303250  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201303250
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201304150  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201304150
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201305160  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201305160
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201305270  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201305270
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201306030  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201306030
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201306240  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201306240

La columna **Urn** será el formulario que pase a `azure vm quick-create`.

A menudo, sin embargo, aún no se conoce lo que está disponible. En este caso, puede navegar por las imágenes, detectando primero los publicadores mediante `azure vm image list-publishers` y respondiendo a la solicitud de ubicación con la ubicación de centro de datos que vaya a usar para el grupo de recursos. Por ejemplo, a continuación se enumeran todos los publicadores de imágenes en la ubicación Oeste de EE. UU. (pase el argumento de ubicación en minúsculas y sin espacios a partir de las ubicaciones estándar).

    azure vm image list-publishers
    info:    Executing command vm image list-publishers
    Location: westus
    + Getting virtual machine image publishers (Location: "westus")
    data:    Publisher                                       Location
    data:    ----------------------------------------------  --------
    data:    a10networks                                     westus  
    data:    aiscaler-cache-control-ddos-and-url-rewriting-  westus  
    data:    alertlogic                                      westus  
    data:    AlertLogic.Extension                            westus  


Estas listas pueden ser bastante largas, por lo que la lista del ejemplo anterior es simplemente un fragmento. Supongamos que hemos observado que Canonical es, de hecho, un publicador de imágenes en la ubicación Oeste de EE. UU. Ahora puede encontrar sus ofertas mediante una llamada a `azure vm image list-offers` y pasar la ubicación y el publicador cuando se soliciten, como en el ejemplo siguiente:

    azure vm image list-offers
    info:    Executing command vm image list-offers
    Location: westus
    Publisher: canonical
    + Getting virtual machine image offers (Publisher: "canonical" Location:"westus")
    data:    Publisher  Offer         Location
    data:    ---------  ------------  --------
    data:    canonical  UbuntuServer  westus  
    info:    vm image list-offers command OK

Ahora ya sabemos que, en la región Oeste de EE. UU., Canonical publica la oferta **UbuntuServer** en Azure. Pero, ¿con qué SKU? Para obtenerlas, se llama a `azure vm image list-skus` y se responde a la solicitud con la ubicación, el publicador y la oferta que haya encontrado.

    azure vm image list-skus
    info:    Executing command vm image list-skus
    Location: westus
    Publisher: canonical
    Offer: ubuntuserver
    + Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
    data:    Publisher  Offer         sku          Location
    data:    ---------  ------------  -----------  --------
    data:    canonical  ubuntuserver  12.04-DAILY  westus  
    data:    canonical  ubuntuserver  12.04.2-LTS  westus  
    data:    canonical  ubuntuserver  12.04.3-LTS  westus  
    data:    canonical  ubuntuserver  12.04.4-LTS  westus  
    data:    canonical  ubuntuserver  12.04.5-LTS  westus  
    data:    canonical  ubuntuserver  12.10        westus  
    data:    canonical  ubuntuserver  14.04-beta   westus  
    data:    canonical  ubuntuserver  14.04-DAILY  westus  
    data:    canonical  ubuntuserver  14.04.0-LTS  westus  
    data:    canonical  ubuntuserver  14.04.1-LTS  westus  
    data:    canonical  ubuntuserver  14.04.2-LTS  westus  
    data:    canonical  ubuntuserver  14.10        westus  
    data:    canonical  ubuntuserver  14.10-beta   westus  
    data:    canonical  ubuntuserver  14.10-DAILY  westus  
    data:    canonical  ubuntuserver  15.04        westus  
    data:    canonical  ubuntuserver  15.04-beta   westus  
    data:    canonical  ubuntuserver  15.04-DAILY  westus  
    info:    vm image list-skus command OK

Con esta información, ahora puede buscar con precisión la imagen que desee mediante una llamada a la llamada original en la parte superior.

    azure vm image list westus canonical ubuntuserver 14.04.2-LTS
    info:    Executing command vm image list
    + Getting virtual machine images (Publisher:"canonical" Offer:"ubuntuserver" Sku: "14.04.2-LTS" Location:"westus")
    data:    Publisher  Offer         Sku          Version          Location  Urn
    data:    ---------  ------------  -----------  ---------------  --------  --------------------------------------------------
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.201503090  westus    canonical:ubuntuserver:14.04.2-LTS:14.04.201503090
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.20150422   westus    canonical:ubuntuserver:14.04.2-LTS:14.04.20150422
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.201504270  westus    canonical:ubuntuserver:14.04.2-LTS:14.04.201504270
    info:    vm image list command OK

Ahora puede elegir con precisión la imagen que desea utilizar. Para crear una máquina virtual rápidamente con la información de URN que acaba de encontrar, o para usar una plantilla con esa información de URN, vea [Uso de la interfaz de la línea de comandos entre plataformas de Azure con el Administrador de recursos de Azure](xplat-cli-azure-resource-manager.md).

### Tutorial en vídeo

Este vídeo muestra los pasos anteriores con la CLI.

[AZURE.VIDEO resource-groups-vm-searching-cli]


## PowerShell

Con PowerShell, escriba `Switch-AzureMode AzureResourceManager`. Vea [Uso de la CLI de Azure con el Administrador de recursos](xplat-cli-azure-resource-manager.md) y [Uso de Azure PowerShell con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md) para más información completa sobre la configuración y actualización.

> [AZURE.NOTE]Con los módulos de Azure PowerShell posteriores a la versión 1.0, se ha quitado el cmdlet `Switch-AzureMode`. Con esa versión y más reciente, reemplace los siguientes comandos con la posición `Azure` reemplazada por `AzureRm`. Si usa los módulos de Azure PowerShell inferiores a la versión 1.0, usará los comandos siguiente, pero primero deberá `Switch-AzureMode AzureResourceManager`.


Al crear una nueva máquina virtual con el Administrador de recursos de Azure, en algunos casos deberá especificar una imagen con la combinación de las propiedades de imagen siguientes:

- Publicador
- Oferta
- SKU

Por ejemplo, estos valores son necesarios para el cmdlet **Set-AzureVMSourceImage** de PowerShell o con un archivo de plantilla de grupo de recursos en el que debe especificar el tipo de máquina virtual que se creará.

Si necesita determinar estos valores, puede explorar las imágenes para determinar estos valores:

1. Listado de los publicadores de imágenes.
2. Para un publicador determinado, enumeración de sus ofertas.
3. Para una oferta determinada, enumeración de sus SKU.

Para hacer esto en PowerShell, en primer lugar cambie al modo de Administrador de recursos de Azure PowerShell.

	Switch-AzureMode AzureResourceManager

Para el primer paso, enumere los publicadores con estos comandos.

	$locName="<Azure location, such as West US>"
	Get-AzureVMImagePublisher -Location $locName | Select PublisherName

Rellene el nombre del publicador elegido y ejecute estos comandos.

	$pubName="<publisher>"
	Get-AzureVMImageOffer -Location $locName -Publisher $pubName | Select Offer

Rellene el nombre de la oferta elegida y ejecute estos comandos.

	$offerName="<offer>"
	Get-AzureVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

En la pantalla del comando **Get-AzureVMImageSku** tiene toda la información que necesita para especificar la imagen para una nueva máquina virtual.

Aquí tiene un ejemplo.

	PS C:\> $locName="West US"
	PS C:\> Get-AzureVMImagePublisher -Location $locName | Select PublisherName

	PublisherName
	-------------
	a10networks
	aiscaler-cache-control-ddos-and-url-rewriting-
	alertlogic
	AlertLogic.Extension
	Barracuda.Azure.ConnectivityAgent
	barracudanetworks
	basho
	boxless
	bssw
	Canonical
	...

Para el publicador de "MicrosoftWindowsServer":

	PS C:\> $pubName="MicrosoftWindowsServer"
	PS C:\> Get-AzureVMImageOffer -Location $locName -Publisher $pubName | Select Offer

	Offer
	-----
	WindowsServer

Para la oferta "WindowsServer":

	PS C:\> $offerName="WindowsServer"
	PS C:\> Get-AzureVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

	Skus
	----
	2008-R2-SP1
	2012-Datacenter
	2012-R2-Datacenter
	Windows-Server-Technical-Preview

En esta lista, copie el nombre de SKU elegido y tiene toda la información para el cmdlet de PowerShell **Set-AzureVMSourceImage** o para un archivo de plantilla de grupo de recursos que requiere que especifique el publicador, la oferta y la SKU de una imagen.

### Tutorial en vídeo

Este vídeo muestra los pasos anteriores con PowerShell.

[AZURE.VIDEO resource-groups-vm-searching-posh]


<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/

<!---HONumber=AcomDC_1217_2015-->