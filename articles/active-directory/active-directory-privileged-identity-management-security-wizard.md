<properties
   pageTitle="Asistente para seguridad de Privileged Identity Management de Azure"
   description="La primera vez que utilice la extensión de Privileged Identity Management de Azure, aparecerá un asistente para seguridad. En este artículo se describen los pasos para usar al asistente."
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

# Asistente para seguridad de Privileged Identity Management de Azure

La primera vez que ejecute Privileged Identity Management (PIM) de Azure, aparecerá un asistente. El asistente le ayuda a comprender los riesgos de seguridad de identidades con privilegios y cómo usar Privileged Identity Management para reducirlos.

Hay tres secciones que revisar, **LOS ADMINISTRADORES PODRÍAN PONERLO EN RIESGO, ADMINISTRE LA SUPERFICIE EXPUESTA A ATAQUES DE LOS ADMINISTRADORES** y **DEFINA LA CONFIGURACIÓN TEMPORAL DE LOS ADMINISTRADORES**. Cada sección ofrece una visión general de los conceptos y una explicación de algunas acciones que deben realizarse.

Al principio, todos los administradores globales serán permanentes. Al hacer clic en **LOS ADMINISTRADORES PODRÍAN PONERLO EN RIESGO**, se mostrará una lista de roles de administrador global y cuántos de ellos tiene actualmente.

Al hacer clic en **ADMINISTRE LA SUPERFICIE EXPUESTA A ATAQUES DE LOS ADMINISTRADORES**, se presentará una oportunidad para cambiar los administradores a temporales, dejarlos permanentes o quitarlos por completo del rol.

**DEFINA LA CONFIGURACIÓN TEMPORAL DE LOS ADMINISTRADORES** le permite requerir la autenticación multifactor, habilitar notificaciones y determinar durante cuánto tiempo un administrador temporal tendrá privilegios.

## Cambio de roles de administrador global a temporal o permanente

Tiene tres opciones para cambiar la ventana de tiempo de un administrador global.

1.  Haga clic en el botón **Convertir todos en temporales** para convertir todos los administradores globales en temporales.

2.  Haga clic en el botón **Convertir todos en permanentes** para convertir todos los administradores globales en permanentes.

3.  Seleccione **Mantener permanente**, **Convertir en temporal** o **Quitar del rol:** para cada administrador global.

## Cambio del período de activación para un rol de administrador global.

Hay dos formas de establecer el período de activación para un administrador global:

1.  Mueva el control deslizante **período de activación** hacia la izquierda o la derecha para aumentar o disminuir el período de activación. El período de activación puede ser de hasta 72 horas.

2.  Escriba el número de horas en el campo **horas** campo situado a la derecha del control deslizante.

## Habilitación de notificaciones

A fin de que los administradores puedan recibir correo electrónico cuando los roles se activen, habilite las notificaciones haciendo clic en el botón **Habilitar**. También puede deshabilitar esta característica más tarde.

## Exigencia de Multi-Factor Authentication

Si quiere que se exija a los administradores que usen MFA para iniciar sesión en sus cuentas y solicitar una extensión de su rol, habilite MFA haciendo clic en el botón **Habilitar**. También puede deshabilitar esta característica más tarde.

<!--For more information about MFA and PIM, click here. PLACEHOLDER: NEED LINK TO MFA DOC.-->

Seleccione los roles a los que se aplicará esta configuración. Haga clic en **Aceptar**.

> [AZURE.WARNING] Es importante que en este momento tenga más de un administrador de seguridad. Si hay solo un administrador de seguridad que no está establecido como permanente y no tiene MFA configurado, el usuario no podrá administrar PIM en absoluto después de que expire la asignación de roles.

Haga clic en el botón **Aceptar** cuando haya terminado.

Una vez realizados los cambios, ya no se mostrará el asistente. Pero se puede acceder a él nuevamente haciendo clic en el botón **Asistente** en **Administrar identidades**.

## Pasos siguientes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0204_2016-->