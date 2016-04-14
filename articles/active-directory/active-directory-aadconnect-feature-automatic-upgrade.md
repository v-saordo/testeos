<properties
   pageTitle="Azure AD Connect: actualización automática | Microsoft Azure"
   description="En este tema se describe la característica de actualización automática integrada en Azure AD Connect Sync."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="02/16/2016"
   ms.author="andkjell"/>

# Azure AD Connect: actualización automática
Esta característica se introdujo con la compilación 1.1.105.0 (publicada en febrero de 2016).

## Información general
Tener la seguridad de que la instalación de Azure AD Connect está siempre actualizada nunca ha sido más fácil con la característica de **actualización automática**. Esta característica está habilitada de forma predeterminada para las instalaciones rápidas y garantizará que cuando se lance una nueva versión, se actualice automáticamente la instalación.

La actualización automática está habilitada de forma predeterminada en los siguientes casos:

- Instalación de la configuración rápida
- Mediante SQL Express LocalDB, que es lo que siempre se usará en la configuración rápida.
- La cuenta de AD es la cuenta de MSOL\_ predeterminada creada en la configuración rápida.
- Hay menos de 100 000 objetos en el metaverso.

Se puede ver el estado actual de la actualización automática con el cmdlet de PowerShell `Get-ADSyncAutoUpgrade`. Este cmdlet tiene los siguientes estados:

| Estado | Comentario |
| ---- | ---- |
| Enabled | La actualización automática está habilitada. |
| Suspended | Solo lo establece el sistema. El sistema ya no cumple los requisitos para recibir actualizaciones automáticas. |
| Disabled | La actualización automática está deshabilitada. |

Puede cambiar entre **Habilitado** y **Deshabilitado** con `Set-ADSyncAutoUpgrade`. Solo el sistema debe establecer el estado **Suspendido**.

En la actualización automática se utiliza Azure AD Connect Health como infraestructura de actualización. Para que la actualización automática funcione correctamente, asegúrese de que ha abierto las direcciones URL en el servidor proxy para **Azure AD Connect Health**, como se documenta en [URL de Office 365 e intervalos de direcciones IP ](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Si la IU de **Synchronization Service Manager** se está ejecutando en el servidor, la actualización se suspenderá hasta que la IU se cierre.

## Pasos siguientes
Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0218_2016-->