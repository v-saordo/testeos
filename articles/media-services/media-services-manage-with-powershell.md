<properties 
	pageTitle="Administración de cuentas de Servicios multimedia de Azure con PowerShell" 
	description="Aprenda a administrar las cuentas de Servicios multimedia de Azure con los cmdlets de PowerShell." 
	authors="Juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="02/03/2016"  
	ms.author="juliako"/>


#Administración de cuentas de Servicios multimedia de Azure con PowerShell

> [AZURE.SELECTOR]
- [Portal](media-services-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [REST](http://msdn.microsoft.com/library/azure/dn194267.aspx)

> [AZURE.NOTE] Para poder crear una cuenta de Servicios multimedia de Azure, debe tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Evaluación gratuita de Azure</a>.

##Información general 

En este artículo se muestra cómo usar los cmdlets de PowerShell para administrar las cuentas de Servicios multimedia de Azure.

>[AZURE.NOTE]
Para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Evaluación gratuita de Azure</a>.

##Instalación de los cmdlets de PowerShell de Microsoft Azure

Para instalar los cmdlets de Azure PowerShell más recientes, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md)

##Selección de la suscripción de Azure

Una vez que se instala y configura los cmdlets de PowerShell, debe especificar en qué suscripción desea trabajar.

Para obtener una lista de suscripciones disponibles, ejecute el siguiente cmdlet:

	PS C:\> Get-AzureSubscription

A continuación, seleccione una realizando los siguientes pasos:

	PS C:\> Select-AzureSubscription "TestSubscription"

 
##Obtención de un nombre de cuenta de almacenamiento

Servicios multimedia de Azure usa Almacenamiento de Azure para almacenar contenido multimedia. Cuando se crea una nueva cuenta de Servicios multimedia, tiene que asociarla a una cuenta de Almacenamiento. La cuenta de Almacenamiento debe pertenecer a la misma suscripción que piensa utilizar para la cuenta de Servicios multimedia.

En este ejemplo, se utiliza una cuenta de almacenamiento existente. El cmdlet [Get-AzureStorageAccount](https://msdn.microsoft.com/library/azure/dn495134.aspx) obtiene cuentas de almacenamiento en la suscripción actual. Obtenga el nombre (StorageAccountName) de la cuenta de almacenamiento que desee asociar a la cuenta multimedia.

	StorageAccountDescription : 
	AffinityGroup             :
	Location                  : East US
	GeoReplicationEnabled     : True
	GeoPrimaryLocation        : East US
	GeoSecondaryLocation      : West US
	Label                     : storagetest001
	StorageAccountStatus      : Created
	StatusOfPrimary           : Available
	StatusOfSecondary         : Available
	Endpoints                 : {https://storagetest001.blob.core.windows.net/,
	                            https://storagetest001.queue.core.windows.net/,
	                            https://storagetest001.table.core.windows.net/}
	AccountType               : Standard_GRS
	StorageAccountName        : storatetest001
	OperationDescription      : Get-AzureStorageAccount
	OperationId               : e919dd56-7691-96db-8b3c-2ceee891ae5d
	OperationStatus           : Succeeded

##Creación de una nueva cuenta de Servicios multimedia

Para crear una nueva cuenta de Servicios multimedia de Azure, use el cmdlet [New-AzureMediaServicesAccount](https://msdn.microsoft.com/library/azure/dn495286.aspx) proporcionando el nombre de la cuenta de Servicios multimedia, la ubicación del centro de datos donde se creará y el nombre de la cuenta de almacenamiento.


	PS C:\> New-AzureMediaServicesAccount -Name "amstestaccount001" -StorageAccountName "storagetest001" -Location "East US"

##Obtención de cuentas de Servicios multimedia

Una vez creadas una o varias cuentas de Servicios multimedia, puede enumerar la información con [Get-AzureMediaServicesAccount](https://msdn.microsoft.com/library/azure/dn495286.aspx).

	
	PS C:\> Get-AzureMediaServicesAccount
	
	AccountId		Name				State
	---------       ----       			 -----
	xxxxxxxxxx      amstestaccount001   Active

Si proporciona el parámetro Name, obtendrá información más detallada, incluidas las claves de cuenta.

	PS C:\> Get-AzureMediaServicesAccount -Name amstestaccount001

##Generación de claves de acceso de Servicios multimedia de nuevo

Si desea actualizar la clave de acceso primaria o secundaria de Servicios multimedia, use [New-AzureMediaServicesKey](https://msdn.microsoft.com/library/azure/dn495215.aspx). Deberá proporcionar el nombre de cuenta y especificar la clave que desea volver a generar (principal o secundaria).

Especifique un modificador -Force si no desea que PowerShell realice preguntas de confirmación.

	PS C:\> New-AzureMediaServicesKey -Name "amstestaccount001" -KeyType "Primary" -Force

##Eliminación la cuenta de Servicios multimedia

Cuando esté listo para eliminar la cuenta de Servicios multimedia de Azure, use [Remove-AzureMediaServicesAccount](https://msdn.microsoft.com/library/azure/dn495220.aspx).

	PS C:\> Remove-AzureMediaServicesAccount -Name "amstestaccount001" -Force


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 

<!---HONumber=AcomDC_0211_2016-->