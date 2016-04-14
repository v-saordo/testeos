<properties
   pageTitle="Uso de un comando de Azure PowerShell para crear un contenedor vacío de servicios en la nube | Microsoft Azure"
   description="En este artículo se explica cómo crear un contenedor de servicios en la nube y realizar las operaciones de administración relacionadas con un servicio en la nube mediante un script de PowerShell."
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
   ms.date="02/09/2016"
   ms.author="cawa"/>

# Uso de un comando de Azure PowerShell para crear un contenedor vacío de servicios en la nube
En este artículo se explica cómo crear rápidamente un contenedor de servicios en la nube mediante cmdlets de Azure PowerShell. Siga estos pasos:

1. Instale el cmdlet de Microsoft Azure PowerShell desde la página de [descargas de Azure PowerShell](http://aka.ms/webpi-azps).
2. Abra un símbolo del sistema de PowerShell.
3. Use [Add-AzureAccount](https://msdn.microsoft.com/library/dn495128.aspx) para iniciar sesión.

    > [AZURE.NOTE] Para más instrucciones acerca de cómo instalar el cmdlet de Azure PowerShell y conectarse a la suscripción de Azure, consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

4. Use el cmdlet **New-AzureService** para crear un contenedor de servicios en la nube de Azure vacío.

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

5. Para invocar el cmdlet, siga este ejemplo: ```
New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
```

Para más información acerca de cómo crear el servicio en la nube de Azure, ejecute lo siguiente: ```
Get-help New-AzureService
```

### Pasos siguientes

 * Para administrar la implementación de servicios en la nube, consulte los comandos [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Remove-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx) y [Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx). Para más información, también puede consultar [Configuración de servicios en la nube](cloud-services-how-to-configure.md).

 * Para publicar el proyecto de servicio en la nube en Azure, consulte el código de ejemplo de **PublishCloudService.ps1** de [Entrega continua para Servicios en la nube de Azure](cloud-services-dotnet-continuous-delivery.md)

<!---HONumber=AcomDC_0211_2016-->