<properties
   pageTitle="Características de Azure AD Connect en la versión preliminar | Microsoft Azure"
   description="En este tema se describen más detenidamente las características que se encuentran en vista previa en Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="02/16/2016"
   ms.author="andkjell;billmath"/>

# Más detalles sobre las características de vista previa
En este tema se describe cómo usar las características que existen actualmente en la vista previa.

## Escritura diferida de grupos
La opción para la escritura diferida de grupos en las características opcionales le permitirá escribir en diferido **Grupos de Office 365** en un bosque con Exchange instalado. Se trata de un tipo de grupo que siempre se controla en la nube. Si dispone de Exchange local, puede reescribir estos grupos en el entono local, por lo que los usuarios con un buzón de Exchange local pueden enviar y recibir correos electrónicos de ellos.

Puede encontrar más información sobre los grupos de Office 365 y cómo utilizarlos [aquí](http://aka.ms/O365g).

Este grupo se representará como un grupo de distribución en AD DS local. El servidor de Exchange local debe tener la actualización acumulativa 8 de Exchange 2013 (publicada en marzo de 2015) o Exchange 2016 para reconocer este nuevo tipo de grupo.

**Notas durante la vista previa**

- En la vista previa no se rellena actualmente el atributo de libreta de direcciones. Sin este atributo, el grupo no estará visible en la GAL. La manera más fácil de rellenar este atributo es usar el cmdlet de Exchange PowerShell `update-recipient`.
- Solo los bosques con el esquema de Exchange son destinos válidos para los grupos. Si no se ha detectado ningún Exchange, no se podrá habilitar la reescritura de grupos.
- Actualmente solo se admiten las implementaciones de organizaciones de Exchange de un solo bosque. Si tiene más de una organización de Exchange local, necesitará una solución de GALSync local para que estos grupos aparezcan en los demás bosques.
- La característica Reescritura de grupos no controla actualmente los grupos de seguridad ni los grupos de distribución.

>[AZURE.NOTE] Se necesita una suscripción a Azure AD Premium para la reescritura de grupos.

## Reescritura de usuarios
> [AZURE.IMPORTANT] La característica en vista previa de reescritura de usuarios se quitó temporalmente en la actualización de agosto de 2015 a Azure AD Connect. Si la ha habilitado, debería deshabilitarla.

La escritura diferida de usuarios se encuentra en la primera etapa de la vista previa. Solo se puede usar cuando Azure AD es el origen de todos los objetos de usuario y la instancia de Active Directory local está vacía antes de habilitar la característica (implementación en entorno virgen).

>[AZURE.WARNING] Esta característica solo se debe evaluar en un entorno de prueba y no debe usarse en un directorio de Azure AD empleado para el uso en producción.

.

>[AZURE.NOTE] Se necesita una suscripción a Azure AD Premium para la reescritura de usuarios.

## Pasos siguientes
Continúe su [Instalación personalizada de Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0218_2016-->