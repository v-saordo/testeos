<properties
   pageTitle="Uso de un comando de Azure PowerShell para crear un contenedor vacío de servicios en la nube"
   description="En este artículo se explica cómo crear un contenedor de servicios en la nube y cómo realizar las operaciones de administración relacionadas con un servicio en la nube mediante un script de PowerShell."
   services="cloud-services"
   documentationCenter=".net"
   authors="cawaMS"
   manager="paulyuk" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na"
   ms.date="01/13/2015"
   ms.author="cawa"/>

# Uso de un comando de Azure PowerShell para crear un contenedor vacío de servicios en la nube
1. Instale el cmdlet de Microsoft Azure PowerShell desde la [descarga de Azure PowerShell](http://aka.ms/webpi-azps). Abra un símbolo del sistema de PowerShell. Use [Add-AzureAccount](https://msdn.microsoft.com/library/dn495128.aspx) para iniciar sesión.

> [AZURE.NOTE]Para obtener más instrucciones sobre cómo instalar el cmdlet de PowerShell de Azure y conectarse a la suscripción de Azure, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).

2. **New-AzureService** es el cmdlet para crear un contenedor de servicios en la nube vacío.

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

   Un ejemplo donde se invoca el cmdlet es: ```
New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
```

3. Para obtener más información sobre cómo crear un servicio de nube de Azure, ejecute lo siguiente: ```
Get-help New-AzureService
```

4. Pasos siguientes:

   - Para administrar la implementación del servicio de nube, consulte los comandos [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Remove-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx) y [Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx). Además, consulte [Configuración de servicios de nube](cloud-services-how-to-configure.md)

    - Para publicar el proyecto de servicio de nube en Azure, consulte el código de ejemplo de **PublishCloudService.ps1** de [Entrega continua para Servicios de nube de Azure](cloud-services-dotnet-continuous-delivery.md)
 

<!---HONumber=AcomDC_0114_2016-->