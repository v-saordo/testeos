<properties
	pageTitle="Enlace de certificados SSL mediante PowerShell"
	description="Aprenda a enlazar certificados SSL a su aplicación web mediante PowerShell."
	services="app-service\web"
	documentationCenter=""
	authors="ahmedelnably"
	manager="stefsch"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/13/2016"
	ms.author="ahmedelnably"/>

# Enlace de certificados SSL con Servicio de aplicaciones de Azure mediante PowerShell #

Con el lanzamiento de Microsoft Azure PowerShell versión 1.1.0, se ha agregado un nuevo cmdlet que proporciona al usuario la capacidad de enlazar certificados SSL nuevos o existentes a una aplicación web existente.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]



## Carga y enlace de un nuevo certificado SSL ##

Escenario: El usuario desea enlazar un certificado SSL a una de sus aplicaciones web.

Conociendo el nombre del grupo de recursos que contiene la aplicación web, el nombre de la aplicación web, la ruta del archivo .pfx de certificado en el equipo del usuario, la contraseña para el certificado y el nombre de host personalizado, podemos usar el siguiente comando de PowerShell para crear ese enlace SSL:

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -CertificateFilePath PathToPfxFile -CertificatePassword PlainTextPwd -Name www.contoso.com

Tenga en cuenta que, antes de agregar un enlace SSL a una aplicación web, ya debe tener configurado un nombre de host (dominio personalizado). Si el nombre de host no está configurado, se producirá un error que indica que el nombre de host no existe al ejecutar New-AzureRmWebAppSSLBinding. Puede agregar un nombre de host directamente desde el portal o mediante Azure PowerShell. Puede usar el siguiente fragmento de PowerShell para configurar el nombre de host antes de ejecutar New-AzureRmWebAppSSLBinding.
  
    $webApp = Get-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup  
    $hostNames = $webApp.HostNames  
    $HostNames.Add("www.contoso.com")  
    Set-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup -HostNames $HostNames   
  
Es importante comprender que el cmdlet Set-AzureRmWebApp sobrescribe los nombres de host de la aplicación web. Por lo tanto, el fragmento de PowerShell anterior se anexa a la lista existente de nombres de host de la aplicación web.

## Carga y enlace de un certificado SSL existente ##

Escenario: El usuario desea enlazar un certificado SSL ya cargado a una de sus aplicaciones web.

Podemos obtener la lista de certificados que ya se han cargado a un grupo de recursos específico mediante el siguiente comando:

	Get-AzureRmWebAppCertificate -ResourceGroupName myresourcegroup

Tenga en cuenta que los certificados son locales a una ubicación y un grupo de recursos específicos; el usuario deberá volver a cargar el certificado si la aplicación web configurada está en una ubicación y un grupo de recursos distintos de los del certificado necesario.

Conociendo el nombre del grupo de recursos que contiene la aplicación web, el nombre de la aplicación web, la huella digital del certificado y el nombre de host personalizado, podemos usar el siguiente comando de PowerShell para crear ese enlace SSL:

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Thumbprint <certificate thumbprint> -Name www.contoso.com

## Eliminación de un enlace SSL existente  ##

Escenario: El usuario desea eliminar un enlace SSL existente.

Conociendo el nombre del grupo de recursos que contiene la aplicación web, el nombre de la aplicación web y el nombre de host personalizado, podemos usar el siguiente comando de PowerShell para quitar ese enlace SSL:

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com

Tenga en cuenta que si el enlace SSL quitado era el último que usaba ese certificado en esa ubicación, se eliminará de forma predeterminada el certificado; si el usuario desea mantener el certificado, puede usar la opción DeleteCertificate para conservarlo.

	Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com -DeleteCertificate $false

### Referencias ###
- [Introducción al entorno del Servicio de aplicaciones](app-service-app-service-environment-intro.md)
- [Uso de Azure PowerShell con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md)

<!---HONumber=AcomDC_0218_2016-->