<properties
	pageTitle="Establecimiento de directivas de caducidad de contraseña en Azure Active Directory | Microsoft Azure"
	description="Obtenga información sobre cómo comprobar las directivas de caducidad y cambiar la caducidad de contraseñas de usuario individualmente o de forma masiva para contraseñas de Azure Active Directory"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="curtand"/>


# Establecimiento de directivas de caducidad de contraseña en Azure Active Directory
> [AZURE.NOTE]Este tema proporciona contenido de ayuda en línea para servicios en la nube, como Microsoft Intune y Office 365, que dependen de Microsoft Azure Active Directory para los servicios de identidad y directorio.

Como administrador global de un servicio en la nube de Microsoft, puede usar el módulo de Microsoft Azure Active Directory para Windows PowerShell para configurar las contraseñas de usuario de modo que no caduquen. También puede usar cmdlets de Windows PowerShell para quitar la configuración de nunca caduca o para ver qué contraseñas de usuario están configuradas para que no caduquen.

  >[AZURE.NOTE]Solo las contraseñas de cuentas de usuario que no están sincronizadas a través de la sincronización de directorios pueden configurarse para que no caduquen. Para obtener más información sobre la sincronización de directorios, vea la lista de temas de [Guía de sincronización de directorios](https://msdn.microsoft.com/library/azure/hh967642.aspx).

Para usar los cmdlets de Windows PowerShell, primero debe instalarlos.

## ¿Qué desea hacer?

- [Comprobación de la directiva de caducidad de una contraseña](#how-to-check-expiration-policy-for-a-password)

- [Configuración de una contraseña para que caduque](#set-a-password-to-expire)

- [Configuración de una contraseña para que no caduque](#set-a-password-not-to-expire)

## Comprobación de la directiva de caducidad de una contraseña

1.  Conéctese a Windows PowerShell con sus credenciales de administrador de empresa.

2.  Realice una de las operaciones siguientes:

	- Para ver si se ha configurado una sola contraseña de usuario para que nunca caduque, ejecute el cmdlet siguiente con el nombre principal de usuario (UPN) (por ejemplo, aprilr@contoso.onmicrosoft.com) o el identificador de usuario del usuario que desea comprobar: `Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`

	- Para ver la configuración "La contraseña nunca caduca" de todos los usuarios, ejecute el siguiente cmdlet: `Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

## Configuración de una contraseña para que caduque

1.  Conéctese a Windows PowerShell con sus credenciales de administrador de empresa.

2.  Realice una de las operaciones siguientes:

	- Para configurar la contraseña de un usuario para que caduque, ejecute el cmdlet siguiente con el nombre principal de usuario (UPN) o el identificador de usuario del usuario: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires \$false`

	- Para configurar las contraseñas de todos los usuarios de la organización de modo que caduquen, use el siguiente cmdlet: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires \$false`

## Configure una contraseña para que no caduque nunca

1. Conéctese a Windows PowerShell con sus credenciales de administrador de empresa.

2.  Realice una de las operaciones siguientes:

	- Para configurar la contraseña de un usuario para que nunca caduque, ejecute el cmdlet siguiente con el nombre principal de usuario (UPN) o el identificador de usuario del usuario: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires \$true`

	- Para configurar las contraseñas de todos los usuarios de una organización para que nunca caduquen, ejecute el siguiente cmdlet: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires \$true`

<!---HONumber=AcomDC_0107_2016-->