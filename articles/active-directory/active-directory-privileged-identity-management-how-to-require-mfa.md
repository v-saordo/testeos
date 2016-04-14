<properties
   pageTitle="Privileged Identity Management de Azure: Exigencia de MFA"
   description="Obtenga información sobre cómo exigir MFA (Multi-Factor Authentication) para identidades con privilegios con la extensión de Privileged Identity Management de Azure."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/21/2016"
   ms.author="kgremban"/>

# Privileged Identity Management de Azure: Exigencia de MFA

Se recomienda exigir Multi-Factor Authentication para todos los administradores.

##Exigencia de MFA en Privileged Identity Management de Azure
Al iniciar sesión como administrador de PIM, recibirá alertas que sugieren que las cuentas con privilegios deben exigir Multi-Factor Authentication (MFA). Haga clic en la alerta de seguridad en el panel PIM y se abrirá una nueva hoja con una lista de las cuentas de administrador que deben exigir MFA. Puede exigir MFA seleccionando varios roles y haciendo clic en el botón **Corregir**, o puede hacer clic en el botón de puntos suspensivos situado junto a cada uno de los roles y luego hacer clic en el botón **Corregir**.

Además, puede cambiar el requisito de MFA por rol haciendo clic en un rol específico en la sección Roles del panel y, luego, habilitar MFA para ese rol haciendo clic en **Configuración** en la hoja del rol y seleccionando **Habilitar** en la autenticación multifactor.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0204_2016-->