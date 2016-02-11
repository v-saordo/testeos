<properties
	pageTitle="Notificaciones de informes de Azure Active Directory"
	description="Uso de las notificaciones de informes de Azure Active Directory para inicios de sesión sospechosos"
	services="active-directory"
	documentationCenter=""
	authors="SSalahAhmed"
	manager="gchander"
	editor="LisaToft"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2015"
	ms.author="saah;kenhoff"/>

# Notificaciones de informes de Azure Active Directory

## Qué informes generan notificaciones de correo electrónico

En este momento, solo el informe Actividad de inicio de sesión irregular desencadena notificaciones por correo electrónico.

## ¿Qué es un "inicio de sesión irregular"?

Inicios de sesión irregulares son aquellos que han sido identificados por los algoritmos de aprendizaje automático, de acuerdo con una condición de "viaje imposible" combinado con una ubicación y un dispositivo inicio de sesión anómalo. Esto puede indicar que un hacker ha intentado iniciar sesión con esta cuenta.

## ¿Quién recibe las notificaciones de correo electrónico?

El correo electrónico se envía a todos los administradores globales a los que se ha asignado una licencia de Active Directory Premium. Para asegurarnos de que se entrega, también lo enviamos a la dirección de correo electrónico alternativa de los administradores. Los administradores deben incluir aad-alerts-noreply@mail.windowsazure.com en su lista de remitentes seguros para no perder el correo electrónico.

## ¿Con qué frecuencia se envían los correos electrónicos?

El correo electrónico se envía si se producen 10 nuevas Actividades de inicio de sesión irregulares en los últimos 30 días, o desde que se envió el último correo electrónico, lo que tenga lugar antes.

## ¿Cómo puedo tener acceso al informe mencionado en el correo electrónico?

Al hacer clic en el vínculo, se le redirigirá a la página del informe en el Portal de administración de Azure. Para tener acceso al informe, deberá ser:

- Administrador o coadministrador de su suscripción de Azure
- Administrador global en el directorio y tener asignada una licencia de Active Directory Premium. Para obtener más información, consulte Ediciones de Azure Active Directory.

## ¿Puedo desactivar los correos electrónicos?

Sí, para desactivar las notificaciones relacionadas con inicios de sesión erróneos dentro del Portal de administración de Azure, haga clic en **Configurar**, y, a continuación, seleccione **Deshabilitadas** en la sección **Notificaciones**.

## Pasos siguientes
- ¿Tiene curiosidad sobre qué informes de actividad, auditoría y seguridad están disponibles? Consulte [Informes de actividad, auditoría y seguridad de Azure AD](active-directory-view-access-usage-reports.md).
- [Introducción a Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Incorporación de la marca de empresa a sus páginas de inicio de sesión y panel de acceso](active-directory-add-company-branding.md)

<!---HONumber=Oct15_HO3-->