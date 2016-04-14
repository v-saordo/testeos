<properties
   pageTitle="Sincronización de Azure AD Connect: Extensiones de directorio | Microsoft Azure"
   description="En este tema se describe la característica de extensiones de directorio en Azure AD Connect."
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

# Sincronización de Azure AD Connect: Extensiones de directorio
Las extensiones de directorio le permiten ampliar el esquema de Azure AD con sus propios atributos desde Active Directory local. De esta manera puede crear aplicaciones LOB mediante el consumo de atributos que se siguen administrando de forma local. Estos atributos se pueden consumir mediante [extensiones de directorio de Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) o [Microsoft Graph](https://graph.microsoft.io/). Puede ver los atributos disponibles mediante el [explorador de Azure AD Graph](https://graphexplorer.cloudapp.net) y el [explorador de Microsoft Graph](https://graphexplorer2.azurewebsites.net/), respectivamente.

En la actualidad, ninguna carga de trabajo de Office 365 consumirá estos atributos.

Configure qué atributos adicionales desea sincronizar en la ruta de acceso de configuración personalizada en el Asistente para instalación. ![Asistente para la extensión de esquema](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png) La instalación mostrará los atributos siguientes, que son candidatos válidos:

- Tipos de objetos de usuario y de grupo
- Atributos de valor único
- Cadenas, entero, binario

Un objeto puede tener hasta 100 atributos de extensiones de directorio. La longitud máxima es de 250 caracteres. Si un valor de atributo es mayor, el motor de sincronización lo truncará.

Durante la instalación de Azure AD Connect, se registrará una aplicación donde estos atributos estarán disponibles. Puede ver esta aplicación en el Portal de Azure. ![Aplicación de extensión de esquema](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3.png)

Estos atributos ahora estarán disponibles mediante Graph:![Gráfico](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Los atributos tienen el prefijo extension\_{AppClientId}\_. El AppClientId tendrá el mismo valor en todos los atributos del directorio de Azure AD.

## Pasos siguientes
Obtenga más información sobre la configuración de la [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0218_2016-->