<properties
	pageTitle="Información general del modelo de aplicaciones versión 2.0 | Microsoft Azure"
	description="Introducción a la creación de aplicaciones con inicio de sesión de cuentas de Microsoft y de Azure Active Directory."
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
	ms.date="01/18/2016"
	ms.author="dastrock"/>

# Versión preliminar del modelo de aplicaciones v2.0: inicio de sesión de usuarios de cuentas de Microsoft y de Azure AD en una sola aplicación

> [AZURE.NOTE]
	Esta información se aplica a la versión preliminar pública del modelo de aplicaciones v2.0. Para obtener instrucciones acerca de la integración con el servicio de Azure AD disponible con carácter general, consulte la [Guía para desarrolladores de Azure Active Directory](active-directory-developers-guide.md).

Antes, un desarrollador de aplicaciones que deseara admitir cuentas de Microsoft y Azure Active Directory debía realizar la integración con dos sistemas independientes. Con el modelo de aplicaciones v2.0, ahora puede iniciar sesión de usuarios con ambos tipos de cuentas. Una integración sencilla le permite llegar a una audiencia que abarca millones de usuarios con cuentas personales, así como profesionales y educativas.

Las aplicaciones también pueden consumir un [conjunto de API de REST de Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) que usen cualquier tipo de cuenta. Actualmente, estas API incluyen las API de calendarios, contactos y correo electrónico de Outlook. Se agregarán servicios adicionales en un futuro próximo. 
<!-- TODO: customer reference article --> 
<!-- Several apps have already begun to bridge the gap between consumer and enterprise accounts, including: [Boomerang](), [TripIt](), & [Uber](). -->

El modelo de aplicaciones v2.0 se encuentra en su versión preliminar. Durante el período de versión preliminar, nos interesa conocer su opinión y experiencia con el nuevo modelo de aplicaciones, según vaya probándolo. En función de estos comentarios, podemos hacer cambios importantes con el fin de mejorar el servicio. No debe lanzar ninguna aplicación de producción que use el modelo de aplicaciones v2.0 durante este período.
<!-- TODO: Get approval on how it looks  -->

## Introducción
Hay dos maneras de poner en funcionamiento su propia aplicación con el modelo de aplicaciones v2.0. Puede elegir enviar mensajes de protocolo directamente, con [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) o [Open ID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow). También puede usar nuestras bibliotecas para que trabajen en su nombre: elija a continuación su plataforma favorita y ya puede comenzar.
<!-- TODO: Finalize this table  -->

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## Novedades
Vuelva a esta sección a menudo para conocer cambios futuros de la versión preliminar pública del modelo de aplicaciones v2.0. También enviaremos tweets acerca de las actualizaciones mediante @AzureAD.

- Si ha creado una aplicación durante el período de vista previa del modelo de aplicaciones v2.0 2015, no olvide [leer acerca de estos cambios de protocolo](active-directory-v2-preview-oidc-changes.md) para asegurarse de que la aplicación sigue funcionando.
- Obtenga información acerca de los [tipos de aplicaciones que puede crear con el modelo de aplicaciones v2.0](active-directory-v2-flows.md).
- Los desarrolladores familiarizados con Azure Active Directory deben comprobar las [actualizaciones de nuestros protocolos y las diferencias en el modelo de aplicaciones v2.0](active-directory-v2-compare.md).
- [Limitaciones y restricciones de la versión preliminar](active-directory-v2-limitations.md) actual.

## Referencia
Estos vínculos le servirán para explorar la plataforma en profundidad:

- Obtenga ayuda acerca del desbordamiento de pila con las etiquetas [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) o [adal](http://stackoverflow.com/questions/tagged/adal).
- Envíenos sus ideas sobre la versión preliminar con [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/), ¡queremos conocerlas! Use la expresión "AppModelv2:" en el título de la publicación para que podamos encontrarla.
- [Referencia de protocolos del modelo de aplicaciones v2.0](active-directory-v2-protocols.md)
- [Referencia de tokens del modelo de aplicaciones v2.0](active-directory-v2-tokens.md)
- [Referencia de API de REST de Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Ámbitos y consentimiento en el extremo de v2](active-directory-v2-scopes.md)

<!-- TODO: These articles
- [ADAL Library Reference]()
- [v2 Endpoint FAQs](active-directory-v2-faq.md)
-->

<!---HONumber=AcomDC_0128_2016-->