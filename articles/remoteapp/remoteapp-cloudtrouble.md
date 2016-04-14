
<properties
    pageTitle="Solución de problemas con las colecciones en la nube de RemoteApp: creación | Microsoft Azure"
    description="Obtenga información sobre cómo solucionar problemas con la creación de las colecciones en la nube de RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="vkbucha"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/05/2016"
    ms.author="vikbucha" />



# Solución de problemas de creación de colecciones en la nube de RemoteApp

Si tiene problemas para crear una colección en la nube, consulte la siguiente información.

## La imagen no es válida ##
Si ve un mensaje como "GoldImageInvalid" cuando esté esperando a que Azure aprovisione la colección, la imagen de plantilla no cumple [los requisitos definidos para la imagen](remoteapp-imagereqs.md). Por lo tanto, consulte los [requisitos](remoteapp-imagereqs.md), corrija la imagen y pruebe a crear la colección de nuevo.

## Errores comunes vistos en el Portal de administración de Azure

	DNS server could not be reached
	ProvisioningTimeout

Las colecciones en la nube a menudo presentan errores durante la creación debido a que se usan imágenes personalizadas. Si ve uno de los anteriores errores y usa una imagen personalizada para crear la colección, revise lo siguiente:

- Asegúrese de que la imagen personalizada que cargó cumple con los requisitos de imagen.
- Con frecuencia, el problema común es que la imagen no está preparada con sysprep correctamente.  
- Compruebe que la imagen puede arrancar dentro de Hyper-V o intente crear una máquina virtual de IAAS directamente en su suscripción de Azure con la imagen. Si la máquina virtual no arranca y no se inicia, esto normalmente indica que la imagen personalizada no se preparó correctamente. Compruebe que la imagen personalizada se creó según lo establecido en Creación de una imagen de plantilla personalizada para RemoteApp.

Si usa una de las imágenes de Microsoft incluidas en su suscripción, intente crear nuevamente la colección. Si el problema persiste, póngase en contacto con Soporte técnico de Microsoft.

	PlatformImageTrialModeOnly

Si recibe este error, normalmente significa que ha actualizado a una cuenta de pago pero intenta usar una imagen proporcionada por Microsoft que solo es válida durante el modo de prueba del servicio. En este caso, intente crear nuevamente la colección en la nube, pero asegúrese de especificar la imagen correcta.

<!---HONumber=AcomDC_0211_2016-->