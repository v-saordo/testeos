<properties
	pageTitle="Modelo de aplicaciones v2.0 | Microsoft Azure"
	description="Cómo registrar una aplicación con Microsoft para habilitar el inicio de sesión y la integración de aplicaciones con el modelo de aplicación v2.0."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/09/2015"
	ms.author="dastrock"/>

# Vista previa del Modelo de aplicaciones v2.0: cómo registrar una aplicación con Microsoft

Para crear una aplicación que acepte tanto el inicio de sesión de MSA como de Azure AD, tendrá que registrar primero una aplicación con Microsoft. No podrá usar cualquier aplicación existente que pueda tener con Azure AD o MSA; es la hora de crear una completamente nueva.

> [AZURE.NOTE]Esta información se aplica a la vista previa pública del modelo de aplicaciones v2.0. Para obtener instrucciones sobre cómo integrar con el servicio de Azure AD disponible con carácter general, consulte la [Guía para desarrolladores de Azure Active Directory](active-directory-developers-guide.md).

## Visitar el Portal de registro para aplicaciones de Microsoft
Lo primero es lo primero: vaya a[https://apps.dev.microsoft.com](https://apps.dev.microsoft.com). Este es el nuevo portal de registro para aplicaciones donde puede administrar todo sobre sus aplicaciones de Microsoft.

Inicie sesión con una cuenta personal o una cuenta profesional o educativa de Microsoft. Si no tiene ninguna de ellas, suscríbase para una nueva cuenta personal. Adelante, no le llevará mucho tiempo; aquí le esperamos.

¿Ha acabado? Ahora debería estar viendo su lista de aplicaciones de Microsoft, que probablemente esté vacía. Cambiemos eso.

<!-- TODO: Verify strings here -->
Haga clic en **Agregar una aplicación** y asígnele un nombre. El portal le asignará a su aplicación un id. de aplicación único global que usará más adelante en su código. Si su aplicación incluye un componente del lado del servidor que necesita tokens de acceso para llamar a las API (piense, como Office, Azure o aplicación de terceros), deseará crear un ** Secreto de la aplicación** aquí también.<!-- TODO: Link for app secrets -->

A continuación, agregue las plataformas que usará su aplicación. -Para las aplicaciones web, ofrezca un **URI de redireccionamiento** donde se pueden enviar mensajes de inicio de sesión. - Para aplicaciones móviles, copie el URI de redirección predeterminado creado automáticamente para usted.

Si lo desea, puede personalizar la apariencia de la página de inicio de sesión en la sección Perfil. Asegúrese de hacer clic en **Guardar** antes de continuar.

## Crear una aplicación de inicio rápido
Ahora que tiene una aplicación de Microsoft, puede completar uno de nuestros tutoriales de inicio rápido para empezar a usar el modelo de aplicación 2.0. Estas son algunas recomendaciones:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

<!---HONumber=AcomDC_1217_2015-->