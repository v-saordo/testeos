<properties
	pageTitle="Notificaciones de aprovisionamiento de cuentas | Microsoft Azure"
	description="Obtenga información sobre cómo asegurarse de que se le notifica si surge cualquier problema relacionado con el aprovisionamiento de usuarios que requiere su atención habilitando las notificaciones de aprovisionamiento de cuentas."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="markusvi"/>


# Notificaciones de aprovisionamiento de cuentas

Gracias al aprovisionamiento de usuarios, puede automatizar el proceso de administración de usuarios en aplicaciones SaaS de terceros. <br> Aunque este proceso es automático, necesitará interactuar con él de forma esporádica. <br> Por ejemplo, deberá utilizarlo si resulta que la contraseña de la cuenta que ha configurado para intercambiar datos con una aplicación SaaS de terceros ha expirado.

Al habilitar las notificaciones de aprovisionamiento de cuentas, podrá asegurarse de que se le notifica si surge cualquier problema relacionado con el aprovisionamiento de usuarios que requiere su atención.

Puede activar o desactivar las notificaciones de aprovisionamiento de cuentas como parte de la configuración de aprovisionamiento de usuarios de aplicaciones SaaS de terceros.

![Aprovisionamiento de usuarios][1]



Para activar las notificaciones de aprovisionamiento de cuentas, active la casilla correspondiente en la página de diálogo **Confirmación** y escriba el alias del correo electrónico del destinatario.

![Notificaciones de aprovisionamiento de cuentas][2]
 


Puede introducir una lista de distribución a modo de destinatario, pero tenga en cuenta que el correo electrónico de notificación contiene vínculos a informes a los que solo pueden acceder los administradores de Azure AD.

Si tiene las notificaciones de aprovisionamiento de cuentas habilitadas, recibirá correos electrónicos de todos los problemas críticos que estén relacionados con el aprovisionamiento de usuarios. De todos modos, y para evitar una sobrecarga de correos electrónicos, solo recibirá un correo electrónico de notificación al día para cada aplicación SaaS para la que esté habilitado dicho correo.


[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

<!--Image references-->
[1]: ./media/active-directory-saas-account-provisioning-notifications/ic766307.png
[2]: ./media/active-directory-saas-account-provisioning-notifications/ic766308.png

<!---HONumber=AcomDC_0107_2016-->