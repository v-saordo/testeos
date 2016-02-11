<properties 
pageTitle="Habilitación de la conexión a Escritorio remoto para un rol de Servicios en la nube de Azure mediante PowerShell" 
description="Configuración de la aplicación de servicios en la nube de Azure con PowerShell para permitir conexiones a Escritorio remoto" 
services="cloud-services" 
documentationCenter="" 
authors="sbtron" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="01/19/2016" 
ms.author="saurabh"/>

# Habilitación de la conexión a Escritorio remoto para un rol de Servicios en la nube de Azure mediante PowerShell

>[AZURE.SELECTOR]
- [Azure classic portal](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)


Escritorio remoto le permite tener acceso al escritorio de un rol que se ejecuta en Azure. Puede usar la conexión de Escritorio remoto para solucionar y diagnosticar problemas con su aplicación mientras se ejecuta.

En este artículo se describe cómo habilitar Escritorio remoto en los roles del servicio en la nube con PowerShell. Para conocer los requisitos previos necesarios para este artículo, consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md). PowerShell usa el enfoque de extensión de Escritorio remoto, por lo que Escritorio remoto se puede habilitar incluso después de que la implementación de la aplicación.


## Configuración de Escritorio remoto desde PowerShell

El cmdlet [Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) permite habilitar Escritorio remoto en los roles especificados o en todos los roles de la implementación del servicio en la nube. Este cmdlet permite especificar el nombre de usuario y la contraseña del usuario de Escritorio remoto a través del parámetro *Credential*, que acepta un objeto PSCredential.

Si PowerShell se usa de forma interactiva, el objeto PSCredential se puede establecer fácilmente mediante una llamada al cmdlet [Get-Credentials](https://technet.microsoft.com/library/hh849815.aspx).

```
	$remoteusercredentials = Get-Credential
```

Se mostrará un cuadro de diálogo que permite especificar el nombre de usuario y la contraseña del usuario remoto de forma segura.

Dado que PowerShell se usará principalmente en escenarios de automatización también es posible configurar el objeto PSCredential de manera que no requiera la interacción del usuario. Para ello, en primer lugar es preciso configurar una contraseña segura. Para empezar, es preciso especificar una contraseña de texto sin formato y convertirla en una cadena segura con [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Seguidamente, hay que convertir esta cadena segura en una cadena estándar cifrada, para lo que se usa [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). Ya se puede guardar la cadena estándar cifrada en un archivo con [Set-Content](https://technet.microsoft.com/library/ee176959.aspx). Al crear el objeto PSCredential es posible leer de él para establecer la contraseña de forma segura sin tener que especificar la contraseña en un símbolo del sistema ni almacenar la contraseña como texto sin formato.

Use la siguiente instrucción de PowerShell para crear un archivo de contraseña seguro:

```
	ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
``` 

Una vez creado el archivo de contraseña (password.txt) solo se usará este archivo, no será preciso especificar la contraseña en texto sin formato. Si necesita actualizar la contraseña, puede volver a ejecutar la instrucción de Powershell anterior con la nueva contraseña para generar un nuevo archivo password.txt.

>[AZURE.IMPORTANT] Al establecer la contraseña asegúrese de cumplir los [requisitos de complejidad](https://technet.microsoft.com/library/cc786468.aspx).

Para crear el objeto de credencial desde el archivo de contraseña seguro debe leer el contenido del archivo y convertirlo en una cadena segura con [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Además de credenciales, el cmdlet [Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) también acepta un parámetro *Expiration* que especifica la fecha y hora en que la cuenta de usuario expirará. Para definirla, especifique una fecha y hora estáticas, o bien puede elegir que la cuenta expire unos días después de la fecha actual.

En este ejemplo de PowerShell se muestra cómo establecer la extensión de Escritorio remoto en un servicio en la nube:

```
	$servicename = "cloudservice"
	$username = "RemoteDesktopUser"
	$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
	$expiry = $(Get-Date).AddDays(1)
	$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
	Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry 
```
Opcionalmente, también puede especificar la ranura de implementación y los roles en que desea habilitar Escritorio remoto. Si no se especifican estos parámetros, de forma predeterminada el cmdlet usará la ranura de implementación de producción y habilitará Escritorio remoto en todos los roles de la implementación de producción.

La extensión de Escritorio remoto se asocia con una implementación. Si desea crear una nueva implementación para el servicio, tendrá que volver a habilitar Escritorio remoto en la nueva implementación. Si desea que Escritorio remoto esté siempre habilitado en las implementaciones, debe considerar la integración de scripts de PowerShell para habilitar Escritorio remoto en el flujo de trabajo de implementación.


## Escritorio remoto en una instancia de rol
El cmdlet [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) se puede usar para introducir Escritorio remoto en una instancia de rol específica del servicio en la nube. Puede usar el parámetro *LocalPath* en el cmdlet para descargar el archivo RDP localmente, o bien puede usar el parámetro *Launch* para iniciar directamente el cuadro de diálogo Conexión a Escritorio remoto para tener acceso a la instancia de rol del servicio en la nube.

```
	Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```


## Comprobación de si la extensión de Escritorio remoto está habilitada en un servicio 
El cmdlet [Get-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495261.aspx) muestra si Escritorio remoto está habilitado en una implementación de servicio. El cmdlet devolverá el nombre de usuario del usuario de Escritorio remoto y los roles en la que está habilitada la extensión de Escritorio remoto. Opcionalmente, puede especificar una ranura de implementación cuyo valor predeterminado es producción.

```
	Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## Eliminación de la extensión de Escritorio remoto de un servicio 
Si ya habilitó la extensión de Escritorio remoto en una implementación y necesita actualizar la configuración de Escritorio remota, primero debe quitar la extensión de Escritorio remoto y, a continuación, habilitarla de nuevo con la nueva configuración. Por ejemplo, si desea establecer una nueva contraseña para la cuenta del usuario remoto o si la cuenta de usuario expiró, tendrá que quitar la extensión y, a continuación, volver a agregarla con la nueva contraseña o expiración. Esto solo se requiere en las implementaciones existentes que tienen la extensión de Escritorio remoto habilitada. En las nuevas implementaciones a las que pueda llamar, simplemente aplique la extensión directamente.

Para quitar la extensión de Escritorio remoto de una implementación de servicio, puede usar el cmdlet [Remove-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495280.aspx). Opcionalmente, también puede especificar la ranura de implementación y los roles de los que desea habilitar la extensión de Escritorio remoto.

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration

```  

>[AZURE.NOTE] El parámetro *UninstallConfiguration* desinstalará cualquier configuración de la extensión que se aplicó al servicio. Toda la configuración de extensión se asocia con la configuración del servicio para activar la extensión con una implementación. La implementación debe estar asociada con la configuración de la extensión. Si se llama al cmdlet Remove sin *UninstallConfiguration* desasociará la implementación de la configuración de la extensión, con lo que se quitará eficazmente la extensión de la implementación. Sin embargo, la configuración de la extensión seguirá estando asociada al servicio. Para quitar completamente la configuración de la extensión se debe llamar al cmdlet Remove con el parámetro *UninstallConfiguration*.



## Recursos adicionales

[Configuración de servicios en la nube](cloud-services-how-to-configure.md)

<!---HONumber=AcomDC_0128_2016-->